<properties 
	pageTitle="Activité de diffusion en continu Hadoop" 
	description="Découvrez comment vous pouvez utiliser l’activité de diffusion en continu Hadoop dans une fabrique de données Azure pour exécuter des programmes de diffusion en continu Hadoop sur votre cluster HDInsight propre/à la demande." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Activité de diffusion en continu Hadoop
Vous pouvez utiliser l’activité HDInsightStreamingActivity pour appeler une tâche de diffusion en continu Hadoop à partir d’un pipeline Azure Data Factory. L’extrait de code JSON suivant illustre la syntaxe pour l’utilisation de HDInsightStreamingActivity dans un fichier JSON de pipeline.

L’activité de diffusion en continu HDInsight dans un [pipeline](data-factory-create-pipelines.md) Data Factory exécute des programmes de diffusion en continu Hadoop sur votre cluster HDInsight [propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) sous Windows ou Linux. Cet article s'appuie sur l'article [Activités de transformation des données](data-factory-data-transformation-activities.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

## Exemple JSON
Le cluster HDInsight est automatiquement rempli avec les données (davinci.txt) et les exemples de programmes (wc.exe et cat.exe). Par défaut, le nom du conteneur utilisé par le cluster HDInsight est le nom du cluster lui-même. Par exemple, si votre nom de cluster est myhdicluster, le nom du conteneur d’objets blob associé est myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

Notez les points suivants :

1. Définissez **linkedServiceName** sur le nom du service lié qui pointe vers votre cluster HDInsight sur lequel est exécutée la tâche de diffusion en continu mapreduce.
2. Affectez au type de l’activité la valeur **HDInsightStreaming**.
3. Pour la propriété **mapper**, spécifiez le nom du fichier exécutable du mappeur. Dans l’exemple, cat.exe est le fichier exécutable du mappeur.
4. Pour la propriété **reducer**, spécifiez le nom du fichier exécutable du raccord de réduction. Dans l’exemple, wc.exe est le fichier exécutable du raccord de réduction.
5. Pour la propriété de type **input**, spécifiez le fichier en entrée (y compris son emplacement) du mappeur. Dans l’exemple « wasb://adfsample@<nom du compte>.blob.core.windows.net/example/data/gutenberg/davinci.txt », adfsample est le conteneur de l’objet blob, example/data/Gutenberg est le dossier et davinci.txt est l’objet blob.
6. Pour la propriété de type **output**, spécifiez le fichier en sortie (y compris son emplacement) du raccord de réduction. La sortie de la tâche de diffusion en continu Hadoop est écrite à l’emplacement spécifié pour cette propriété.
7. Dans la section **filePaths**, spécifiez les chemins des fichiers exécutables du mappeur et du raccord de réduction. Dans l’exemple « adfsample/example/apps/wc.exe », adfsample est le conteneur de l’objet blob, example/apps est le dossier et wc.exe est le fichier exécutable.
8. Pour la propriété **fileLinkedService**, spécifiez le service lié Azure Storage qui représente le stockage Azure qui contient les fichiers spécifiés dans la section filePaths.
9. Pour la propriété **arguments**, spécifiez les arguments de la tâche de diffusion en continu.
10. La propriété **getDebugInfo** est un élément facultatif. Si sa valeur est Failure, les journaux ne sont téléchargés qu’en cas d’échec. Si sa valeur est All, les journaux sont toujours téléchargés, quel que soit l’état de l’exécution.

> [AZURE.NOTE] Comme indiqué dans l’exemple, vous spécifiez un jeu de données de sortie pour l’activité de diffusion en continu Hadoop pour la propriété **outputs**. Il s’agit simplement d’un ensemble de données factice qui est nécessaire au fonctionnement de la planification de pipeline. Il est inutile de spécifier un jeu de données en entrée pour l’activité de la propriété **entrées**.

	
## Exemple
Le pipeline dans cette procédure pas à pas exécute le programme de diffusion en continu Map/Reduce de calcul du nombre de mots sur votre cluster Azure HDInsight.

### Services liés

#### Service lié Azure Storage
Tout d'abord, vous créez un service lié pour lier le stockage Azure qui est utilisé par le cluster Azure HDInsight à la fabrique de données Azure. Si vous copiez/collez le code suivant, n’oubliez pas de remplacer le nom de compte et la clé de compte par le nom et la clé de votre stockage Azure.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Service lié Azure HDInsight
Tout d'abord, vous créez un service lié pour lier le cluster Azure HDInsight à la fabrique de données Azure. Si vous copiez/collez le code suivant, remplacez le nom du cluster HDInsight par le nom de votre cluster HDInsight et modifiez le nom d’utilisateur et le mot de passe.
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Groupes de données

#### Jeu de données de sortie
Le pipeline de cet exemple n’accepte pas d’entrées. Vous spécifiez un jeu de données de sortie pour l’activité de diffusion en continu HDInsight. Il s’agit simplement d’un ensemble de données factice qui est nécessaire au fonctionnement de la planification de pipeline.

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Pipeline

Le pipeline de cet exemple n’a qu’une seule activité de type : **HDInsightStreaming**.

Le cluster HDInsight est automatiquement rempli avec les données (davinci.txt) et les exemples de programmes (wc.exe et cat.exe). Par défaut, le nom du conteneur utilisé par le cluster HDInsight est le nom du cluster lui-même. Par exemple, si votre nom de cluster est myhdicluster, le nom du conteneur d’objets blob associé est myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

## Voir aussi
- [Activité Hive](data-factory-hive-activity.md)
- [Activité pig](data-factory-pig-activity.md)
- [Activité MapReduce](data-factory-map-reduce.md)
- [Appeler des programmes Spark](data-factory-spark.md)
- [Appeler des scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0921_2016-->