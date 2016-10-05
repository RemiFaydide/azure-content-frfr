<properties
    pageTitle="Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage | Microsoft Azure"
    description="Découvrez comment utiliser l’interface de ligne de commande de Microsoft Azure (CLI) avec Microsoft Azure Storage pour créer et gérer des comptes de stockage et utiliser des fichiers et objets blob Microsoft Azure. L’interface de ligne de commande Azure est un outil multiplateforme. "
    services="storage"
    documentationCenter="na"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="micurd;tamram"/>

# Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage

## Vue d'ensemble

La CLI Microsoft Azure offre un ensemble de commandes multiplateforme open source pour l’utilisation de la plateforme Microsoft Azure. Elle offre des fonctionnalités similaires à celles du [portail Azure](https://portal.azure.com), ainsi que des fonctions étendues d’accès aux données.

Dans ce guide, nous allons explorer l’utilisation de l’[interface de ligne de commande Azure](../xplat-cli-install.md) pour effectuer diverses tâches de développement et d’administration avec Microsoft Azure Storage. Nous vous recommandons de télécharger et d’installer ou de mettre à niveau la CLI Azure vers la dernière version avant d’utiliser ce guide.

Ce guide part du principe que vous comprenez les concepts de base de Microsoft Azure Storage. Il contient un certain nombre de scripts, qui illustrent l’utilisation de la CLI Azure avec Microsoft Azure Storage. N’oubliez pas de mettre à jour les variables du script en fonction de votre configuration avant d’exécuter ce dernier.

> [AZURE.NOTE] Le guide fournit des exemples de commande et de script de la CLI Azure pour les comptes de stockage en mode classique. Pour obtenir la liste des commandes de CLI Azure pour les comptes de stockage en mode Resource Manager, voir [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects).

## Prise en main de Microsoft Azure Storage et de la CLI Azure en 5 minutes

Ce guide inclut des exemples basés sur Ubuntu, mais les résultats devraient être les mêmes sur d’autres plates-formes.

**Nouveautés de Microsoft Azure :** obtenez un abonnement à Microsoft Azure, ainsi qu’un compte Microsoft associé. Pour en savoir plus sur les options d’achat de Microsoft Azure, voir [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/), [Modes d’achat d’Azure](https://azure.microsoft.com/pricing/purchase-options/) et [Offres spéciales membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network et BizSpark, ainsi que d’autres programmes Microsoft).

Pour plus d’informations sur les abonnements Azure, consultez la section [Attribution de rôles d’administrateur dans Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**Une fois le compte et l’abonnement à Microsoft Azure créés :**

1. Téléchargez et installez la CLI Azure en suivant les instructions fournies dans la section [Installer la CLI Azure](../xplat-cli-install.md).
2. Une fois la CLI Azure installée, vous serez à même d’utiliser la commande azure à partir de l’interface de ligne de commande (invite de ligne de commande, interpréteur de commandes, terminal) afin d’accéder aux commandes de la CLI Azure. Saisissez la commande `azure`. La sortie suivante doit s’afficher.

    ![Sorte de commande Microsoft Azure][Image1]

3. Dans l’interface de ligne de commande, saisissez `azure storage` pour répertorier toutes les commandes Microsoft Azure Storage et obtenir une première impression des fonctionnalités fournies par l’interface CLI Azure. Vous pouvez saisir le nom de la commande avec le paramètre **-h** (par exemple, `azure storage share create -h`) pour afficher les détails de la syntaxe de commande.
4. À présent, voici un script simple, qui affiche les commandes de l’API Azure de base permettant d’accéder à Azure Storage. Ce script vous demande tout d’abord de définir deux variables pour votre clé et votre compte de stockage. Ensuite, le script crée un conteneur dans ce nouveau compte, puis charge un fichier image existant (blob) dans ce conteneur. Une fois qu’il a répertorié tous les objets blob de ce conteneur, il télécharge le fichier image dans le répertoire de destination existant sur l’ordinateur local.

		#!/bin/bash
		# A simple Azure storage example

		export AZURE_STORAGE_ACCOUNT=<storage_account_name>
		export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

		export container_name=<container_name>
		export blob_name=<blob_name>
		export image_to_upload=<image_to_upload>
		export destination_folder=<destination_folder>

		echo "Creating the container..."
		azure storage container create $container_name

		echo "Uploading the image..."
		azure storage blob upload $image_to_upload $container_name $blob_name

		echo "Listing the blobs..."
		azure storage blob list $container_name

		echo "Downloading the image..."
		azure storage blob download $container_name $blob_name $destination_folder

		echo "Done"

5. Sur ce dernier, ouvrez l’éditeur de texte de votre choix (vim, par exemple). Saisissez le script ci-dessus dans cet éditeur.

6. Maintenant, vous devez mettre à jour les variables du script en fonction de vos paramètres de configuration.

    - **<nom\_compte\_stockage>** : utilisez le nom donné dans le script ou entrez un nouveau nom pour votre compte de stockage. **Important :** le nom du compte de stockage doit être unique dans Microsoft Azure. Il doit également inclure des minuscules uniquement.

    - **<clé\_compte\_stockage>** : clé d’accès associée à votre compte de stockage.

    - **<nom\_conteneur>** : utilisez le nom donné dans le script ou entrez un nouveau nom pour votre conteneur.

    - **<image\_à\_télécharger>** : entrez le chemin d’accès à une image sur votre ordinateur local, par exemple « ~/images/HelloWorld.png ».

    - **<dossier\_destination>** : entrez un chemin d’accès à un répertoire local pour stocker les fichiers téléchargés à partir d’Azure Storage, par exemple « ~/downloadImages ».

7. Une fois que vous avez mis à jour les variables nécessaires dans vim, appuyez sur les combinaisons de touches « Échap, :, wq! » pour enregistrer le script.

8. Pour exécuter ce script, saisissez simplement le nom de fichier correspondant dans la console de l’interpréteur de commandes. Une fois ce script exécuté, vous devez disposer d’un répertoire de destination local incluant le fichier image téléchargé. La capture d’écran suivante présente un exemple de cette opération :

Après l’exécution du script, vous devez avoir un dossier local de destination qui inclut le fichier image téléchargé.

## Gérer les comptes de stockage avec la CLI Azure

### Connectez-vous à un abonnement Azure

Bien que la plupart des commandes de stockage fonctionnent sans abonnement à Azure, nous vous recommandons de vous connecter à votre abonnement à partir de l’interface CLI Azure. Pour configurer l’interface de ligne de commande Azure avec votre abonnement, suivez les étapes indiquées dans [Se connecter à un abonnement Azure à partir de l’interface CLI Azure](../xplat-cli-connect.md).

### Création d’un nouveau compte de stockage

Pour utiliser Azure Storage, vous avez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour qu’il se connecte à votre abonnement.

        azure storage account create <account_name>

Le nom du compte de stockage doit inclure entre 3 et 24 caractères et utiliser uniquement des lettres en minuscules et des nombres.

### Définir un compte Azure Storage par défaut dans les variables d’environnement

Vous pouvez disposer de plusieurs comptes de stockage dans votre abonnement. Vous pouvez en choisir un et le définir comme compte de stockage par défaut pour toutes les commandes de stockage, au cours de la même session, dans les variables d’environnement. Cela vous permet d’exécuter les commandes de stockage de la CLI Azure sans spécifier de manière explicite la clé et le compte de stockage.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Vous pouvez également définir un compte de stockage par défaut via la chaîne de connexion. Commencez par obtenir cette dernière, via la commande :

        azure storage account connectionstring show <account_name>

Ensuite, copiez la chaîne de connexion de sortie, puis associez-lui la variable d’environnement :

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## Créer et gérer des objets blob

Le stockage d’objets blob Azure est un service permettant de stocker de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts du stockage d’objets blob Microsoft Azure. Pour obtenir des informations détaillées, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) et [Concepts de service Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### Créer un conteneur

Chaque objet blob du stockage Azure doit se trouver dans un conteneur. Vous pouvez créer un conteneur privé à l’aide de la commande `azure storage container create` :

        azure storage container create mycontainer

> [AZURE.NOTE] Il existe trois niveaux d’accès en lecture anonyme : **Désactivé**, **Blob** et **Conteneur**. Pour empêcher tout accès anonyme aux objets blob, définissez le paramètre Autorisation sur **Désactivé**. Par défaut, le nouveau conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser les accès anonymes publics en lecture aux ressources blob, mais non aux métadonnées du conteneur ou à la liste d’objets blob du conteneur, définissez le paramètre Autorisation sur **Blob**. Pour autoriser les accès anonymes publics complets aux ressources blob, aux métadonnées du conteneur et à la liste d’objets blob du conteneur, définissez le paramètre Autorisation sur **Conteneur**. Pour plus d’informations, consultez la section [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md).

### Charger un objet blob dans un conteneur

Le service de stockage d’objets blob Azure prend en charge les objets blob de blocs et de page. Pour plus d’informations, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pour télécharger des objets blob dans un conteneur, vous pouvez utiliser la fonction de `azure storage blob upload`. Par défaut, cette commande charge les fichiers locaux vers un objet blob de blocs. Pour spécifier le type de l’objet blob, vous pouvez utiliser le paramètre `--blobtype`.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### Télécharger des objets blob depuis un conteneur

L’exemple suivant indique comment télécharger des objets blob depuis un conteneur.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### Copier des objets blob

Vous pouvez copier des objets blob au sein d’un compte de stockage, ou vers des comptes de stockage et des régions et ce, de façon asynchrone.

L’exemple suivant indique comment copier des objets blob depuis un compte de stockage et les coller dans un autre. Dans cet exemple, nous créons un conteneur incluant des objets blob accessibles de manière publique et anonyme.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Cet exemple repose sur une copie asynchrone. Vous pouvez surveiller l’état de chaque opération de copie en exécutant l’opération `azure storage blob copy show`.

Remarque : l’URL source fournie pour l’opération de copie doit être accessible au public, ou inclure un jeton de signature d’accès partagé.

### Supprimer un objet blob

Pour supprimer un objet blob, utilisez la commande ci-dessous :

        azure storage blob delete mycontainer myBlockBlob2

## Créer et gérer des partages de fichiers

Le stockage de fichiers Azure propose un stockage partagé pour les applications utilisant le protocole SMB. Les services cloud et les machines virtuelles Microsoft Azure, ainsi que les applications locales, peuvent partager des données de fichiers via des partages montés. Vous pouvez gérer des partages de fichiers et des données de fichiers via la CLI Azure. Pour plus d’informations sur Azure File Storage, consultez [Prise en main d’Azure File Storage sur Windows](storage-dotnet-how-to-use-files.md) ou [Utilisation d’Azure File Storage sous Linux](storage-how-to-use-files-linux.md).

### Créer un partage de fichiers

Un partage de fichiers Azure est un partage de fichiers SMB dans Microsoft Azure. Tous les répertoires et fichiers doivent être créés dans un partage de fichiers. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage. L’exemple suivant détaille la création d’un partage de fichiers nommé **MonPartage**.

        azure storage share create myshare

### Créer un répertoire

Un répertoire fournit une structure hiérarchique facultative pour un partage de fichiers Microsoft Azure. L’exemple suivant crée un répertoire nommé **MonRép** dans le partage de fichiers.

        azure storage directory create myshare myDir

Remarque : ce chemin d’accès au répertoire peut inclure plusieurs niveaux, *par exemple* : **a/b**. Cependant, vous devez vous assurer que tous les répertoires parents existent. Par exemple, pour le chemin d’accès **a/b**, vous devez créer le répertoire **a**, puis le répertoire **b**.

### Charger un fichier local vers le répertoire

Dans l’exemple suivant, un fichier est chargé à partir de l’emplacement **~/temp/samplefile.txt** vers le répertoire **MonRép**. Modifiez le chemin d’accès du fichier de façon à ce qu’il désigne un fichier valide sur votre ordinateur local :

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Remarque : dans le partage, un fichier peut présenter une taille maximale d’1 To.

### Répertorier les fichiers du répertoire ou de la racine du partage

Vous pouvez répertorier les fichiers et sous-répertoires de la racine d’un partage, à l’aide de la commande suivante :

        azure storage file list myshare myDir

Notez que le nom du répertoire est facultatif pour l’opération de création de liste. En cas d’omission, la commande répertorie le contenu du répertoire racine du partage.

### Copie des fichiers

Depuis la version 0.9.8 de l’interface de ligne de commande Azure, vous pouvez copier un fichier dans un autre, un fichier dans un objet blob ou un objet blob dans un fichier. Ci-dessous, nous montrons comment effectuer ces opérations de copie à l’aide de commandes de l’interface de ligne de commande. Copie d'un fichier vers le nouveau répertoire :

	azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Copie d’un objet blob vers un répertoire de fichiers :

	azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## Étapes suivantes

Pour en savoir plus sur Azure Storage, consultez les articles et ressources suivants :

- [Documentation d’Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
- [Référence de l’API REST des services de stockage](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png

<!---HONumber=AcomDC_0921_2016-->