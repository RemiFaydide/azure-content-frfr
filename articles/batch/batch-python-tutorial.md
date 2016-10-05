<properties
	pageTitle="Didacticiel - Prise en main du client Python Azure Batch | Microsoft Azure"
	description="Découvrez les concepts de base d’Azure Batch et la procédure de développement applicable au service Batch avec un scénario simple."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/27/2016"
	ms.author="marsma"/>

# Prise en main du client Python Azure Batch

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Découvrez les principes de base des clients [Azure Batch][azure_batch] et [Batch Python][py_azure_sdk] dans cet article où nous décrivons une simple application Batch écrite en langage Python. Nous allons voir comment deux exemples de scripts tirent parti du service Batch pour traiter une charge de travail parallèle sur des machines virtuelles Linux dans le cloud, ainsi que la façon dont ceux-ci interagissent avec [Stockage Azure](./../storage/storage-introduction.md) pour la gestion intermédiaire et la récupération des fichiers. Vous allez vous familiariser avec les flux de travail d’application Batch et découvrir une vue d’ensemble des principaux composants de Batch, tels que les travaux, les tâches, les pools et les nœuds de calcul.

![Flux de travail de la solution Batch (de base)][11]<br/>

## Composants requis

Cet article suppose que vous avez acquis une connaissance pratique de Python et que vous êtes familiarisé avec Linux. Il suppose également que vous êtes en mesure de satisfaire les exigences de création de compte spécifiées ci-dessous pour Azure et les services Batch et Storage.

### Comptes

