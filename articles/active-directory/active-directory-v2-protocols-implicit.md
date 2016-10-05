<properties
	pageTitle="Flux implicite d’Azure AD v2.0 | Microsoft Azure"
	description="Création d’applications web à l’aide de l’implémentation v2.0 d’Azure AD du flux implicite pour les applications à page unique."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Protocoles v2.0 - Applications à page unique utilisant le flux implicite
Le point de terminaison v2.0 vous permet de connecter des utilisateurs dans vos applications à page unique avec des comptes personnels et professionnels/scolaires de Microsoft. Les applications à page unique et autres applications JavaScript qui s’exécutent principalement dans un navigateur présentent des problématiques spécifiques liées à l’authentification :

- Les caractéristiques de sécurité de ces applications sont considérablement différentes de celles des applications web traditionnelles basées sur serveur.
- De nombreux serveurs d’autorisation et fournisseurs d’identité ne prennent pas en charge les demandes CORS.
- Chacune des redirections à partir de l’application du navigateur plein écran perturbe de manière assez importante l’expérience utilisateur.

Pour ces applications (AngularJS, Ember.js, React.js, etc.), Azure AD prend en charge le flux d’octroi implicite OAuth 2.0. Le flux implicite est décrit dans la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Il permet notamment à l’application d’obtenir des jetons d’Azure AD sans échanger les informations d’identification du serveur principal. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client. Quelques points de sécurité importants sont à prendre en compte lorsque vous utilisez le flux implicite, particulièrement en ce qui concerne l’emprunt d’identité du [client](http://tools.ietf.org/html/rfc6749#section-10.3) et de l’[utilisateur](http://tools.ietf.org/html/rfc6749#section-10.3).

Si vous souhaitez recourir au flux implicite et à Azure AD pour ajouter de l’authentification à votre application JavaScript, nous vous recommandons d’utiliser notre bibliothèque JavaScript open source, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Pour vous aider à commencer, nous mettons [ici](active-directory-appmodel-v2-overview.md#getting-started) à votre disposition quelques didacticiels AngularJS.

Toutefois, si vous préférez ne pas utiliser de bibliothèque dans votre application à page unique et envoyer vous-même des messages de protocole, suivez la procédure générale ci-dessous.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
    
## Schéma de protocole
Le flux de connexion entièrement implicite ressemble à ceci : chacune des étapes est décrite en détail ci-dessous.

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## Envoyer la requête de connexion

Pour connecter initialement l’utilisateur à votre application, vous pouvez envoyer une demande d’autorisation [OpenID Connect](active-directory-v2-protocols-oidc.md) et obtenir un élément `id_token` à partir du point de terminaison v2.0 :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Cliquez sur le lien ci-dessous pour exécuter cette requête ! Une fois que vous êtes connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec un élément `id_token` dans la barre d'adresse. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| locataire | required | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | required | Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure l’élément response\_type `token`. L’utilisation de l’élément `token` permet ici à votre application de recevoir immédiatement un jeton d’accès à partir du point de terminaison d’autorisation sans avoir à exécuter une deuxième requête sur le point de terminaison d’autorisation. Si vous utilisez l’élément response\_type `token`, le paramètre `scope` doit contenir une étendue indiquant la ressource pour laquelle le jeton doit être émis. |
| redirect\_uri | recommandé | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement. Vous pouvez également, si vous le souhaitez, inclure les [étendues](active-directory-v2-scopes.md) `email` ou `profile` permettant d’accéder à des données utilisateur supplémentaires. Vous pouvez aussi inclure d’autres étendues dans cette requête pour solliciter le consentement sur diverses ressources. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application. Doit être `fragment` pour le flux implicite. |
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](http://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. Les seules valeurs valides à ce stade sont login, none et consent. `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur. Si la requête ne peut pas être exécutée en mode silencieux au moyen d'une authentification unique, le point de terminaison v2.0 renvoie une erreur. `prompt=consent` déclenche l'affichage de la boîte de dialogue de consentement OAuth après la connexion de l'utilisateur, afin de lui demander d'octroyer des autorisations à l'application. |
| login\_hint | facultatif | Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`. |
| domain\_hint | facultatif | Peut être `consumers` ou `organizations`. S’il est inclus, ce paramètre ignore le processus de découverte par courrier électronique auquel l’utilisateur doit se soumettre sur la page de connexion v2.0, ce qui améliore légèrement l’expérience utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` du jeton id\_token. Si la revendication `tid` est définie sur la valeur `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`. Sinon, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` et `response_type=id_token+token` ressemble à ceci (des sauts de ligne sont ici insérés pour une meilleure lisibilité) :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Inclus si `response_type` inclut `token`. Jeton d’accès demandé par l’application, dans ce cas Microsoft Graph. Le jeton d’accès ne doit pas être décodé ou inspecté ; il peut être traité comme une chaîne opaque. |
| token\_type | Inclus si `response_type` inclut `token`. Sera toujours `Bearer`. |
| expires\_in | Inclus si `response_type` inclut `token`. Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| scope | Inclus si `response_type` inclut `token`. Indique la ou les étendues pour lesquelles le jeton access\_token sera valide. |
| id\_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id\_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour obtenir plus de détails sur les jetons id-token et leur contenu, consultez la page de [référence des jetons de point de terminaison v2.0](active-directory-v2-tokens.md). |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |


#### Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## Valider le jeton id\_token
La réception du jeton id\_token ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature du jeton id\_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application. Le point de terminaison v2.0 utilise les [jetons web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Une fois que vous avez validé la signature du jeton id\_token, il vous faudra vérifier quelques revendications. Pour plus d’informations, consultez la page [Informations de référence sur les jetons v2.0](active-directory-v2-tokens.md), et notamment les sections [Validation des jetons](active-directory-v2-tokens.md#validating-tokens) et [Informations importantes sur la substitution des clés de signature](active-directory-v2-tokens.md#validating-tokens). Nous vous recommandons d’utiliser une bibliothèque pour analyser et valider les jetons. Il en existe au moins une pour la plupart des langages et plateformes.
<!--TODO: Improve the information on this-->

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
- S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Pour plus d’informations sur les revendications dans un jeton id\_token, consultez la page de [référence sur les jetons du point de terminaison v2.0](active-directory-v2-tokens.md).

Une fois que vous avez complètement validé le jeton id\_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id\_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc.

## Obtenir des jetons d’accès

Maintenant que vous avez connecté l’utilisateur dans votre application à page unique, vous pouvez obtenir des jetons d’accès afin d’appeler des API web sécurisées à l’aide d’Azure AD, comme [Microsoft Graph](https://graph.microsoft.io). Même si vous avez déjà reçu un jeton utilisant l’élément response\_type `token`, vous pouvez utiliser cette méthode pour acquérir des jetons vers des ressources supplémentaires sans avoir à demander à l’utilisateur de se reconnecter.

Dans le flux normal OpenID Connect/OAuth, il vous faut transmettre une requête au point de terminaison `/token` v2.0. Toutefois, le point de terminaison v2.0 ne prend pas en charge les demandes CORS. Dès lors, il est hors de question d’effectuer des appels AJAX afin d’obtenir et d’actualiser des jetons. Au lieu de cela, vous pouvez utiliser le flux implicite d’un iFrame masqué afin d’obtenir de nouveaux jetons pour d’autres API web :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Essayez de copier et coller la requête ci-dessous dans un onglet de navigateur ! (N'oubliez pas de remplacer les valeurs `domain_hint` et `login_hint` par les valeurs correctes pour votre utilisateur)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| locataire | required | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | required | Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut inclure d’autres types de réponses, comme `code`. |
| redirect\_uri | recommandé | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour obtenir des jetons, incluez toutes les [étendues](active-directory-v2-scopes.md) dont vous avez besoin pour la ressource concernée. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application. Peut être `query`, `form_post` ou `fragment`. |
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| prompt | required | Pour actualiser et obtenir des jetons dans un iframe masqué, vous devez utiliser `prompt=none` pour vous assurer que l’iframe ne se bloque pas sur la page de connexion v2.0 et renvoie immédiatement des résultats. |
| login\_hint | required | Pour actualiser et obtenir des jetons dans un iframe masqué, vous devez inclure dans cet indicateur le nom d’utilisateur de l’utilisateur afin de faire la distinction entre plusieurs sessions que l’utilisateur pourrait ouvrir à un moment précis. Vous pouvez extraire le nom d’utilisateur à partir d’une précédente connexion à l’aide de la revendication `preferred_username`. |
| domain\_hint | required | Peut être `consumers` ou `organizations`. Pour actualiser et obtenir des jetons dans un iframe masqué, vous devez inclure le paramètre domain\_hint dans la demande. Vous devez extraire la revendication `tid` du jeton id\_token d’une précédente connexion pour déterminer la valeur à utiliser. Si la revendication `tid` est définie sur la valeur `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`. Sinon, utilisez `domain_hint=organizations`. |

Grâce au paramètre `prompt=none`, cette requête va immédiatement réussir ou échouer, avant de revenir vers votre application. Une réponse correcte sera envoyée à votre application, à l’`redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### Réponse correcte
Une réponse correcte utilisant `response_mode=fragment` se présente ainsi :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton que l’application a demandé. |
| token\_type | Sera toujours `Bearer`. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| scope | Les étendues de validité du jeton d’accès. |

#### Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée. Dans le cas de `prompt=none`, une erreur se présentera généralement ainsi :

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Si vous recevez cette erreur dans la requête iFrame, l’utilisateur doit se connecter de nouveau de manière interactive, ceci pour récupérer un nouveau jeton. Vous êtes invité à gérer ce cas de la manière la plus appropriée pour votre application.

## Actualisation des jetons

Les `id_token` et les `access_token` expirant après une courte période, votre application doit être préparée à les actualiser de manière régulière. Pour actualiser chaque type de jeton, vous pouvez exécuter la requête iFrame ci-dessus à l’aide du paramètre `prompt=none` afin de contrôler le comportement d’Azure AD. Si vous souhaitez recevoir un nouvel élément `id_token`, veillez à utiliser `response_type=id_token` et `scope=openid`, ainsi qu’un paramètre `nonce`.


## Envoi d’une demande de déconnexion

Le `end_session_endpoint` OpenIdConnect n’est pas actuellement pris en charge par le point de terminaison v2.0. Cela signifie que votre application ne peut pas envoyer une requête au point de terminaison v2.0 afin d’arrêter une session utilisateur et d’effacer les cookies définis par le point de terminaison v2.0. Pour déconnecter un utilisateur, il suffit à votre application de terminer sa propre session avec l’utilisateur, sans affecter aucunement la session de l’utilisateur avec le point de terminaison v2.0. Lors de la tentative de connexion suivante de l’utilisateur, une page l’invitant à sélectionner un compte s’affiche ; ses comptes de connexion actifs sont répertoriés. Sur cette page, l’utilisateur peut choisir de se déconnecter d’un compte, et de terminer la session avec le point de terminaison v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

<!---HONumber=AcomDC_0921_2016-->