<properties
	pageTitle="Le point de terminaison v2.0 Azure AD | Microsoft Azure"
	description="Une comparaison entre la version Azure AD d’origine et les points de terminaison v2.0."
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

# Quelles sont les particularités du point de terminaison v2.0 ?

Si vous connaissez bien le service Azure Active Directory ou si vous avez déjà intégré des applications à Azure AD dans le passé, il peut exister des différences dans le point de terminaison v2.0 auxquelles vous ne vous attendiez pas. Pour votre bonne compréhension, ce document énumère ces différences.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).


## Comptes Microsoft et comptes Azure AD
Le point de terminaison v2.0 permet aux développeurs d’écrire des applications qui prennent en charge la connexion à partir de comptes Microsoft et de comptes Azure AD, à l’aide d’un seul point de terminaison d’authentification. Vous pouvez donc écrire votre application en ignorant complètement le type de compte avec lequel l’utilisateur se connecte. Bien entendu, vous *pouvez* faire en sorte que votre application reconnaisse le type de compte utilisé dans une session particulière, mais vous n’y êtes pas tenu.

Par exemple, si votre application appelle [Microsoft Graph](https://graph.microsoft.io), certaines fonctionnalités et données supplémentaires seront disponibles pour les utilisateurs de l’entreprise, telles que leurs sites SharePoint ou les données Directory. Néanmoins, pour de nombreuses actions, telles que la [lecture du courrier d’un utilisateur](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), le code peut être exactement le même pour les comptes Microsoft et Azure AD.

L’intégration de votre application aux comptes Microsoft et Azure AD est désormais un simple processus. Vous pouvez utiliser un seul et même ensemble de points de terminaison, une seule et même bibliothèque et une seule et même inscription d’application pour accéder aux mondes des consommateurs et de l’entreprise. Pour en savoir plus sur le point de terminaison v2.0, consultez [la vue d’ensemble](active-directory-appmodel-v2-overview.md).


## Nouveau portail d’inscription des applications
Le point de terminaison v2.0 ne peut être inscrit que dans un nouvel emplacement : [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Il s’agit du portail où vous pouvez obtenir un ID d’application, personnaliser l’apparence de la page de connexion de votre application, et bien plus encore. Pour accéder au portail, vous devez avoir un compte Microsoft alimenté : compte personnel ou professionnel/scolaire.

Nous allons continuer à ajouter des fonctionnalités à ce portail d’inscription des applications. Notre souhait est que ce portail soit le nouvel emplacement où vous pouvez gérer absolument tout ayant un rapport avec vos applications Microsoft.


## Un ID d’application pour toutes les plateformes
Dans le service Azure Active Directory d’origine, vous pouvez avoir inscrit plusieurs applications différentes pour un seul et même projet. Vous avez été contraint d’utiliser des inscriptions d’application distinctes pour vos applications clientes et web natives :

![Ancienne interface utilisateur de l’inscription des applications](../media/active-directory-v2-flows/old_app_registration.PNG)

Par exemple, si vous avez créé un site web et une application iOS, vous avez dû les inscrire séparément, en utilisant deux ID d’application différents. Si vous aviez un site web et une API web principale, vous avez pu les inscrire chacun en tant qu’application distincte dans Azure AD. Si vous aviez une application iOS et une application Android, vous pouvez également avoir inscrit deux applications différentes.

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Désormais, une seule et même inscription d’application et un seul ID d’application vous suffisent pour chacun de vos projets. Vous pouvez ajouter plusieurs « plateformes » pour chaque projet, et fournir les données appropriées pour chaque plateforme ajoutée. Bien entendu, vous pouvez créer autant d’applications que vous le souhaitez selon vos besoins, mais pour la plupart des cas, un seul ID d’application est nécessaire.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Notre objectif est de simplifier encore davantage la gestion des applications et l’expérience de développement, et de créer une vue plus consolidée d’un seul et même projet sur lequel vous travaillez peut-être.


## Des étendues, pas des ressources
Dans le service Azure AD d’origine, une application peut se comporter comme une **ressource**, ou un destinataire de jetons. Une ressource peut définir plusieurs **étendues** ou **oAuth2Permissions** qu’elle comprend, permettant ainsi aux applications clientes de demander des jetons pour cette ressource pour un ensemble d’étendues donné. Prenez comme exemple de ressource l’API Graph d’Azure AD :

- Identificateur de ressource, ou `AppID URI` : `https://graph.windows.net/`
- Étendues, ou `OAuth2Permissions` : `Directory.Read`, `Directory.Write`, etc.

Tout ceci vaut pour le point de terminaison v2.0. Une application peut toujours se comporter comme une ressource, définir des étendues et être identifiée par un URI. Les applications clientes peuvent toujours demander l’accès à ces étendues. Toutefois, la façon dont un client demande ces autorisations a changé. Dans le passé, une demande d’autorisation OAuth 2.0 à Azure AD pouvait ressembler à ceci :

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

où le paramètre de **ressource** indiquait la ressource pour laquelle l’application cliente demandait une autorisation. Azure AD calculait les autorisations requises par l’application en fonction de la configuration statique définie dans le portail Azure, puis émettait les jetons en conséquence. Désormais, la même demande d’autorisation OAuth 2.0 ressemble à ceci :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

où le paramètre d’**étendue** indique la ressource et les autorisations pour lesquelles l’application demande une autorisation. La ressource souhaitée est toujours très présente dans la demande ; elle est simplement incluse dans chacune des valeurs du paramètre d’étendue. L’utilisation du paramètre d’étendue de cette manière permet au point de terminaison v2.0 d’être plus conforme à la spécification OAuth 2.0, et elle est en harmonie plus étroite avec les pratiques courantes du secteur. Elle permet également aux applications d’effectuer un [consentement incrémentiel](#incremental-and-dynamic-consent), qui est décrit dans la section suivante.

## Consentement incrémentiel et dynamique
Les applications inscrites dans le service Azure AD mis à la disposition générale devaient spécifier leurs autorisations OAuth 2.0 requises dans le portail Azure, au moment de leur création :

![Interface utilisateur de l’inscription des autorisations](../media/active-directory-v2-flows/app_reg_permissions.PNG)

Les autorisations requises par une application étaient configurées **statiquement**. Si cela permettait à la configuration de l’application d’exister dans le portail Azure et de maintenir la simplicité du code, cela présentait quelques problèmes pour les développeurs :

- L’application devait connaître toutes les autorisations dont elle aurait besoin lors de sa création. L’ajout d’autorisations au fil du temps était un processus difficile.
- Une application devait connaître au préalable l’ensemble des ressources auxquelles elle aurait accès. Il était difficile de créer des applications pouvant accéder à un nombre arbitraire de ressources.
- Une application devait demander toutes les autorisations dont elle aurait besoin dès la première connexion de l’utilisateur. Dans certains cas, cela engendrait une très longue liste d’autorisations, qui dissuadait les utilisateurs finaux d’approuver l’accès de l’application à la connexion initiale.

Avec le point de terminaison v2.0, vous pouvez spécifier les autorisations dont votre application a besoin **dynamiquement**, lors de l’exécution, pendant l’utilisation régulière de votre application. Pour ce faire, vous pouvez spécifier les étendues nécessaires à votre application, à un moment donné, en les incluant dans le paramètre `scope` d’une demande d’autorisation :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Le paramètre ci-dessus demande l’autorisation pour l’application de lire les données d’annuaire d’un utilisateur d’Azure AD et d’écrire des données dans son annuaire. Si l’utilisateur a accepté ces autorisations dans le passé pour cette application particulière, il doit simplement entrer ses informations d’identification et se connecter à l’application. Si l’utilisateur n’a accepté aucune de ces autorisations, le point de terminaison v2.0 lui demande de corriger ce manquement. Pour en savoir plus, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md).

Autoriser une application à demander des autorisations dynamiquement via le paramètre `scope` vous permet de contrôler totalement l’expérience de vos utilisateurs. Si vous le souhaitez, vous pouvez choisir d’anticiper votre expérience de consentement et demander toutes les autorisations dans une demande d’autorisation initiale. Si votre application nécessite un grand nombre d’autorisations, vous pouvez choisir de rassembler celles de l’utilisateur de façon incrémentielle lorsqu’il essaie d’utiliser certaines fonctionnalités de votre application au fil du temps.

## Étendues connues

#### Accès hors connexion
Le point de terminaison v2.0 peut nécessiter l’utilisation d’une nouvelle autorisation bien connue pour les applications : l’étendue `offline_access`. Toutes les applications doivent demander cette autorisation si elles doivent accéder aux ressources au nom d’un utilisateur pendant une période prolongée, même si l’utilisateur n’utilise peut-être pas activement l’application donnée. L’étendue `offline_access` apparaît à l’utilisateur dans la boîte de dialogue de consentement « Accéder aux données hors connexion », que l’utilisateur doit accepter. Demander l’autorisation `offline_access` permet à votre application web de recevoir les jetons d’actualisation OAuth 2.0 du point de terminaison v2.0. Les jetons d’actualisation sont de longue durée, et peuvent être échangés contre les nouveaux jetons d’accès OAuth 2.0 pour des périodes d’accès prolongées.

Si votre application ne sollicite pas l’étendue `offline_access`, elle ne reçoit pas les jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période (généralement une heure), avant d’arriver à expiration. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant ce réacheminement, il peut être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou d’accepter une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur OAuth 2.0, les jetons d’actualisation et les jetons d’accès, consultez [Informations de référence sur les protocoles du point de terminaison v2.0](active-directory-v2-protocols.md).

#### OpenID, profile et email

Dans le service Azure Active Directory d’origine, le flux de connexion OpenID Connect le plus simple fournit une somme d’informations sur l’utilisateur dans le paramètre id\_token résultant. Les revendications contenues dans un paramètre id\_token peuvent inclure différentes informations utilisateur : son nom, son nom d’utilisateur par défaut, son adresse de messagerie, son ID objet, etc.

À présent, nous allons limiter les informations auxquelles l’étendue `openid` permet à votre application d’accéder. L’étendue « openid » permettra uniquement à votre application de connecter l’utilisateur et de recevoir un identificateur propre à l’application pour l’utilisateur. Si vous souhaitez obtenir des informations d’identification personnelle (PII) sur l’utilisateur dans votre application, votre application devra lui demander des autorisations supplémentaires. Nous introduisons deux nouvelles étendues, les étendues `email` et `profile`, qui vous permettent d’effectuer cette opération.

L’étendue `email` est très directe : elle permet à votre application d’accéder à l’adresse de messagerie principale de l’utilisateur via la revendication `email` contenue dans le paramètre id\_token. L’étendue `profile` permet à votre application d’accéder à toutes les autres informations de base sur l’utilisateur (nom, nom d’utilisateur par défaut, ID objet, etc.).

Vous pouvez ainsi coder votre application en en divulguant le moins possible : vous pouvez vous contenter de demander à l’utilisateur les informations nécessaires à l’exécution de votre application. Pour plus d’informations sur ces étendues, consultez [les informations de référence sur les étendues v2.0](active-directory-v2-scopes.md).

## Demandes de jetons

Les demandes des jetons émis par le point de terminaison v2.0 ne sont pas identiques à celles des jetons émis par les points de terminaison du service Azure AD mis à la disposition générale. Les applications migrant vers le nouveau service ne doivent pas supposer qu’une demande particulière existe dans les jetons d’ID ou les jetons d’accès. Les jetons émis par le point de terminaison v2.0 sont conformes aux spécifications OAuth 2.0 et OpenID Connect, mais peuvent suivre une sémantique différente de celle du service Azure AD mis à la disposition générale.

Pour en savoir plus sur les demandes spécifiques émises dans les jetons v2.0, consultez [Informations de référence sur les jetons v2.0](active-directory-v2-tokens.md).

## Limites
Il existe quelques restrictions à connaître lors de l’utilisation du point v2.0. Consultez le [document relatif aux limites v2.0](active-directory-v2-limitations.md) pour voir si ces restrictions s’appliquent à votre scénario particulier.

<!---HONumber=AcomDC_0921_2016-->