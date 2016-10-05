
<properties
   pageTitle="Mise à niveau d'une application : paramètres de mise à niveau | Microsoft Azure"
   description="Décrit les paramètres relatifs à la mise à niveau d'une application Service Fabric, y compris les vérifications d'intégrité à effectuer et les stratégies pour annuler automatiquement la mise à niveau."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>



# Paramètres de mise à niveau d'application

Cet article décrit les différents paramètres qui s’appliquent pendant la mise à niveau d’une application Azure Service Fabric. Les paramètres incluent le nom et la version de l’application. Ils permettent de contrôler les délais d’attente et les vérifications d’intégrité appliqués au cours de la mise à niveau et de spécifier les stratégies à appliquer quand une mise à niveau échoue.


<br>

| Paramètre | Description |
| --- | --- |
| ApplicationName | Nom de l'application en cours de mise à niveau. Exemples : fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion | Version du type d'application ciblée par la mise à niveau. |
| FailureAction | Action effectuée par Service Fabric en cas d’échec de la mise à niveau. L’application peut être restaurée sur la version antérieure à la mise à jour ou la mise à niveau peut être arrêtée au niveau du domaine de mise à niveau en cours. Dans le dernier cas, le mode de mise à niveau passe également à Manual. Les valeurs autorisées sont Rollback et Manual. |
| HealthCheckWaitDurationSec | Délai d’attente (en secondes) après la fin de la mise à niveau sur le domaine de mise à niveau au terme duquel Service Fabric évalue l’intégrité de l’application. Cette durée peut également être considérée comme le temps pendant lequel une application doit s'exécuter pour être estimée saine. Si la vérification d'intégrité réussit, le processus de mise à niveau se poursuit sur le domaine de mise à niveau suivant. Si la vérification d'intégrité échoue, Service Fabric attend pendant un intervalle de temps (UpgradeHealthCheckInterval) avant d'exécuter de nouveau la vérification dans le délai imparti par HealthCheckRetryTimeout. La valeur par défaut et recommandée est 0 seconde. |
| HealthCheckRetryTimeoutSec | Durée (en secondes) pendant laquelle Service Fabric continue d'évaluer l'intégrité avant de déclarer la mise à niveau comme ayant échoué. La valeur par défaut est 600 secondes. Cette durée commence quand HealthCheckWaitDuration est atteint. Dans le délai imparti par HealthCheckRetryTimeout, Service Fabric peut effectuer plusieurs vérifications d'intégrité de l'application. La valeur par défaut est 10 minutes, mais vous devez l’adapter à votre application. |
| HealthCheckStableDurationSec | Durée (en secondes) nécessaire pour vérifier que l’application est stable avant de passer au domaine de mise à niveau suivant ou de terminer la mise à niveau. Cette durée d'attente facilite la détection des modifications d'intégrité qui pourraient avoir lieu juste après la fin de la vérification d'intégrité. La valeur par défaut est 120 secondes, mais vous devez l’adapter à votre application. |
| UpgradeDomainTimeoutSec | Durée maximale (en secondes) pour mettre à niveau un seul domaine de mise à niveau. Si ce délai est atteint, la mise à niveau s’arrête et effectue l’action spécifiée par UpgradeFailureAction. La valeur par défaut est « never » (Infinie), mais vous devez l’adapter à votre application. |
| UpgradeTimeout | Délai (en secondes) qui s’applique à l’ensemble de la mise à niveau. Si ce délai est atteint, la mise à niveau s’arrête et UpgradeFailureAction est déclenché. La valeur par défaut est « never » (Infinie), mais vous devez l’adapter à votre application. |
| UpgradeHealthCheckInterval | Fréquence à laquelle l’état d’intégrité est vérifié. Ce paramètre est spécifié dans la section ClusterManager du _manifeste_ de _cluster_. Il n’est pas défini dans le cadre de l’applet de commande de mise à niveau. La valeur par défaut est de 60 secondes. |






<br>
## Évaluation de l'intégrité du service au cours de la mise à niveau de l'application

<br> Les critères d'évaluation d'intégrité sont facultatifs. Si les critères d’évaluation d’intégrité ne sont pas spécifiés au démarrage d’une mise à niveau, Service Fabric utilise les stratégies de vérification d’intégrité d’application définies dans le fichier ApplicationManifest.xml de l’instance d’application.


