<properties 
	pageTitle="Déplacer des données vers/depuis Oracle à l’aide de Data Factory | Microsoft Azure" 
	description="Découvrez comment déplacer des données vers et depuis une base de données Oracle locale à l’aide d’Azure Data Factory." 
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

# Déplacer des données vers/depuis Oracle en local à l’aide d’Azure Data Factory 

Cet article explique comment utiliser l’activité de copie Data factory pour déplacer des données vers/depuis Oracle depuis/vers un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

## Installation 
Pour permettre au service Azure Data Factory de se connecter à votre base de données Oracle locale, vous devez installer ce qui suit :

- Une passerelle de gestion de données sur l’ordinateur qui héberge la base de données ou sur un autre ordinateur afin d’éviter toute mise en concurrence avec la base de données pour les ressources. La passerelle de gestion de données est un agent client qui connecte des sources de données locales à des services cloud de manière gérée et sécurisée. Pour plus d’informations sur la passerelle de gestion de données, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md).
- Fournisseur de données Oracle pour .NET. Ce composant est inclus dans [Oracle Data Access Components for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installez la version appropriée (32/64 bits) sur l’ordinateur hôte sur lequel la passerelle est installée. [Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) peut accéder à Oracle Database 10g Release 2 ou version ultérieure.

	Si vous choisissez « Installation XCopy », suivez les étapes dans le fichier readme.htm. Nous vous recommandons de choisir le programme d’installation avec interface utilisateur (et pas le programme d’installation XCopy).
 
	Après avoir installé le fournisseur, redémarrez le service hôte de la passerelle de gestion des données sur votre ordinateur à l’aide de l’applet Services (ou) du Gestionnaire de configuration de la passerelle de gestion des données.

> [AZURE.NOTE] Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.

## Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie des données entre une base de données Oracle et n’importe quel magasin de données récepteur pris en charge consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données entre une base de données Oracle et Azure Blob Storage. Toutefois, les données peuvent être copiées vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie d’Azure Data Factory.

## Exemple : copie de données d’Oracle vers Azure Blob
Cet exemple indique comment copier des données à partir d’une base de données Oracle locale vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie d’Azure Data Factory.
 
L’exemple contient les entités de fabrique de données suivantes :

1.	Un service lié de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) comme source et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) comme récepteur.

L’exemple copie toutes les heures les données d’une table d’une base de données Oracle locale vers un objet blob. Pour plus d’informations sur les diverses propriétés utilisées dans l’exemple, consultez la documentation dans les sections qui suivent les exemples.

**Service lié Oracle :**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Service lié Azure Blob Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Jeu de données d’entrée Oracle :**

L'exemple suppose que vous avez créé une table « MyTable » dans Oracle et qu'elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.

La définition de « external » : « true» informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
	        },
	      "policy": {     
	           "externalData": {        
	                "retryInterval": "00:01:00",    
	                "retryTimeout": "00:10:00",       
	                "maximumRetry": 3       
	            }     
	          }
	    }
	}


**Jeu de données de sortie Azure Blob :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie et planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **OracleSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **oracleReaderQuery** sélectionne les données de la dernière heure à copier.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OracletoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}


Vous devez ajuster la chaîne de requête en fonction de la façon dont les dates sont configurées dans votre base de données Oracle. Si le message d’erreur suivant s’affiche :

	Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

vous devrez peut-être modifier la requête, comme indiqué dans l’exemple ci-dessous (à l’aide de la fonction to\_date) :

	"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## Exemple : copie de données à partir d’un objet Blob Azure vers Oracle
Cet exemple indique comment copier des données d’un stockage d’objets blob Azure vers une base de données Oracle locale. Toutefois, vous pouvez copier les données **directement** à partir des sources indiquées [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie de Microsoft Azure Data Factory.
 
L’exemple contient les entités de fabrique de données suivantes :

1.	Un service lié de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	un [jeu de données](data-factory-create-datasets.md) d'entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) ;
4.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
5.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) comme source et [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) comme récepteur.

L’exemple copie chaque heure les données d’une objet blob vers une table d’une base de données Oracle locale. Pour plus d’informations sur les diverses propriétés utilisées dans l’exemple, consultez la documentation dans les sections qui suivent les exemples.

**Service lié Oracle :**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Service lié Azure Blob Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Jeu de données d'entrée d'objet Blob Azure**

Les données sont récupérées à partir d’un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois et le jour de début et le nom de fichier utilise l’heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la fabrique de données et n’est pas produite par une activité dans la fabrique de données.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Jeu de données de sortie Oracle :**

