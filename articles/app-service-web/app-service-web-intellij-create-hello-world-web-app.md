<properties 
	pageTitle="Créer une application web « Hello World » pour Azure dans IntelliJ | Microsoft Azure" 
	description="Ce didacticiel vous montre comment utiliser le Kit de ressources Azure pour IntelliJ pour créer une application web Hello World pour Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="asirveda;robmcm"/>

# Créer une application web « Hello World » pour Azure dans IntelliJ

Ce didacticiel explique comment créer une application Hello World de base et la déployer sur Azure en tant qu’application web à l’aide du [Kit de ressources Azure pour IntelliJ]. Un exemple JSP de base est présenté par souci de simplicité, mais des étapes très similaires conviennent également pour un servlet Java en ce qui concerne le déploiement d’Azure.

À la fin de ce didacticiel, votre application ressemble à l’illustration suivante quand vous l’affichez dans un navigateur web :

![][01]
 
## Composants requis

* JDK (Java Development Kit) version 1.8 ou ultérieure.
* IntelliJ IDEA édition Ultimate. Vous pouvez le télécharger à partir de <https://www.jetbrains.com/idea/download/index.html>.
* Une distribution d’un serveur web ou d’un serveur d’applications basé sur Java, comme Apache Tomcat ou Jetty.
* Un abonnement à Azure, pouvant être souscrit à l’adresse <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Installing the Azure Toolkit for IntelliJ] \(Installation du kit de ressources Azure pour IntelliJ).

## Pour créer une application Hello World

Tout d’abord, nous allons commencer par créer un projet Java.

1. Démarrer IntelliJ, puis dans la barre de menus, cliquez sur **File** (Fichier), sur **New** (Nouveau), puis cliquez sur **Project** (Projet).

   ![][02]

1. Dans la boîte de dialogue New Project (Nouveau projet), sélectionnez **Java**, puis **Web Application** (Application web), puis cliquez sur **Next** (Suivant).

   ![][03a]

   Si vous êtes invité à continuer sans SDK affecté, cliquez sur **Yes** (Oui).

   ![][03b]

1. Pour les besoins de ce didacticiel, nommez le projet **Java-Web-App-On-Azure**, puis cliquez sur **Terminer** (Finish).

   ![][04]

1. Dans l’affichage de l’explorateur de projets d’IntelliJ, développez **Java-Web-App-On-Azure**, puis développez **web** et double-cliquez sur **index.jsp**.

   ![][05]

1. Quand votre fichier index.jsp s’ouvre dans IntelliJ, ajoutez un texte pour afficher dynamiquement **Hello World!** dans l’élément `<body>` existant. Le contenu `<body>` mis à jour doit ressembler à l’exemple suivant :

   `<body><b><% out.println("Hello World!"); %></b></body>`

1. Enregistrez index.jsp.

## Pour déployer votre application sur un conteneur d’application web Azure

Vous pouvez déployer une application web Java sur Azure de plusieurs façons. Ce didacticiel décrit l’une des plus simples : votre application est déployée sur un conteneur d’application web Azure ; ainsi, aucun type de projet spécifique ni outil supplémentaire n’est nécessaire. Le JDK et le logiciel du conteneur web vous étant fournis par Azure, vous n’avez pas besoin de charger les vôtres ; vous devez uniquement être en possession de votre application web Java. Ainsi, le processus de publication de votre application ne prend que quelques secondes.

1. Dans l’explorateur de projets d’IntelliJ, cliquez avec le bouton droit sur le projet **Java-Web-App-On-Azure**. Quand le menu contextuel apparaît, sélectionnez **Azure**, puis cliquez sur **Publish as Azure Web App...** (Publier comme application web Azure...).

   ![][06]

1. Si vous n’êtes pas encore connecté à Azure à partir d’IntelliJ, vous êtes invité à vous connecter à votre compte Azure :

   ![][07]

   Remarque : si vous avez plusieurs comptes Azure, certaines des invites du processus de connexion peuvent s’afficher plusieurs fois, même si elles semblent être identiques. Dans ce cas, continuez à suivre les instructions de connexion.

1. Une fois que vous êtes connecté à votre compte Azure, la boîte de dialogue **Gérer les abonnements** affiche la liste des abonnements associés à vos informations d’identification. Si plusieurs abonnements sont répertoriés et que vous ne souhaitez utiliser qu’une partie d’entre eux, vous pouvez éventuellement désélectionner ceux qui ne vous intéressent pas. Quand vous avez sélectionné vos abonnements, cliquez sur **Fermer**.

   ![][08]

