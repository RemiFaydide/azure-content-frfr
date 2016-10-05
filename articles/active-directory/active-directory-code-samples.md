<properties
   pageTitle="Exemples de code Azure Active Directory | Microsoft Azure"
   description="Index des exemples de code Azure Active Directory, organisé par scénario."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# Exemples de code Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Vous pouvez utiliser Microsoft Azure Active Directory (Azure AD) pour ajouter l'authentification et l'autorisation à vos applications web et API web. Cette section vous renvoie à des exemples de code qui vous montrent comment procéder et à des extraits de code que vous pouvez utiliser dans vos applications. Sur la page d’exemples de code, vous trouverez des rubriques Lisez-moi qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Le code est également accompagné de commentaires pour vous aider à comprendre les sections critiques.

Pour comprendre le scénario de base de chaque type d'exemple, consultez les scénarios d'authentification pour Azure AD.

Contribuer à nos exemples sur GitHub : [Exemples et documentation Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## Navigateur web vers application web

Ces exemples montrent comment écrire une application web qui dirige le navigateur de l'utilisateur pour le connecter à Azure AD.



| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Utiliser OpenID Connect (middleware OWIN ASP.Net OpenID Connect) pour authentifier les utilisateurs à partir d’un client Azure AD.
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Application web MVC .NET mutualisée qui utilise OpenID Connect (middleware OWIN OpenID Connect ASP.Net) pour authentifier les utilisateurs à partir de plusieurs clients Azure.
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Utiliser WS-Federation (middleware OWIN ASP.Net WS-Federation) pour authentifier les utilisateurs à partir d’un client Azure AD.






## Application à page unique (SPA)

Cet exemple montre comment écrire une application à page unique sécurisée avec Azure AD.

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Utiliser une bibliothèque ADAL pour JavaScript et Azure AD pour sécuriser une application à page unique basée sur AngularJS implémentée avec un serveur principal de l’API web ASP.NET.


## Application native vers API web

Ces exemples de code montrent comment créer des applications clientes natives qui appellent des API web sécurisées par Azure AD. Ils utilisent [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) et [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Utiliser le plug-in de la bibliothèque ADAL pour Apache Cordova pour générer une application Apache Cordova qui appelle une API web et utilise Azure AD pour l’authentification.
| C#/.NET | [NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Application WPF .NET qui appelle une API web sécurisée à l’aide d’Azure AD.
| C#/.NET | [NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Application Windows Store qui appelle une API web sécurisée avec Azure AD.
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Application Windows Store qui appelle une API web mutualisée sécurisée avec Azure AD.
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Application cliente native qui appelle une API web qui obtient un jeton pour agir au nom de l’utilisateur d’origine, puis utilise ensuite le jeton pour appeler une autre API web.
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Application Windows Store pour Windows Phone 8.1 qui appelle une API web sécurisée par Azure AD.
| ObjC | [NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) | Application iOS qui appelle une API web qui nécessite Azure AD pour l’authentification.
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Application cliente native qui inclut la logique permettant de traiter un jeton JWT dans une API web au lieu d’utiliser le middleware OWIN.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Liaison Xamarin à la bibliothèque Azure AD Authentication Library (ADAL) native pour la bibliothèque Android.
| C#/Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Liaison Xamarin à la bibliothèque Azure AD Authentication Library (ADAL) native pour iOS.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Projet Xamarin qui cible cinq plateformes et appelle une API web sécurisée par Azure AD.
| C#/.NET | [NativeClient-Headless-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Application native qui effectue une authentification non interactive et appelle une API web sécurisée par Azure AD.



## Application web vers API web

Ces exemples de code montrent comment utiliser [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour générer des applications web qui appellent des API web sécurisées par Azure AD.

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Appeler une API web avec les autorisations de l’utilisateur connecté.
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Appeler une API web avec les autorisations de l’application.
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Ajouter une autorisation avec [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) à une application web existante afin de pouvoir appeler une API web.
| JavaScript | [WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configurer un service API REST intégré à Azure AD pour la protection de l’API. Inclut un serveur Node.js avec une API web.
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) | Application web MVC mutualisée qui utilise OpenID Connect (middleware OWIN OpenID Connect ASP.Net) pour authentifier les utilisateurs à partir d’un client Azure. Utilise un code d'autorisation pour appeler l'API Graph.

## Application serveur ou démon vers API Web

Ces exemples de code montrent comment créer une application démon ou serveur qui obtient des ressources provenant d’une API web en utilisant [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) et [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Application console qui appelle une API web. Les informations d'identification du client forment un mot de passe.
| C#/.NET | [Démon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Application console qui appelle une API web. Les informations d'identification du client forment un certificat.


## Appel de l'API Graph d'Azure AD

Ces exemples de code montrent comment créer des applications qui appellent l'API Graph d'Azure AD pour lire et écrire des données d'annuaire.

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Application web qui utilise l’API Graph pour accéder aux données d’annuaire Azure AD.
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Application web qui utilise l’API Graph pour accéder aux données d’annuaire Azure AD.
| C#/.NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Application web qui utilise l’API Graph pour accéder aux données d’annuaire Azure AD.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Cette application console présente les appels en lecture et écriture les plus courants vers l’API Graph et indique comment exécuter une attribution de licence utilisateur et mettre à jour la photo miniature et les liens d’un utilisateur.
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Application console qui utilise la requête différentielle dans l’API Graph pour obtenir les modifications périodiques apportées aux objets utilisateur dans un client Azure AD.
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Application MVC qui utilise des requêtes de l’API Graph pour générer un organigramme d’entreprise simple.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Application PHP qui appelle l’API Graph pour inscrire une extension, puis pour lire, mettre à jour et supprimer des valeurs dans l’attribut d’extension.


## Autorisation

Ces exemples de code montrent comment utiliser Azure AD pour l'autorisation.

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Effectuer un contrôle d’accès en fonction du rôle (RBAC) à l’aide de revendications de groupe Active Directory Azure dans une application qui est intégrée avec Azure AD.
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Effectuer un contrôle d’accès en fonction du rôle (RBAC) à l’aide de rôles d’application Active Directory Azure dans une application qui est intégrée avec Azure AD.


## Procédures pas à pas héritées

Ces procédures pas à pas utilisent une technologie légèrement plus ancienne mais peuvent toujours présenter un intérêt.

| Langue/plateforme | Exemple | Description
| ----------------- | ------ | -----------
| C#/.NET | [Autorisation basée sur un rôle et sur une liste de contrôle d’accès dans une application Microsoft Azure AD de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=331694) | Effectuer une autorisation en fonction du rôle (RBAC) et une autorisation basée sur une liste de contrôle d’accès dans une application qui est intégrée avec Azure AD.
| C#/.NET | [AAL - application Windows Store vers service REST - authentification](http://go.microsoft.com/fwlink/?LinkId=330605) | Utiliser [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) (anciennement AAL) pour Windows Store Beta pour ajouter des fonctionnalités d’authentification utilisateur à une application Windows Store.
| C#/.NET | [ADAL - application native vers service REST - authentification avec AAD via une boîte de dialogue du navigateur](http://go.microsoft.com/fwlink/?LinkId=259814) | Utiliser [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) pour ajouter des fonctionnalités d’authentification utilisateur à un client WPF.
| C#/.NET | [ADAL - application native vers service REST - authentification avec ACS via une boîte de dialogue du navigateur](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | Utiliser [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) et [Access Control Service 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) pour ajouter des fonctionnalités d’authentification utilisateur à un client WPF.
| C#/.NET | [ADAL - authentification serveur à serveur](http://go.microsoft.com/fwlink/?LinkId=259816) | Utiliser [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) pour sécuriser les appels de service d’un processus côté serveur à un service REST d’API web MVC 4.
| C#/.NET | [Ajout de l’authentification à votre application web à l’aide d’Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configurer une application .NET permettant d’exécuter une authentification unique web sur votre annuaire d’entreprise Azure AD.
| C#/.NET | [Développement d’une application web mutualisée avec Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Utiliser Azure AD pour ajouter l’authentification unique et les fonctionnalités d’accès aux annuaires d’une application .NET pour travailler dans plusieurs organisations.
JAVA | [Exemple d’application Java pour l’API Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=263969) | Utiliser l’API Graph pour accéder aux données d’annuaire à partir d’Azure AD.
PHP | [Exemple d’application PHP pour l’API Azure AD Graph](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Utiliser l’API Graph pour accéder aux données d’annuaire à partir d’Azure AD.
| C#/.NET | [Exemple d’application pour l’API Azure AD Graph](http://go.microsoft.com/fwlink/?LinkID=262648) | Utiliser l’API Graph pour accéder aux données d’annuaire à partir d’Azure AD.
| C#/.NET | [Exemple d’application pour la requête différentielle d’Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) | Utiliser la requête différentielle dans l’API Graph pour obtenir les modifications périodiques apportées aux objets utilisateur dans un client Azure AD.
| C#/.NET | [Exemple d’application pour l’intégration d’une application cloud mutualisée pour Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Intégrer une application mutualisée dans Azure AD.
| C#/.NET | [Sécurisation d’une application Windows Store et du service web REST avec Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Créer une ressource API web simple et une application cliente Windows Store à l’aide d’Azure AD et de la bibliothèque [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md).
| C#/.NET| [Utilisation de l’API Graph pour interroger Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configurer une application Microsoft .NET pour utiliser l’API Azure AD Graph pour accéder aux données à partir d’un annuaire client Azure AD.

## Voir aussi

##### Autres ressources

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

<!---HONumber=AcomDC_0921_2016-->