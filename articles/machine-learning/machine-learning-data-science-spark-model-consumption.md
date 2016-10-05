<properties
	pageTitle="Noter les modèles Machine Learning créés avec Spark | Microsoft Azure"
	description="Comment noter les modèles d’apprentissage stockés Azure Blob Storage (WASB)."
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

# Noter les modèles Machine Learning créés avec Spark 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette rubrique décrit comment charger des modèles Machine Learning (ML) créés avec Spark MLlib et stockés dans Azure Blob Storage (WASB) et les noter avec des jeux de données également stockées dans WASB. Il montre comment prétraiter les données d’entrée, transformer les caractéristiques à l’aide des fonctions d’indexation et d’encodage du kit d’outils MLlib, et comment créer un objet de données point étiqueté, utilisable comme entrée de notation avec les modèles ML. Les modèles utilisés pour la notation sont les suivants : Régression linéaire, Régression logistique, Modèles de forêts aléatoires et Modèles GBT (Gradient Boosting Tree).


## Composants requis

1. Vous avez besoin d’un compte Azure et d’un cluster Spark HDInsight. Vous avez besoin d’un cluster HDInsight 3.4 Spark 1.6 pour effectuer cette procédure pas à pas. Consultez [Vue d’ensemble de la science des données utilisant Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour en savoir plus sur ces exigences, pour obtenir une description des données NYC 2013 Taxi utilisées ici et pour savoir comment exécuter du code à partir d’un notebook Jupyter sur le cluster Spark. Le notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb** contenant les exemples de code de cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. Vous devez également créer les modèles Machine Learning à noter ici en appliquant la procédure de la rubrique [Exploration et modélisation de données avec Spark](machine-learning-data-science-spark-data-exploration-modeling.md).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## Configuration : emplacements de stockage, bibliothèques et contexte Spark prédéfini

Spark peut lire et écrire dans un objet blob Azure Storage (également appelé WASB). Donc, vos données stockées dedans sont exploitables par Spark et les résultats peuvent être stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers dans WASB, le chemin d’accès doit être correctement spécifié. Le conteneur par défaut associé au cluster Spark peut être référencé à l’aide d’un chemin commençant par *"wasb//"*. L’exemple de code suivant spécifie l’emplacement des données à lire et le chemin d’accès au répertoire de stockage dans lequel la sortie du modèle sera enregistrée.


### Définir les chemins d’accès aux emplacements de stockage dans WASB

Les modèles sont enregistrés dans : wasb:///user/remoteuser/NYCTaxi/Models. Si ce chemin d’accès n’est pas défini correctement, les modèles ne seront pas chargés en vue de leur notation.

Les résultats notés sont enregistrés dans : wasb:///user/remoteuser/NYCTaxi/ScoredResults. Si le chemin d’accès au dossier est incorrect, les résultats ne seront pas enregistrés dans ce dossier.


>[AZURE.NOTE] Les chemins des fichiers peuvent être copiés et collés dans les espaces réservés à cet effet dans le code à partir de la sortie de la dernière cellule du notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb**.


Voici le code pour définir les chemins de répertoires :

	# LOCATION OF DATA TO BE SCORED (TEST DATA)
	taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
	
	# SET SCORDED RESULT DIRECTORY PATH
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
	
	# FILE LOCATIONS FOR THE MODELS TO BE SCORED
	logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
	linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
	randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
	randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
	BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
	BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

	# RECORD START TIME
	import datetime
	datetime.datetime.now()

**SORTIE :**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)


### Importer les bibliothèques

Définir le contexte Spark et importer les bibliothèques nécessaires avec le code suivant

	#IMPORT LIBRARIES
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


## Recevoir les données et créer une trame de données nettoyée

Cette section contient le code d’une série de tâches nécessaires à la réception de l’échantillon de données à modéliser. Lire un échantillon de 0,1 % du fichier contenant les trajets et prix de taxi (stocké dans un fichier TSV), formater les données et créer une trame de données propre.

