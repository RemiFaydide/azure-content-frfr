<properties
	pageTitle="Utilisation du plug-in subordonné Azure avec la solution d’intégration continue Jenkins | Microsoft Azure"
	description="Décrit comment utiliser le plug-in subordonné Azure avec la solution d’intégration continue Jenkins"
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

# Utilisation du plug-in subordonné Azure avec la solution d’intégration continue Jenkins

Vous pouvez utiliser le plug-in subordonné Azure pour Jenkins pour mettre en service des nœuds subordonnés sur Azure lors de l’exécution de builds distribués.

## Installation du plug-in subordonné Azure

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins** (Gérer Jenkins).

1. Sur la page **Manage Jenkins**, cliquez sur **Manage Plugins**.

1. Cliquez sur l'onglet **Available**.

1. Dans le champ de filtre au-dessus de la liste des plug-ins disponibles, tapez **Azure** pour limiter la liste aux plug-ins appropriés.

    Si vous choisissez de faire défiler la liste des plug-ins disponibles, vous trouverez le plug-in subordonné Azure dans la section **Cluster Management and Distributed Build**.

1. Cochez la case **Azure Slave Plugin**.

1. Cliquez sur **Install without restart** (Installer sans redémarrer) ou sur **Download now and install after restart** (Télécharger maintenant et installer après redémarrage).

Maintenant que le plug-in est installé, les étapes suivantes consistent à le configurer avec votre profil d’abonnement Azure et à créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud subordonné.


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

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Une fois que vous avez votre profil d’abonnement, procédez comme suit pour configurer le plug-in subordonné Azure :

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins** (Gérer Jenkins).

1. Cliquez sur **Configure System** (Configurer le système).

1. Faites défiler la page vers le bas pour trouver la section **Cloud**.

