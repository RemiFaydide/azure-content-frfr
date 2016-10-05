<properties
	pageTitle="Processus TDSP (Team Data Science Process) en action : utiliser des clusters Hadoop | Microsoft Azure"
	description="Utilisation du processus TDSP (Team Data Science Process) pour un scénario de bout en bout employant un cluster Hadoop HDInsight pour créer et déployer un modèle à l'aide d'un groupe de données disponible publiquement."
	services="machine-learning,hdinsight"
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
	ms.author="hangzh;bradsev" />


# Processus TDSP (Team Data Science Process) en action : utilisation de clusters Hadoop HDInsight

Dans cette procédure pas à pas, nous allons utiliser le [processus TDSP (Team Data Science Process)](data-science-process-overview.md) avec un scénario complet au moyen d’un [cluster Azure Hadoop HDInsight](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur le jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) disponible publiquement, telles que le stockage, l’exploration, la conception de fonctionnalités et la réduction de l’échantillon de données. Les modèles de données sont créés avec Azure Machine Learning pour gérer les tâches prédictives de classification et de régression binaires et multiclasses.

Pour une procédure pas à pas qui montre comment gérer un jeu de données plus grand (1 téraoctet) avec un scénario similaire à l’aide de clusters Hadoop HDInsight pour le traitement des données, consultez [Processus TDSP (Team Data Science Process) : utilisation des clusters Hadoop Azure HDInsight sur un jeu de données de 1 To](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Il est également possible d'avoir recours à un interpréteur IPython notebook pour accomplir les tâches présentées dans cette procédure pas à pas au moyen du jeu de données de 1 To. Les utilisateurs qui souhaitent essayer cette approche doivent consulter la rubrique [Procédure pas à pas Criteo à l’aide d’une connexion Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Description du jeu de données NYC Taxi Trips

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

Pour obtenir tous les détails pertinents pour un voyage en particulier, il suffit de joindre les trois clés suivants : « medallion », « hack\_license » et « pickup\_datetime ».

Nous fournissons rapidement des informations supplémentaires relatives aux données lorsque nous les stockons dans les tables Hive.

## <a name="mltasks"></a>Exemples de tâches de prédiction
Le fait de connaître le type de prévisions que vous souhaitez obtenir de l’analyse des données permet de clarifier les tâches à inclure dans votre processus. Voici trois exemples de problèmes de prévisions que nous allons traiter dans ce guide et dont la formulation s’appuie sur le champ *tip\_amount* :

1. **Classification binaire** : prédire si un pourboire a ou non été versé pour une course ; autrement dit, une valeur *tip\_amount* supérieure à 0 $ constitue un exemple positif, alors qu’une valeur *tip\_amount* de 0 $ est un exemple négatif.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0

2. **Classification multiclasse** : prédire la fourchette du montant des pourboires versés pour une course. Nous divisons la valeur *tip\_amount* en cinq compartiments ou classes :

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Tâche de régression** : prédire le montant du pourboire versé pour une course.


## <a name="setup"></a>Configuration d’un cluster Hadoop HDInsight pour une analyse avancée

>[AZURE.NOTE] Il s'agit généralement d’une tâche d’**administration**.

Vous pouvez configurer un environnement Azure pour une analyse avancée qui utilise un cluster HDInsight en trois étapes :

1. [Création d’un compte de stockage](../storage/storage-create-storage-account.md) : ce compte de stockage est utilisé pour stocker des données dans un stockage Azure Blob. Les données utilisées dans les clusters HDInsight résident également ici.

2. [Personnaliser des clusters Hadoop Azure HDInsight pour le processus et la technologie d'analyse avancée](machine-learning-data-science-customize-hadoop-cluster.md). Cette étape crée un cluster Hadoop Azure HDInsight avec Anaconda Python 2.7 64 bits installé sur tous les nœuds. Il existe deux étapes importantes à retenir lors de la personnalisation de votre cluster HDInsight.

	* Rappelez-vous de lier le compte de stockage créé à l'étape 1 à votre cluster HDInsight, lorsque vous le créez. Ce compte de stockage est utilisé pour accéder aux données qui peuvent être traitées au sein du cluster.

	* Une fois le cluster créé, activez l'accès à distance au nœud principal du cluster. Accédez à l’onglet **Configuration** et cliquez sur **Activation à distance**. Cette étape fournit les informations d'identification d'utilisateur utilisées pour la connexion à distance.

3. [Création d’un espace de travail Azure Machine Learning](machine-learning-create-workspace.md) : cet espace de travail Azure Machine Learning est utilisé pour construire des modèles d'apprentissage automatique. Cette tâche est entamée après avoir effectué une exploration de données initiales et une réduction de l’échantillon à l'aide du cluster HDInsight.

## <a name="getdata"></a>Obtenir les données auprès d’une source publique

>[AZURE.NOTE] Il s'agit généralement d’une tâche d’**administration**.

Pour récupérer le jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) depuis son emplacement public, vous pouvez utiliser l’une des méthodes décrites dans l’article [Déplacer des données vers et depuis le stockage d’objets blob Azure](machine-learning-data-science-move-azure-blob.md) afin de copier les données dans votre machine.

Nous décrivons ici comment utiliser AzCopy pour transférer les fichiers contenant des données. Pour télécharger et installer AzCopy, suivez les instructions dans [Prise en main de l'utilitaire de ligne de commande AzCopy](../storage/storage-use-azcopy.md).

1. Dans une fenêtre d’invite de commandes, exécutez les commandes AzCopy suivantes en remplaçant *<chemin\_dossier\_de\_données* par la destination souhaitée :


		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Une fois la copie terminée, un total de 24 fichiers compressés se trouvent dans le dossier de données choisi. Décompressez les fichiers téléchargés dans le même répertoire sur votre ordinateur local. Prenez note du dossier où résident les fichiers décompressés. Ce dossier sera désigné par la chaîne *<chemin\_d’accès\_aux\_fichiers\_de\_données\_décompressés>* qui suit.


## <a name="upload"></a>Charger les données dans le conteneur par défaut du cluster Hadoop Azure HDInsight

>[AZURE.NOTE] Il s'agit généralement d’une tâche d’**administration**.

Dans les commandes AzCopy suivantes, remplacez les paramètres suivants par les valeurs réelles que vous avez spécifiées lors de la création du cluster Hadoop et lors de la décompression des fichiers de données.

* ***& #60;path\_to\_data\_folder >*** le répertoire (ainsi que le chemin d'accès) sur votre ordinateur qui contiennent les fichiers de données décompressés  
* ***&#60;storage account name of Hadoop cluster>*** le compte de stockage associé à votre cluster HDInsight
* ***&#60;default container of Hadoop cluster>*** le conteneur par défaut utilisé par votre cluster. Notez que le nom du conteneur par défaut est généralement le même nom que celui du cluster. Par exemple, si le cluster est appelé « abc123.azurehdinsight.net », le conteneur par défaut est abc123.
* ***&#60;storage account key>*** la clé du compte de stockage utilisé par votre cluster

À partir d'une invite de commandes ou d’une fenêtre Windows PowerShell sur votre ordinateur, exécutez les deux commandes AzCopy suivantes.

Cette commande permet de télécharger les données relatives aux courses sur le répertoire ***nyctaxitripraw*** dans le conteneur par défaut du cluster Hadoop.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Cette commande télécharge les données de prix sur le répertoire ***nyctaxifareraw*** dans le conteneur par défaut du cluster Hadoop.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Les données doivent être désormais dans le stockage Blob Azure et prêtes à être utilisées au sein du cluster HDInsight.

## <a name="#download-hql-files"></a>Connectez-vous au nœud principal du cluster Hadoop et préparez une analyse exploratoire de données

>[AZURE.NOTE] Il s'agit généralement d’une tâche d’**administration**.

Pour accéder au nœud principal du cluster afin d’exécuter une analyse exploratoire des données et une réduction de l’échantillon des données, suivez la procédure décrite dans [Accéder au nœud principal du cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

Dans cette procédure pas à pas, nous utilisons principalement les requêtes écrites dans [Hive](https://hive.apache.org/), un langage de requête similaire à SQL, pour effectuer des explorations de données préliminaires. Les requêtes Hive sont stockées dans des fichiers .hql. Nous réduisons ensuite l’échantillon de ces données à utiliser avec Azure Machine Learning pour la construction de modèles.

Pour préparer le cluster d’analyse exploratoire des données, nous téléchargeons les fichiers .hql contenant les scripts Hive pertinents de [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) dans un répertoire local (C:\\temp) sur le nœud principal. Pour ce faire, ouvrez l’**invite de commandes** dans le nœud principal du cluster et exécutez les deux commandes suivantes :

	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Ces deux commandes téléchargent tous les fichiers .hql nécessaires dans cette procédure pas à pas sur le répertoire local ***C:\\temp & #92 ;*** dans le nœud principal.

## <a name="#hive-db-tables"></a>Créer la base de données Hive et les tables partitionnées par mois

>[AZURE.NOTE] Il s'agit généralement d’une tâche d’**administration**.

Nous sommes maintenant prêts à créer des tables Hive pour notre jeu de données NYC taxi. Dans le nœud principal du cluster Hadoop, ouvrez la ***Ligne de commande Hadoop*** sur le bureau du nœud principal et saisissez le répertoire Hive en entrant la commande

    cd %hive_home%\bin

>[AZURE.NOTE] **Exécutez, dans cette procédure pas à pas, toutes les commandes Hive depuis l’invite de l’emplacement/du répertoire Hive mentionnée ci-dessus. Il se chargera automatiquement de tout problème lié au chemin d'accès. Nous utiliserons les termes « Invite du répertoire Hive », « Invite de l’emplacement/du répertoire Hive » et « Ligne de commande Hadoop » de manière interchangeable dans cette procédure pas à pas.**

À partir de l'invite du répertoire Hive, entrez la commande suivante dans la Ligne de commande Hadoop du nœud principal pour soumettre la requête Hive afin de créer des tables et une base de données Hive :

	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Voici le contenu du fichier ***C:\\temp\\sample\_hive\_create\_db\_and\_tables.hql*** qui crée la base de données Hive ***nyctaxidb*** et les tables ***trip*** et ***fare***.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double)  
	PARTITIONED BY (month int)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    pickup_datetime string,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Ce script Hive crée deux tables :

* la table « trip » contient le détail du trajet de chaque course (détails du chauffeur, heure d’embarquement, durée de la course et distance parcourue)
* la table « fare » contient le détail des prix (montant de la course, montant des pourboires, péages et surcharges).

Si vous avez besoin d’aide sur ces procédures souhaitez examiner d’autres solutions, consultez l’article [Envoyer des requêtes Hive directement depuis la ligne de commande Hadoop](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="#load-data"></a>Charger les données dans les tables Hive par partitions

>[AZURE.NOTE] Il s'agit généralement d’une tâche d’**administration**.

Le jeu de données taxi NYC a un partitionnement naturel par mois, qui nous permet d’accélérer les temps de traitement et de requête. Les commandes PowerShell ci-dessous (émises à partir du répertoire Hive à l'aide de la **Ligne de commande Hadoop**) chargent des données dans les tables Hive « trip » et « fare » partitionnées par mois.

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Le fichier *sample\_hive\_load\_data\_by\_partitions.hql* contient les commandes **LOAD** suivantes.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Notez que plusieurs des requêtes Hive que nous utilisons ici dans le processus d'exploration impliquent la recherche d'une seule partition ou seulement de quelques partitions. Mais ces requêtes peuvent être exécutées pour l'ensemble des données.

### <a name="#show-db"></a>Afficher les bases de données dans le cluster Hadoop HDInsight

Pour afficher les bases de données créées dans le cluster Hadoop HDInsight à l’intérieur de fenêtre de commande Hadoop, exécutez la commande suivante dans la ligne de commande Hadoop :

	hive -e "show databases;"

### <a name="#show-tables"></a>Afficher les tables Hive de la base de données nyctaxidb

Pour afficher les tables dans la base de données nyctaxidb, exécutez la commande suivante dans la ligne de commande Hadoop :

	hive -e "show tables in nyctaxidb;"

Nous pouvons confirmer que les tables sont partitionnées en exécutant la commande suivante :

	hive -e "show partitions nyctaxidb.trip;"

Le résultat prévu est affiché ci-dessous :

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 2.075 seconds, Fetched: 12 row(s)

De même, nous pouvons vérifier que la table « fare » est partitionnée en exécutant la commande suivante :

	hive -e "show partitions nyctaxidb.fare;"

Le résultat prévu est affiché ci-dessous :

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Exploration des données et ingénierie des fonctionnalités dans Hive

>[AZURE.NOTE] Il s'agit généralement d’une tâche de **données scientifiques**.

Les tâches d’exploration des données et d’ingénierie des fonctionnalités pour les données chargées dans les tables Hive peuvent être exécutées à l’aide de requêtes Hive. Voici des exemples de ces tâches que nous vous décrivons dans cette section :

- Affichez les 10 premiers enregistrements des deux tables.
- explorer les distributions de données de quelques champs portant sur différentes périodes ;
- examiner la qualité des données des champs de longitude et de latitude ;
- Générer des étiquettes de classification binaire et multiclasse reposant sur la valeur **tip\_amount**.
- Générez des fonctionnalités en calculant les distances des trajets directs.

### Exploration : afficher les 10 premiers enregistrements de la table trip

>[AZURE.NOTE] Il s'agit généralement d’une tâche de **données scientifiques**.

Pour avoir un aperçu des données, nous examinons les 10 enregistrements de chaque table. Exécutez les deux requêtes suivantes séparément depuis l’invite de commande du répertoire Hive de la ligne de commande Hadoop pour analyser les enregistrements.

Pour obtenir les 10 premiers enregistrements dans la table « trip » du premier mois :

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Pour obtenir les 10 premiers enregistrements dans la table « fare » du premier mois :

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Il est souvent utile de sauvegarder les enregistrements dans un fichier pour un affichage pratique. Une petite modification à la requête ci-dessus effectue cette opération :

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### Exploration : afficher le nombre d’enregistrements dans chacune des 12 partitions

>[AZURE.NOTE] Il s'agit généralement d’une tâche de **données scientifiques**.

La façon dont le nombre de courses varie au cours de l'année civile est intéressante. Le regroupement par mois nous permet d’avoir un aperçu de cette distribution de courses.

	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Cela nous donne le résultat :

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 283.406 seconds, Fetched: 12 row(s)

Ici, la première colonne est le mois et la seconde est le nombre de courses pour ce mois.

Nous pouvons également compter le nombre total d'enregistrements dans notre jeu de données de courses en exécutant la commande suivante à l'invite du répertoire Hive.

	hive -e "select count(*) from nyctaxidb.trip;"

Cela donne :

	173179759
	Time taken: 284.017 seconds, Fetched: 1 row(s)

À l'aide des commandes similaires à celles indiquées pour le jeu de données de course, nous pouvons émettre des requêtes Hive à partir de l'invite du répertoire Hive pour le jeu de données fare afin de valider le nombre d'enregistrements.

	hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Cela nous donne le résultat :

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 253.955 seconds, Fetched: 12 row(s)

Notez qu’exactement le même nombre de courses par mois est retourné pour les deux jeux de données. C’est le premier élément garantissant que les données ont été chargées correctement.

Le calcul du nombre total d'enregistrements dans le jeu de données fare peut être effectué à l'aide de la commande ci-dessous à partir de l'invite du répertoire Hive :

	hive -e "select count(*) from nyctaxidb.fare;"

Cela donne :

	173179759
	Time taken: 186.683 seconds, Fetched: 1 row(s)

Le nombre total d'enregistrements dans les deux tables est également le même. C’est le deuxième élément garantissant que les données ont été chargées correctement.

### Exploration : distribution des courses par médaillon

>[AZURE.NOTE] Il s'agit généralement d’une tâche de **données scientifiques**.

Cet exemple identifie le médaillon (numéro de taxi) sur plus de 100 courses au cours d’une période donnée. La requête a accès aux tables partitionnées, car elle est conditionnée par la variable de partition **month**. Les résultats de la requête sont écrits dans un fichier local queryoutput.tsv dans `C:\temp` sur le nœud principal.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Voici le contenu du fichier *sample\_hive\_trip\_count\_by\_medallion.hql* pour l’inspection.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

Le médaillon dans le jeu de données NYC taxi identifie un seul taxi. Nous pouvons identifier les taxis « occupés » en demandant quels taxis ont effectué plus d'un certain nombre d'allers-retours sur une période donnée. L'exemple suivant identifie les taxis qui ont effectué plus d’une centaine de courses durant les trois premiers mois et enregistre les résultats de la requête dans un fichier local, C:\\temp\\queryoutput.tsv.

Voici le contenu du fichier *sample\_hive\_trip\_count\_by\_medallion.hql* pour l’inspection.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

À partir de l'invite du répertoire Hive, exécutez la commande suivante :

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### Exploration : distribution des courses par médaillon et par licence de taxi

>[AZURE.NOTE] Il s'agit généralement d’une tâche de **données scientifiques**.

Lors de l'exploration d'un jeu de données, nous devons examiner fréquemment le nombre de co-occurrences des groupes de valeurs. Cette section fournit un exemple de procédure à suivre pour les chauffeurs et les taxis.

Le fichier *sample\_hive\_trip\_count\_by\_medallion\_license.hql* regroupe le jeu de données fare sur « medallion » et « hack\_license » et renvoie le nombre de chaque combinaison. Son contenu est présenté ci-dessous.

    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

Cette requête renvoie les combinaisons de taxi et de chauffeur particulier classées par ordre décroissant de courses.

À partir de l'invite du répertoire Hive, exécutez :

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Les résultats de la requête sont écrits dans un fichier local C:\\temp\\queryoutput.tsv.

### Exploration : évaluation de la qualité des données en recherchant les enregistrements de longitude et de latitude non valides

>[AZURE.NOTE] Il s'agit généralement d’une tâche de **données scientifiques**.

Un objectif commun d'une analyse exploratoire des données est d'éliminer les enregistrements non valides ou incorrects. L'exemple de cette section détermine si les champs de latitude ou de longitude contiennent une valeur en dehors de la zone NYC. Dans la mesure où il est probable que les valeurs de latitude-longitude de ces enregistrements soient erronées, nous souhaitons les éliminer des données devant être utilisées pour la modélisation.

Voici le contenu du fichier *sample\_hive\_quality\_assessment.hql* pour l’inspection.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


À partir de l'invite du répertoire Hive, exécutez :

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

L’argument *-S* inclus dans la commande supprime l’affichage de l’état des travaux Map/Reduce Hive. Son utilité réside dans le fait qu’il rend l’affichage de la sortie de la requête Hive plus lisible.

### Exploration : distributions de classe binaire des pourboires de course

**Remarque :** il s'agit généralement d’une tâche de **données scientifiques**.

Pour le problème de classification binaire présenté dans la section [Exemples de tâches de prédiction](machine-learning-data-science-process-hive-walkthrough.md#mltasks), il est utile de savoir si un pourboire a été donné ou non. Cette distribution de pourboires est binaire :

* pourboire donné (classe 1, tip\_amount > 0 $)  
* aucun pourboire (classe 0, tip\_amount > 0 $)

Le fichier *sample\_hive\_tipped\_frequencies.hql* ci-dessous effectue cette opération.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

À partir de l'invite du répertoire Hive, exécutez :

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### Exploration : distributions de classe dans le paramètre multiclasse

**Remarque :** il s'agit généralement d’une tâche de **données scientifiques**.

Pour le problème de classification multiclasse décrit dans la section [Exemples de tâches de prédiction](machine-learning-data-science-process-hive-walkthrough.md#mltasks), ce jeu de données se prête également à une classification naturelle où nous aimerions prédire la quantité de pourboires donnés. Nous pouvons utiliser des compartiments pour définir les montants de pourboires dans la requête. Pour obtenir les distributions de classe pour les différents montants de pourboire, nous utilisons le fichier *sample\_hive\_tip\_range\_frequencies.hql*. Son contenu est présenté ci-dessous.

	SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Exécutez la commande suivante dans la console de ligne de commande Hadoop :

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### Exploration : calculer la distance directe entre deux emplacements de latitude-longitude

**Remarque:** il s'agit généralement d’une tâche de **données scientifiques**.

Avoir une idée de la distance directe nous permet de déterminer l'écart entre celle-ci et la distance de course réelle. Nous expliquons cette fonctionnalité par le fait qu’un passager peut être moins susceptible de donner un pourboire s’il se rend compte que le chauffeur a pris intentionnellement un itinéraire beaucoup plus long.

Pour afficher la comparaison entre la distance de course réelle et la [distance Haversine](http://en.wikipedia.org/wiki/Haversine_formula) entre deux points de latitude-longitude (la distance orthodromique), nous utilisons les fonctions trigonométriques disponibles au sein de Hive, par conséquent :

    set R=3959;
    set pi=radians(180);

	insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

Dans la requête ci-dessus, R est le rayon de la Terre en miles et pi est converti en radians. Notez que les points de latitude-longitude sont « filtrés » pour supprimer les valeurs éloignées de la zone NYC.

Dans ce cas, nous écrivons nos résultats sur un répertoire nommé « queryoutputdir ». La séquence de commandes affichée ci-dessous crée d'abord ce répertoire de sortie, puis exécute la commande Hive.

À partir de l'invite du répertoire Hive, exécutez :

	hdfs dfs -mkdir wasb:///queryoutputdir

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Les résultats de la requête sont consignés dans 9 blobs Azure ***queryoutputdir/000000\_0*** à ***queryoutputdir/000008\_0*** situés dans le conteneur par défaut du cluster Hadoop.

Pour connaître la taille des objets BLOB individuels, nous exécutons la commande suivante à partir de l'invite du répertoire Hive :

	hdfs dfs -ls wasb:///queryoutputdir

Pour afficher le contenu d'un fichier donné, par exemple 000000\_0, nous utilisons la commande Hadoop`copyToLocal`.

	hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**Avertissement :**`copyToLocal` peut être très lent pour les fichiers volumineux et n'est pas recommandé pour une utilisation avec eux.

Le principal avantage lié au fait que ces données résident dans un objet blob Azure est que nous pouvons explorer les données au sein de Azure Machine Learning à l’aide du module [Importer des données][import-data].


## <a name="#downsample"></a>Réduire l’échantillon des données et créer des modèles dans Azure Machine Learning

**Remarque :** il s'agit généralement d’une tâche de **données scientifiques**.

Après la phase d'analyse exploratoire des données, nous sommes prêts à réduire l’échantillon des données pour générer des modèles dans Azure Machine Learning. Dans cette section, nous montrons comment utiliser une requête Hive pour réduire l’échantillon de données, qui est ensuite accessible à partir du module [Importer des données][import-data] dans Azure Machine Learning.

### Réduction de l'échantillonnage des données

Il existe deux étapes dans cette procédure. Tout d'abord nous regroupons les tables **nyctaxidb.trip** et **nyctaxidb.fare** sur trois clés présentes dans tous les enregistrements : « medallion », « hack\_license » et « pickup\_datetime ». Nous générons ensuite une étiquette de classification binaire **avec pourboire** et une étiquette de classification multiclasse **tip\_class**.

Pour pouvoir utiliser les échantillons de données réduits directement à partir du module [Importer des données][import-data] dans Azure Machine Learning, il est nécessaire de stocker les résultats de la requête ci-dessus dans une table Hive interne. Dans ce qui suit, nous créons une table interne Hive et remplissons son contenu avec les données regroupées et à échantillon réduit.

La requête s'applique directement aux fonctions Hive standards pour générer l'heure du jour, la semaine de l’année, le jour de la semaine (1 signifie lundi et 7 signifie dimanche) à partir du champ « pickup\_datetime » et la distance directe entre les emplacements de départ et d’arrivée. Les utilisateurs peuvent se reporter à la fonction [UDF LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) pour consulter la liste complète de ces fonctions.

Ensuite, cette requête réduit l’échantillon des données pour que ses résultats tiennent dans Azure Machine Learning Studio. Seulement 1 % environ du jeu de données d'origine est importé dans le Studio.

Voici le contenu du fichier *sample\_hive\_prepare\_for\_aml\_full.hql* qui prépare les données pour la création du modèle dans Azure Machine Learning.

		set R = 3959;
	    set pi=radians(180);

		create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
		)
		row format delimited fields terminated by ','
		lines terminated by '\n'
		stored as textfile;

		--- now insert contents of the join into the above internal table

    	insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
    	select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
		t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

    	from
    	(
        select
		medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
		${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Pour exécuter cette requête, à partir de l'invite du répertoire Hive :

	hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Nous avons maintenant une table interne « nyctaxidb.nyctaxi\_downsampled\_dataset », qui est accessible à l’aide du module [Importer des données][import-data] d’Azure Machine Learning. En outre, nous pouvons utiliser ce jeu de données pour générer des modèles d'apprentissage automatique.

### Utiliser le module Importer des données dans Azure Machine Learning pour accéder aux données à échantillon réduit

En tant que composants requis pour la création de requêtes Hive dans le module [Importer des données][import-data] d’Azure Machine Learning, nous devons accéder à un espace de travail Azure Machine Learning et aux informations d’identification du cluster et de son compte de stockage associé.

Certains détails sur le module [Importer des données][import-data] et les paramètres à entrer :

**URI du serveur HCatalog** : si le nom du cluster est abc123, il s'agit simplement : https://abc123.azurehdinsight.net

**Nom du compte utilisateur Hadoop** : le nom d'utilisateur choisi pour le cluster (et **non** le nom d'utilisateur de l'accès à distance)

**Mot de passe du compte utilisateur Hadoop** : le mot de passe choisi pour le cluster (et **non** le mot de passe d'accès à distance)

**Emplacement des données de sortie** : il est choisi pour être Azure.

**Nom du compte de stockage Azure** : le nom du compte de stockage par défaut associé au cluster.

**Nom de conteneur Azure** : c’est le nom de conteneur par défaut pour le cluster et c’est généralement le même que le nom du cluster. Pour un cluster appelé « abc123 », il s'agit simplement d’abc123.

**Remarque importante :** **toute table que nous souhaitons interroger à l’aide du module [Importer des données][import-data] dans Azure Machine Learning doit être une table interne.** Voici un conseil pour déterminer si une table T dans une base de données D.db est une table interne.

À partir de l'invite du répertoire Hive, exécutez la commande :

	hdfs dfs -ls wasb:///D.db/T

Si la table est une table interne et qu’elle est remplie, son contenu doit s’afficher ici. Pour déterminer si une table est une table interne, il est également possible d’utiliser Azure Storage Explorer. Utilisez-le pour accéder au nom de conteneur par défaut du cluster, puis filtrez par nom de table. Si la table et son contenu s'affichent, cela confirme qu'il s’agit d’une table interne.

Voici un aperçu de la requête Hive et du module [Importer des données][import-data] :

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Notez que depuis que nos données à l’échantillon réduit résident dans le conteneur par défaut, la requête Hive obtenue d’Azure Machine Learning est très simple est consiste uniquement en une « sélection * à partir de nyctaxidb.nyctaxi\_downsampled\_data ».

Le jeu de données peut maintenant être utilisé comme point de départ pour générer des modèles d'apprentissage automatique.

### <a name="mlmodel"></a>Créer des modèles dans Azure Machine Learning

Nous sommes désormais capables de passer aux phases de création et de déploiement de modèles dans [Azure Machine Learning](https://studio.azureml.net). Les données sont exploitables pour répondre aux problèmes de prévision identifiés précédemment :

**1. Classification binaire** : prédire si un pourboire a ou non été versé pour une course.

**Apprenant utilisé :** régression logistique à deux classes

a. Pour ce problème, notre étiquette (ou classe) cible est « avec pourboire ». Notre jeu de données original à l’échantillon réduit dispose de quelques colonnes qui sont des fuites cibles pour cette expérience de classification. En particulier : tip\_class, tip\_amount et total\_amount révèlent des informations sur l'étiquette cible qui n'est pas disponible au moment du test. Nous supprimons ces colonnes du compte à l’aide du module [Sélectionner des colonnes dans le jeu de données][select-columns].

L'instantané ci-dessous illustre notre expérience pour prédire si un pourboire a été versé pour une course donnée.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. Pour cette expérience, nos distributions d'étiquette cible ont été d’environ 1:1.

L'instantané ci-dessous montre la distribution d’étiquettes de classe de pourboire pour le problème de classification binaire.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

Par conséquent, nous obtenons une intégration de 0,987 comme indiqué dans la figure ci-dessous.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. Classification multiclasse ** : pour prédire le montant des pourboires réglés pour la course, en utilisant les classes précédemment définies.

**Apprenant utilisé :** régression logistique multiclasse

a. Pour ce problème, notre cible (ou classe) est « tip\_class », ce qui peut prendre une des cinq valeurs suivantes (0,1,2,3,4). Comme dans le cas de classification binaire, nous avons quelques colonnes qui sont des fuites cibles pour cette expérience. En particulier : avec pourboire, tip\_amount et total\_amount révèlent des informations sur l'étiquette cible qui n'est pas disponible au moment du test. Nous supprimons ces colonnes à l’aide du module [Sélectionner des colonnes dans le jeu de données][select-columns].

L'instantané ci-dessous illustre notre expérience pour prédire le compartiment où un pourboire est susceptible de tomber (classe 0 : pourboire = 0 $, classe 1 : pourboire > 0 $ et pourboire <= 5 $, classe 2 : pourboire > 5 $ et pourboire <= 10 $, classe 3 : pourboire > 10 $ et pourboire <= 20 $, classe 4 : pourboire > 20 $)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Nous montrons maintenant à quoi ressemble notre distribution de classe test réelle. Nous voyons que la classe 0 et la classe 1 prévalent et que les autres classes sont rares.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. Pour cette expérience, nous utilisons une matrice de confusion pour consulter la précision de nos prédictions. Consultez l’illustration ci-dessous.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Notez que la précision des classes sur les classes les plus courantes est assez bonne, mais que le modèle n'effectue pas un bon travail d’« apprentissage » sur les classes plus rares.


**3. Tâche de régression** : prédire le montant du pourboire versé pour une course.

**Apprenant utilisé :** arbre de décision optimisé

a. Pour ce problème, notre étiquette (ou classe) cible est « tip\_amount ». Nos fuites cibles dans ce cas sont : avec pourboire, tip\_class, total\_amount. Toutes ces variables révèlent des informations sur le montant du pourboire qui est en général indisponible au moment du test. Nous supprimons ces colonnes à l’aide du module [Sélectionner des colonnes dans le jeu de données][select-columns].

L'instantané ci-dessous illustre notre expérience pour prédire la quantité de pourboire donné.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. Pour les problèmes de régression, nous évaluons la précision de nos prévisions en examinant l'erreur au carré dans les prévisions, le coefficient de détermination, etc. Nous présentons ce qui suit.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Nous voyons que le coefficient de détermination est de 0,709, ce qui signifie que 71 % environ de la variance est expliquée par nos coefficients modèles.

**Remarque importante :** pour en savoir plus sur Azure Machine Learning, comment y accéder et comment l’utiliser, reportez-vous à [Qu’est-ce que l'apprentissage automatique ?](machine-learning-what-is-machine-learning.md). La [galerie Cortana Intelligence](https://gallery.cortanaintelligence.com/) est une ressource très utile pour découvrir de nombreuses expériences d’apprentissage automatique sur Azure Machine Learning. La galerie couvre une large gamme d'expériences et fournit une présentation approfondie des fonctionnalités d’Azure Machine Learning.

## Informations de licence

Ce didacticiel et ses scripts associés sont partagés par Microsoft sous la licence MIT. Pour plus d’informations, voir le fichier LICENSE.txt figurant dans le répertoire de l’exemple de code sur GitHub.

## Références

•	[Page de téléchargement des jeux de données NYC Taxi Trips par Andrés Monroy (en anglais)](http://www.andresmh.com/nyctaxitrips/)  
•	[Page de partage des données relatives aux courses en taxi new-yorkais par Chris Whong (en anglais)](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•	[Page de recherche et de statistiques de la Commission des services de taxis et de limousines de la ville de New York (en anglais)](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->