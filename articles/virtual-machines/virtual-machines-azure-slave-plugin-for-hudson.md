<properties
	pageTitle="Utilisation du plug-in subordonné Azure avec la solution d’intégration continue Hudson | Microsoft Azure"
	description="Décrit l’utilisation du plug-in subordonné Azure avec la solution d'intégration continue Hudson"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="robmcm"/>

# Utilisation du plug-in subordonné Azure avec la solution d'intégration continue Hudson

Le plug-in subordonné Azure pour Hudson vous permet d’approvisionner des nœuds subordonnés sur Azure lors de l'exécution de builds distribués.

## Installation du plug-in subordonné Azure

1. Dans le tableau de bord Hudson, cliquez sur **Manage Hudson** (Gérer Hudson).

1. Sur la page **Manage Hudson** (Gérer Hudson), cliquez sur **Manage Plugins** (Gérer les plug-ins).

1. Cliquez sur l'onglet **Available**.

1. Cliquez sur **Search** (Rechercher) et entrez **Azure** pour limiter la liste aux plug-ins appropriés.

	Si vous choisissez de faire défiler la liste des plug-ins disponibles, vous trouverez le plug-in subordonné Azure sous la section **Cluster Management and Distributed Build** (Gestion du cluster et build distribué) dans l'onglet **Others** (Autres).

1. Cochez la case **Azure Slave Plugin** (Plug-in esclave Azure).

1. Cliquez sur **Installer**.

1. Redémarrez Hudson.

Maintenant que le plug-in est installé, les étapes suivantes consistent à le configurer avec votre profil d'abonnement Azure et à créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud subordonné.

## Configuration du plug-in subordonné Azure avec votre profil d’abonnement

Un profil d'abonnement, également appelé paramètres de publication, est un fichier XML qui contient des informations d'identification sécurisées, ainsi que des informations supplémentaires, dont vous aurez besoin pour utiliser Azure dans votre environnement de développement. Pour configurer le plug-in subordonné Azure, vous avez besoin des éléments suivants :

* Votre ID d’abonnement
* Un certificat de gestion pour votre abonnement

Vous les trouverez dans votre [profil d’abonnement]. Vous trouverez ci-dessous un exemple de profil d'abonnement.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Une fois que vous avez votre profil d'abonnement, procédez comme suit pour configurer le plug-in subordonné Azure.

1. Dans le tableau de bord Hudson, cliquez sur **Manage Hudson** (Gérer Hudson).

1. Cliquez sur **Configure System** (Configurer le système).

1. Faites défiler la page vers le bas pour trouver la section **Cloud**.

1. Cliquez sur **Add new cloud > Microsoft Azure** (Ajouter nouveau cloud > Microsoft Azure).

    ![ajouter un cloud][add new cloud]

    Cette commande affiche les champs où vous devez entrer les détails de votre abonnement.

    ![configurer le profil][configure profile]

1. Copiez l'ID d'abonnement et le certificat de gestion de votre profil d'abonnement et collez-les dans les champs appropriés.

    Lors de la copie de l'ID d'abonnement et du certificat de gestion, n'incluez **pas** les guillemets qui entourent les valeurs.

1. Cliquez sur **Verify configuration** (Vérifier la configuration).

1. Lorsque la configuration est vérifiée avec succès, cliquez sur **Save** (Enregistrer).

## Configuration d’un modèle de machine virtuelle pour le plug-in subordonné Azure

Un modèle de machine virtuelle définit les paramètres qui seront utilisés par le plug-in pour créer un nœud subordonné dans Azure. Dans les étapes suivantes, nous allons créer un modèle pour un ordinateur virtuel Ubuntu.

1. Dans le tableau de bord Hudson, cliquez sur **Manage Hudson** (Gérer Hudson).

1. Cliquez sur **Configure System** (Configurer le système).

1. Faites défiler la page vers le bas pour trouver la section **Cloud**.

