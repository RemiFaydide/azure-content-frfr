<properties
	pageTitle="Application Web .NET v2.0 Azure AD | Microsoft Azure"
	description="Comment créer une application Web .NET MVC qui appelle des services Web à l’aide de comptes personnels Microsoft et de comptes professionnels ou scolaires pour la connexion."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Appeler une API web à partir d’une application web .NET

Avec le point de terminaison v2.0, vous pouvez rapidement ajouter une authentification à vos applications Web et à vos API Web, avec prise en charge pour les comptes Microsoft personnels, ainsi que pour les comptes professionnels ou scolaires. Ici, nous allons créer une application web MVC qui connecte les utilisateurs à l’aide d’OpenID Connect, avec l’aide de l’intergiciel OWIN de Microsoft. L’application web obtiendra des jetons d’accès OAuth 2.0 pour une API web sécurisée par OAuth 2.0 qui permet d’exécuter des opérations de création, lecture et suppression dans la « To Do List » (Liste des tâches) d’un utilisateur.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

Ce didacticiel s’intéresse principalement à l’utilisation d’ADAL pour récupérer et utiliser des jetons d’accès dans une application Web, dont la description complète est disponible [ici](active-directory-v2-flows.md#web-apps). Afin de vous renseigner sur la configuration requise, apprenez dans un premier temps comment [ajouter une connexion de base à une application Web](active-directory-v2-devquickstarts-dotnet-web.md) ou comment [sécuriser de manière appropriée une application Web](active-directory-v2-devquickstarts-dotnet-api.md).

## Télécharger l’exemple de code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Vous pouvez également [télécharger l'application terminée dans un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou cloner l'application terminée :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- créer un **secret d’application** du type **Mot de passe**, puis copiez sa valeur pour une utilisation ultérieure ;
- Ajoutez la plate-forme **Web** pour votre application.
- entrer l’**URI de redirection** approprié. L’URI de redirection indique à Azure AD où les réponses d’authentification doivent être dirigées. La valeur par défaut pour ce didacticiel est `https://localhost:44326/`.


## Installer OWIN
Ajoutez au projet `TodoList-WebApp` les packages NuGet de l’intergiciel OWIN à l’aide de la console du gestionnaire de package. L’intergiciel OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur, entre autres.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## Connexion de l’utilisateur
À présent, configurez l’intergiciel OWIN pour utiliser le [protocole d’authentification OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow).

-	Ouvrez le fichier `web.config` à la racine du projet `TodoList-WebApp`, puis entrez les valeurs de configuration de votre application dans la section `<appSettings>`.
    -	L’élément `ida:ClientId` est l’**ID d’application** affecté à votre application dans le portail d’inscription.
	- L’élément `ida:ClientSecret` est le **secret d’application** que vous avez créé dans le portail d’inscription.
    -	L’élément `ida:RedirectUri` est l’**URI de redirection** que vous avez saisi dans le portail.
- Ouvrez le fichier `web.config` dans la racine du projet `TodoList-Service`, puis remplacez l’élément `ida:Audience` par l’**ID d’application** ci-dessus.


- Ouvrez le fichier `App_Start\Startup.Auth.cs`, puis ajoutez les instructions `using` pour les bibliothèques ci-dessus.
- Dans le même fichier, implémentez la méthode `ConfigureAuth(...)`. Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

					// The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
					// The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
					// In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

					ClientId = clientId,
					Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
					Scope = "openid email profile offline_access",
					RedirectUri = redirectUri,
					PostLogoutRedirectUri = redirectUri,
					TokenValidationParameters = new TokenValidationParameters
					{
						ValidateIssuer = false,
					},

					// The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

					Notifications = new OpenIdConnectAuthenticationNotifications
					{
						AuthenticationFailed = OnAuthenticationFailed,
						AuthorizationCodeReceived = OnAuthorizationCodeReceived,
					}

    	});
}
...
```

## Utilisation d’ADAL pour obtenir des jetons d’accès
Dans la notification `AuthorizationCodeReceived`, nous souhaitons utiliser [OAuth 2.0 en tandem avec OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) afin d’échanger le code d’autorisation contre un jeton d’accès au service de la liste de tâches. ADAL peut vous faciliter ce processus :

- Tout d’abord, installez la version d’évaluation d’ADAL :

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease```
- Puis, ajoutez une autre instruction `using` au fichier `App_Start\Startup.Auth.cs` pour ADAL.
- Vous pouvez alors ajouter une nouvelle méthode, le gestionnaire d’événements `OnAuthorizationCodeReceived`. Ce gestionnaire utilisera ADAL pour acquérir un jeton d’accès à l’API To-Do List et le stockera dans le cache de jetons ADAL pour une utilisation ultérieure :

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
		string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
		string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
		ClientCredential cred = new ClientCredential(clientId, clientSecret);

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
		var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
}
...
```

- Dans les applications Web, ADAL dispose d’un cache de jeton extensible pouvant être utilisé pour stocker les jetons. Cet exemple implémente l’élément `NaiveSessionCache` qui utilise le stockage de session HTTP pour mettre en cache les jetons.

<!-- TODO: Token Cache article -->


## 4\. Appel de l’API web
Il est à présent temps d’utiliser le jeton d’accès acquis lors de l’étape 3. Ouvrez le fichier `Controllers\TodoListController.cs` de l’application web, qui exécute l’ensemble des requêtes CRUD sur l’API To-Do List.

- Vous pouvez réutiliser ADAL afin de récupérer les jetons d’accès du cache ADAL. Tout d’abord, ajoutez une instruction `using` pour ADAL dans ce fichier.

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- Dans l’action `Index`, utilisez la méthode `AcquireTokenSilentAsync` d’ADAL pour obtenir un jeton d’accès pouvant être utilisé pour lire les données du service de la liste des tâches :

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- L’exemple ajoute ensuite le jeton obtenu à la requête GET HTTP en tant qu’en-tête `Authorization`, que le service de la liste des tâches utilise pour authentifier la requête.
- Si le service To-Do List renvoie une réponse `401 Unauthorized`, les jetons d’accès d’ADAL deviennent non valides, pour une raison indéterminée. Dans ce cas, vous devez abandonner les jetons d’accès du cache ADAL et transmettre à l’utilisateur un message lui demandant de se reconnecter. Le cas échéant, le flux d’acquisition des jetons est redémarré.

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
		var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
		foreach (TokenCacheItem tci in todoTokens)
				authContext.TokenCache.DeleteItem(tci);

		return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- De la même manière, si ADAL est dans l’impossibilité de renvoyer un jeton d’accès, pour quelque raison que ce soit, vous devez demander à l’utilisateur de se reconnecter. Cette opération n’est pas plus compliquée que la récupération d’un élément `AdalException` :

```C#
...
catch (AdalException ee)
{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- Le même appel `AcquireTokenSilentAsync` est implémenté dans les actions `Create` et `Delete`. Dans les applications Web, vous pouvez utiliser cette méthode ADAL pour obtenir les jetons d’accès lorsque vous en avez besoin dans votre application. ADAL se charge de l’acquisition, de la mise en cache et de l’actualisation des jetons.

Enfin, générez et exécutez votre application. Connectez-vous avec un compte Microsoft ou un compte Azure AD, et prenez note du mode d’indication de l’identité de l’utilisateur dans la barre de navigation supérieure. Ajoutez et supprimez certains éléments de la liste de tâches de l’utilisateur afin d’observer les appels d’API sécurisés OAuth 2.0 en action. Vous disposez désormais d’une application Web et d’une API Web, toutes deux sécurisées à l’aide de protocoles standard et pouvant authentifier les utilisateurs avec leurs comptes personnels et professionnels/scolaires.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).

## Étapes suivantes

Pour obtenir des ressources supplémentaires, consultez :
- [Guide du développeur 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Balise « adal » StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

## Obtenir les mises à jour de sécurité de nos produits

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0921_2016-->