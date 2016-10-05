<properties
   pageTitle="Comment appeler une perte des données sur des services Service Fabric | Microsoft Azure"
   description="Explique comment utiliser l’API de perte des données"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# Comment appeler la perte des données sur des services

>[AZURE.WARNING] Ce document décrivent comment entraîner une perte de données dans vos services et doit être utilisé avec précaution.

## Introduction
Vous pouvez appeler une perte de données sur une partition de votre service Service Fabric en appelant StartPartitionDataLossAsync(). Cette API utilise le service d’injection et d’analyse des erreurs pour provoquer des conditions de perte de données.

## Utilisation du service d’injection et d’analyse des erreurs

Le service d’injection et d’analyse des erreurs prend actuellement en charge les API suivantes, comme indiqué dans le graphique ci-dessous. Le côté droit du graphique montre l’applet de commande PowerShell. Reportez-vous à la documentation msdn de chaque API pour plus d’informations sur chacune d’elles.

| C# API | Applet de commande PowerShell |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
|[StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
|[StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## Vue d’ensemble conceptuelle de l’exécution d’une commande

Le service d’injection et d’analyse des erreurs utilise un modèle asynchrone dans lequel vous démarrez la commande avec une API, appelée API « Start » dans ce document, puis vérifie la progression de cette commande à l’aide d’une API « GetProgress » jusqu'à ce qu’elle atteigne un état terminal, ou jusqu'à ce que vous l’annuliez. Pour démarrer une commande, appelez l’API « Start » pour l’API correspondante. Cette API est renvoyée lorsque le service d’injection et d’analyse des erreurs a accepté la demande. Toutefois, elle n’indique pas jusqu’où une commande est exécutée, ou si elle a déjà démarré. Pour vérifier la progression d’une commande, appelez l’API qui correspond à l’API « Start » précédemment appelée « GetProgress ». L’API « GetProgress » renvoie un objet qui indique l’état actuel de la commande à l’intérieur de sa propriété d’état (State). La commande s’exécute indéfiniment jusqu'à ce que :

1.	Elle se termine correctement. Si vous appelez « GetProgress » sur cette API dans ce cas, l’état de progression de l’objet affiche Completed (terminé).
2.	Elle rencontre une erreur irrécupérable. Si vous appelez « GetProgress » sur cette API dans ce cas, l’état de progression de l’objet affiche Faulted (défaillance)
3.	Vous l’annulez avec l’API [CancelTestCommandAsync][cancel] ou l’applet de commande PowerShell [Stop-ServiceFabricTestCommand][cancelps]. Si vous appelez « GetProgress » sur cette API dans ce cas, l’état de progression de l’objet affiche Cancelled (annulé) ou ForceCancelled (annulation forcée), selon un argument de cette API. Consultez la documentation de [CancelTestCommandAsync][cancel] pour plus de détails.


## Détails de l’exécution d’une commande

Pour démarrer une commande, appelez l’API Start avec les arguments attendus. Toutes les API Start ont un argument Guid nommé operationId. Vous devriez effectuer le suivi de l’argument operationId car il est utilisé pour suivre la progression de cette commande. Cet argument doit être passée à l’API « GetProgress » afin de suivre la progression de la commande. La valeur operationId doit être unique.

Après l’appel réussi de l’API Start, l’API GetProgress devrait être appelée dans une boucle jusqu'à ce que la propriété d’état de progression de l’objet retourné affiche Completed (Terminé). Toutes les commandes [FabricTransientException][fte] et OperationCanceledException devraient être relancées. Lorsque la commande a atteint un état terminal (Completed, Faulted ou Cancelled), la propriété Result de progression de l’objet retourné aura plus d’informations. Si l’état affiche Completed (terminé), Result.SelectedPartition.PartitionId contient l’ID de partition qui a été sélectionné. La valeur Result.Exception sera nulle. Si l’état indique Faulted, Result.Exception indiquera la raison pour laquelle le service d’injection et d’analyse des erreurs a généré une erreur de la commande. Result.SelectedPartition.PartitionId affichera l’ID de partition qui a été sélectionné. Dans certains cas, la commande n’a peut-être pas été suffisamment loin pour choisir une partition. Dans ce cas, la valeur PartitionId sera 0. Si l’état affiche Cancelled (annulé), la valeur Result.Exception est nulle. Comme pour l’état Faulted (défaillance), Result.SelectedPartition.PartitionId affichera l’ID de partition qui a été choisi, mais si la commande n’a pas été suffisamment loin pour cela, la valeur sera 0. Reportez-vous aussi à l’exemple ci-dessous.

L’exemple de code ci-dessous montre comment démarrer puis vérifier la progression d’une commande pour provoquer une perte de données sur une partition spécifique.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

L’exemple ci-dessous montre comment utiliser PartitionSelector pour choisir une partition aléatoire d’un service spécifié :

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## Historique et troncation

Dès qu’une commande a atteint un état terminal, ses métadonnées resteront dans le service d’injection et d’analyse des erreurs pour un certain temps, avant d’être supprimées pour économiser de l’espace. Si « GetProgress » est appelée à l’aide de la valeur operationId d’une commande après avoir été supprimée, elle retournera une valeur FabricException avec un code d’erreur KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx

<!---HONumber=AcomDC_0921_2016-->