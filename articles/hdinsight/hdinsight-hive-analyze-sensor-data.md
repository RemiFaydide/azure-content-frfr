<properties
	pageTitle="Analyse des données de capteur au moyen de Hive et de Hadoop | Microsoft Azure"
	description="Découvrez comment analyser des données de capteur au moyen de la console de requête Hive avec HDInsight (Hadoop), puis visualiser les données dans Microsoft Excel avec Power View."
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
	ms.date="09/20/2016" 
	ms.author="larryfr"/>

#Analyse des données de capteur à l’aide de la console de requête Hive sur Hadoop dans HDInsight

Découvrez comment analyser des données de capteur au moyen de la console de requête Hive avec HDInsight (Hadoop), puis visualiser les données dans Microsoft Excel à l’aide de Power View.

> [AZURE.NOTE] Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight Windows.

Dans cet exemple, vous allez utiliser Hive pour traiter les données d'historique produites par les systèmes de chauffage, de ventilation et de climatisation, et ainsi identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée. Vous apprendrez à :

- Créer des tables HIVE pour interroger des données stockées dans des fichiers au format CSV.
- Créer des requêtes HIVE pour analyser les données.
- Utiliser Microsoft Excel pour vous connecter à HDInsight (au moyen d’une connexion ODBC) pour extraire les données analysées.
- Utiliser Power View pour visualiser les données.

![A diagram of the solution architecture](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##Composants requis

* Un cluster HDInsight (Hadoop) : consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md) pour des informations sur la création d’un cluster

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel est utilisé pour la visualisation des données avec [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=fr-FR&rs=fr-FR&ad=US).

* [Pilote ODBC Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

##Exécution de l'exemple

1. À partir d’un navigateur web, accédez à l'URL suivante. Remplacez `<clustername>` par le nom de votre cluster HDInsight :

	 	https://<clustername>.azurehdinsight.net

	À l’invite, authentifiez-vous au moyen du nom d’utilisateur et du mot de passe d’administrateur que vous avez utilisés lors de l’approvisionnement de ce cluster.

2. Dans la page web qui s’ouvre, cliquez sur l’onglet **Galerie de mise en route**, puis sous la catégorie **Solutions avec des exemples de données**, cliquez sur l’exemple **Analyse des données de capteur**.

    ![Image Galerie de mise en route](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Suivez les instructions fournies dans la page web pour terminer l'exemple.

<!---HONumber=AcomDC_0921_2016-->