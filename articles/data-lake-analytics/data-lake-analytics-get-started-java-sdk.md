<properties
   pageTitle="Utiliser le SDK Java Data Lake Analytics pour développer des applications | Azure"
   description="Utiliser le SDK Java Azure Data Lake Analytics pour développer des applications"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Didacticiel : prise en main d’Azure Data Lake Analytics en utilisant le Kit de développement logiciel (SDK) .NET

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Apprenez à utiliser le Kit de développement logiciel (SDK) Java Azure Data Lake Analytics pour créer un compte Azure Data Lake et effectuer des opérations de base comme créer des dossiers, charger et télécharger des fichiers de données, supprimer votre compte et effectuer des travaux. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Analytics](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer une application de console Java qui contient des exemples de tâches d’administration courantes. Vous créerez ensuite des données de test et soumettrez un travail. Pour suivre ce didacticiel même à l'aide d'autres outils pris en charge, cliquez sur les onglets en haut de cette section.

## Composants requis

* Kit de développement Java (JDK) 8 (avec Java version 1.8).
* IntelliJ ou un autre environnement de développement Java approprié. Ceci étape est facultatif mais recommandé. Les instructions ci-dessous utilisent IntelliJ.
* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Activation de votre abonnement Azure** pour la version préliminaire publique de Data Lake Analytics. Consultez les [instructions](data-lake-analytics-get-started-portal.md#signup).
* Création d’une application Azure Active Directory (AAD) et récupération de ses **ID client**, **ID de locataire** et **Clé**. Pour plus d’informations sur les applications AAD et pour savoir comment obtenir un ID client, consultez [Création de l’application Active Directory et du principal du service à l’aide du portail](../resource-group-create-service-principal-portal.md). L’URI de réponse et la Clé seront également disponibles sur le portail une fois l’application créée et la clé générée.

## Comment s’authentifier à l’aide d’Azure Active Directory ?

L’extrait de code ci-dessous fournit le code pour une authentification **non interactive**, où l’application fournit ses propres informations d’identification.

Vous devrez donner à votre application l’autorisation de créer des ressources dans Azure pour que ce didacticiel fonctionne. Dans le cadre de ce didacticiel, il est **recommandé** de n’accorder à cette application que des autorisations de type Collaborateur pour un nouveau groupe de ressources, inutilisé et vide, dans votre abonnement Azure.

## Création d’une application Java

1. Ouvrez IntelliJ et créez un nouveau projet Java à l’aide du modèle **Application de ligne de commande**.

2. Cliquez avec le bouton droit sur le projet sur le côté gauche de l’écran et cliquez sur **Ajouter la prise en charge Framework**. Choisissez **Maven** et cliquez sur **OK**.

3. Ouvrez le fichier **« pom.xml »** nouvellement créé et ajoutez l’extrait de texte suivant entre la balise **</version>** et la balise **</project>** :

    REMARQUE : cette étape est provisoire tant que le Kit de développement logiciel (SDK) Azure Data Lake Analytics n’est pas disponible dans Maven. Cet article sera mis à jour une fois le Kit de développement logiciel (SDK) disponible dans Maven. Toutes les futures mises à jour ce Kit de développement logiciel (SDK) seront disponibles via Maven.

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-20160513.000802-24</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-runtime</artifactId>
	            <version>1.0.0-20160513.000812-28</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.rest</groupId>
	            <artifactId>client-runtime</artifactId>
	            <version>1.0.0-20160513.000825-29</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-analytics</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. Accédez à **Fichier**, puis choisissez **Paramètres**, **Build**, **Exécution** et **Déploiement**. Sélectionnez **Outils de génération**, **Maven**, **Importation**. Cochez ensuite **Importer les projets Maven automatiquement**.

5. Ouvrez **Main.java** et remplacez le bloc de code existant par le code suivant. De même, indiquez les valeurs des paramètres inclus dans l’extrait de code, tels que **localFolderPath**, **\_adlaAccountName**, **\_adlsAccountName**, **\_resourceGroupName** et remplacez les espaces réservés pour **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** et **SUBSCRIPTION-ID**.

	Ce code mène à bien l’ensemble du processus, à savoir : création de comptes Data Lake Store et Data Lake Analytics, création de fichiers dans le magasin, exécution d’un travail, obtention de l’état du travail, téléchargement de la sortie du travail et enfin suppression du compte.

	>[AZURE.NOTE] Il existe actuellement un problème connu avec le service Azure Data Lake. Si l’exemple d’application est interrompue ou rencontre une erreur, vous devrez peut-être supprimer manuellement les comptes Data Lake Store & Data Lake Analytics créés par le script. Si vous n’êtes pas familiarisé avec le portail, le guide [Gérer les analyses Azure Data Lake à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md) vous aidera à démarrer.


		package com.company;

		import com.microsoft.azure.CloudException;
		import com.microsoft.azure.credentials.ApplicationTokenCredentials;
		import com.microsoft.azure.management.datalake.store.*;
		import com.microsoft.azure.management.datalake.store.models.*;
		import com.microsoft.azure.management.datalake.analytics.*;
		import com.microsoft.azure.management.datalake.analytics.models.*;
		import com.microsoft.rest.credentials.ServiceClientCredentials;
		import java.io.*;
		import java.nio.charset.Charset;
		import java.nio.file.Files;
		import java.nio.file.Paths;
		import java.util.ArrayList;
		import java.util.UUID;
		import java.util.List;
		
		public class Main {
		    private static String _adlsAccountName;
		    private static String _adlaAccountName;
		    private static String _resourceGroupName;
		    private static String _location;
		
		    private static String _tenantId;
		    private static String _subId;
		    private static String _clientId;
		    private static String _clientSecret;
		
		    private static DataLakeStoreAccountManagementClient _adlsClient;
		    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
		    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
		    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
		
		    public static void main(String[] args) throws Exception {
		        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
		        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
		        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
		        _location = "East US 2";
		
		        _tenantId = "<TENANT-ID>";
		        _subId =  "<SUBSCRIPTION-ID>";
		        _clientId = "<CLIENT-ID>";
		
		        _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
		
		        String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
		
		        // Authenticate
		        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
		        SetupClients(creds);
		
		        // Create Data Lake Store and Analytics accounts
		        WaitForNewline("Authenticated.", "Creating NEW accounts.");
		        CreateAccounts();
		        WaitForNewline("Accounts created.", "Displaying accounts.");
		
		        // List Data Lake Store and Analytics accounts that this app can access
		        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
		        List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
		        for (DataLakeStoreAccount acct : adlsListResult) {
		            System.out.println(acct.getName());
		        }
		        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
		        List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
		        for (DataLakeAnalyticsAccount acct : adlaListResult) {
		            System.out.println(acct.getName());
		        }
		        WaitForNewline("Accounts displayed.", "Creating files.");
		
		        // Create a file in Data Lake Store: input1.csv
		        // TODO: these change order in the next patch
		        byte[] bytesContents = "123,abc".getBytes();
		        _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);
		
		        WaitForNewline("File created.", "Submitting a job.");
		
		        // Submit a job to Data Lake Analytics
		        UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM "/input1.csv" USING Extractors.Csv(); OUTPUT @input TO @"/output1.csv" USING Outputters.Csv();", "testJob");
		        WaitForNewline("Job submitted.", "Getting job status.");
		
		        // Wait for job completion and output job status
		        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
		        System.out.println("Waiting for job completion.");
		        WaitForJob(jobId);
		        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
		        WaitForNewline("Job completed.", "Downloading job output.");
		
		        // Download job output from Data Lake Store
		        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
		        WaitForNewline("Job output downloaded.", "Deleting file.");
		
		        // Delete file from Data Lake Store
		        DeleteFile("/output1.csv");
		        WaitForNewline("File deleted.", "Deleting account.");
		
		        // Delete account
		        _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
		        _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
		        WaitForNewline("Account deleted.", "DONE.");
		    }
		
		    //Set up clients
		    public static void SetupClients(ServiceClientCredentials creds)
		    {
		        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
		        _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
		        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
		        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
		        _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
		        _adlsClient.setSubscriptionId(_subId);
		        _adlaClient.setSubscriptionId(_subId);
		    }
		
		    // Helper function to show status and wait for user input
		    public static void WaitForNewline(String reason, String nextAction)
		    {
		        if (nextAction == null)
		            nextAction = "";
		
		        System.out.println(reason + "\r\nPress ENTER to continue...");
		        try{System.in.read();}
		        catch(Exception e){}
		
		        if (!nextAction.isEmpty())
		        {
		            System.out.println(nextAction);
		        }
		    }
		
		    // Create Data Lake Store and Analytics accounts
		    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
		        // Create ADLS account
		        DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
		        adlsParameters.setLocation(_location);
		
		        _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
		
		        // Create ADLA account
		        DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
		        adlsInfo.setName(_adlsAccountName);
		
		        DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
		        adlsInfo.setProperties(adlsInfoProperties);
		
		        List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
		        adlsInfoList.add(adlsInfo);
		
		        DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
		        adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
		        adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);
		
		        DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
		        adlaParameters.setLocation(_location);
		        adlaParameters.setName(_adlaAccountName);
		        adlaParameters.setProperties(adlaProperties);
		
		            /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */
		
		        _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
		    }
		
		    //todo: this changes in the next version of the API
		    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
		        byte[] bytesContents = contents.getBytes();
		
		        _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
		    }
		
		    public static void DeleteFile(String filePath) throws IOException, CloudException {
		        _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
		    }
		
		    // Download file
		    public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
		        InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();
		
		        PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
		
		        String fileContents = "";
		        if (stream != null) {
		            Writer writer = new StringWriter();
		
		            char[] buffer = new char[1024];
		            try {
		                Reader reader = new BufferedReader(
		                        new InputStreamReader(stream, "UTF-8"));
		                int n;
		                while ((n = reader.read(buffer)) != -1) {
		                    writer.write(buffer, 0, n);
		                }
		            } finally {
		                stream.close();
		            }
		            fileContents =  writer.toString();
		        }
		
		        pWriter.println(fileContents);
		        pWriter.close();
		    }
		
		    // Submit a U-SQL job by providing script contents.
		    // Returns the job ID
		    public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
		        UUID jobId = java.util.UUID.randomUUID();
		        USqlJobProperties properties = new USqlJobProperties();
		        properties.setScript(script);
		        JobInformation parameters = new JobInformation();
		        parameters.setName(jobName);
		        parameters.setJobId(jobId);
		        parameters.setType(JobType.USQL);
		        parameters.setProperties(properties);
		
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();
		
		        return jobId;
		    }
		
		    // Wait for job completion
		    public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
		        while (jobInfo.getState() != JobState.ENDED)
		        {
		            jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
		        }
		        return jobInfo.getResult();
		    }
		
		    // Get job status
		    public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
		        return jobInfo.getState().toValue();
		    }
		}

6. Suivez les invites pour exécuter et terminer l’application.


## Voir aussi

- Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
- Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec les outils Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour découvrir U-SQL, consultez les articles [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) et [Référence sur le langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de l’analyse de données Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0921_2016-->