<properties
   pageTitle="Planification de la capacité du cluster Service Fabric | Microsoft Azure"
   description="Considérations en matière de planification de la capacité du cluster Service Fabric. Types de nœuds, durabilité et niveaux de fiabilité"
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


# Considérations en matière de planification de la capacité du cluster Service Fabric

Pour un déploiement de production, la planification de la capacité est une étape importante. Voici certains éléments que vous devez prendre en compte dans ce processus.

- Nombre de types de nœuds avec lesquels votre cluster doit démarrer
- Propriétés de chaque type de nœud (taille, principal ou non, accessibilité sur Internet, nombre de machines virtuelles, etc.)
- Caractéristiques de fiabilité et de durabilité du cluster

Nous allons présenter brièvement chacun de ces éléments.

## Nombre de types de nœuds avec lesquels votre cluster doit démarrer

Tout d’abord, vous devez savoir à quoi va servir le cluster que vous créez et quels sont les types d’applications que vous voulez déployer dans ce cluster. Si vous n’êtes pas sûr de ce à quoi va servir le cluster, vous n’êtes probablement pas encore prêt pour le processus de planification de capacité.

Établissez le nombre de types de nœuds avec lesquels votre cluster doit démarrer. Chaque type de nœud est mappé à un groupe de machines virtuelles identiques. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Par conséquent, pour savoir combien de types de nœud vous avez besoin, vous devez vous poser les questions suivantes :

- Votre application inclut-elle plusieurs services ? Si oui, ces services doivent-ils être publics ou accessibles sur Internet ? Les applications standard contiennent un service de passerelle frontale qui reçoit des entrées d’un client, et un ou plusieurs services principaux qui communiquent avec les services frontaux. Par conséquent, dans ce cas, vous avez besoin d’au moins deux types de nœuds.

- Vos services (qui composent votre application) ont-ils des besoins d’infrastructure différents tels qu’une RAM plus volumineuse ou des cycles processeur plus élevés ? Supposons, par exemple, que l’application que vous voulez déployer contient un service frontal et un service principal. Le service frontal peut s’exécuter sur des machines virtuelles plus petites (par exemple, de taille D2) ayant des ports ouverts sur Internet. Toutefois, le service principal est gourmand en calculs et doit s’exécuter sur des machines virtuelles plus grandes (par exemple, de taille D4, D6, D15) qui ne sont pas connectées à Internet.

 Dans cet exemple, même si vous pouvez décider de placer tous les services sur un seul type de nœud, nous vous recommandons de les placer dans un cluster avec deux types de nœuds. Ainsi, chaque type de nœud a des propriétés distinctes telles que la connectivité Internet ou la taille de machine virtuelle. Le nombre de machines virtuelles peut être mis à l’échelle indépendamment, également.

- Étant donné que vous ne pouvez pas prédire l’avenir, prenez les informations que vous connaissez et choisissez le nombre de types de nœuds dont vos applications ont besoin pour démarrer. Vous pourrez toujours ajouter ou supprimer des types de nœuds ultérieurement. Un cluster Service Fabric doit comprendre au moins un type de nœud.

## Propriétés de chaque type de nœud

Le **type de nœud** peut être considéré comme l’équivalent des rôles dans Cloud Services. Les types de nœuds définissent les tailles de machine virtuelle, le nombre de machines virtuelles et leurs propriétés. Chaque type de nœud qui est défini dans un cluster Service Fabric est configuré en tant que jeu de mise à l’échelle de machine virtuelle distinct. Les groupes de machines virtuelles identiques sont des ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud étant défini comme un groupe de machines virtuelles identiques distinct, il peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité.

Votre cluster peut avoir plusieurs types de nœuds, mais le type de nœud principal (le premier que vous définissez dans le portail) doit avoir au moins cinq machines virtuelles pour les clusters utilisés pour les charges de travail de production (ou au moins trois machines virtuelles pour les clusters de test). Si vous créez le cluster à l’aide d’un modèle Resource Manager, un attribut **is Primary** se trouve sous la définition du type de nœud. Le type de nœud principal est le type de nœud où sont placés les services système de Service Fabric.

### Type de nœud principal
Pour un cluster avec plusieurs types de nœuds, vous devez en choisir un comme nœud principal. Voici les caractéristiques d’un type de nœud principal :

