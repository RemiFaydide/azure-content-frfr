<properties
	pageTitle="Protocoles Azure AD v2.0 | Microsoft Azure"
	description="Un guide sur les protocoles pris en charge par le point de terminaison Azure AD v2.0."
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

# Protocoles v2.0 - OAuth 2.0 et OpenID Connect

Le point de terminaison v2.0 peut utiliser Azure AD pour l’identité en tant que service avec les protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit distribué en standard, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. Les informations fournies ici vous seront utiles si vous choisissez d’écrire votre code en envoyant ou en traitant directement des requêtes HTTP ou si vous utilisez une bibliothèque open source tierce, plutôt qu’en utilisant l’une de nos bibliothèques open source.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## Concepts de base
Dans presque tous les flux OAuth et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Le **serveur d’autorisation** est le point de terminaison v2.0. Il est chargé de garantir l’identité de l’utilisateur, l’octroi et la révocation de l’accès aux ressources et l’émission de jetons. Il est également connu sous le nom du fournisseur d’identité. Il traite de manière sécurisée les informations de l’utilisateur, leur accès et les relations de confiance entre les parties des flux.
- Le **propriétaire de la ressource** est généralement l'utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.
- Le **Client OAuth** est votre application, identifiée par son ID d'application. Il s’agit généralement de la partie avec laquelle l’utilisateur final interagit ; elle demande des jetons provenant du serveur d’autorisation. Le client doit se voir octroyer une autorisation d’accès à la ressource par le propriétaire de cette dernière.
- Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser de manière sûre le client OAuth et utilise les jetons d’accès porteurs pour garantir l’octroi de l’accès à une ressource.


## Inscription d’application
Toutes les applications qui utilisent le point de terminaison v2.0 doivent être inscrites à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com) avant de pouvoir interagir à l’aide d’OAuth ou d’OpenID Connect. Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

- un **ID d'application** qui identifie de manière unique votre application ;
- un **URI de redirection** ou un **identificateur de package** pouvant être utilisé pour diriger des réponses vers votre application ;
- quelques valeurs spécifiques au scénario.

Pour plus de détails, découvrez comment [inscrire une application](active-directory-v2-app-registration.md).

## Points de terminaison
Une fois inscrite, l’application communique avec Azure AD en transmettant les requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Où le `{tenant}` peut prendre l’une de quatre valeurs différentes :

| Valeur | Description |
| ----------------------- | ------------------------------- |
| `common` | Permet aux utilisateurs avec des comptes Microsoft personnels et des comptes professionnels/scolaires Azure Active Directory de se connecter à l’application. |
| `organizations` | Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires Azure Active Directory de se connecter à l’application. |
| `consumers` | Permet uniquement aux utilisateurs avec des comptes personnels Microsoft (MSA) de se connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires d’un client Azure Active Directory spécifique de se connecter à l’application. Le nom de domaine convivial du client Azure AD ou l’identificateur guid du client peut être utilisé. |

Pour plus d’informations sur la façon d’interagir avec ces points de terminaison, choisissez un type particulier d’application ci-dessous.

## Jetons
L’implémentation d’OAuth 2.0 et d’OpenID Connect par v2.0 utilise massivement les jetons du porteur, y compris ceux représentés sous forme de JWT. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Pour plus d’informations sur les différents types de jetons utilisés dans le point de terminaison v2.0, consultez la page de [Référence sur les jetons du point de terminaison v2.0](active-directory-v2-tokens.md).

## Protocoles

Si vous êtes prêt à voir des exemples de demandes, entamez l’un des didacticiels ci-dessous. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin d’aide pour déterminer le flux qui vous convient, consultez les [types d’applications que vous pouvez créer avec le point de terminaison v2.0](active-directory-v2-flows.md).

- [Génération d’une application mobile et native avec OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Génération d’applications web avec Open ID Connect](active-directory-v2-protocols-oidc.md)
- [Génération d'applications de page unique avec le flux implicite OAuth 2.0](active-directory-v2-protocols-implicit.md)
- Génération de démons ou de processus côté serveur avec le flux d’informations d’identification de Client OAuth 2.0 (prochainement)
- Obtention de jetons dans une API web avec le flux De la part de d’OAuth 2.0 (prochainement)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 

<!---HONumber=AcomDC_0921_2016-->