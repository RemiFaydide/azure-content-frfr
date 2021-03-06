<properties
	pageTitle="Ajout de notifications Push à votre plateforme Windows universelle (UWP) | Azure Mobile Apps"
	description="Découvrez comment utiliser Azure App Service Mobile Apps et Azure Notification Hubs pour envoyer des notifications Push à votre application de plateforme Windows universelle (UWP)."
	services="app-service\mobile,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/15/2016"
	ms.author="glenga"/>

# Ajouter des notifications Push à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Vue d'ensemble

Cette rubrique vous montre comment envoyer des notifications Push à une plateforme Windows universelle à l'aide de Mobile Apps dans Azure App Service avec Azure Notification Hubs. Dans ce scénario, lorsqu'un nouvel élément est ajouté, votre serveur principal d’applications mobiles envoie une notification Push à toutes les applications Windows qui sont inscrites auprès de Windows Notification Service (WNS).

Ce didacticiel est basé sur le démarrage rapide de Mobile Apps. Avant de commencer ce didacticiel, vous devez suivre le didacticiel de démarrage rapide [Créer une application Windows](app-service-mobile-windows-store-dotnet-get-started.md). Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="create-hub"></a>Créer un hub de notification

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers des applications Windows à partir d’Azure, vous devez soumettre votre application au Windows Store. Vous pouvez ensuite configurer votre projet de serveur pour l’intégrer à WNS.

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d’application UWP, puis cliquez sur **Store** > **Associer l’application au Windows Store...**.

    ![Associer une application au Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. Dans l'Assistant, cliquez sur **Suivant**, connectez-vous à votre compte Microsoft, saisissez un nom pour votre application dans **Réserver un nouveau nom d'application**, puis cliquez sur **Réserver**.

3. Une fois l’inscription de l’application créée, sélectionnez le nouveau nom d’application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7. Accédez au [Centre de développement Windows](https://dev.windows.com/fr-FR/overview), connectez-vous à l'aide de votre compte Microsoft, cliquez sur la nouvelle inscription d'application dans **Mes applications**, puis développez **Services** > **Notifications Push**.

8. Sur la page **Notifications Push**, cliquez sur **Site des services Live** sous **Microsoft Azure Mobile Services**.

9. Dans la page d’inscription, prenez note de la valeur sous **Secrets de l’application** et **SID du package**, que vous utiliserez ensuite pour configurer le backend de votre application mobile.

	![Associer une application au Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application. L’**ID d’application** est utilisé avec la clé secrète pour configurer l’authentification du compte Microsoft.

##Configurer le serveur principal pour l’envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Mise à jour du serveur pour l'envoi de notifications Push

Maintenant que les notifications push sont activées dans l'application, vous devez mettre à jour votre backend d'application pour envoyer des notifications push. Utilisez la procédure ci-dessous correspondant à votre type de projet de serveur principal : [serveur principal .NET](#dotnet) ou [serveur principal Node.js](#nodejs).

### <a name="dotnet"></a>Projet de serveur principal .NET

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez Microsoft.Azure.NotificationHubs et cliquez sur **Installer**. Cette commande installe la bibliothèque cliente Notification Hubs.

2. Développez **Contrôleurs**, ouvrez TodoItemController.cs et ajoutez les instructions using suivantes :

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l’appel à **InsertAsync** :

	    // Get the settings for the server project.
	    HttpConfiguration config = this.Configuration;
	    MobileAppSettingsDictionary settings =
	        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

	    // Get the Notification Hubs credentials for the Mobile App.
	    string notificationHubName = settings.NotificationHubName;
	    string notificationHubConnection = settings
	        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
	        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

	        // Write the success result to the logs.
	        config.Services.GetTraceWriter().Info(result.State.ToString());
	    }
	    catch (System.Exception ex)
	    {
	        // Write the failure result to the logs.
	        config.Services.GetTraceWriter()
	            .Error(ex.Message, null, "Push.SendAsync Error");
	    }

	Ce code indique au hub de notification d'envoyer une notification Push après l'insertion d’un nouvel élément.

4. Publier à nouveau le projet de serveur

### <a name="nodejs"></a>Projet de back-end Node.js

1. Si vous ne l’avez pas déjà fait, [téléchargez le projet de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou utilisez l’[éditeur en ligne du portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Remplacez le code présent dans le fichier todoitem.js par le code suivant :

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');

		var table = azureMobileApps.table();

		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');

	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";

	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;

	Ce code envoie une notification toast WNS contenant item.text quand un nouvel élément todo est inséré.

2. Quand vous modifiez le fichier sur votre ordinateur local, republiez le projet serveur.

##<a id="update-app"></a>Ajout de notifications Push à votre application

Ensuite, votre application doit s’inscrire pour les notifications Push au démarrage. Si vous avez déjà activé l'authentification, assurez-vous que l'utilisateur est connecté avant d'essayer de s'inscrire aux notifications Push. Pour plus d'informations, consultez [Authentification préalable](https://github.com/Azure-Samples/app-service-mobile-windows-quickstart/blob/master/README.md#authenticate-first) dans l'exemple complet de démarrage rapide.

1. Ouvrez le fichier projet **App.xaml.cs** et ajoutez les instructions `using` suivantes :

		using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. Dans le même fichier, ajoutez la définition de la méthode **InitNotificationsAsync** à la classe **App** :

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Ce code récupère l'URI de canal ChannelURI pour l'application dans WNS et l'inscrit avec votre application App Service Mobile App.

3. En haut du gestionnaire d’événements **OnLaunched** dans **App.xaml.cs**, ajoutez le modificateur **async** à la définition de méthode et ajoutez l’appel suivant à la nouvelle méthode **InitNotificationsAsync**, comme dans l’exemple suivant :

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Cela garantit l'inscription de l'URI de canal ChannelURI de courte durée chaque fois que l'application est lancée.

4. Recréez votre projet d’application UWP. Votre application est maintenant prête à recevoir des notifications toast.

##<a id="test"></a>Tester les notifications push dans votre application

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Étapes suivantes

Apprenez-en plus sur les notifications Push :

* [Utilisation du client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) Les modèles vous apportent la souplesse nécessaire pour envoyer des notifications push multiplateformes et localisées. Apprenez à inscrire des modèles.

* [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) Les balises vous permettent de vous permettent de cibler des clients segmentés avec des notifications push. Apprenez à ajouter des balises à l’installation d’un appareil.

* [Diagnostiquer les problèmes de notification Push](../notification-hubs/notification-hubs-push-notification-fixer.md) Il existe différentes raisons pour lesquelles les notifications peuvent être perdues ou n’arrivent pas sur les appareils. Cette rubrique vous explique comment analyser et déterminer la cause première des défaillances de notification Push.

Vous pouvez poursuivre avec l’un des didacticiels suivants :

+ [Ajouter l’authentification à votre application](app-service-mobile-windows-store-dotnet-get-started-users.md) Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.

+ [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal d’applications mobiles. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_0629_2016-->