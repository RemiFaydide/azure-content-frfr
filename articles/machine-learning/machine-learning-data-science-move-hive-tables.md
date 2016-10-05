<properties 
	pageTitle="Créer et charger des données dans des tables Hive à partir d'un stockage Blob | Microsoft Azure" 
	description="Créer des tables Hive et charger des données d’un blob dans des tables Hive" 
	services="machine-learning,storage" 
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
	ms.date="09/14/2016" 
	ms.author="bradsev" />

 
#Créer et charger des données dans des tables Hive à partir d’un stockage Azure Blob

Cette rubrique présente des requêtes Hive génériques qui créent des tables Hive et chargent des données à partir d’un stockage d’objets blob Azure. Il donne également quelques conseils sur le partitionnement des tables Hive et sur l’utilisation du format ORC (Optimized Row Columnar) pour améliorer les performances des requêtes.

Ce **menu** pointe vers des rubriques qui expliquent comment recevoir des données dans d’autres environnements cibles où les données peuvent être stockées et traitées pendant le processus TDSP (Team Data Science Process).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## Composants requis
Cet article suppose que vous avez :
 
* Créé un compte Azure Storage. Si vous avez besoin d'aide, consultez [Créer un compte Azure Storage](../hdinsight-get-started.md#storage).
* Approvisionné un cluster Hadoop personnalisé avec le service HDInsight. Si vous avez besoin d'aide, consultez [Personnaliser des clusters Hadoop Azure HDInsight pour l'analyse avancée](machine-learning-data-science-customize-hadoop-cluster.md).
* Activé l’accès à distance au cluster, saisi les identifiants appropriés et ouvert la console de ligne de commande Hadoop. Si vous avez besoin d'aide, consultez [Accéder au nœud principal du cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

## Téléchargement de données vers le stockage d’objets blob Azure
Si vous avez créé une machine virtuelle Azure en suivant les instructions de l’article [Configurer une machine virtuelle Azure pour l’analyse avancée](machine-learning-data-science-setup-virtual-machine.md), ce fichier de script doit avoir été téléchargé dans le répertoire *C:\\Users<nom\_utilisateur>\\Documents\\Data Science Scripts* de la machine virtuelle. Pour pouvoir être envoyées, ces requêtes Hive nécessitent simplement que votre schéma de données et la configuration de votre stockage Azure Blob soient déclarés dans les champs appropriés.

Nous partons du principe que les données des tables Hive ont un format tabulaire **non compressé** et qu’elles ont été chargées dans le conteneur par défaut (ou un conteneur supplémentaire) du compte de stockage utilisé par le cluster Hadoop.

Si vous souhaitez vous exercer avec l’exemple **NYC Taxi Trip Data**, vous devez :

