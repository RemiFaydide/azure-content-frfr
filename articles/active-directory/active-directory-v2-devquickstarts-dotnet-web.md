<properties
	pageTitle="Application Web .NET v2.0 Azure AD | Microsoft Azure"
	description="Génération d’une application web .NET MVC qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leur compte professionnel ou scolaire."
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

# Ajouter une connexion à une application Web MVC .NET

Avec le point de terminaison v2.0, vous pouvez rapidement ajouter une authentification à vos applications web, avec prise en charge des comptes Microsoft personnels et des comptes professionnels ou scolaires. Dans les applications web ASP.NET, vous pouvez y parvenir en utilisant l’intergiciel OWIN de Microsoft inclus dans .NET Framework 4.5.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

 Nous allons créer une application Web qui utilise OWIN pour connecter l’utilisateur, afficher des informations sur l’utilisateur et déconnecter l’utilisateur de l’application.
 
 ## Téléchargement Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

L'application terminée est également fournie à la fin de ce didacticiel.

## Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- ajouter la plateforme **Web** pour votre application ;
- entrer l’**URI de redirection** approprié. L’URI de redirection indique à Azure AD où les réponses d’authentification doivent être dirigées. La valeur par défaut pour ce didacticiel est `https://localhost:44326/`.

## Installer et configurer l’authentification OWIN
Ici, nous allons configurer l’intergiciel OWIN pour utiliser le protocole d’authentification OpenID Connect. OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur, entre autres.

-	Pour commencer, ouvrez le fichier `web.config` dans la racine du projet, puis entrez les valeurs de configuration de votre application dans la section `<appSettings>`.
    -	L’élément `ida:ClientId` est l’**ID d’application** affecté à votre application dans le portail d’inscription.
    -	L’élément `ida:RedirectUri` est l’**URI de redirection** que vous avez saisi dans le portail.

-	Ajoutez ensuite les packages NuGet d'intergiciel (middleware) OWIN au projet à l'aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Ajoutez une « classe de démarrage OWIN » au projet nommé `Startup.cs`. Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Rechercher « OWIN ». L’intergiciel OWIN appelle la méthode `Configuration(...)` lorsque votre application démarre.
-	Modifiez la déclaration de classe en `public partial class Startup`. Nous avons déjà mis en œuvre une partie de cette classe pour vous, dans un autre fichier. Dans la méthode `Configuration(...)`, appelez ConfigureAuth(...) pour configurer l’authentification de votre application web.

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
	public partial class Startup
	{
		public void Configuration(IAppBuilder app)
		{
			ConfigureAuth(app);
		}
	}
}
```

-	Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`. Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD. Vous devrez également configurer l’authentification des cookies ; l’intergiciel OpenID Connect utilise des cookies en coulisses.

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
									 RedirectUri = redirectUri,
									 Scope = "openid email profile",
									 ResponseType = "id_token",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## Envoi de demandes d’authentification
Votre application est maintenant correctement configurée pour communiquer avec le point de terminaison v2.0 en utilisant le protocole d’authentification OpenID Connect. OWIN a pris en charge tous les détails déplaisants de la création de messages d’authentification, de la validation des jetons d’Azure AD et de la gestion des sessions utilisateur. Il ne reste qu’à offrir aux utilisateurs un moyen de se connecter et de se déconnecter.

- Vous pouvez utiliser des balises autorisées dans vos contrôleurs pour exiger que l’utilisateur se connecte avant d’accéder à une page donnée. Ouvrez `Controllers\HomeController.cs` et ajoutez la balise `[Authorize]` au contrôleur About.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	Vous pouvez également utiliser OWIN pour émettre des demandes d’authentification provenant directement de votre code. Ouvrez `Controllers\AccountController.cs`. Dans les actions SignIn() et SignOut(), émettez les demandes de test OpenID Connect et de déconnexion, respectivement.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`. Voici où vous allez afficher les liens de connexion et de déconnexion de votre application pour l’utilisateur et où vous allez afficher le nom de l’utilisateur.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## Afficher les informations utilisateur
Lors de l’authentification des utilisateurs avec OpenID Connect, le point de terminaison v2.0 renvoie un jeton d’ID à l’application qui contient des [revendications](active-directory-v2-tokens.md#id_tokens) ou des assertions concernant l’utilisateur. Vous pouvez utiliser ces revendications pour personnaliser votre application :

- Ouvrez le fichier `Controllers\HomeController.cs`. Vous pouvez accéder aux revendications de l’utilisateur dans vos contrôleurs via l’objet principal de sécurité `ClaimsPrincipal.Current`.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## Exécuter

Enfin, générez et exécutez votre application. Connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire, et notez comment l’identité de l’utilisateur est indiquée dans la barre de navigation supérieure. Vous disposez désormais d’une application web sécurisée à l’aide de protocoles standard et pouvant authentifier les utilisateurs avec leurs comptes personnels et professionnels/scolaires.

Pour référence, l'exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## Étapes suivantes

Vous pouvez maintenant aborder des rubriques plus sophistiquées. Par exemple :

[Sécuriser une API Web avec le point de terminaison v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez :
- [Guide du développeur 2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active-directory" tag >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## Obtenir les mises à jour de sécurité de nos produits

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0921_2016-->