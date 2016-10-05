
<properties
   pageTitle="Sécurité d’un cluster Service Fabric : rôles clients | Microsoft Azure"
   description="Cet article décrit les deux rôles clients et les autorisations fournies pour les rôles."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>



# Contrôle d’accès en fonction du rôle pour les clients de Service Fabric

Azure Service Fabric prend en charge deux types de contrôle d’accès différents pour les clients qui sont connectés à un cluster Service Fabric : administrateur et utilisateur. Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certaines opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster.

Les **administrateurs** ont un accès complet aux fonctions de gestion (notamment les fonctionnalités de lecture/écriture). Les **utilisateurs**, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.

Vous spécifiez les deux rôles clients (client et administrateur) au moment de la création du cluster en fournissant des certificats séparés pour chacun. Pour plus d’informations sur la configuration d’un cluster Service Fabric sécurisé, consultez [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md).


## Paramètres de contrôle d'accès par défaut


Le type de contrôle d'accès administrateur a un accès complet à toutes les API FabricClient. Il peut exécuter toute opération de lecture et d’écriture sur le cluster Service Fabric, notamment les opérations suivantes :


### Opérations de service et d'application
* **CreateService** : création du service
* **CreateServiceFromTemplate** : création du service à partir d’un modèle
* **UpdateService** : mises à jour du service
* **DeleteService** : suppression du service
* **ProvisionApplicationType** : approvisionnement du type d’application
* **CreateApplication** : création d’application
* **DeleteApplication** : suppression d’application
* **UpgradeApplication** : démarrage ou interruption des mises à niveau de l’application
* **UnprovisionApplicationType** : désapprovisionnement du type d’application
* **MoveNextUpgradeDomain** : reprise des mises à niveau de l’application avec un domaine de mise à jour explicite
* **ReportUpgradeHealth** : reprise des mises à niveau de l’application avec la progression de la mise à niveau actuelle
* **ReportHealth** : création de rapports d'intégrité
* **PredeployPackageToNode** : API de prédéploiement
* **CodePackageControl** : redémarrage des packages de code
* **RecoverPartition** : récupération d’une partition
* **RecoverPartitions** : récupération de partitions
* **RecoverServicePartitions** : récupération des partitions d’un service
* **RecoverSystemPartitions** : récupération des partitions d’un service système


### Opérations de cluster
* **ProvisionFabric** : approvisionnement du manifeste de cluster et/ou MSI
* **UpgradeFabric** : démarrage des mises à niveau du cluster
* **UnprovisionFabric** : désapprovisionnement du manifeste de cluster et/ou MSI
* **MoveNextFabricUpgradeDomain** : reprise des mises à niveau du cluster avec un domaine de mise à jour explicite
* **ReportFabricUpgradeHealth** : reprise des mises à niveau du cluster avec la progression de la mise à niveau actuelle
* **StartInfrastructureTask** : démarrage des tâches d’infrastructure
* **FinishInfrastructureTask** : fin des tâches d’infrastructure
* **InvokeInfrastructureCommand** : commandes de gestion des tâches d’infrastructure
* **ActivateNode** : activation d’un nœud
* **DeactivateNode** : désactivation d’un nœud
* **DeactivateNodesBatch** : désactivation de plusieurs nœuds
* **RemoveNodeDeactivations** : annulation de la désactivation de plusieurs nœuds
* **GetNodeDeactivationStatus** : vérification de l’état de désactivation
* **NodeStateRemoved** : suppression du rapport d’état du nœud
* **ReportFault** : erreur de création de rapport
* **FileContent** : transfert de fichier client de magasin d’image (externe au cluster)
* **FileDownload** : lancement du téléchargement de fichier client de magasin d’image (externe au cluster)
* **InternalList** : opération de liste de fichier client de magasin d’image (interne)
* **Delete** : opération de suppression de client de magasin d’image
* **Upload** : opération de téléchargement de client de magasin d’image
* **NodeControl** : démarrage, arrêt et redémarrage des nœuds
* **MoveReplicaControl** : déplacement de réplicas d’un nœud vers un autre

### Opérations diverses
* **Ping** : commandes ping client
* **Query** : toutes les requêtes autorisées
* **NameExists** : contrôles de présence de l’URI de dénomination



Le type de contrôle d’accès utilisateur est, par défaut, limité aux opérations suivantes :

* **EnumerateSubnames** : énumération de l’URI de dénomination
* **EnumerateProperties** : énumération des propriétés de dénomination
* **PropertyReadBatch** : opérations de lecture des propriétés de dénomination
* **GetServiceDescription** : lecture des descriptions de service et notifications de sondage de longue durée
* **ResolveService** : résolution de service basée sur plainte
* **ResolveNameOwner** : résolution du propriétaire de l’URI de dénomination
* **ResolvePartition** : résolution des services système
* **ServiceNotifications** : notifications de service basées sur des événements
* **GetUpgradeStatus** : interrogation de l’état de mise à niveau de l’application
* **GetFabricUpgradeStatus** : interrogation de l’état de mise à niveau du cluster
* **InvokeInfrastructureQuery** : interrogation des tâches d’infrastructure
* **List** : opération de liste de fichier client de magasin d’image
* **ResetPartitionLoad** : réinitialisation de chargement pour une unité de basculement
* **ToggleVerboseServicePlacementHealthReporting** : basculement des rapports d’intégrité sur le placement de service détaillé

Le contrôle d’accès administrateur a également accès aux opérations précédentes.

## Modification des paramètres par défaut des rôles clients

Dans le fichier du manifeste de cluster, vous pouvez fournir au client des capacités d’administration, si nécessaire. Vous pouvez modifier les valeurs par défaut en accédant à l’option **Paramètres de structure** durant la [création du cluster](service-fabric-cluster-creation-via-portal.md) et en fournissant les paramètres ci-dessus dans les champs **nom**, **admin**, **utilisateur** et **valeur**.

## Étapes suivantes

[Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)

[Création d’un cluster Service Fabric](service-fabric-cluster-creation-via-portal.md)

<!---HONumber=AcomDC_0921_2016-->