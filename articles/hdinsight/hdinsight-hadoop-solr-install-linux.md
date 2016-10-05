<properties
	pageTitle="Utilisation d’une action de script pour installer Solr sur HDInsight basé sur Linux | Microsoft Azure"
	description="Découvrez comment installer Solr sur des clusters Hadoop HDInsight basés sur Linux à l’aide des actions de script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="larryfr"/>

# Installation et utilisation de Solr sur des clusters HDInsight Hadoop

Dans cette rubrique, vous allez apprendre à installer Solr sur Azure HDInsight en utilisant une action de script. Solr est une puissante plateforme de recherche qui fournit des fonctionnalités de recherche au niveau de l'entreprise pour les données gérées par Hadoop. Après avoir installé Solr sur un cluster HDInsight, vous apprendrez également comment rechercher des données à l’aide de Solr.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent un cluster HDInsight Linux. Pour plus d’informations sur l’utilisation de Solr avec un cluster Windows, consultez [Installation et utilisation de Solr sur des clusters HDInsight Hadoop (Windows)](hdinsight-hadoop-solr-install.md)

L’exemple de script utilisé dans cette rubrique crée un cluster Solr avec une configuration spécifique. Si vous souhaitez configurer le cluster Solr avec d'autres collections, partitions, schémas, réplicas, etc., vous devez modifier le script et les fichiers binaires Solr en conséquence.

