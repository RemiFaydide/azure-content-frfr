<properties 
   pageTitle="Didacticiel : prise en main d’Analytique Data Lake Azure à l’aide du kit de développement logiciel .NET | Azure" 
   description="Apprenez à utiliser le kit de développement .NET pour créer les comptes Data Lake Store, créez des travaux Analytique Data Lake et envoyez des travaux rédigés en U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="edmaca"/>

# Didacticiel : prise en main d’Analytique Data Lake Azure à l’aide du kit de développement logiciel .NET

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Découvrez comment utiliser le Kit de développement logiciel (SDK) .NET Azure pour soumettre des tâches écrites en [U-SQL](data-lake-analytics-u-sql-get-started.md) à Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer une application de console C# pour soumettre une tâche U-SQL qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertit en fichier CSV (valeurs séparées par des virgules). Pour suivre ce didacticiel même à l’aide d’autres outils pris en charge, cliquez sur les onglets en haut de cet article.

##Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C+**.
- **Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5 ou ultérieure**. Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **Un compte Azure Data Lake Analytics**. Consultez [Manage Data Lake Analytics using Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) (Gérer Data Lake Analytics à l’aide du Kit de développement logiciel (SDK) Azure .NET).

##Création d’une application de console

Dans ce didacticiel, vous allez traiter des journaux de recherche. Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Vous trouverez un exemple de journal de recherche dans un conteneur d’objets blobs Azure public. Dans l’application, vous allez télécharger le fichier sur votre station de travail, puis charger le fichier dans le compte Data Lake Store par défaut de votre compte Data Lake Analytics.

**Pour créer une application**

1. Ouvrez Visual Studio.
2. Création d’une application console C#.
3. Ouvrez la console de gestion des packages Nuget, puis exécutez les commandes suivantes :

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage

4. Ajoutez un nouveau fichier au projet nommé **SampleUSQLScript.txt**, puis collez le script U-SQL suivant. Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**.
    
    Dans le programme C#, vous devez préparer le dossier **/Samples/Data/SearchLog.tsv** fichier et le **/Output/**.
	
	Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus. Par exemple :
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés. La syntaxe des fichiers stockés dans le compte de stockage Azure lié est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

	>[AZURE.NOTE] Il existe actuellement un problème connu avec le service Azure Data Lake. Si l’exemple d’application est interrompue ou rencontre une erreur, vous devrez peut-être supprimer manuellement les comptes Data Lake Store & Data Lake Analytics créés par le script. Si vous n’êtes pas familiarisé avec le portail, le guide [Gérer les analyses Azure Data Lake à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md) vous aidera à démarrer.
       
5. Dans Program.cs, collez le code suivant :

		using System;
		using System.IO;
		using System.Collections.Generic;
		using System.Threading;
		using Microsoft.Rest;
		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.WindowsAzure.Storage.Blob;

		namespace SdkSample
		{
		  class Program
		  {
			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
			private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;
			private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
			private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		
		    private static void Main(string[] args)
		    {
				string localFolderPath = @"c:\temp";

				// Connect to Azure
				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				SetupClients(creds, SUBSCRIPTIONID);

				// Transfer the source file from a public Azure Blob container to Data Lake Store.
				CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
				blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
				UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
				WaitForNewline("Source data file prepared.", "Submitting a job.");


				// Submit the job
				Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
				WaitForNewline("Job submitted.", "Waiting for job completion.");

				// Wait for job completion
				WaitForJob(jobId);
				WaitForNewline("Job completed.", "Downloading job output.");

				// Download job output
				DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
		
		      	WaitForNewline("Job output downloaded. You can now exit.");
		    }
		
			public static ServiceClientCredentials AuthenticateAzure(
				string domainName,
				string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
			{
				_adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
				_adlaClient.SubscriptionId = subscriptionId;

				_adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

				_adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
			}

			public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
			{
				var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
				var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
				var uploader = new DataLakeStoreUploader(parameters, frontend);
				uploader.Execute();
			}

			public static void DownloadFile(string srcPath, string destPath)
			{
				var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
				var fileStream = new FileStream(destPath, FileMode.Create);

				stream.CopyTo(fileStream);
				fileStream.Close();
				stream.Close();
			}

			// Helper function to show status and wait for user input
			public static void WaitForNewline(string reason, string nextAction = "")
			{
				Console.WriteLine(reason + "\r\nPress ENTER to continue...");

				Console.ReadLine();

				if (!String.IsNullOrWhiteSpace(nextAction))
					Console.WriteLine(nextAction);
			}


			// List all Data Lake Analytics accounts within the subscription
			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
				for (int i = 0; i < accounts.Count; i++)
				{
					Console.WriteLine(accounts[i].Name);
				}

				return accounts;
			}
			public static Guid SubmitJobByPath(string scriptPath, string jobName)
			{
				var script = File.ReadAllText(scriptPath);

				var jobId = Guid.NewGuid();
				var properties = new USqlJobProperties(script);
				var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
				var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

				return jobId;
			}

			public static JobResult WaitForJob(Guid jobId)
			{
				var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				while (jobInfo.State != JobState.Ended)
				{
					jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				}
				return jobInfo.Result.Value;
			}
		  }
		}

6. Appuyez sur **F5** pour exécuter l'application. La sortie se présente comme suit :

	![Sortie de la tâche Azure Data Lake Analytics dans le SDK .NET U-SQL](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. Vérifiez le fichier de sortie. Le chemin et le nom de fichier par défaut sont c:\\Temp\\SearchLog-from-Data-Lake.csv.

## Voir aussi

- Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
- Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec les outils Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour découvrir U-SQL, consultez les articles [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) et [Référence sur le langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de l’analyse de données Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0928_2016-->