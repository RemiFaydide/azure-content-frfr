<properties
	pageTitle="Fonctionnalités de haute disponibilité de HDInsight sous Linux (Hadoop) | Microsoft Azure"
	description="Découvrez comment les clusters HDInsight Linux améliorent la fiabilité et la disponibilité en utilisant un nœud principal supplémentaire. Vous allez apprendre dans quelle mesure les services Hadoop tels qu’Ambari et Hive sont concernés, et comment se connecter à chaque nœud principal à l’aide de SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="larryfr"/>

#Disponibilité et fiabilité des clusters Hadoop dans HDInsight

Hadoop garantit de hauts niveaux de disponibilité et de fiabilité en distribuant des copies redondantes des services et des données aux différents nœuds d’un cluster. Toutefois, les distributions standard de Hadoop ne comportent généralement qu’un seul nœud principal. Toute défaillance du nœud principal unique peut entraîner un arrêt de fonctionnement du cluster.

Pour éviter ce problème, les clusters HDInsight Linux sur Azure fournissent deux nœuds principaux afin d’augmenter la disponibilité et la fiabilité des services et travaux Hadoop en cours d’exécution.

> [AZURE.NOTE] Les étapes de ce document sont spécifiques aux clusters HDInsight sous Linux. Si vous utilisez un cluster Windows, consultez [Disponibilité et fiabilité des clusters Hadoop Windows dans HDInsight](hdinsight-high-availability.md) pour plus d'informations spécifiques Windows.

##Vue d’ensemble des nœuds

Les nœuds d’un cluster HDInsight sont implémentés à l’aide de machines virtuelles Azure. En cas de défaillance d’un nœud, ce dernier est mis hors connexion, et un autre nœud est créé pour remplacer le nœud défaillant. Pendant que le nœud se trouve à l’état hors connexion, un autre nœud du même type est utilisé jusqu’à ce que le nouveau nœud soit mis en ligne.

> [AZURE.NOTE] Si le nœud était en train d’analyser des données au moment où il a cessé de fonctionner, le travail d’analyse qu’il avait effectué jusque-là est perdu. Le travail qui était en cours sur le nœud défaillant est alors soumis à un autre nœud.

