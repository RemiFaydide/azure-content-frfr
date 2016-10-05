<properties
	pageTitle="Exploration et modélisation de données avec Spark | Microsoft Azure"
	description="Présente les fonctionnalités de modélisation et d’exploration de données du kit d’outils Spark MLlib."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="deguhath;bradsev;gokuma" />

# Exploration et modélisation des données avec Spark

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette procédure utilise HDInsight Spark pour effectuer l’exploration des données ainsi que les tâches de classification binaire et de modélisation de la régression sur un échantillon du jeu de données NYC Taxi Trip and Fare 2013. Elle vous guide tout au long des étapes du [processus de science des données](http://aka.ms/datascienceprocess), à l’aide d’un cluster HDInsight Spark pour le traitement et d’objets blob Azure pour stocker les données et les modèles. Le processus explore et visualise les données importées à partir d’un objet blob Azure Storage, puis prépare les données pour créer des modèles prédictifs. Ces modèles sont créés à l’aide de la boîte à outils Spark MLlib pour effectuer des tâches de classification binaire et de modélisation de régression.

- La **classification binaire** consiste à prédire si le trajet va faire l’objet d’un pourboire.
- La tâche de **régression** consiste à prédire le montant du pourboire en fonction d’autres critères.

Les modèles que nous utilisons incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree) :

