<properties
   pageTitle="Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory | Microsoft Azure"
   description="Apprenez-en plus sur l’implémentation du flux d’octroi implicite OAuth2 d’Azure Active Directory, et déterminez si elle est adaptée à votre application."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)

L’octroi implicite OAuth2 est connu pour présenter le plus grand nombre de problèmes de sécurité de la spécification OAuth2. Pourtant, c’est l’approche implémentée par ADAL JS et celle que nous recommandons pour l’écriture d’applications à page unique. Quelle en est la raison ? Tout est une question de compromis : il s’avère que l’octroi implicite est la meilleure approche vous pouvez adopter pour les applications qui utilisent une API web via JavaScript à partir d’un navigateur.

## Qu’est-ce que l’octroi implicite OAuth2 ?

Fondamentalement, [l’octroi de code d’autorisation OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) est l’octroi d’autorisation qui utilise deux points de terminaison distincts. Le point de terminaison d’autorisation est utilisé pour la phase d’interaction avec l’utilisateur, ce qui génère un code d’autorisation. Le point de terminaison de jeton est ensuite utilisé par le client pour échanger le code avec un jeton d’accès et, souvent, un jeton d’actualisation. Les applications web sont tenues de présenter leurs propres informations d’identification d’application au point de terminaison de jeton, de sorte que le serveur d’autorisation puisse authentifier le client.

L’[octroi implicite OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) est une variante dans laquelle un client peut obtenir un jeton d’accès (et, dans le cas d’[OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html), un jeton id\_token) directement à partir du point de terminaison d’autorisation, sans avoir à contacter le point de terminaison de jeton ni à authentifier l’application cliente. Cette variante a été spécialement conçue pour les applications JavaScript qui s’exécutent dans un navigateur web : dans la spécification OAuth2 d’origine, les jetons sont renvoyés dans un fragment d’URI. Ceci met les bits de jeton à la disposition du code JavaScript dans le client, mais garantit qu’ils ne seront pas inclus dans les redirections vers le serveur. Le renvoi des jetons via le navigateur s’effectue directement à partir du point de terminaison d’autorisation. Il présente également l’avantage d’éliminer tous les appels Cross-Origin, qui sont nécessaires si l’application JavaScript est requise pour contacter le point de terminaison de jeton.

Le fait que ces flux ne retournent jamais de jetons d’actualisation au client représente une caractéristique importante de l’octroi implicite OAuth2. Comme nous allons le voir dans la section suivante, ceci n’est pas vraiment nécessaire et représenterait en fait un problème de sécurité.

## Scénarios adaptés à l’octroi implicite OAuth2

Comme la spécification OAuth2 l’indique, l’octroi implicite a été conçu pour autoriser les applications d’agent utilisateur, c’est-à-dire les applications JavaScript exécutées dans un navigateur. La principale caractéristique de ces applications réside dans le fait que le code JavaScript est utilisé pour accéder aux ressources du serveur (généralement une API web) et adapter l’expérience utilisateur de l’application en conséquence. Pensez à des applications comme Gmail ou Outlook Web Access : lorsque vous sélectionnez un message dans votre boîte de réception, seul le volet de visualisation du message change pour afficher la nouvelle sélection, tandis que le reste de la page reste inchangé. La situation est totalement différente avec les applications web classiques à redirection : chaque interaction utilisateur entraîne une publication de la page entière et un rendu de page complet de la nouvelle réponse du serveur.

Les applications qui poussent l’approche JavaScript à la limite sont appelées « applications à page unique » : l’idée est de ne traiter que la page HTML initiale et le code JavaScript associé. Toutes les interactions postérieures sont gérées par des appels d’API web exécutés par JavaScript. Toutefois, les approches hybrides, où l’application est principalement basée sur la publication mais effectue des appels JS occasionnels, ne sont pas rares. La discussion sur l’utilisation des flux implicites est également pertinente pour ces approches.

En général, les applications à redirection sécurisent leurs demandes par des cookies. Mais cette approche présente une efficacité limitée pour les applications JavaScript. Les cookies ne fonctionnent que sur le domaine pour lequel ils ont été générés, tandis que les appels JavaScript peuvent être dirigés vers d’autres domaines. En fait, ce sera très souvent le cas : pensez aux applications appelant une API Graph Microsoft, une API Office et une API Azure qui, toutes, résident hors du domaine à partir duquel l’application est exécutée. Une tendance croissante pour les applications JavaScript consiste à n’avoir aucun principal et à reposer à 100 % sur des API web tierces pour implémenter leur fonction métier.

Actuellement, la méthode recommandée de protection des appels d’une API Web consiste à utiliser l’approche de jeton de support OAuth2, dans laquelle chaque appel est accompagné d’un jeton d’accès OAuth2. L’API Web examine le jeton d’accès entrant et, si elle y détecte les périmètres nécessaires, elle autorise l’accès à l’opération demandée. Le flux implicite fournit un mécanisme pratique permettant aux applications JavaScript d’obtenir des jetons d’accès pour une API Web, offrant ainsi de nombreux avantages sur les cookies :

