<properties
	pageTitle="Prise en main d’Azure AD .NET | Microsoft Azure"
	description="Création d’une application web .NET MVC qui s’intègre avec Azure AD pour la connexion"
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

# Connexion et déconnexion de l’application web avec Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD simplifie l’externalisation de la gestion des identités de votre application web en fournissant une authentification unique avec seulement quelques lignes de code. Dans les applications web Asp.NET, vous pouvez y parvenir en utilisant l’implémentation Microsoft de l’intergiciel communautaire OWIN inclus dans .NET Framework 4.5. Ici, nous allons utiliser OWIN pour :
-	connecter l’utilisateur à l’application à l’aide d’Azure AD comme fournisseur d’identité ;
-	afficher des informations sur l’utilisateur ;
-	déconnecter l’utilisateur de l’application.

Pour ce faire, vous devez :

1. inscrire une application auprès d’Azure AD ;
2. configurer votre application pour utiliser le pipeline d’authentification OWIN ;
3. utiliser OWIN pour émettre des demandes de connexion et de déconnexion auprès d’Azure AD ;
4. afficher les données relatives à l’utilisateur.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip). Vous aurez également besoin d’un client Azure AD dans lequel inscrire votre application. Si ce n’est pas déjà fait, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## *1. Inscription d’une application auprès d’Azure AD*
Pour autoriser l’authentification des utilisateurs par votre application, vous devez tout d’abord inscrire une nouvelle application dans votre client.

- Connectez-vous au portail de gestion Azure.
- Cliquez sur **Active Directory** dans la partie de gauche.
- Sélectionnez le client dans lequel vous souhaitez inscrire l’application.
- Cliquez sur l’onglet **Applications**, puis sur Ajouter dans le menu déroulant inférieur.
- Suivez les invites et créez une **Application Web et/ou API Web**.
    - Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	L’**URL de connexion** est l’URL de base de votre application. La valeur par défaut de la structure est `https://localhost:44320/`.
    - Un **URI ID d’application** est un identificateur unique pour votre application. L’usage est d’utiliser `https://<tenant-domain>/<app-name>`, par exemple `https://contoso.onmicrosoft.com/my-first-aad-app`.
- Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet Configurer, car vous en aurez besoin dans les sections suivantes.

## *2. Configuration de votre application pour utiliser le pipeline d’authentification OWIN*
Ici, nous allons configurer l’intergiciel OWIN pour utiliser le protocole d’authentification OpenID Connect. OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur, entre autres.

-	Pour commencer, ajoutez au projet les packages NuGet de l’intergiciel OWIN à l’aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Ajoutez une classe de démarrage OWIN au projet nommé `Startup.cs`. Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Recherchez « OWIN ». L’intergiciel OWIN appelle la méthode `Configuration(...)` lorsque votre application démarre.
-	Modifiez la déclaration de classe en `public partial class Startup`. Nous avons déjà mis en œuvre une partie de cette classe pour vous, dans un autre fichier. Dans la méthode `Configuration(...)`, appelez ConfgureAuth(...) pour configurer l’authentification à votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`. Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD. Vous devrez également configurer l’authentification des cookies ; l’intergiciel OpenID Connect utilise des cookies en coulisses.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	Enfin, ouvrez le fichier `web.config` dans la racine du projet, puis entrez les valeurs de configuration dans la section `<appSettings>`.
    -	Le `ida:ClientId` de votre application est le GUID que vous avez copié à partir du portail Azure à l’étape 1.
    -	`ida:Tenant` est le nom de votre client Azure AD, par exemple, « contoso.onmicrosoft.com ».
    -	Votre `ida:PostLogoutRedirectUri` indique à Azure AD l’endroit vers lequel un utilisateur doit être redirigé lorsqu’une demande de déconnexion est réussie.

## *3. Utilisation d’OWIN pour émettre des demandes de connexion et de déconnexion dans Azure AD*
Votre application est maintenant correctement configurée pour communiquer avec Azure AD en utilisant le protocole d’authentification OpenID Connect. OWIN a pris en charge tous les détails déplaisants de la création de messages d’authentification, de la validation des jetons d’Azure AD et de la gestion des sessions utilisateur. Il ne reste qu’à offrir aux utilisateurs un moyen de se connecter et de se déconnecter.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`. Voici où vous allez afficher les liens de connexion et de déconnexion de votre application pour l’utilisateur et où vous allez afficher le nom de l’utilisateur.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## *4. Affichage des informations utilisateur*
Lors de l’authentification des utilisateurs avec OpenID Connect, Azure AD renvoie un id\_token à l’application qui contient des « revendications » ou des assertions concernant l’utilisateur. Vous pouvez utiliser ces revendications pour personnaliser votre application :

- Ouvrez le fichier `Controllers\HomeController.cs`. Vous pouvez accéder aux revendications de l’utilisateur dans vos contrôleurs via l’objet principal de sécurité `ClaimsPrincipal.Current`.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Enfin, générez et exécutez votre application. Si vous ne l’avez pas encore fait, il est temps de créer un nouvel utilisateur dans votre client, avec un domaine *.onmicrosoft.com. Connectez-vous avec les informations d’identification de cet utilisateur et observez comment l’identité de l’utilisateur se reflète dans la barre de navigation supérieure. Déconnectez-vous et reconnectez-vous sous le nom d’un autre utilisateur de votre client. Si vous vous sentez particulièrement ambitieux, enregistrez et exécutez une autre instance de cette application (avec son propre ID de client) et observez le fonctionnement de l’authentification unique.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).

Vous pouvez maintenant aborder des rubriques plus sophistiquées. Par exemple :

[Sécurisation d’une API web avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->