L’exemple suppose que vous avez créé une table « MyTable » dans Oracle. Créez la table dans Oracle avec le même nombre de colonnes que le fichier CSV d’objets blob doit en contenir. De nouvelles lignes sont ajoutées à la table toutes les heures.

	{
	    "name": "OracleOutput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": "1"
	        }
	    }
	}


**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **OracleSink**.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoOracle",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OracleOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "OracleSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}


## Propriétés du service lié Oracle

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Oracle.

Propriété | Description | Requis
-------- | ----------- | --------
type | Le type de propriété doit être défini sur : **OnPremisesOracle** | Oui
connectionString | Spécifier les informations requises pour la connexion à l’instance de base de données Oracle pour la propriété connectionString. | Oui 
gatewayName | Nom de la passerelle utilisée pour se connecter au serveur Oracle local | Oui

Pour plus d’informations sur la définition des informations d’identification pour une source de données Oracle locale, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)
## Propriétés de type du jeu de données Oracle

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections telles que la structure, la disponibilité et la stratégie d’un jeu de données JSON sont similaires pour tous les types de jeux de données (Oracle, objet blob Azure, table Azure, etc.).
 
La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type OracleTable a les propriétés suivantes :

Propriété | Description | Requis
-------- | ----------- | --------
TableName | Nom de la table dans la base de données Oracle à laquelle le service lié fait référence. | Non (si **oracleReaderQuery** de **OracleSource** est spécifié)

## Propriétés de type de l’activité de copie Oracle

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et la stratégie sont disponibles pour tous les types d’activités.

> [AZURE.NOTE]
L'activité de copie accepte uniquement une entrée et produit une seule sortie.

En revanche, les propriétés disponibles dans la section typeProperties de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

### OracleSource
Dans le cas d’une activité de copie, lorsque la source est de type **OracleSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

Propriété | Description |Valeurs autorisées | Requis
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : select * from MyTable <br/><br/>Si non spécifié, l’instruction SQL exécutée : select * from MyTable | Non (si **tableName** de **dataset** est spécifiée)

### OracleSink
**OracleSink** prend en charge les propriétés suivantes :

Propriété | Description | Valeurs autorisées | Requis
-------- | ----------- | -------------- | --------
writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. | intervalle de temps<br/><br/> Exemple : « 00:30:00 » (30 minutes). | Non
writeBatchSize | Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize | Nombre entier (nombre de lignes)| Non (valeur par défaut : 10000)  
sqlWriterCleanupScript | Spécifiez une requête pour exécuter l’activité de copie afin que les données d’un segment spécifique soient nettoyées. | Une instruction de requête. | Non
sliceIdentifierColumnName | Spécifiez le nom de la colonne que l’activité de copie doit remplir avec l’identificateur de segment généré automatiquement, et qui est utilisée pour nettoyer les données d’un segment spécifique lors de la réexécution. | Nom d’une colonne avec le type de données binary(32). | Non


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mappage de type pour Oracle

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement les types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données à partir d’Oracle, les mappages suivants sont utilisés pour convertir le type de données Oracle en type .NET et vice versa.

Type de données Oracle | Type de données .NET Framework.
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Décimal
INTEGER | Décimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Décimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String

## Conseils de dépannage

**Problème:** le **message d’erreur** suivant s’affiche : L’activité de copie a rencontré des paramètres non valides : « UnknownParameterName ». Message détaillé : Le fournisseur de données .Net Framework demandé est introuvable. Il n’est peut-être pas installé.

**Causes possibles :**

1. Le fournisseur de données .NET Framework pour Oracle n’a pas été installé.
2. Le fournisseur de données .NET Framework pour Oracle a été installé pour .NET Framework 2.0 et est introuvable dans les dossiers de .NET Framework 4.0.

**Résolution/solution de contournement :**

1. Si vous n’avez pas installé le fournisseur .NET pour Oracle, [installez-le](http://www.oracle.com/technetwork/topics/dotnet/downloads/), puis réessayez.
2. Si vous obtenez le message d’erreur même après l’installation du fournisseur, procédez comme suit :
	1. Ouvrez le fichier machine.config de .NET 2.0 à partir du dossier : <disque système>:\\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
	2. Recherchez **Fournisseur de données Oracle pour .NET**, et vous devriez être en mesure de trouver une entrée semblable à l’exemple suivant sous **system.data** -> **DbProviderFactories**: “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.	Copiez cette entrée dans le fichier machine.config dans le dossier v4.0 suivant : <disque système>:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config, et remplacez la version par 4.xxx.x.x.
3.	Installez « <Chemin d’installation d’ODP.NET>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll » dans le Global Assembly Cache (GAC) en exécutant `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!---HONumber=AcomDC_0921_2016-->