- La [régression linéaire avec SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) est un modèle de régression linéaire qui utilise la méthode SGD (Stochastic Gradient Descent), l’optimisation et la mise à l’échelle des caractéristiques pour prédire le montant des pourboires payés.
- La [régression logistique avec LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS), ou régression « logit », est un modèle de régression qui s’utilise quand la variable dépendante est catégorielle, pour la classification des données. LBFGS est un algorithme d’optimisation de Quasi-Newton qui correspond approximativement à l’algorithme BFGS (Broyden–Fletcher–Goldfarb–Shanno) avec une quantité limitée de mémoire informatique et qui est largement utilisé dans l’apprentissage automatique (Machine Learning).
- Les [forêts aléatoires](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision. Elles combinent plusieurs arbres de décision pour réduire le risque de sur-ajustement. Utilisées pour la régression et la classification, les forêts aléatoires gèrent les caractéristiques catégorielles, prennent en compte le paramètre de classification multiclasse, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Les forêts aléatoires constituent l’un des modèles Machine Learning les plus performants pour la classification et la régression.
- Les arbres GBT ([Gradient Boosting Tree](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)) sont des ensembles d’arbres de décision. Ils aident les arbres de décision à minimiser itérativement une fonction de perte. Utilisés pour la régression et la classification, les arbres GBT gèrent les caractéristiques catégorielles, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Ils s’utilisent également dans le paramétrage de classification multiclasse.

Les étapes de modélisation contiennent également du code déterminant comment former, évaluer et enregistrer chaque type de modèle. Python a été utilisé pour coder la solution et montrer les tracés correspondants.


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
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### Importer les bibliothèques

Le programme d’installation nécessite également l’importation des bibliothèques nécessaires. Définissez le contexte Spark et importez les bibliothèques nécessaires à l’aide du code suivant.


	# IMPORT LIBRARIES
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
 

## Ingestion de données à partir d’un objet blob public

Cette section contient le code d’une série de tâches nécessaires à l’ingestion de l’échantillon de données à modéliser. Lisez un échantillon de 0,1 % du fichier de trajets et de tarifs de taxi (stocké dans un fichier TSV), formatez et nettoyez les données, créez et mettez en cache une trame de données en mémoire, puis enregistrez-le en tant que table temporaire dans le contexte SQL.

La première étape du processus de science des données consiste à recevoir les données à partir de sources ou de systèmes externes, et à les analyser dans votre environnement de modélisation et d’exploration de données. Dans cette procédure, cet environnement est Spark. Cette section contient le code permettant d’effectuer une série de tâches :

- recevoir l’échantillon de données à modéliser
- lire le jeu de données en entrée (stocké dans un fichier TSV)
- formater et nettoyer les données
- créer et mettre en cache des objets (RDD ou trames de données) en mémoire
- enregistrer les données en tant que table temporaire dans le contexte SQL.

Voici le code pour l’ingestion de données.

	# INGEST DATA

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

	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 51,72 seconde


## Exploration et visualisation de données

Une fois les données intégrées dans Spark, l’étape suivante du processus de science des données consiste à mieux comprendre les données par l’exploration et la visualisation. Dans cette section, nous examinons les données des taxis à l’aide de requêtes SQL et traçons les variables cibles et les caractéristiques prospectives à vérifier visuellement. Plus précisément, nous traçons la fréquence des nombres de passagers dans les trajets en taxi, la fréquence des montants des pourboires et la variation des pourboires par type et par montant.

### Tracer un histogramme des fréquences de nombres de passagers dans l’échantillon des courses de taxi

Ce code et les extraits de code suivants utilisent une commande magique SQL pour interroger l’exemple et une commande magique locale pour tracer les données.

- **Commande magique SQL (`%%sql`)** Le noyau HDInsight PySpark prend en charge les requêtes HiveQL inline faciles exécutées sur sqlContext. L’argument (-o nom\_variable) conserve la sortie de la requête SQL en tant que tableau de données Pandas sur le serveur Jupyter. Cela signifie qu’elle sera disponible en mode local.
- La **commande magique `%%local`** est utilisée pour exécuter le code localement sur le serveur Jupyter, qui est le nœud principal du cluster HDInsight. En général, vous utilisez la commande magique `%%local` conjointement avec la commande magique `%%sql` avec le paramètre -o. Le paramètre -o conserve la sortie de la requête SQL localement, puis la commande magique %%local déclenche l’ensemble suivant d’extrait de code pour une exécution locale sur la sortie des requêtes SQL conservées localement.

La sortie est affichée automatiquement après l’exécution du code.

Cette requête récupère le nombre de trajets par passager.

	# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

	# HIVEQL QUERY AGAINST THE sqlContext
	%%sql -q -o sqlResults
	SELECT passenger_count, COUNT(*) as trip_counts 
	FROM taxi_train 
	WHERE passenger_count > 0 and passenger_count < 7 
	GROUP BY passenger_count 

Ce code crée un tableau de données local à partir de la sortie de la requête et il trace les données. La commande magique `%%local` crée un tableau de données local, `sqlResults`, qui peut être utilisé pour le tracé avec matplotlib.

>[AZURE.NOTE] Cette commande magique PySpark est utilisée plusieurs fois lors de cette procédure pas à pas. Si la quantité de données est élevée, vous devez échantillonner pour créer un tableau de données adapté à la mémoire locale.

	#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

Voici le code qui permet de tracer les nombres de trajets par passager.

	# PLOT PASSENGER NUMBER VS. TRIP COUNTS
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	x_labels = sqlResults['passenger_count'].values
	fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Counts of trips by passenger count')
	fig.set_xlabel('Passenger count in trips')
	fig.set_ylabel('Trip counts')
	plt.show()

**SORTIE :**

![Fréquence des voyages par nombre de passagers](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Vous pouvez sélectionner différents types de visualisations (tables, secteurs, lignes, zones ou barres) à l’aide des boutons de menu **Type** dans le notebook. Le graphique à barres est illustré ici.
	
### Tracez un histogramme du montant des pourboires et montrez comment le montant du pourboire varie selon le nombre de passagers et le montant des trajets.

Utilisez une requête SQL pour échantillonner les données.

	#PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE
	
	# HIVEQL QUERY AGAINST THE sqlContext
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

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 100, -2, 20])
	plt.show()


**SORTIE :**

![Distribution des montants de pourboire](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Montant de pourboire par nombre de passagers](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Montant du pourboire par montant de la course](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)


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

**SORTIE :**

126050

### Indexer et encoder les caractéristiques catégorielles à intégrer dans les fonctions de modélisation

