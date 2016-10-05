<properties
	pageTitle="Azure AD v2.0 - Étendues, autorisations et consentement | Microsoft Azure"
	description="Une description de l’autorisation dans le point de terminaison v2.0 Azure AD, relative notamment aux étendues, aux autorisations et aux consentements."
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

# Étendues, autorisations et consentement dans le point de terminaison v2.0

Les applications qui s’intègrent à Azure AD respectent un modèle particulier d’autorisation, qui permet aux utilisateurs de contrôler le mode d’accès d’une application à leurs données. L’implémentation v2.0 de ce modèle d’autorisation a été mise à jour ; le mode d’interaction des applications avec Azure AD est aujourd’hui différent. Cette rubrique aborde les concepts de base de ce modèle d’autorisation, notamment les étendues, les autorisations et les consentements.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## Étendues et autorisations

Azure AD implémente le protocole d’autorisation [OAuth 2.0](active-directory-v2-protocols.md), qui est une méthode permettant d’autoriser à une application tierce d’accéder aux ressources hébergées sur le Web pour le compte d’un utilisateur. L’ensemble des ressources hébergées sur le Web qui s’intègrent à Azure AD présentent un identificateur de ressource, également appelé **URI d’ID d’application**. Voici, par exemple, quelques-unes des ressources hébergées sur le Web de Microsoft :

- L’API de messagerie unifiée Office 365 : `https://outlook.office.com`
- L’API Graph Azure AD : `https://graph.windows.net`
- Microsoft Graph : `https://graph.microsoft.com`

Ceci s’applique également aux ressources tierces intégrées à Azure AD. Ces ressources peuvent également définir un ensemble d’autorisations à utiliser pour diviser la fonctionnalité de cette ressource en fragments plus réduits. Par exemple, Microsoft Graph a défini un nombre limité d’autorisations :

