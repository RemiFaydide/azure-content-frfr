<properties
	pageTitle="Utilisation du stockage de files d’attente à partir de Python | Microsoft Azure"
	description="Découvrez comment utiliser le service de File d’attente Azure à partir de Python pour créer et supprimer des files d’attente, ainsi que pour insérer, récupérer et supprimer des messages."
	services="storage"
	documentationCenter="python"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="cbrooks;robinsh"/>

# Utilisation du stockage de files d'attente à partir de Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de files d'attente Azure. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et suppression des files d'attente**. Pour plus d’informations sur les files d’attente, consultez la section [Étapes suivantes].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une file d'attente

L'objet **QueueService** permet d'utiliser des files d'attente. Le code suivant permet de créer un objet **QueueService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

	from azure.storage.queue import QueueService

Le code suivant permet de créer un objet **QueueService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **put\_message** pour créer un nouveau message et l'ajouter à la file d'attente.

	queue_service.put_message('taskqueue', u'Hello World')


## Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans le supprimer de la file d'attente en appelant la méthode **peek\_messages**. Par défaut, **peek\_messages** permet de lire furtivement un seul message.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.content)


## Retrait des messages de la file d’attente

Votre code supprime un message d'une file d'attente en deux étapes. Lorsque vous appelez **get\_messages**, vous obtenez le message suivant d'une file d'attente par défaut. Un message renvoyé par **get\_messages** devient invisible de tout autre code lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **delete\_message**. Ce processus de suppression d’un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message** juste après le traitement du message.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **get\_messages** pour obtenir 16 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle for. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

	messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.id, message.pop_receipt)		


## Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code ci-dessous utilise la méthode **update\_message** pour mettre à jour un message. Ce délai de visibilité est défini sur 0, ce qui signifie que le message s’affiche immédiatement et que le contenu est mis à jour.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **get\_queue\_metadata** demande au service de File d’attente de renvoyer les métadonnées relatives à la file d’attente et **approximate\_message\_count**. Le résultat est seulement approximatif, car des messages peuvent être ajoutés ou supprimés après que le service de file d'attente a répondu à votre demande.

	metadata = queue_service.get_queue_metadata('taskqueue')
	count = metadata.approximate_message_count

## Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage de files d’attente, consultez les liens suivants pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l'équipe Azure Storage]
- [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]

[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->