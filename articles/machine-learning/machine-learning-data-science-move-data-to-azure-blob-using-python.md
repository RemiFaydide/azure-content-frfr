<properties
	pageTitle="Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de Python | Microsoft Azure"
	description="Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de Python"
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev" />

# Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de Python

Cette rubrique décrit comment répertorier, charger et télécharger des objets blob à l’aide de l’API Python. Avec l’API Python fournie dans le SDK Azure, vous pouvez :

- Créer un conteneur
- Charger un objet blob dans un conteneur
- Télécharger des objets blob
- Créer une liste d'objets blob dans un conteneur
- Supprimer un objet blob

Pour plus d’informations sur l’utilisation de l’API Python, consultez [Utilisation du service de stockage d’objets blob à partir de Python](../storage/storage-python-how-to-use-blob-storage.md).

Des conseils sur les technologies utilisées pour déplacer des données vers et/ou depuis le stockage d’objets blob Azure sont disponibles ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si vous utilisez une machine virtuelle qui a été configurée avec les scripts fournis par les [machines virtuelles de science des données dans Azure](machine-learning-data-science-virtual-machines.md), cela signifie qu’AzCopy est déjà installé sur la machine virtuelle.

> [AZURE.NOTE] Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## Composants requis

Ce document suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

- Pour configurer un abonnement Azure, consultez [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’un compte de stockage, ainsi que des informations sur le compte et la clé, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).


## Charger les données dans le blob

Ajoutez la ligne suivante vers le début du code Python dans lequel vous souhaitez programmer l’accès au service Azure Storage :

	from azure.storage.blob import BlobService

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code suivant crée un objet BlobService à l’aide du nom et de la clé du compte de stockage. Remplacez le nom et la clé du compte de stockage par le nom et la clé de votre compte.

	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Pour charger les données dans un blob, utilisez les méthodes suivantes :

1. put\_block\_blob\_from\_path (charge le contenu d’un fichier situé à l’emplacement spécifié)
2. put\_block\_blob\_from\_file (charge le contenu d’un fichier/flux déjà ouvert)
3. put\_block\_blob\_from\_bytes (charge un tableau d’octets)
4. put\_block\_blob\_from\_text (charge la valeur de texte indiquée, dans l’encodage spécifié)

L’exemple de code suivant charge un fichier local dans un conteneur :

	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

L’exemple de code suivant charge tous les fichiers (à l’exception des sous-répertoires) d’un répertoire local dans le blob :

	from azure.storage.blob import BlobService
	from os import listdir
	from os.path import isfile, join

	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		

	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name


## Télécharger des données à partir d’un blob

Pour télécharger des données à partir d’un blob, utilisez les méthodes suivantes :
1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

Ces méthodes effectuent le traitement nécessaire lorsque les données dépassent 64 Mo.

L’exemple de code suivant télécharge le contenu d’un blob d’un conteneur dans un fichier local :

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

L’exemple de code suivant télécharge tous les blobs d’un conteneur. Il utilise list\_blobs pour obtenir la liste des objets blob disponibles dans le conteneur et les télécharge dans un répertoire local.

	from azure.storage.blob import BlobService
	from os.path import join

	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		

	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!---HONumber=AcomDC_0921_2016-->