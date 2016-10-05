<properties
	pageTitle="Utilisation du stockage de fichiers Azure à partir de Python | Microsoft Azure"
	description="Découvrez comment utiliser le stockage de fichiers Azure à partir de Python pour charger, répertorier, télécharger et supprimer des fichiers."
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
	ms.author="minet;robinsh"/>

# Utilisation du stockage de fichiers Azure à partir de Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## Vue d'ensemble

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du stockage de fichiers. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent le téléchargement (vers une cible), la création de listes, le téléchargement (à partir d'une source) et la suppression de fichiers.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Créer un partage

L’objet **FileService** permet d'utiliser des partages, des répertoires et des fichiers. Le code suivant permet de créer un objet **FileService**. Ajoutez ce qui suit vers le début de tout fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme.

	from azure.storage.file import FileService

Le code suivant permet de créer un objet **FileService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

	file_service = **FileService** (account_name='myaccount', account_key='mykey')

Dans l’exemple de code suivant, vous pouvez utiliser un objet **FileService** pour créer le partage s’il n’existe pas.

	file_service.create_share('myshare')

## Télécharger un fichier dans un partage

Un partage de fichiers de stockage Azure contient au minimum un répertoire racine dans lequel les fichiers peuvent résider. Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

Pour créer un fichier et télécharger des données, utilisez la méthode **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** ou **create\_file\_from\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**create\_file\_from\_path** télécharge le contenu d’un fichier à partir du chemin spécifié. **create\_file\_from\_stream** télécharge le contenu à partir d’un fichier/flux déjà ouvert. **create\_file\_from\_bytes** télécharge un tableau d’octets. **create\_file\_from\_text** télécharge la valeur de texte spécifiée à l’aide du codage indiqué (UTF-8 par défaut).

L’exemple suivant charge le contenu du fichier **sunset.png** dans le fichier **myfile**.

	from azure.storage.file import ContentSettings
	file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## Création d’un répertoire

Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

	file_service.create_directory('myshare', 'sampledir')

## Obtention d’une liste des fichiers et répertoires d’un partage

Pour répertorier les fichiers et répertoires d'un partage, utilisez la méthode **list\_directories\_and\_files**. Cette méthode retourne un générateur. Le code suivant sort le **nom** de chaque fichier et répertoire d'un partage sur la console.

	generator = file_service.list_directories_and_files('myshare')
	for file_or_dir in generator:
		print(file_or_dir.name)

## Téléchargement de fichiers

Pour télécharger des données à partir d'un fichier, utilisez **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** ou **get\_file\_to\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de **get\_file\_to\_path** pour télécharger le contenu du fichier **myfile** et le stocker dans le fichier **out-sunset.png**.

	file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## Supprimer un fichier

Enfin, pour supprimer un fichier, appelez **delete\_file**.

	file_service.delete_file('myshare', None, 'myfile')

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage de fichiers, consultez les liens suivants pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l'équipe Azure Storage]
- [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]

[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->