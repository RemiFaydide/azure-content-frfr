<properties
	pageTitle="Didacticiel : Chiffrement et déchiffrement d’objets blob dans Microsoft Azure Storage à l’aide d’Azure Key Vault | Microsoft Azure"
	description="Ce didacticiel vous explique comment chiffrer et déchiffrer un objet blob en utilisant le chiffrement côté client pour Microsoft Azure Storage avec Azure Key Vault."
	services="storage"
	documentationCenter=""
	authors="adhurwit"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="required"
	ms.date="09/20/2016"
	ms.author="lakasa;robinsh"/>

# Didacticiel : Chiffrement et déchiffrement d’objets blob dans Microsoft Azure Storage à l'aide d'Azure Key Vault

## Introduction

Ce didacticiel décrit comment utiliser le chiffrement de stockage côté client avec Azure Key Vault. Il vous explique comment chiffrer et déchiffrer un objet blob dans une application console à l'aide de ces technologies.

**Durée estimée :** 20 minutes

Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](../key-vault/key-vault-whatis.md).

Pour plus d’informations générales sur le chiffrement côté client d’Azure Storage, consultez [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](storage-client-side-encryption.md)


## Composants requis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un compte Azure Storage
- Visual Studio 2013 ou une version ultérieure
- Azure PowerShell


## Vue d’ensemble du chiffrement côté client

Pour une vue d’ensemble du chiffrement côté client d’Azure Storage, consultez [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](storage-client-side-encryption.md).

Voici une brève description du fonctionnement du chiffrement côté client :

1. Le Kit de développement logiciel (SDK) client d’Azure Storage génère une clé de chiffrement de contenu (CEK) qui est une clé symétrique à usage unique.
2. Les données du client sont chiffrées à l’aide de cette clé de chiffrement de contenu.
3. La clé de chiffrement de contenu est ensuite encapsulée (chiffrée) à l’aide de la clé de chiffrement de clés (KEK). La clé de chiffrement de clés est identifiée par un identificateur de clé et peut être une paire de clés asymétriques ou une clé symétrique pouvant être gérée localement ou stockée dans Azure Key Vault. Le client Storage n’a jamais accès à la clé de chiffrement de clés. Il appelle simplement l'algorithme d’encapsulage de clés fourni par Key Vault. Si besoin est, les clients peuvent choisir d’utiliser des fournisseurs personnalisés pour l’encapsulage/le désencapsulage de clés.
4. Les données chiffrées sont ensuite téléchargées sur le service Azure Storage.


## Configurer votre coffre de clés Azure
Pour continuer avec ce didacticiel, vous devez effectuer les étapes suivantes qui sont décrites dans le didacticiel [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md) :

- Création d’un coffre de clés
- Ajout d’une clé ou d’un secret au coffre de clés
- Inscription d’une application auprès d’Azure Active Directory
- Autorisation de l’application à utiliser la clé ou le secret

Notez les valeurs ClientID et ClientSecret qui ont été générées lors de l'inscription d'une application avec Azure Active Directory.

Créez les deux clés dans le coffre de clés. Pour la suite du didacticiel, nous partons du principe que vous avez utilisé les noms suivants : ContosoKeyVault et TestRSAKey1.


## Création d’une application console avec les packages et AppSettings

Dans Visual Studio, créez une application console.

Ajoutez les packages nuget nécessaires dans la Console du gestionnaire de package.

	Install-Package WindowsAzure.Storage

	// This is the latest stable release for ADAL.
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault
	Install-Package Microsoft.Azure.KeyVault.Extensions


Ajoutez la section AppSettings au fichier App.Config.

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

Ajoutez les instructions `using` suivantes et assurez-vous d’ajouter une référence à l’assembly System.Configuration du projet.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## Ajouter une méthode pour obtenir un jeton pour votre application console

La méthode suivante est utilisée par les classes Key Vault qui doivent s’authentifier pour accéder à votre coffre de clés.

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"],
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");

	    return result.AccessToken;
	}

## Accéder à Storage et à Key Vault dans votre programme

Dans la fonction Main, ajoutez le code suivant.

	// This is standard code to interact with Blob storage.
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage.
	// This is where the GetToken method from above is used.
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modèles d'objet Key Vault
>
>Il est important de comprendre qu'il n'y a que deux modèles d'objet Key Vault à connaître : l’un est basé sur l'API REST (espace de noms KeyVault) et l'autre est une extension pour le chiffrement côté client.

> Le client Key Vault interagit avec l’API REST et comprend les secrets et les clés web JSON pour les deux genres d’éléments qui sont contenus dans Key Vault.