- **télécharger** les 24 fichiers [NYC Taxi Trip Data](http://www.andresmh.com/nyctaxitrips) (12 fichiers Trip et 12 fichiers Fare),
- **décompresser** tous les fichiers en fichiers .csv, puis
- les **télécharger** vers le conteneur par défaut (ou conteneur approprié) du compte de stockage Azure créé par la procédure décrite dans la rubrique [Personnaliser des clusters Azure HDInsight Hadoop pour le processus d’analyse avancé et la technologie](machine-learning-data-science-customize-hadoop-cluster.md). Pour découvrir le processus qui vous permet de télécharger les fichiers .csv du conteneur par défaut sur le compte de stockage, consultez cette [page](machine-learning-data-science-process-hive-walkthrough.md#upload).


## <a name="submit"></a>Envoi de requêtes Hive

Pour envoyer des requêtes Hive, utilisez au choix :

1. [Envoyer des requêtes Hive avec la ligne de commande Hadoop dans le nœud principal du cluster Hadoop](#headnode)
2. [Envoyer des requêtes Hive avec l'éditeur Hive](#hive-editor)
3. [Envoyer des requêtes Hive avec les commandes Azure PowerShell](#ps)
 
Des requêtes Hive sont similaires à SQL. Si vous maîtrisez SQL, l’[aide-mémoire Hive pour utilisateurs SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) peut vous être utile.

Lors de l’envoi d’une requête Hive, vous pouvez également contrôler la destination de sa sortie : écran, fichier local sur le nœud principal ou blob Azure.


###<a name="headnode"></a> 1. Envoyer des requêtes Hive avec la ligne de commande Hadoop dans le nœud principal du cluster Hadoop

Une requête Hive complexe envoyée directement au nœud principal du cluster Hadoop est traitée plus rapidement qu'avec un éditeur Hive ou des scripts Azure PowerShell.

Connectez-vous au nœud principal du cluster Hadoop, ouvrez la ligne de commande Hadoop sur le bureau du nœud principal et saisissez la commande `cd %hive_home%\bin`.

Vous disposez de trois possibilités pour envoyer des requêtes Hive dans la ligne de commande Hadoop :

* directement ;
* à l’aide de fichiers HQL ;
* à l’aide de la console de commande Hive.

#### Envoyer directement des requêtes Hive dans la ligne de commande Hadoop 

Vous pouvez exécuter une commande du type `hive -e "<your hive query>;` pour envoyer une requête Hive simple directement dans la ligne de commande Hadoop. Voici un exemple, où l’encadré rouge indique la commande qui envoie la requête Hive et l’encadré vert, la sortie de la requête Hive.

![Create workspace](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-1.png)

#### Envoyer des requêtes Hive dans des fichiers HQL

Lorsque la requête Hive est plus complexe et comporte plusieurs lignes, la modifier dans la ligne de commande ou la console de commande Hive n’est pas simple. L’alternative consiste à utiliser un éditeur de texte dans le nœud principal du cluster Hadoop pour enregistrer la requête Hive dans un fichier HQL situé dans le répertoire local du nœud principal. Ensuite, celle-ci peut être envoyée à l'aide de l'argument `-f`, comme indiqué ci-dessous :
	
	hive -f "<path to the .hql file>"

![Create workspace](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-3.png)


**Supprimer l’affichage de l’état d’avancement des requêtes Hive**

Par défaut, après l’envoi d’une requête Hive dans la ligne de commande Hadoop, l’état d’avancement de la tâche Map/Reduce s’affiche à l’écran. Pour supprimer cet affichage, déclarez l'argument `-S` (« S » en majuscule) dans la ligne de commande, comme indiqué ci-dessous :

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Envoyer des requêtes Hive dans la console de commande Hive

Vous pouvez également ouvrir la console de commande Hive en exécutant la commande `hive` dans la ligne de commande Hadoop, puis envoyer les requêtes Hive dans la console de commande Hive. Voici un exemple. Ici, les deux encadrés rouges indiquent les commandes utilisées pour ouvrir la console de commande Hive et envoyer la requête Hive dans cette console. L’encadré vert montre la sortie de la requête Hive.

![Create workspace](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-2.png)

Les exemples précédents affichent directement les résultats de la requête à l’écran. Vous pouvez également consigner la sortie dans un fichier local sur le nœud principal ou dans un blob Azure. Puis, vous pouvez utiliser d’autres outils pour analyser plus finement la sortie de la requête Hive.

**Enregistrer les résultats d’une requête Hive dans un fichier local**

Pour enregistrer les résultats d’une requête Hive dans un répertoire local du nœud principal, vous devez envoyer celle-ci dans la ligne de commande Hadoop, comme indiqué ci-dessous :

	hive -e "<hive query>" > <local path in the head node>

Dans l'exemple suivant, la sortie de la requête Hive est consignée dans un fichier `hivequeryoutput.txt` situé dans le répertoire `C:\apps\temp`.

![Create workspace](./media/machine-learning-data-science-move-hive-tables/output-hive-results-1.png)

**Enregistrer les résultats d’une requête Hive dans un blob Azure**

Vous pouvez également enregistrer les résultats d’une requête Hive dans un blob Azure situé dans le conteneur par défaut du cluster Hadoop. La requête Hive se présente comme suit :

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Dans l'exemple suivant, la sortie de la requête Hive est consignée dans le répertoire de blob `queryoutputdir` situé dans le conteneur par défaut du cluster Hadoop. Ici, il suffit d’indiquer le nom du répertoire, sans celui du blob. Une erreur est générée si vous déclarez le nom du répertoire et celui du blob, comme dans `wasb:///queryoutputdir/queryoutput.txt`.

![Create workspace](./media/machine-learning-data-science-move-hive-tables/output-hive-results-2.png)

Si vous ouvrez le conteneur par défaut du cluster Hadoop à l’aide d’Azure Storage Explorer, la requête Hive renvoie le résultat affiché dans la figure suivante. Vous pouvez utiliser le filtre (encadré rouge) pour retrouver un blob dont le nom comporte les lettres spécifiées.

![Create workspace](./media/machine-learning-data-science-move-hive-tables/output-hive-results-3.png)

###<a name="hive-editor"></a> 2. Envoyer des requêtes Hive avec l'éditeur Hive

Vous pouvez également utiliser la console de requête (éditeur Hive) en entrant une URL sous la forme *https://&#60;Hadoop nom de cluster >.azurehdinsight.net/Home/HiveEditor* dans un navigateur web. Vous devez être connecté pour afficher cette console, et vous devez donc saisir ici vos informations d’identification de cluster Hadoop.

###<a name="ps"></a> 3. Envoyer des requêtes Hive avec les commandes Azure PowerShell

Vous pouvez également utiliser PowerShell pour envoyer des requêtes Hive. Pour obtenir de l'aide, consultez [Envoi de tâches Hive avec PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Création de la base de données et des tables Hive

Les requêtes Hive sont disponibles en téléchargement dans le [dépôt Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).

Voici la requête Hive qui crée une table Hive.

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Voici les descriptions des champs que vous devez renseigner et d’autres opérations de configuration :

- **&#60;database name>** : nom de la base de données que vous souhaitez créer. Si vous voulez utiliser la base de données par défaut, la requête *create database...* peut être omise.
- **&#60;table name>** : nom de la table que vous voulez créer dans la base de données spécifiée. Si vous voulez utiliser la base de données par défaut, la table peut être désignée directement par *&#60;table name>* sans &#60;database name>.
- **&#60;field separator>** : séparateur qui délimite les champs dans le fichier de données à charger dans la table Hive.
- **&#60;line separator>** : séparateur qui délimite les lignes dans le fichier de données.
- **&#60;storage location>** : emplacement Azure où enregistrer les données des tables Hive. Si vous ne spécifiez pas *LOCATION &#60;storage location>*, la base de données et les tables sont stockées dans le répertoire *hive/warehouse/* du conteneur par défaut du cluster Hive par défaut. Si vous souhaitez spécifier l’emplacement de stockage, ce dernier doit se trouver dans le conteneur par défaut de la base de données et des tables. Cet emplacement doit être désigné comme emplacement relatif du conteneur par défaut du cluster au format *’wasb:///&#60;directory 1>/’* ou *’wasb:///&#60;directory 1>/&#60;directory 2>/’*, etc. Une fois la requête exécutée, les répertoires relatifs sont créés dans le conteneur par défaut.
- **TBLPROPERTIES("skip.header.line.count"="1")** : si le fichier de données contient une ligne d'en-tête, vous devez ajouter cette propriété **à la fin** de la requête *create table*. Sinon, cette ligne d’en-tête est chargée comme un enregistrement dans la table. Si le fichier de données ne contient aucune ligne d’en-tête, cette configuration peut être omise dans la requête.

## <a name="load-data"></a>Chargement des données dans des tables Hive
Voici la requête Hive qui charge les données dans une table Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;path to blob data>** : si le fichier blob à charger dans la table Hive se trouve dans le conteneur par défaut du cluster Hadoop HDInsight, le chemin *&#60;path to blob data>* doit être au format *’wasb:///&#60;directory in this container>/&#60;blob file name>’*. Le fichier blob peut également se trouver dans un autre conteneur du cluster Hadoop HDInsight. Dans ce cas, *&#60;path to blob data>* doit être au format *’wasb://&#60;container name>@&#60;storage account name>.blob.core.windows.net/&#60;blob file name>’*.

	>[AZURE.NOTE] Les données blob à charger dans la table Hive doivent se trouver dans le conteneur par défaut ou un autre conteneur du compte de stockage du cluster Hadoop. Sinon, la requête *LOAD DATA* ne peut pas s'exécuter car elle n'aura pas accès aux données.


## <a name="partition-orc"></a>Rubriques avancées : Table partitionnée et Stocker des données Hive au format ORC

Si les données sont volumineuses, le partitionnement de la table est avantageux pour les requêtes qui doivent n’en balayer que quelques partitions. Par exemple, il est raisonnable de partitionner les données journalisées d’un site Web par dates.

Outre le partitionnement des tables Hive, il est également judicieux de stocker les données Hive au format ORC (Optimized Row Columnar). Pour plus d'informations sur le format ORC, consultez l'article <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">L'utilisation de fichiers ORC améliore les performances lorsque Hive lit, écrit et traite des données</a>.

### Table partitionnée
Voici la requête Hive qui crée une table partitionnée et charge les données dans celle-ci.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Lors de l'interrogation de tables partitionnées, il est recommandé d'ajouter la condition de partition au **début** de la clause `where`, car cela améliore considérablement l'efficacité de la recherche.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Stocker des données Hive au format ORC

Vous ne pouvez pas charger directement des données au format ORC depuis le stockage blob dans des tables Hive. Voici les étapes que vous devez suivre pour charger des données au format ORC depuis des blobs Azure dans des tables Hive.

Créez une table externe **STORED AS TEXTFILE** et chargez les données du stockage blob dedans.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Créez une table interne avec le même schéma que la table externe à l’étape 1 et le même délimiteur de champ. Puis, stockez-y les données Hive au format ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Sélectionnez les données de la table externe à l’étape 1 et insérez-les dans la table ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

>[AZURE.NOTE] Si la table TEXTFILE *&#60;database name>.&#60;external textfile table name>* a des partitions, à l'Étape 3, la commande `SELECT * FROM <database name>.<external textfile table name>` sélectionne la variable de partition comme champ dans le jeu de données renvoyé. Le fait de l'insérer dans *&#60;database name>.&#60;ORC table name>* échoue car *&#60;database name>.&#60;ORC table name>* ne dispose pas de la variable de partition comme champ dans le schéma de la table. Dans ce cas, vous devez sélectionner explicitement les champs à insérer dans *&#60;database name>.&#60;ORC table name>* comme suit :

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

Pour plus de sécurité, lorsque vous utilisez la requête suivante, il est recommandé de déplacer la table *&#60;external textfile table name>* une fois toutes les données insérées dans la table *&#60;database name>.&#60;ORC table name>* :

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

À l’issue de cette procédure, vous devez obtenir une table immédiatement exploitable et contenant des données au format ORC.

<!---HONumber=AcomDC_0921_2016-->