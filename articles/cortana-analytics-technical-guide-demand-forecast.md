<properties
	pageTitle="Prévision de la demande énergétique : guide technique | Microsoft Azure"
	description="Guide technique de l’utilisation du modèle de solution avec Microsoft Cortana Intelligence pour prévoir la demande énergétique."
	services="cortana-analytics"
	documentationCenter=""
	authors="yijichen"
	manager="ilanr9"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="inqiu;yijichen;ilanr9"/>

# Guide technique de l’utilisation du modèle de solution Cortana Intelligence pour prévoir la demande énergétique

## **Vue d'ensemble**

Les modèles de solution visent à accélérer le processus de création d’une démonstration E2E sur Cortana Intelligence Suite. Un modèle déployé fournit à votre abonnement les composants Cortana Intelligence nécessaires et établit les relations entre eux. Il amorce également le pipeline de données avec des exemples de données générées à partir d'une application de simulation de données. Téléchargez le simulateur de données en cliquant sur le lien fourni et installez-le sur votre ordinateur local. Pour obtenir des instructions sur l'utilisation du simulateur, consultez le fichier readme.txt. Les données issues du simulateur alimentent le pipeline de données et génèrent des prédictions Machine Learning que vous pouvez ensuite visualiser dans le tableau de bord Power BI.