Cette section montre comment indexer ou encoder les caractéristiques catégorielles à intégrer dans les fonctions de modélisation. Les fonctions de modélisation et de prédiction de MLlib requièrent des caractéristiques avec des données d’entrée catégorielles à indexer ou à encoder avant leur utilisation. Selon le modèle, vous devez les indexer ou les encoder différemment :

- La **modélisation arborescente** nécessite l’encodage des catégories en tant que valeurs numériques (par exemple, une caractéristique avec 3 catégories peut être encodée avec 0, 1, 2). C’est ce que permet la fonction [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) de MLlib. Cette fonction encode une colonne de libellés en une colonne d’index de libellé, classés par fréquence de libellé. Bien qu’indexés par des valeurs numériques pour le traitement des entrées et des données, les algorithmes arborescents peuvent être configurés pour les traiter comme des catégories.

- Les **modèles de régression logistique et linéaire** nécessitent un encodage linéaire où, par exemple, une fonction avec trois catégories peut être développée en 3 colonnes de caractéristiques, chacune contenant 0 ou 1 en fonction de la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) permettant d’effectuer un encodage linéaire. Cet encodeur mappe une colonne d’index de libellé à une colonne de vecteurs binaires, contenant au plus une seule une valeur. Cet encodage autorise les algorithmes qui appliquent des caractéristiques numériques, comme la régression logistique, à des caractéristiques catégorielles.

Voici le code permettant d’indexer et d’encoder des caractéristiques catégorielles :


	# INDEX AND ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES	
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
	
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

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 1,28 seconde

### Créer des objets point étiquetés à intégrer dans les fonctions ML

Cette section contient le code qui montre comment indexer des données textuelles catégorielles en type point étiqueté et les encoder afin qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les objets point étiquetés sont des jeux de données distribués résilients (RDD) mis en forme en tant que données d’entrée utilisables par la plupart des algorithmes ML dans MLlib. Un [point étiqueté](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est un vecteur local, dense ou fragmenté, associé à un libellé/une réponse.

Cette section contient le code qui montre comment indexer des données textuelles catégorielles en type [point étiqueté](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) et les encoder afin qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les objets point étiquetés sont des jeux de données distribués résilients (RDD) composés d’un libellé (variable cible/réponse) et du vecteur de caractéristique. Ce format est nécessaire en entrée par de nombreux algorithmes ML de MLlib.

Voici le code qui permet d’indexer et d’encoder des caractéristiques textuelles pour la classification binaire.

	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
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
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.sql.functions import rand

	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
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

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 0,24 seconde


### Mise à l’échelle des caractéristiques

La mise à l’échelle des caractéristiques, également appelée normalisation des données, garantit que les caractéristiques aux valeurs très dispersées sont pondérées dans la fonction cible. Le code de mise à l’échelle des caractéristiques utilise [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) pour mettre à l’échelle les caractéristiques à la variance d’unité. MLlib le fournit en vue d’une utilisation dans une régression linéaire avec SGD (Stochastic Gradient Descent), un algorithme populaire permettant de former une large gamme d’autres modèles Machine Learning, tels que les régressions régularisées ou les machines à vecteurs de support (SVM).

>[AZURE.NOTE] Nous avons découvert que l’algorithme LinearRegressionWithSGD est sensible à la mise à l’échelle des caractéristiques.

Voici le code pour mettre à l’échelle des variables pour l’algorithme pour une utilisation avec l’algorithme SGD linéaire régularisé.

	# FEATURE SCALING

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

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 13,17 secondes


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

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 0,15 seconde


## Prédire si un pourboire a été payé avec des modèles de classification binaires

Cette section montre comment utiliser trois modèles de tâche de classification binaire pour prédire si un pourboire est payé pour une course en taxi. Les modèles présentés sont les suivants :

- Régression logistique régularisée
- Modèle de forêts aléatoires
- Arbres GBT (Gradient Boosting Tree)

Chaque section de code générateur de modèle est divisée en étapes :

1. Données de **formation du modèle** avec un jeu de paramètres
2. **Évaluation de modèle** sur un jeu de données de test avec mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure

### Classification par régression logistique

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.

**Former le modèle de régression logistique à l’aide de la validation croisée et du balayage hyperparamétrique**

	# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

	# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	
	# CREATE MODEL WITH ONE SET OF PARAMETERS
	logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
	                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
	                                               tolerance=0.0001, validateData=True, numClasses=2)
	
	# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
	# NOTE: There are 20 coefficient terms for the 10 features, 
	#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
	print("Coefficients: " + str(logitModel.weights))
	print("Intercept: " + str(logitModel.intercept))
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE :**

