<properties
   pageTitle="Créer et gérer un cluster Azure Service Fabric autonome | Microsoft Azure"
   description="Apprenez à créer et à gérer un cluster Azure Service Fabric sur n’importe quel ordinateur (physique ou virtuel) exécutant Windows Server, qu’il soit local ou dans un cloud."
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
   ms.date="09/15/2016"
   ms.author="dkshir;chackdan"/>


# Créer et gérer un cluster exécuté sur Windows Server

Azure Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement contenant un ensemble d’ordinateurs Windows Server interconnectés, que ce soit en local ou avec un fournisseur cloud. Service Fabric fournit un package d’installation pour créer des clusters Service Fabric, appelé package Windows Server autonome.

Cet article vous guide tout au long des étapes de création locale d’un cluster à l’aide d’un package autonome Service Fabric, même s’il peut être facilement adapté à tout autre environnement, notamment d’autres fournisseurs de cloud.

>[AZURE.NOTE] Ce package Windows Server autonome peut contenir des fonctionnalités qui sont actuellement en version préliminaire et n’est pas pris en charge pour les charges de production. Pour afficher la liste de fonctionnalités en version préliminaire, faites défiler jusqu’à la fin de ce document. [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=733084) si vous souhaitez télécharger une copie du CLUF (Contrat de Licence Utilisateur Final).

<a id="downloadpackage"></a>
## Télécharger le package autonome Service Fabric


