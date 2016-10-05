<properties 
	pageTitle="Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de Visual Studio | Microsoft Azure" 
	description="Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de Visual Studio." 
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
	ms.topic="get-started-article" 
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Visual Studio
> [AZURE.SELECTOR]
- [Vue d’ensemble et étapes préalables requises](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistant de copie](data-factory-copy-data-wizard-tutorial.md)

Ce didacticiel vous explique comment créer et surveiller une fabrique de données Azure à l’aide de Visual Studio. Le pipeline de la fabrique de données utilise une activité de copie pour copier les données d’un objet Stockage Blob Azure vers une base de données Azure SQL Database.

Voici les étapes à effectuer dans le cadre de ce didacticiel :

1. Créez deux services liés : **AzureStorageLinkedService1** et **AzureSqlinkedService1**.

	Le service AzureStorageLinkedService1 lie un espace de stockage Azure, et le service AzureSqlLinkedService1 lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactoryVS**. Les données d’entrée du pipeline se trouvent dans un conteneur d’objets blob situé dans le stockage d’objets blob Azure et les données de sortie sont stockées dans une table de la base de données SQL Azure. Par conséquent, vous ajoutez ces deux magasins de données en tant que services liés à la fabrique de données.
2. Créez deux jeux de données : **InputDataset** et **OutputDataset**, qui représentent les données d’entrée/sortie stockées dans les banques de données.

	Pour InputDataset, vous spécifiez le conteneur d’objets blob qui contient un objet blob avec les données source. Pour OutputDataset, vous spécifiez la table SQL qui stocke les données de sortie. Vous spécifiez également d’autres propriétés telles que la structure, la disponibilité et la stratégie.
3. Créez un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données ADFTutorialDataFactoryVS.

	Le pipeline assure une **activité de copie** qui copie les données d’entrée de l’objet blob Azure dans la table SQL Azure de sortie. L’activité de copie effectue le déplacement des données dans Azure Data Factory. Elle est mise en œuvre par un service disponible dans le monde entier, capable de copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).
4. Créez une fabrique de données nommée **VSTutorialFactory**. Déployez la fabrique de données et toutes les entités Data Factory (services liés, tables et pipeline).

## Composants requis

