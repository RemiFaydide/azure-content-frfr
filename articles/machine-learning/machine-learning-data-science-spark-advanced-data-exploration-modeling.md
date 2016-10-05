<properties
	pageTitle="Exploration et modélisation avancées de données avec Spark | Microsoft Azure"
	description="Utilisez HDInsight Spark pour effectuer l’exploration des données et former des modèles de régression et de classification binaire à l’aide de la validation croisée et de l’optimisation hyperparamétrique."
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

# Modélisation et exploration avancées des données avec Spark 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette procédure utilise HDInsight Spark pour effectuer l’exploration des données et former des modèles de régression et de classification binaire à l’aide de la validation croisée et de l’optimisation hyperparamétrique sur un échantillon du jeu de données NYC Taxi Trip and Fare 2013. Elle vous guide tout au long des étapes du [processus de science des données](http://aka.ms/datascienceprocess), à l’aide d’un cluster HDInsight Spark pour le traitement et d’objets blob Azure pour stocker les données et les modèles. Le processus explore et visualise les données importées à partir d’un objet blob Azure Storage, puis prépare les données pour créer des modèles prédictifs. Python a été utilisé pour coder la solution et montrer les tracés correspondants. Ces modèles sont créés à l’aide de la boîte à outils Spark MLlib pour effectuer des tâches de classification binaire et de modélisation de régression.

- La **classification binaire** consiste à prédire si le trajet va faire l’objet d’un pourboire.
- La tâche de **régression** consiste à prédire le montant du pourboire en fonction d’autres critères.

Les étapes de modélisation contiennent également du code déterminant comment former, évaluer et enregistrer chaque type de modèle. Cette rubrique traite le même sujet que la rubrique [Exploration et modélisation des données avec Spark](machine-learning-data-science-spark-data-exploration-modeling.md), mais est dite plus « avancée », dans la mesure où elle utilise également la validation croisée conjointement avec le balayage hyperparamétrique, afin de former de manière optimale des modèles de classification et de régression.

La **validation croisée** est une technique qui évalue la généralisation d’un modèle formé sur un jeu connu de données pour la prédiction des caractéristiques d’un jeu de données sur lequel il n’a pas été formé. L’idée derrière cette technique est de former un modèle sur un jeu de données connues, puis d’évaluer la précision de ses prédictions sur un jeu de données indépendant. Une implémentation commune utilisée ici consiste à diviser un jeu de données en plis « K », puis de former le modèle par la méthode tourniquet (round robin) sur tous les plis sauf un.

L’**optimisation hyperparamétrique** consiste à choisir un jeu d’hyperparamètres pour un algorithme d’apprentissage, généralement dans le but d’optimiser la mesure des performances de l’algorithme sur un jeu de données indépendant. Les **hyperparamètres** sont des valeurs qui doivent être spécifiées en dehors de la procédure de formation de modèle. Les hypothèses concernant ces valeurs peuvent avoir un impact sur la flexibilité et la précision des modèles. Les arbres de décision ont des hyperparamètres, tels que la profondeur voulue et le nombre de feuilles de l’arbre. Les machines à vecteurs de support nécessitent la configuration d’un terme de pénalité en cas d’erreur de classification.

Une façon courante d’effectuer l’optimisation hyperparamétrique, utilisée ici, est la recherche par grille, ou **un balayage paramétrique**. Elle consiste en une recherche exhaustive d’un algorithme d’apprentissage sur les valeurs d’un sous-ensemble spécifié de l’espace hyperparamétrique. La validation croisée peut fournir une mesure de performance permettant de trier les résultats optimaux produits par l’algorithme de recherche par grille. La validation croisée, utilisée avec le balayage hyperparamétrique, limite les problèmes tels que le surajustement d’un modèle aux données de formation ; le modèle peut ainsi être appliqué au jeu de données général à partir duquel les données de formation ont été extraites.

Les modèles que nous utilisons incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree) :

