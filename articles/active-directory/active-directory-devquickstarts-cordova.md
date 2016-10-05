<properties
	pageTitle="Prise en main d’Azure Cordova | Microsoft Azure"
	description="Création d’une application Cordova qui s’intègre avec Azure AD pour la connexion et appelle les API protégées par Azure AD en utilisant OAuth."
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="vittorib"/>

# Intégration d’Azure AD avec une application Apache Cordova

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova permet de développer des applications HTML5/JavaScript qui peuvent s’exécuter sur des appareils mobiles en tant qu’applications natives à part entière. Avec Azure AD, vous pouvez ajouter des fonctionnalités d’authentification d’entreprise à vos applications Cordova. Grâce à un plug-in Cordova encapsulant les Kits de développement logiciel (SDK) natifs d’Azure AD sur iOS, Android, Windows Store et Windows Phone, vous pouvez améliorer votre application pour qu’elle prenne en charge la connexion avec les comptes AD de vos utilisateurs, l’accès aux API Office 365 et Azure et la protection des appels vers vos propres API web personnalisées.

Dans ce didacticiel, nous allons utiliser le plug-in Apache Cordova pour la bibliothèque d’authentification Active Directory (ADAL) pour améliorer une application simple avec les fonctionnalités suivantes :

-	Avec quelques lignes de code, authentifiez un utilisateur Active Directory et obtenez un jeton pour appeler l’API Azure AD Graph.
-	Utilisez ce jeton pour appeler l’API Graph pour interroger ce répertoire et afficher les résultats
-	Tirez parti du cache de jetons ADAL pour limiter les invites d’authentification de l’utilisateur.

Pour ce faire, vous devez :

2. inscrire une application auprès d’Azure AD ;
2. ajouter du code pour que votre application demande des jetons ;
3. ajouter du code pour utiliser le jeton pour l’interrogation de l’API Graph et afficher les résultats ;
4. créer le projet de déploiement Cordova avec toutes les plateformes à cibler et le plug-in Cordova ADAL, puis tester la solution dans des émulateurs.

## *0. Configuration requise*

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un client Azure AD où vous avez un compte disposant de droits de développement d’application
- Un environnement de développement configuré pour exécuter Apache Cordova.

Si vous disposez déjà de ces éléments, passez directement à l’étape 1.

Si vous n’avez pas de client Azure AD, vous pouvez [consulter des instructions sur la façon d’en obtenir un ici](active-directory-howto-tenant.md).

Si Apache Cordova n’est pas configuré sur votre ordinateur, installez les éléments suivants :

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (peut être installé facilement via le Gestionnaire de package NPM : `npm install -g cordova`)

Notez que ces éléments doivent en principe fonctionner aussi bien sur PC que sur Mac.

Chaque plateforme cible a sa propre configuration requise.

- Pour générer et exécuter une version d’application Windows Tablet/PC ou Windows Phone
	- [Visual Studio 2013 pour Windows Update 2 ou version ultérieure](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou autre version).
