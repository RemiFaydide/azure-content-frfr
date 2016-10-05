<properties
	pageTitle="Application Android Azure Active Directory v2.0 | Microsoft Azure"
	description="Génération d’une application Android qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leurs comptes professionnel ou scolaire et appelle l’API Graph à l’aide de bibliothèques tierces."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

#  Ajouter l’authentification dans une application Android à l’aide d’une bibliothèque tierce avec l’API Graph à l’aide du point de terminaison v2.0

La plateforme d’identité Microsoft utilise des normes ouvertes telles que OAuth2 et OpenID Connect. Les développeurs peuvent utiliser n’importe quelle bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons rédigé quelques procédures pas à pas comme celle-ci pour présenter la configuration des bibliothèques tierces pour se connecter à la plateforme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) peuvent se connecter à la plateforme Microsoft Identity.

Avec l’application créée par cette procédure pas à pas, les utilisateurs peuvent se connecter à leur organisation, puis rechercher eux-mêmes dans leur entreprise à l’aide de l’API Graph.

Si vous découvrez OAuth2 ou OpenID Connect, cet exemple de configuration n’est peut-être pas très parlant pour vous. Nous vous recommandons de lire [Protocoles 2.0 - Flux du Code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md) pour l’arrière-plan.

> [AZURE.NOTE] Certaines fonctionnalités de notre plateforme qui ont une expression dans les normes OAuth2 ou OpenID Connect, comme la gestion de la stratégie d’accès conditionnel et d’Intune, requièrent l’utilisation de nos bibliothèques d’identité Microsoft Azure open source.

Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory.

> [AZURE.NOTE] Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).