1. Lisez l’article [Vue d’ensemble du didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) et effectuez les **étapes préalables requises**.
2. Pour pouvoir publier des entités de fabrique de données dans Azure Data Factory, vous devez être un **administrateur de l’abonnement Azure**.
3. Les composants suivants doivent être installés sur votre ordinateur :
	- Visual Studio 2013 ou Visual Studio 2015
	- Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013 ou Visual Studio 2015. Accédez à la [page de téléchargement d’Azure](https://azure.microsoft.com/downloads/), puis cliquez sur **VS 2013** ou **VS 2015** dans la section **.NET**.
	- Téléchargez le dernier plug-in Azure Data Factory pour Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Vous pouvez également mettre à jour le plug-in en procédant comme suit : dans le menu, cliquez sur **Outils** -> **Extensions et mises à jour** -> **En ligne** -> **Galerie Visual Studio** -> **Outils Microsoft Azure Data Factory pour Visual Studio** -> **Mettre à jour**.

## Création d’un projet Visual Studio 
1. Lancez **Visual Studio 2013**. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. La boîte de dialogue **Nouveau projet** doit s’afficher.
2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez le modèle **DataFactory**, puis cliquez sur **Projet Data Factory vide**. Si le modèle DataFactory n’est pas affiché, fermez Visual Studio, installez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, puis rouvrez Visual Studio.

	![Boîte de dialogue Nouveau projet](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Entrez le **nom** du projet, son **emplacement** et le nom de la **solution**, puis cliquez sur **OK**.

	![Explorateur de solutions](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)

## Créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Pour connaître l’ensemble des sources et des récepteurs pris en charge par l’activité de copie, consultez [Banques de données et formats pris en charge](data-factory-data-movement-activities.md##supported-data-stores-and-formats). Pour obtenir la liste des services de calcul pris en charge par Data Factory, consultez [Services liés de calcul](data-factory-compute-linked-services.md). Dans ce didacticiel, aucun service de calcul n’est utilisé.

Dans cette étape, vous allez créer deux services liés : **AzureStorageLinkedService1** et **AzureSqlLinkedService1**. Le service AzureStorageLinkedService1 lie un compte de stockage Azure, et le service AzureSqlLinkedService lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**.

### Créer le service lié Azure Storage

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Service lié Azure Storage** dans la liste, puis cliquez sur **Ajouter**.

	![Nouveau service lié](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Remplacez `<accountname>` et `<accountkey>` par le nom de votre compte de stockage Azure et par sa clé.

	![Service lié Azure Storage](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Enregistrez le fichier **AzureStorageLinkedService1.json**.

> Pour plus d’informations sur les propriétés JSON, consultez [Déplacer des données vers et depuis un objet Blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### Créer le service lié SQL Azure

5. Cliquez de nouveau avec le bouton droit sur le nœud **Services liés** dans l'**Explorateur de solutions**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
6. Cette fois, sélectionnez **Service lié SQL Azure**, puis cliquez sur **Ajouter**.
7. Dans le **fichier AzureSqlLinkedService1.json**, remplacez `<servername>``<databasename>``<username@servername>``<password>` par le nom de votre serveur, de la base de données, du compte d’utilisateur SQL Azure et le mot de passe associé.
8.  Enregistrez le fichier **AzureSqlLinkedService1.json**.

> [AZURE.NOTE]
Pour plus d’informations sur les propriétés JSON, consultez [Déplacer des données vers et depuis SQL Azure Database](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).

## Créer des jeux de données
À l’étape précédente, vous avez créé les services liés **AzureStorageLinkedService1** et **AzureSqlLinkedService1** pour lier un compte de stockage Azure et une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**. Dans cette étape, vous définissez deux jeux de données, **InputDataset** et **OutputDataset**, qui représentent les données d’entrée/sortie stockées dans les banques de données référencées par AzureStorageLinkedService1 et AzureSqlLinkedService1, respectivement. Pour InputDataset, vous spécifiez le conteneur d’objets blob qui contient un objet blob avec les données source. Pour OutputDataset, vous spécifiez la table SQL qui stocke les données de sortie.

### Créer le jeu de données d’entrée
Dans cette étape, vous créez un jeu de données nommé **InputDataset** qui pointe vers un conteneur d’objets blob dans l’emplacement Azure Storage représenté par le service lié **AzureStorageLinkedService1**. Une table est un jeu de données rectangulaire. C’est le seul type de jeu de données pris en charge pour l’instant.

9. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
10. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Objet blob Azure**, puis cliquez sur **Ajouter**.
10. Remplacez le texte JSON par le texte suivant, puis enregistrez le fichier **AzureBlobLocation1.json**.

		{
		  "name": "InputDataset",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

     Notez les points suivants :
	
	- Le **type** de jeu de données est défini sur **AzureBlob**.
	- **linkedServiceName** est défini sur la valeur **AzureStorageLinkedService**. Vous avez créé ce service lié à l’étape 2.
	- **folderPath** est défini sur le conteneur **adftutorial**. Vous pouvez également spécifier le nom d’un objet blob dans le dossier à l’aide de la propriété **fileName**. Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.
	- Le **type** de format a la valeur **TextFormat**.
	- Le fichier texte contient deux champs, **FirstName** et **LastName**, séparés par une virgule (**columnDelimiter**).
	- L’élément **availability** est réglé sur **hourly** (**frequency** sur **hour** et **interval** sur **1**). Le service Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur d’objets blob (**adftutorial**) que vous avez spécifié.
	
	Si vous ne spécifiez pas de nom (**fileName**) pour un jeu de données **d’entrée**, tous les fichiers/objets blob du dossier d’entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée.
 
	Si vous ne spécifiez pas de nom (**fileName**) pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.&lt;Guid&gt;.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété **partitionedBy**. Dans l’exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2016-09-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2016/09/20, alors que la valeur de fileName est 08.csv.

			"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	        "fileName": "{Hour}.csv",
	        "partitionedBy": 
	        [
	        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 

> [AZURE.NOTE]
Pour plus d’informations sur les propriétés JSON, consultez [Déplacer des données vers et depuis un objet Blob Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).

### Créer un jeu de données de sortie
Dans cette étape, vous créez un jeu de données de sortie nommé **OutputDataset**. Ce jeu de données pointe vers une table SQL de la base de données SQL Azure représentée par **AzureSqlLinkedService1**.

11. À nouveau dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
12. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **SQL Azure**, puis cliquez sur **Ajouter**.
13. Remplacez le texte JSON par le texte JSON suivant, puis enregistrez le fichier **AzureSqlTableLocation1.json**.

		{
		  "name": "OutputDataset",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

     Notez les points suivants :
	
	- Le **type** de jeu de données est défini sur **AzureSQLTable**.
	- **linkedServiceName** est défini sur **AzureSqlLinkedService** (vous avez créé ce service lié à l’étape 2).
	- **tablename** est défini sur **emp**.
	- La table emp de la base de données contient trois colonnes : **ID**, **FirstName** et **LastName**. ID étant une colonne d’identité, il vous suffit de spécifier **FirstName** et **LastName**.
	- **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** est défini sur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Azure.

> [AZURE.NOTE]
Pour plus d’informations sur les propriétés JSON, consultez [Déplacer des données vers et depuis SQL Azure Database](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).

## Création d’un pipeline 
Jusqu’à présent, vous avez créé des services liés et des tables d’entrée/sortie. À présent, vous créez un pipeline avec une **activité de copie** pour copier les données de la base de données d’objets blob Azure dans la base de données SQL Azure.


1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Pipelines**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
15. Sélectionnez **Pipeline de copie de données** dans la boîte de dialogue **Ajouter un nouvel élément**, puis cliquez sur**Ajouter**.
16. Remplacez le texte JSON par le texte JSON suivant, puis enregistrez le fichier **CopyActivity1.json**.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "InputDataset"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputDataset"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

	Notez les points suivants :

	- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **Copy**.
	- L’entrée de l’activité est définie sur **InputDataset** et la sortie sur **OutputDataset**.
	- Dans la section **typeProperties**, **BlobSource** est spécifié en tant que type de source et **SqlSink** en tant que type de récepteur.

	Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end** par le jour suivant. Si vous le souhaitez, spécifiez uniquement la date et ignorez l'heure. Par exemple, « 2016-02-03 », qui équivaut à « 2016-02-03T00:00:00Z ».
	
	Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2016-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.
	
	Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour la propriété **end**.
	
	Dans l’exemple ci-dessus, il existe 24 tranches de données, car une tranche de données est générée toutes les heures.

## Publier/déployer des entités Data Factory
Dans cette étape, vous publiez les entités Data Factory (services liés, jeux de données et pipeline) que vous avez créées précédemment. Vous spécifiez également le nom de la fabrique de données à créer pour contenir ces entités.

18. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.
19. Si la boîte de dialogue **Connectez-vous à votre compte Microsoft** s'affiche, entrez vos informations d'identification pour le compte qui dispose de l'abonnement Azure, puis cliquez sur **Se connecter**.
20. La boîte de dialogue suivante doit s’afficher :

	![Boîte de dialogue Publier](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
21. Dans la page Configurer une fabrique de données, procédez comme suit :
	1. Sélectionnez l’option **Créer une fabrique de données**.
	2. Entrez **VSTutorialFactory** comme **Nom**.
	
		> [AZURE.IMPORTANT]  
		Le nom de la fabrique de données Azure doit être un nom global unique. Si vous obtenez une erreur concernant le nom de la fabrique de données pendant la publication, modifiez ce nom (par exemple, votrenomVSTutorialFactory) et relancez la publication. Consultez la rubrique [Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
	3. Sélectionnez votre abonnement Azure pour le champ **Abonnement**.
	 
		> [AZURE.IMPORTANT] Si vous ne voyez pas les abonnements, vérifiez que vous êtes connecté à l’aide d’un compte administrateur ou coadministrateur de l’abonnement.
	4. Sélectionnez le **groupe de ressources** pour la fabrique de données à créer.
	5. Sélectionnez la **région** pour la fabrique de données. Seules les régions prises en charge par le service Data Factory sont affichées dans la liste déroulante.
	6. Cliquez sur **Suivant** pour basculer vers la page **Publier des éléments**.
	
		![Page Configurer une fabrique de données](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)
23. Dans la page **Publier des éléments**, vérifiez que toutes les entités de fabriques de données sont sélectionnées, puis cliquez sur **Suivant** pour basculer vers la page **Résumé**.
	
	![Page Publier des éléments](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)
24. Passez en revue le résumé, puis cliquez sur **Suivant** pour démarrer le processus de déploiement et afficher l’**état du déploiement**.

	![Page Résumé de la publication](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
25. Dans la page **État du déploiement**, vous devez voir l’état du processus de déploiement. Une fois le déploiement terminé, cliquez sur Terminer. ![Page État du déploiement](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)Notez les points suivants :

- Si vous recevez le message d’erreur : « **L’abonnement n’est pas inscrit pour utiliser l’espace de noms Microsoft.DataFactory** », effectuez l’une des opérations suivantes et essayez de relancer la publication :

	- Dans Azure PowerShell, exécutez la commande suivante pour enregistrer le fournisseur Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Vous pouvez exécuter la commande suivante pour confirmer que le fournisseur Data Factory est bien enregistré.
	
			Get-AzureRmResourceProvider
	- Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de l’abonnement Azure et accédez à un panneau Data Factory (ou) créez une fabrique de données dans le portail Azure. Cette action enregistre automatiquement le fournisseur.
- 	Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.

> [AZURE.IMPORTANT] Pour créer des instances Data Factory, vous devez être administrateur ou co-administrateur de l’abonnement Azure.

## Résumé
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour copier des données d'objet blob Azure dans une base de données SQL Azure. Vous avez utilisé Visual Studio pour créer la fabrique de données, les services liés, les jeux de données et un pipeline. Voici les opérations globales que vous avez effectuées dans ce didacticiel :

1.	Création d’une **fabrique de données** Azure.
2.	Création de **services liés** :
	1. Un service lié **Stockage Azure** pour lier votre compte Stockage Azure contenant des données d’entrée.
	2. Un service lié **Azure SQL** pour lier votre base de données Azure SQL contenant les données de sortie.
3.	Création des **jeux de données** qui décrivent les données d’entrée et de sortie des pipelines.
4.	Création d’un **pipeline** avec une **activité de copie** avec **BlobSource** en tant que source et **SqlSink** en tant que récepteur.


## Utiliser l’Explorateur de serveurs pour passer en revue la fabrique des données

1. Dans **Visual Studio**, cliquez sur **Affichage** dans le menu, puis sur **Explorateur de serveurs**.
2. Dans la fenêtre Explorateur de serveurs, développez **Azure**, puis **Data Factory**. Si la boîte de dialogue **Connectez-vous à Visual Studio** s’affiche, entrez le **compte** associé à votre abonnement Azure, puis cliquez sur **Continuer**. Entrez le **mot de passe**, puis cliquez sur **Se connecter**. Visual Studio essaie d’obtenir des informations sur toutes les fabriques de données Azure contenues dans votre abonnement. L’état de cette opération s’affiche dans la fenêtre **Data Factory Task List** (Liste des tâches de Data Factory). ![Explorateur de serveurs](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Vous pouvez cliquer avec le bouton droit sur une fabrique de données et sélectionner Exporter la fabrique de données vers le nouveau projet pour créer un projet Visual Studio basé sur une fabrique de données existante. ![Exporter la fabrique de données vers un projet Visual Studio](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)

## Mettre à jour des outils Data Factory pour Visual Studio
Pour mettre à jour des outils Azure Data Factory pour Visual Studio, procédez comme suit :

1. Dans le menu, cliquez sur **Outils**, puis sélectionnez **Extensions et mises à jour**.
2. Dans le volet gauche, sélectionnez **Mises à jour**, puis **Galerie Visual Studio**.
4. Sélectionnez **Outils Azure Data Factory pour Visual Studio**, puis cliquez sur **Mettre à jour**. Si cette entrée n’est pas affichée, c’est que vous possédez déjà la dernière version de ces outils.

Consultez [Surveillance d’un pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) pour obtenir des instructions sur l’utilisation du portail Azure pour surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.

## Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Activités de déplacement des données](data-factory-data-movement-activities.md) | Cet article fournit une description détaillée de l’activité de copie que vous avez utilisée dans ce didacticiel. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory |
| [Groupes de données](data-factory-create-datasets.md) | Cet article vous aide à comprendre les jeux de données dans Azure Data Factory.
| [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) | Cet article décrit comment surveiller, gérer et déboguer les pipelines à l’aide de l’application de surveillance et gestion. 

<!---HONumber=AcomDC_0928_2016-->