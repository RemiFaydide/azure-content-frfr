<properties
   pageTitle="Utiliser le Kit de développement logiciel (SDK) .NET Data Lake Store pour développer des applications | Microsoft Azure"
   description="Utiliser le Kit de développement logiciel (SDK) .NET Azure Data Lake Store pour développer des applications"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/26/2016"
   ms.author="nitinme"/>

# Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser le [Kit de développement logiciel (SDK) .NET Azure Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx) pour effectuer des opérations de base comme créer des dossiers ou charger et télécharger des fichiers de données. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Store](data-lake-store-overview.md).

## Composants requis

* **Visual Studio 2013 ou 2015**. Les instructions ci-dessous utilisent Visual Studio 2015.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Store**. Pour savoir comment créer un compte, consultez [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Créez une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Store auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : **authentification de l’utilisateur final** ou **authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).

## Créer une application .NET

1. Ouvrez Visual Studio et créez une application console.

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

	| Propriété | Valeur |
	|----------|-----------------------------|
	| Catégorie | Modèles/Visual C#/Windows |
	| Modèle | Application console |
	| Nom | CreateADLApplication |

4. Cliquez sur **OK** pour créer le projet.

5. Ajoutez les packages Nuget à votre projet.

	1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
	2. Dans l’onglet **Gestionnaire de package Nuget**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
	3. Recherchez et installez les packages NuGet suivants :

		* `Microsoft.Azure.Management.DataLake.Store` - Ce didacticiel utilise v0.12.5-preview.
		* `Microsoft.Azure.Management.DataLake.StoreUploader` - Ce didacticiel utilise v0.10.6-preview.
		* `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Ce didacticiel utilise v2.2.8-preview.

		![Ajouter une source Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Créer un compte Azure Data Lake")

	4. Fermez le **Gestionnaire de package NuGet**.

6. Ouvrez **Program.cs**, supprimez le code existant, puis insérez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Déclarez les variables comme indiqué ci-dessous et indiquez les valeurs pour le nom du Data Lake Store et le nom du groupe de ressources qui existent déjà. En outre, assurez-vous que le chemin d'accès local et le nom de fichier que vous fournissez ici existent sur l'ordinateur. Ajoutez l'extrait de code suivant après les déclarations d'espace de noms.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
				private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
					_subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Dans les sections suivantes de cet article, vous pouvez découvrir comment utiliser les méthodes .NET pour effectuer des opérations telles que l’authentification des utilisateurs et le chargement de fichiers.

## Authentification

### Si vous utilisez l’authentification de l’utilisateur final

Utilisez cette option avec une application « cliente native » Azure AD existante ; vous trouverez une telle application ci-dessous.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Dans l’extrait de code ci-dessus, nous utilisons un domaine et un ID client Azure AD qui sont disponibles par défaut pour tous les abonnements Azure. Si vous souhaitez utiliser vos propres domaine et ID client d’application Azure AD, vous devez créer une application native Azure AD. Pour connaître la marche à suivre, consultez [Créer une application Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application).

>[AZURE.NOTE] Les instructions fournies dans les liens ci-dessus s’appliquent à une application web Azure AD. Cependant, la procédure est exactement la même si vous choisissez de créer une application cliente native à la place.

### Si vous utilisez l’authentification de service à service avec une clé secrète client 

Vous pouvez faire appel à l’extrait de code suivant pour authentifier votre application en mode non interactif, en utilisant la clé secrète du client pour une application/un principal du service. Utilisez-le avec une [« application web » Azure AD](../resource-group-create-service-principal-portal.md) existante.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### Si vous utilisez l’authentification de service à service avec un certificat

Ou vous pouvez l’utiliser pour authentifier votre application en mode non interactif, en utilisant le certificat pour une application / un principal de service. Utilisez-le avec une [« application web » Azure AD](../resource-group-create-service-principal-portal.md) existante.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## Créer des objets clients

L’extrait de code suivant crée le compte Data Lake Store et les objets clients filesystem, qui sont utilisés pour adresserdes demandes au service.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

	_adlsClient.SubscriptionId = _subId;

## Répertorier tous les comptes Data Lake Store d’un abonnement

L’extrait de code suivant répertorie tous les comptes Data Lake Store d’un abonnement Azure donné.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## Créer un répertoire

L’extrait de code suivant montre une méthode `CreateDirectory` que vous pouvez utiliser pour créer un répertoire dans un compte Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## Charger un fichier

L’extrait de code suivant montre une méthode `UploadFile` que vous pouvez utiliser pour charger des fichiers vers un compte Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader` prend en charge le chargement et le téléchargement récursifs entre un chemin d’accès local et un chemin d’accès Data Lake Store.

## Obtenir des informations sur un fichier ou répertoire

L’extrait de code suivant montre une méthode `GetItemInfo` que vous pouvez utiliser pour récupérer des informations sur un fichier ou un répertoire disponibles dans Data Lake Store.

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## Répertorier des fichiers ou répertoires

L’extrait de code suivant montre une méthode `ListItem` que vous pouvez utiliser pour répertorier les fichiers et répertoires dans un compte Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## Concaténation de fichiers

L’extrait de code suivant montre une méthode `ConcatenateFiles` qui vous permet de concaténer des fichiers.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## Ajout à un fichier

L’extrait de code suivant montre une méthode `AppendToFile` qui permet d’ajouter des données à un fichier déjà stocké dans un compte Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## Téléchargement d’un fichier

L’extrait de code suivant montre une méthode `DownloadFile` que vous pouvez utiliser pour télécharger un fichier depuis un compte Data Lake Store.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## Étapes suivantes

- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK Reference (Informations de référence sur le Kit de développement logiciel (SDK) .NET Azure Data Lake Store)](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST Reference (Informations de référence sur les API REST Data Lake Store)](https://msdn.microsoft.com/library/mt693424.aspx)

<!---HONumber=AcomDC_0928_2016-->