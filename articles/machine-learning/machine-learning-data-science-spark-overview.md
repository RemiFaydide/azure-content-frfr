<properties
	pageTitle="Vue d’ensemble de la science des données à l’aide de Spark sur Azure HDInsight | Microsoft Azure"
	description="La boîte à outils MLlib de Spark offre de nombreuses fonctionnalités de modélisation Machine Learning (ML) à cet environnement distribué."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="deguhath;bradsev;gokuma" />

# Vue d’ensemble de la science des données à l’aide de Spark sur Azure HDInsight

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cet ensemble de rubriques montre comment utiliser HDInsight Spark pour effectuer des tâches de science des données courantes telles que l’ingestion de données, la conception de fonctionnalités, la modélisation et l’évaluation de modèle. Les données utilisées sont un échantillon du jeu de données NYC Taxi Trip and Fare 2013. Les modèles conçus incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree). Les rubriques montrent également comment stocker ces modèles dans le stockage d’objets blob Azure (WASB) et comment noter et évaluer leurs performances de prédiction. D’autres rubriques plus avancées décrivent comment former des modèles par validation croisée et balayage hyperparamétrique. Cette rubrique de présentation décrit également comment configurer le cluster Spark dont vous avez besoin pour effectuer les étapes des trois procédures pas à pas fournies.

[Spark](http://spark.apache.org/) est une infrastructure de traitement en parallèle open source qui prend en charge le traitement en mémoire pour accroître les performances des applications d’analyse de Big Data. Le moteur de traitement Spark est élaboré pour permettre des analyses rapides, simples d’utilisation et sophistiquées. De par ses capacités de calcul distribué en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans l'apprentissage automatique et les calculs de graphiques. [MLlib](http://spark.apache.org/mllib/) est la bibliothèque évolutive d’apprentissage automatique de Spark. Elle apporte des fonctionnalités de modélisation à cet environnement distribué.

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) est l’offre Azure de Spark Open Source. Il prend également en charge les **notebooks Jupyter PySpark** sur le cluster Spark, qui peuvent exécuter des requêtes interactives SQL Spark pour transformer, filtrer et visualiser les données stockées dans les objets blob Azure (WASB). PySpark est l’API Python pour Spark. Les extraits de code qui fournissent les solutions et montrent les tracés pertinents permettant de visualiser les données ici s’exécutent dans des notebooks Jupyter installés sur les clusters Spark. Les étapes de modélisation dans ces rubriques contiennent du code qui montre comment former, évaluer, enregistrer et consommer chaque type de modèle.

Les étapes de configuration et le code fournis dans cette procédure concernent HDInsight 3.4 Spark 1.6. Toutefois, le code présenté ici et dans les notebooks est générique et doit fonctionner sur n’importe quel cluster Spark. Les étapes de configuration et de gestion de cluster peuvent être légèrement différentes de celles indiquées ici, si vous n’utilisez pas HDInsight Spark.

## Composants requis