Le modèle de solution se trouve [ici](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Le processus de déploiement vous guide à travers plusieurs étapes permettant de configurer les informations d’identification de votre solution. Veillez à consigner les informations d’identification, telles que le nom de la solution, le nom d’utilisateur et le mot de passe, que vous renseignez pendant le déploiement.

L’objectif de ce document est de vous présenter l’architecture de référence et les différents composants configurés dans votre abonnement dans le cadre de ce modèle de solution. Ce document explique également comment remplacer les exemples de données par vos propres données réelles afin de vous permettre de visualiser des analyses et des prévisions dérivées de vos propres données. Il décrit enfin les éléments du modèle de solution que vous devez modifier si vous souhaitez personnaliser la solution avec vos propres données. Vous trouverez à la fin de ce document des instructions sur la façon de configurer le tableau de bord Power BI pour ce modèle de solution.

## **Grandes lignes**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### Architecture
Le déploiement de la solution entraîne l’activation de divers services Azure dans Cortana Analytics Suite (*c’est-à-dire* Event Hub, Stream Analytics, HDInsight, Data Factory, Machine Learning, *etc.*). Le schéma d’architecture ci-dessus illustre globalement la construction de bout en bout du modèle de solution de prévision de la demande énergétique. Vous pouvez passer en revue ces services en cliquant dessus sur le schéma du modèle de solution créé avec le déploiement de la solution. Les sections suivantes décrivent chaque élément du schéma.

## **Source et ingestion de données**

### Source de données de synthèse

Pour ce modèle, la source de données utilisée est générée à partir d’une application de bureau que vous allez télécharger et exécuter localement après la réussite du déploiement. Vous trouverez les instructions de téléchargement et d’installation de cette application dans la barre des propriétés lorsque vous sélectionnez le premier nœud appelé Energy Forecasting Data Simulator sur le schéma du modèle de solution. Cette application alimente le service [Azure Event Hub](#azure-event-hub) avec des points de données, ou événements, qui seront utilisés dans le reste du flux de la solution.

L’application de génération d’événements alimente le service Azure Event Hub uniquement lorsqu’elle est exécutée sur votre ordinateur.

### Azure Event Hub

Le service [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) est le destinataire des données fournies par la source de données de synthèse décrite ci-dessus.

## **Préparation et analyse des données**


### Azure Stream Analytics

Le service [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) sert à fournir des analyses en temps quasi-réel sur le flux d’entrée du service [Azure Event Hub](#azure-event-hub) et à publier les résultats dans un tableau de bord [Power BI](https://powerbi.microsoft.com). Il permet également d’archiver, dans le service [Azure Storage](https://azure.microsoft.com/services/storage/), tous les événements bruts entrants qui seront ensuite traités par le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### Agrégation personnalisée HDInsight

Le service Azure HD Insight est utilisé pour exécuter des scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orchestrés par Azure Data Factory) pour agréger les événements bruts ayant été archivés à l’aide du service Azure Stream Analytics.

### Azure Machine Learning

Le service [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) est utilisé (et orchestré par Azure Data Factory) pour prédire la consommation électrique future d’une région particulière compte tenu des entrées reçues.

## **Publication des données**


### Service de base de données SQL Azure

Le service de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) sert à stocker (sous le contrôle d’Azure Data Factory) les prédictions reçues par le service Azure Machine Learning et qui seront utilisées dans le tableau de bord [Power BI](https://powerbi.microsoft.com).

## **Consommation des données**

### Power BI

Le service [Power BI](https://powerbi.microsoft.com) permet d’afficher un tableau de bord contenant les agrégations fournies par le service [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), ainsi que les résultats des prévisions de la demande stockés dans [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) qui ont été générés à l’aide du service [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Pour obtenir des instructions sur la façon de configurer le tableau de bord Power BI pour ce modèle de solution, consultez la section ci-dessous.

## **Comment importer vos propres données**

Cette section explique comment importer vos propres données dans Azure et décrit les éléments à modifier compte tenu des données que vous importez dans cette architecture.

Les jeux de données que vous importez ont peu de chance de correspondre parfaitement à ce modèle de solution. Il est important de bien comprendre vos données et besoins pour déterminer la façon dont vous allez modifier ce modèle pour l’utiliser avec vos propres données. Si vous n’êtes pas familiarisé avec le service Azure Machine Learning, vous pouvez consulter la page [Didacticiel sur l’apprentissage automatique : création de votre première expérience dans Azure Machine Learning Studio](machine-learning\machine-learning-create-experiment.md) pour en obtenir un premier aperçu.

Dans les sections suivantes, nous allons décrire les sections du modèle que vous devrez modifier lors de l’introduction d’un nouveau jeu de données.

### Azure Event Hub

Le service [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) est très générique, au point que les données peuvent être publiées sur le hub au format CSV ou JSON. Bien que le service Azure Event Hub n’implique aucun traitement particulier, il est important de comprendre les données qui l’alimentent.

Ce document ne décrit pas le mode de réception de vos données, mais vous pouvez facilement envoyer des événements ou des données vers un service Azure Event Hub à l’aide de [l’API Event Hub](event-hubs\event-hubs-programming-guide.md).

### Azure Stream Analytics

Le service [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) permet de fournir des analyses en temps quasi-réel grâce à une lecture des flux de données et à l’envoi de données vers diverses sources.

Dans le cas du modèle de solution de prévision de la demande énergétique, la requête Azure Stream Analytics se compose de deux sous-requêtes, chacune utilisant les événements du service Azure Event Hub en tant qu’entrées et générant des sorties à deux emplacements distincts. Ces sorties comprennent un jeu de données Power BI et un emplacement Azure Storage.

Pour accéder à la requête[ Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) :

-   Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/)

-   Recherchez les travaux Stream Analytics ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) qui ont été générés au moment du déploiement de la solution. L’un effectue un Push des données vers Blob Storage (par exemple, mytest1streaming432822asablob), et l'autre vers Power BI (par exemple, mytest1streaming432822asapbi).


-   Sélectionnez

    -   ***INPUTS*** pour afficher l’entrée de la requête

    -   ***QUERY*** pour afficher la requête elle-même

    -   ***OUTPUTS*** pour afficher les différentes sorties

Pour plus d’informations sur la construction des requêtes Azure Stream Analytics, consultez la [référence de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) sur MSDN.

Dans cette solution, le travail Azure Stream Analytics qui génère le jeu de données avec des informations d’analyse en temps quasi-réel concernant le flux de données entrant dans un tableau de bord Power BI est fourni dans le cadre de ce modèle de solution. Étant donné que le format des données entrantes est implicitement connu, ces requêtes devront être modifiées en fonction de votre format de données.

L’autre travail Azure Stream Analytics génère tous les événements [Event Hub](https://azure.microsoft.com/services/event-hubs/) dans [Azure Storage](https://azure.microsoft.com/services/storage/) et ne nécessite donc aucune modification, quel que soit votre format de données, puisque les informations d’événement complètes sont transmises en continu vers le stockage.

### Azure Data Factory

Le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestre le déplacement et le traitement des données. Dans le modèle de solution de prévision de la demande énergétique, la fabrique de données est constituée de douze [pipelines](data-factory\data-factory-create-pipelines.md) qui déplacent et traitent les données à l’aide de différentes technologies.

  Vous pouvez accéder à votre fabrique de données en ouvrant le nœud Data Factory en bas du diagramme du modèle de solution créé avec le déploiement de la solution. Vous êtes alors redirigé vers la fabrique de données sur votre portail de gestion Azure. Si vous rencontrez des erreurs dans vos jeux de données, vous pouvez les ignorer car ces erreurs sont liées à la fabrique de données déployée avant le démarrage du générateur de données. Ces erreurs ne perturbent pas le fonctionnement de votre fabrique de données.

Cette section décrit les [pipelines](data-factory\data-factory-create-pipelines.md) et les [activités](data-factory\data-factory-create-pipelines.md) nécessaires qui sont contenus dans le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Vous trouverez ci-dessous une vue schématique de la solution.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinq des pipelines de cette fabrique contiennent des scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) utilisés pour partitionner et agréger les données. Quand ils sont référencés, les scripts apparaissent dans le compte [Azure Storage](https://azure.microsoft.com/services/storage/) créé pendant l’installation, à l’emplacement suivant : demandforecasting\\\script\\\hive\\\ (ou https://[Your nom de votre solution].blob.core.windows.net/demandforecasting).

De la même manière que les requêtes [Azure Stream Analytics](#azure-stream-analytics-1), les scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) ont une connaissance du format des données entrantes ; vous devrez donc modifier ces requêtes en fonction de votre format de données et des exigences [d’ingénierie des caractéristiques](machine-learning\machine-learning-feature-selection-and-engineering.md).

#### *AggregateDemandDataTo1HrPipeline*

Ce [pipeline](data-factory\data-factory-create-pipelines.md) contient une seule activité, [HDInsightHive](data-factory\data-factory-hive-activity.md). Cette activité utilise un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour agréger les données à la demande diffusées en continu toutes les 10 secondes au niveau de la sous-station en données horaires au niveau de la région et les placer dans [Azure Storage](https://azure.microsoft.com/services/storage/) par le biais du travail Azure Stream Analytics.

Le script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) utilisé pour cette tâche de partitionnement est ***AggregateDemandRegion1Hr.hql***


#### *LoadHistoryDemandDataPipeline*

Ce [pipeline](data-factory\data-factory-create-pipelines.md) contient deux activités :
- [HDInsightHive](data-factory\data-factory-hive-activity.md), qui utilise un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) exécutant un script Hive pour agréger les données de demande d’historique horaire au niveau de la sous-station en données horaires au niveau de la région et les placer dans Azure Storage pendant le travail Azure Stream Analytics

- [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), qui déplace les données agrégées de l’objet blob Azure Storage vers la base de données SQL Microsoft Azure approvisionnée dans le cadre de l’installation du modèle de solution.

Le script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche est ***AggregateDemandHistoryRegion.hql***.


#### *MLScoringRegionXPipeline*

Ces [pipelines](data-factory\data-factory-create-pipelines.md) comprennent plusieurs activités dont le résultat final contient les prédictions notées à partir de l’expérience Azure Machine Learning associée à ce modèle de solution. Ils sont quasiment identiques : ce qui les différencie, c’est qu’ils gèrent chacun une région différente. Pour cela, un RegionID différent est passé dans le pipeline ADF et le script Hive pour chaque région. Ce pipeline contient les activités suivantes :
-	[HDInsightHive](data-factory\data-factory-hive-activity.md), qui utilise un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) exécutant un script Hive pour effectuer les agrégations et l’ingénierie de fonctionnalités nécessaires pour l’expérience Azure Machine Learning. Les scripts Hive pour cette tâche sont le ***PrepareMLInputRegionX.hql*** respectif ;

-	Activité [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’activité [HDInsightHive](data-factory\data-factory-hive-activity.md) vers un objet blob Azure Storage unique, accessible à l’activité [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-	Activité [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) qui appelle l’expérience Azure Machine Learning pour placer les résultats dans un objet blob Azure Storage unique.

#### *CopyScoredResultRegionXPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une seule activité, [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx). Cette activité déplace les résultats de l’expérience Azure Machine Learning du ***MLScoringRegionXPipeline*** respectif vers la base de données SQL Microsoft Azure approvisionnée dans le cadre de l’installation du modèle de solution.

#### *CopyAggDemandPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une seule activité, [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx). Cette activité déplace les données à la demande agrégées en continu de ***LoadHistoryDemandDataPipeline*** vers la base de données SQL Microsoft Azure approvisionnée dans le cadre de l’installation du modèle de solution.

#### *CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une seule activité, [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx). Cette activité déplace les données de référence de Region/Substation/Topologygeo chargées dans l’objet blob Azure Storage dans le cadre de l’installation du modèle de solution vers la base de données SQL Microsoft Azure approvisionnée dans le cadre de l’installation du modèle de solution.

### Azure Machine Learning
L’expérience [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) utilisée pour ce modèle de solution fournit la prédiction de la demande de la région. L’expérience est spécifique au jeu de données consommé et doit donc être modifiée ou remplacée compte tenu des données qui y sont importées.

## **Surveiller la progression**
Une fois le Générateur de données lancé, l’alimentation du pipeline commence et les différents composants de votre solution entrent en action selon les commandes émises par la fabrique de données. Il existe deux méthodes de surveillance du pipeline.

1. Vérifiez les données à partir d’Azure Blob Storage.

	Un des travaux Stream Analytics enregistre les données brutes entrantes dans Blob Storage. Si vous cliquez sur le composant **Azure Blob Storage** de votre solution dans l’écran à partir duquel vous avez déployé la solution, puis cliquez sur **Ouvrir** dans le volet droit, vous accédez au [portail de gestion Azure](https://portal.azure.com). Une fois dans le portail, cliquez sur **Objets BLOB**. Dans le panneau suivant, vous verrez une liste de conteneurs. Cliquez sur **energysadata**. Le panneau suivant affiche le dossier **demandongoing**. Celui-ci comprend des dossiers nommés par exemple date=2016-01-28, etc. Si vous voyez ces dossiers, cela signifie que les données brutes sont correctement générées sur votre ordinateur et stockées dans Blob Storage. Vous devriez voir des fichiers avec des tailles finies en Mo dans ces dossiers.

2. Vérifiez les données à partir d’Azure SQL Database.

	La dernière étape du pipeline consiste à enregistrer des données (par exemple, les prédictions générées à partir de Machine Learning) dans SQL Database. Vous devrez peut-être attendre un maximum de deux heures pour que les données s'affichent dans SQL Database. Le [portail de gestion Azure](https://manage.windowsazure.com/) offre un moyen de surveiller la quantité de données disponible dans votre base de données SQL. Dans le volet gauche, identifiez les bases de données SQL ![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) et cliquez dessus. Recherchez ensuite votre base de données (par exemple, demo123456db), puis cliquez dessus. Dans la page suivante, sous la section **Se connecter à votre base de données**, cliquez sur **Exécuter des requêtes Transact-SQL sur votre base de données SQL**.

	Ici, vous pouvez cliquer sur Nouvelle requête et entrez une requête pour obtenir le nombre de lignes (par exemple, select count(*) from DemandRealHourly). À mesure que votre base de données augmente, le nombre de lignes de la table doit augmenter.

3. Vérifiez les données à partir du tableau de bord Power BI.

	Vous pouvez configurer un tableau de bord de chemin à chaud Power BI pour surveiller les données brutes entrantes. Suivez les instructions contenues dans la section « Tableau de bord Power BI ».



## **Tableau de bord Power BI**

### Vue d'ensemble

Cette section décrit comment configurer un tableau de bord Power BI afin de visualiser vos données en temps réel à partir d’Azure Stream Analytics (chemin à chaud), ainsi que les résultats des prévisions à partir d’Azure Machine Learning (chemin à froid).


### Configurer le tableau de bord de chemin à chaud

Dans les étapes suivantes, nous allons vous expliquer comment visualiser la sortie de données en temps réel des tâches Stream Analytics qui ont été générées au moment du déploiement de la solution. Les étapes suivantes nécessitent un compte [Power BI en ligne](http://www.powerbi.com/). Si vous ne possédez pas de compte, vous pouvez en [créer un](https://powerbi.microsoft.com/pricing).

1.  Ajouter une sortie Power BI dans Azure Stream Analytics (ASA).

    -  Pour configurer la sortie de votre travail Azure Stream Analytics en tant que tableau de bord Power BI, vous devez suivre les instructions contenues dans [Azure Stream Analytics et Power BI : tableau de bord d’analyse permettant de visualiser en temps réel les données de diffusion en continu](stream-analytics-power-bi-dashboard.md).

	- Dans votre [portail de gestion Azure](https://manage.windowsazure.com), recherchez le travail Stream Analytics. Le nom du travail doit être au format nom\_solution+"streamingjob"+nombre\_aléatoire+"asapbi" (c’est-à-dire, demostreamingjob123456asapbi).

	- Ajoutez une sortie PowerBI pour le travail ASA. Définissez l**’alias de sortie** sur **« PBIoutput »**. Dans **Nom du jeu de données** et **Nom de la table**, entrez **EnergyStreamData**. Une fois que vous avez ajouté la sortie, cliquez sur **Démarrer** en bas de la page pour démarrer le travail Stream Analytics. Un message de confirmation doit s’afficher (*par exemple* : La tâche Stream Analytics ’myteststreamingjob12345asablob’ a bien démarré).

2. Se connecter à [Power BI en ligne](http://www.powerbi.com)

    -   Dans la section Jeux de données du volet gauche de Mon espace de travail, un nouveau jeu de données doit apparaître dans le volet gauche de Power BI. Il s'agit des données de diffusion en continu qui ont fait l’objet d’un Push à partir d'Azure Stream Analytics au cours de l'étape précédente.

    -   Assurez-vous que le volet ***Visualisations*** est ouvert et qu’il s’affiche à droite de l’écran.

3. Créez la vignette Demand by Timestamp :
	-	Cliquez sur le jeu de données **EnergyStreamData** dans la section Jeux de données du volet gauche.

	-	Cliquez sur l’icône **Courbes** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

	-	Cliquez sur EnergyStreamData dans le panneau **Champs**.

	-	Cliquez sur **Horodatage** et vérifiez qu’il apparaît sous Axe. Cliquez sur **Charger** et vérifiez qu’il apparaît sous Valeurs.

	-	Cliquez sur **ENREGISTRER** en haut de l’écran et nommez le rapport EnergyStreamDataReport. Le rapport EnergyStreamDataReport s’affiche dans la section Rapports du volet Navigateur de gauche.

	-	Cliquez sur l’icône **Épingler au tableau de bord**![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) dans le coin supérieur droit de ce graphique en courbes. Une fenêtre Épingler au tableau de bord peut apparaître pour vous permettre de sélectionner un tableau de bord. Sélectionnez EnergyStreamDataReport, puis cliquez sur Épingler.

	-	Dans le tableau de bord, pointez la souris sur cette vignette, cliquez sur l’icône Modifier située en haut à droite pour donner le titre « Demand by Timestamp ».

4.	Créez d’autres vignettes de tableau de bord en fonction des jeux de données appropriés. La vue Tableau de bord finale est illustrée ci-dessous. ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### Configurer le tableau de bord de chemin à froid
Dans le pipeline de données de chemin à froid, l'objectif principal est d’obtenir la prévision de la demande de chaque région. Power BI se connecte à une base de données SQL Azure en tant que source de données où sont stockés les résultats de la prédiction.

> [AZURE.NOTE] \(1) Il faut plusieurs heures pour collecter suffisamment de résultats de prévisions pour le tableau de bord. Nous vous recommandons donc de démarrer ce processus 2 à 3 heures après le lancement du Générateur de données. 2) Avant d’effectuer cette étape, vous devez télécharger et installer le logiciel gratuit [Power BI Desktop](https://powerbi.microsoft.com/desktop).



1.  Obtenez les informations d’identification de la base de données.

    Avant de passer aux étapes suivantes, vous devez rechercher **le nom du serveur de base de données, le nom de la base de données, le nom d’utilisateur et le mot de passe**. Voici les étapes à suivre pour obtenir ces informations.

    -   Une fois que la **« Base de données SQL Azure »** apparaît en vert sur votre schéma de modèle de solution, cliquez dessus, puis cliquez sur **« Ouvrir »**. Vous accédez alors au portail de gestion Azure qui affiche une page d'informations sur votre base de données.

    -   La page contient une section Base de données dans laquelle figure la base de données que vous avez créée. Le nom de votre base de données doit être au format **nom\_solution+nombre\_aléatoire+’db’** (par exemple, montest12345db).

	-	Cliquez sur votre base de données. Dans le nouveau panneau contextuel, le nom de votre serveur de base de données apparaît dans la partie supérieure. Le nom de votre serveur de base de données doit être au format **nom\_solution+nombre\_aléatoire+’database.windows.net,1433’** (par exemple, montest12345.database.windows.net,1433).

	-   Les **nom d’utilisateur** et **mot de passe** de votre base de données sont identiques aux nom d’utilisateur et mot de passe précédemment enregistrés au moment du déploiement de la solution.

2.	Mettez à jour la source de données du fichier Power BI de chemin à froid.
	-  Vérifiez que vous avez installé la dernière version de [Power BI Desktop](https://powerbi.microsoft.com/desktop).

	-	Dans le dossier **DemandForecastingDataGeneratorv1.0** que vous avez téléchargé, double-cliquez sur le fichier **Power BI Template\\DemandForecastPowerBI.pbix**. Les visualisations initiales sont basées sur des données fictives. **Remarque :** si un message d’erreur s’affiche, vérifiez que vous avez installé la dernière version de Power BI Desktop.

		Après avoir ouvert le fichier, cliquez sur **Modifier les requêtes** dans la partie supérieure. Dans la fenêtre contextuelle, double-cliquez sur **Source** dans le volet droit. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

	-   Dans la fenêtre contextuelle, remplacez **Serveur** et **Base de données** par vos propres noms de serveur et de base de données, puis cliquez sur **OK**. Pour le nom du serveur, veillez à spécifier le port 1433 (**NomVotreSolution.database.windows.net, 1433**). Ignorez les messages d’avertissement qui s’affichent à l’écran.

	-   Dans la fenêtre contextuelle suivante, deux options s’affichent dans le volet gauche (**Windows** et **Base de données**). Cliquez sur **Base de données** et renseignez vos **Nom d’utilisateur** et **Mot de passe** (il s’agit des nom d’utilisateur et mot de passe que vous avez entrés au moment du déploiement de la solution et de la création de la base de données SQL Azure). Dans ***Sélectionnez le niveau auquel appliquer ces paramètres***, cochez l’option du niveau de base de données. Cliquez ensuite sur **Se connecter**.

	-   Une fois redirigé sur la page précédente, fermez la fenêtre. Un message s’affiche ; cliquez sur **Appliquer**. Pour finir, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. Votre fichier Power BI a maintenant établi la connexion au serveur. Si vos visualisations sont vides, veillez à désactiver les sélections de visualisation pour visualiser toutes les données en cliquant sur l’icône de suppression en haut à droite des légendes. Utilisez le bouton Actualiser pour afficher les nouvelles données sur les visualisations. Au départ, vous verrez uniquement les données d’amorçage sur vos visualisations, étant donné que la fabrique de données est planifiée pour s’actualiser toutes les 3 heures. Au bout de 3 heures, les nouvelles prédictions sont reflétées dans vos visualisations lorsque vous actualisez les données.

3. (Facultatif) Publier le tableau de bord de chemin à froid dans [Power BI en ligne](http://www.powerbi.com/). Notez que vous avez besoin d’un compte Power BI (ou d’un compte Office 365) pour cette étape.

	-   Cliquez sur **Publier** et attendez quelques secondes ; une fenêtre s’affiche avec une coche verte pour confirmer que la publication dans Power BI a bien été effectuée. Cliquez sur le lien sous Ouvrir demoprediction.pbix dans Power BI. Pour obtenir des instructions détaillées, consultez la page [Publier à partir de Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

	-   Pour créer un tableau de bord : cliquez sur le signe **+** en regard de la section **Tableaux de bord** dans le volet gauche. Nommez ce nouveau tableau de bord « Prévision de la demande - Démonstration ».

	-   Après avoir ouvert le rapport, cliquez sur ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) pour épingler toutes les visualisations à votre tableau de bord. Pour obtenir des instructions détaillées, consultez la page [Épingler une vignette à un tableau de bord Power BI à partir d’un rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Accédez à la page Tableau de bord et ajustez la taille et l’emplacement de vos visualisations, puis modifier leurs titres. Pour obtenir des instructions détaillées sur la modification de vos vignettes, consultez [Modifier une vignette -- redimensionner, déplacer, renommer, épingler, supprimer, ajouter un lien hypertexte](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Voici un exemple de tableau de bord sur lequel sont épinglées des visualisations de chemin à froid.

		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Facultatif) Planifiez l'actualisation de la source de données.
	-	  Pour planifier l’actualisation des données, pointez sur le jeu de données **EnergyBPI-Final**, cliquez sur ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png), puis sélectionnez **Planifier l’actualisation**. **Remarque :** si vous voyez un message d’avertissement, cliquez sur **Modifier les informations d’identification** et assurez-vous que vos informations d’identification de base de données sont les mêmes que celles décrites à l’étape 1.

	![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

	-   Développez la section **Planifier l’actualisation**. Activez l’option Tenir vos données à jour.

	-   Planifiez l’actualisation selon vos besoins. Pour plus d’informations, consultez la page [Actualisation des données dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## **Comment supprimer votre solution**
Veillez à arrêter le générateur de données quand vous n’utilisez pas la solution activement, car l’exécution du générateur de données entraîne des coûts plus élevés. Supprimez la solution si vous ne l’utilisez pas. La suppression de votre solution supprime tous les composants qui étaient configurés dans votre abonnement quand vous avez déployé la solution. Pour supprimer la solution, cliquez sur son nom dans le volet gauche du modèle de solution, puis cliquez sur Supprimer.

## **Outils d’estimation des coûts**

Les deux outils suivants peuvent vous aider à mieux comprendre les coûts impliqués dans l’exécution du modèle de solution de prévision de la demande énergétique dans votre abonnement :

-   [Microsoft Azure Cost Estimator Tool (en ligne)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure Cost Estimator Tool (de bureau)](http://www.microsoft.com/download/details.aspx?id=43376)

## **Remerciements**
Cet article a été créé par le scientifique de données Yijing Chen et l’ingénieur logiciel Min Qiu de Microsoft.

<!---HONumber=AcomDC_0921_2016-->