1. Dans la section **Cloud**, recherchez **Add Azure Virtual Machine Template** (Ajouter modèle d'ordinateur virtuel Azure) et cliquez sur le bouton **Add** (Ajouter).

    ![ajouter un modèle d'ordinateur virtuel][add vm template]

1. Spécifiez un nom de service cloud dans le champ **Name** (Nom). Si le nom spécifié fait référence à un service cloud existant, l'ordinateur virtuel sera déployé dans ce service. Dans le cas contraire, Azure en crée un nouveau.

1. Dans le champ **Description**, entrez du texte qui décrit le modèle que vous créez. Ces informations sont uniquement à des fins documentaires et ne sont pas utilisées dans la mise en service d'un ordinateur virtuel.

1. Dans le champ **Labels** (Étiquettes), entrez **linux**. Cette étiquette est utilisée pour identifier le modèle que vous créez. Elle est ensuite utilisée pour référencer le modèle lors de la création d'un travail Hudson.

1. Sélectionnez une région où l'ordinateur virtuel sera créé.

1. Sélectionnez la taille appropriée pour l'ordinateur virtuel.

1. Spécifiez un compte de stockage dans lequel l'ordinateur virtuel sera créé. Assurez-vous qu'il se trouve dans la même région que le service Cloud que vous allez utiliser. Si vous souhaitez créer un nouveau stockage, vous pouvez laisser ce champ vide.

1. La durée de conservation spécifie le nombre de minutes avant la suppression d'un esclave inactif par Hudson. Conservez la valeur par défaut de 60.

1. Dans **Usage** (Utilisation), sélectionnez la condition appropriée lorsque ce nœud subordonné sera utilisé. Pour l'instant, sélectionnez **Utilize this node as much as possible** (Utiliser ce nœud autant que possible).

    À ce stade, votre formulaire doit ressembler à ce qui suit :

    ![configuration du modèle][template config]

1. Dans **Image Family or Id** (Famille d'images ou ID), vous devez spécifier l'image système qui sera installée sur votre ordinateur virtuel. Vous pouvez faire votre choix dans une liste de familles d'images ou spécifier une image personnalisée.

    Si vous souhaitez faire votre choix dans une liste de familles d'images, entrez le premier caractère (respectez la casse) du nom de famille de l'image. Par exemple, le fait de taper **U** affichera une liste des familles de serveurs Ubuntu. Une fois que vous avez fait votre choix dans la liste, Hudson utilisera la version la plus récente de cette image système de cette famille lors de la mise en service de l'ordinateur virtuel.

    ![liste de famille de système d'exploitation][OS family list]

    Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez son nom. Les noms des images personnalisées ne figurent pas dans une liste. Vous devez donc vous assurer que le nom est entré correctement.

    Pour ce didacticiel, tapez **U** pour afficher une liste d'images Ubuntu et sélectionnez **Ubuntu Server 14.04 LTS**.

1. Pour la **méthode de lancement**, sélectionnez **SSH**.

1. Copiez le script ci-dessous et collez-le dans le champ du **script init**.

		# Install Java

		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk

		# Install git

		sudo apt-get install -y git

		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

    Le **script init** sera exécuté une fois l'ordinateur virtuel créé. Dans cet exemple, le script installe Java, git et ant.

1. Dans les champs **Username** (Nom d'utilisateur) et **Password** (Mot de passe), saisissez les valeurs de votre choix pour le compte administrateur qui sera créé sur l'ordinateur virtuel.

1. Cliquez sur **Verify Template** (Vérifier le modèle) pour vérifier si les paramètres spécifiés sont valides.

1. Cliquez sur **Save** (Enregistrer).

## Créer un travail Hudson qui s'exécute sur un nœud subordonné sur Azure

Dans cette section, vous allez créer un travail Hudson qui s'exécutera sur un nœud subordonné sur Azure.

1. Dans le tableau de bord Hudson, cliquez sur **New Job** (Nouveau travail).

1. Entrez un nom pour le travail que vous créez.

1. Pour le type de travail, sélectionnez **Build a free-style software job** (Générer un travail logiciel libre).

1. Cliquez sur **OK**.

1. Dans la page de configuration du travail, sélectionnez **Restrict where this project can be run** (Limiter où ce projet peut être exécuté).

1. Sélectionnez **Node and label menu** (Menu nœud et étiquette) et sélectionnez **linux** (nous avons spécifié cette étiquette lors de la création du modèle d'ordinateur virtuel dans la section précédente).

1. Dans la section **Build**, cliquez sur **Add build step** (Ajouter une étape de build) et sélectionnez **Execute shell** (Exécuter shell).

1. Modifiez le script suivant, en remplaçant **{nom de votre compte github}**, **{nom de votre projet}** et **{répertoire de votre projet}** par les valeurs appropriées et collez le script modifié dans la zone de texte qui s’affiche.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e {your project directory} ]; then

  			cd {your project directory}

  			git pull origin master

		else

  			git clone https://github.com/{your github account name}/{your project name}.git

		fi

		# change directory to project

		cd $currentDir/{your project directory}

		#Execute build task

		ant

1. Cliquez sur **Save** (Enregistrer).

1. Dans le tableau de bord Hudson, recherchez le travail que vous venez de créer, puis cliquez sur l'icône **Schedule a build** (Planifier un build).

Hudson crée ensuite un nœud subordonné à l'aide du modèle créé dans la section précédente, puis il exécute le script que vous avez spécifié dans l'étape de build pour ce travail.

## Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

<!-- URL List -->

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[profil d’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

<!---HONumber=AcomDC_0921_2016-->