[Téléchargez le package autonome Service Fabric pour Windows Server 2012 R2 et ultérieur](http://go.microsoft.com/fwlink/?LinkId=730690), nommé *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

>[AZURE.NOTE] Si vous utilisez des navigateurs Internet Explorer ou Microsoft Edge pour télécharger ce package, vous devez ajouter le site [http://download.microsoft.com](http://download.microsoft.com) aux sites de confiance dans l’intranet pour empêcher que la balise Zones ne soit écrite dans les fichiers lorsque le fichier zip est téléchargé

 ![TustedZone][TrustedZone]

Vous trouverez les fichiers suivants dans le package de téléchargement :

|**Nom de fichier**|**Brève description**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|Fichier CAB qui contient les fichiers binaires déployés sur chaque ordinateur du cluster.|
|ClusterConfig.Unsecure.DevCluster.json|Exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster de développement avec machine virtuelle/ordinateur unique à trois nœuds non sécurisé, notamment les informations de chaque nœud du cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|Exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster avec plusieurs machines virtuelles/ordinateurs non sécurisé, notamment les informations de chaque ordinateur du cluster.|
|ClusterConfig.Windows.DevCluster.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster de développement avec machine virtuelle/ordinateur unique à trois nœuds sécurisé, notamment les informations de chaque nœud du cluster. Le cluster est sécurisé à l’aide d[’identités Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster avec plusieurs machines virtuelles/ordinateurs sécurisé, utilisant la sécurité Windows, notamment les informations de chaque ordinateur du cluster sécurisé. Le cluster est sécurisé à l’aide d[’identités Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster de développement avec machine virtuelle/ordinateur unique à trois nœuds sécurisé, notamment les informations de chaque nœud du cluster. Le cluster est sécurisé à l’aide de certificats x509.|
|ClusterConfig.x509.MultiMachine.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour le cluster à plusieurs machines virtuelles/ordinateurs sécurisé, notamment les informations de chaque nœud du cluster sécurisé. Le cluster est sécurisé à l’aide de certificats x509.|
|EULA.txt|Les termes du contrat de licence pour l’utilisation du package Windows Server autonome Microsoft Azure Service Fabric. [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=733084) si vous souhaitez télécharger une copie du CLUF (Contrat de Licence Utilisateur Final).|
|Readme.txt|Lien vers les notes de publication et des instructions d’installation de base. Il s’agit d’une partie des instructions que vous trouverez dans cette page.|
|CreateServiceFabricCluster.ps1|Script PowerShell qui crée le cluster à l’aide des paramètres figurant dans le fichier ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script PowerShell qui supprime un cluster à l’aide des paramètres figurant dans le fichier ClusterConfig.json.|
|AddNode.ps1|Script PowerShell pour l’ajout d’un nœud à un cluster déployé existant.|
|RemoveNode.ps1|Script PowerShell pour la suppression d’un nœud d’un cluster déployé existant.|


## Planifier et préparer le déploiement de cluster
Exécutez les étapes suivantes avant de créer votre cluster.

### Étape 1 : planifier votre infrastructure de cluster
Vous allez créer un cluster Service Fabric sur les ordinateurs que vous avez pour décider des types de défaillance auxquels le cluster doit survivre. Par exemple, devez-vous séparer les lignes d’alimentation des connexions internet reliées à ces ordinateurs ? Prenez en outre en compte la sécurité physique de ces ordinateurs. Où l’emplacement physique se trouve-t-il et qui a besoin d’y accéder ? Une fois ces décisions prises, vous pouvez mapper logiquement les ordinateurs sur les différents domaines d’erreur (faites défiler vers le bas pour plus d’informations). La planification de l’infrastructure pour les clusters de production est plus complexe que pour les clusters de test.

<a id="preparemachines"></a>
### Étape 2 : préparer les ordinateurs pour répondre aux conditions préalables
Conditions préalables pour chaque ordinateur que vous souhaitez ajouter au cluster :

- 4 Go de mémoire minimum recommandés
- Connectivité réseau : assurez-vous que les ordinateurs sont sur un réseau/des réseaux sécurisé(s).
- Windows Server 2012 R2 ou Windows Server 2012 ([KB2858668](https://support.microsoft.com/kb/2858668) doit être installé).
- [.NET Framework 4.5.1 ou une version ultérieure](https://www.microsoft.com/download/details.aspx?id=40773), installation complète.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- L’administrateur de cluster déployant et configurant le cluster doit disposer de [privilèges d’administrateur](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) sur chaque ordinateur.
- Le [service RemoteRegistry](https://technet.microsoft.com/library/cc754820) doit être exécuté sur tous les ordinateurs.

### Étape 3 : déterminer la taille initiale du cluster
Le runtime Service Fabric est déployé sur chaque nœud d’un cluster Service Fabric autonome, et les nœuds sont membres du cluster. Un déploiement de production type comprend un nœud par instance du système d’exploitation (physique ou virtuel). La taille de cluster est déterminée par vos besoins métier. Toutefois, vous devez disposer d’une taille minimale de cluster de trois nœuds (ordinateurs/machines virtuelles). À des fins de développement, vous pouvez avoir plusieurs nœuds sur un ordinateur donné. Dans un environnement de production, Service Fabric ne prend en charge qu’un seul nœud par ordinateur physique ou virtuel.

### Étape 4 : déterminer le nombre de domaines d’erreur et de domaines de mise à niveau
Un **domaine d’erreur (FD)** est une unité physique de défaillance directement liée à l’infrastructure physique dans les centres de données. Un domaine d’erreur est constitué de composants matériels (ordinateurs, commutateurs, réseau, etc.) qui partagent un point de défaillance unique. Bien qu’il n’existe aucun mappage 1:1 entre les domaines d’erreur et les racks, chaque rack peut être considéré au sens large comme un domaine d’erreur. Lorsque vous envisagez d’utiliser les nœuds de votre cluster, nous vous recommandons fortement de distribuer les nœuds sur au moins trois domaines d’erreur.

Quand vous spécifiez des domaines d’erreur dans *ClusterConfig.json*, vous pouvez choisir le nom de chaque domaine d’erreur. Service Fabric prend en charge les domaines d’erreur hiérarchiques pour vous permettre d’y refléter votre topologie d’infrastructure. Par exemple, les domaines d’erreur suivants sont valides :

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **domaine de mise à niveau (UD)** est une unité logique de nœuds. Pendant une mise à niveau orchestrée par Service Fabric (mise à niveau de l’application ou du cluster), tous les nœuds dans un domaine de mise à niveau sont mis hors service pour effectuer la mise à niveau alors que les nœuds dans d’autres domaines de mise à niveau restent disponibles pour répondre aux requêtes. Les mises à niveau du microprogramme effectuées sur vos ordinateurs ne respectent pas les domaines de mise à niveau. Vous devez les exécuter sur un ordinateur à la fois.

L’approche la plus simple de ces concepts consiste à considérer les domaines d’erreur comme unité de défaillance non planifiée et les domaines mise à niveau comme unité de maintenance planifiée.

Quand vous spécifiez des domaines de mise à niveau dans *ClusterConfig.json*, vous pouvez choisir le nom de chaque domaine de mise à niveau. Par exemple, les éléments suivants sont valides :

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Pour plus d’informations sur les domaines de mise à niveau et les domaines d’erreur, consultez l’article [Description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Étape 5 : télécharger le package autonome Service Fabric pour Windows Server
[Téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur un ordinateur de déploiement qui ne fait pas partie du cluster ou sur l’un des ordinateurs qui fera partie de votre cluster.

<a id="createcluster"></a>
## Création de votre cluster

Une fois les étapes décrites dans la section Planification et préparation ci-dessus effectuées, vous êtes prêt à créer votre cluster.

### Étape 1 : modifier la configuration du cluster
Le cluster est décrit dans un fichier *ClusterConfig.json*. Pour plus d’informations sur les sections de ce fichier, consultez l’article [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md). Ouvrez l’un des fichiers *ClusterConfig.json* à partir du package que vous avez téléchargé et modifiez les paramètres suivants :

|**Paramètres de configuration**|**Description**|
|-----------------------|--------------------------|
|NodeTypes|Les types de nœuds permettent de séparer vos nœuds de cluster en différents groupes. Un cluster doit comprendre au moins un NodeType. Tous les nœuds d’un groupe ont les caractéristiques communes suivantes. <br> *Name* : il s’agit du nom du type de nœud. <br> *EndPoints* : il s’agit de différents points de terminaison (ports) nommés qui sont associés à ce type de nœud. Vous pouvez utiliser n’importe quel numéro de port, tant qu’il n’entre en conflit avec aucun autre élément dans ce manifeste et n’est pas déjà en cours d’utilisation par d’autres programmes sur les ordinateurs/machines virtuelles <br> *PlacementProperties* : elles décrivent les propriétés pour ce type de nœud que vous utiliserez ensuite comme contraintes de placement pour les services système ou vos services. Ces propriétés sont des paires clé/valeur définies par l'utilisateur qui fournissent des métadonnées supplémentaires pour un nœud donné. La présence d’un disque dur ou d’une carte graphique sur le nœud, le nombre de rotations du disque dur, les noyaux et d’autres propriétés physiques sont des exemples de propriétés du nœud. <br> *Capacities* - les capacités du nœud définissent le nom et la quantité d’une ressource spécifique disponible sur un nœud particulier pour consommation. Par exemple, un nœud peut définir qu’il possède la capacité pour une mesure appelée « MemoryInMb » et qu’il dispose de 2 048 Mo de mémoire disponible par défaut. Ces capacités permettent de garantir que les services qui nécessitent une quantité spécifique de ressources sont placés sur des nœuds dans lesquels ces ressources restent disponibles au cours de l’exécution.|
|Nœuds|Les détails de chacun des nœuds qui feront partie du cluster (type de nœud, nom de nœud, adresse IP, domaine d’erreur et domaine de mise à niveau du nœud). Les ordinateurs sur lesquels vous souhaitez créer le cluster doivent être répertoriés ici avec leur adresse IP. <br> Si vous utilisez les mêmes adresses IP pour tous les nœuds, un cluster à boîtier unique est créé. Vous pouvez l’utiliser à des fins de test. Les clusters à boîtier unique ne doivent pas servir au déploiement de charges de travail de production.|

### Étape 2 : exécuter le script de création de cluster
Une fois que vous avez modifié la configuration de cluster dans le document JSON et ajouté toutes les informations de nœud, exécutez le script PowerShell de création de cluster *CreateServiceFabricCluster.ps1* à partir du dossier de package et transmettez-lui le chemin du fichier de configuration JSON et acceptez le CLUF (Contrat de Licence Utilisateur Final).

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté peut faire partie du cluster ou non.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA true
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA true
```

>[AZURE.NOTE] Les journaux de déploiement sont disponibles localement sur une machine virtuelle/un ordinateur sur lequel vous avez exécuté le script PowerShell CreateServiceFabricCluster. Ils se trouvent dans un sous-dossier nommé « DeploymentTraces » sous le dossier où vous avez exécuté la commande Powershell. En outre, pour voir si Service Fabric a été déployé correctement sur un ordinateur, vous pouvez rechercher les fichiers installés dans le répertoire C:\\ProgramData et vérifier si les processus FabricHost.exe et Fabric.exe sont mentionnés comme étant en cours d’exécution dans le Gestionnaire des tâches.

### Étape 3 : se connecter au cluster

Reportez-vous à [ce document](service-fabric-connect-to-secure-cluster.md) pour obtenir des instructions de connexion à un cluster sécurisé.

Exécutez la commande PowerShell suivante pour vous connecter à un cluster non sécurisé

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### Étape 4 : Afficher Service Fabric Explorer

Désormais, vous pouvez vous connecter au cluster avec Service Fabric Explorer directement à partir de l’un des ordinateurs avec http://localhost:19080/Explorer/index.html ou à distance avec http://<*adresse\_IP\_ordinateur*>:19080/Explorer/index.html



## Ajouter et supprimer des nœuds à/de votre cluster

Vous pouvez ajouter des nœuds à votre cluster Service Fabric autonome ou en supprimer en fonction de l’évolution de vos besoins. Lisez l’article [Ajouter ou supprimer des nœuds à/d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md) pour obtenir des instructions détaillées.


## Supprimer votre cluster

Pour supprimer un cluster, exécutez le script Powershell *RemoveServiceFabricCluster.ps1* à partir du dossier de package et transmettez le chemin du fichier de configuration JSON et spécifiez un emplacement pour le fichier de la suppression (cette dernière étape est facultative).

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté peut faire partie du cluster ou non.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```

## Fonctionnalités préliminaires incluses dans ce package

L’ensemble du package est actuellement en version préliminaire.

## Étapes suivantes
- [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
- [Ajouter ou supprimer des nœuds à/d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Créer un cluster Service Fabric autonome avec des machines virtuelles Azure sous Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
- [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)


<!--Image references-->
[TrustedZone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

<!---HONumber=AcomDC_0921_2016-->