Les fichiers de trajet et de prix de taxi ont été joints dans la procédure décrite dans la rubrique [Processus TDSP (Team Data Science Process) en action : Utilisation de clusters HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).

	# INGEST DATA AND CREATE A CLEANED DATA FRAME

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_test_file = sc.textFile(taxi_test_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	    
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_test_file.first()
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
	
	# CREATE DATA FRAME
	taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_cleaned.cache()
	taxi_df_test_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_test_cleaned.registerTempTable("taxi_test")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 46,37 secondes


## Préparer les données à la notation dans Spark 

Cette section montre comment indexer, encoder et mettre à l’échelle des caractéristiques catégorielles en vue de leur utilisation dans les algorithmes d’apprentissage contrôlé MLlib pour la classification et la régression.

### Transformation de caractéristiques : indexer et encoder des caractéristiques catégorielles en vue de leur utilisation dans des modèles à noter 

Cette section montre comment indexer les données catégorielles à l’aide d’un `StringIndexer` et encoder les caractéristiques avec entrée de `OneHotEncoder` dans les modèles.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) encode une colonne de libellés en une colonne d’index de libellés. Les index sont classés par fréquence de libellé.

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mappe une colonne d’index de libellés à une colonne de vecteurs binaires, contenant au plus une valeur 1. Cet encodage autorise les algorithmes qui appliquent des caractéristiques numériques continues, comme la régression logistique, à des caractéristiques catégorielles.
	
	#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
	
	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_test 
	"""
	taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_with_newFeatures.cache()
	taxi_df_test_with_newFeatures.count()
	
	# INDEX AND ONE-HOT ENCODING
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_test_with_newFeatures)
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
	
	# INDEX AND ENCODE TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 5,37 secondes


### Créer des objets RDD avec des tableaux de caractéristiques à intégrer dans des modèles

Cette section contient le code montrant comment indexer des données textuelles catégorielles en un objet RDD et les encoder linéairement pour qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les données indexées sont stockées dans des objets [RDD (Resilient Distributed Dataset)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html). Il s’agit de l’abstraction de base dans Spark. Un objet RDD représente une collection immuable et partitionnée d’éléments qui peuvent faire l’objet d’un traitement en parallèle avec Spark.

Il contient également du code montrant comment mettre à l’échelle des données avec le `StandardScalar` fourni par MLlib, en vue d’une utilisation dans la régression linéaire avec SGD (Stochastic Gradient Descent), un algorithme répandu permettant de former une large gamme de modèles Machine Learning. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) permet de mettre à l’échelle les caractéristiques en fonction de la variance d’unité. La mise à l’échelle des caractéristiques, également appelée normalisation des données, garantit que les caractéristiques aux valeurs très dispersées sont pondérées dans la fonction cible.


	# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# IMPORT LIBRARIES
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	from numpy import array
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features

	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
	oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
	
	# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
	indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
	oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
	
	# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
	scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
	oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
	
	# CACHE RDDS IN MEMORY
	indexedTESTbinary.cache();
	oneHotTESTbinary.cache();
	indexedTESTreg.cache();
	oneHotTESTreg.cache();
	oneHotTESTregScaled.cache();
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 11,72 secondes


## Noter avec le modèle de régression logistique et enregistrer la sortie dans l’objet BLOB

Le code de cette section montre comment charger un modèle de régression logistique enregistré dans Azure Blob Storage et l’utiliser pour prédire si un pourboire est payé lors d’un trajet en taxi, le noter avec des mesures de classification standard, l’enregistrer puis représenter graphiquement les résultats dans Blob Storage. Les résultats notés sont stockés dans des objets RDD.


	# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	## LOAD SAVED MODEL
	savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
	predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
	
	## SAVE SCORED RESULTS (RDD) TO BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
	dirfilename = scoredResultDir + logisticregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 19,22 secondes


## Noter un modèle de régression linéaire

Nous avons utilisé [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) pour former un modèle de régression linéaire utilisant SGD (Stochastic Gradient Descent) à des fins d’optimisation pour prédire le montant des pourboires payés.

Le code de cette section montre comment charger un modèle de régression linéaire à partir d’Azure Blob Storage, le noter avec des variables mises à l’échelle, puis réenregistrer les résultats dans l’objet blob.

	#SCORE LINEAR REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	#LOAD LIBRARIES​
	from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
	
	# LOAD MODEL AND SCORE USING **SCALED VARIABLES**
	savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
	predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = scoredResultDir + linearregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL​
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 16,63 secondes


## Noter les modèles Forêts aléatoires de classification et de régression

Le code de cette section montre comment charger les modèles Forêts aléatoires de classification et de régression enregistrés dans Azure Blob Storage, noter leurs performances avec des mesures standard de classificateur et de régression, puis réenregistrer les résultats dans Azure Blob Storage.

Les [forêts aléatoires](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision. Elles combinent plusieurs arbres de décision pour réduire le risque de sur-ajustement. Elles gèrent les caractéristiques catégorielles, prennent en compte le paramètre de classification multiclasse, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Les forêts aléatoires constituent l’un des modèles Machine Learning les plus performants pour la classification et la régression.

[spark.mllib](http://spark.apache.org/mllib/) prend en charge les forêts aléatoires pour la classification binaire et multiclasse et pour la régression, à l’aide des caractéristiques continues et catégorielles.

	# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES	
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfregressionfilename;
	predictions.saveAsTextFile(dirfilename)

	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 31,07 secondes


## Noter les modèles GBT de classification et de régression

Le code de cette section montre comment charger les modèles GBT de classification et de régression enregistrés dans Azure Blob Storage, noter leurs performances avec des mesures standard de classificateur et de régression, puis réenregistrer les résultats dans Azure Blob Storage.

**spark.mllib** prend en charge les arbres GBT pour la classification binaire et la régression, à l’aide des caractéristiques continues et catégorielles.

Les arbres GBT ([Gradient Boosted Tree](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)) sont des ensembles d’arbres de décision. Ils aident les arbres de décision à minimiser itérativement une fonction de perte. Ils gèrent les caractéristiques catégorielles, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Ils s’utilisent également dans le paramétrage de classification multiclasse.


	# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	#LOAD AND SCORE THE MODEL
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	# LOAD AND SCORE MODEL 
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btregressionfilename;
	predictions.saveAsTextFile(dirfilename)


	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
	
**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 14,6 secondes


## Nettoyer des objets de la mémoire et imprimer les emplacements de fichier notés

	# UNPERSIST OBJECTS CACHED IN MEMORY
	taxi_df_test_cleaned.unpersist()
	indexedTESTbinary.unpersist();
	oneHotTESTbinary.unpersist();
	indexedTESTreg.unpersist();
	oneHotTESTreg.unpersist();
	oneHotTESTregScaled.unpersist();


	# PRINT OUT PATH TO SCORED OUTPUT FILES
	print "logisticRegFileLoc: " + logisticregressionfilename;
	print "linearRegFileLoc: " + linearregressionfilename;
	print "randomForestClassificationFileLoc: " + rfclassificationfilename;
	print "randomForestRegFileLoc: " + rfregressionfilename;
	print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
	print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**SORTIE :**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22\_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22\_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23\_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23\_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23\_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23\_56.860740.txt



## Utiliser les modèles Spark via une interface web

Spark fournit un mécanisme permettant de soumettre à distance des travaux par lots ou des requêtes interactives via une interface REST dotée d’un composant appelé Livy. Par défaut, Livy est activé sur votre cluster HDInsight Spark. Pour plus d’informations, consultez [Envoi de travaux Spark à distance en utilisant Livy](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md).

Vous pouvez utiliser Livy pour envoyer à distance un travail qui note un fichier stocké dans un objet blob Azure, puis consigne les résultats dans un autre objet blob. Pour ce faire, téléchargez le script Python à partir de [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) dans l’objet blob du cluster Spark. Vous pouvez utiliser un outil tel que **l’Explorateur de stockage Microsoft Azure** ou **AzCopy** pour copier le script dans l’objet blob de cluster. Dans le cas présent, nous avons chargé le script dans ***wasb:///example/python/ConsumeGBNYCReg.py***.


>[AZURE.NOTE] Les clés d’accès dont vous avez besoin se trouvent sur le portail du compte de stockage associé au cluster Spark.


Une fois chargé à cet emplacement, ce script s’exécute au sein du cluster Spark dans un contexte distribué. Il charge le modèle et exécute les prévisions sur les fichiers d’entrée en fonction du modèle.

Vous pouvez exécuter ce script à distance en effectuant une simple requête HTTPS/REST sur Livy. Voici une commande curl permettant de créer la requête HTTP qui appelle le script Python à distance. Vous devez remplacer CLUSTERLOGIN, CLUSTERPASSWORD et CLUSTERNAME par les valeurs appropriées à votre cluster Spark.


	# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Vous pouvez utiliser n’importe quel langage sur le système distant pour appeler la tâche Spark via Livy, par un simple appel HTTPS avec l’authentification de base.


>[AZURE.NOTE] Il vaut mieux utiliser la bibliothèque de requêtes Python pour effectuer cet appel HTTP, mais elle n’est pas installée par défaut dans Azure Functions. C’est pourquoi les anciennes bibliothèques HTTP sont utilisées.


Voici le code Python pour l’appel HTTP :

	#MAKE AN HTTPS CALL ON LIVY. 

	import os

	# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
	import httplib, urllib, base64
	
	# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
	host = '<spark cluster name>.azurehdinsight.net:443'
	username='<username>'
	password='<password>'
	
	#AUTHORIZATION
	conn = httplib.HTTPSConnection(host)
	auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
	headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
	
	# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
	# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
	r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
	response = conn.getresponse().read()
	print(response)
	conn.close()


Vous pouvez également ajouter ce code Python dans [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) pour soumettre un travail Spark qui évalue un objet blob en fonction de divers événements, comme un minuteur, une création ou la mise à jour d’un objet blob.

Si vous préférez vous passer de code, utilisez [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) pour appeler la notation groupée Spark en définissant une action HTTP dans le **Concepteur d’applications logiques** et en définissant ses paramètres.

- Sur le portail Azure, créez une application logique en sélectionnant **+Nouveau** > **Web + Mobile** > **Application logique**.
- Entrez le nom de l’application logique et le plan de service d’application pour afficher le **Concepteur d’applications logiques**.
- Sélectionnez une action HTTP, puis entrez les paramètres indiqués dans la figure suivante :

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## Et ensuite ? 

**Validation croisée et balayage hyperparamétrique** : consultez [Exploration et modélisation avancées des données avec Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) pour savoir comment effectuer la formation des modèles à l’aide de la validation croisée et du balayage hyperparamétrique.

<!---HONumber=AcomDC_0921_2016-->