1\. Avant de commencer ces rubriques, vous devez avoir un abonnement Azure. Si vous n’en avez pas, consultez [Obtenir une version d’évaluation gratuite Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\. Vous avez besoin d’un cluster HDInsight 3.4 Spark 1.6 pour effectuer cette procédure pas à pas. Pour en créer un, consultez les instructions fournies dans [Prise en main : Créer Apache Spark sur Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Vous spécifiez le type et la version du cluster à partir du menu **Sélectionner le type de cluster**.


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [AZURE.NOTE] Pour une rubrique qui montre comment utiliser Scala plutôt que Python pour effectuer des tâches de processus de science des données de bout en bout, consultez [Science des données à l’aide de Scala avec Spark sur Azure](machine-learning-data-science-process-scala-walkthrough.md).

<!-- -->

>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Données de NYC 2013 Taxi

Pesant environ 20 Go au format compressé (ou 48 Go au format non compressé), le jeu de données NYC Taxi Trip contient des fichiers CSV (valeurs séparées par des virgules) concernant plus de 173 millions de trajets et le prix réglé pour chacun d’entre eux. Chaque enregistrement de course inclut le lieu et l’heure d’embarquement et de débarquement, le numéro de licence (du chauffeur) rendu anonyme et le numéro de médaillon (numéro d’identification unique) du taxi. Les données portent sur toutes les courses effectuées en 2013 et sont fournies dans les deux jeux de données ci-après pour chaque mois :

1. Les fichiers CSV trip\_data contiennent les détails de chaque course, comme le nombre de passagers, les points d’embarquement et de débarquement, la durée du trajet et la distance parcourue. Voici quelques exemples d’enregistrements :

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Les fichiers CSV trip\_fare contiennent des informations sur le prix payé pour chaque trajet, comme le type de paiement, le montant, la surcharge et les taxes, les pourboires et péages, ainsi que le montant total réglé. Voici quelques exemples d’enregistrements :

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


La clé unique permettant de joindre trip\_data et trip\_fare se compose des champs suivants : medallion (médaillon), hack\_licence (licence de taxi) et pickup\_datetime (date et heure d’embarquement).


Nous avons pris un échantillon représentant 0,1 % de ces fichiers et créé un jeu de données qui sera utilisé comme entrée dans cette procédure. La clé unique permettant de joindre trip\_data et trip\_fare se compose des champs suivants : medallion (médaillon), hack\_licence (licence de taxi) et pickup\_datetime (date et heure d’embarquement). Chaque enregistrement du jeu de données contient les attributs suivants qui représentent un trajet NYC Taxy :


|Champ| Brève description
|------|---------------------------------
| medallion |Médaillon de taxi anonymisé (id unique de taxi)
| hack\_license |	Numéro de licence Hackney Transport anonymisé
| vendor\_id |	ID du fournisseur de taxi
| rate\_code | Tarification du taxi NYC
| store\_and\_fwd\_flag | Indicateur de stockage et de transmission
| pickup\_datetime |	Date et heure du départ
| dropoff\_datetime | Date et heure d’arrivée
| pickup\_hour |	Heure de départ
| pickup\_week |	Semaine de l’année correspondant au départ
| weekday |	Jour de la semaine (de 1 à 7)
| passenger\_count |	Nombre de passagers dans un trajet en taxi
| trip\_time\_in\_secs | Durée du trajet en secondes
| trip\_distance | Distance du trajet en miles
| pickup\_longitude | Longitude de départ
| pickup\_latitude |	Latitude de départ
| dropoff\_longitude | Longitude d’arrivée
| dropoff\_latitude | Latitude d’arrivée
| direct\_distance |	Distance directe entre les emplacements de départ et d’arrivée
| payment\_type | Type de paiement (espèces, carte de crédit, etc.).
| fare\_amount | Montant du trajet
| surcharge | Supplément pour surcharge
| mta\_tax |	Taxe du MTA
| tip\_amount | Montant du pourboire
| tolls\_amount | Montant des péages
| total\_amount | Montant total
| tipped | Pourboire payé (0/1 pour non ou oui)
| tip\_class | Classe de pourboire (0 : 0 $, 1 : 0-5 $ 2 : 6-10 $, 3 : 11 à 20 $, 4: 20 $ et +)


## Exécuter le code à partir d’un notebook Jupyter sur le cluster Spark 

Vous pouvez lancer le notebook Jupyter à partir du portail Azure. Recherchez votre cluster Spark sur votre tableau de bord, puis cliquez dessus pour accéder à la page de gestion de votre cluster. Ensuite, cliquez sur **Tableaux de bord du cluster** -> **Bloc-notes Jupyter** pour ouvrir le notebook associé au cluster Spark.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Vous pouvez également naviguer jusqu’à ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** pour accéder aux notebooks Jupyter. Remplacez simplement la partie CLUSTERNAME de cette URL par le nom de votre propre cluster. Vous aurez besoin du mot de passe de votre compte d’administrateur pour accéder aux notebooks.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Sélectionnez PySpark pour afficher un répertoire qui contient quelques exemples de notebooks prédéfinis qui utilisent l’API PySpark. Les notebooks qui contiennent les exemples de code pour cet ensemble de rubriques Spark sont disponibles sur [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)


Vous pouvez télécharger les notebooks directement de Github sur le serveur de notebooks Jupyter de votre cluster Spark. Dans la page d’accueil de votre Jupyter, cliquez sur le bouton **Télécharger** dans la partie droite de l’écran. Un explorateur de fichiers s’ouvre. Vous pouvez coller l’URL GitHub (contenu brut) du notebook et cliquer sur **Ouvrir**. Les notebooks PySpark sont disponibles aux URL suivantes :

1.	[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.	[pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.	[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Le nom de fichier réapparaît dans votre liste de fichiers Jupyter avec un bouton **Télécharger**. Cliquez sur ce bouton **Télécharger**. Le notebook est maintenant importé. Répétez ces étapes pour télécharger les notebooks suivants à partir de cette procédure.

> [AZURE.TIP] Vous pouvez cliquer avec le bouton droit sur les liens ci-dessous dans votre navigateur et sélectionner **Copier le lien** pour obtenir l’URL du contenu brut GitHub que vous pouvez coller dans la boîte de dialogue de l’explorateur Télécharger le fichier de Jupyter.

Vous pouvez désormais :

- Cliquer sur le notebook pour visualiser le code.
- Exécutez chaque cellule en appuyant sur **MAJ-ENTRÉE**
- Exécutez le notebook en cliquant sur **Cellule** -> **Exécuter**
- Utiliser la visualisation automatique des requêtes

> [AZURE.TIP] Le noyau Pyspark visualise automatiquement la sortie des requêtes SQL (HiveQL). Vous pouvez sélectionner différents types de visualisations (tables, secteurs, lignes, zones ou barres) à l’aide des boutons de menu **Type** dans le notebook :

![Courbe ROC de régression logistique pour une approche générique](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## Et ensuite ?

Maintenant que vous avez configuré un cluster HDInsight Spark et téléchargé des notebooks Jupyter, vous êtes prêt à appliquer les procédures correspondant à ces trois notebooks PySpark, qui montrent comment explorer vos données, et créer et utiliser des modèles. Le notebook avancé d’exploration et de modélisation des données approfondit la validation croisée, le balayage hyperparamétrique et l’évaluation du modèle.

**Exploration et modélisation des données avec Spark :** explorez le jeu de données, créez les modèles Machine Learning à noter et à évaluer, en appliquant la procédure de la rubrique [Créer des modèles de classification binaire et de régression pour les données avec la boîte à outils MLlib de Spark](machine-learning-data-science-spark-data-exploration-modeling.md).

**Consommation de modèles :** pour apprendre à noter les modèles de classification et de régression créés dans cette rubrique, consultez [Noter et évaluer des modèles Machine Learning créés avec Spark](machine-learning-data-science-spark-model-consumption.md).

**Validation croisée et balayage hyperparamétrique** : consultez [Exploration et modélisation avancées des données avec Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) pour savoir comment effectuer la formation des modèles à l’aide de la validation croisée et du balayage hyperparamétrique

<!---HONumber=AcomDC_0921_2016-->