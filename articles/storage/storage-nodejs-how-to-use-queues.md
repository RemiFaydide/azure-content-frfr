<properties
	pageTitle="Utilisation du stockage de files d’attente à partir de Node.js | Microsoft Azure"
	description="Découvrez comment utiliser le service de File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Node.js."
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
	ms.author="robmcm"/>


# Utilisation du stockage de files d'attente à partir de Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de File d’attente Microsoft Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent l’**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d’attente, ainsi que la **création et suppression des files d’attente**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, consultez [Créer une application web Node.js dans Azure App Service], [Création et déploiement d'une application Node.js dans Azure Cloud Services] \(à l’aide de Windows PowerShell) ou [Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix].

## Configuration de votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous avez besoin du Kit de développement logiciel (SDK) Azure Storage pour Node.js, qui inclut un ensemble de bibliothèques pratiques qui communiquent avec les services REST de stockage.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure-storage** dans la fenêtre de commande. Le résultat de la commande ressemble à l’exemple suivant.

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node\_modules** a été créé. Dans ce dossier, recherchez le dossier **azure-storage**, qui contient les bibliothèques dont vous avez besoin pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

	var azure = require('azure-storage');

## Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT et AZURE\_STORAGE\_ACCESS\_KEY, ou AZURE\_STORAGE\_CONNECTION\_STRING pour obtenir les informations obligatoires pour se connecter à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createQueueService**.

Pour obtenir un exemple de configuration des variables d’environnement dans le [portail Azure](https://portal.azure.com) pour un site web Azure, consultez [Application web Node.js avec le service de Table Azure].

## Création d'une file d'attente

Le code suivant crée un objet **QueueService**, ce qui vous permet d'utiliser les files d'attente.

	var queueSvc = azure.createQueueService();

Utilisez la méthode **createQueueIfNotExists**, qui renvoie la file d'attente spécifiée si elle existe déjà ou qui en crée une avec le nom spécifié dans le cas contraire.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
	  if(!error){
	    // Queue created or exists
	  }
	});

Si la file d’attente est créée, `result.created` a la valeur true. Si la file d’attente existe, `result.created` a la valeur false.

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées par le biais de **QueueService**. Il peut s’agir d’opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

	function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en passant un rappel avec la signature suivante :

	function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d’autres filtres ou simplement appeler finalCallback pour terminer l’utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **QueueService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **createMessage** afin de créer un message et de l'ajouter dans la file d'attente.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode **peekMessages**. Par défaut, **peekMessages** lit un seul message.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
	    // Message text is in messages[0].messageText
	  }
	});

`result` contient le message.

> [AZURE.NOTE] L’utilisation de **peekMessages** alors qu’il n’y a pas de message dans la file d’attente ne renvoie pas d’erreur, mais ne renvoie pas non plus de message.

## Suppression du message suivant dans la file d'attente

Le traitement d'un message se fait en deux étapes :

1. Enlever le message de la file d'attente.

2. Supprimer le message.

Pour enlever un message de la file d’attente, utilisez **getMessages**. Cela rend les messages invisibles dans la file d'attente, et aucun autre client ne peut les traiter. Lorsque votre application a traité un message, appelez **deleteMessage** pour supprimer le message de la file d’attente. L'exemple suivant obtient un message, puis le supprime :

	queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
	    // Message text is in messages[0].messageText
	    var message = result[0];
	    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
	      if(!error){
	        //message deleted
	      }
	    });
	  }
	});

> [AZURE.NOTE] Par défaut, un message est masqué uniquement pendant 30 secondes avant d'être de nouveau visible pour les autres clients. Vous pouvez indiquer une autre valeur en utilisant `options.visibilityTimeout` avec **getMessages**.

> [AZURE.NOTE]
L’utilisation de **getMessages** alors qu’il n’y a pas de message dans la file d’attente ne renvoie pas d’erreur, mais ne renvoie pas non plus de message.

## Modification du contenu d'un message en file d'attente

Vous pouvez changer le contenu d'un message qui se trouve dans la file d'attente en utilisant **updateMessage**. L'exemple suivant met à jour le texte d'un message :

	queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
	    // Got the message
	    var message = result[0];
	    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
	      if(!error){
	        // Message updated successfully
	      }
	    });
	  }
	});

## Options supplémentaires pour la suppression des messages dans la file d'attente

Il existe deux méthodes pour personnaliser l'extraction d'un message d'une file d'attente :

* `options.numOfMessages` - Extraire un lot de messages (jusqu’à 32).
* `options.visibilityTimeout` - Définir une durée d’invisibilité plus longue ou plus courte.