- La [régression linéaire avec SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) est un modèle de régression linéaire qui utilise la méthode SGD (Stochastic Gradient Descent), l’optimisation et la mise à l’échelle des caractéristiques pour prédire le montant des pourboires payés.
- La [régression logistique avec LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS), ou régression « logit », est un modèle de régression qui s’utilise quand la variable dépendante est catégorielle, pour la classification des données. LBFGS est un algorithme d’optimisation de Quasi-Newton qui correspond approximativement à l’algorithme BFGS (Broyden–Fletcher–Goldfarb–Shanno) avec une quantité limitée de mémoire informatique et qui est largement utilisé dans l’apprentissage automatique (Machine Learning).
- Les [forêts aléatoires](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision. Elles combinent plusieurs arbres de décision pour réduire le risque de sur-ajustement. Utilisées pour la régression et la classification, les forêts aléatoires gèrent les caractéristiques catégorielles, prennent en compte le paramètre de classification multiclasse, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Les forêts aléatoires constituent l’un des modèles Machine Learning les plus performants pour la classification et la régression.
- Les arbres GBT ([Gradient Boosting Tree](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)) sont des ensembles d’arbres de décision. Ils aident les arbres de décision à minimiser itérativement une fonction de perte. Utilisés pour la régression et la classification, les arbres GBT gèrent les caractéristiques catégorielles, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Ils s’utilisent également dans le paramétrage de classification multiclasse.

Le problème de classification binaire comporte des exemples de modélisation à l’aide de la validation croisée et du balayage hyperparamétrique. Des exemples plus simples (sans le balayage paramétrique) sont présentés dans la rubrique principale pour les tâches de régression. L’annexe présente également des exemples de validation utilisant un filet élastique pour la régression linéaire, ainsi que de validation croisée utilisant le balayage paramétrique pour la régression par forêts aléatoires. Le **filet élastique** est une méthode de régression régularisée pour l’ajustement des modèles de régression, combinant de manière linéaire les métriques L1 et L2 en tant que pénalités des méthodes [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) et [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization).



>[AZURE.NOTE] Bien que la boîte à outils Spark MLlib soit conçue pour des jeux de données volumineux, nous utilisons par souci de commodité un échantillon relativement petit (environ 30 Mo sur 170 000 lignes, soit 0,1 % du jeu de données NYC d’origine) afin d’illustrer ses fonctionnalités de modélisation. Le cas de figure présenté ici fonctionne efficacement sur un cluster HDInsight à 2 nœuds de travail (en 10 minutes environ). Le même code permet de traiter des jeux de données plus volumineux, avec quelques modifications mineures concernant la mise en cache des données dans la mémoire ou l’adaptation de la taille du cluster.


## Composants requis

Vous avez besoin d’un compte Azure et d’un cluster Spark HDInsight. Vous avez besoin d’un cluster HDInsight 3.4 Spark 1.6 pour effectuer cette procédure pas à pas. Consultez [Vue d’ensemble de la science des données utilisant Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour en savoir plus sur ces exigences, pour obtenir une description des données NYC 2013 Taxi utilisées ici et pour savoir comment exécuter du code à partir d’un notebook Jupyter sur le cluster Spark. Le notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb** contenant les exemples de code de cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Configuration : emplacements de stockage, bibliothèques et contexte Spark prédéfini

Spark peut lire et écrire dans Azure Blob Storage (également appelé WASB). Donc, vos données stockées dedans sont exploitables par Spark et les résultats peuvent être stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers dans WASB, le chemin d’accès doit être correctement spécifié. Le conteneur par défaut associé au cluster Spark peut être référencé à l’aide d’un chemin commençant par wasb///. Les autres emplacements sont référencés par wasb://.

### Définir les chemins d’accès aux emplacements de stockage dans WASB

L’exemple de code suivant spécifie l’emplacement des données à lire et le chemin d’accès au répertoire de stockage dans lequel la sortie du modèle sera enregistrée.

	# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

	# LOCATION OF TRAINING DATA
	taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

	# PRINT START TIME
	import datetime
	datetime.datetime.now()

**SORTIE**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)


### Importer les bibliothèques

Importez les bibliothèques nécessaires avec le code suivant.

	# LOAD PYSPARK LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	import matplotlib
	import matplotlib.pyplot as plt
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	

### Contexte Spark prédéfini et commandes magiques PySpark

Les noyaux PySpark fournis avec les notebooks Jupyter ont un contexte prédéfini. Vous n’avez pas besoin de définir les contextes Spark ou Hive explicitement avant de commencer à utiliser l’application que vous développez ; ils sont disponibles par défaut. Ces contextes sont les suivants :

- sc : pour Spark
- sqlContext : pour Hive

Le noyau PySpark fournit certaines « commandes magiques » prédéfinies, qui sont des commandes spéciales que vous pouvez appeler avec %%. Deux de ces commandes sont utilisées dans ces exemples de code.

- **%%local** Indique que le code des lignes suivantes est exécuté localement. Le code doit être du code Python valide.
- **%%sql -o <nom de variable>** Exécute une requête Hive sur sqlContext. Si le paramètre -o est passé, le résultat de la requête est conservé dans le contexte Python %%local en tant que tableau de données Pandas.
 

Pour plus d’informations sur les noyaux pour notebooks Jupyter et sur les « commandes magiques » prédéfinies appelées avec %% (par exemple, %%local) qu’ils fournissent, consultez [Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## Ingestion de données à partir d’un objet blob public : 

Cette section contient le code d’une série de tâches nécessaires à l’ingestion de l’échantillon de données à modéliser. Lisez un échantillon de 0,1 % du fichier de trajets et de tarifs de taxi (stocké dans un fichier TSV), formatez et nettoyez les données, créez et mettez en cache une trame de données en mémoire, puis enregistrez-le en tant que table temporaire dans le contexte SQL.

La première étape du processus de science des données consiste à recevoir les données à partir de sources ou de systèmes externes, et à les analyser dans votre environnement de modélisation et d’exploration de données. Dans cette procédure, cet environnement est Spark. Cette section contient le code permettant d’effectuer une série de tâches :

- recevoir l’échantillon de données à modéliser
- lire le jeu de données en entrée (stocké dans un fichier TSV)
- formater et nettoyer les données
- créer et mettre en cache des objets (RDD ou trames de données) en mémoire
- enregistrer les données en tant que table temporaire dans le contexte SQL.

Voici le code pour l’ingestion de données.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_train_file = sc.textFile(taxi_train_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_train_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
	taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	
	    
	# CREATE DATA FRAME
	taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Durée d’exécution de la cellule ci-dessus : 276,62 secondes


## Exploration et visualisation de données 

Une fois les données intégrées dans Spark, l’étape suivante du processus de science des données consiste à mieux comprendre les données par l’exploration et la visualisation. Dans cette section, nous examinons les données des taxis à l’aide de requêtes SQL et traçons les variables cibles et les caractéristiques prospectives à vérifier visuellement. Plus précisément, nous traçons la fréquence des nombres de passagers dans les trajets en taxi, la fréquence des montants des pourboires et la variation des pourboires par type et par montant.

### Tracer un histogramme des fréquences de nombres de passagers dans l’échantillon des courses de taxi

Ce code et les extraits de code suivants utilisent une commande magique SQL pour interroger l’exemple et une commande magique locale pour tracer les données.

- **Commande magique SQL (`%%sql`)** Le noyau HDInsight PySpark prend en charge les requêtes HiveQL inline faciles exécutées sur sqlContext. L’argument (-o nom\_variable) conserve la sortie de la requête SQL en tant que tableau de données Pandas sur le serveur Jupyter. Cela signifie qu’elle sera disponible en mode local.
- La **commande magique `%%local`** est utilisée pour exécuter le code localement sur le serveur Jupyter, qui est le nœud principal du cluster HDInsight. En général, vous utilisez la commande magique `%%local` conjointement avec la commande magique `%%sql` avec le paramètre -o. Le paramètre -o conserve la sortie de la requête SQL localement, puis la commande magique %%local déclenche l’ensemble suivant d’extrait de code pour une exécution locale sur la sortie des requêtes SQL conservées localement.

La sortie est affichée automatiquement après l’exécution du code.

Cette requête récupère le nombre de trajets par passager.

	# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ce code crée un tableau de données local à partir de la sortie de la requête et il trace les données. La commande magique `%%local` crée un tableau de données local, `sqlResults`, qui peut être utilisé pour le tracé avec matplotlib.

>[AZURE.NOTE] Cette commande magique PySpark est utilisée plusieurs fois lors de cette procédure pas à pas. Si la quantité de données est élevée, vous devez échantillonner pour créer un tableau de données adapté à la mémoire locale.


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

Voici le code qui permet de tracer les nombres de trajets par passager.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# PLOT PASSENGER NUMBER VS TRIP COUNTS
	x_labels = sqlResults['passenger_count'].values
	fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Counts of trips by passenger count')
	fig.set_xlabel('Passenger count in trips')
	fig.set_ylabel('Trip counts')
	plt.show()

**SORTIE**

![Fréquence des voyages par nombre de passagers](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Vous pouvez sélectionner différents types de visualisations (tables, secteurs, lignes, zones ou barres) à l’aide des boutons de menu **Type** dans le notebook. Le graphique à barres est illustré ici.


### Tracez un histogramme du montant des pourboires et montrez comment le montant du pourboire varie selon le nombre de passagers et le montant des trajets.

Utilisez une requête SQL pour échantillonner les données.
	
	# SQL SQUERY
	%%sql -q -o sqlResults
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 
		AND passenger_count < 7
	    AND fare_amount > 0 
		AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 
		AND tip_amount < 25
	

Cette cellule de code utilise la requête SQL pour créer trois graphiques de données.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount ($) by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
	ax.set_title('Tip amount by Fare amount ($)')
	ax.set_xlabel('Fare Amount')
	ax.set_ylabel('Tip Amount')
	plt.axis([-2, 120, -2, 30])
	plt.show()
	

**SORTIE :**

![Distribution des montants de pourboire](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Montant de pourboire par nombre de passagers](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Montant du pourboire par montant de la course](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)


## Conception des caractéristiques, transformation et préparation des données à modéliser

Cette section décrit et fournit le code des procédures servant à préparer les données à utiliser dans la modélisation ML. Elle montre comment effectuer les opérations suivantes :

- Créer une caractéristique en regroupant les heures dans des périodes de trafic
- Indexer et encoder des fonctionnalités catégorielles
- Créer des objets point étiquetés à intégrer dans les fonctions ML
- Créer un sous-échantillonnage aléatoire des données et le diviser en un jeu de formation et un jeu de test
- Mise à l’échelle des caractéristiques
- Mettre en cache des objets en mémoire


### Créer une caractéristique en regroupant les heures dans des périodes de trafic

Ce code montre comment créer une nouvelle caractéristique en regroupant les heures dans des périodes de trafic et comment mettre en cache la trame de données obtenue en mémoire. Là où les jeux de données distribués résilients (RDD) et les trames de données sont utilisés de manière répétitive, la mise en cache réduit les temps d’exécution. Par conséquent, nous mettons en cache les RDD et les trames de données à plusieurs stades de la procédure.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
	# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()

**SORTIE**

126050


### Indexer et encoder « à chaud » des fonctionnalités catégorielles

Cette section montre comment indexer ou encoder les caractéristiques catégorielles à intégrer dans les fonctions de modélisation. Les fonctions de modélisation et de prédiction de MLlib requièrent des caractéristiques avec des données d’entrée catégorielles à indexer ou à encoder avant leur utilisation.

Selon le modèle, vous devez les indexer ou les encoder différemment : Les modèles de régression logistique et linéaire requièrent un encodage linéaire où, par exemple, une fonction avec 3 catégories peut être développée en 3 colonnes de caractéristiques, chacune contenant 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) permettant d’effectuer un encodage linéaire. Cet encodeur mappe une colonne d’index de libellé à une colonne de vecteurs binaires, contenant au plus une seule une valeur. Cet encodage autorise les algorithmes qui appliquent des caractéristiques numériques, comme la régression logistique, à des caractéristiques catégorielles.

Voici le code permettant d’indexer et d’encoder des caractéristiques catégorielles :


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
	
	# INDEX AND ENCODE VENDOR_ID
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_train_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Durée d’exécution de la cellule ci-dessus : 3,14 secondes


### Créer des objets point étiquetés à intégrer dans les fonctions ML

Cette section contient le code qui montre comment indexer des données textuelles catégorielles en type point étiqueté et les encoder afin qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les objets point étiquetés sont des jeux de données distribués résilients (RDD) mis en forme en tant que données d’entrée utilisables par la plupart des algorithmes ML dans MLlib. Un [point étiqueté](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est un vecteur local, dense ou fragmenté, associé à un libellé/une réponse.

Voici le code qui permet d’indexer et d’encoder des caractéristiques textuelles pour la classification binaire.

	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
	                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt


Voici le code qui permet d’encoder des caractéristiques textuelles par catégorie d’index pour l’analyse de régression linéaire.

	# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt


### Créer un sous-échantillonnage aléatoire des données et le diviser en un jeu de formation et un jeu de test

Ce code crée un échantillonnage aléatoire des données (25 % utilisé ici). Bien que ce ne soit pas nécessaire dans cet exemple en raison de la taille du jeu de données, nous vous montrons comment créer un échantillon ici afin de savoir comment l’utiliser pour votre problème en cas de nécessité. Lorsque les échantillons sont volumineux, cela permet de gagner beaucoup de temps pendant l’apprentissage des modèles. Ensuite, nous divisons l’échantillon en une partie d’apprentissage (75 % ici) et une partie de test (25 % ici) à utiliser dans la modélisation de la classification et de la régression.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	from pyspark.sql.functions import rand
	
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
	# CACHE TRAIN AND TEST DATA
	trainData.cache()
	testData.cache()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
	indexedTESTbinary = testData.map(parseRowIndexingBinary)
	oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
	oneHotTESTbinary = testData.map(parseRowOneHotBinary)
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg = trainData.map(parseRowIndexingRegression)
	indexedTESTreg = testData.map(parseRowIndexingRegression)
	oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
	oneHotTESTreg = testData.map(parseRowOneHotRegression)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

Durée d’exécution de la cellule ci-dessus : 0,31 seconde


### Mise à l’échelle des caractéristiques

La mise à l’échelle des caractéristiques, également appelée normalisation des données, garantit que les caractéristiques aux valeurs très dispersées sont pondérées dans la fonction cible. Le code de mise à l’échelle des caractéristiques utilise [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) pour mettre à l’échelle les caractéristiques à la variance d’unité. MLlib le fournit en vue d’une utilisation dans une régression linéaire avec SGD (Stochastic Gradient Descent), un algorithme populaire permettant de former une large gamme d’autres modèles Machine Learning, tels que les régressions régularisées ou les machines à vecteurs de support (SVM).

>[AZURE.TIP] Nous avons découvert que l’algorithme LinearRegressionWithSGD est sensible à la mise à l’échelle des caractéristiques.

Voici le code pour mettre à l’échelle des variables pour l’algorithme pour une utilisation avec l’algorithme SGD linéaire régularisé.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	
	# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
	label = oneHotTRAINreg.map(lambda x: x.label)
	features = oneHotTRAINreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	label = oneHotTESTreg.map(lambda x: x.label)
	features = oneHotTESTreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

Durée d’exécution de la cellule ci-dessus : 11,67 secondes


### Mettre en cache des objets en mémoire

La durée d’apprentissage et de test des algorithmes ML peut être réduite par la mise en cache d’objets de trame de données utilisés pour la classification, la régression et les caractéristiques mises à l’échelle.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.cache()
	indexedTESTbinary.cache()
	oneHotTRAINbinary.cache()
	oneHotTESTbinary.cache()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.cache()
	indexedTESTreg.cache()
	oneHotTRAINreg.cache()
	oneHotTESTreg.cache()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.cache()
	oneHotTESTregScaled.cache()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

Durée d’exécution de la cellule ci-dessus : 0,13 seconde


## Prédire si un pourboire a été payé avec des modèles de classification binaires

Cette section montre comment utiliser trois modèles de tâche de classification binaire pour prédire si un pourboire est payé pour une course en taxi. Les modèles présentés sont les suivants :

- Régression logique
- Forêts aléatoires
- Arbres GBT (Gradient Boosting Tree)

Chaque section de code générateur de modèle est divisée en étapes :

1. Données de **formation du modèle** avec un jeu de paramètres
2. **Évaluation de modèle** sur un jeu de données de test avec mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure

Nous présentons la validation croisée avec le balayage paramétrique de deux manières :

1. À l’aide de code personnalisé **générique** pouvant être appliqué à n’importe quel algorithme de MLlib et à n’importe quel jeu de paramètres dans un algorithme.
1. À l’aide de la **fonction pipeline pySpark CrossValidator**. Notez que, bien que pratique, CrossValidator comporte certaines limitations par rapport à Spark 1.5.0 :

	- Les modèles de pipeline ne peuvent pas être enregistrés/conservés pour une consommation future.
	- Ne peut pas être utilisé pour chaque paramètre dans un modèle.
	- Ne peut pas être utilisé pour chaque algorithme MLlib.


### Validation croisée générique et balayage paramétrique utilisés avec l’algorithme de régression logistique pour la classification binaire

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York. Le modèle est formé à l’aide de la validation croisée et le balayage paramétrique, implémentés avec du code personnalisé pouvant être appliqué à un algorithme d’apprentissage quelconque dans MLlib.

>[AZURE.NOTE] L’exécution de ce code de validation croisée personnalisé peut prendre plusieurs minutes.

**Former le modèle de régression logistique à l’aide de la validation croisée et du balayage hyperparamétrique**

	# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

	# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	
	# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
	from sklearn.grid_search import ParameterGrid
	grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
	paramGrid = list(ParameterGrid(grid))
	numModels = len(paramGrid)
	
	# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
	nFolds = 3;
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	# BEGIN CV WITH PARAMETER SWEEP
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create LabeledPoints from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowOneHotBinary)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        regt = paramGrid[j]['regType']
	        regp = paramGrid[j]['regParam']
	        iters = paramGrid[j]['iterations']
	        tol = paramGrid[j]['tolerance']
	        # Train logistic regression model with hypermarameter set
	        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
	                                                  regParam=regp, tolerance = tol, intercept=True)
	        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
	        metric = validMetrics.areaUnderROC
	        metricSum[j] += metric
	
	avgAcc = metricSum / nFolds;
	bestParam = paramGrid[np.argmax(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	    
	# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
	logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
	                                              iterations=bestParam['iterations'], 
	                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
	                                              intercept=True)
	
	
	# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
	# NOTE: There are 20 coefficient terms for the 10 features, 
	#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
	print("Coefficients: " + str(logitBest.weights))
	print("Intercept: " + str(logitBest.intercept))
	
	# PRINT ELAPSED TIME	
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Coefficients : [0,0082065285375, -0,0223675576104, -0,0183812028036, -3.48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0,111823113101, -0,324609912762, -0,204549780032, -1,36499216354, 0,591088507921, -0,664263411392, -1,00439726852, 3,46567827545, -3,51025855172, -0,0471341112232, -0,043521833294, 0,000243375810385, 0,054518719222]

Interception : -0,0111216486893

Durée d’exécution de la cellule ci-dessus : 14,43 secondes


**Évaluer le modèle de classification binaire avec des mesures standard**

Le code de cette section montre comment évaluer un modèle de régression logistique par rapport à un jeu de données de test, y compris un tracé de la courbe ROC.


	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT LIBRARIES
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	
	# INSTANTIATE METRICS OBJECT
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# AREA UNDER PRECISION-RECALL CURVE
	print("Area under PR = %s" % metrics.areaUnderPR)

	# AREA UNDER ROC CURVE
	print("Area under ROC = %s" % metrics.areaUnderROC)
	metrics = MulticlassMetrics(predictionAndLabels)

	# OVERALL STATISTICS
	precision = metrics.precision()
	recall = metrics.recall()
	f1Score = metrics.fMeasure()
	print("Summary Stats")
	print("Precision = %s" % precision)
	print("Recall = %s" % recall)
	print("F1 Score = %s" % f1Score)
	
	# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
	logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
	predictionAndLabelsDF = predictionAndLabels.toDF()
	predictionAndLabelsDF.registerTempTable("tmp_results");

	# PRINT ELAPSED TIME	
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Zone sous PR = 0,985336538462

Zone sous ROC = 0,983383274312

Résumé des statistiques

Précision = 0,984174341679

Rappel = 0,984174341679

Score F1 = 0,984174341679

Durée d’exécution de la cellule ci-dessus : 2,67 secondes


**Tracer la courbe ROC.**

*predictionAndLabelsDF* est enregistré en tant que tableau, *tmp\_results*, dans la cellule précédente. *tmp\_results* peut servir à effectuer des requêtes et à afficher les résultats dans le tableau de données sqlResults pour le traçage. Voici le code.


	# QUERY RESULTS                              
	%%sql -q -o sqlResults
	SELECT * from tmp_results


Voici le code permettant d’effectuer des prédictions et de tracer la courbe ROC.

	# MAKE PREDICTIONS AND PLOT ROC-CURVE

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	#PREDICTIONS
	predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
	prob = predictions_pddf["probability"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVES
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()
	

**SORTIE**

![Courbe ROC de régression logistique pour une approche générique](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)


**Conservation du modèle dans un objet blob en vue d’une consommation ultérieure**

Le code de cette section montre comment enregistrer le modèle de régression logistique en vue d’une consommation.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	# PERSIST MODEL
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	
	logitBest.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**SORTIE**

Durée d’exécution de la cellule ci-dessus : 34,57 secondes


### Utiliser la fonction pipeline CrossValidator de MLlib avec le modèle LogisticRegression (régression élastique)

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York. Le modèle est formé à l’aide de la validation croisée et du balayage hyperparamétrique, implémentés par la fonction pipeline MLlib CrossValidator pour la validation croisée avec le balayage paramétrique.


>[AZURE.NOTE] L’exécution de ce code de validation croisée MLlib peut prendre plusieurs minutes.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.classification import LogisticRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import BinaryClassificationEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	from sklearn.metrics import roc_curve,auc
	
	# DEFINE ALGORITHM / MODEL
	lr = LogisticRegression()
	
	# DEFINE GRID PARAMETERS
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build()
	
	# DEFINE CV WITH PARAMETER SWEEP
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=BinaryClassificationEvaluator(),
	                    numFolds=3)
	
	# CONVERT TO DATA-FRAME: THIS WILL NOT RUN ON RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
	
	# TRAIN WITH CROSS-VALIDATION
	cv_model = cv.fit(trainDataFrame)
	

	## PREDICT AND EVALUATE ON TEST DATA-SET

	# USE TEST DATASET FOR PREDICTION
	testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
	test_predictions = cv_model.transform(testDataFrame)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE**

Durée d’exécution de la cellule ci-dessus : 107,98 secondes


**Tracer la courbe ROC.**

*predictionAndLabelsDF* est enregistré en tant que tableau, *tmp\_results*, dans la cellule précédente. *tmp\_results* peut servir à effectuer des requêtes et à afficher les résultats dans le tableau de données sqlResults pour le traçage. Voici le code.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT label, prediction, probability from tmp_results

Voici le code permettant de tracer la courbe ROC.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
	%%local
	from sklearn.metrics import roc_curve,auc
	
	# ROC CURVE
	prob = [x["values"][1] for x in sqlResults["probability"]]
	fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()


**SORTIE**

![Courbe ROC de régression logistique utilisant la fonction CrossValidator de MLlib](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)



### Classification par forêts aléatoires

Le code de cette section montre comment former, évaluer et enregistrer une régression de forêts aléatoires qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# TRAIN RANDOMFOREST MODEL
	rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
	                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                       numTrees=25, featureSubsetStrategy="auto",
	                                       impurity='gini', maxDepth=5, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp;
	dirfilename = modelDir + rfclassificationfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Zone sous ROC = 0,985336538462

Durée d’exécution de la cellule ci-dessus : 26,72 secondes


### Classification par arbres GBT (Gradient Boosting Tree)

Le code de cette section montre comment former, évaluer et enregistrer un modèle d’arbres GBT qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                numIterations=10)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# Area under ROC curve
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN A BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
	dirfilename = modelDir + btclassificationfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

Zone sous ROC = 0,985336538462

Durée d’exécution de la cellule ci-dessus : 28,13 secondes


## Prédire le montant des pourboires avec les modèles de régression (sans la validation croisée)

Cette section montre comment utiliser trois modèles pour la tâche de régression qui consiste à prédire le montant du pourboire versé pour une course de taxi en fonction d’autres caractéristiques de pourboire. Les modèles présentés sont les suivants :

- Régression linéaire régularisée
- Forêts aléatoires
- Arbres GBT (Gradient Boosting Tree)

Ces modèles sont décrits dans l’introduction. Chaque section de code générateur de modèle est divisée en étapes :

1. Données de **formation du modèle** avec un jeu de paramètres
2. **Évaluation de modèle** sur un jeu de données de test avec mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure


>REMARQUE AZURE : La validation croisée n’est pas utilisée avec les trois modèles de régression dans cette section, car cela a été décrit en détail pour les modèles de régression logistique. L’annexe de cette rubrique présente un exemple d’utilisation de la validation croisée avec un filet élastique pour la régression linéaire.


>REMARQUE AZURE : Selon notre expérience, il peut y avoir des problèmes avec la convergence des modèles LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisés avec soin pour obtenir un modèle valide. La mise à l’échelle des variables est très utile avec la convergence. Vous pouvez aussi utiliser la régression élastique nette, présentée dans l’annexe de cette rubrique, au lieu de LinearRegressionWithSGD.


### Régression linéaire avec SGD

Le code de cette section montre comment utiliser des caractéristiques mises à l’échelle pour former une régression linéaire utilisant utilise la descente de gradient stochastique (SGD) à des fins d’optimisation et comment noter, évaluer et enregistrer le modèle dans Azure Blob Storage (WASB).

>[AZURE.TIP] D’après notre expérience, il peut y avoir des problèmes avec la convergence des modèles LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisés avec soin pour obtenir un modèle valide. La mise à l’échelle des variables est très utile avec la convergence.


	# LINEAR REGRESSION WITH SGD 

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD LIBRARIES
	from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
	from pyspark.mllib.evaluation import RegressionMetrics
	from scipy import stats
	
	# USE SCALED FEATURES TO TRAIN MODEL
	linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

	# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
	# NOTE: There are 20 coefficient terms for the 10 features, 
	#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
	print("Coefficients: " + str(linearModel.weights))
	print("Intercept: " + str(linearModel.intercept))
	
	# SCORE ON SCALED TEST DATA-SET & EVALUATE
	predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
	testMetrics = RegressionMetrics(predictionAndLabels)
	
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = modelDir + linearregressionfilename;
	
	linearModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

Coefficients : [0,0141707753435, -0,0252930927087, -0,0231442517137, 0,247070902996, 0,312544147152, 0,360296120645, 0,0122079566092, -0,00456498588241, -0,0898228505177, 0,0714046248793, 0,102171263868, 0,100022455632, -0,00289545676449, -0,00791124681938, 0,54396316518, -0,536293513569, 0,0119076553369, -0,0173039244582, 0,0119632796147, 0,00146764882502]

Interception : -0,854507624459

RMSE = 1,23485131376

Racine carrée = 0,597963951127

Durée d’exécution de la cellule ci-dessus : 38,62 secondes


### Régression par forêts aléatoires

Le code de cette section montre comment former, évaluer et enregistrer un modèle de forêts aléatoires qui prédit le montant d’un pourboire pour les données sur les courses de taxi à New York.

>[AZURE.NOTE] La validation croisée avec le balayage paramétrique, utilisant un code personnalisé, est présentée dans l’annexe.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	# UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp;
	dirfilename = modelDir + rfregressionfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

RMSE = 0,931981967875

Racine carrée = 0,733445485802

Durée d’exécution de la cellule ci-dessus : 25,98 secondes


### Régression par arbres GBT (Gradient Boosting Tree)

Le code de cette section montre comment former, évaluer et enregistrer un modèle d’arbres GBT, qui prédit le montant d’un pourboire pour les données sur les courses de taxi à New York.

**Former et évaluer**

	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	# EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
	test_predictions= sqlContext.createDataFrame(predictionAndLabels)
	test_predictions_pddf = test_predictions.toPandas()
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
	dirfilename = modelDir + btregressionfilename;
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

RMSE = 0,928172197114

Racine carrée = 0,732680354389

Durée d’exécution de la cellule ci-dessus : 20,9 secondes


**Tracer**
	
*tmp\_results* est enregistré en tant que table Hive dans la cellule précédente. Les résultats de la table sont fournis au tableau de données *sqlResults* pour le traçage. Voici le code.

	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

	# SELECT RESULTS
	%%sql -q -o sqlResults
	SELECT * from tmp_results


Voici le code pour tracer les données à l’aide du serveur Jupyter.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import numpy as np
	
	# PLOT
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 15])
	plt.show(ax)

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## Annexe : Tâches de régression supplémentaires utilisant la validation croisée avec balayage paramétrique