1. Quand la boîte de dialogue **Deploy to Azure Web App Container** (Déployer sur le conteneur d’application web Azure) s’affiche, elle présente tous les conteneurs d’application web déjà créés ; si vous n’avez pas créé de conteneur, la liste est vide.

   ![][09]

1. Si vous n’avez pas déjà créé de conteneur d’application web Azure ou que vous souhaitez publier votre application dans un nouveau conteneur, procédez comme suit. Sinon, sélectionnez un conteneur d’application web existant et passez à l’étape 6 ci-dessous.

  1. Cliquez sur **+**.

        ![][10]

  1. La boîte de dialogue **New Web App Container** (Nouveau conteneur d’application web) s’affiche, que nous allons utiliser au cours des prochaines étapes.

        ![][11]

  1. Entrez un **nom DNS** pour votre conteneur d’application web ; celui-ci constitue le nom DNS feuille de l’URL hôte de votre application web dans Azure. Remarque : Le nom doit être disponible et conforme aux exigences d’affectation de noms pour les applications web Azure.

  1. Dans le menu déroulant **Web Container** (Conteneur d’application), sélectionnez le logiciel approprié pour votre application.

        Pour le moment, vous pouvez choisir entre Tomcat 8, Tomcat 7 ou Jetty 9. Une distribution récente du logiciel sélectionné sera fournie par Azure, et il s’exécutera sur une distribution récente de JDK 8 créée par Oracle et fournie par Azure.

  1. Dans le menu déroulant **Subscription** (Abonnement), sélectionnez l’abonnement à utiliser pour ce déploiement.

  1. Dans le menu déroulant **Resource Group** (Groupe de ressources), sélectionnez le groupe de ressources auquel vous souhaitez associer votre application web.

        Remarque : les groupes de ressources Azure permettent de regrouper les ressources associées afin de pouvoir, par exemple, les supprimer simultanément.

        Vous pouvez sélectionner un groupe de ressources existant (le cas échéant) et passer directement à l’étape G ou suivre les étapes ci-dessous pour créer un groupe de ressources :

      * Cliquez sur **New...** (Nouveau...)

      * La boîte de dialogue **New Resource Group** (Nouveau groupe de ressources) s’affiche :

            ![][12]

      * Dans la zone de texte **Name** (Nom), spécifiez un nom pour votre nouveau groupe de ressources.

      * Dans le menu déroulant **Region** (Région), sélectionnez l’emplacement de centre de données Azure approprié pour votre groupe de ressources.

      * Cliquez sur **OK**.

  1. Le menu déroulant **App Service Plan** (Plan de Service d’application) répertorie les plans de service d’application qui sont associés au groupe de ressources que vous avez sélectionné.

        Remarque : un plan App Service spécifie des informations telles que l’emplacement de votre application web, le niveau tarifaire et la taille d’instance de calcul. Un seul plan App Service peut être utilisé pour plusieurs Web Apps. Pour cette raison, il est stocké séparément d’un déploiement d’application web spécifique.

        Vous pouvez sélectionner un plan App Services existant (le cas échéant) et passer directement à l’étape H ou suivre les étapes ci-dessous pour créer un plan App Service :

      * Cliquez sur **New...** (Nouveau...)

      * La boîte de dialogue **New App Service Plan** (Nouveau plan de Service d’application) s’affiche :

            ![][13]

      * Dans la zone de texte **Name** (Nom), spécifiez un nom pour votre nouveau plan de service d’application.

      * Dans le menu déroulant **Location** (Emplacement), sélectionnez l’emplacement de centre de données Azure approprié pour le plan.

      * Dans le menu déroulant **Pricing Tier** (Niveau de tarification), sélectionnez la tarification appropriée pour le plan. À des fins de test, vous pouvez choisir **Free** (Gratuit).

      * Dans le menu déroulant **Instance Size** (Taille de l’instance), sélectionnez la taille d’instance appropriée pour le plan. À des fins de test, vous pouvez choisir **Small** (Petite).

  1. Une fois effectuées toutes les étapes ci-dessus, la boîte de dialogue New Web App Container doit ressembler à ceci :

        ![][14]

  1. Cliquez sur **OK** pour terminer la création de votre conteneur d’application web.

        Attendez quelques secondes pour que la liste des conteneurs d’application web s’actualise. Votre conteneur d’application web nouvellement créée doit maintenant être sélectionné dans la liste.

1. Vous êtes maintenant prêt à effectuer le déploiement initial de votre application web dans Azure ; cliquez sur **OK** pour déployer votre application Java sur le conteneur d’application web sélectionné.

    ![][15]

    Remarque : Par défaut, votre application est déployée en tant que sous-répertoire du serveur d’applications. Si vous voulez qu’elle soit déployée en tant qu’application racine, cochez la case **Deploy to root** (Déployer sur la racine) avant de cliquer sur **OK**.

