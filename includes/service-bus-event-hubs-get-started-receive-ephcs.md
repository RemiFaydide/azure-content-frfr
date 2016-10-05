## Réception de messages avec EventProcessorHost

[EventProcessorHost][] est une classe .NET qui simplifie la réception d'événements provenant d'Event Hubs grâce à la gestion des points de contrôle permanents et des réceptions en parallèle d'Event Hubs. [EventProcessorHost][] permet de répartir des événements sur plusieurs récepteurs, même quand ils sont hébergés dans des nœuds différents. Cet exemple illustre l'utilisation de la classe [EventProcessorHost][] pour un récepteur unique. L’exemple de [traitement d’événement mis à l’échelle][] illustre l’utilisation d’[EventProcessorHost][] pour plusieurs récepteurs.

Pour utiliser [EventProcessorHost][], vous devez disposer d’un [compte Azure Storage][] \:

1. Connectez-vous au [portail Azure][], puis cliquez sur **Nouveau** en haut à gauche de l’écran.

2. Cliquez sur **Données + stockage**, puis sur **Compte de stockage**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)

3. Dans le panneau **Créer un compte de stockage**, tapez un nom pour votre compte de stockage. Choisissez un abonnement Azure, un groupe de ressources et l’emplacement où créer la ressource. Cliquez ensuite sur **Créer**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)

4. Dans la liste des comptes de stockage, cliquez sur le compte de stockage nouvellement créé.

5. Dans le panneau du compte de stockage, cliquez sur **Clés d’accès**. Copiez la valeur de **key1** pour pouvoir l’utiliser ultérieurement au cours de ce didacticiel.

	![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)

4. Dans Visual Studio, créez un projet d'application de bureau Visual C# à l'aide du modèle de projet d'**application de console**. Nommez le projet **Récepteur**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)

5. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution**.

6. Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Vérifiez que le nom du projet (**Récepteur**) est spécifié dans la zone **Version(s)**. Cliquez sur **Installer** et acceptez les conditions d’utilisation.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)

	Visual Studio lance le téléchargement, l’installation et ajoute une référence au [Package NuGet Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), avec toutes les dépendances associées.

7. Cliquez avec le bouton droit sur le projet **Récepteur**, cliquez sur **Ajouter**, puis cliquez sur **Classe**. Nommez la nouvelle classe **SimpleEventProcessor**, puis cliquez sur **Ajouter** pour créer la classe.

	![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)

8. Ajoutez les instructions ci-après au début du fichier SimpleEventProcessor.cs :

	```
	using Microsoft.ServiceBus.Messaging;
	using System.Diagnostics;
	```

	Insérez ensuite le code suivant dans le corps de la classe :

	```
    class SimpleEventProcessor : IEventProcessor
	{
	    Stopwatch checkpointStopWatch;

	    async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	    {
	        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	        if (reason == CloseReason.Shutdown)
	        {
	            await context.CheckpointAsync();
	        }
	    }

	    Task IEventProcessor.OpenAsync(PartitionContext context)
	    {
	        Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	        this.checkpointStopWatch = new Stopwatch();
	        this.checkpointStopWatch.Start();
	        return Task.FromResult<object>(null);
	    }

	    async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	    {
	        foreach (EventData eventData in messages)
	        {
	            string data = Encoding.UTF8.GetString(eventData.GetBytes());

	            Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                context.Lease.PartitionId, data));
	        }

	        //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	        if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
	    }
	}
    ```

	Cette classe sera appelée par **EventProcessorHost** pour traiter les événements envoyés par le hub d'événements. Notez que la classe `SimpleEventProcessor` utilise un chronomètre pour appeler régulièrement la méthode de point de contrôle sur le contexte **EventProcessorHost**. Cette opération garantit que, en cas de redémarrage du récepteur, la perte de traitement de travail ne sera pas supérieure à cinq minutes.

9. Dans la classe **Program**, ajoutez l’instruction `using` suivante en haut du fichier :

	```
	using Microsoft.ServiceBus.Messaging;
	```

	Remplacez ensuite la méthode `Main` dans la classe `Program` par le code suivant, en remplaçant le nom d’Event Hub et la chaîne de connexion au niveau de l’espace de noms, enregistrée précédemment, ainsi que la clé et le compte de stockage que vous avez copiés dans les sections précédentes :

    ```
	static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
	```

> [AZURE.NOTE] Ce didacticiel utilise une seule instance d'[EventProcessorHost][]. Pour augmenter le débit, il est recommandé d'exécuter plusieurs instances d'[EventProcessorHost][], comme illustré dans l'exemple de [traitement d'événement mis à l'échelle][]. Dans ces cas, les différentes instances se coordonnent automatiquement entre elles afin d’équilibrer la charge des événements reçus. Si vous souhaitez que plusieurs récepteurs traitent *tous* les événements, vous devez utiliser le concept **ConsumerGroup**. Au moment de la réception des événements à partir de différents ordinateurs, il peut être utile de spécifier des noms pour les instances d'[EventProcessorHost][] basées sur les ordinateurs (ou rôles) dans lesquels ils sont déployés. Pour plus d’informations sur ces sujets, consultez les rubriques [Vue d’ensemble d’Event Hubs][] et [Guide de programmation Event Hubs][].

<!-- Links -->
[Vue d’ensemble d’Event Hubs]: event-hubs-overview.md
[Guide de programmation Event Hubs]: event-hubs-programming-guide.md
[traitement d'événement mis à l'échelle]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[traitement d’événement mis à l’échelle]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[compte Azure Storage]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->