Cette annexe contient un code présentant la validation croisée utilisant un filet élastique pour la régression linéaire. Elle montre également comment effectuer une validation croisée avec balayage paramétrique à l’aide d’un code personnalisé pour la régression par forêts aléatoires.


### Validation croisée à l’aide d’un filet élastique pour la régression linéaire

Le code dans cette section montre comment effectuer une validation croisée à l’aide d’un filet élastique pour la régression linéaire, et comment évaluer le modèle par rapport aux données de test.

	###  CV USING ELASTIC NET FOR LINEAR REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.regression import LinearRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import RegressionEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	
	# DEFINE ALGORITHM/MODEL
	lr = LinearRegression()
	
	# DEFINE GRID PARAMETERS
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build() 
	
	# DEFINE PIPELINE 
	# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
	pipeline = Pipeline(stages=[lr])
	
	# DEFINE CV WITH PARAMETER SWEEP
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=RegressionEvaluator(),
	                    numFolds=3)
	
	# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
	
	# TRAIN WITH CROSS-VALIDATION
	cv_model = cv.fit(trainDataFrame)
	

	# EVALUATE MODEL ON TEST SET
	testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
	
	# MAKE PREDICTIONS ON TEST DOCUMENTS
	# cvModel uses the best model found (lrModel).
	predictionAndLabels = cv_model.transform(testDataFrame)
	
	# CONVERT TO DF AND SAVE REGISER DF AS TABLE
	predictionAndLabels.registerTempTable("tmp_results");
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Durée d’exécution de la cellule ci-dessus : 161,21 secondes