<br>

| Paramètre | Description |
| --- | --- |
| ConsiderWarningAsError | La valeur par défaut est False. Traiter les événements d’avertissement d’intégrité de l’application comme des erreurs pendant l’évaluation de l’intégrité de l’application au cours de la mise à niveau. Par défaut, Service Fabric ne considère pas les événements d’avertissement d’intégrité comme des échecs (erreurs) ; la mise à niveau peut donc continuer même si des événements d’avertissement se produisent. |
| MaxPercentUnhealthyDeployedApplications | La valeur par défaut et recommandée est 0. Nombre maximal d’applications déployées (voir la [section Intégrité](service-fabric-health-introduction.md)) pouvant être défectueuses avant que l’application ne soit considérée comme défectueuse et que sa mise à niveau échoue. Ce paramètre définit l’intégrité de l’application sur le nœud et vous aide à détecter les problèmes lors de la mise à niveau. En général, les réplicas de l’application font l’objet d’un équilibrage de la charge sur l’autre nœud, ce qui rend l’application intègre et permet de poursuivre la mise à niveau. En spécifiant une intégrité MaxPercentUnhealthyDeployedApplications stricte, Service Fabric peut détecter rapidement un problème avec le package d’application et provoquer un échec de la mise à niveau. |
| MaxPercentUnhealthyServices | La valeur par défaut et recommandée est 0. Nombre maximal de services dans l’instance d’application pouvant être défectueux avant que l’application ne soit considérée comme défectueuse et que sa mise à niveau échoue. |
| MaxPercentUnhealthyPartitionsPerService | La valeur par défaut et recommandée est 0. Nombre maximal de partitions dans un service pouvant être défectueuses avant que le service ne soit considéré comme défectueux. |
| MaxPercentUnhealthyReplicasPerPartition | La valeur par défaut et recommandée est 0. Nombre maximal de réplicas dans une partition pouvant être défectueux avant que la partition ne soit considérée comme défectueuse. |
| UpgradeReplicaSetCheckTimeout | **Service sans état** : dans un même domaine de mise à niveau, Service Fabric tente de s’assurer que des instances supplémentaires du service sont disponibles. Si le nombre d’instances cibles est supérieur à un, Service Fabric attend que plusieurs instances soient disponibles, jusqu’à ce que la valeur de délai maximal soit atteinte. Ce délai est spécifié à l’aide de la propriété UpgradeReplicaSetCheckTimeout. Si le délai expire, Service Fabric poursuit la mise à niveau, quel que soit le nombre d’instances de service. Si le nombre d'instances cibles est un, Service Fabric n'attend pas et procède immédiatement à la mise à niveau. **Service avec état** : dans un même domaine de mise à niveau, Service Fabric tente de s’assurer que le jeu de réplicas a un quorum. Service Fabric attend qu’un quorum soit disponible, jusqu’à ce que soit atteinte la valeur de délai maximal (spécifiée par la propriété UpgradeReplicaSetCheckTimeout). Si le délai expire, Service Fabric poursuit la mise à niveau, indépendamment du quorum. Ce paramètre est défini sur « never » (infini) pour une restauration par progression et sur 900 secondes pour une restauration. |
| ForceRestart | Si vous mettez à jour un package de configuration ou de données sans mettre à jour le code de service, le service n’est redémarré que si la propriété ForceRestart est définie sur true. Quand la mise à jour est terminée, Service Fabric indique au service qu'un nouveau package de configuration ou de données est disponible. Le service est chargé d'appliquer les modifications. Si nécessaire, le service peut redémarrer automatiquement. |



<br> <br> Les critères MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService et MaxPercentUnhealthyReplicasPerPartition peuvent être spécifiés par type de service pour une instance d’application. La configuration de ces paramètres par service permet à une application de contenir différents types de services avec des stratégies d’évaluation différentes. Par exemple, pour une instance d’application donnée, un type de service de passerelle sans état et un type de service de moteur avec état peuvent avoir des valeurs MaxPercentUnhealthyPartitionsPerService différentes.

## Étapes suivantes

La [mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

La [mise à niveau de votre application à l’aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l’application à l’aide de PowerShell.

Rendez les mises à niveau de votre application compatibles en apprenant à utilisez la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0921_2016-->