1. Cliquez sur **Add new cloud > Microsoft Azure** (Ajouter nouveau cloud > Microsoft Azure).

    ![section cloud][cloud section]

    Cette commande affiche les champs où vous devez entrer les détails de votre abonnement.

    ![configuration de l'abonnement][subscription configuration]

1. Copiez les valeurs de l'ID d'abonnement et du certificat de gestion de votre profil d'abonnement et collez-les dans les champs appropriés.

    Lors de la copie de l’ID d’abonnement et du certificat de gestion, n’incluez pas les guillemets qui entourent les valeurs.

1. Cliquez sur **Verify configuration**.

1. Lorsque la configuration est vérifiée comme étant correcte, cliquez sur **Save** (Enregistrer).

## Configuration d’un modèle de machine virtuelle pour le plug-in subordonné Azure

Un modèle de machine virtuelle définit les paramètres qui seront utilisés par le plug-in pour créer un nœud subordonné sur Azure. Dans les étapes suivantes, nous allons créer un modèle pour une machine virtuelle Ubuntu.

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins** (Gérer Jenkins).

1. Cliquez sur **Configure System** (Configurer le système).

1. Faites défiler la page vers le bas pour trouver la section **Cloud**.

1. Dans la section **Cloud**, recherchez **Add Azure Virtual Machine Template** et cliquez sur **Add**.

    ![ajouter un modèle d'ordinateur virtuel][add vm template]

    Cette commande affiche les champs où vous entrez des informations sur le modèle que vous créez.

    ![configuration générale vide][blank general configuration]

1. Dans la zone **Name**, entrez un nom de service cloud Azure. Si le nom entré fait référence à un service cloud existant, la machine virtuelle sera déployée dans ce service. Dans le cas contraire, Azure en crée un nouveau.

1. Dans la zone **Description**, entrez du texte décrivant le modèle que vous créez. Ces informations sont uniquement destinées à vos dossiers et ne sont pas utilisées dans la mise en service d’une machine virtuelle.

1. La zone **Labels** est utilisée pour identifier le modèle que vous créez. Elle est ensuite utilisée pour référencer le modèle lors de la création d’une tâche Jenkins. Dans notre cas, entrez **linux** dans cette zone.

1. Dans la liste **Region**, cliquez sur la région où la machine virtuelle sera créée.

1. Dans **Virtual Machine Size**, cliquez sur la taille appropriée.

1. Dans la zone **Storage Account Name**, spécifiez un compte de stockage où la machine virtuelle sera créée. Assurez-vous qu'il se trouve dans la même région que le service Cloud que vous allez utiliser. Si vous souhaitez créer un stockage, vous pouvez laisser cette zone vide.

1. La durée de conservation spécifie le nombre de minutes avant la suppression d'un esclave inactif par Jenkins. Conservez la valeur par défaut de 60. Vous pouvez également choisir d’arrêter le subordonné plutôt que de le supprimer lorsqu’il est inactif. Pour ce faire, cochez la case **Shutdown Only (Do Not Delete) After Retention Time**.

1. Dans la liste **Usage**, cliquez sur la condition appropriée lorsque ce nœud subordonné sera utilisé. Pour l’instant, cliquez sur **Utilize this node as much as possible**.

    À ce stade, votre formulaire doit ressembler à ce qui suit :

    ![configuration de modèle général de point de contrôle][checkpoint general template config]

    L’étape suivante consiste à fournir des détails concernant l’image du système d’exploitation dans laquelle vous voulez créer le subordonné.

1. Dans **Image Family or Id**, vous devez spécifier l’image système qui sera installée sur votre machine virtuelle. Vous pouvez faire votre choix dans une liste de familles d'images ou spécifier une image personnalisée.

    Si vous souhaitez faire votre choix dans une liste de familles d'images, entrez le premier caractère (respectez la casse) du nom de famille de l'image. Par exemple, le fait de taper **U** affichera une liste des familles de serveurs Ubuntu. Une fois que vous avez fait votre choix dans la liste, Jenkins utilisera la dernière version de cette image système de cette famille lors de la mise en service de votre machine virtuelle.

    ![exemple de liste d'images de système d'exploitation][OS Image list sample]

    Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez son nom. Les noms des images personnalisées ne figurent pas dans une liste. Vous devez donc vous assurer que le nom est entré correctement.

    Pour ce didacticiel, tapez **U** pour afficher une liste d’images Ubuntu, puis cliquez sur **Ubuntu Server 14.04 LTS**.

1. Dans la liste **Launch Method**, cliquez sur **SSH**.

1. Copiez le script ci-dessous et collez-le dans la zone **Init Script**.

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

    Le script d’initialisation sera exécuté une fois que la machine virtuelle sera créée. Dans cet exemple, le script installe Java, Git et ant.

1. Dans les zones **Username** et **Password**, saisissez les valeurs de votre choix pour le compte administrateur qui sera créé sur votre machine virtuelle.

1. Cliquez sur **Verify Template** pour vérifier si les paramètres spécifiés sont valides.

1. Cliquez sur **Save**.


## Créer un travail Jenkins qui s'exécute sur un nœud subordonné sur Azure

Dans cette section, vous allez créer un travail Jenkins qui s'exécutera sur un nœud subordonné sur Azure. Vous devrez disposer de votre propre projet sur GitHub pour suivre la procédure.

1. Dans le tableau de bord Jenkins, cliquez sur **New Item** (Nouvel élément).

1. Entrez un nom pour le travail que vous créez.

1. Pour le type de projet, cliquez sur **Freestyle project** .

1. Cliquez sur **OK**.

1. Dans la page de configuration du travail, sélectionnez **Restrict where this project can be run** (Limiter où ce projet peut être exécuté).

1. Dans la zone **Label Expression**, entrez **linux**. Dans la section précédente, nous avons créé un modèle subordonné nommé **linux** qui correspond à ce que nous définissons ici.

1. Dans la section **Build**, cliquez sur **Add build step** (Ajouter une étape de build) et sélectionnez **Execute shell** (Exécuter shell).

1. Modifiez le script suivant, en remplaçant **(nom de votre compte GitHub)**, **(nom de votre projet)** et **(répertoire de votre projet)** par les valeurs appropriées et collez le script modifié dans la zone de texte qui s’affiche.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your GitHub account name)/(your project name).git

		fi

		# change directory to project

		cd $currentDir/(your project directory)

		#Execute build task

		ant

1. Cliquez sur **Save**.

1. Dans le tableau de bord Jenkins, survolez la tâche que vous venez de créer, puis cliquez sur la flèche du menu déroulant pour afficher les options de la tâche.

1. Cliquez sur **Build now**.

Jenkins crée ensuite un nœud subordonné à l’aide du modèle créé dans la section précédente, puis il exécute le script que vous avez spécifié dans l’étape de build pour cette tâche.

## Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

<!-- URL List -->

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[profil d’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png

<!---HONumber=AcomDC_0921_2016-->