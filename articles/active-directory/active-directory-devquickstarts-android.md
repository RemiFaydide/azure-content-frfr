<properties
	pageTitle="Prise en main d’Azure AD Android | Microsoft Azure"
	description="Création d’une application Android qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth."
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Intégration d’Azure AD dans une application Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous développez une application de bureau, Azure AD facilite l’authentification de vos utilisateurs en utilisant leurs comptes Active Directory. Il permet également à votre application d’utiliser en toute sécurité une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les clients Android qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d'accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application To-Do List Android qui effectue les actions suivantes :

-	obtention de jetons d’accès pour appeler l’API To-Do List à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
-	obtention de la liste des tâches d’un utilisateur ;
-	déconnexion des utilisateurs.

Pour commencer, vous avez besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## Étape 1 : Téléchargement et exécution de l’exemple de serveur TODO d’API REST Node.js

Cet exemple est écrit spécifiquement pour fonctionner avec notre exemple existant pour la création d’une API REST TODO avec un seul client pour Microsoft Azure Active Directory. Il s’agit d’un composant requis pour le démarrage rapide.

Pour plus d’informations sur la configuration associée, consultez nos exemples existants ici :

* [Exemple de service d’API REST Microsoft Azure Active Directory pour Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Étape 2 : Inscription de votre API web auprès de votre client Microsoft Azure AD

**Qu’est-ce que je fais ?**

*Microsoft Active Directory prend en charge l’ajout de deux types d’application. Les API web qui offrent des services aux utilisateurs, ainsi que les applications (soit sur Internet soit sur une application exécutée sur un appareil) qui y accèdent. Lors de cette étape, vous inscrivez l’API web que vous exécutez localement pour tester cet exemple. Normalement, cette API web est un service REST qui offre des fonctionnalités auxquelles une application va accéder. Microsoft Azure Active Directory peut protéger n’importe quel point de terminaison.*

*Ici, nous supposons que vous inscrivez l’API REST TODO mentionnée ci-dessus, mais cela fonctionne aussi pour les API web qui doivent être protégées par Azure Active Directory.*

Étapes d’inscription d’une API web avec Microsoft Azure AD

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans la partie de gauche.
3. Cliquez sur le répertoire client dans lequel vous souhaitez enregistrer l'exemple d'application.
4. Cliquez sur l’onglet Applications.
5. Dans le tiroir, cliquez sur Ajouter.
6. Cliquez sur « Ajouter une application développée par mon organisation ».
7. Entrez un nom convivial pour l’application, par exemple « TodoListService », sélectionnez « Application Web et/ou API Web », puis cliquez sur Suivant.
8. Pour l’URL d’authentification, entrez l’URL de base pour l’exemple, qui est par défaut `https://localhost:8080`.
9. Pour l’URI ID d’application, saisissez `https://<your_tenant_name>/TodoListService`, en remplaçant `<your_tenant_name>` par le nom de votre client Azure AD. Cliquez sur OK pour terminer l’inscription.
10. Toujours dans le portail Azure, cliquez sur l’onglet Configurer de votre application.
11. **Recherchez la valeur d’ID client et copiez-la**, car vous en aurez besoin plus tard lors de la configuration de votre application.

## Étape 3 : Inscription de l’exemple d’application cliente native Android

Vous devez d’abord inscrire votre application web. Ensuite, vous devez aussi indiquer à Azure Active Directory l’existence de votre application. Cela permet à votre application de communiquer avec l’API web qui vient d’être inscrite.

**Qu’est-ce que je fais ?**

*Comme indiqué plus haut, Microsoft Active Directory prend en charge l’ajout de deux types d’application. Les API web qui offrent des services aux utilisateurs, ainsi que les applications (soit sur Internet soit sur une application exécutée sur un appareil) qui y accèdent. Au cours de cette étape, vous inscrivez l’application de cet exemple. Vous devez procéder ainsi pour que cette application soit en mesure de demander l’accès à l’API web que vous venez d’inscrire. Azure Active Directory ne permet pas à votre application de demander une connexion, sauf si celle-ci est inscrite. Cela fait partie de la sécurité du modèle.*

*Ici, nous supposons que vous inscrivez l’exemple d’application mentionné ci-dessus, mais cela fonctionne pour n’importe quelle application que vous développez.*

**Pourquoi incorporer à la fois une application et une API web dans un seul client ?**

*Comme vous l’avez peut-être deviné, vous pouvez générer une application qui accède à une API externe inscrite dans Azure Active Directory à partir d’un autre client. Si vous procédez ainsi, vos clients sont invités à donner leur autorisation pour l’utilisation de l’API dans l’application. L’avantage, c’est que c’est la bibliothèque d’authentification Active Directory pour iOS qui s’occupe de cette autorisation pour vous. À mesure que nous aborderons des fonctionnalités plus avancées, vous verrez qu’il s’agit d’une partie importante du travail nécessaire pour accéder à la suite d’API Microsoft Azure et Office, ainsi qu’à tout autre fournisseur de services. Pour l’instant, comme vous avez inscrit votre API web et l’application sous le même client, vous ne voyez aucune invite de consentement. Cela est généralement le cas si vous développez une application uniquement pour une utilisation par votre entreprise.*

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans la partie de gauche.
3. Cliquez sur le répertoire client dans lequel vous souhaitez enregistrer l'exemple d'application.
4. Cliquez sur l’onglet Applications.
5. Dans le tiroir, cliquez sur Ajouter.
6. Cliquez sur « Ajouter une application développée par mon organisation ».
7. Entrez un nom convivial pour l’application, par exemple « TodoListClient-Android », sélectionnez « Application cliente native », puis cliquez sur Suivant.
8. Pour l’URI de redirection, entrez `http://TodoListClient`. Cliquez sur Terminer.
9. Cliquez sur l’onglet Configurer de l’application.
10. Recherchez la valeur d’ID client et prenez-en note, car vous en aurez besoin plus tard lors de la configuration de votre application.
11. Dans Autorisations pour d’autres applications, cliquez sur Ajouter une application. Sélectionnez Autre dans la liste déroulante Afficher et cliquez sur la coche située en haut. Recherchez l’API TodoListService, cliquez dessus, puis cliquez sur la coche située en bas pour ajouter l’application. Sélectionnez Accéder à TodoListService à partir de la liste déroulante Autorisations déléguées, puis enregistrez la configuration.



Pour générer avec Maven, vous pouvez utiliser le pom.xml au niveau supérieur.

  * Clonez ce référentiel dans un répertoire de votre choix :

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * Suivez les étapes de la section [Configuration requise pour configurer maven pour android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Installez l’émulateur avec le kit de développement (SDK) 19.
  * Accédez au dossier racine où vous avez cloné le référentiel.
  * Exécutez la commande : mvn clean install
  * Accédez au répertoire de l’exemple de démarrage rapide : cd samples\\hello
  * Exécutez la commande : mvn android:deploy android:run
  * L’application doit normalement se lancer
  * Entrez les informations d’identification utilisateur de test pour faire un essai.

Les packages JAR sont également envoyés à côté du package aar.

### Étape 4 : Téléchargement de la bibliothèque ADAL Android et ajout de cette dernière à votre espace de travail Eclipse

Nous avons fait en sorte que vous puissiez facilement disposer de plusieurs options pour utiliser cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer cette bibliothèque dans Eclipse et la lier à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser le format de package *aar* et référencer les fichiers binaires.

####Option 1 : code source dans un fichier ZIP

Pour télécharger une copie du code source, cliquez sur « Télécharger ZIP » sur le côté droit de la page ou cliquez [ici](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####Option 2:  code Source via Git

Pour obtenir le code source du Kit de développement (SDK) via git, tapez simplement :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####Option 3: fichiers binaires via Gradle

Vous pouvez obtenir les fichiers binaires à partir du référentiel central Maven. Le package AAR peut être inclus dans votre projet dans AndroidStudio comme suit :

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####Option 4: aar via Maven

Si vous utilisez le plug-in m2e dans Eclipse, vous pouvez spécifier la dépendance dans votre fichier pom.xml :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####Option 5 : package jar à l'intérieur du dossier de bibliothèques
Vous pouvez obtenir le fichier jar du référentiel maven et le déposer dans le dossier *libs* de votre projet. Vous devez également copier les ressources nécessaires à votre projet dans la mesure où les packages jar ne les incluent pas.


### Étape 5 : ajout des références à la bibliothèque ADAL Android dans votre projet


2. Ajoutez une référence à votre projet et spécifiez qu’il s’agit d’une bibliothèque Android. Si vous ne savez pas comment procéder, [cliquez ici pour plus d'informations](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Ajoutez la dépendance de projet pour le débogage dans vos paramètres de projet

4. Mettez à jour le fichier AndroidManifest.xml du projet pour y inclure le code suivant :

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Créez une instance d’AuthenticationContext pour votre activité principale. Les détails de cet appel dépassent la cadre de ce fichier Lisez-moi, mais pour commencer, vous pouvez examiner l’[exemple de client natif Android](https://github.com/AzureADSamples/NativeClient-Android). Voici un exemple :

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * REMARQUE : mContext est un champ dans votre activité.

8. Copiez ce bloc de code pour gérer la fin de AuthenticationActivity une fois que l’utilisateur a entré les informations d’identification et reçu le code d'autorisation :

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Pour demander un jeton, vous définissez un rappel.

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Enfin, demandez un jeton à l’aide de ce rappel :

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explication des paramètres :

  * Resource est obligatoire et désigne la ressource à laquelle vous tentez d’accéder.
  * Clientid est obligatoire et provient du portail Azure AD.
  * Vous pouvez configurer redirectUri comme votre packagename. Il n’est pas obligatoire pour l’appel à acquireToken.
  * PromptBehavior permet de demander des informations d’identification pour ignorer le cache et le cookie.
  * Callback sera appelé une fois que le code d’autorisation aura été échangé contre un jeton.

  Callback aura un objet AuthenticationResult avec jeton d’accès, date d’expiration et info idtoken.

Facultatif : **acquireTokenSilent**

Vous pouvez appeler **acquireTokenSilent** pour gérer la mise en cache et l’actualisation du jeton. Il fournit également la version de synchronisation. Il accepte userid comme paramètre.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Broker** :
  l’application Portail d’entreprise de Microsoft Intune fournira le composant Service Broker. La bibliothèque ADAL utilisera le compte Service Broker, si un compte d’utilisateur a été créé pour cet authentificateur et que le développeur choisit ne pas l'ignorer. Le développeur peut ignorer l’utilisateur de Service Broker avec :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Le développeur doit inscrire un redirectUri spécial pour l’utilisation de Service Broker. RedirectUri est au format msauth://packagename/Base64UrlencodedSignature. Vous pouvez obtenir votre redirecturi pour votre application en utilisant le script « brokerRedirectPrint.ps1 » ou utiliser l’appel d’API mContext.getBrokerRedirectUri. La signature est liée à vos certificats de signature.

 Le modèle actuel de Service Broker est limité à un utilisateur. AuthenticationContext fournit une méthode API pour obtenir l’utilisateur de Service Broker.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 L’utilisateur de Service Broker est renvoyé si le compte est valide.

 Votre manifeste d’application doit disposer des autorisations requises pour utiliser des comptes AccountManager : http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Cette procédure pas à pas devrait vous permettre d’effectuer correctement l’intégration avec Azure Active Directory. Pour d’autres exemples, consultez le référentiel AzureADSamples/ sur GitHub.

## Informations importantes

### Personnalisation

Les ressources du projet de bibliothèque peuvent être remplacées par celles de votre application. Cela se produit lors de la création de votre application. Pour cette raison, vous pouvez personnaliser la mise en page de l’activité d’authentification comme vous le souhaitez. Vous devez veiller à conserver l’ID des contrôles utilisés par ADAL (Webview).

### Service Broker

Le composant Service Broker est remis avec l’application Portail d’entreprise de Microsoft Intune. Le compte est créé dans le Gestionnaire de comptes. Le type de compte est « com.microsoft.workaccount ». Il n’autorise qu’un compte d’authentification unique. Il crée un cookie d’authentification unique pour cet utilisateur après avoir terminé la demande d’appareil pour l’une des applications.

### ADFS et URL de l'autorité

ADFS n’est pas reconnu comme service d’émission de jeton de sécurité (STS) de production ; vous devez donc activer la découverte de l’instance et transmettre la valeur false au constructeur AuthenticationContext.

L’URL de l’autorité a besoin de l’instance STS et du nom du client :https://login.windows.net/yourtenant.onmicrosoft.com

### Interrogation des éléments de cache

ADAL fournit un cache par défaut dans SharedPreferences avec certaines fonctions simples de requête de cache. Vous pouvez récupérer le cache actuel d'AuthenticationContext avec :
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Vous pouvez également fournir votre implémentation de cache, si vous souhaitez le personnaliser.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL fournit une option permettant de spécifier le comportement de l’invite. PromptBehavior.Auto affiche l’interface utilisateur si le jeton d’actualisation n’est pas valide et que les informations d’identification utilisateur sont requises. PromptBehavior.Always ignore l’utilisation du cache et affiche toujours l’interface utilisateur.

### Demande de jeton en mode silencieux à partir du cache et actualisation

Cette méthode n’utilise pas l’affichage de l’interface utilisateur et ne nécessite pas d’activité. Elle renvoie le jeton à partir du cache s’il est disponible. Si le jeton est arrivé à expiration, elle tente de l’actualiser. Si le jeton d’actualisation est arrivé à expiration ou a échoué, elle renvoie AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Vous pouvez également effectuer un appel de synchronisation avec cette méthode. Vous pouvez définir la valeur null pour un rappel ou utiliser acquireTokenSilentSync.

### Diagnostics

Voici les principales sources d'informations pour diagnostiquer les problèmes :

+ Exceptions
+ Journaux
+ Suivis réseau

Notez également que les ID de corrélation sont un élément central des diagnostics dans la bibliothèque. Vous pouvez définir vos ID de corrélation en fonction de chaque demande si vous souhaitez mettre en corrélation une demande de la bibliothèque d’authentification Azure AD (ADAL) avec d’autres opérations dans votre code. Si vous ne définissez aucun ID de corrélation, ADAL génère un ID aléatoire. Tous les messages du journal et appels réseau sont alors marqués avec l’ID de corrélation. L’ID généré automatiquement change à chaque demande.

#### Exceptions

Il s’agit évidemment du premier diagnostic. Nous essayons de fournir des messages d’erreur utiles. Si vous trouvez un qui n’est pas utile, déposez une question et avertissez-nous. Fournissez également des informations sur l’appareil, notamment le modèle et le numéro du Kit de développement (SDK).

#### Journaux

Vous pouvez configurer la bibliothèque pour générer des messages de journal que vous pouvez utiliser pour diagnostiquer les problèmes. Pour configurer la journalisation, effectuez l’appel suivant afin de configurer un rappel qui sera utilisé par ADAL pour transmettre chaque message de journal lorsqu'il sera généré.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Les messages peuvent être écrits dans un fichier journal personnalisé, comme indiqué ci-dessous. Malheureusement, il n’existe aucun moyen standard d’obtenir les journaux d’un appareil. Il existe des services qui peuvent vous y aider. Vous pouvez également inventer votre propre méthode, telle que l’envoi du fichier à un serveur.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### Niveau de journalisation

+ Error (exceptions)
+ Warn (avertissement)
+ Info (information)
+ Verbose (plus de détails)

Vous définissez le niveau de journal comme suit :
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Tous les messages de journal sont envoyés à logcat en plus des rappels des journaux personnalisés. Vous pouvez obtenir un journal sous forme de fichier logcat, comme illustré ci-dessus :

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Plus d’exemples à propos des commandes adb : https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### Suivis réseau

Vous pouvez utiliser différents outils pour capturer le trafic HTTP généré par la bibliothèque ADAL. Cela est particulièrement utile si vous êtes familiarisé avec le protocole OAuth ou si vous avez besoin de fournir des informations de diagnostic à Microsoft ou d’autres canaux de prise en charge.

Fiddler est l'outil de suivi HTTP le plus simple. Utilisez les liens suivants pour le configurer de manière à enregistrer correctement le trafic réseau ADAL. Pour que cela puisse être utile, vous devez configurer fiddler ou tout autre outil tel que Charles pour enregistrer le trafic SSL non chiffré. REMARQUE : les suivis générés de cette manière peuvent contenir des informations hautement privilégiées tels que les jetons d’accès, noms d'utilisateur et mots de passe. Si vous utilisez des comptes de production, ne partagez pas ces suivis avec des tiers. Si vous avez besoin de fournir un suivi à quelqu’un pour obtenir un support technique, reproduisez le problème avec un compte temporaire, avec les noms d’utilisateur et mots de passe que vous êtes prêt à partager.

+ [Configuration de Fiddler pour Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configuration des règles de Fiddler pour la bibliothèque ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### Mode de la boîte de dialogue
La méthode acquireToken sans activité prend en charge l’invite de la boîte de dialogue.

### Chiffrement

ADAL chiffre les jetons et les stocke dans SharedPreferences par défaut. Vous pouvez consulter la classe StorageHelper pour afficher les détails. Android a introduit AndroidKeyStore pour le stockage sécurisé des clés privées de la version 4.3(API18). La bibliothèque ADAL utilise ces informations pour API18 et les versions ultérieures. Si vous souhaitez utiliser la bibliothèque ADAL pour les versions inférieures du Kit de développement (SDK), vous devez fournir une clé secrète à AuthenticationSettings.INSTANCE.setSecretKey.

### Demande de support Oauth2

La classe AuthenticationParameters fournit les fonctionnalités pour obtenir authorization\_uri à partir de la demande de support Oauth2.

### Cookies de session dans Webview

Android Webview n’efface pas les cookies de session après la fermeture de l’application. Vous gérez cela avec l’exemple de code suivant :
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Pour en savoir plus sur les cookies : http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### Remplacements de ressources

La bibliothèque ADAL inclut les chaînes en anglais pour deux messages ProgressDialog suivants.

Votre application doit les remplacer si des chaînes localisées sont désirées.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### Boîte de dialogue NTLM
Adal version 1.1.0 prend en charge la boîte de dialogue NTLM qui est traitée par l’événement onReceivedHttpAuthRequest de WebViewClient. Les chaînes et la mise en page de la boîte de dialogue peuvent être personnalisées.

### Authentification unique entre applications
En savoir plus sur l’[Activation de l'authentification unique entre applications sur Android à l'aide de la bibliothèque ADAL](active-directory-sso-android.md)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->