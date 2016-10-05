<properties
	pageTitle="Prise en main du stockage d’objets blob Azure à l’aide de .NET | Microsoft Azure"
	description="Stockez des données non structurées dans le cloud avec Azure Blob Storage (stockage d’objets)."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/20/2016"
	ms.author="jwillis;tamram"/>


# Prise en main du stockage d’objets blob Azure à l’aide de .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Vue d'ensemble

Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets/blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Blob Storage.

### À propos de ce didacticiel

Ce didacticiel montre comment écrire du code .NET pour des scénarios courants d’utilisation du stockage d’objets blob Azure. Les scénarios traités incluent le chargement, la création de listes, le téléchargement et la suppression d’objets blob.

**Durée estimée :** 45 minutes

**Configuration requise :**

- [Microsoft Visual Studio](https://www.visualstudio.com/fr-FR/visual-studio-homepage-vs.aspx)
- [Bibliothèque cliente Azure Storage pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestionnaire de configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### Autres exemples

Pour obtenir des exemples supplémentaires utilisant Blob Storage, voir [Prise en main d’Azure Blob Storage dans .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Vous pouvez télécharger l’exemple d’application et l’exécuter ou parcourir le code sur GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### Ajout de déclarations d'espaces de noms

Ajoutez les instructions `using` suivantes au début du fichier `program.cs` :

	using Microsoft.Azure; // Namespace for CloudConfigurationManager
	using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### Analyse de la chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### Créer le client du service Blob

La classe **CloudBlobClient** vous permet de récupérer des conteneurs et des objets blob stockés dans Blob Storage. Voici un moyen de créer le client du service :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Vous êtes maintenant prêt à écrire du code qui lit et écrit des données dans le Blob Storage.

## Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Cet exemple montre comment créer un conteneur, si celui-ci n’existe pas encore :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Le nouveau conteneur est privé par défaut, ce qui signifie que vous devez indiquer votre clé d’accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Tous les utilisateurs d’Internet peuvent afficher des objets blob d’un conteneur public, mais vous ne pouvez les modifier ou les supprimer que si vous disposez de la clé d’accès du compte ou de la signature d'accès partagé adéquate.

## Charger un objet blob dans un conteneur

Le service de stockage d’objets blob Azure prend en charge les objets blob de blocs et de page. Dans la plupart des cas, il est recommandé d’utiliser le type d’objet blob de blocs.

Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d’objet blob de blocs. Lorsque vous disposez d’une référence d’objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream**. Si l’objet blob n’existe pas, cette opération entraîne sa création. S’il existe, il est remplacé.

L’exemple suivant illustre le chargement d’un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Création d’une liste d’objets blob dans un conteneur

Pour créer une liste d’objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **ListBlobs** du conteneur pour récupérer les objets blob et/ou les répertoires qu’il contient. Pour accéder aux nombreuses propriétés et méthodes d’une **IListBlobItem** renvoyée, vous devez l’appeler vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l’appel. Le code suivant illustre la récupération et la génération de l’URI de chaque élément du conteneur `photos` :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;

			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Comme indiqué ci-dessus, vous pouvez nommer les objets blob avec des informations de chemin d’accès dans leurs noms. Vous obtenez alors une structure de répertoires virtuels que vous pouvez organiser et parcourir de la même manière qu’un système de fichiers traditionnel. Notez que la structure de répertoires est uniquement virtuelle : les seules ressources disponibles dans le stockage d’objets blob sont des conteneurs et des objets blob. Toutefois, la bibliothèque cliente de stockage fournit un objet **CloudBlobDirectory** pour faire référence à un répertoire virtuel et simplifier le processus d’utilisation des objets blob organisés de cette façon.

Par exemple, prenez l’ensemble d’objets blob de blocs suivant, situé dans un conteneur nommé `photos` :

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quand vous appelez **ListBlobs** pour le conteneur « photos » (comme dans l’exemple ci-dessus), une liste hiérarchique est retournée. Celle-ci contient à la fois des objets **CloudBlobDirectory** et **CloudBlockBlob** représentant respectivement les répertoires et les objets blob du conteneur. La sortie obtenue ressemble à ceci :

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Vous pouvez également affecter au paramètre **UseFlatBlobListing** de la méthode **ListBlobs** la valeur **true**. Dans ce cas, chaque objet blob dans le conteneur est retourné en tant qu’objet **CloudBlockBlob**. L’appel à **ListBlobs** pour retourner une liste plate ressemble à ceci :

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

Les résultats se présentent comme suit :

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Télécharger des objets blob

Pour télécharger des objets blob, commencez par récupérer une référence d’objet blob, puis appelez la méthode **DownloadToStream**. L’exemple suivant utilise la méthode **DownloadToStream** pour transférer les contenus d’objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger les contenus d’un objet blob en tant que chaîne de texte.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Suppression d’objets blob

Pour supprimer un objet blob, commencez par obtenir une référence d’objet blob, puis appelez la méthode **Delete** associée.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Création d’une liste d’objets blob dans des pages de manière asynchrone

Si vous répertoriez un grand nombre d’objets blob ou que vous souhaitez contrôler le nombre de résultats renvoyés lors d’une opération de création de liste, vous pouvez répertorier les objets blob dans des pages de résultats. Cet exemple montre comment renvoyer les résultats dans des pages de manière asynchrone afin que l’exécution ne soit pas bloquée lorsqu’un ensemble volumineux de résultats est attendu.

Cet exemple montre une liste plate d’objets blob. Vous pouvez également obtenir une liste hiérarchique en affectant au paramètre `useFlatBlobListing` de la méthode **ListBlobsSegmentedAsync** la valeur `false`.

Comme l’exemple de méthode appelle une méthode asynchrone, il doit être précédé du mot clé `async` et doit retourner un objet **Task**. Le mot clé d’attente spécifié pour la méthode **ListBlobsSegmentedAsync** suspend l’exécution de l’exemple de méthode jusqu’à ce que la tâche de création de liste soit terminée.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Écriture dans un objet blob d’ajout

Un objet blob d’ajout est un nouveau type d’objet blob, introduit avec la version 5.x de la bibliothèque cliente de stockage Microsoft Azure pour .NET. Il est optimisé pour les opérations d’ajout, telles que la journalisation. Comme un objet blob de blocs, un objet blob d’ajout est composé de blocs. Mais lorsqu’il est ajouté à un objet blob d’ajout, un nouveau bloc l’est toujours à la fin. Vous ne pouvez pas mettre à jour ou supprimer un bloc dans un objet blob d’ajout. Les ID de bloc dans un objet blob d’ajout ne sont pas visibles, comme pour un objet blob de blocs.

Chaque bloc d’un objet blob d’ajout peut avoir une taille différente (jusqu’à 4 Mo), et un objet blob d’ajout peut contenir au maximum 50 000 blocs. La taille maximale d’un objet blob d’ajout est donc légèrement supérieure à 195 Go (4 Mo x 50 000 blocs).

L’exemple ci-dessous crée un objet blob d’ajout et y ajoute des données pour simuler une opération de journalisation simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Pour plus d’informations sur les différences entre les trois types d’objets blob, consultez la page [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](https://msdn.microsoft.com/library/azure/ee691964.aspx).

## Gestion de la sécurité pour les objets blob

Par défaut, Azure Storage préserve la sécurité de vos données en limitant l’accès au propriétaire du compte, qui est en possession des clés d’accès au compte. Lorsque vous avez besoin de partager des données d’objets blob de votre compte de stockage, il est important de le faire sans compromettre la sécurité de vos clés d’accès au compte. En outre, vous pouvez chiffrer les données d’objets blob pour vous assurer qu’elles sont sécurisées lors de leur transfert et dans Azure Storage.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### Contrôle de l’accès aux données d’objets blob

Par défaut, les données d’objets blob de votre compte de stockage sont accessibles uniquement par le propriétaire du compte de stockage. L’authentification des demandes vis-à-vis du stockage d’objets blob requiert la clé d’accès par défaut. Toutefois, vous pouvez rendre certaines données d’objets blob disponibles pour d’autres utilisateurs. Deux options s'offrent à vous :

- **Accès anonyme :** vous pouvez rendre un conteneur ou ses objets blob disponibles publiquement pour un accès anonyme. Pour plus d'informations, consultez [Gestion de l'accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md).
- **Signatures d’accès partagé :** vous pouvez fournir aux clients une signature d’accès partagé (SAP), qui fournit un accès délégué à une ressource de votre compte de stockage, avec des autorisations et sur un intervalle que vous spécifiez. Pour plus d’informations, consultez [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).

### Chiffrement des données d’objets blob

Azure Storage prend en charge le chiffrement des données d’objets blob côtés client et serveur :

- **Chiffrement côté client :** la bibliothèque cliente de stockage pour .NET prend en charge le chiffrement des données au sein des applications clientes, avant le chargement vers Azure Storage, et le déchiffrement des données pendant leur téléchargement vers le client. La bibliothèque prend également en charge l’intégration à Azure Key Vault pour la gestion des clés de compte de stockage. Pour plus d’informations, voir [Chiffrement côté client avec .NET pour Microsoft Azure Storage](storage-client-side-encryption.md). Voir également [Didacticiel : Chiffrement et déchiffrement d’objets blob dans Microsoft Azure Storage à l’aide d’Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md).
- **Chiffrement côté serveur** : Azure Storage prend désormais en charge le chiffrement côté serveur. Voir [Azure Storage Service Encryption pour les données au repos (version préliminaire)](storage-service-encryption.md).

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage d’objets blob, consultez les liens suivants pour en savoir plus.

### Explorateur Microsoft Azure Storage
- [Microsoft Azure Storage Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite qui vous permet d’exploiter visuellement les données Azure Storage sur Windows, OS X et Linux.

### Exemples relatifs à Blob Storage

- [Prise en main d’Azure Blob Storage dans .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### Documentation de référence sur le stockage d’objets blob

- [Référence de la bibliothèque cliente de stockage pour .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Référence d’API REST](http://msdn.microsoft.com/library/azure/dd179355)

### Guides conceptuels

- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- [Prise en main du stockage de fichier pour .NET](storage-dotnet-how-to-use-files.md)
- [Utilisation du stockage d’objets blob Azure avec le Kit de développement logiciel (SDK) WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355

<!---HONumber=AcomDC_0921_2016-->