Coefficients : [0,0082065285375, -0,0223675576104, -0,0183812028036, -3.48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0,111823113101, -0,324609912762, -0,204549780032, -1,36499216354, 0,591088507921, -0,664263411392, -1,00439726852, 3,46567827545, -3,51025855172, -0,0471341112232, -0,043521833294, 0,000243375810385, 0,054518719222]

Interception : -0,0111216486893

Durée d’exécution de la cellule ci-dessus : 14,43 secondes

**Évaluer le modèle de classification binaire avec des mesures standard**

	#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# PREDICT ON TEST DATA WITH MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
	
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


	## SAVE MODEL WITH DATE-STAMP
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	logitModel.save(sc, dirfilename);
	
	# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
	logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
	predictionAndLabelsDF = predictionAndLabels.toDF()
	predictionAndLabelsDF.registerTempTable("tmp_results");
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Zone sous PR = 0,985297691373

Zone sous ROC = 0,983714670256

Résumé des statistiques

Précision = 0,984304060189

Rappel = 0,984304060189

Score F1 = 0,984304060189

Durée d’exécution de la cellule ci-dessus : 57,61 secondes

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

	# MAKE PREDICTIONS
	predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
	prob = predictions_pddf["probability"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)

	# PLOT ROC CURVE
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
	

**SORTIE :**

![Logistic regression ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)


### Classification par forêts aléatoires

Le code de cette section montre comment former, évaluer et enregistrer un modèle de forêts aléatoires qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.
	
	#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

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
	## UN-COMMENT IF YOU WANT TO PRINT TREES
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

**SORTIE :**

Zone sous ROC = 0,985297691373

Durée d’exécution de la cellule ci-dessus : 31,09 secondes


### Classification par arbres GBT (Gradient Boosting Tree)

Le code de cette section montre comment former, évaluer et enregistrer un modèle d’arbres GBT qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.

	#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
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


**SORTIE :**

Zone sous ROC = 0,985297691373

Durée d’exécution de la cellule ci-dessus : 19,76 secondes


## Prédire le montant des pourboires de courses de taxi avec les modèles de régression

Cette section montre comment utiliser trois modèles pour la tâche de régression qui consiste à prédire le montant du pourboire versé pour une course de taxi en fonction d’autres caractéristiques de pourboire. Les modèles présentés sont les suivants :

- Régression linéaire régularisée
- Forêts aléatoires
- Arbres GBT (Gradient Boosting Tree)

Ces modèles sont décrits dans l’introduction. Chaque section de code générateur de modèle est divisée en étapes :

1. Données de **formation du modèle** avec un jeu de paramètres
2. **Évaluation de modèle** sur un jeu de données de test avec mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure

### Régression linéaire avec SGD 

Le code de cette section montre comment utiliser des caractéristiques mises à l’échelle pour former une régression linéaire utilisant utilise la descente de gradient stochastique (SGD) à des fins d’optimisation et comment noter, évaluer et enregistrer le modèle dans Azure Blob Storage (WASB).

