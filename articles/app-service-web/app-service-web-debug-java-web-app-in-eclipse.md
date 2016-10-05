<properties 
	pageTitle="Débogage d’une application web Java sur Azure dans Eclipse | Microsoft Azure" 
	description="Ce didacticiel vous explique comment utiliser le Kit de ressources Azure pour Eclipse pour déboguer une application web Java s’exécutant sur Azure." 
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
	ms.date="09/20/2016" 
	ms.author="asirveda;robmcm"/>

# Débogage d’une application web Java sur Azure dans Eclipse

Ce didacticiel explique comment déboguer une application web Java s’exécutant sur Azure à l’aide du [Kit de ressources Azure pour Eclipse]. Par souci de simplicité, vous utiliserez un exemple Java Server Page (JSP) de base pour ce didacticiel, mais la procédure est identique pour un servlet Java lors du débogage sur Azure.

À la fin de ce didacticiel, votre application doit ressembler à l’illustration suivante quand vous la déboguez dans Eclipse :

![][01]
 
## Composants requis

* JDK (Java Development Kit) version 1.8 ou ultérieure.
* IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>.
* Une distribution d’un serveur web ou d’un serveur d’applications basé sur Java, comme Apache Tomcat ou Jetty.
* Un abonnement à Azure, pouvant être souscrit à l’adresse <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Kit de ressources Azure pour Eclipse. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour Eclipse].
* Un projet web dynamique créé et déployé sur Azure App Service ; par exemple, consultez [Créer une application web « Hello World » pour Azure dans Eclipse].

## Pour déboguer une application web Java sur Azure

Pour effectuer les étapes de cette section, vous pouvez utiliser un projet web dynamique existant que vous avez déjà déployé comme application web Java sur Azure, vous téléchargez un [exemple de projet web dynamique] et suivez les étapes indiquées dans [Créer une application web « Hello World » pour Azure dans Eclipse] pour le déployer sur Azure.

1. Ouvrez Eclipse.

1. Configurez les délais d’expiration pour le débogage à distance :

    1. Cliquez sur le menu **Windows** dans Eclipse, puis cliquez sur **Preferences** (Préférences).
    1. Développez le nœud **Java**, puis sélectionnez **Debug** (Déboguer).
    1. Configurez les paramètres **Debugger timeout (ms)** (Délai d’expiration du débogueur (ms)) et **Launch timeout (ms)** (Délai d’expiration du lancement (ms)) `120000`.

        ![][02]

    1. Cliquez sur **OK** pour fermer la boîte de dialogue **Preferences** (Préférences).

1. Dans la vue de l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit sur le projet web dynamique que vous avez déployé dans Azure. Quand le menu contextuel apparaît, sélectionnez **Debug As** (Déboguer comme), puis cliquez sur **Azure Web App** (Application web Azure).

    ![][03]

1. Si c’est la première fois que vous déboguez votre projet web dynamique, la boîte de dialogue **Debug Configurations** (Déboguer les configurations) s’ouvre ; vous pouvez accepter les valeurs par défaut qui sont spécifiées par le kit de ressources dans l’onglet **Connect** (Connexion). Dans l’onglet **Source**, cliquez sur **Add** (Ajouter), puis sur **Java project** (Projet Java), sélectionnez **Dynamic Web Project** (Projet web dynamique), puis cliquez sur **OK**. Une fois ces étapes terminées, cliquez sur **Debug** (Déboguer).

    ![][04]

1. À l’invite **Enable remote debugging in the remote Web App now?** (Activer le débogage à distance sur l’application web à distance maintenant ?), cliquez sur **OK**.

1. À l’invite **Your web app is now ready for remote debugging** (Votre application web est maintenant prête pour le débogage à distance), cliquez sur **OK**.

    ![][05]

1. Lorsque la boîte de dialogue **Debug Configurations** (Déboguer les configurations) s’affiche à nouveau, cliquez sur **Debug** (Déboguer).

1. Une invite de commande Windows ou un shell Unix s’ouvre et prépare la connexion nécessaire pour le débogage. Vous devez attendre que la connexion à votre application web Java à distance soit établie avant de continuer. Si vous utilisez Windows, cela ressemble à l’illustration suivante.

    ![][06]

1. Insérez un point d’arrêt dans votre page JSP, puis ouvrez l’URL de votre application web Java dans un navigateur :

    1. Ouvrez **Azure Explorer** dans Eclipse.
    1. Accédez à **Web Apps** et à l’application web Java à déboguer.
    1. Cliquez avec le bouton droit sur l’application web, puis cliquez sur **Ouvrir dans un navigateur**.
    1. Eclipse passe en mode débogage.

## Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

Pour plus d’informations sur la création d’Azure Web Apps, consultez la [Vue d’ensemble de Web Apps].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ressources Azure pour Eclipse]: ../azure-toolkit-for-eclipse.md
[Installation du kit de ressources Azure pour Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[exemple de projet web dynamique]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble de Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->