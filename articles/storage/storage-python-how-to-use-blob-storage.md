<properties
	pageTitle="Utilisation du stockage d’objets blob Azure à partir de Python | Microsoft Azure"
	description="Stockez des données non structurées dans le cloud avec Azure Blob Storage (stockage d’objets)."
	services="storage"
	documentationCenter="python"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
    ms.date="09/20/2016"
	ms.author="jwillis;tamram"/>

# Utilisation du stockage d’objets blob Azure à partir de Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Vue d'ensemble

Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets/blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Blob Storage.

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage d’objets blob. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent le chargement, l'énumération, le téléchargement et la suppression d'objets blob.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Créer un conteneur

Selon le type d’objet blob que vous souhaitez utiliser, créez un objet **BlockBlobService**, **AppendBlobService** ou **PageBlobService**. Le code suivant utilise un objet **BlockBlobService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder au stockage d’objet blob de blocs Azure par programme.

	from azure.storage.blob import BlockBlobService

Le code suivant crée un objet **BlockBlobService** à l’aide du nom et de la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

	block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Dans l’exemple de code suivant, vous pouvez utiliser un objet **BlockBlobService** pour créer le conteneur s’il n’existe pas.

	block_blob_service.create_container('mycontainer')

Par défaut, le nouveau conteneur est privé. Vous devez donc indiquer votre clé d’accès au stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les objets blob du conteneur soient accessibles à tout le monde, vous pouvez créer le conteneur et lui attribuer le niveau d’accès public en utilisant le code suivant.

	from azure.storage.blob import PublicAccess
	block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

L’autre possibilité consiste à modifier un conteneur déjà créé, à l’aide du code suivant.

	block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

Après cette modification, tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais vous êtes le seul à pouvoir les modifier ou les supprimer.

## Charger un objet blob dans un conteneur

Pour créer un objet blob de blocs et télécharger des données, utilisez les méthodes **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** ou **create\_blob\_from\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**create\_blob\_from\_path** télécharge le contenu d’un fichier à partir du chemin spécifié. **create\_blob\_from\_stream** télécharge le contenu à partir d’un fichier/flux déjà ouvert. **create\_blob\_from\_bytes** télécharge un tableau d’octets. **create\_blob\_from\_text** télécharge la valeur de texte spécifiée à l’aide de l’encodage indiqué (UTF-8 par défaut).

L’exemple suivant charge le contenu du fichier **sunset.png** dans l’objet blob **myblob**.

	from azure.storage.blob import ContentSettings
	block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
				)

## Créer une liste d'objets blob dans un conteneur

Pour répertorier les objets blob dans un conteneur, utilisez la méthode **list\\_blobs**. Cette méthode retourne un générateur. Le code suivant sort le **nom** de chaque objet blob d'un conteneur sur la console.

	generator = block_blob_service.list_blobs('mycontainer')
	for blob in generator:
		print(blob.name)

## Télécharger des objets blob

Pour télécharger les données d’un objet blob, utilisez **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de **get\_blob\_to\_path** pour télécharger le contenu de l’objet blob **myblob** et le stocker dans le fichier **out-sunset.png**.

	block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## Supprimer un objet blob

Pour supprimer un objet blob, appelez **delete\_blob**.

	block_blob_service.delete_blob('mycontainer', 'myblockblob')

## Écriture dans un objet blob d’ajout

Il est optimisé pour les opérations d’ajout, telles que la journalisation. Comme un objet blob de blocs, un objet blob d’ajout est composé de blocs. Mais lorsqu’il est ajouté à un objet blob d’ajout, un nouveau bloc l’est toujours à la fin. Vous ne pouvez pas mettre à jour ou supprimer un bloc dans un objet blob d’ajout. Les ID de bloc dans un objet blob d’ajout ne sont pas visibles, comme pour un objet blob de blocs.

Chaque bloc d’un objet blob d’ajout peut avoir une taille différente (jusqu’à 4 Mo), et un objet blob d’ajout peut contenir au maximum 50 000 blocs. La taille maximale d’un objet blob d’ajout est donc légèrement supérieure à 195 Go (4 Mo x 50 000 blocs).

L’exemple ci-dessous crée un objet blob d’ajout et y ajoute des données pour simuler une opération de journalisation simple.

	from azure.storage.blob import AppendBlobService
	append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

	# The same containers can hold all types of blobs
	append_blob_service.create_container('mycontainer')

	# Append blobs must be created before they are appended to
	append_blob_service.create_blob('mycontainer', 'myappendblob')
	append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

	append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage d’objets blob, consultez les liens suivants pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l'équipe Azure Storage]
- [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]

[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->