## Téléchargez le code à partir de GitHub
Le code associé à ce didacticiel est stocké [sur GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip) ou la cloner :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Vous pouvez aussi simplement télécharger l’exemple et commencer immédiatement :

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## Inscription d’une application
Créez une nouvelle application dans le [Portail d’inscription des applications](https://apps.dev.microsoft.com), ou suivez les étapes détaillées dans [Inscription d’une application avec le point de terminaison v2.0](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- Copiez **l’ID d’application** affecté à votre application, vous en aurez besoin rapidement.
- ajouter la plateforme **Mobile** pour votre application ;

> Remarque : Le portail d’inscription des applications fournit une valeur **URI de redirection**. Toutefois, dans cet exemple, vous devez utiliser la valeur par défaut `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## Téléchargez la bibliothèque tierce NXOAuth2 et créez un espace de travail

Pour cette procédure pas à pas, vous allez utiliser OIDCAndroidLib de GitHub, une bibliothèque OAuth2 basée sur le code OpenID Connect de Google. Elle implémente le profil de l’application native et prend en charge le point de terminaison de l’autorisation de l’utilisateur final. Voici tout ce dont vous aurez besoin pour l’intégration avec la plateforme d’identité Microsoft.

Clonez le référentiel OIDCAndroidLib sur votre ordinateur.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## Configurer votre environnement Android Studio

1. Créez un nouveau projet Android Studio et acceptez les valeurs par défaut dans l’assistant.

	![Créer un nouveau projet dans Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

	![Cibler des appareils Android](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

	![Ajouter une activité à un mobile](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

2. Pour configurer les modules de votre projet, déplacez le référentiel cloné à l’emplacement du projet. Vous pouvez également créer le projet puis le cloner directement dans l’emplacement du projet.

	![Modules de projet](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

3. Ouvrez les paramètres de modules du projet à l’aide du menu contextuel ou en utilisant le raccourci Ctrl + Alt + Maj + S.

	![Paramètres de modules de projet](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

4. Supprimez le module d’application par défaut pour conserver uniquement les paramètres de conteneur du projet.

	![Le module d’application par défaut](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

5. Importez des modules à partir du référentiel cloné dans le projet actuel.

	![Importer un projet gradle](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![Créer une nouvelle page de module](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

6. Répétez ces étapes pour le module `oidlib-sample`.

7. Vérifiez les dépendances oidclib sur le module `oidlib-sample`.

	![Dépendances oidclib sur le module oidlib-sample](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

8. Cliquez sur **OK** et attendez l’activité de synchronisation Gradle.

	Votre fichier settings.gradle doit ressembler à ceci :

	![Capture d’écran de settings.gradle](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

9. Générez l’exemple d’application pour vérifier qu’il s’exécute correctement.

	Vous ne pourrez pas encore l’utiliser avec Azure Active Directory. Vous devez d’abord configurer certains points de terminaison. Cela permet de vérifier l’absence de problèmes au niveau d’Android Studio avant de commencer à personnaliser l’exemple d’application.

10. Générez et exécutez `oidlib-sample` comme cible dans Android Studio.

	![Progression de la création d’oidlib-sample](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

11. Supprimez le répertoire `app ` conservé après la suppression du module du projet car Android Studio ne le supprime pas par sécurité.

	![Structure de fichier qui contient le répertoire d’application](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

12. Ouvrez le menu **Modifier les configurations** pour supprimer la configuration d’exécution également conservée lors de la suppression du module du projet.

	![Menu Modifier les configurations](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![Exécuter la configuration de l’application](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## Configurer les points de terminaison de l’exemple

Maintenant que `oidlib-sample` s’exécute correctement, vous allez modifier quelques points de terminaison pour qu’il fonctionne avec Azure Active Directory.

### Configurez votre client en modifiant le fichier oidc\_clientconf.xml

1. Dans la mesure où nous utilisons seulement les flux OAuth2 pour obtenir un jeton et appeler l’API Graph, nous allons définir le client pour OAuth2 seulement. Nous verrons OIDC dans un autre exemple.

	```xml
	    <bool name="oidc_oauth2only">true</bool>
	```

2. Configurez l’ID client que vous avez reçu à partir du portail d’inscription.

	```xml
	    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
	    <string name="oidc_clientSecret"></string>
	```

3. Configurez votre URI de redirection avec celle ci-dessous.

	```xml
	    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
	```

4. Configurez les étendues dont vous avez besoin pour accéder à l’API Graph.

	```xml
	    <string-array name="oidc_scopes">
	        <item>openid</item>
	        <item>https://graph.microsoft.com/User.Read</item>
	        <item>offline_access</item>
	    </string-array>
	```

La valeur `User.Read` dans `oidc_scopes` vous permet de lire le profil de base de l’utilisateur connecté. Plus d’informations sur toutes les étendues disponibles, consultez [Étendues d’autorisation Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Si vous souhaitez des explications sur `openid` ou `offline_access` comme étendues dans OpenID Connect, consultez [Protocoles 2.0 - Flux du Code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### Configurez vos points de terminaison clients en modifiant le fichier oidc\_endpoints.xml

- Ouvrez le fichier `oidc_endpoints.xml` et effectuez les modifications suivantes :

	```xml
	<!-- Stores OpenID Connect provider endpoints. -->
	<resources>
	    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
	    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
	    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
	    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
	</resources>
	```

Ces points de terminaison ne doivent jamais changer si vous utilisez OAuth2 comme protocole.

> [AZURE.NOTE]
Les points de terminaison pour `userInfoEndpoint` et `revocationEndpoint` ne sont actuellement pas pris en charge par Azure Active Directory. Si vous laissez la valeur par défaut exemple.com, vous recevrez un rappel qu’ils ne sont pas disponibles dans l’exemple :-)


## Configurer un appel d’API Graph

- Ouvrez le fichier `HomeActivity.java` et effectuez les modifications suivantes :

	```Java
	   //TODO: set your protected resource url
	    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
	```

Ici, un simple appel à l’API Graph renvoie nos informations.

Ce sont toutes les modifications que vous devez faire. Exécutez l’application `oidlib-sample`, puis cliquez sur **Connexion**.

Une fois que vous avez été authentifié, sélectionnez le bouton **Request Protected Resource** (Demander une ressource protégée) pour tester votre appel à l’API Graph.

## Obtenir des mises à jour de sécurité pour notre produit

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [Security TechCenter](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0921_2016-->