- **Compte Azure** : si vous ne possédez pas encore d’abonnement Azure, [créez un compte Azure gratuit][azure_free_account].
- **Compte Batch** : une fois que vous disposez d’un abonnement Azure, [créez un compte Azure Batch](batch-account-create-portal.md).
- **Compte de stockage** : voir la section [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

### Exemple de code

L’[exemple de code][github_article_samples] du didacticiel Python est l’un des nombreux exemples de code Batch disponibles dans le référentiel [azure-batch-samples][github_samples] sur GitHub. Vous pouvez télécharger tous les exemples en cliquant sur le bouton **Cloner ou télécharger > Télécharger ZIP** de la page d’accueil du référentiel ou en cliquant sur le lien de téléchargement direct [azure-batch-samples-master.zip][github_samples_zip]. Une fois que vous aurez extrait le contenu du fichier ZIP, les deux scripts de ce didacticiel se trouvent dans le répertoire `article_samples` :

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/> `/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### Environnement Python

Pour exécuter l’exemple de script *python\_tutorial\_client.py* sur votre station de travail locale, vous aurez besoin d’un **interpréteur Python** compatible avec la version **2.7** ou **3.3+**. Le script a été testé sur Linux et Windows.

### dépendances de chiffrement

Vous devez installer les dépendances pour la bibliothèque de [chiffrement][crypto], qui sont requises par les packages Python `azure-batch` et `azure-storage`. Effectuez l’opération appropriée pour votre plateforme ci-dessous ou reportez-vous aux détails de [l’installation du chiffrement][crypto_install] pour plus d’informations :

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Si vous effectuez l’installation pour Python 3.3 + sur Linux, utilisez les équivalents python3 pour les dépendances Python. Par exemple, sur Ubuntu : `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### Packages Azure

Ensuite, installez les packages Python **Azure Batch** et **Stockage Azure**. Vous pouvez le faire avec **pip** et le fichier*requirements.txt* disponibles ici :

`/azure-batch-samples/Python/Batch/requirements.txt`

Exécutez la commande **pip** suivante pour installer les packages Batch et Storage :

`pip install -r requirements.txt`

Vous pouvez également installer les packages Python [azure-batch][pypi_batch] et [azure-storage][pypi_storage] manuellement :

`pip install azure-batch`<br/> `pip install azure-storage`

> [AZURE.TIP] Vous devrez peut-être faire précéder vos commandes de `sudo` si vous utilisez un compte non privilégié. Par exemple : `sudo pip install -r requirements.txt`. Pour plus d’informations sur l’installation des packages Python, voir [Installing Packages][pypi_install] (Installation des packages) sur readthedocs.io.

## Exemple de code de didacticiel Python Batch

L’exemple de code du didacticiel Python Batch se compose de deux scripts Python et de quelques fichiers de données.

- **python\_tutorial\_client.py** : interagit avec les services Batch et Storage pour exécuter une charge de travail parallèle sur des nœuds de calcul (machines virtuelles). Le script *python\_tutorial\_client.py* s’exécute sur votre station de travail locale.

- **python\_tutorial\_task.py** : le script qui s’exécute sur les nœuds de calcul dans Azure pour mener à bien l’opération proprement dite. Dans l’exemple, *python\_tutorial\_task.py* analyse le texte dans un fichier téléchargé depuis Stockage Azure (le fichier d’entrée). Ensuite, il génère un fichier texte (fichier de sortie) qui contient une liste des trois mots principaux qui s’affichent dans le fichier d’entrée. Après avoir créé le fichier de sortie, *python\_tutorial\_task.py* télécharge le fichier dans Stockage Azure. Cela permet de le télécharger vers le script client s’exécutant sur votre station de travail. Le script *python\_tutorial\_task.py* s’exécute en parallèle sur plusieurs nœuds de calcul dans le service Batch.

- **./data/taskdata*.txt** : ces trois fichiers texte fournissent l’entrée pour les tâches qui s’exécutent sur les nœuds de calcul.

Le schéma suivant illustre les opérations principales effectuées par les scripts clients et de tâche. Ce flux de travail de base est caractéristique de nombreuses solutions de calcul créées avec Batch. Même s’il ne présente pas toutes les fonctionnalités disponibles dans le service Batch, la quasi-totalité des scénarios Batch comporte des processus de ce flux de travail.

![Exemple de flux de travail Batch][8]<br/>

[**Étape 1.**](#step-1-create-storage-containers) Créer des **conteneurs** dans Azure Blob Storage.<br/> [**Étape 2.**](#step-2-upload-task-script-and-data-files) Charger le script de tâche et les fichiers d’entrée dans les conteneurs.<br/> [**Étape 3.**](#step-3-create-batch-pool) Créer un **pool** Batch <br/> &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Le pool **StartTask** télécharge le script de tâche (python\_tutorial\_task.py) dans les nœuds lorsque ces derniers rejoignent le pool.<br/> [**Étape 4.**](#step-4-create-batch-job) Créer un **travail** Batch.<br/> [**Étape 5.**](#step-5-add-tasks-to-job) Ajouter des **tâches** au travail.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Les tâches sont planifiées pour s’exécuter sur des nœuds.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Chaque tâche télécharge ses données d’entrée depuis Stockage Azure, puis commence l’exécution.<br/> [**Étape 6.**](#step-6-monitor-tasks) Analyser les tâches.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Lorsque les tâches sont terminées, les résultats générés sont chargés dans Stockage Azure.<br/> [**Étape 7.**](#step-7-download-task-output) Télécharger la sortie des tâches à partir de Storage.

Comme indiqué précédemment, certaines solutions Batch ne suivent pas exactement cette procédure et peuvent exécuter de nombreuses autres opérations ; toutefois, cet exemple illustre les processus fréquemment inclus dans une solution Batch.

## Préparer le script client

Avant d’exécuter l’exemple, ajoutez vos informations d’identification du compte Batch et de Stockage Azure à *python\_tutorial\_client.py*. Si ce n’est déjà fait, ouvrez le fichier dans votre éditeur favori, puis mettez à jour les lignes suivantes avec vos informations d’identification.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Les informations d’identification de votre compte Batch et de votre compte de stockage figurent dans le panneau du compte de chaque service dans le [portail Azure][azure_portal] \:

![Informations d’identification Batch dans le portail][9] ![Informations d’identification de stockage dans le portail][10]<br/>

Dans les sections suivantes, nous analyserons les processus utilisés par les scripts pour traiter une charge de travail dans le service Batch. Nous vous encourageons à consulter régulièrement les scripts dans votre éditeur à mesure que vous progressez dans la lecture de cet article.

Accédez à la ligne suivante dans **python\_tutorial\_client.py** pour démarrer l’étape 1 :

```python
if __name__ == '__main__':
```

## Étape 1 : créer des conteneurs de stockage

![Créer des conteneurs dans Azure Storage][1] <br/>

Batch prend en charge l’interaction avec Azure Storage. Les conteneurs présents dans votre compte de stockage fournissent les fichiers nécessaires aux tâches s’exécutant dans votre compte Batch. Les conteneurs fournissent également un emplacement pour stocker les données de sortie générées par les tâches. Le script *python\_tutorial\_client.py* commence par créer trois conteneurs dans [Azure Blob Storage](../storage/storage-introduction.md#blob-storage) :

- **application** : ce conteneur stocke le script Python exécuté par les tâches, *python\_tutorial\_task.py*.
- **input** : les tâches téléchargeront les fichiers de données à traiter à partir du conteneur *input*.
- **output** : une fois que les tâches auront terminé de traiter les fichiers d’entrée, elles chargeront leurs résultats dans le conteneur *output*.

Pour interagir avec un compte de stockage et créer des conteneurs, nous utilisons le package [azure-storage][pypi_storage] pour créer un objet [BlockBlobService][py_blockblobservice] (le « client d’objet blob »). Ensuite, nous créons trois conteneurs dans le compte de stockage à l’aide du client d’objet blob.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Une fois les conteneurs créés, l’application peut charger les fichiers destinés à être utilisés par les tâches.

> [AZURE.TIP] [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md) offre un bon aperçu de l’utilisation des conteneurs Azure Storage et des objets blob. Il doit se trouver au début de votre liste de lecture lorsque vous commencez à travailler avec le traitement Batch.

## Étape 2 : charger les scripts de tâche et les fichiers de données

![Charger les fichiers d’application de tâche et les fichiers (de données) d’entrée dans les conteneurs][2] <br/>

Dans le cadre de l’opération de chargement des fichiers, *python\_tutorial\_client.py* commence par définir les groupes de chemins d’accès aux fichiers **application** et **input** tels qu’ils existent sur la machine locale. Il télécharge ensuite ces fichiers dans les conteneurs créés à l’étape précédente.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

À l’aide de la compréhension de liste, la fonction `upload_file_to_container` est appelée pour chaque fichier dans les groupes et les deux groupes [ResourceFile][py_resource_file] sont remplis. La fonction `upload_file_to_container` apparaît ci-dessous :

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### Objets ResourceFile

Un objet [ResourceFile][py_resource_file] fournit aux tâches de Batch l’URL d’un fichier de Stockage Azure qui est téléchargé dans un nœud de calcul avant l’exécution de ces tâches. La propriété [ResourceFile][py_resource_file].**blob\_source** spécifie l’URL complète du fichier tel qu’il existe dans Stockage Azure. L’URL peut également inclure une signature d’accès partagé (SAS) fournissant un accès sécurisé au fichier. La propriété *ResourceFiles* est utilisée par la plupart des types de tâches dans Batch :

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

Cet exemple n’utilise pas les types de tâche JobPreparationTask ou JobReleaseTask. Pour plus d’informations sur ces types de tâches, consultez la section [Exécution de tâches de préparation et de fin du travail sur les nœuds de calcul Azure Batch](batch-job-prep-release.md).

### Signature d’accès partagé (SAP)

Les signatures d’accès partagé sont des chaînes qui offrent un accès sécurisé aux conteneurs et aux objets blob dans Azure Storage. Le script *python\_tutorial\_client.py* utilise les signatures d’accès partagé des objets blob et des conteneurs et montre comment obtenir ces chaînes de signature d’accès partagé auprès du service Storage.

- **Signature d’accès partagé d’objet blob** : le type de tâche StartTask du pool utilise les signatures d’accès partagé d’objet blob lors du téléchargement des scripts de tâche et des fichiers de données d’entrée à partir de Storage (voir [l’étape 3](#step-3-create-batch-pool) ci-dessous). La fonction `upload_file_to_container` dans *python\_tutorial\_client.py* contient le code qui obtient la signature d’accès partagé de l’objet blob. Elle le fait en appelant [BlockBlobService.make\_blob\_url][py_make_blob_url] dans le module Storage.

- **Signature d’accès partagé de conteneur** : une fois que chaque tâche a mené à bien l’opération qui lui était affectée sur le nœud de calcul, elle charge son fichier de sortie dans le conteneur *output* de Stockage Azure. Pour ce faire, *python\_tutorial\_task.py* utilise une signature d’accès partagé de conteneur qui fournit l’accès en écriture au conteneur. La fonction `get_container_sas_token` dans *python\_tutorial\_client.py* obtient la signature d’accès partagé du conteneur, qui est ensuite transmise aux tâches en tant qu’argument de ligne de commande. L’étape 5, [Ajout de tâches à un travail](#step-5-add-tasks-to-job), décrit l’utilisation du SAP de conteneur.

> [AZURE.TIP] Pour plus d’informations sur la sécurisation de l’accès aux données dans votre compte de stockage, consultez la série en deux parties sur les signatures d’accès partagé, [Partie 1 : présentation du modèle SAP](../storage/storage-dotnet-shared-access-signature-part-1.md) et [Partie 2 : création et utilisation d’une signature d’accès partagé avec le service BLOB](../storage/storage-dotnet-shared-access-signature-part-2.md).

## Étape 3 : créer le pool Batch

![Créer un pool Batch][3] <br/>

Un **pool** Batch est une collection de nœuds de calcul (machines virtuelles) sur lequel Batch exécute les tâches d’un travail.

Après le chargement des tâches de script et des fichiers de données dans le compte de stockage, *python\_tutorial\_client.py* commence à interagir avec le service Batch à l’aide du module Batch Python. Pour ce faire, un [BatchServiceClient][py_batchserviceclient] est créé :

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Ensuite, un pool de nœuds de calcul est créé dans le compte Batch avec un appel à `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Lorsque vous créez un pool, vous définissez un [PoolAddParameter][py_pooladdparam] qui spécifie plusieurs propriétés pour le pool :

- L’**ID** du pool (*id* - requis)<p/> Comme pour la plupart des entités dans Batch, votre nouveau pool doit avoir un ID unique au sein de votre compte Batch. Votre code fait référence à ce pool en utilisant son ID ; c’est ainsi que vous pouvez identifier le pool dans le [portail][azure_portal] Azure.

- Le **nombre de nœuds de calcul** (*target\_dedicated* - requis)<p/> Spécifie le nombre de machines virtuelles devant être déployées dans le pool. Il est important de noter que tous les comptes Batch ont un **quota** par défaut qui limite le nombre de **cœurs** (et par conséquent, celui des nœuds de calcul) dans un compte Batch. Pour en savoir plus sur les quotas par défaut et obtenir des instructions sur comment [augmenter un quota](batch-quota-limit.md#increase-a-quota) (par exemple, le nombre maximal de cœurs dans votre compte Batch), consultez l’article [Quotas et limites pour le service Azure Batch](batch-quota-limit.md). Si vous vous demandez pourquoi votre pool n’atteint pas plus de X nœuds, ce quota de cœurs peut en être la raison.

- Le **système d’exploitation** pour les nœuds (*virtual\_machine\_configuration* **ou** *cloud\_service\_configuration* - requis)<p/> Dans *python\_tutorial\_client.py*, nous créons un pool de nœuds Linux à l’aide de [VirtualMachineConfiguration][py_vm_config]. La fonction `select_latest_verified_vm_image_with_node_agent_sku` dans `common.helpers` simplifie l’utilisation des images du [Marketplace de machines virtuelles Azure][vm_marketplace]. Pour plus d’informations sur l’utilisation des images du Marketplace, voir [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

- La **taille de nœuds de calcul** (*vm\_size* - requis)<p/> Étant donné que nous spécifions des nœuds Linux pour notre [VirtualMachineConfiguration][py_vm_config], nous spécifions une taille de machine virtuelle (`STANDARD_A1` dans cet exemple) parmi les [tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md). Pour plus d’informations, voir [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

- **Tâche de démarrage** (*start\_task* : non requis)<p/> Outre les propriétés du nœud physique citées plus haut, vous pouvez également spécifier un [StartTask][py_starttask] pour le pool (celui-ci n’est cependant pas requis). La tâche StartTask s’exécute sur chacun des nœuds rejoignant le pool, ainsi qu’à chaque redémarrage d’un nœud. StarTask est particulièrement utile pour préparer les nœuds de calcul à l’exécution des tâches, telles que l’installation des applications que vos tâches exécuteront.<p/>Dans cet exemple d’application, la tâche StartTask copie les fichiers qu’elle a téléchargés à partir de Storage (spécifiés avec la propriété **resource\_files** de StartTask) depuis le *répertoire de travail* de StartTask vers le répertoire *partagé* accessible à toutes les tâches qui s’exécutent sur le nœud. Pour l’essentiel, ceci copie `python_tutorial_task.py` sur le répertoire partagé sur chaque nœud lorsque le nœud rejoint le pool, afin que toutes les tâches qui s’exécutent sur le nœud puissent y accéder.

Vous remarquerez peut-être l’appel à la fonction d’assistance `wrap_commands_in_shell`. Cette fonction prend une collection de commandes distinctes et crée une ligne de commande unique adaptée à la propriété de ligne de commande d’une tâche.

Notez également que l’extrait de code ci-dessus utilise deux variables d’environnement dans la propriété **command\_line** de StartTask : `AZ_BATCH_TASK_WORKING_DIR` et `AZ_BATCH_NODE_SHARED_DIR`. Chaque nœud de calcul au sein d’un pool Batch est automatiquement configuré avec plusieurs variables d’environnement propres au Batch. Tout processus qui est exécuté par une tâche a accès à ces variables d’environnement.

> [AZURE.TIP] Pour plus d’informations sur les variables d’environnement disponibles sur les nœuds de calcul d’un pool Batch, ainsi que sur les répertoires de travail de tâche, voir les sections **Paramètres d’environnement des tâches** et **Fichiers et répertoires** de l’article [Vue d’ensemble des fonctionnalités d’Azure Batch](batch-api-basics.md).

## Étape 4 : créer un travail Batch

![Créer un travail Batch][4]<br/>

Un **travail** Batch constitue un ensemble de tâches et est associé à un pool de nœuds de calcul. Les tâches d’un travail s’exécutent sur les nœuds de calcul du pool associé.

Vous pouvez utiliser un travail non seulement dans le cadre de l’organisation et du suivi des tâches dans des charges de travail liées, mais également pour imposer certaines contraintes telles que la durée d’exécution maximale du travail (et, par extension, de ses tâches) et la priorité du travail par rapport aux autres travaux du compte Batch. Dans cet exemple, toutefois, le travail est uniquement associé au pool qui a été créé à l’étape 3. Aucune propriété supplémentaire n’est configurée.

Tous les travaux Batch sont associés à un pool spécifique. Cette association indique les nœuds sur lesquels les tâches du travail sont exécutées. Vous spécifiez le pool avec la propriété [PoolInformation][py_poolinfo], comme indiqué dans l’extrait de code ci-dessous.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Une fois qu’un travail a été créé, des tâches lui sont ajoutées pour mener à bien l’opération requise.

## Étape 5 : ajouter des tâches au travail

![Ajouter des tâches au travail][5]<br/> *(1) Les tâches sont ajoutées au travail, (2) les tâches sont planifiées pour s’exécuter sur les nœuds et (3) les tâches téléchargent les fichiers de données à traiter*

Les **tâches** Batch constituent les unités de travail individuelles qui s’exécutent sur les nœuds de calcul. Une ligne de commande est associée à une tâche et cette dernière exécute les scripts ou les exécutables que vous spécifiez dans la ligne de commande.

Pour mener à bien l’opération requise, il est nécessaire d’ajouter les tâches à un travail. Chaque tâche [CloudTask][py_task] est configurée par le biais d’une propriété de ligne de commande et [ResourceFiles][py_resource_file] (comme avec la tâche StartTask du pool) que la tâche télécharge dans le nœud avant l’exécution automatique de sa ligne de commande. Dans l’exemple, chaque tâche traite un seul fichier. Par conséquent, sa collection ResourceFiles contient un seul élément.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Lorsqu’elles accèdent aux variables d’environnement telles que `$AZ_BATCH_NODE_SHARED_DIR` ou exécutent une application introuvable dans le `PATH` du nœud, les lignes de commande de tâche doivent appeler l’interpréteur de commandes explicitement, comme avec `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Cette exigence n’est pas nécessaire si vos tâches exécutent une application dans le `PATH` du nœud et ne référencent aucune variable d’environnement.

Dans la boucle `for` de l’extrait de code ci-dessus, vous pouvez remarquer que la ligne de commande de la tâche est faite avec cinq arguments de ligne de commande, qui sont transmis à *python\_tutorial\_task.py* :

1. **filepath** : il s’agit du chemin d’accès local au fichier tel qu’il existe sur le nœud. Lorsque l’objet ResourceFile dans `upload_file_to_container` a été créé à l’étape 2, le nom de fichier a été utilisé pour cette propriété (le paramètre `file_path` dans le constructeur ResourceFile). Cela indique que le fichier se trouve dans le même répertoire que *python\_tutorial\_task.py* sur le nœud.

2. **numwords** : les *N* premiers mots doivent être écrits dans le fichier de sortie.

3. **storageaccount** : le nom du compte de stockage que possède le conteneur dans lequel la sortie de la tâche doit être chargée.

4. **storagecontainer** : le nom du conteneur de stockage dans lequel les fichiers de sortie doivent être chargés.

5. **sastoken** : la signature d’accès partagé (SAP) qui fournit un accès en écriture au conteneur **output** de Stockage Azure. Le script *python\_tutorial\_task.py* utilise cette signature d’accès partagé pour créer sa référence BlockBlobService. Cela fournit un accès en écriture au conteneur sans avoir recours à une clé d’accès pour le compte de stockage.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## Étape 6 : surveiller les tâches

![Surveiller les tâches][6]<br/> *Le script (1) surveille l’état d’achèvement des tâches, et (2) les tâches chargent les données de résultat dans Stockage Azure*

Lorsque les tâches sont ajoutées à un travail, elles sont automatiquement mises en file d’attente et planifiées pour s’exécuter sur les nœuds de calcul dans le pool associé au travail. Selon les paramètres que vous spécifiez, Batch gère l’ensemble des opérations de mise en file d’attente, de planification, de ré-exécution et d’administration des tâches à votre intention.

Il existe plusieurs approches pour l’exécution de la tâche d’analyse. La fonction `wait_for_tasks_to_complete` dans *python\_tutorial\_client.py* fournit un exemple simple de tâches d’analyse pour un certain état, dans ce cas, l’état [terminé][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## Étape 7 : télécharger la sortie des tâches

![Télécharger la sortie des tâches à partir de Storage][7]<br/>

Une fois le travail terminé, les données de sortie des tâches peuvent être téléchargées à partir d’Azure Storage. Cette opération s’effectue par le biais d’un appel de `download_blobs_from_container` dans *python\_tutorial\_client.py* :

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] L’appel de `download_blobs_from_container` dans *python\_tutorial\_client.py* spécifie que les fichiers doivent être téléchargés dans le répertoire de base de l’utilisateur. Vous pouvez modifier cet emplacement de sortie.

## Étape 8 : supprimer les conteneurs

Dans la mesure où les données qui résident dans Azure Storage vous sont facturées, il est toujours judicieux de supprimer tous les objets blob dont vous n’avez plus besoin pour vos travaux Batch. Dans *python\_tutorial\_client.py*, cela se fait par le biais de trois appels vers [BlockBlobService.delete\_container][py_delete_container] \:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## Étape 9 : Supprimer le travail et le pool

Au cours de la dernière étape, l’utilisateur est invité à supprimer le travail et le pool créés par le script *python\_tutorial\_client.py*. Bien que vous ne soyez pas facturé pour les travaux et les tâches à proprement parler, les nœuds de calcul vous *sont* facturés. Par conséquent, nous vous conseillons d’affecter les nœuds uniquement en fonction des besoins. La suppression des pools inutilisés peut être incluse dans votre processus de maintenance.

Les classes [JobOperations][py_job] et [PoolOperations][py_pool] de BatchServiceClient disposent toutes deux de méthodes de suppression correspondantes, appelées si l’utilisateur confirme la suppression :

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] N’oubliez pas que les ressources de calcul vous sont facturées et que la suppression des pools inutilisés vous permet de réduire les coûts. Tenez également compte du fait que la suppression d’un pool permet la suppression de tous les nœuds de calcul de ce pool, et que toutes les données sur les nœuds seront irrécupérables une fois le pool supprimé.

## Exécuter l’exemple de script

Lorsque vous exécutez le script *python\_tutorial\_client.py* du didacticiel [exemple de code][github_article_samples], la sortie de la console est identique à ce qui suit. Vous verrez une pause à `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` ; cela signifie que les nœuds de calcul du pool sont en cours de création et de démarrage et que les commandes de la tâche de démarrage du pool sont exécutées. Utilisez le [portail Azure][azure_portal] pour surveiller le pool, les nœuds de calcul, le travail et les tâches pendant et après l’exécution. Utilisez le [portail Azure][azure_portal] ou l’un des [explorateurs de stockage Microsoft Azure][storage_explorer] disponibles pour visualiser les ressources de stockage (conteneurs et objets blob) créées par l’application.

>[AZURE.TIP] Exécutez le script *python\_tutorial\_client.py* à partir du répertoire `azure-batch-samples/Python/Batch/article_samples`. Comme ce script utilise un chemin d’accès relatif pour l’importation du module `common.helpers`, il se peut que le message d’erreur `ImportError: No module named 'common'` s’affiche si vous ne l’exécutez pas à partir de ce répertoire.

Le temps d’exécution standard de l’exemple est **d’environ 5-7 minutes** avec la configuration par défaut.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## Étapes suivantes

N’hésitez pas à apporter des modifications à *python\_tutorial\_client.py* et à *python\_tutorial\_task.py* pour découvrir des scénarios de calcul différents. Essayez par exemple d’ajouter une suspension d’exécution à *python\_tutorial\_task.py* pour simuler des tâches de longue durée et les surveiller dans le portail. Essayez d’ajouter davantage de tâches ou d’ajuster le nombre de nœuds de calcul. Ajoutez une logique pour vérifier et autoriser l’utilisation d’un pool existant pour raccourcir la durée d’exécution.

À présent que vous voici familiarisé avec le flux de travail de base d’une solution Batch, il est temps pour vous d’approfondir les fonctionnalités supplémentaires du service Batch.

- Consultez l’article [Présentation des fonctionnalités de présentation d’Azure Batch](batch-api-basics.md), que nous vous recommandons si vous ne connaissez pas le service.
- Commencez par les autres articles de développement Batch sous **Développement approfondi** dans le [parcours d’apprentissage Batch][batch_learning_path].
- Consultez une autre implémentation du traitement de la charge de travail « N premiers mots » à l’aide de Batch dans l’exemple [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Créer des conteneurs dans Azure Storage"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Charger les fichiers d’application de tâche et les fichiers (de données) d’entrée dans les conteneurs"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Créer un pool Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Créer un travail Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Ajouter des tâches au travail"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Surveiller les tâches"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Télécharger la sortie des tâches à partir de Storage"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Flux de travail de la solution Batch (diagramme complet)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Informations d’identification de compte Batch dans le portail"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Informations d’identification de compte de stockage dans le portail"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Flux de travail de la solution Batch (diagramme minimal)"

<!---HONumber=AcomDC_0928_2016-->