- Lire le calendrier d’un utilisateur
- Écrire dans le calendrier d’un utilisateur
- Envoi de messages en tant qu’utilisateur
- [\+ plus](https://graph.microsoft.io)

En définissant ces autorisations, la ressource dispose d’un contrôle affiné sur ses données et sur leur exposition. Une application tierce peut ensuite demander ces autorisations à l’utilisateur ; ce dernier doit fournir son consentement avant qu’une application puisse agir en son nom. En fragmentant les fonctionnalités de ressources en ensembles plus réduits d’autorisation, il est possible de configurer les applications tierces pour qu’elles demandent uniquement les autorisations nécessaires à l’accomplissement de leur tâche. Par ailleurs, les utilisateurs connaissent avec précision la manière dont les applications vont utiliser leurs données, et s’inquiètent moins de leur éventuelle intention malveillante.

Dans Azure AD et OAuth, ces autorisations sont appelées **étendues**. Vous pouvez également les retrouver sous l’appellation **autorisations oAuth2**. Une étendue est représentée dans Azure AD en tant que valeur de chaîne. Toujours dans l’exemple Microsoft Graph, la valeur d’étendue pour chaque autorisation est la suivante :

- Lire le calendrier d’un utilisateur : `Calendar.Read`
- Écrire dans le calendrier d’un utilisateur : `Mail.ReadWrite`
- Envoi de messages en tant qu’utilisateur : `Mail.Send`

Une application peut demander ces autorisations en spécifiant les étendues dans les requêtes dirigées vers le point de terminaison v2.0, comme décrit ci-dessous.


## Consentement

Dans une demande d’autorisation [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), une application peut demander les autorisations requises à l’aide du paramètre de requête `scope`. Par exemple, lorsqu’un utilisateur se connecte à une application, cette dernière envoie une requête de ce type (avec des sauts de ligne pour une meilleure lisibilité) :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Le paramètre `scope` est une liste d’étendues séparées par des espaces, demandées par l’application. Chaque étendue est indiquée par l’ajout de la valeur correspondante à l’identificateur de la ressource (URI d’ID d’application). La requête ci-dessus indique que l’application nécessite l’autorisation de lecture du calendrier de l’utilisateur et d’envoi de messages au nom de l’utilisateur.

Une fois que l’utilisateur a saisi des informations d’identification, le point de terminaison v2.0 cherche un enregistrement correspondant de **consentement d’utilisateur**. Si l’utilisateur n’a accepté aucune des autorisations sollicitées par le passé, le point de terminaison v2.0 demande à l’utilisateur d’octroyer les autorisations demandées.

![Capture d’écran du consentement dans le compte de travail](../media/active-directory-v2-flows/work_account_consent.png)

Lorsque l’utilisateur approuve l’autorisation, le consentement est enregistré, de manière à ce que l’utilisateur ne doive pas le fournir à nouveau lors des connexions suivantes.

## Utilisation des autorisations

Une fois que l’utilisateur accepte les autorisations pour votre application, cette dernière peut acquérir des jetons d’accès représentant l’autorisation de votre application à accéder, dans une certaine capacité, à une ressource. Un jeton d’accès donné peut être utilisé pour une ressource uniquement, mais l’encodage comporte les informations relatives à l’ensemble des autorisations octroyé pour cette ressource à votre application. Pour acquérir un jeton d’accès, votre application peut transmettre une requête au point de terminaison de jetons v2.0 :

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"redirect_uri": "https://localhost/myapp",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Le jeton d’accès résultant peut ensuite être utilisé dans les requêtes HTTP dirigées vers cette ressource. Il indique de manière fiable à la ressource que votre application dispose de l’autorisation appropriée pour effectuer une tâche spécifique.

Pour en savoir plus sur le protocole OAuth 2.0 et sur le mode d’acquisition des jetons d’accès, consultez la page de [référence sur les protocoles du point de terminaison v2.0](active-directory-v2-protocols.md).

## Étendues OpenId Connect

L’implémentation v2.0 d’OpenID Connect comprend quelques étendues bien définies qui ne s’appliquent pas à une ressource particulière : `openid`, `email`, `profile` et `offline_access`.

#### OpenId

Si une application exécute la connexion à l’aide d’[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), elle doit solliciter l’étendue `openid`. L’étendue `openid` s’affiche sur la page de consentement de l’écran de travail, en tant qu’autorisation de connexion, et sur la page de consentement du compte Microsoft personnel en tant qu’autorisation « Afficher votre profil et se connecter aux applications et aux services à l’aide de votre compte Microsoft ». Cette autorisation permet à une application de recevoir un identifiant utilisateur unique sous la forme de la revendication `sub`. Elle permet également l’application d’accéder au point de terminaison des informations utilisateur. L’étendue `openid` peut également être utilisée sur le point de terminaison de jeton v2.0 afin d’acquérir des jetons id\_token, qui peuvent être sollicités pour sécuriser les appels HTTP entre différents composants d’une application.

#### Email

L’étendue `email` peut être incluse avec l’étendue `openid` ainsi que d’autres. Elle permet à l’application d’accéder à l’adresse de messagerie principale de l’utilisateur sous la forme de la revendication `email`. La revendication `email` ne figurera dans les jetons que si une adresse de messagerie est associée au compte d’utilisateur, ce qui n’est pas toujours le cas. Si vous utilisez l’étendue `email`, votre application doit être préparée à faire face à l’éventualité où la revendication `email` n’existerait pas dans le jeton.

#### Profil

L’étendue `profile` peut être incluse avec l’étendue `openid` ainsi que d’autres. Elle permet à l’application d’accéder à une foule d’informations sur l’utilisateur, notamment le prénom de l’utilisateur, son nom de famille, son nom d’utilisateur privilégié, l’ID d’objet, etc. Pour obtenir une liste complète des revendications de profil disponibles dans les jetons id\_token pour un utilisateur donné, consultez la page [Informations de référence sur les jetons v2.0](active-directory-v2-tokens.md).

#### Offline\_access

L’[étendue `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permet à votre application d’accéder aux ressources pour le compte de l’utilisateur, pendant une période prolongée. Sur l’écran de consentement du compte de travail, cette étendue apparaît en tant qu’autorisation d’« Accéder à vos données à tout moment ». Sur l’écran de consentement du compte Microsoft personnel, elle apparaît en tant qu’autorisation « Accéder à vos informations à tout moment ». Lorsqu’un utilisateur approuve l’étendue `offline_access`, votre application est configurée pour recevoir les jetons d’actualisation du point de terminaison des jetons v2.0. Les jetons d’actualisation, durables, sont mis à profit par votre application pour acquérir de nouveaux jetons d’accès lors de l’expiration des anciens.

Si votre application ne sollicite pas l’étendue `offline_access`, elle ne reçoit pas les jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période (généralement une heure), avant d’arriver à expiration. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant ce réacheminement, il peut être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou d’accepter une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur la récupération et l’utilisation des jetons d’actualisation, consultez la page de [référence sur les protocoles v2.0](active-directory-v2-protocols.md).

<!---HONumber=AcomDC_0921_2016-->