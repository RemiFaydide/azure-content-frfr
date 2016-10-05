<properties
   pageTitle="Augmenter ou diminuer la taille des instances d’un cluster Service Fabric | Microsoft Azure"
   description="Augmenter ou diminuer la taille des instances d’un cluster Service Fabric pour répondre à la demande en définissant des règles de mise à l’échelle automatique pour chaque type de nœud/jeu de mise à l’échelle de machine virtuelle. Ajouter ou supprimer des nœuds d’un cluster Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# Augmenter ou diminuer la taille des instances d’un cluster Service Fabric à l’aide de règles de mise à l’échelle automatique

Les jeux de mise à l’échelle de machine virtuelle sont des ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles en tant que jeu. Chaque type de nœud qui est défini dans un cluster Service Fabric est configuré en tant que jeu de mise à l’échelle de machine virtuelle distinct. Chaque type de nœud peut ensuite faire l’objet d’une augmentation ou d’une diminution de la taille des instances de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Pour en savoir plus, voir le document portant sur les [types de nœuds Service Fabric](service-fabric-cluster-nodetypes.md). Étant donné que les types de nœuds Service Fabric de votre cluster sont constitués de jeux de mise à l’échelle de machine virtuelle sur le principal, vous devez définir des règles de mise à l’échelle automatique pour chaque type de nœud/jeu de mise à l’échelle de machine virtuelle.

>[AZURE.NOTE] Votre abonnement doit avoir suffisamment de cœurs pour ajouter les nouvelles machines virtuelles qui composeront ce cluster. Il n’existe actuellement aucune validation de modèle. Vous obtenez donc une erreur liée au temps de déploiement si l’une des limites de quota est atteinte.

## Choisir le type de nœud/jeu de mise à l’échelle de machine virtuelle à mettre à l’échelle

Actuellement, vous n’êtes pas en mesure de spécifier les règles de mise à l’échelle automatique pour les jeux de mise à l’échelle de machine virtuelle à l’aide du portail. Nous allons donc utiliser Azure PowerShell (1.0 et versions ultérieures) pour répertorier les types de nœud et leur ajouter des règles de mise à l’échelle automatique.

Pour obtenir la liste des jeux de mise à l’échelle de machine virtuelle qui composent votre cluster, exécutez les applets de commande suivantes :

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## Définir des règles de mise à l’échelle automatique pour le type de nœud/jeu de mise à l’échelle de machine virtuelle