1. Ensuite, la vue **Journaux d’activité** doit apparaître et indiquer l’état du déploiement de votre application web.

    ![][16]

    Le processus de déploiement de votre application web sur Azure doit prendre seulement quelques secondes. Quand votre application est prête, un lien nommé **Publié** apparaît dans la colonne **État**. Quand vous cliquez sur le lien, vous êtes redirigé vers la page d’accueil de votre application web déployée, à laquelle vous pouvez accéder en suivant les étapes de la section suivante.

## Accès à votre application web sur Azure

Pour accéder à votre application web sur Azure, vous pouvez utiliser la vue **Explorateur Azure**.

Si la vue **Explorateur Azure** n’est pas déjà ouverte, procédez comme suit : dans IntelliJ, cliquez sur le menu **View** (Affichage), sur **Tool Windows** (Fenêtres des outils), puis sur **Service Explorer** (Explorateur de services). Si vous ne vous êtes pas déjà connecté, vous êtes invité à le faire.

Quand l’**Explorateur Azure** s’affiche, procédez comme suit pour arrêter votre application web :

1. Développez le nœud **Azure**.

1. Développez le nœud **Web Apps** (Applications web).

1. Cliquez avec le bouton droit sur l’application web souhaitée.

1. Quand le menu contextuel s’affiche, cliquez sur **Open in Browser** (Ouvrir dans un navigateur).

    ![][17]

## Mise à jour de votre application web

La mise à jour d’une application web Azure existante en cours d’exécution est un processus simple et rapide, que vous pouvez effectuer de deux façons :

* Vous pouvez mettre à jour le déploiement d’une application web Java existante.
* Vous pouvez publier une application Java supplémentaire dans le même conteneur d’application web.

Dans les deux cas, le processus est identique et ne prend que quelques secondes :

1. Dans l’Explorateur de projets IntelliJ, cliquez avec le bouton droit sur l’application Java que vous souhaitez mettre à jour ou ajouter à un conteneur d’application web existant.

1. Quand le menu contextuel apparaît, sélectionnez **Azure**, puis **Publish as Azure Web App...** (Publier en tant qu’application web Azure...).

1. Comme vous vous êtes déjà connecté, la liste de vos conteneurs d’application web existants apparaît. Sélectionnez celui dans lequel vous souhaitez publier ou republier votre application Java, puis cliquez sur **OK**.

Quelques secondes plus tard, la vue **Journaux d’activité** associe l’état **Publié** à votre déploiement mis à jour, et vous pouvez vérifier dans un navigateur web que votre application a été correctement mise à jour.

## Démarrage ou arrêt d’une application web existante

Pour démarrer ou arrêter un conteneur d’application web Azure existant (y compris toutes les applications Java déployées dans celui-ci), vous pouvez utiliser la vue **Explorateur Azure**.

Si la vue **Explorateur Azure** n’est pas déjà ouverte, procédez comme suit : dans IntelliJ, cliquez sur le menu **View** (Affichage), sur **Tool Windows** (Fenêtres des outils), puis sur **Service Explorer** (Explorateur de services). Si vous ne vous êtes pas déjà connecté, vous êtes invité à le faire.

Quand l’**Explorateur Azure** s’affiche, procédez comme suit pour démarrer ou arrêter votre application web :

1. Développez le nœud **Azure**.

1. Développez le nœud **Web Apps** (Applications web).

1. Cliquez avec le bouton droit sur l’application web souhaitée.

1. Quand le menu contextuel s’affiche, cliquez sur **Start** (Arrêter) ou **Stop** (Arrêter). Les options de menu étant sensibles au contexte, vous pouvez uniquement arrêter une application web en cours d’exécution ou démarrer une application web qui n’est pas en cours d’exécution.

    ![][18]

## Étapes suivantes

Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

- [Kit de ressources Azure pour Eclipse]
  - [Installation du kit de ressources Azure pour Eclipse]
  - [Créer une application web « Hello World » pour Azure dans Eclipse]
  - [Nouveautés du kit de ressources Azure pour Eclipse]
- [Kit de ressources Azure pour IntelliJ]
  - [Installation du kit de ressources Azure pour IntelliJ]
  - *Créer une application web « Hello World » pour Azure dans IntelliJ (cet article)*
  - [Nouveautés du Kit de ressources Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

Pour plus d’informations sur la création d’Azure Web Apps, consultez la [Vue d’ensemble de Web Apps].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ../azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ../azure-toolkit-for-intellij.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Nouveautés du kit de ressources Azure pour Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble de Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

<!---HONumber=AcomDC_0817_2016-->