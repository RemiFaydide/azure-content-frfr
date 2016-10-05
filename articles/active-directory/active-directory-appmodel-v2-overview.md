<properties
	pageTitle="Vue d’ensemble du modèle d’application v2.0 | Microsoft Azure"
	description="Introduction à la création d’applications avec une connexion de compte Microsoft et Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Connecter les utilisateurs de compte Microsoft et d’Azure AD dans une même application

Auparavant, un développeur d’application qui souhaitait prendre en charge à la fois les comptes Microsoft et Azure Active Directory devait opérer une intégration avec deux systèmes distincts. Nous avons mis en place une nouvelle version d’API d’authentification qui vous permet de connecter des utilisateurs avec les deux types de comptes à l’aide du système Azure AD. Ce système d’authentification convergé est appelé **le point de terminaison v2.0**. Avec le point de terminaison v2.0 et moyennant une intégration unique, vous pouvez toucher un public qui représente plusieurs millions d’utilisateurs titulaires aussi bien de comptes personnels que professionnels/scolaires.

Les applications qui utilisent le point de terminaison v2.0 peuvent également consommer des API REST à partir de [Microsoft Graph](https://graph.microsoft.io) et [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) à l’aide d’un type de compte.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## Mise en route
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Choisissez votre plateforme préférée ci-dessous pour créer une application à l’aide de nos bibliothèques et infrastructures open source. Vous pouvez également utiliser notre documentation du protocole OAuth 2.0 et OpenID Connect pour envoyer et recevoir les messages de protocole directement sans utiliser une bibliothèque d’authentification.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Nouveautés
Les informations conceptuelles ici seront utiles pour comprendre ce qui est et ce qui n’est pas possible avec le point de terminaison v2.0.

- Si vous avez créé une application dans le cadre de la version préliminaire du point de terminaison v2.0 2015, veillez à [consulter les dernières modifications du protocole](active-directory-v2-preview-oidc-changes.md) que nous avons récemment apportées.
- Découvrez les [différents types d’application que vous pouvez créer avec le point de terminaison v2.0](active-directory-v2-flows.md).
- Si vous êtes développeur et que vous connaissez bien Azure Active Directory, prenez connaissance des [mises à jour concernant nos protocoles, ainsi que des différences qui caractérisent le point de terminaison v2.0](active-directory-v2-compare.md).
- Familiarisez-vous avec les [limites, restrictions et contraintes](active-directory-v2-limitations.md) du point de terminaison v2.0.

## Référence
Les liens ci-dessous vous seront utiles pour explorer la plateforme en profondeur :

- Build 2016 : [Prise en main des identités Microsoft : authentification de niveau entreprise pour vos applications](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Pour obtenir une aide sur le dépassement de capacité de pile, utilisez les mots-clés [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- [Référence sur le protocole v2.0](active-directory-v2-protocols.md)
- [Référence sur le jeton v2.0](active-directory-v2-tokens.md)
- [Étendues et consentement dans le point de terminaison v2.0](active-directory-v2-scopes.md)
- [Le portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com)
- [Informations de référence sur les API Office 365 REST](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- Vous trouverez ci-dessous les bibliothèques et les exemples open source qui ont été testés avec le point de terminaison v2.0.

  - [Serveur d’identité WSO2 Java](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Client OAuth2 iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Client OAuth2 Android](https://github.com/wuman/android-oauth-client)
  - [Client OpenID Connect Android](https://github.com/kalemontes/OIDCAndroidLib)

<!---HONumber=AcomDC_0921_2016-->