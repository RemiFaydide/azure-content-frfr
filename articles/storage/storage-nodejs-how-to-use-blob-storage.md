<properties
	pageTitle="Utilisation du stockage d’objets blob à partir de Node.js | Microsoft Azure"
	description="Stockez des données non structurées dans le cloud avec Azure Blob Storage (stockage d’objets)."
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
    ms.date="08/11/2016"
	ms.author="micurd"/>



# Utilisation du stockage d'objets blob à partir de Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## Vue d'ensemble

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation de Blob Storage. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent comment transférer, lister, télécharger et supprimer des objets blob.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’une application Node.js

Pour obtenir des instructions sur la création d’une application Node.js, consultez [Créer une application web Node.js dans Azure App Service], [Création et déploiement d'une application Node.js dans Azure Cloud Services] \(à l’aide de Windows PowerShell) ou [Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix].

## Configuration de votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous avez besoin du Kit de développement logiciel (SDK) Azure Storage pour Node.js, qui inclut un ensemble de bibliothèques pratiques qui communiquent avec les services REST de stockage.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande comme **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier où vous avez créé votre exemple d'application.

2.  Tapez **npm install azure-storage** dans la fenêtre de commande. Le résultat de la commande est similaire à l’exemple de code suivant.

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node\_modules** a été créé. Dans ce dossier, recherchez le package **azure-storage** qui contient les bibliothèques dont vous avez besoin pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure-storage');

## Configurer une connexion Azure Storage

Le module Azure lit les variables d'environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING`, pour obtenir les informations nécessaires à la connexion à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createBlobService**.

Pour obtenir un exemple de configuration des variables d’environnement dans le [portail Azure](https://portal.azure.com) pour une application web Azure, consultez [Application web Node.js avec le service de Table Azure].

## Créer un conteneur

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code suivant crée un objet **BlobService**. Ajoutez le code suivant vers le début du fichier **server.js** :

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Vous pouvez accéder à un objet blob de façon anonyme en utilisant **createBlobServiceAnonymous** et en fournissant l’adresse hôte. Par exemple, utilisez `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Pour créer un conteneur, utilisez **createContainerIfNotExists**. Le code suivant crée un nouveau conteneur appelé « mycontainer » :

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
	    if(!error){
	      // Container exists and is private
	    }
	});

Si le conteneur est nouvellement créé, `result.created` a la valeur true. Si le conteneur existe déjà, `result.created` a la valeur false. `response` contient des informations sur l’opération, notamment les informations ETag du conteneur.

### Sécurité du conteneur

Par défaut, les nouveaux conteneurs sont privés et ne sont pas accessibles de façon anonyme. Pour rendre le conteneur public afin de permettre l’accès anonyme, définissez le niveau d’accès au conteneur sur **blob** ou **container**.

* **blob** : permet l'accès en lecture anonyme au contenu de l'objet blob et aux métadonnées de ce conteneur, mais pas aux métadonnées du conteneur, comme la liste de tous les objets blob d'un conteneur.

* **container** : permet l’accès en lecture anonyme au contenu et aux métadonnées de l’objet blob, ainsi qu’aux métadonnées de conteneur.

L’exemple de code suivant montre la définition du niveau d’accès sur **blob** :

	blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
	    if(!error){
	      // Container exists and allows
	      // anonymous read access to blob
	      // content and metadata within this container
	    }
	});

Vous pouvez également modifier le niveau d'accès d'un conteneur en le spécifiant via **setContainerAcl**. L’exemple de code suivant change le niveau d’accès en « container » :

	blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
	    // Container access level set to 'container'
	  }
	});

Le résultat contient des informations sur l’opération, y compris les informations **ETag** actuelles pour le conteneur.

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées au moyen de **BlobService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

	function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en passant un rappel avec la signature suivante :

	function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse à la requête du serveur), le rappel doit appeler next, s'il existe, pour continuer à traiter d'autres filtres ou appeler finalCallback pour terminer l'appel du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **BlobService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Charger un objet blob dans un conteneur

Il existe trois types d’objets blob : les objets blob de blocs, les objets blob d’ajouts et les objets blob de pages. Les objets blob de blocs vous permettent de télécharger plus efficacement les données volumineuses. Les objets blob d’ajout sont optimisés pour les opérations d’ajout. Les objets blob de pages sont optimisés pour les opérations de lecture/écriture. Pour plus d’informations, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### Objets blob de blocs

Pour télécharger des données dans un objet blob de blocs, utilisez :

* **createBlockBlobFromLocalFile** : permet de créer un objet blob de blocs et de télécharger le contenu d'un fichier

* **createBlockBlobFromStream** : permet de créer un objet blob de blocs et de télécharger le contenu d'un flux

* **createBlockBlobFromText** : permet de créer un objet blob de blocs et de télécharger le contenu d'une chaîne

* **createWriteStreamToBlockBlob** : fournit un flux d’écriture vers un objet blob de blocs

L’exemple de code suivant charge le contenu du fichier **test.txt** dans **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Le `result` renvoyé par ces méthodes contient les informations sur l'opération, comme l'**ETag** de l'objet blob.

### Objets blob d’ajout

Pour télécharger des données dans un nouvel objet blob d’ajout, utilisez :

* **createAppendBlobFromLocalFile** : permet de créer un objet blob d’ajout et de charger le contenu d’un fichier

* **createAppendBlobFromStream** : permet de créer un objet blob d’ajout et de charger le contenu d’un flux

* **createAppendBlobFromText** : permet de créer un objet blob d’ajout et de charger le contenu d’une chaîne

* **createWriteStreamToNewAppendBlob** : permet de créer un objet blob d’ajout et fournit un flux pour y écrire

L’exemple de code suivant charge le contenu du fichier **test.txt** dans **myappendblob**.

	blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Pour ajouter un bloc à un objet blob d’ajout existant, procédez comme suit :

* **appendFromLocalFile** : permet d’ajouter le contenu d’un fichier à un objet blob d’ajout existant

* **appendFromStream** : permet d’ajouter le contenu d’un flux à un objet blob d’ajout existant

* **appendFromText** : permet d’ajouter le contenu d’une chaîne à un objet blob d’ajout existant

* **appendBlockFromStream** : permet d’ajouter le contenu d’un flux à un objet blob d’ajout existant

* **appendBlockFromText** : permet d’ajouter le contenu d’une chaîne à un objet blob d’ajout existant

> [AZURE.NOTE] Les API appendFromXXX API effectuent une validation côté client pour échouer rapidement afin d’éviter tout appel inutile au serveur. Ce n’est pas le cas des API appendBlockFromXXX.

L’exemple de code suivant charge le contenu du fichier **test.txt** dans **myappendblob**.

	blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
	  if(!error){
	    // text appended
	  }
	});


### Objets blob de pages

Pour télécharger des données dans un objet blob de pages, utilisez :

* **createPageBlob** : permet de créer un objet blob de pages d’une longueur spécifique

* **createPageBlobFromLocalFile** : permet de créer un objet blob de pages et de charger le contenu d’un fichier

* **createPageBlobFromStream** : permet de créer un objet blob de pages et de charger le contenu d’un flux

* **createWriteStreamToExistingPageBlob** : fournit un flux d’écriture à un objet blob de pages existant

* **createWriteStreamToNewPageBlob** : permet de créer un objet blob de pages et fournit un flux pour y écrire

L’exemple de code suivant charge le contenu du fichier **test.txt** dans **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE] Les objets blob de pages sont constitués de « pages » de 512 octets. Une erreur se produit lors du téléchargement de données lorsque leur taille n’est pas un multiple de 512.

## Création d’une liste d’objets blob dans un conteneur

Pour créer une liste d'objets blob dans un conteneur, utilisez la méthode **listBlobsSegmented**. Pour renvoyer les objets blob avec un préfixe donné, utilisez **listBlobsSegmentedWithPrefix**.

	blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
	  if(!error){
	      // result.entries contains the entries
	      // If not all blobs were returned, result.continuationToken has the continuation token.
	  }
	});

Le `result` contient une collection `entries`, qui est un tableau d’objets décrivant chaque objet blob. Si tous les objets blob ne peuvent pas être retournés, `result` fournit également un élément `continuationToken`, qui peut servir de deuxième paramètre pour récupérer d’autres entrées.

## Télécharger des objets blob

Pour télécharger les données d'un objet blob, utilisez :

* **getBlobToLocalFile** : permet d’écrire le contenu de l’objet blob dans un fichier

* **getBlobToStream** : permet d’écrire le contenu de l’objet blob dans un flux

* **getBlobToText** : permet d’écrire le contenu de l’objet blob dans une chaîne

* **createReadStream** : fournit un flux pour lire à partir de l'objet blob.

L’exemple de code suivant montre l’utilisation de **getBlobToStream** pour télécharger le contenu de l’objet blob **myblob** et le stocker dans le fichier **output.txt** en utilisant un flux :

	var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

Le `result` contient les informations de l’objet blob, y compris les informations **ETag**.

## Supprimer un objet blob

Pour supprimer un objet blob, appelez **deleteBlob**. L’exemple de code suivant supprime l’objet blob nommé **myblob**.

	blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Accès simultané

Pour activer la prise en charge de l'accès simultané à un objet blob par plusieurs clients ou instances de processus, vous pouvez utiliser **ETags** ou **leases**.

* **Etag** : permet de détecter la modification de l’objet blob ou du conteneur par un autre processus

* **Lease** : permet d’obtenir l’accès exclusif, renouvelable, en écriture ou en suppression à un objet blob pour une période donnée

### ETag

Utilisez les ETag si vous avez besoin que plusieurs clients ou instances écrivent simultanément sur l’objet blob de blocs ou l’objet blob de pages. Les ETag vous permettent de déterminer si le conteneur ou l'objet blob a été modifié depuis sa création ou son premier accès en lecture, ce qui vous évite d'écraser des changements effectués par un autre client ou processus.

Vous pouvez définir des conditions ETag à l’aide du paramètre facultatif `options.accessConditions`. L’exemple de code suivant charge le fichier **test.txt** seulement si l’objet blob existe déjà et a la valeur ETag contenue dans `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
	    if(!error){
	    // file uploaded
	  }
	});

