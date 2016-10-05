<properties
   pageTitle="Configuration de votre cluster autonome | Microsoft Azure"
   description="Cet article explique comment configurer votre cluster Service Fabric autonome ou privé."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="dkshir"/>


# Paramètres de configuration pour un cluster Windows autonome

Cet article explique comment configurer un cluster Service Fabric autonome à l’aide du fichier _**ClusterConfig.JSON**_. Ce fichier est téléchargé sur votre ordinateur de travail lorsque vous [téléchargez le package Service Fabric autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage). Le fichier ClusterConfig.JSON vous permet de spécifier des informations telles que les nœuds Service Fabric et leurs adresses IP, différents types de nœuds sur le cluster, les configurations de sécurité, ainsi que la topologie du réseau en termes de domaines d’erreur et de mise à niveau, pour votre cluster Service Fabric.

Nous allons examiner ci-dessous les différentes sections de ce fichier.

## Configurations de cluster générales
Cette section couvre les configurations spécifiques à de larges clusters, comme indiqué dans l’extrait de code JSON ci-dessous.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

Vous pouvez attribuer un nom convivial à votre cluster Service Fabric en lui assignant la variable **name**. Vous pouvez modifier le valeur **clusterManifestVersion** selon votre configuration ; cette valeur doit être mise à jour avant la mise à niveau de votre configuration Service Fabric. Vous pouvez également conserver la valeur par défaut attribuée à **apiVersion**.


<a id="clusternodes"></a>
## Nœuds sur le cluster
Vous pouvez configurer les nœuds de votre cluster Service Fabric à l’aide de la section **nœuds**, comme le montre l’extrait de code suivant.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un cluster Service Fabric a besoin d’un minimum de 3 nœuds. Vous pouvez ajouter d’autres nœuds à cette section, selon votre configuration. Le tableau suivant décrit les paramètres de configuration de chaque nœud.

