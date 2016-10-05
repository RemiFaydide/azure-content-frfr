<properties
   pageTitle="Bibliothèques d’authentification Azure Active Directory | Microsoft Azure"
   description="La bibliothèque d'authentification Azure AD (ADAL) permet aux développeurs d’applications clientes d’authentifier facilement les utilisateurs sur Active Directory (AD) en local ou sur le cloud, puis d'obtenir des jetons d'accès pour sécuriser les appels d'API."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# Bibliothèques d’authentification d’Azure Active Directory

La bibliothèque d'authentification Azure AD (ADAL) permet aux développeurs d’applications clientes d’authentifier facilement les utilisateurs sur Active Directory (AD) en local ou sur le cloud, puis d'obtenir des jetons d'accès pour sécuriser les appels d'API. ADAL possède de nombreuses fonctionnalités qui facilitent l'authentification aux développeurs, notamment la prise en charge asynchrone, un cache de jeton configurable qui stocke les jetons d'accès et les jetons d'actualisation, l'actualisation automatique des jetons lorsqu'un jeton d'accès expire et qu’un jeton d'actualisation est disponible, et bien plus encore. En gérant l’essentiel de la complexité, la bibliothèque ADAL peut aider les développeurs à se concentrer sur la logique métier dans leur application et à sécuriser facilement les ressources sans être un expert de la sécurité.

La bibliothèque ADAL est disponible sur diverses plateformes.

|Plateforme|Nom du package|Client/serveur|Télécharger|Code source|Documentation et exemples|
|---|---|---|---|---|---|
|Client .NET, Windows Store, Windows Phone (8.1)|Bibliothèque d'authentification Active Directory (ADAL) pour .NET|Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentation](https://msdn.microsoft.com/library/azure/mt417579.aspx)|
|JavaScript|Bibliothèque d'authentification Active Directory (ADAL) pour JavaScript|Client|[ADAL pour JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL pour JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Exemple : [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Bibliothèque d'authentification Active Directory (ADAL) pour Objective-C|Client|[ADAL pour Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io)|[ADAL pour Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Exemple : [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Bibliothèque d'authentification Active Directory (ADAL) pour Android|Client|[ADAL pour Android (The Central Repository)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL pour Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Exemple : [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Bibliothèque d'authentification Active Directory (ADAL) pour Node.js|Client|[ADAL pour Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL pour Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Exemple : [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Intergiciel d'authentification Microsoft Azure Active Directory Passport pour Node|Client|[Azure Active Directory Passport pour Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory pour Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Bibliothèque d'authentification Active Directory (ADAL) pour Java|Client|[ADAL pour Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL pour Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensions des protocoles d’identité pour Microsoft .NET Framework 4.5|Serveur|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Extensions des modèles d'identité Azure AD pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Gestionnaire de jetons web JSON pour Microsoft .NET Framework 4.5|Serveur|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Extensions des modèles d'identité Azure AD pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Intergiciel OWIN qui permet à une application d'utiliser la technologie Microsoft pour l'authentification.|Serveur|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Intergiciel OWIN qui permet à une application d'utiliser OpenIDConnect pour l'authentification.|Serveur|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemple : [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Intergiciel OWIN qui permet à une application d'utiliser WS-Federation pour l'authentification.|Serveur|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemple : [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## Scénarios

Voici trois scénarios courants dans lesquels ADAL peut être utilisée pour l'authentification.

### Authentification des utilisateurs d'une application cliente sur une ressource distante

Dans ce scénario, un développeur a un client, par exemple une application WPF, qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le locataire Azure AD qu’utilise l’API web. Par conséquent, il peut utiliser la bibliothèque ADAL pour faciliter l'authentification avec Azure AD, soit en déléguant complètement l'expérience d'authentification à ADAL, soit en gérant de manière explicite les informations d'identification de l'utilisateur. La bibliothèque ADAL facilite l’authentification de l'utilisateur, l’obtention d’un jeton d'accès et d’un jeton d'actualisation d'Azure AD, puis l’utilisation du jeton d'accès pour effectuer des requêtes à l'API web.

Pour voir un exemple de code qui illustre ce scénario en utilisant l’authentification auprès d’Azure AD, consultez la section [Native Client WPF Application to Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### Authentification d'une application serveur sur une ressource distante

Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. Il dispose d’un abonnement Azure, sait comment appeler le service en aval et connaît le locataire Azure AD qu’utilise l’API web. Par conséquent, il peut utiliser la bibliothèque ADAL pour faciliter l'authentification avec Azure AD en gérant de manière explicite les informations d'identification de l'application. La bibliothèque ADAL facilite la récupération d’un jeton d'Azure AD à l'aide d'informations d'identification client de l'application, puis l’utilisation de ce jeton pour faire des requêtes à l'API web. La bibliothèque ADAL gère également la durée de vie du jeton d'accès en le mettant en cache et en le renouvelant si nécessaire. Pour voir un exemple de code qui illustre ce scénario, consultez la section [Console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### Authentification d'une application serveur pour le compte d'un utilisateur afin d'accéder à une ressource distante

Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. La demande doit également être effectuée au nom d'un utilisateur d’Azure AD. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le locataire Azure AD qu’utilise le service. Une fois l'utilisateur authentifié sur l'application web, l'application peut obtenir d’Azure AD un code d'autorisation pour l'utilisateur. L'application web peut ensuite utiliser ADAL pour obtenir d’Azure AD un jeton d'accès et un jeton d’actualisation pour le compte de l'utilisateur en utilisant le code d'autorisation et les informations d’identification client associés à l'application. Une fois l'application web en possession du jeton d'accès, elle peut appeler l'API web jusqu’à expiration du jeton. Lorsque le jeton expire, l'application web peut utiliser la bibliothèque ADAL pour obtenir un nouveau jeton d'accès en utilisant le jeton d’actualisation reçu précédemment.


## Voir aussi

[Le guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Scénarios d’authentification pour Azure Active Directory](active-directory-authentication-scenarios.md)

[Exemples de code Azure Active Directory](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->