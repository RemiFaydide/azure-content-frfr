<properties
	pageTitle="Installation de R sur HDInsight basé sur Linux | Microsoft Azure"
	description="Découvrez comment installer et utiliser R pour personnaliser des clusters Hadoop basés sur Linux."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="larryfr"/>

# Installation et utilisation de R sur des clusters HDInsight Hadoop

Vous pouvez installer R sur n’importe quel type de cluster dans Hadoop sur HDInsight à l’aide de la personnalisation de cluster **Action de script**. Les scientifiques et les analystes de données peuvent ainsi utiliser R pour déployer l’infrastructure de programmation MapReduce/YARN pour traiter de grandes quantités de données sur des clusters Hadoop déployés dans HDInsight.

> [AZURE.IMPORTANT] L’offre de [niveau Premium](https://azure.microsoft.com/pricing/details/hdinsight/) pour HDInsight inclut R Server dans votre cluster HDInsight. Cela permet aux scripts R d’utiliser MapReduce et Spark pour exécuter des calculs distribués. Pour plus d’informations, consultez la section [Prise en main de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md).


## Qu'est-ce que R ?

Le <a href="http://www.r-project.org/" target="_blank">projet R pour le calcul de statistiques</a> se compose d’un langage et d’un environnement open source destinés au calcul de statistiques. R intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues. R est l'environnement de programmation préféré des statisticiens professionnels et des scientifiques dans de nombreux domaines.

Les scripts R peuvent être exécutés dans HDInsight sur des clusters Hadoop personnalisés au moment de leur création à l'aide d'une action de script pour installer l'environnement R. R est compatible avec le stockage d'objets Blob Azure (WASB), ce qui permet aux données stockées à cet emplacement d'être traitées avec R sur HDInsight.

## Ce que fait le script

L’action de script utilisée pour installer R sur votre cluster HDInsight installe les packages Ubuntu suivants. Ceux-ci fournissent une installation R de base :

* [r-base](http://packages.ubuntu.com/precise/r-base): package R GNU de base.
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): packages R GNU auxiliaires.

Les packages RHadoop suivants, qui assurent l’intégration à MapReduce et à HDFS, sont également installés :

* [rmr2](https://github.com/RevolutionAnalytics/rmr2) : permet aux développeurs R d’utiliser Hadoop MapReduce.
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs) : permet aux développeurs R d’utiliser HDFS Hadoop (WASB pour HDInsight).

Les packages R suivants sont également installés :

| Package R | Ce qu’il fournit |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | R de bas niveau pour interface Java. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Intégration de R et de C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Sérialiser/désérialiser des objets R au format JSON. |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Fonctions pour les opérations au niveau du bit sur les vecteurs d’entier. |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) | Créer des résumés de hachage de chiffrement d’objets R |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) | Curry, Compose et autres fonctions d’ordre supérieur |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Restructurez et agrégez des données de manière flexible. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Wrappers simples et cohérents pour les opérations de chaînes courantes. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Outils de fractionnement, d’application et d’association de données. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Outils pour les statistiques de déplacement de la fenêtre, GIF, Base64, ROC AUC, etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Fonctions approximatives de correspondance et de distance de chaîne. |

## Installer R à l’aide d’actions de script

L’action de script suivante permet d’installer R sur un cluster HDInsight. https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    
Cette section explique comment utiliser le script lors de la création d’un cluster à l’aide du portail Azure.

> [AZURE.NOTE] Azure PowerShell, l'interface de ligne de commande Azure (CLI), le Kit de développement logiciel (SDK) .NET HDInsight ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionnement de clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais ne terminez pas l’approvisionnement.

2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations ci-dessous :

	* __NAME__ : saisissez un nom convivial pour l’action de script.
	* __SCRIPT URI__ : https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
	* __HEAD__ : cochez cette option
	* __WORKER__ : cochez cette option.
	* __ZOOKEEPER__ : cochez cette option pour installer le nœud ZooKeeper.
	* __PARAMETERS__ : laissez ce champ vide.

3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuer l’approvisionnement du cluster, comme décrit dans la section [Approvisionnement de cluster HDInsight Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Exécuter des scripts R

Une fois que le cluster a terminé l’approvisionnement, procédez comme suit pour utiliser R pour effectuer une opération MapReduce sur le cluster.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À l’invite `username@hn0-CLUSTERNAME:~$`, entrez la commande suivante pour démarrer une session R interactive :

		R

3. Entrez le programme R suivant. Cette opération génère les nombres 1 à 100, puis les multiplie par 2.

		library(rmr2)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

	La première ligne appelle la bibliothèque RHadoop rmr2 qui est utilisée pour les opérations MapReduce.

	La deuxième ligne génère les valeurs 1 à 100, puis les stocke dans le système de fichiers Hadoop à l’aide de `to.dfs`.

	La troisième ligne crée un processus MapReduce à l’aide de la fonctionnalité fournie par rmr2, puis commence le traitement. Vous devriez voir défiler plusieurs lignes quand le traitement commence.

4. Utilisez ensuite la commande suivante pour afficher le chemin d’accès temporaire dans lequel la sortie MapReduce a été stockée :

		print(calc())

	Il doit ressembler à ceci : `/tmp/file5f615d870ad2`. Pour afficher la sortie réelle, utilisez la commande suivante :

		print(from.dfs(calc))

	La sortie doit ressembler à ceci :

		[1,]  1 2
		[2,]  2 4
		.
		.
		.
		[98,]  98 196
		[99,]  99 198
		[100,] 100 200

5. Pour quitter R, entrez la commande suivante :

		q()


## Étapes suivantes

- [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Hue est une interface utilisateur web qui permet de facilement créer, exécuter et enregistrer des tâches Pig et Hive, ainsi que de parcourir le stockage par défaut pour votre cluster HDInsight.

- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight.

- [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d’effectuer de puissantes opérations de recherche sur des données stockées.

- [Installer Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Utilisez la personnalisation de clusters pour installer Hue sur des clusters HDInsight Hadoop. Hue est un ensemble d’applications web permettant d’interagir avec un cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0921_2016-->