> Les extensions Key Vault sont des classes qui semblent avoir été créées spécifiquement pour le chiffrement côté client dans Azure Storage. Elles contiennent une interface pour les clés (IKey) et des classes basées sur le concept d’un programme de résolution de clés. Il existe deux implémentations d’IKey à connaître : RSAKey et SymmetricKey. Elles coïncident désormais avec les éléments contenus dans un coffre de clés, mais à ce stade, ce sont des classes indépendantes (la clé et le secret récupérés par le client Key Vault n'implémentent donc pas IKey).


## Chiffrement et téléchargement d’objets blob
Ajoutez le code suivant pour chiffrer un objet blob et le télécharger sur votre compte de stockage Azure. La méthode **ResolveKeyAsync** utilisée renvoie un IKey.


	// Retrieve the key that you created previously.
	// The IKey that is returned here is an RsaKey.
	// Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob.
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method.
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


Voici une capture d’écran du [portail Azure Classic](https://manage.windowsazure.com) pour un objet blob qui a été chiffré à l’aide du chiffrement côté client avec une clé stockée dans Key Vault. La propriété **KeyId** est l’URI de la clé dans Key Vault qui fait office de clé de chiffrement de clés (KEK). La propriété **EncryptedKey** contient la version chiffrée de la clé de chiffrement de contenu (CEK).

![Capture d'écran présentant des métadonnées d'objets blob qui incluent des métadonnées de chiffrement][1]

> [AZURE.NOTE] Si vous examinez le constructeur BlobEncryptionPolicy, vous remarquerez qu'il peut accepter une clé et/ou un programme de résolution. Rappelez-vous que vous ne pouvez pas utiliser un programme de résolution pour le chiffrement, car celui-ci ne prend pour l’instant pas en charge de clé par défaut.



## Déchiffrement et téléchargement d’objets blob
Le déchiffrement s’effectue réellement quand les classes du programme de résolution sont pertinentes. L’ID de la clé utilisée pour le chiffrement est associé à l’objet blob dans ses métadonnées. Il est donc inutile de récupérer la clé et de mémoriser l’association entre la clé et les objets blob. Il vous suffit de vous assurer que la clé reste bien dans Key Vault.

La clé privée d’une clé RSA reste dans Key Vault. Ainsi, la clé chiffrée des métadonnées d’objets blob qui contient la clé de chiffrement de contenu (CEK) est envoyée à Key Vault pour que le déchiffrement puisse avoir lieu.

Ajoutez le code suivant pour déchiffrer l’objet blob que vous venez de télécharger.

	// In this case, we will not pass a key and only pass the resolver because
	// this policy will only be used for downloading / decrypting.
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Il existe deux autres genres de programmes de résolution vous permettant de gérer plus facilement vos clés, à savoir : AggregateKeyResolver et CachingKeyResolver.


## Utiliser les secrets Key Vault
L’utilisation d’un secret avec le chiffrement côté client s’effectue via la classe SymmetricKey, car un secret est essentiellement une clé symétrique. Mais, comme indiqué ci-dessus, un secret dans Key Vault ne correspond pas exactement à une valeur SymmetricKey. Plusieurs choses sont à savoir ici :


- La clé d’une valeur SymmetricKey doit avoir une longueur fixe : 128, 192, 256, 384 ou 512 bits.
- La clé d’une valeur SymmetricKey doit être codée en Base64.
- Un secret Key Vault qui est utilisé comme une valeur SymmetricKey doit avoir un type de contenu « application/octet-stream » dans Key Vault.

Voici un exemple de création de secret dans Key Vault, effectuée dans PowerShell, qui peut être utilisé comme une valeur SymmetricKey. REMARQUE : la valeur codée en dur, $key, est fournie uniquement à des fins de démonstration. Dans votre propre code, vous devez générer cette clé.

	// Here we are making a 128-bit key so we have 16 characters.
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128.
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// Substitute the VaultName and Name in this command.
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Dans votre application console, vous pouvez utiliser le même appel qu’auparavant pour récupérer ce secret comme une valeur SymmetricKey.

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Vous avez terminé. Vous n’avez plus qu’à l’utiliser !

## Étapes suivantes

Pour plus d’informations sur l’utilisation de Microsoft Azure Storage avec C#, consultez [Bibliothèque cliente Microsoft Azure Storage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Pour plus d’informations sur l’API REST Blob, consultez [API REST du service BLOB](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Pour obtenir les dernières informations sur Microsoft Azure Storage, consultez le [Blog de l’équipe Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=AcomDC_0921_2016-->