- Pour générer et exécuter pour iOS
	-   Xcode 5.x ou supérieur. Téléchargez-le à la page http://developer.apple.com/downloads ou dans le [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
	-   [ios-sim](https://www.npmjs.org/package/ios-sim) : vous permet de lancer des applications iOS sur le simulateur iOS à partir de la ligne de commande (peut être installé facilement via le terminal : `npm install -g ios-sim`)

- Pour générer et exécuter l’application pour Android
	- Installez le [Kit de développement Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou ultérieur. Assurez-vous que la variable d’environnement `JAVA_HOME` est correctement définie en fonction du chemin d’installation du JDK (par exemple C:\\Program Files\\Java\\jdk1.7.0\_75).
	- Installez le [Kit de développement logiciel (SDK) Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) et ajoutez l’emplacement `<android-sdk-location>\tools` (par exemple, C:\\tools\\Android\\android-sdk\\tools) pour votre variable d’environnement`PATH`.
	- Ouvrez Android SDK Manager (via le terminal, par exemple : `android`) et installez-le.
    - Kit de développement logiciel (SDK) de la plateforme *Android 5.0.1 (API 21)*
    - *Android SDK Build-tools* version 19.1.0 ou supérieure
    - *Android Support Repository* (modules complémentaires)

  Le Kit de développement logiciel (SDK) Android ne fournit pas d’instance d’émulateur par défaut. Créez-en une en exécutant `android avd` à partir du terminal, puis en sélectionnant *Create...* si vous souhaitez exécuter l’application Android sur l’émulateur. *Niveau d’API* 19 ou supérieur recommandé. Voir [Gestionnaire AVD](http://developer.android.com/tools/help/avd-manager.html) pour plus d’informations sur les options de création et l’émulateur Android.


## *1. Inscription d’une application auprès d’Azure AD*

Remarque : cette __étape est facultative__. Le didacticiel fournit des valeurs préconfigurées qui vous permettent de voir l’exemple en action sans effectuer d’approvisionnement dans votre propre client. Toutefois, nous vous recommandons d’effectuer cette étape et de vous familiariser avec le processus, car vous devrez y faire appel lorsque vous créerez vos propres applications.

Azure AD émet uniquement les jetons pour les applications connues. Avant de pouvoir utiliser Azure AD à partir de votre application, vous devez créer une entrée pour elle dans votre client. Pour inscrire une nouvelle application dans votre client :

-	Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
-	Cliquez sur **Active Directory** dans la partie de gauche.
-	Sélectionnez le client dans lequel vous souhaitez inscrire l’application.
-	Cliquez sur l’onglet **Applications**, puis sur **Ajouter** dans le menu déroulant inférieur.
-	Suivez les invites et créez une **application cliente native** (bien que les applications Cordova soient basées sur le langage HTML, l’option `Native Client Application` doit être sélectionnée, car nous créons ici une application cliente native ; sinon, l’application ne fonctionnera pas).
    -	Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	**URI de redirection** correspond à l’URI utilisé pour renvoyer les jetons à votre application. Entrez `http://MyDirectorySearcherApp`.

Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Vous aurez besoin de cette valeur dans les sections suivantes : vous pouvez la trouver dans l’onglet **Configurer** de l’application nouvellement créée.

Pour exécuter `DirSearchClient Sample`, autorisez l’application nouvellement créée à interroger l’_API Azure AD Graph_ :
-	Dans l’onglet **Configurer**, cherchez la section Autorisations pour d’autres applications. Pour l’application Azure Active Directory, ajoutez l’autorisation **Accéder au répertoire en tant qu’utilisateur connecté** sous **Autorisations déléguées**. Cela permet à votre application d’interroger l’API Graph concernant les utilisateurs.

## *2. Clonage du référentiel d’exemples d’applications requis pour le didacticiel*

À partir de votre environnement ou de la ligne de commande, tapez la commande suivante :

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3. Création de l’application Cordova*

Il existe plusieurs façons de créer des applications Cordova. Dans ce didacticiel, nous allons utiliser l’interface de ligne de commande (CLI) de Cordova. À partir de votre environnement ou de la ligne de commande, tapez la commande suivante :


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Cela permet de créer la structure de dossiers et la structure du projet Cordova, en copiant le contenu du projet de démarrage dans le sous-dossier www. Accédez au nouveau dossier DirSearchClient.

    cd .\DirSearchClient

Ajoutez le plug-in d’autorisation nécessaire pour appeler l’API Graph.

     cordova plugin add cordova-plugin-whitelist

Ensuite, ajoutez toutes les plateformes que vous souhaitez prendre en charge. Pour avoir un exemple fonctionnel, vous devez exécuter au moins une des commandes ci-dessous. Notez que vous ne pourrez pas émuler iOS sur Windows ou Windows/Windows Phone sur un Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Enfin, vous pouvez ajouter la bibliothèque ADAL pour le plug-in Cordova dans votre projet.

    cordova plugin add cordova-plugin-ms-adal

## *3. Ajout de code pour authentifier les utilisateurs et obtenir des jetons d’AAD*

L’application que vous développez dans ce didacticiel fournit une fonctionnalité de recherche de répertoire de base, où l’utilisateur peut taper l’alias d’un utilisateur dans le répertoire et visualiser certains attributs de base. Le projet de démarrage contient la définition de l’interface utilisateur de base de l’application (dans www/index.html) et la génération de modèles automatique qui associe les cycles d’événements des applications, les liaisons d’interface utilisateur et la logique d’affichage des résultats (dans www/js/index.js). Il vous reste juste à ajouter la logique d’implémentation des tâches d’identité.

La première chose à faire est d’introduire dans votre code les valeurs de protocole utilisées par AAD pour l’identification de votre application et des ressources que vous ciblez. Ces valeurs sont ensuite utilisées pour créer les demandes de jeton. Insérez l’extrait de code ci-dessous tout en haut du fichier index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Les valeurs `redirectUri` et `clientId` doivent correspondre à celles décrivant votre application dans AAD. Vous pouvez les trouver dans l’onglet Configurer dans le portail Azure, comme décrit à l’étape 1 plus haut dans ce didacticiel. Remarque : si vous avez choisi de ne pas inscrire de nouvelle application dans votre propre client, vous pouvez simplement coller les valeurs préconfigurées ci-dessus sans les modifier. Cela vous permet de voir l’exécution de l’exemple, mais vous devez toujours créer votre propre entrée pour vos applications de production.

Ensuite, nous devons ajouter le code de demande de jeton réel. Insérez l’extrait de code suivant entre les définitions `search ` et `renderdata `.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinons cette fonction en la décomposant en deux parties principales. Cet exemple est conçu pour fonctionner avec n’importe quel client. Il utilise le point de terminaison « /common », ce qui permet à l’utilisateur d’entrer n’importe quel compte au moment de l’authentification et de rediriger la demande au client auquel elle appartient. La première partie de la méthode inspecte le cache de la bibliothèque ADAL pour voir si un jeton y est déjà stocké. Si c’est le cas, elle utilise les clients d’où elle provient pour la réinitialisation de la bibliothèque ADAL. Cela est nécessaire pour éviter les invites supplémentaires, car l’utilisation de « /common » génère toujours une invite demandant à l’utilisateur d’entrer un nouveau compte.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La deuxième partie de la méthode effectue la demande de jeton appropriée. La méthode `acquireTokenSilentAsync` demande à la bibliothèque ADAL de renvoyer un jeton pour la ressource spécifiée sans afficher d’expérience utilisateur. Cela peut se produire si le cache a déjà un jeton d’accès stocké approprié, ou s’il existe un jeton d’actualisation qui peut être utilisé pour obtenir un nouveau jeton d’accès sans afficher d’invite. Si cette tentative échoue, nous revenons à `acquireTokenAsync`, qui invitera visiblement l’utilisateur à s’authentifier.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Maintenant que nous avons le jeton, nous pouvons enfin appeler l’API Graph et effectuer la requête de recherche souhaitée. Insérez l’extrait de code suivant juste en dessous de la définition `authenticate`.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Les fichiers de départ ont fourni une expérience utilisateur de base pour la saisie de l’alias d’un utilisateur dans une zone de texte. Cette méthode utilise cette valeur pour construire une requête, la combiner avec le jeton d’accès, l’envoyer à l’API Graph et analyser les résultats. La méthode renderData, déjà présente dans le fichier de départ, s’occupe de visualiser les résultats.

## *4. Exécution*
Votre application est maintenant prête à s’exécuter. Son fonctionnement est très simple : une fois que l’application démarre, entrez dans la zone de texte l’alias de l’utilisateur que vous souhaitez rechercher, puis cliquez sur le bouton. Vous êtes invité à vous authentifier. Une fois l’authentification et la recherche réussies, les attributs de l’utilisateur faisant l’objet d’une recherche s’affichent. Les exécutions suivantes effectuent la recherche sans afficher d’invite, grâce à la présence dans le cache du jeton obtenu précédemment. Les étapes concrètes pour l’exécution de l’application varient selon la plateforme.

####Windows 10 :

   Tablette/PC : `cordova run windows --archs=x64 -- --appx=uap`

   Mobile (nécessite un appareil Windows 10 Mobile connecté au PC) : `cordova run windows --archs=arm -- --appx=uap --phone`

   __Remarque__ : lors de la première exécution, vous pouvez être invité à vous connecter pour obtenir une licence de développeur. Pour plus d’informations, consultez la page [Obtenir une licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

####Tablette/PC Windows 8.1 :

   `cordova run windows`

   __Remarque__ : lors de la première exécution, vous pouvez être invité à vous connecter pour obtenir une licence de développeur. Pour plus d’informations, consultez la page [Obtenir une licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

####Windows Phone 8,1 :

   Pour une exécution sur l’appareil connecté : `cordova run windows --device -- --phone`

   Pour une exécution sur l’émulateur par défaut : `cordova emulate windows -- --phone`

   Utilisez `cordova run windows --list -- --phone` pour voir toutes les cibles disponibles et `cordova run windows --target=<target_name> -- --phone` pour exécuter l’application sur l’appareil ou émulateur spécifique (par exemple, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####Android :

   Pour une exécution sur l’appareil connecté : `cordova run android --device`

   Pour une exécution sur l’émulateur par défaut : `cordova emulate android`

   __Remarque__ : assurez-vous que vous avez créé une instance d’émulateur à l’aide de *AVD Manager*, comme indiqué dans la section *Configuration requise*.

   Utilisez `cordova run android --list` pour voir toutes les cibles disponibles et `cordova run android --target=<target_name>` pour exécuter l’application sur l’appareil ou émulateur spécifique (par exemple, `cordova run android --target="Nexus4_emulator"`).

####iOS :

   Pour une exécution sur l’appareil connecté : `cordova run ios --device`

   Pour une exécution sur l’émulateur par défaut : `cordova emulate ios`

   __Remarque__ : assurez-vous que vous avez installé le package `ios-sim` pour l’exécuter sur l’émulateur. Pour plus d’informations, consultez la section *Configuration requise*.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Utilisez `cordova run --help` pour voir d’autres options de génération et d’exécution.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient). Vous pouvez maintenant passer à des scénarios plus avancés et plus intéressants. Par exemple :

[Sécurisation d’une API web Node.js avec Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->