**Évaluer avec la mesure R-SQR**

*tmp\_results* est enregistré comme table Hive dans la cellule précédente. Les résultats de la table sont fournis au tableau de données *sqlResults* pour le traçage. Voici le code.

	# SELECT RESULTS
	%%sql -q -o sqlResults
	SELECT label,prediction from tmp_results


Voici le code pour calculer R-sqr.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	from scipy import stats
	
	#R-SQR TEST METRIC
	corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
	r2 = (corstats[2]*corstats[2])
	print("R-sqr = %s" % r2)


**SORTIE**

Racine carrée = 0,619184907088


### Validation croisée avec balayage paramétrique à l’aide de code personnalisé pour la régression par forêts aléatoires

Le code dans cette section montre comment effectuer une validation croisée avec balayage paramétrique à l’aide d’un code personnalisé pour la régression par forêts aléatoires, et comment évaluer le modèle par rapport aux données de test.


	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	from sklearn.grid_search import ParameterGrid
	
	## CREATE PARAMETER GRID
	grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
	paramGrid = list(ParameterGrid(grid))
	
	## SPECIFY LEVELS OF CATEGORICAL VARIBLES
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
	nFolds = 3;
	numModels = len(paramGrid)
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowIndexingRegression)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        maxD = paramGrid[j]['maxDepth']
	        numT = paramGrid[j]['numTrees']
	        # Train logistic regression model with hypermarameter set
	        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=numT, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=maxD, maxBins=32)
	        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
	        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = RegressionMetrics(predictionAndLabels)
	        metric = validMetrics.rootMeanSquaredError
	        metricSum[j] += metric
	
	avgAcc = metricSum/nFolds;
	bestParam = paramGrid[np.argmin(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	        
	## TRAIN FINAL MODL WIHT BEST PARAMETERS
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

	# EVALUATE MODEL ON TEST DATA
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	#PRINT TEST METRICS
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

RMSE = 0,906972198262

Racine carrée = 0,740751197012

Durée d’exécution de la cellule ci-dessus : 69,17 secondes


### Nettoyer des objets de la mémoire et imprimer les emplacements des modèles

Utilisez `unpersist()` pour supprimer les objets mis en cache en mémoire.

	# UNPERSIST OBJECTS CACHED IN MEMORY

	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	trainData.unpersist()
	trainData.unpersist()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.unpersist()
	indexedTESTbinary.unpersist()
	oneHotTRAINbinary.unpersist()
	oneHotTESTbinary.unpersist()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.unpersist()
	indexedTESTreg.unpersist()
	oneHotTRAINreg.unpersist()
	oneHotTESTreg.unpersist()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.unpersist()
	oneHotTESTregScaled.unpersist()


**SORTIE**

PythonRDD[122] at RDD at PythonRDD.scala:43


**Imprimez le chemin des fichiers de modèle à utiliser dans le notebook de consommation.** Pour consommer et évaluer un jeu de données indépendant, vous devez copier et coller ces noms de fichiers dans le notebook de consommation.


	# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


**SORTIE**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47\_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51\_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50\_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51\_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50\_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52\_18.827237"

## Et ensuite ?

Maintenant que vous avez créé des modèles de régression et de classification avec la bibliothèque MlLib Spark, vous êtes prêt à apprendre à noter et évaluer ces modèles.

**Consommation de modèles :** pour apprendre à noter et évaluer les modèles de classification et de régression créés dans cette rubrique, consultez [Noter et évaluer des modèles Machine Learning intégrés Spark](machine-learning-data-science-spark-model-consumption.md).

<!---HONumber=AcomDC_0921_2016-->