Les sections ci-après décrivent les différents types de nœuds utilisés avec HDInsight. Les types de nœuds utilisables varient selon le type de cluster concerné. Par exemple, un type de cluster Hadoop ne comporte aucun nœud Nimbus. Pour plus d’informations sur les nœuds utilisés par les types de clusters HDInsight, voir la section Types de cluster de l’article [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

###Nœuds principaux

Certaines implémentations de Hadoop comportent un seul nœud principal hébergeant les services et les composants qui gèrent efficacement la défaillance des nœuds de travail. Mais tout arrêt des services principaux en cours d'exécution sur le nœud principal peut provoquer l'arrêt du cluster.

Les clusters HDInsight fournissent un nœud principal secondaire, ce qui permet aux services et composants de continuer de s'exécuter sur le nœud secondaire en cas de panne sur le serveur principal.

> [AZURE.IMPORTANT] Les deux nœuds principaux sont actifs et s'exécutent simultanément sur le cluster. Certains services tels que HDFS ou YARN, ne sont plus « actifs » sur un nœud principal à toute donnée temps (et « En attente » sur le nœud de tête). D'autres services tels que HiveServer2 ou Hive MetaStore sont actifs sur les deux nœuds principaux simultanément.

Les nœuds principaux (et les autres nœuds dans HDInsight) possèdent une valeur numérique comme partie du nom d’hôte du nœud. Par exemple, `hn0-CLUSTERNAME` ou `hn4-CLUSTERNAME`.

> [AZURE.IMPORTANT] N’associez pas la valeur numérique à un nœud si celui-ci est primaire ou secondaire ; la valeur numérique est uniquement présente afin de fournir un nom unique pour chaque nœud.

###Nœuds Nimbus

Pour les clusters Storm, les nœuds Nimbus offrent des fonctionnalités comparables au service Hadoop JobTracker en distribuant et en surveillant le traitement dans l’ensemble des nœuds de travail. HDInsight fournit 2 nœuds Nimbus pour le type de cluster Storm.

###Nœuds Zookeeper

Les nœuds [ZooKeeper](http://zookeeper.apache.org/) (ZKs) sont utilisés pour la sélection primaire de services principaux sur les nœuds principaux et garantissent que les services, les nœuds (worker) et les passerelles savent sur quel nœud principal, un service principal est actif. Par défaut, HDInsight fournit 3 nœuds ZooKeeper.

###Nœuds de travail

Les nœuds de travail effectuent l’analyse de données proprement dite lorsqu’un travail est soumis au cluster. En cas de défaillance d’un nœud de travail, la tâche en cours d’exécution sur ce dernier est soumise à un autre nœud de travail. Par défaut, HDInsight crée 4 nœuds de travail ; toutefois, vous pouvez modifier ce nombre selon vos besoins pendant et après la création d’un cluster.

###Nœud de périmètre

Un nœud de périmètre ne participe pas activement à l’analyse des données au sein du cluster, mais est plutôt utilisé par les développeurs ou les scientifiques des données lorsque ces derniers travaillent avec Hadoop. Le nœud de périmètre se trouve dans le même réseau virtuel Azure que les autres nœuds du cluster et peut accéder directement à tous les autres nœuds. Étant donné qu’il ne prend pas part à l’analyse des données pour le cluster, il peut être utilisé sans détourner les ressources nécessaires aux travaux d’analyse ou aux services Hadoop critiques.

Pour l’instant, R Server sur HDInsight est le seul type de cluster à fournir un nœud de périmètre par défaut. Dans le cas de R Server sur HDInsight, le nœud de périmètre est utilisé pour tester le code R localement sur le nœud avant de le soumettre au cluster à des fins de traitement distribué.

Le modèle [Create a Linux-based HDInsight cluster with Hue on an Edge Node](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/) (Créer un cluster HDInsight basé sur Linux avec Hue sur un nœud de périmètre) est un exemple de modèle permettant de créer un type de cluster Hadoop comportant un nœud de périmètre.


## Accès aux nœuds

L’accès au cluster sur Internet est fourni par le biais d’une passerelle publique et se limite à une connexion aux nœuds principaux et, dans le cas d’un cluster de type R Server sur HDInsight, au nœud de périmètre. L’accès aux services qui s’exécutent sur les nœuds principaux n’est pas affecté par l’existence de plusieurs nœuds principaux, car la passerelle publique achemine les demandes vers le nœud principal qui héberge le service demandé. Par exemple, si Ambari est actuellement hébergé sur le nœud principal secondaire, la passerelle achemine les demandes entrantes pour Ambari vers ce nœud.

Lors de l’accès au cluster à l’aide de SSH, la connexion via le port 22 (valeur par défaut pour SSH) entraîne la connexion au nœud principal primaire. Une connexion via le port 23 entraîne une connexion au nœud principal secondaire. Par exemple, `ssh username@mycluster-ssh.azurehdinsight.net` établit une connexion au nœud principal primaire du cluster nommé __mycluster__.

> [AZURE.NOTE] Cela s’applique également aux protocoles basés sur SSH, comme le protocole FTP sécurisé (SFTP).

Le nœud de périmètre fourni avec les clusters R Server sur HDInsight est également directement accessible à l’aide de SSH par le biais du port 22. Par exemple, `ssh username@RServer.mycluster.ssh.azurehdinsight.net` établit une connexion au nœud de périmètre pour un cluster R Server sur HDInsight nommé __mycluster__.

### Noms de domaine pleinement qualifiés internes (FQDN)

Les nœuds présents dans un cluster HDInsight sont dotés d'une adresse IP interne et d'un nom de domaine complet uniquement accessibles depuis le cluster (par exemple, une session SSH sur le nœud principal ou un travail en cours d'exécution sur le cluster.) Lorsque vous accédez à des services sur le cluster à l'aide de l'adresse IP ou du nom de domaine complet interne, vous devez utiliser Ambari pour vérifier l'adresse IP ou le nom de domaine complet à utiliser.

Par exemple, le service Oozie peut s'exécuter uniquement sur un nœud principal et l'utilisation de la commande `oozie` à partir d'une session SSH requiert l'URL du service. Elle peut être extraite à partir d'Ambari à l'aide de la commande suivante :

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

elle renverra une valeur semblable à celle qui suit, qui contient l'URL interne à utiliser avec la commande `oozie` :

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### Accès à d’autres types de nœuds

Vous pouvez vous connecter aux nœuds qui ne sont pas directement accessibles sur Internet en utilisant les méthodes suivantes.

* __SSH__ : une fois connecté à un nœud principal au moyen de SSH, vous pouvez utiliser SSH à partir de ce nœud principal pour vous connecter à d’autres nœuds du cluster.
* __Tunnel SSH__ : si vous avez besoin d’accéder à un service web hébergé sur l’un des nœuds qui ne sont pas exposés à Internet, vous devez [utiliser un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).
* __Réseau virtuel Azure__ : si votre cluster HDInsight fait partie intégrante d’un réseau virtuel Azure, toutes les ressources du même réseau virtuel peuvent accéder directement à tous les nœuds du cluster.

## Comment contrôler l'état d'un service

Vous pouvez utiliser l’interface utilisateur web d’Ambari ou l’API REST Ambari pour vérifier l’état des services qui s’exécutent sur les nœuds principaux.

###Interface utilisateur web d'Ambari

L'interface utilisateur web d'Ambari est visible à https://CLUSTERNAME.azurehdinsight.net. Remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, saisissez les informations d'identification utilisateur de votre cluster. Le nom d'utilisateur HTTP par défaut est **admin** et le mot de passe est le mot de passe que vous avez saisi lors de la création du cluster.

Lorsque vous arrivez sur la page Ambari, les services installés apparaissent à gauche de la page.

![Services installés](./media/hdinsight-high-availability-linux/services.png)

Une série d'icônes s'affichent en regard d'un service pour indiquer son état. Des alertes liées à un service peuvent être affichées à l'aide du lien **Alertes** situé en haut de la page. Vous pouvez sélectionner chaque service pour afficher plus d'informations sur ce dernier.

La page de service fournit des informations sur l'état et la configuration de chaque service. Il ne fournit pas d'informations sur le nœud principal sur lequel le service s'exécute. Pour afficher ces informations, utilisez le lien **Hôtes** en haut de la page. Cela permettra d'afficher les hôtes au sein du cluster, notamment les nœuds principaux.

![liste des hôtes](./media/hdinsight-high-availability-linux/hosts.png)

La sélection du lien de l'un des nœuds principaux affichera les services et les composants qui s'exécutent sur ce nœud.

![État du composant](./media/hdinsight-high-availability-linux/nodeservices.png)

###API Ambari REST

L’API REST Ambari est disponible sur Internet, et la passerelle publique gère les demandes d’acheminement vers le nœud principal qui héberge actuellement l’API REST.

Vous pouvez utiliser la commande suivante pour vérifier l'état d'un service via l'API REST Ambari :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Remplacez **Mot de passe** par l'utilisateur HTTP (admin), le mot de passe de compte

* Remplacez **CLUSTERNAME** par le nom de votre cluster.

* Remplacez **SERVICENAME** par le nom du service dont l'état doit être contrôlé

Par exemple, pour vérifier l'état du service **HDFS** dans un cluster nommé **moncluster**, avec un mot de passe **mot de passe**, vous devez utiliser la commande suivante :

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La réponse ressemblera à ce qui suit :

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

L’URL indique que le service est en cours d’exécution sur un nœud principal nommé __hn0-CLUSTERNAME__.

L'URL indique que le service est en cours d'exécution ou **démarré**.

Si vous ne connaissez pas les services installés sur le cluster, vous pouvez utiliser les éléments suivants pour récupérer une liste :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Composants du service

Les services peuvent contenir des composants dont vous souhaitez vérifier l'état individuellement. Par exemple, HDFS contient le composant NameNode. Pour afficher des informations relatives à un composant, la commande serait :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Si vous ne connaissez pas les services fournis pour n service, vous pouvez utiliser les éléments suivants pour récupérer une liste :

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## Accès aux fichiers journaux sur les nœuds principaux

###SSH

Lorsque vous êtes connecté à un nœud principal via SSH, les fichiers journaux se trouvent sous **/var/log**. Par exemple, **/var/log/hadoop-yarn/yarn** contiennent les journaux correspondant à YARN.

Chaque nœud principal peut contenir des entrées de journal uniques. Vous devez donc vérifier les journaux correspondant aux deux.

###SFTP

Vous pouvez également vous connecter au nœud principal à l’aide du protocole FTP SSH, ou protocole FTP sécurisé (SFTP), et télécharger directement les fichiers journaux.

Comme lors de l’utilisation d’un client SSH, lorsque vous vous connectez au cluster, vous devez fournir le nom du compte d’utilisateur SSH et l’adresse SSH du cluster. Par exemple, `sftp username@mycluster-ssh.azurehdinsight.net`. Vous devez également spécifier le mot de passe du compte lorsque vous y êtes invité, ou bien fournir une clé publique à l’aide du paramètre `-i`.

Une fois connecté, vous voyez apparaître une invite `sftp>`. À partir de cette invite, vous pouvez changer de répertoire, ainsi que charger et télécharger des fichiers. Par exemple, les commandes ci-après activent le répertoire **/var/log/hadoop/hdfs**, puis téléchargent tous les fichiers dans ce répertoire.

    cd /var/log/hadoop/hdfs
    get *

Pour obtenir la liste des commandes disponibles, entrez `help` au niveau de l’invite `sftp>`.

> [AZURE.NOTE] Il existe également des interfaces graphiques qui vous permettent de visualiser le système de fichiers lorsque vous êtes connecté à l’aide du protocole SFTP. Par exemple, [MobaXTerm](http://mobaxterm.mobatek.net/) vous offre la possibilité de parcourir le système de fichiers au moyen d’une interface semblable à l’Explorateur Windows.


###Ambari

> [AZURE.NOTE] L'accès aux fichiers journaux via Ambari nécessite un tunnel SSH, les sites web pour les services individuels ne sont pas exposés publiquement sur Internet. Pour des informations sur l'utilisation d'un tunnel SSH, consultez [Utilisation de SSH Tunneling pour accéder à l'interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d'autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md).

À partir de l'interface utilisateur Web d'Ambari, sélectionnez le service dont vous souhaitez afficher les journaux (par exemple, YARN) et utilisez **Liens rapides** pour sélectionner le nœud principal dont vous voulez afficher les journaux.

![Utilisation des liens rapides pour afficher les journaux](./media/hdinsight-high-availability-linux/viewlogs.png)

## Configuration de la taille des nœuds ##

La taille d’un nœud est uniquement sélectionnable lors de la création du cluster. Vous trouverez une liste des différentes tailles de machines virtuelles disponibles pour HDInsight, y compris le noyau, la mémoire et le stockage local pour chacune, sur la [page Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Lorsque vous créez un nouveau cluster, vous pouvez spécifier la taille des nœuds. Les éléments suivants fournissent des informations sur la façon de spécifier la taille à l'aide du [portail Azure][preview-portal], [Azure PowerShell][azure-powershell] et l'[interface de ligne de commande Azure][azure-cli] :

* **Portail Azure** : lorsque vous créez un cluster, vous avez la possibilité de définir la taille (niveau tarifaire) des nœuds principaux, des nœuds de travail et (s’ils sont utilisés par le type de cluster) des nœuds ZooKeeper pour le cluster :

	![Image de l'Assistant de création de cluster avec sélection de taille de nœud](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Interface de ligne de commande Azure** : lorsque vous utilisez la commande `azure hdinsight cluster create`, vous pouvez définir la taille des nœuds principaux, de travail et ZooKeeper en utilisant les paramètres `--headNodeSize`, `--workerNodeSize` et `--zookeeperNodeSize`.

* **Azure PowerShell** : lorsque vous utilisez l’applet de commande `New-AzureRmHDInsightCluster`, vous pouvez définir la taille des nœuds principaux, de travail et ZooKeeper en utilisant les paramètres `-HeadNodeVMSize`, `-WorkerNodeSize` et `-ZookeeperNodeSize`.

##Étapes suivantes

Dans ce document, vous avez appris comment HDInsight Azure offre une haute disponibilité pour Hadoop. Utilisez les éléments qui suivent pour en savoir plus sur les éléments mentionnés dans ce document.

- [Référence REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Installation et configuration Azure CLI](../xplat-cli-install.md)

- [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md)

- [Gestion de HDInsight à l'aide d'Ambari](hdinsight-hadoop-manage-ambari.md)

- [Approvisionnement de clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0921_2016-->