L'utilisation des ETags suit le modèle général suivant :

1. Obtention de l'ETag à la suite d'une opération create, list ou get.

2. Exécution d'une action, recherche de la modification éventuelle de la valeur ETag.

Si la valeur a été modifiée, cela signifie qu'un autre client ou qu'une autre instance a modifié l'objet blob ou le conteneur depuis que vous avez obtenu la valeur ETag.

### Lease

Vous pouvez acquérir un nouveau bail à l’aide de la méthode **acquireLease** en spécifiant l’objet blob ou le conteneur concerné. Par exemple, le code suivant permet d'acquérir un bail sur **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Les opérations suivantes sur **myblob** doivent fournir le paramètre `options.leaseId`. L'ID du bail est renvoyé comme `result.id` à partir de **acquireLease**.

> [AZURE.NOTE] Par défaut, la durée du bail est infinie. Vous pouvez spécifier une durée finie (entre 15 et 60 secondes) en fournissant le paramètre `options.leaseDuration`.

Pour supprimer un bail, utilisez **releaseLease**. Pour terminer un bail, mais empêcher d'autres utilisateurs d'obtenir un nouveau bail avant la fin de la durée d'origine, utilisez **breakLease**.

## Utilisation des signatures d'accès partagé

Les signatures d'accès partagé sont un moyen sécurisé de fournir un accès précis aux objets blob et aux conteneurs sans fournir le nom ni les clés de votre compte de stockage. Elles servent souvent à fournir un accès limité à vos données, par exemple pour autoriser une application mobile à accéder à des objets blob.

