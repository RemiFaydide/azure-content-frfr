<properties
   pageTitle="Induire un chaos dans les clusters Service Fabric | Microsoft Azure"
   description="Utilisation des API du service d’injection d’erreurs et d’analyse du cluster pour gérer le chaos dans le cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# Induire un chaos contrôlé dans les clusters Service Fabric
Les systèmes distribués à grande échelle, comme les infrastructures cloud, sont par définition peu fiables. Azure Service Fabric permet aux développeurs d’écrire des services fiables sur une infrastructure peu fiable. Pour écrire des services robustes, les développeurs doivent pouvoir introduire des erreurs dans une infrastructure peu fiable pour tester la stabilité des solutions qu’ils conçoivent.

Le service d’injection d’erreurs et d’analyse de cluster (FAS) permet aux développeurs de provoquer des actions erronées afin de tester les services. Toutefois, les erreurs simulées ciblées présentent une efficacité limitée. Pour aller plus loin dans le test, il est possible d’utiliser la technique du chaos. Au sein du cluster, le chaos simule des erreurs entrelacées en continu, avec et sans perte de données, sur des périodes prolongées. Une fois que le chaos est configuré avec la fréquence et le type des erreurs, il peut être démarré ou arrêté à l’aide des API C# ou de PowerShell pour générer des erreurs au sein du cluster et de votre service. Pendant que le chaos s’exécute, il génère différents événements qui capturent l’état de l’exécution à un moment précis. Par exemple, un événement ExecutingFaultsEvent contient toutes les erreurs qui sont en cours d’exécution dans cette itération ; un événement ValidationFailedEvent contient les détails d’une défaillance trouvée pendant la validation du cluster. L’API GetChaosReportAsync peut être appelée pour obtenir un rapport sur les exécutions du chaos.

## Erreurs introduites dans le chaos
Le chaos génère des erreurs dans l’ensemble du cluster Service Fabric et compresse, en quelques heures, les erreurs constatées au cours de plusieurs mois ou années. L’utilisation d’erreurs entrelacées avec un taux élevé d’erreurs permet d’identifier des dysfonctionnements qui n’auraient pu être isolés autrement. Cet exercice du chaos entraîne une amélioration significative de la qualité du code du service. Le chaos introduit des erreurs à partir des catégories suivantes :

 - Redémarrer un nœud
 - Redémarrer un package de code déployé
 - Supprimer un réplica
 - Redémarrer un réplica
 - Déplacer un réplica principal (configurable)
 - Déplacer un réplica secondaire (configurable)

Le chaos s’exécute dans de multiples itérations. Chaque itération consiste en des validations de cluster et des erreurs pendant la période spécifiée. Les délais de stabilisation du cluster et de validation sont configurables. Si une défaillance est trouvée dans la validation du cluster, le chaos génère et fait persister un événement ValidationFailedEvent avec l’horodatage UTC et les détails de la défaillance.

Prenons, par exemple, une instance de chaos définie pour s’exécuter une heure, avec un maximum de trois erreurs simultanées. Le chaos introduit trois erreurs, puis valide l’intégrité du cluster et effectue une itération sur l’étape précédente jusqu'à ce qu’il soit explicitement arrêté via l’API StopChaosAsync ou jusqu’à ce qu’une heure se soit écoulée. Si le cluster devient défectueux dans une itération, autrement dit, s’il ne se stabilise pas dans le délai configuré, le chaos génère un événement ValidationFailedEvent, qui indique qu’un problème est survenu qui peut nécessiter un examen approfondi.

Dans sa forme actuelle, le chaos déclenche uniquement des erreurs sécurisées, ce qui implique qu’en l’absence d’erreurs externes, une perte de quorum ou une perte de données ne se produit jamais.

## Options de configuration importantes
 - **TimeToRun** : durée totale d’exécution du chaos jusqu’à sa réussite. Il est possible d’arrêter le chaos avant qu’il ne se soit exécuté durant la période TimeToRun définie via l’API StopChaos.
 - **MaxClusterStabilizationTimeout** : durée d’attente maximale pour que le cluster devienne sain avant une nouvelle vérification. Cette attente vise à réduire la charge sur le cluster pendant sa récupération. Les vérifications effectuées sont
    - Si l’intégrité du cluster est OK
    - L’intégrité du service est OK
    - La taille du jeu de réplicas cible est atteinte pour la partition de service
    - Aucun réplica InBuild n’existe
 - **MaxConcurrentFaults** : nombre maximal d’erreurs introduites simultanément dans chaque itération. Plus le nombre est élevé, plus le chaos est efficace. Vous obtiendrez des combinaisons plus complexes de basculement et de transition. Le chaos garantit qu’aucune perte de quorum ou de données ne sera à déplorer en l’absence d’erreurs externes, quel que soit la valeur de la configuration.
 - **EnableMoveReplicaFaults** : active ou désactive les erreurs provoquant le déplacement des réplicas primaires ou secondaires. Ces erreurs sont désactivées par défaut.
 - **WaitTimeBetweenIterations** : délai d’attente entre les itérations, c’est-à-dire après une séquence d’erreurs et la validation correspondante.
 - **WaitTimeBetweenFaults** : délai d’attente entre deux erreurs consécutives lors d’une itération.

## Procédure d’exécution du chaos
Exemple de code C#

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```

<!---HONumber=AcomDC_0921_2016-->