|**Configuration de nœuds**|**Description**|
|-----------------------|--------------------------|
|nodeName|Vous pouvez attribuer n’importe quel nom convivial au nœud.|
|iPAddress|Identifiez l’adresse IP de votre nœud en ouvrant une fenêtre de commande puis en tapant `ipconfig`. Notez l’adresse IPV4 et assignez-la à la variable **iPAddress**.|
|nodeTypeRef|Chaque nœud peut être associé à un type de nœud différent. Les [types de nœuds](#nodetypes) sont définis dans la section ci-dessous.|
|faultDomain|Les domaines d'erreur permettent aux administrateurs de cluster de définir les nœuds physiques qui sont susceptibles de rencontrer un échec en même temps en raison des dépendances physiques partagées.|
|upgradeDomain|Les domaines de mise à niveau décrivent des ensembles de nœuds qui sont arrêtés pour les mises à niveau Service Fabric, à peu près au même moment. Vous pouvez choisir les nœuds à attribuer aux domaines de mise à niveau car ils ne sont pas limités par des exigences physiques.| 


## **Propriétés** du cluster

La section **properties** du fichier ClusterConfig.JSON permet de configurer le cluster comme suit.

### **diagnosticsStore**
Vous pouvez configurer des paramètres pour activer les diagnostics et corriger les défaillances de nœud et du cluster à l’aide de la section **diagnosticsStore** comme illustré dans l’extrait de code suivant.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

La section **metadata** est une description du diagnostic de votre cluster et peut être définie selon votre installation. Ces variables vous aident à collecter les journaux de suivi ETW, les vidages sur incident ainsi que les compteurs de performance. Consultez les sections [Journal de suivi](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) et [Suivi ETW](https://msdn.microsoft.com/library/ms751538.aspx) pour plus d’informations sur les journaux de suivi ETW. Tous les journaux, notamment les [vidages sur incident](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) et les [compteurs de performance](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) peuvent être dirigés vers le dossier **connectionString** sur votre ordinateur. Vous pouvez également utiliser **AzureStorage** pour le stockage des diagnostics. Voici un exemple d’extrait de code.

	"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### **security** 
La section **security** est nécessaire pour garantir la sécurité d’un cluster Service Fabric autonome. L’extrait de code suivant montre une partie de cette section.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
		. . .
	}

La section **metadata** est une description de votre cluster sécurisé et peut être définie selon votre installation. Les propriétés **ClusterCredentialType** et **ServerCredentialType** déterminent le type de sécurité que le cluster et les nœuds implémenteront. Elles peuvent avoir la valeur *X509* pour une sécurité basée sur un certificat, ou *Windows* pour une sécurité de type Azure Active Directory. Le reste de la section **security** varie selon le type de sécurité. Consultez les rubriques [Sécurité basée sur des certificats dans un cluster autonome](service-fabric-windows-cluster-x509-security.md) ou [Sécurité Windows dans un cluster autonome](service-fabric-windows-cluster-windows-security.md) pour plus d’informations sur la façon de remplir le reste de la section **security**.

### **reliabilityLevel**
La propriété **reliabilityLevel** définit le nombre de copies des services système qui peuvent s’exécuter sur les nœuds principaux du cluster. Cette valeur augmente la fiabilité de ces services et, par conséquent, du cluster. Vous pouvez définir cette variable sur *Bronze*, *Silver*, *Gold* ou *Platinum* pour 3, 5, 7 ou 9 copies de ces services, respectivement. Reportez-vous à l’exemple ci-dessous.

	"reliabilityLevel": "Bronze",
	
Notez que dans la mesure où un nœud principal exécute une copie unique des services système, vous avez besoin d’au moins 3 nœuds principaux pour le niveau de fiabilité *Bronze*, 5 pour *Silver*, 7 pour *Gold* et 9 pour *Platinum*.


<a id="nodetypes"></a>
### **nodeTypes**
La section **nodeTypes** décrit le type des nœuds de votre cluster. Au moins un type de nœud doit être spécifié pour un cluster, comme indiqué dans l’extrait de code ci-dessous.

	"nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
			"startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

La valeur **name** représente le nom convivial de ce type de nœud particulier. Pour créer un nœud de ce type de nœud, vous devez affecter le nom convivial pour ce type de nœud en utilisant la variable **nodeTypeRef**, comme indiqué dans la section [Nœuds sur le cluster](#clusternodes) ci-dessus. Pour chaque type de nœud, vous pouvez définir plusieurs points de terminaison pour la connexion à ce cluster. Vous pouvez choisir n’importe quel numéro de port pour ces points de terminaison de connexion, à condition qu’ils n’entrent pas en conflit avec d’autres points de terminaison de ce cluster. Un cluster avec plusieurs types de nœud inclura un type de nœud principal dont la propriété **isPrimary** est définie sur *true*. Pour les nœuds restants, la propriété **isPrimary** est définie sur *false*. Consultez la rubrique [Considérations en matière de planification de la capacité du cluster Service Fabric](service-fabric-cluster-capacity.md) pour plus d’informations sur les valeurs **nodeTypes** et **reliabilityLevel** selon la capacité de votre cluster, et pour connaître la différence entre les types de nœud principal et non principal.


### **fabricSettings**
Cette section vous permet de définir les répertoires racine des données et journaux Service Fabric. Vous pouvez personnaliser ces éléments uniquement lors de la création initiale du cluster. Voici un exemple d’extrait de cette section.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\ProgramData\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\ProgramData\SF\Log"
    }]

Nous vous recommandons d’utiliser un lecteur autre que celui du système d’exploitation pour FabricDataRoot et FabricLogRoot pour plus de fiabilité en cas de défaillance du système d’exploitation. Notez que si vous personnalisez uniquement la racine des données, la racine du journal sera placée un niveau en dessous de la racine des données.


## Étapes suivantes

Une fois que vous disposez d’un fichier ClusterConfig.JSON complètement configuré selon votre cluster autonome, vous pouvez déployer votre cluster en suivant les instructions de l’article [Création d’un cluster Azure Service Fabric en local ou dans le cloud](service-fabric-cluster-creation-for-windows-server.md) puis passer à la section [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!---HONumber=AcomDC_0921_2016-->