- Les jetons peuvent être obtenus de façon fiable sans avoir recours aux appels cross-origin – l’inscription obligatoire de l’URI de redirection vers laquelle les jetons sont retournés garantit que les jetons ne sont pas déplacés.
- Les applications JavaScript peuvent obtenir autant de jetons d’accès que nécessaire, pour un nombre illimité d’API Web ciblées, sans aucune restriction sur les domaines.
- Les fonctionnalités HTML5 comme le stockage de session ou local accordent un contrôle total sur la gestion de la mise en cache et de la durée de vie des jetons, tandis que la gestion des cookies est opaque pour l’application.
- Les jetons d’accès ne sont pas vulnérables aux falsifications de requête intersites (CSRF, Cross Site Request Forgery).

Le flux d’octroi implicite n’émet pas de jetons d’actualisation, principalement pour des raisons de sécurité. L’étendue d’un jeton d’actualisation n’est pas aussi étroite que celle des jetons d’accès. Ainsi, un jeton d’actualisation offre une puissance bien supérieure et peut causer des dommages beaucoup plus lourds en cas de fuite. Dans le flux implicite, les jetons sont remis à l’URL. Par conséquent, le risque d’interception est plus élevé qu’avec l’octroi de code d’autorisation.

Toutefois, notez qu’une application JavaScript a un autre mécanisme à sa disposition pour renouveler les jetons d’accès sans inviter l’utilisateur à entrer ses informations d’identification à plusieurs reprises. L’application peut utiliser un IFrame masqué pour effectuer de nouvelles demandes de jeton sur le point de terminaison d’autorisation d’Azure AD : tant que le navigateur a une session active (c’est-à-dire qu’il dispose d’un cookie de session) sur le domaine Azure AD, la demande d’authentification peut s’effectuer correctement sans aucune intervention de l’utilisateur.

Ce modèle permet à l’application JavaScript de renouveler les jetons d’accès en toute autonomie, voire d’en acquérir d’autres pour une nouvelle API (à condition que l’utilisateur les ait préalablement acceptés). Cela évite les tâches d’acquisition, de maintenance et de protection d’un artefact aussi précieux qu’un jeton d’actualisation. L’artefact qui permet un renouvellement silencieux, le cookie de session Azure AD, est géré en dehors de l’application. Autre avantage de cette approche : un utilisateur peut se déconnecter d’Azure AD, à l’aide des applications signées dans Azure AD et en cours d’exécution dans les onglets de navigateur. Cela entraîne la suppression du cookie de session Azure AD, l’application JavaScript perdant automatiquement la capacité de renouveler les jetons pour l’utilisateur déconnecté.

## L’octroi implicite est-il adapté à mon application ?

L’octroi implicite présente plus de risques que d’autres octrois. Les aspects auxquels vous devez prêter attention sont bien documentés. Par exemple, consultez [Misuse of Access Token to Impersonate Resource Owner in Implicit Flow][OAuth2-Spec-Implicit-Misuse] \(Utilisation incorrecte du jeton d’accès pour emprunter l’identité d’un propriétaire des ressources dans un flux implicite) et [OAuth 2.0 Threat Model and Security Considerations][OAuth2-Threat-Model-And-Security-Implications] \(Modèle de menace OAuth 2.0 et considérations de sécurité). Toutefois, le profil de risque supérieur est principalement dû au fait qu’il est destiné à autoriser les applications qui exécutent du code actif, envoyé à un navigateur par une ressource distante. Si vous optez pour une architecture d’application à page unique, que vous n’avez aucun composant principal ou que vous envisagez d’appeler une API Web à l’aide de JavaScript, l’utilisation du flux implicite pour l’acquisition de jeton est recommandée.

Si votre application est un client natif, le flux implicite n’est pas idéal. L’absence du cookie de session Azure AD dans le contexte d’un client natif empêche votre application de maintenir une session longue. En d’autres termes, votre application sollicite régulièrement l’utilisateur pour obtenir des jetons d’accès aux nouvelles ressources.

Si vous développez une application Web comprenant un composant principal et qui utilise une API à partir de son code principal, le flux implicite n’est pas non plus la solution idéale. D’autres modes d’octroi d’autorisation sont beaucoup plus puissants. Par exemple, l’octroi d’informations d’identification du client OAuth2 permet d’obtenir des jetons correspondant aux autorisations attribuées à l’application elle-même, et non aux délégations de l’utilisateur. Le client conserve ainsi l’accès par programmation aux ressources, même lorsqu’un utilisateur n’est pas actif dans une session et ainsi de suite. De plus, ces modes d’octroi offrent de meilleurs gages de sécurité. Par exemple, les jetons d’accès ne transitent jamais par le navigateur de l’utilisateur. Ils ne risquent donc pas d’être enregistrés dans l’historique du navigateur. L’application cliente peut également effectuer une authentification forte lorsqu’elle demande un jeton.

## Étapes suivantes

- Pour obtenir la liste complète des ressources de développement, ainsi que des informations de référence sur les protocoles et les flux d’octroi d’autorisation OAuth2 pris en charge par Azure AD, consultez le [Guide du développeur Azure Active Directory][AAD-Developers-Guide].
- Consultez la page [Intégration avec Azure Active Directory][ACOM-How-To-Integrate] pour des informations plus détaillées sur le processus d’intégration d’applications.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

<!---HONumber=AcomDC_0817_2016-->