> [AZURE.NOTE] Vous pouvez bien sûr autoriser l'accès anonyme aux objets blob, mais les signatures d'accès partagé permettent de mieux contrôler les accès, car elles doivent être générées.

Une application approuvée, comme un service cloud, génère une signature d’accès partagé à l’aide de l’élément **generateSharedAccessSignature** du **BlobService** et la fournit à une application non approuvée ou semi-approuvée, comme une application mobile. Les signatures d'accès partagé sont générées à l'aide d'une stratégie, qui décrit les dates de début et de fin de validité des signatures d'accès partagé, et le niveau d'accès accordé au détenteur des signatures d'accès partagé.

L’exemple de code suivant génère une nouvelle stratégie d’accès partagé qui autorise le détenteur de la signature d’accès partagé à effectuer des opérations de lecture sur l’objet blob **myblob** et expire 100 minutes après sa création.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);

	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Notez que les informations sur l'hôte doivent également être fournies, car elles sont obligatoires lorsque le détenteur de la signature d'accès partagé tente d'accéder au conteneur.

L’application cliente utilise ensuite les signatures d’accès partagé avec **BlobServiceWithSAS** pour effectuer les opérations sur l’objet blob. Le code suivant obtient les informations sur **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Étant donné que les signatures d'accès partagé ont été générées avec un accès en lecture seule, une erreur est renvoyée en cas de tentative de modification de l'objet blob.

### Listes de contrôle d'accès

Vous pouvez également utiliser une liste de contrôle d'accès (ACL) pour définir la stratégie d'accès pour une signature d'accès partagé. Cela est utile si vous voulez autoriser plusieurs clients à accéder à un conteneur, mais fournir des stratégies d'accès différentes à chaque client.

Une liste de contrôle d'accès est implémentée à l'aide d'un tableau de stratégies d'accès, dans lequel un ID est associé à chaque stratégie. L'exemple de code suivant définit deux stratégies ; une pour « user1 » et une pour « user2 » :

	var sharedAccessPolicy = {
	  user1: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	  user2: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

L'exemple de code suivant obtient la liste de contrôle d'accès active pour **mycontainer**, puis ajoute les nouvelles stratégies à l'aide de **setBlobAcl**. Cette approche permet :

	var extend = require('extend');
	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
	  if(!error){
	    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
	    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
	      if(!error){
	        // ACL set
	      }
	    });
	  }
	});

Lorsque la liste de contrôle d'accès est définie, vous pouvez créer des signatures d'accès partagé basées sur l'ID pour une stratégie. L'exemple de code suivant crée de nouvelles signatures d'accès partagé pour « user2 » :

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Étapes suivantes

Pour plus d'informations, consultez les ressources suivantes.

-   [Kit de développement logiciel (SDK) Azure Storage pour la référence de l'API Node][]
-   [Blog de l'équipe Azure Storage][]
-   Référentiel [Kit de développement logiciel (SDK) Azure Storage pour Node][] sur GitHub.
-   [Centre de développement Node.js](/develop/nodejs/)
-   [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

[Kit de développement logiciel (SDK) Azure Storage pour Node]: https://github.com/Azure/azure-storage-node

[Créer une application web Node.js dans Azure App Service]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Application web Node.js avec le service de Table Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Création et déploiement d'une application Node.js dans Azure Cloud Services]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Azure Storage pour la référence de l'API Node]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!---HONumber=AcomDC_0921_2016-->