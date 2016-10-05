<properties
	pageTitle="Types du point de terminaison v2.0 | Microsoft Azure"
	description="Les types d’applications et de scénarios pris en charge par le point de terminaison v2.0 Azure AD."
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

# Types de point de terminaison v2.0
Le point de terminaison v2.0 prend en charge l’authentification pour de multiples architectures modernes d’application, toutes basées sur les protocoles standard [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) et/ou [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Ce document décrit brièvement les types d’applications pouvant être créées, indépendamment de la langue ou de la plate-forme souhaitées. Il vous aidera à comprendre les scénarios de niveau supérieur avant d’[entrer dans le code](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## Concepts de base
Toutes les applications qui utilisent le point de terminaison v2.0 doivent être inscrites à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

- un **ID d’application** qui identifie de manière unique votre application ;
- un **URI de redirection** pouvant être utilisé pour diriger les réponses vers votre application ;
- quelques valeurs spécifiques au scénario. Pour plus de détails, découvrez comment [inscrire une application](active-directory-v2-app-registration.md).

Une fois inscrite, l’application communique avec Azure AD en transmettant les requêtes au point de terminaison Azure Active Directory v2.0. Nous fournissons des infrastructures et des bibliothèques open source qui prennent en charge les détails de ces demandes. Vous pouvez aussi implémenter la logique d’authentification vous-même en créant des demandes sur ces points de terminaison :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## les applications web
Pour les applications Web (.NET, PHP, Java, Ruby, Python, Node, etc.) accessibles via un navigateur, vous pouvez appliquer la connexion utilisateur à l’aide d’[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Dans OpenID Connect, l’application Web reçoit un élément `id_token`, un jeton de sécurité qui vérifie l’identité de l’utilisateur et vérifie les informations le concernant sous la forme de revendications :

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

Pour en savoir plus sur les différents types de jetons et de revendications disponibles pour une application, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md).

Dans les applications de serveur Web, le flux d’authentification de connexion respecte cette procédure de niveau supérieur :

![Images de couloirs d’application Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

La validation du jeton id\_token à l’aide d’une clé de signature publique reçue du point de terminaison v2.0 suffit à garantir l’identité de l’utilisateur. Par ailleurs, cette opération permet de définir un cookie de session pouvant être utilisé pour identifier l’utilisateur sur les demandes de page suivantes.

Pour voir ce scénario en action, exécutez l’un des exemples de code de connexion d’application Web de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

En plus de la connexion simple, une application de serveur Web peut également nécessiter l’accès à d’autres services Web, comme une API REST. Dans ce cas, l’application de serveur Web peut s’engager dans un flux OpenID Connect et OAuth 2.0, à l’aide du [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Ce scénario est abordé ci-dessous dans la [rubrique de prise en main des applications Web-API Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## API Web
Vous pouvez utiliser le point de terminaison v2.0 pour sécuriser également les services Web, comme l’API Web RESTful de votre application. En lieu et place des jetons id\_token et des cookies de session, les API Web utilisent les jetons d’accès OAuth 2.0 pour sécuriser les données et authentifier les requêtes entrantes. L’appelant d’une API Web ajoute un jeton d’accès dans l’en-tête d’autorisation d’une requête HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API Web peut ensuite utiliser le jeton d’accès pour vérifier l’identité de l’appelant de l’API et extraire les informations à son sujet, à partir des revendications encodées dans le jeton d’accès. Pour en savoir plus sur les différents types de jetons et de revendications disponibles pour une application, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md).

Une API Web peut octroyer aux utilisateurs la possibilité d’accepter/de refuser certaines fonctionnalités ou données en exposant des autorisations ; on parle ici d’[étendues](active-directory-v2-scopes.md). Pour qu’une application appelante puisse acquérir l’autorisation à une étendue, l’utilisateur doit accepter l’étendue au cours d’un flux. Le point de terminaison v2.0 se charge de demander l’autorisation à l’utilisateur et d’enregistrer ces autorisations dans l’ensemble des jetons d’accès reçus par l’API Web. Toutes les API Web doivent valider les jetons d’accès reçus lors de chaque appel et exécuter les contrôles d’autorisation appropriés.

Une API Web peut recevoir des jetons d’accès de tous types d’applications, notamment des applications de serveur Web, des applications de bureau et mobiles, des applications de page unique, des démons côté serveur, et même d’autres API Web. Le flux de haut niveau pour l’authentification d’API web est le suivant :

![Images de couloirs d’API Web](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Pour en savoir plus sur les codes d'autorisation, les jetons d'actualisation et la procédure détaillée de récupération des jetons d’accès, consultez cette rubrique sur le [protocole OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Pour savoir comment sécuriser une API Web avec les jetons d’accès OAuth2, consultez les exemples de code d’API Web de notre [section Prise en main](active-directory-appmodel-v2-overview.md#getting-started).


## Applications mobiles et natives
Les applications installées sur un appareil, comme les applications de bureau et les applications mobiles nécessitent bien souvent l’accès à des services principaux ou des API Web, qui stockent les données et exécutent différentes fonctions pour le compte d’un utilisateur. Ces applications peuvent ajouter des fonctionnalités de connexion et d’autorisation à des services principaux à l’aide du [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Dans ce flux, l’application reçoit un code d’autorisation du point de terminaison v2.0 lors de la connexion de l’utilisateur. Cette étape correspond à l’octroi à l’application de l’autorisation d’appeler les services principaux pour le compte de l’utilisateur actuellement connecté. L’application peut ensuite échanger le code d’autorisation dans l’arrière-plan, contre un jeton d’accès et un jeton d’actualisation OAuth 2.0. L’application peut utiliser le jeton d’accès pour s’authentifier sur les API Web dans les requêtes HTTP et solliciter le jeton d’actualisation afin de récupérer de nouveaux jetons d’accès une fois les anciens expirés.

![Images de couloirs d’application native](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Applications à page unique (javascript)
De nombreuses applications modernes présentent une application frontale à page unique (SPA) écrite principalement en Javascript, utilisant bien souvent des infrastructures comme AngularJS, Ember.js, Durandal, etc. Le point de terminaison Azure AD v2.0 prend en charge ces applications à l’aide du [flux implicite OAuth 2.0](active-directory-v2-protocols-implicit.md).

Dans ce flux, l'application reçoit des jetons directement du point de terminaison d'autorisation v2.0, sans exécuter de serveur principal pour des échanges de serveurs. Cela permet d'exécuter tout traitement de logique d'authentification et de gestion de sessions entièrement dans le client javascript, sans effectuer de redirections de pages supplémentaires.

![Images de couloirs de flux implicites](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Pour voir ce scénario en action, exécutez l'un des exemples de code de page unique de la section [Mise en route](active-directory-appmodel-v2-overview.md#getting-started).

## Limitations actuelles
Ces types d’applications ne sont pas actuellement pris en charge par le point de terminaison v2.0, mais cela est prévu. Les autres limites et restrictions associées au point de terminaison v2.0 sont décrites dans l’[article sur les limites v2.0](active-directory-v2-limitations.md).

### Applications côté démons/serveur
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API Web. Ces applications peuvent s'authentifier et récupérer des jetons à l'aide de l'identité d'application (plutôt qu'avec l'identité déléguée d'un utilisateur), avec le flux des informations d'identification du client OAuth 2.0.

Le flux d’informations d’identification de client n’est pas pris en charge dans le point de terminaison v2.0. Pour observer le fonctionnement de ce flux dans le service Azure AD disponible généralement, consultez l’[exemple de code démon sur GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### API Web chaînées (On-Behalf-Of)
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par le point de terminaison v2.0. Ce scénario est courant dans les clients natifs qui disposent d’une API Web principale, qui à son tour appelle un service Microsoft Online, comme Office 365 ou l’API Graph.

Ce scénario d’API Web chaînée peut être pris en charge à l’aide de la concession des informations d’identification du porteur OAuth 2.0 Jwt, également appelé [flux On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Toutefois, le flux On-Behalf-Of n’est pas actuellement implémenté dans le point de terminaison v2.0. Pour observer le fonctionnement de ce flux dans le service Azure AD disponible généralement, consultez l’[’exemple de code On-Behalf-Of sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_0921_2016-->