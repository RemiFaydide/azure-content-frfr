<properties
	pageTitle="Limitations et restrictions du point de terminaison V2.0 | Microsoft Azure"
	description="Une liste des limitations et restrictions associées au point de terminaison v2.0 Azure AD."
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

# Dois-je utiliser le point de terminaison v2.0 ?

Lorsque vous créez des applications qui s’intègrent à Azure Active Directory, vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 satisferont vos besoins. Le modèle d’application Azure AD d’origine est toujours intégralement pris en charge. À certains égards, il est plus riche en fonctionnalités que le point de terminaison v2.0. Toutefois, le point de terminaison v2.0 [octroie d’importants avantages](active-directory-v2-compare.md) aux développeurs, ce qui peut vous inciter à utiliser le nouveau modèle de programmation. Au fil du temps, le point de terminaison v2.0 évoluera pour prendre en charge l’intégralité des fonctions Azure AD. Dès lors, vous n’aurez aucunement besoin de recourir à une autre solution.

À ce stade, vous pouvez valoriser deux fonctionnalités principales avec le point de terminaison v2.0 :

- Connexion des utilisateurs avec des comptes personnels et professionnels.
- Appel de l’[API Outlook convergée](https://dev.outlook.com).

Ces deux fonctionnalités peuvent être implémentées dans des applications web, mobiles et PC. Si ces fonctionnalités relativement limitées vous semblent adaptées à votre application, nous vous recommandons d’utiliser le point de terminaison v2.0. Si votre application nécessite des fonctionnalités supplémentaires des services Microsoft, nous vous recommandons de continuer à utiliser les points de terminaison éprouvés d’Azure AD et le compte Microsoft. Avec le temps, les points de terminaison v2.0 ont vocation à remplacer Azure AD et le compte Microsoft ; nous aiderons l’ensemble des développeurs à effectuer leur transition vers le point de terminaison v2.0.

En attendant, cet article vous aide à déterminer si le point de terminaison v2.0 est approprié pour vous. Nous continuerons à mettre à jour cet article au fil du temps afin de prendre en compte l’évolution du point de terminaison v2.0. Aussi, prenez le temps de remettre régulièrement vos exigences en correspondance avec les fonctionnalités du point de terminaison v2.0.

Si vous disposez d’une application associée à Azure AD qui ne recourt pas au point de terminaison v2.0, il n’est pas nécessaire de repartir de zéro. À l’avenir, nous vous fournirons un moyen de configurer vos applications Azure AD existantes pour l’utilisation avec le point de terminaison v2.0.

## Restrictions sur les applications
Les types d’application suivants ne sont actuellement pas pris en charge par le point de terminaison v2.0. Pour obtenir une description des types d’applications pris en charge, consultez [cet article](active-directory-v2-flows.md).

##### API Web autonome
Avec le point de terminaison v2.0, vous avez la possibilité de [concevoir une API web sécurisée à l’aide d’OAuth 2.0](active-directory-v2-flows.md#web-apis). Toutefois, cette API web pourra recevoir uniquement les jetons d’une application qui partage le même ID d’application. La création d’une API web accessible par un client présentant un ID d’application différent n’est pas prise en charge. Ce client ne pourra pas demander ou obtenir d’autorisation d’accès à votre API web.

Pour voir comment créer une API Web qui accepte des jetons d’un client présentant un ID d’application identique, consultez les exemples d’API Web de point de terminaison v2.0 de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

##### Applications côté démons/serveur
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API Web. Ces applications peuvent s'authentifier et récupérer des jetons à l'aide de l'identité d'application (plutôt qu'avec l'identité déléguée d'un utilisateur), avec le flux des informations d'identification du client OAuth 2.0.

Ce flux n’est pas actuellement pas pris en charge par le point de terminaison v2.0. Concrètement, cela signifie que les applications peuvent récupérer des jetons uniquement après l’exécution d’un flux interactif de connexion utilisateur. Le flux des informations d’identification du client sera ajouté très prochainement. Si vous souhaitez consulter le flux d’informations d’identification du client à l’aide du point de terminaison Azure AD d’origine, consultez l’[exemple de démon sur GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### Flux On-Behalf-Of d’API web
De nombreuses architectures incluent une API web qui doit appeler une autre API Web en aval, toutes deux sécurisées par le point de terminaison v2.0. Ce scénario est courant dans les clients natifs qui disposent d’une API web principale, qui à son tour appelle un service Microsoft Online ou une autre API web personnalisé qui prend en charge Azure AD.

Ce scénario peut être pris en charge à l’aide de la concession des informations d’identification du porteur OAuth 2.0 Jwt, également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n’est actuellement pas pris en charge pour le point de terminaison v2.0. Pour observer le fonctionnement de ce flux dans le service Azure AD disponible généralement, consultez l’[’exemple de code On-Behalf-Of sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restrictions sur les inscriptions d’application
À ce stade, l’ensemble des applications dont l’intégration avec le point de terminaison v2.0 est envisagée doivent créer une nouvelle inscription d’application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Aucune application existante Azure AD ou de compte Microsoft, ou application inscrite dans un portail différent du nouveau portail d’inscription d’application n’est compatible avec le point de terminaison v2.0. Nous envisageons de proposer un moyen de prise en charge des applications existantes en tant qu’applications v2.0, mais à ce stade, il n’existe aucun chemin de migration d’une application vers le point de terminaison v2.0.

De la même manière, les applications inscrites dans le nouveau portail d’inscription des applications ne fonctionneront pas avec le point de terminaison d’authentification d’origine Azure AD. Vous pouvez, cependant, utiliser les applications créées dans le portail d’inscription des applications pour procéder à une intégration avec le point de terminaison d’authentification du compte Microsoft, `https://login.live.com`.

Les applications inscrites dans le nouveau portail d’inscription des applications sont actuellement limitées à un jeu limité de valeurs redirect\_uri. Les valeurs redirect\_uri pour les services ou applications Web doivent démarrer par le schéma ou `https`, tandis que les valeurs redirect\_uri pour toutes les autres plateformes doivent utiliser les valeurs codées en dur de `urn:ietf:oauth:2.0:oob`.

## Restrictions de l’URI de redirection
Pour les applications web, des valeurs de redirect\_uri doivent toutes partager un domaine DNS unique. Par exemple, il n’est pas possible d’inscrire une application web qui a des URI de redirection (redirect\_uris) :

`https://login-east.contoso.com` `https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet du redirect\_uri existant avec le nom DNS du redirect\_uri que vous ajoutez. Si le nom DNS complet du nouveau redirect\_uri ne correspond pas exactement au nom DNS du redirect\_uri existant, ou si le nom DNS entier du nouveau redirect\_uri n’est pas un sous-domaine du redirect\_uri existant, la demande d’ajout échoue. Par exemple, si l’application possède actuellement un redirect\_uri :

`https://login.contoso.com`

Il est alors possible d’ajouter :

`https://login.contoso.com/new`

qui correspond exactement au nom DNS, ou :

`https://new.login.contoso.com`

qui est un sous-domaine DNS de login.contoso.com. Si vous souhaitez disposer d’une application avec login-east.contoso.com et login-west.contoso.com en tant que redirect\_uris, vous devez ajouter les redirect\_uris suivants dans l’ordre :

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

Les deux derniers peuvent être ajoutés, car ils sont des sous-domaines du première redirect\_uri, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Pour savoir comment inscrire une application dans le nouveau portail d’inscription des applications, consultez [cet article](active-directory-v2-app-registration.md).

## Restrictions sur les services et API
Le point de terminaison v2.0 prend actuellement en charge la connexion de toute application inscrite dans le nouveau portail d’inscription des applications, à condition qu’elles appartiennent à la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md). Toutefois, ces applications pourront obtenir des jetons d’accès OAuth 2.0 uniquement pour un ensemble très limité de ressources. Le point de terminaison v2.0 émet des valeurs access-token uniquement pour :

- L’application qui a demandé le jeton. Une application peut acquérir une valeur access-token pour son propre compte, si l’application logique est composée de plusieurs composants ou niveaux. Pour voir ce scénario en action, consultez nos didacticiels [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).
- La messagerie Outlook, le calendrier et API REST de Contacts, qui se trouvent àhttps://outlook.office.com. Pour savoir comment écrire une application qui accède à ces API, consultez ces didacticiels [Prise en main Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- Les API Microsoft Graph. Pour en savoir plus sur Microsoft Graph et sur toutes les données disponibles, consultez le site [https://graph.microsoft.io](https://graph.microsoft.io).

Aucun autre service n’est actuellement pris en charge. Davantage de services Microsoft Online seront ajoutés prochainement, tout comme la prise en charge de vos propres services et API Web personnalisés.

## Restrictions sur les bibliothèques et les kits de développement logiciel
Pour vous aider à faire ces tests, nous mettons à votre disposition une version expérimentale de la bibliothèque d’authentification Active Directory, compatible avec le point de terminaison v2.0. Toutefois, cette version de la bibliothèque d’authentification Active Directory est en phase d’évaluation. Elle n’est pas prise en charge et ne sera pas modifiée en profondeur au cours des prochains mois. Si vous souhaitez obtenir rapidement une application exécutée avec le point de terminaison v2.0, consultez notre section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started) pour des exemples de codes utilisant la bibliothèque d’authentification Active Directory pour .NET, iOS, Android et Javascript.

Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

- Si vous générez une application web, vous pouvez en toute sécurité utiliser notre middleware mis à la disposition générale côté serveur afin de vous connecter et de procéder à la validation des jetons. Vous recourrez notamment au middleware OWIN Open ID Connect pour ASP.NET et à notre plug-in NodeJS Passport. Des exemples de codes utilisant ces middlewares sont également disponibles dans notre section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).
- Pour d’autres plateformes et pour les applications natives et mobiles, vous pouvez également procéder à l’intégration avec le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans votre code d’application. Les protocoles v2.0 OpenID Connect et OAuth [ont été explicitement documentés](active-directory-v2-protocols.md) afin de vous aider à effectuer une telle intégration.
- Enfin, vous pouvez utiliser les bibliothèques open source Open ID Connect et OAuth pour procéder à l’intégration avec le point de terminaison v2.0. Le protocole v2.0 devrait être compatible avec de nombreuses bibliothèques de protocole open source, sans modification majeure. La disponibilité de telles bibliothèques varie en fonction des langues et des plateformes, et les sites web [Open ID Connect](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) conservent des listes d’implémentations populaires. Vous trouverez ci-dessous les bibliothèques et les exemples open source qui ont été testés avec le point de terminaison v2.0.

  - [Serveur d’identité WSO2 Java](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Client OAuth2 iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Client OAuth2 Android](https://github.com/wuman/android-oauth-client)
  - [Client OpenID Connect Android](https://github.com/kalemontes/OIDCAndroidLib)

## Restrictions sur les protocoles
Le point de terminaison v2.0 prend uniquement en charge Open ID Connect et OAuth 2.0. Toutefois, certaines des fonctionnalités de ces protocoles n’ont pas été intégrées dans le point de terminaison v2.0. Voici quelques exemples :

- Le paramètre `end_sesssion_endpoint` OpenID Connect
- Octroi des informations d’identification du client OAuth 2.0

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le point de terminaison v2.0, consultez notre page de [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

## Fonctionnalités avancées de développeur Azure AD
Un ensemble de fonctionnalités de développeur disponible dans le service Azure Active Directory n’est pas encore pris en charge pour le point de terminaison v2.0. Il s’agit notamment des composantes suivantes :

- Revendications de groupe pour les utilisateurs Azure AD
- Rôles d’application et revendications de rôle

<!---HONumber=AcomDC_0921_2016-->