<properties
   pageTitle="Types de nœuds Service Fabric et groupes de machines virtuelles identiques | Microsoft Azure"
   description="Décrit la relation entre les types de nœuds Service Fabric et les groupes de machines virtuelles identiques et la connexion à distance à une instance de groupe de machines virtuelles identiques ou à un nœud de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# Relation entre les types de nœuds Service Fabric et les groupes de machines virtuelles identiques

Les groupes de machines virtuelles identiques sont des ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud qui est défini dans un cluster Service Fabric est configuré en tant que jeu de mise à l’échelle de machine virtuelle distinct. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité.

La capture d’écran suivante montre un cluster qui a deux types de nœuds : frontaux et principaux. Chaque type de nœud comporte cinq nœuds.

![Capture d’écran d’un cluster qui a deux types de nœuds][NodeTypes]

## Mappage des instances du groupe de machines virtuelles identiques sur les nœuds

Comme vous pouvez le constater ci-dessus, les instances du groupe de machines virtuelles identiques démarrent à l’instance 0. Les noms reflètent la numérotation. Par exemple, BackEnd_0 représente l’instance 0 du jeu de mise à l’échelle de machine virtuelle principal. Ce jeu a cinq instances, nommées BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 et BackEnd_4.

Quand vous effectuez une montée en puissance sur un groupe de machines virtuelles identiques, une instance est créée. En règle générale, le nom de la nouvelle instance du groupe de machines virtuelles identiques est le nom du groupe de machines virtuelles identiques suivi du numéro d’instance suivant. Dans notre exemple, il s’agit de BackEnd\_5.


## Mappage des équilibreurs de charge de groupe de machines virtuelles identiques sur chaque type de nœud/groupe de machines virtuelles identiques

Si vous avez déployé votre cluster à partir du portail ou que vous avez utilisé l’exemple de modèle Resource Manager fourni, les équilibreurs de charge pour chaque type de nœud ou jeu de mise à l’échelle de machine virtuelle apparaissent quand vous obtenez la liste de toutes les ressources sous un groupe de ressources.

Le nom ressemble à ceci : **LB-&lt;nom du type de nœud&gt;**. Par exemple, LB-sfcluster4doc-0, comme indiqué dans cette capture d’écran :


![Ressources][Resources]


## Se connecter à distance à une instance du groupe de machines virtuelles identiques ou à un nœud de cluster
Chaque type de nœud qui est défini dans un cluster est configuré comme un jeu de mise à l’échelle de machine virtuelle distinct. Cela signifie que les types de nœuds peuvent subir une montée ou descente en puissance de manière indépendante et comprendre différentes références (SKU) de machine virtuelle. Contrairement aux machines virtuelles à instance unique, les instances du groupe de machines virtuelles identiques n’obtiennent pas une adresse IP virtuelle qui leur est propre. C’est pourquoi rechercher une adresse IP et un port pour se connecter à distance à une instance spécifique peut s’avérer un peu difficile.

Voici la procédure à suivre pour les trouver.

### Étape 1 : Rechercher l’adresse IP virtuelle du type de nœud, puis les règles NAT de trafic entrant pour RDP

Pour ce faire, vous devez obtenir les valeurs des règles NAT de trafic entrant qui ont été établies dans le cadre de la définition des ressources pour **Microsoft.Network/loadBalancers**.

Dans le portail, accédez au panneau Équilibrage de charge, puis à **Paramètres**.

![Panneau Équilibrage de charge][LBBlade]


Dans **Paramètres**, cliquez sur **Règles NAT de trafic entrant**. Vous disposez à présent de l’adresse IP et du port nécessaires pour vous connecter à distance à la première instance du groupe de machines virtuelles identiques. Dans la capture d’écran ci-dessous, il s’agit de **104.42.106.156** et **3389**

![Règles NAT][NATRules]

### Étape 2 : Déterminer le port à utiliser pour se connecter à distance à un nœud ou à une instance du groupe de machines virtuelles identiques spécifique

Plus haut dans ce document, j’ai abordé le mappage des instances du groupe de machines virtuelles identiques sur les nœuds. Nous allons l’utiliser pour déterminer le port exact.

Les ports sont alloués dans l’ordre croissant des instances du jeu de mise à l’échelle de machine virtuelle. Ainsi, dans mon exemple, pour le type de nœud frontal, les ports pour chacune des cinq instances sont les suivants. Vous devez maintenant effectuer le même mappage pour votre instance de jeu de mise à l’échelle de machine virtuelle.

|**Instance de jeu de mise à l’échelle de machine virtuelle**|**Port**|
|-----------------------|--------------------------|
|FrontEnd\_0|3389|
|FrontEnd\_1|3390|
|FrontEnd\_2|3391|
|FrontEnd\_3|3392|
|FrontEnd\_4|3393|
|FrontEnd\_5|3394|


### Étape 3 : Se connecter à distance à une instance spécifique du groupe de machines virtuelles identiques

Dans la capture d’écran ci-dessous, j’utilise Connexion Bureau à distance pour me connecter à l’instance FrontEnd\_1 :

![RDP][RDP]

## Comment modifier les valeurs des plages de ports RDP

### Avant le déploiement du cluster

Quand vous configurez le cluster à l’aide d’un modèle Resource Manager, vous pouvez spécifier la plage dans **inboundNatPools**.

Accédez à la définition de ressource pour **Microsoft.Network/loadBalancers**. Sous celle-ci se trouve la description de **inboundNatPools**. Remplacez les valeurs *frontendPortRangeStart* et *frontendPortRangeEnd*.

![InboundNatPools][InboundNatPools]


### Après le déploiement du cluster
Cette procédure est un peu plus complexe et peut aboutir au recyclage des machines virtuelles. Vous devez maintenant définir de nouvelles valeurs à l’aide d’Azure PowerShell. Vérifiez qu’Azure PowerShell 1.0 ou version ultérieure est installé sur votre ordinateur. Si ce n’est déjà fait, je vous recommande vivement de suivre les étapes décrites dans [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Connectez-vous à votre compte Azure. Si cette commande PowerShell échoue pour une raison quelconque, vous devez vérifier si Azure PowerShell est correctement installé.

```
Login-AzureRmAccount
```

Exécutez la commande suivante pour obtenir les détails de votre équilibrage de charge et découvrir les valeurs qui décrivent **inboundNatPools** :

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

À présent, définissez *frontendPortRangeEnd* et *frontendPortRangeStart* sur les valeurs souhaitées.

```
$PropertiesObject = @{
	#Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## Étapes suivantes

- [Vue d’ensemble de la fonction « Déployer n’importe où » et comparaison avec les clusters gérés par Azure](service-fabric-deploy-anywhere.md)
- [Sécurité des clusters](service-fabric-cluster-security.md)
- [Kit de développement logiciel (SDK) de Service Fabric et prise en main](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

<!---HONumber=AcomDC_0921_2016-->