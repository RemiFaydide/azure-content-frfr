<properties 
	pageTitle="Traiter les données de SQL Azure | Microsoft Azure" 
	description="Traiter les données de SQL Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="jhubbard" 
	editor="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Traitement des données d’une machine virtuelle SQL Server sur Azure

Ce document décrit l'exploration des données et la génération de fonctionnalités pour les données stockées dans une machine virtuelle SQL Server sur Azure. Cela est possible avec le retraitement des données à l'aide de SQL ou en utilisant un langage de programmation comme Python.


> [AZURE.NOTE] Les exemples d’instructions SQL qui figurent dans ce document reposent sur l’hypothèse que les données sont stockées dans SQL Server. Dans le cas contraire, reportez-vous à la cartographie du processus de science des données du cloud pour découvrir comment déplacer vos données vers SQL Server.

##<a name="SQL"></a>Utilisation de SQL

Dans cette section, nous décrivons les tâches de retraitement des données via SQL ci-après :

1. [Exploration des données](#sql-dataexploration)
2. [Génération de fonctionnalités](#sql-featuregen)

###<a name="sql-dataexploration"></a>Exploration des données
Voici quelques exemples de scripts SQL utilisables pour l’exploration de magasins de données dans SQL Server.


> [AZURE.NOTE] Pour découvrir un exemple pratique, vous pouvez utiliser le [jeu de données des taxis new-yorkais NYC Taxi](http://www.andresmh.com/nyctaxitrips/) et vous reporter au notebook IPython intitulé [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) (Retraitement des données de New-York City à l’aide de Notebook IPython et de SQL Server) pour connaître la procédure pas à pas.

1. Obtenir le nombre d’observations par jour

	`SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)`

2. Obtenir les niveaux dans une colonne catégorielle

	`select  distinct <column_name> from <databasename>`

3. Obtenir le nombre de niveaux en combinant deux colonnes catégorielles

	`select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtenir la distribution relative aux colonnes numériques

	`select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Génération de fonctionnalités

Dans cette section, nous décrivons plusieurs manières de générer des fonctionnalités via SQL :

1. [Génération de fonctionnalités utilisant des décomptes](#sql-countfeature)
2. [Génération de caractéristiques de compartimentage](#sql-binningfeature)
3. [Déploiement des caractéristiques à partir d’une seule colonne](#sql-featurerollout)


> [AZURE.NOTE] Une fois que vous avez généré des fonctionnalités supplémentaires, vous pouvez soit les ajouter sous forme de colonnes à la table existante, soit créer une autre table avec les fonctionnalités supplémentaires et la clé primaire que vous pouvez joindre à la table d’origine.

###<a name="sql-countfeature"></a>Génération de fonctionnalités utilisant des décomptes

Ce document décrit deux manières de générer des fonctionnalités utilisant des décomptes. La première méthode a recours à une somme conditionnelle, tandis que la seconde méthode utilise la clause « where ». Vous pouvez ensuite associer ces dernières à la table d’origine (à l’aide des colonnes de clé primaire) pour disposer de fonctionnalités de décompte parallèlement aux données d’origine.

	select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

	select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
	where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Génération de caractéristiques de compartimentage

L’exemple ci-dessous illustre comment générer des fonctionnalités compartimentées en divisant (à l’aide de 5 emplacements) une colonne numérique qui peut être plutôt utilisée sous la forme d’une fonctionnalité :

	`SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Déploiement des fonctionnalités à partir d’une seule colonne

Dans cette section, nous décrivons comment déployer une seule colonne dans une table afin de générer des fonctionnalités supplémentaires. Cet exemple présuppose l’existence d’une colonne de latitude ou de longitude dans la table à partir de laquelle vous essayez de générer des fonctionnalités.

Voici une petite présentation des données de latitude/longitude issue du site stackoverflow : [How to measure the accuracy of latitude and longitude?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude) (Comment mesurer la précision de la latitude et de la longitude). Vous pourrez ainsi vous familiariser avec ces notions avant d’implémenter le champ d’emplacement :

- Le signe indique si nous nous trouvons au nord, au sud, à l’est ou à l’ouest.
- Un chiffre des centaines différent de zéro indique que nous utilisons la longitude, et non la latitude.
- Le chiffre des dizaines équivaut à environ 1 000 kilomètres. Il nous fournit des informations utiles sur le continent ou l’océan dans lequel nous nous trouvons.
- Le chiffre des unités (un degré décimal) équivaut à 111 kilomètres maximum (60 milles marins, soit environ 69 milles terrestres). Il nous permet de déterminer approximativement le département de grande superficie ou le pays dans lequel nous sommes.
- La première décimale équivaut à 11,1 km maximum : elle permet de distinguer la position d’une grande ville de celle d’une autre grande localité voisine.
- La deuxième décimale équivaut à 1,1 km maximum : elle permet de différencier un village du suivant.
- La troisième décimale équivaut à 110 m maximum : elle permet d’identifier un domaine agricole ou un campus universitaire de grande taille.
- La quatrième décimale correspond à 11 m maximum : elle permet d’identifier une parcelle de terre. Cette information est comparable à la précision classique d’un appareil GPS non corrigé sans aucune interférence.
- La cinquième décimale équivaut à 1,1 m maximum : elle permet de distinguer un arbre d’un autre. Un tel niveau de précision sur les appareils GPS commerciaux ne peut être atteint qu’au moyen d’une correction différentielle.
- La sixième décimale équivaut à 0,11 m maximum : vous pouvez notamment l’utiliser pour représenter des structures en détail, pour concevoir des plans d’aménagement paysager et pour construire des routes. Elle devrait se révéler amplement suffisante pour assurer le suivi des mouvements des glaciers et des rivières. L’obtention d’une telle précision sur un GPS nécessite l’emploi de mesures rigoureuses, telles qu’une correction différentielle.

Vous pouvez implémenter les informations de localisation comme illustré ci-dessous, en les répartissant par région, par emplacement et par ville. Notez que vous pouvez aussi appeler un point de terminaison REST, tel que l’API Bing Cartes disponible dans [Rechercher un emplacement par point](https://msdn.microsoft.com/library/ff701710.aspx), pour obtenir des informations sur la région/le secteur.

	select 
		<location_columnname>
		,round(<location_columnname>,0) as l1		
		,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end 	
		,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end 	
		,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end 	
		,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end 	
		,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end 	
		,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end 	
	from <tablename>

Vous pouvez en outre exploiter les fonctionnalités de localisation ci-dessus pour générer d’autres fonctionnalités utilisant des décomptes comme décrit précédemment.


> [AZURE.TIP] Vous pouvez insérer les enregistrements par programmation en utilisant le langage de votre choix. Vous aurez peut-être besoin d’insérer les données dans des blocs pour améliorer l’efficacité des écritures (pour obtenir un exemple illustrant la marche à suivre, consultez cet [exemple HelloWorld qui montre comment accéder à SQL Server avec python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Une autre solution consiste à insérer les données dans la base de données à l’aide de l’[utilitaire BCP](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Connexion à Azure Machine Learning

La fonctionnalité que vous venez de générer peut être ajoutée sous la forme d’une colonne à une table existante ou stockée dans une nouvelle table et associée à la table d’origine pour l’apprentissage automatique. Vous pouvez générer des fonctionnalités ou y accéder si elles sont déjà créées à l’aide du module [Importer des données][import-data] dans Azure Machine Learning comme expliqué ci-dessous :

![lecteurs azureml][1]

##<a name="python"></a>Utilisation d’un langage de programmation tel que Python

L’utilisation de Python pour explorer les données et générer des fonctionnalités quand les données sont stockées dans SQL Server est comparable au traitement des données dans l’objet blob Azure à l’aide de Python comme expliqué dans [Traiter les données Azure Blob dans votre environnement de science des données](machine-learning-data-science-process-data-blob.md). Les données doivent être chargées à partir de la base de données dans une trame de données pandas, puis faire l’objet d’un traitement complémentaire. Nous décrivons dans cette section le processus de connexion à la base de données et de chargement des données dans la trame de données.

Le format de chaîne de connexion ci-après vous permet de vous connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (en remplaçant les variables servername, dbname, username et password par les valeurs qui vous correspondent) :

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque Pandas](http://pandas.pydata.org/) de Python offre un ensemble élaboré de structures de données et d’outils d’analyse des données pour la manipulation des données dans le cadre d’une programmation en Python. Le code ci-après lit les résultats renvoyés par une base de données SQL Server dans une trame de données pandas :

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Vous pouvez à présent utiliser la trame de données Pandas comme décrit dans l’article [Traiter les données Azure Blob dans votre environnement de science des données](machine-learning-data-science-process-data-blob.md).

## Exemple de processus de science des données Azure en action

Pour découvrir un exemple de procédure pas à pas du processus de science des données Azure à l’aide d’un jeu de données public, consultez la rubrique [Processus de science des données Azure en action](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=AcomDC_0921_2016-->