L'exemple suivant utilise la méthode **getMessages** pour obtenir 15 messages dans un appel. Ensuite, il traite chaque message à l'aide d'une boucle for. La durée d'invisibilité est passée à cinq minutes pour tous les messages renvoyés par cette méthode.

	queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
	    // Messages retreived
	    for(var index in result){
	      // text is available in result[index].messageText
	      var message = result[index];
	      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
	        if(!error){
	          // Message deleted
	        }
	      });
	    }
	  }
	});

## Obtention de la longueur de la file d'attente

**getQueueMetadata** renvoie des métadonnées sur la file d'attente, y compris le nombre approximatif de messages en attente dans la file d'attente.

	queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
	    // Queue length is available in result.approximateMessageCount
	  }
	});

## Procédure : Affichage de la liste de disques

Pour extraire une liste de files d'attente, utilisez **listQueuesSegmented**. Pour extraire une liste filtrée par un certain préfixe, utilisez **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Si aucune file d’attente ne peut être renvoyée, `result.continuationToken` peut servir de premier paramètre de **listQueuesSegmented** ou de second paramètre de **listQueuesSegmentedWithPrefix** pour récupérer plus de résultats.

## Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **deleteQueue** sur l'objet file d'attente.

	queueSvc.deleteQueue(queueName, function(error, response){
	  if(!error){
	    // Queue has been deleted
	  }
	});

Pour effacer tous les messages d'une file d'attente sans supprimer cette dernière, utilisez **clearMessages**.

## Procédure : Utilisation des signatures d’accès partagé

Les signatures d'accès partagé sont un moyen sécurisé de fournir un accès précis aux files d'attente sans fournir le nom ni les clés de votre compte de stockage. Elles servent souvent à fournir un accès limité à vos files d'attente, par exemple pour autoriser une application mobile à soumettre des messages.

Une application approuvée, comme un service cloud, génère une signature d'accès partagé à l'aide de **generateSharedAccessSignature** de **QueueService**, et la fournit à une application non approuvée ou à moitié approuvée. Par exemple, une application mobile. La signature d'accès partagé est générée à l'aide d'une stratégie, qui décrit les dates de début et de fin de validité de la signature, et le niveau d'accès accordé au détenteur de la signature.

L'exemple suivant génère une nouvelle stratégie d'accès partagé qui autorise le détenteur de la signature d'accès partagé à ajouter des messages à la file d'attente et expire 100 minutes après son heure de création.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);

	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

Notez que les informations sur l'hôte doivent également être fournies, car elles sont obligatoires lorsque le détenteur de la signature d'accès partagé tente d'accéder à la file d'attente.

L'application cliente utilise les signatures d'accès partagé avec **QueueServiceWithSAS** pour effectuer les opérations sur la file d'attente. L'exemple suivant se connecte à la file d'attente et crée un message.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Comme la signature d'accès partagé a été générée avec un accès en ajout, une erreur sera renvoyée en cas de tentative de lecture, de mise à jour ou de suppression des messages.

### Listes de contrôle d'accès

Vous pouvez également utiliser une liste de contrôle d'accès (ACL) pour définir la stratégie d'accès pour une signature d'accès partagé. Cela est utile si vous voulez autoriser plusieurs clients à accéder à une file d'attente, mais fournir des stratégies d'accès différentes à chaque client.

Une liste de contrôle d'accès est implémentée à l'aide d'un tableau de stratégies d'accès, dans lequel un ID est associé à chaque stratégie. L'exemple suivant définit deux stratégies ; une pour « user1 » et une pour « user2 » :

	var sharedAccessPolicy = {
	  user1: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	  user2: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

L'exemple suivant obtient la liste de contrôle d'accès active pour **myqueue**, puis ajoute les nouvelles stratégies à l'aide de **setQueueAcl**. Cette approche permet :

	var extend = require('extend');
	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
	  if(!error){
	    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
	    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
	      if(!error){
	        // ACL set
	      }
	    });
	  }
	});

Lorsque la liste de contrôle d'accès est définie, vous pouvez créer une signature d'accès partagé basée sur l'ID pour une stratégie. L'exemple suivant crée une signature d'accès partagé pour « user2 » :

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des files d'attente, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

-   Consultez le [Blog de l'équipe Azure Storage][].
-   Consultez le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Node][] sur GitHub.

  [Kit de développement logiciel (SDK) Azure Storage pour Node]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Créer une application web Node.js dans Azure App Service]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Application web Node.js avec le service de Table Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Création et déploiement d'une application Node.js dans Azure Cloud Services]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md

<!---HONumber=AcomDC_0817_2016-->