- La taille minimale des machines virtuelles pour le type de nœud principal est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut du niveau de durabilité est Bronze. Faites défiler vers le bas pour plus d’informations sur le niveau de durabilité et sur les valeurs qu’il peut prendre.

- Le nombre minimal de machines virtuelles pour le type de nœud principal est déterminé par le niveau de fiabilité que vous choisissez. La valeur par défaut du niveau de fiabilité est Silver. Faites défiler vers le bas pour plus d’informations sur le niveau de fiabilité et sur les valeurs qu’il peut prendre.

- Les services système de Service Fabric (par exemple, le service Gestionnaire de cluster ou le service Magasin d’images) sont placés sur le type de nœud principal et, par conséquent, la fiabilité et la durabilité du cluster sont déterminées par les valeurs de niveau correspondantes sélectionnées pour le type de nœud principal.

![Capture d’écran d’un cluster qui a deux types de nœuds][SystemServices]


### Type de nœud non principal
Un cluster avec plusieurs types de nœud comprend un type de nœud principal et les autres types de nœud. Voici les caractéristiques d’un type de nœud non principal :

- La taille minimale des machines virtuelles pour ce type de nœud est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut du niveau de durabilité est Bronze. Faites défiler vers le bas pour plus d’informations sur le niveau de durabilité et sur les valeurs qu’il peut prendre.

- Le nombre minimal de machines virtuelles pour ce type de nœud peut être un seul. Toutefois, vous devez choisir ce nombre en fonction du nombre de réplicas des applications/services que vous voulez exécuter dans ce type de nœud. Le nombre de machines virtuelles dans un type de nœud peut être augmenté une fois le cluster déployé.


## Caractéristiques de durabilité du cluster

Le niveau de durabilité est utilisé pour indiquer au système les privilèges dont disposent vos machines virtuelles avec l’infrastructure Azure sous-jacente. Dans le type de nœud principal, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, un redémarrage de la machine virtuelle, une réinitialisation de la machine virtuelle ou une migration de machine virtuelle) qui influe sur la configuration requise du quorum pour les services système et vos services avec état. Dans les types de nœud non principal, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, un redémarrage de la machine virtuelle, une réinitialisation de la machine virtuelle, une migration de machine virtuelle, etc.) qui influe sur la configuration requise du quorum pour vos services avec état qui s’y exécutent.

Ce privilège est exprimé dans les valeurs suivantes :

- Gold : Les travaux de l’infrastructure peuvent être suspendus pour une durée de 2 heures par jour

- Silver : Les travaux de l’infrastructure peuvent être suspendus pour une durée de 30 minutes par jour (cette valeur n’est pas actuellement disponible pour utilisation. Une fois activée, elle sera disponible sur toutes les machines virtuelles standard avec un cœur minimum).

- Bronze : Aucun privilège. Il s’agit de la valeur par défaut.

## Caractéristiques de fiabilité du cluster

Le niveau de fiabilité est utilisé pour définir le nombre de réplicas des services système que vous voulez exécuter dans ce cluster sur le type de nœud principal. Plus le nombre de réplicas est important, plus les services système sont fiables dans votre cluster.

Le niveau de fiabilité peut prendre les valeurs suivantes.

- Platinum : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 9

- Gold : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 7

- Silver : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 5

- Bronze : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 3

>[AZURE.NOTE] Le niveau de fiabilité que vous choisissez détermine le nombre minimal de nœuds que doit contenir votre type de nœud principal. Le niveau de fiabilité n’a aucune incidence sur la taille maximale du cluster. Vous pouvez donc disposer d’un cluster de 20 nœuds s’exécutant au niveau de fiabilité Bronze.

 Vous pouvez choisir de mettre à jour la fiabilité de votre cluster d’un niveau à l’autre. Cette opération déclenche les mises à niveau de cluster nécessaires pour modifier le nombre de jeux de réplicas des services système. Attendez que la mise à niveau en cours se termine avant d’apporter d’autres modifications au cluster, comme l’ajout de nœuds, etc. Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou en exécutant [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Une fois que vous avez terminé la planification de votre capacité et que vous avez configuré un cluster, lisez ce qui suit :
- [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

<!---HONumber=AcomDC_0921_2016-->