Si votre cluster comporte plusieurs types de nœud, vous devez effectuer cette opération pour chaque type de nœud/jeu de mise à l’échelle de machine virtuelle pour lequel vous voulez augmenter ou diminuer la taille des instances. Tenez compte du nombre de nœuds dont vous devez disposer avant de configurer la mise à l’échelle automatique. Le nombre de nœuds minimal dont vous devez disposer pour le type de nœud principal est déterminé par le niveau de fiabilité que vous avez choisi. En savoir plus sur les [niveaux de fiabilité](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Une descente en puissance du type de nœud principal en deçà du nombre minimal rend le cluster instable ou entraîne son interruption. Cela peut entraîner une perte de données pour vos applications et pour les services système.

Actuellement, la fonctionnalité de mise à l’échelle automatique ne dépend pas des charges que vos applications peuvent signaler à Service Fabric. Par conséquent, pour le moment, la mise à l’échelle automatique que vous obtenez dépend purement des compteurs de performances émis par chaque instance du jeu de mise à l’échelle de machine virtuelle.

Suivez ces instructions pour [configurer la mise à l’échelle automatique pour chaque jeu de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Dans un scénario de descente en puissance, à moins que votre type de nœud ne possède un niveau de durabilité Gold ou Silver, vous devez appeler [l’applet de commande Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) avec le nom de nœud approprié.

## Ajouter manuellement des machines virtuelles à un type de nœud/jeu de mise à l’échelle de machine virtuelle

Suivez les exemples/les instructions dans la [galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles dans chaque Nodetype.

>[AZURE.NOTE] L’ajout de machines virtuelles prend du temps, donc ne vous attendez pas à ce que les ajouts soient instantanés. Par conséquent, envisagez d’ajouter de la capacité bien à l’avance ; plus de 10 minutes sont nécessaires avant que la capacité de la machine virtuelle soit disponible pour les réplicas/instances de service à placer.

## Supprimer manuellement des machines virtuelles du type de nœud/jeu de mise à l’échelle de machine virtuelle principal

>[AZURE.NOTE] Les services système Service Fabric s’exécutent sur le type de nœud principal de votre cluster. Vous ne devez donc jamais arrêter ou faire descendre en puissance le nombre d’instances dans ces types de nœuds sur une valeur inférieure à celle garantie par le niveau de fiabilité. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md).

Vous devez exécuter les étapes suivantes sur une instance de machine virtuelle à la fois. Ceci permet d’arrêter correctement les services système (et vos services avec état) sur l’instance de machine virtuelle que vous supprimez et les nouveaux réplicas créés sur d’autres nœuds.

1. Exécutez [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) avec « RemoveNode » pour désactiver le nœud que vous souhaitez supprimer (l’instance la plus élevée dans ce type de nœud).

2. Exécutez [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) pour vous assurer que le nœud est en effet passé à l’état désactivé. Si ce n’est pas le cas, patientez jusqu'à ce que le nœud soit désactivé. Vous ne pouvez pas accélérer cette étape.

2. Suivez les exemples/instructions dans la [galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles d’une unité dans ce Nodetype. L’instance supprimée est l’instance de machine virtuelle la plus élevée.

3. Répétez les étapes 1 à 3 selon vos besoins, mais ne faites jamais descendre en puissance le nombre d’instances sur les types de nœuds principaux sur une valeur inférieure à celle garantie par le niveau de fiabilité. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md).

## Supprimer manuellement des machines virtuelles du type de nœud/jeu de mise à l’échelle de machine virtuelle non principal

>[AZURE.NOTE] Pour un service avec état, un certain nombre de nœuds doivent toujours fonctionner afin de maintenir la disponibilité et de conserver l’état de votre service. Au minimum, le nombre de nœuds doit être égal au nombre de jeux de réplicas cibles du service/de la partition.

Vous devez exécuter les étapes suivantes sur une instance de machine virtuelle à la fois. Ceci permet d’arrêter correctement les services système (et vos services avec état) sur l’instance de machine virtuelle que vous supprimez et les nouveaux réplicas créés ailleurs.

1. Exécutez [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) avec « RemoveNode » pour désactiver le nœud que vous souhaitez supprimer (l’instance la plus élevée dans ce type de nœud).

2. Exécutez [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) pour vous assurer que le nœud est en effet passé à l’état désactivé. Si ce n’est pas le cas, patientez jusqu'à ce que le nœud soit désactivé. Vous ne pouvez pas accélérer cette étape.

2. Suivez les exemples/instructions dans la [galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles d’une unité dans ce Nodetype. Ceci supprime l’instance de machine virtuelle la plus élevée.

3. Répétez les étapes 1 à 3 selon vos besoins, mais ne faites jamais descendre en puissance le nombre d’instances sur les types de nœuds principaux sur une valeur inférieure à celle garantie par le niveau de fiabilité. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md).

## Comportements que vous pouvez observer dans Service Fabric Explorer

Lorsque vous faites monter un cluster en puissance, Service Fabric Explorer reflète le nombre de nœuds (instances de jeu de mise à l’échelle de machine virtuelle) qui font partie du cluster. Toutefois, lorsque vous faites descendre un cluster en puissance, le nœud/l’instance de machine virtuelle supprimé reste affiché avec un état défectueux, sauf si vous appelez la [commande Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) avec le nom de nœud approprié.

Voici l’explication de ce comportement.

Les nœuds répertoriés dans Service Fabric Explorer reflètent ce que savent les services système Service Fabric (FM spécifiquement) quant au nombre de nœuds que possède/possédait le cluster. Lorsque vous faites descendre en puissance le jeu de mise à l’échelle de machine virtuelle, la machine virtuelle est supprimée mais le service système FM pense toujours que le nœud (mappé à la machine virtuelle qui a été supprimée) reviendra. Par conséquent, Service Fabric Explorer continue d’afficher ce nœud (même si l’état d’intégrité peut être inconnu ou un état d’erreur).

Pour vous assurer qu’un nœud est supprimé lorsqu’une machine virtuelle est supprimée, vous avez deux possibilités :

1) Choisissez un niveau de durabilité Gold ou Silver (disponible prochainement) pour les types de nœuds de votre cluster, ce qui assure l’intégration de l’infrastructure. Ceci supprimera automatiquement les nœuds de l’état de nos services système (FM) lors de la descente en puissance. Consultez les [détails sur les niveaux de durabilité ici](service-fabric-cluster-capacity.md)

2) Après la descente en puissance de l’instance de machine virtuelle, vous devez appeler [l’applet de commande Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l’état, situation appelée « conservation du quorum ». Il est donc généralement déconseillé d’arrêter toutes les machines du cluster, sauf si vous avez d’abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## Étapes suivantes
Lisez les documents suivants pour en savoir plus sur la planification de la capacité du cluster, la mise à niveau d’un cluster et le partitionnement des services :

- [Planification de la capacité de votre cluster](service-fabric-cluster-capacity.md)
- [Mise à niveau des clusters](service-fabric-cluster-upgrade.md)
- [Partitionnement des services avec état pour une mise à l’échelle maximale](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0921_2016-->