## <a name="whatis"></a>Présentation de Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) est une plateforme de recherche d’entreprise qui permet d’effectuer de puissantes opérations de recherche en texte intégral sur des données. Alors que Hadoop permet de stocker et de gérer de grandes quantités de données, Apache Solr fournit les fonctionnalités de recherche nécessaires pour les récupérer rapidement. Cette rubrique contient les instructions de personnalisation d’un cluster HDInsight pour installer Solr.

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés, tels que Solr, bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple : [Hadoop](http://hadoop.apache.org/).

## Ce que fait le script

Ce script effectue les modifications suivantes au cluster HDInsight :

* Installe Solr dans `/usr/hdp/current/solr`
* Crée un nouvel utilisateur, __solrusr__, qui est utilisé pour exécuter le service Solr.
* Définit __solruser__ comme propriétaire de `/usr/hdp/current/solr`
* Ajoute une configuration [Upstart](http://upstart.ubuntu.com/) qui démarre Solr si un nœud de cluster redémarre. Solr est également automatique démarré sur les nœuds de cluster après l’installation

## <a name="install"></a>Installer Solr à l’aide d’actions de script

Un exemple de script d’installation de Solr sur un cluster HDInsight est disponible à l’emplacement suivant.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Cette section explique comment utiliser l’exemple de script dans le cadre de la création d’un cluster à l’aide du portail Azure.

> [AZURE.NOTE] Azure PowerShell, l'interface de ligne de commande Azure (CLI), le Kit de développement logiciel (SDK) .NET HDInsight ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionnement de clusters HDInsight sous Linux](hdinsight-provision-linux-clusters.md#portal), mais ne terminez pas l’approvisionnement.

2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations ci-dessous :

	* __NAME__ : saisissez un nom convivial pour l’action de script.
	* __SCRIPT URI__ : https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
	* __HEAD__ : cochez cette option
	* __WORKER__ : cochez cette option
	* __ZOOKEEPER__ : cochez cette option pour installer le nœud ZooKeeper.
	* __PARAMETERS__ : laissez ce champ vide.

3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuez l’approvisionnement du cluster, comme décrit dans la section [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Utilisation de Solr dans HDInsight

### Mode d’indexation

Vous devez commencer par indexer Solr avec quelques fichiers de données. Vous pouvez ensuite utiliser Solr pour exécuter des requêtes de recherche sur les données indexées. Procédez comme suit pour ajouter des données d’exemple vers Solr, puis interrogez la requête :

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] Étapes de création de ce document utilisent ultérieurement un tunnel SSL pour se connecter à l’interface utilisateur Solr. Pour utiliser ces étapes, vous devez établir un tunnel SSL, puis configurer votre navigateur pour l’utiliser.
	>
	> Pour plus d’informations, consultez [Utiliser SSH Tunneling pour accéder à l’interface web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Internet](hdinsight-linux-ambari-ssh-tunnel.md)

2. Pour obtenir des exemples de données Solr, utilisez les commandes suivantes :

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	Le résultat suivant s’affiche sur la console :

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	L’utilitaire post.jar indexe Solr avec deux exemples de documents : **solr.xml** et **monitor.xml**. Celles-ci seront stockées dans __collection1__ dans Solr.

3. Pour interroger la requête API REST exposée par Solr, utilisez les éléments suivants :

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	Cela permet d’émettre une requête par rapport à __collection1__ pour tous les documents correspondants à __\*:\*__ (encodé sous la forme \*%3A\* dans la chaîne de requête) et que la réponse doit être retournée au format JSON. La réponse doit sembler similaire à ce qui suit :

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Utilisation du tableau de bord Solr

Le tableau de bord Solr est une interface utilisateur web qui permet de travailler avec le mode série sur Solr via votre navigateur web. Le tableau de bord Solr n’est pas exposé directement sur Internet à partir de votre cluster HDInsight, mais doit être accessible à l’aide d’un tunnel SSH. Pour plus d’informations sur l’utilisation d’un tunnel SSH, consultez [Utiliser SSH Tunneling pour accéder une interface web Ambari, ResourceManager, JobHistory, NameNode, Oozie et l’autre interface utilisateur web](hdinsight-linux-ambari-ssh-tunnel.md)

Une fois que vous avez établi un tunnel SSH, procédez comme suit pour utiliser le tableau de bord Solr :

1. Déterminez le nom d’hôte du nœud principal primaire :

    1. Utilisez SSH pour vous connecter au cluster sur le port 22. Par exemple, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` où __USERNAME__ est votre nom d’utilisateur SSH et __CLUSTERNAME__ est le nom de votre cluster.

        Pour plus d’informations sur l’utilisation de SSH, consultez les documents suivants :

        * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
    3. Utilisez la commande suivante pour obtenir le nom d’hôte complet :

            hostname -f

        Cette commande renvoie un nom similaire à ce qui suit :

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
        Il s’agit du nom d’hôte qui doit être utilisé dans les étapes suivantes.
    
1. Dans votre navigateur, connectez-vous à __http://HOSTNAME:8983/solr/#/__, où __HOSTNAME\_\_ correspond au nom que vous avez déterminé aux étapes précédentes.

    La requête doit être routée via le tunnel SSH vers le nœud principal de votre cluster HDInsight. Une page similaire à celle ci-dessous doit s'afficher :

	![Image du tableau de bord Solr.](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. Dans le volet de gauche, utilisez la liste déroulante **Sélecteur de base** pour sélectionner **collection1**. Plusieurs entrées doivent apparaître sous __collection1__.

3. Dans les entrées sous __collection1__, sélectionnez __Requête__. Utilisez les valeurs suivantes pour remplir la page de recherche :

	* Dans la zone de texte **q**, entrez **\*:**\*. Tous les documents indexés dans Solr sont alors renvoyés. Si vous souhaitez rechercher une chaîne spécifique dans les documents, vous pouvez la saisir à cet emplacement.

	* Sélectionnez le format de sortie dans la zone de texte **wt**. La valeur par défaut est **json**.

	Enfin, sélectionnez le bouton **Exécuter la requête** au bas de la page de recherche.

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

	La sortie renvoie les deux documents que nous avions utilisés pour indexer Solr. La sortie se présente comme suit :

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Démarrage et arrêt de Solr

Si vous souhaitez arrêter ou démarrer Solar manuellement, utilisez les commandes suivantes :

	sudo stop solr

	sudo start solr

## Sauvegarde de données indexées

Il est conseillé de sauvegarder les données indexées à partir de nœuds de cluster Solr sur un stockage d’objets blob Azure. Pour ce faire, procédez comme suit :

1. Connectez-vous au cluster à l’aide de SSH, puis utilisez la commande suivante pour obtenir le nom d’hôte du nœud principal :

        hostname -f
        
2. Utilisez ce qui suit pour créer un instantané des données indexées. Remplacez __HOSTNAME__ par le nom retourné par la commande précédente :

		curl http://HOSTNAME:8983/solr/replication?command=backup

	La réponse doit ressembler à ceci :

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. Ensuite, accédez au répertoire __/usr/hdp/current/solr/example/solr__. Il y aura ici un sous-répertoire pour chaque collection. Chaque répertoire de la collection contient un répertoire __data__ où l’instantané de cette collection se trouve.

	Par exemple, si vous avez utilisé les étapes précédentes pour indexer les exemples de documents, le répertoire __/usr/hdp/current/solr/example/solr/collection1/data__ doit maintenant contenir un répertoire nommé __snapshot.###########__ où les caractères # correspondent à la date et à l’heure de l’instantané.

3. Créer une archive compressée du dossier d’instantanés à l’aide d’une commande semblable à ce qui suit :

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	Une archive nommée __snapshot.20150806185338855.tgz__, comprenant le contenu du répertoire __snapshot.20150806185338855__ est ainsi créée.

3. Vous pouvez ensuite stocker l’archive vers le stockage principal du cluster à l’aide de la commande suivante :

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Voulez-vous créer un répertoire dédié pour stocker les instantanés Solr. Par exemple, `hadoop fs -mkdir /solrbackup`.

Pour plus d’informations sur l’utilisation de sauvegardes et de restaurations Solr, consultez [Création et restauration de sauvegardes de SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## Voir aussi

- [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Hue est une interface utilisateur web qui permet de facilement créer, exécuter et enregistrer des tâches Pig et Hive, ainsi que de parcourir le stockage par défaut pour votre cluster HDInsight.

- [Installation de R sur des clusters HDInsight][hdinsight-install-r]. Utilisez la personnalisation de clusters pour installer R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.

- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques à l’aide de Hadoop et peut être utilisé avec Azure HDInsight.

- [Installer Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Utilisez la personnalisation de clusters pour installer Hue sur des clusters HDInsight Hadoop. Hue est un ensemble d’applications web permettant d’interagir avec un cluster Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0921_2016-->