>[AZURE.TIP] D’après notre expérience, il peut y avoir des problèmes avec la convergence des modèles LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisés avec soin pour obtenir un modèle valide. La mise à l’échelle des variables est très utile avec la convergence.


	#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

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
	
	# PRINT TEST METRICS
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = modelDir + linearregressionfilename;
	
	linearModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Coefficients : [0,00457675809917, -0,0226314167349, -0,0191910355236, 0,246793409578, 0,312047890459, 0,359634405999, 0,00928692253981, -0,000987181489428, -0,0888306617845, 0,0569376211553, 0,115519551711, 0,149250164995, -0,00990211159703, -0,00637410344522, 0,545083566179, -0,536756072402, 0,0105762393099, -0,0130117577055, 0,0129304737772, -0,00171065945959]

Interception : -0,853872718283

RMSE = 1,24190115863

Racine carrée = 0,608017146081

Durée d’exécution de la cellule ci-dessus : 58,42 secondes


### Régression par forêts aléatoires

Le code de cette section montre comment former, évaluer et enregistrer une régression de forêts aléatoires qui prédit le montant d’un pourboire pour les données sur les courses de taxi à New York.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	## TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	## PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

	# TEST METRICS
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

**SORTIE :**

RMSE = 0,891209218139

Racine carrée = 0,759661334921

Durée d’exécution de la cellule ci-dessus : 49,21 secondes


### Régression par arbres GBT (Gradient Boosting Tree)

Le code de cette section montre comment former, évaluer et enregistrer un modèle d’arbres GBT, qui prédit le montant d’un pourboire pour les données sur les courses de taxi à New York.

**Former et évaluer**

	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	## TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	## EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

	# TEST METRICS
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)

	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
	dirfilename = modelDir + btregressionfilename;
	gbtModel.save(sc, dirfilename)
	
	# CONVER RESULTS TO DF AND REGISER TEMP TABLE
	test_predictions = sqlContext.createDataFrame(predictionAndLabels)
	test_predictions.registerTempTable("tmp_results");
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

RMSE = 0,908473148639

Racine carrée = 0,753835096681

Durée d’exécution de la cellule ci-dessus : 34,52 secondes

**Tracer**

*tmp\_results* est enregistré en tant que table Hive dans la cellule précédente. Les résultats de la table sont fournis au tableau de données *sqlResults* pour le traçage. Voici le code.

	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

	# SELECT RESULTS
	%%sql -q -o sqlResults
	SELECT * from tmp_results

Voici le code pour tracer les données à l’aide du serveur Jupyter.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	import numpy as np

	# PLOT 
	ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
	plt.axis([-1, 20, -1, 20])
	plt.show(ax)
	

**SORTIE :**

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

	
## Nettoyage d’objets de la mémoire

Utilisez `unpersist()` pour supprimer les objets mis en cache en mémoire.
		
	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	
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


## Enregistrer les emplacements de stockage des modèles à des fins de consommation et de notation

Pour consommer et évaluer un jeu de données indépendant décrit dans la rubrique [Noter et évaluer des modèles Machine Learning créés avec Spark](machine-learning-data-science-spark-model-consumption.md), vous devez copier et coller ces noms de fichiers contenant les modèles enregistrés créés ici dans le notebook Jupyter de consommation. Voici le code permettant d’imprimer les chemins d’accès aux fichiers de modèle dont vous avez besoin ici.

	# MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


**SORTIE**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03\_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05\_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04\_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06\_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04\_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06\_51.737282"


## Et ensuite ?

Maintenant que vous avez créé des modèles de régression et de classification avec la bibliothèque MlLib Spark, vous êtes prêt à apprendre à noter et évaluer ces modèles. Le notebook avancé d’exploration et de modélisation des données approfondit la validation croisée, le balayage hyperparamétrique et l’évaluation du modèle.

**Consommation de modèles :** pour apprendre à noter et évaluer les modèles de classification et de régression créés dans cette rubrique, consultez [Noter et évaluer des modèles Machine Learning intégrés Spark](machine-learning-data-science-spark-model-consumption.md).

**Validation croisée et balayage hyperparamétrique** : consultez [Exploration et modélisation avancées des données avec Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) pour savoir comment effectuer la formation des modèles à l’aide de la validation croisée et du balayage hyperparamétrique

<!---HONumber=AcomDC_0921_2016-->