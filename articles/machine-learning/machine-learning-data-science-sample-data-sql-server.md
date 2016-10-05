<properties 
	pageTitle="Échantillonner des données dans SQL Server sur Azure | Microsoft Azure" 
	description="Échantillonner des données dans SQL Server sur Azure" 
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
	ms.date="09/19/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Échantillonner des données dans SQL Server sur Azure


Ce document montre comment échantillonner des données stockées dans SQL Server sur Azure à l’aide de SQL ou du langage de programmation Python. Il montre également comment déplacer les données échantillonnées vers Azure Machine Learning en les enregistrant dans un fichier, en les chargeant vers un objet blob Azure, puis en les lisant dans Azure Machine Learning Studio.

L’échantillonnage Python utilise la bibliothèque ODBC [pyodbc](https://code.google.com/p/pyodbc/) pour se connecter à SQL Server sur Azure et la bibliothèque [Pandas](http://pandas.pydata.org/) pour effectuer l’échantillonnage proprement dit.

>[AZURE.NOTE] L’exemple de code SQL figurant dans ce document repose sur l’hypothèse que les données sont stockées dans SQL Server sur Azure. Si ce n’est pas le cas, reportez-vous à la rubrique [Déplacer des données vers SQL Server sur Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) pour savoir comment déplacer vos données vers SQL Server sur Azure.

**Pourquoi échantillonner vos données ?** Si vous prévoyez d'analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. Cette opération facilite la compréhension et l’exploration des données, ainsi que la conception de fonctionnalités. Son rôle dans le [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles d’apprentissage automatique.

Le **menu** ci-dessous pointe vers des rubriques qui expliquent comment échantillonner des données dans différents environnements de stockage.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Utilisation de SQL

Cette section décrit différentes méthodes permettant d’effectuer un échantillonnage aléatoire simple des données de la base de données via SQL. Choisissez une méthode en fonction de la taille de vos données et de leur distribution.

Les deux options ci-après indiquent comment utiliser l’élément newid dans SQL Server pour procéder à l’échantillonnage. La méthode que vous choisissez dépend du degré aléatoire qui doit caractériser l’échantillon (dans l’exemple de code ci-après, l’élément pk\_id est supposé correspondre à une clé primaire générée automatiquement).

1. Échantillon aléatoire moins strict

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Échantillon plus aléatoire

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Vous pouvez également utiliser l’élément TABLESAMPLE pour l’échantillonnage, comme illustré ci-dessous. L’utilisation de cette méthode peut constituer une meilleure approche si vos données sont volumineuses (en supposant que les données figurant sur des pages différentes ne sont pas corrélées) et que vous souhaitez que la requête s’exécute dans un délai acceptable.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Vous pouvez explorer et générer des fonctionnalités à partir de ces données échantillonnées en les stockant dans une nouvelle table.


###<a name="sql-aml"></a>Connexion à Azure Machine Learning

Vous pouvez utiliser directement les exemples de requêtes ci-dessus dans le module [Importer les données][import-data] d’Azure Machine Learning afin de sous-échantillonner les données à la volée et de les importer dans une expérience Azure Machine Learning. La capture d’écran ci-après illustre l’utilisation du module Lecteur pour lire les données échantillonnées :
   
![lecteur sql][1]

##<a name="python"></a>Utilisation du langage de programmation Python 

Cette section décrit l’utilisation de la [bibliothèque pyodbc](https://code.google.com/p/pyodbc/) pour établir une connexion ODBC à une base de données SQL Server dans Python. La chaîne de connexion de base de données se présente comme suit :(remplacez les variables servername, dbname, username et password par les valeurs de votre configuration) :

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La bibliothèque [Pandas](http://pandas.pydata.org/) de Python offre un ensemble élaboré de structures de données et d’outils d’analyse des données pour la manipulation des données dans le cadre d’une programmation en Python. Le code ci-après lit un échantillon de 0,1 % des données d’une table de la base de données Azure SQL dans une trame de données pandas :

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Vous pouvez à présent travailler sur les données échantillonnées dans la trame de données pandas.

###<a name="python-aml"></a>Connexion à Azure Machine Learning

Vous pouvez utiliser l’exemple de code ci-après pour enregistrer les données sous-échantillonnées dans un fichier et les charger dans un objet blob Azure. Les données figurant dans l’objet blob peuvent être lues directement dans une expérimentation Azure Machine Learning à l’aide du module [Importer les données][import-data]. La procédure comporte trois étapes :

1. Écrire la trame de données pandas dans un fichier local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Charger le fichier local dans un objet blob Azure

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Lisez les données de l’objet blob Azure à l’aide du module [Importer les données][import-data] d’Azure Machine Learning, comme l’illustre la capture d’écran ci-dessous :
 
![objet blob de lecteur][2]

## Exemple de processus TDSP (Team Data Science Process) en action

Pour obtenir un exemple de procédure pas à pas complet du processus TDSP (Team Data Science Process) à l’aide d’un jeu de données public, consultez [Processus TDSP (Team Data Science Process) en action : utilisation de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->