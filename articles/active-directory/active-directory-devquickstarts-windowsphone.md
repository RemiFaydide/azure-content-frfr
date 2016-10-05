<properties
	pageTitle="Prise en main d’Azure AD Windows Phone | Microsoft Azure"
	description="Création d’une application Windows Phone qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth"
	services="active-directory"
	documentationCenter="windows"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>



# Intégration d’Azure AD avec une application Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous développez une application Windows Phone 8.1, Azure AD facilite l’authentification de vos utilisateurs avec leurs comptes Active Directory. Il permet également à votre application d’utiliser en toute sécurité une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

> [AZURE.NOTE] Cet exemple de code utilise la bibliothèque ADAL v2.0. Pour utiliser les dernières technologies, vous souhaiterez peut-être plutôt essayer notre [Didacticiel universel Windows avec ADAL v3.0](active-directory-devquickstarts-windowsstore.md). Si vous créez une application pour Windows Phone 8.1, vous êtes au bon endroit. ADAL v2.0 est entièrement pris en charge. C’est la méthode recommandée pour le développement d’applications, par rapport à Windows Phone 8.1 avec Azure AD.

Pour les clients natifs .NET qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d'accès pour votre application. Pour illustrer cette simplicité, nous créerons une application Windows Phone 8.1 « Directory Searcher » effectuant les actions suivantes :

-	obtention de jetons d’accès pour appeler l’API Azure AD Graph à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
-	recherche, dans un répertoire, d’utilisateurs correspondant à un UPN (nom d’utilisateur principal) donné.
-	déconnexion des utilisateurs.

Pour générer l’application fonctionnelle complète, vous devez :

2. inscrire votre application auprès d’Azure AD ;
3. installer et configurer la bibliothèque ADAL ;
5. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Pour commencer, téléchargez [la structure du projet](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Chaque option est une solution Visual Studio 2013. Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## *1. Inscription de l’application Directory Searcher*
Pour autoriser votre application à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD :

-	Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
-	Cliquez sur **Active Directory** dans la partie de gauche.
-	Sélectionnez un client dans lequel inscrire l’application.
-	Cliquez sur l’onglet **Applications**, puis sur **Ajouter** dans le menu déroulant inférieur.
-	Suivez les invites et créez une **application cliente native**.
    -	Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	L’**URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons. Entrez temporairement une valeur d’espace réservé, par exemple, `http://DirectorySearcher`. Nous remplacerons cette valeur ultérieurement.
-	Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet **Configurer**, car vous en aurez besoin dans les sections suivantes.
- Toujours sous l’onglet **Configurer**, cherchez la section Autorisations pour d’autres applications. Pour l’application Azure Active Directory, ajoutez l’autorisation **Accéder au répertoire de l’organisation** sous **Autorisations déléguées**. Cela permet à votre application d’interroger l’API Graph concernant les utilisateurs.

## *2. Installation et configuration de la bibliothèque ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité. Pour permettre à ADAL de communiquer avec Azure AD, vous devez lui fournir des informations sur l’inscription de votre application.
-	Commencez par ajouter ADAL au projet DirectorySearcher à l’aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	Dans le projet DirectorySearcher, ouvrez `MainPage.xaml.cs`. Remplacez les valeurs dans la zone `Config Values` afin de refléter les valeurs que vous avez saisies dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	`tenant` est le domaine de votre client Azure AD, par exemple, contoso.onmicrosoft.com.
    -	`clientId` est l’ID client de votre application que vous avez copié à partir du portail.
-	Vous devez maintenant découvrir l’URI de rappel pour votre application Windows Phone. Définissez un point d’arrêt sur cette ligne dans la méthode `MainPage` :

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Exécutez l’application et copiez la valeur de `redirectUri` à part lorsque le point d’arrêt est atteint. Le résultat suivant doit s’afficher :

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Sous l’onglet **Configurer** de votre application, dans le portail de gestion Azure, remplacez la valeur de l’**URI de redirection** par cette valeur.

## *3. Utilisation de la bibliothèque ADAL pour obtenir des jetons à partir d’AAD*
Le principe de base de la bibliothèque ADAL consiste simplement à appeler `authContext.AcquireToken(…)` chaque fois que votre application a besoin d’un jeton d’accès, et la bibliothèque ADAL s’occupe du reste.

-	La première étape est d’initialiser le `AuthenticationContext` de votre application, la classe principale de la bibliothèque ADAL. C’est à ce moment-là que vous fournissez à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD et lui indiquer comment mettre en cache des jetons.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Recherchez maintenant la méthode `Search(...)` qui est appelée lorsque l’utilisateur clique sur le bouton Rechercher dans l’interface utilisateur de l’application. Cette méthode effectue une demande GET auprès de l’API Graph Azure AD pour l’interroger à propos d’utilisateurs dont l’UPN commence par le terme de recherche donné. Cependant, pour interroger l’API Graph, vous devez inclure un jeton d’accès (access\_token) dans l’en-tête `Authorization` de la demande ; c’est à ce moment qu’intervient la bibliothèque ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Si l’authentification interactive est nécessaire, la bibliothèque ADAL utilise le WAB (Web Authentication Broker) de Windows Phone et le [modèle de continuation](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) pour afficher la page de connexion d’Azure AD. Lorsque l’utilisateur se connecte, votre application doit fournir à la bibliothèque ADAL les résultats de l’interaction du WAB. C’est aussi simple que d’implémenter l’interface `ContinueWebAuthentication` :

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Il est temps d’utiliser le `AuthenticationResult` qu’ADAL renvoie à votre application. Dans le rappel `QueryGraph(...)`, joignez à la demande GET, dans l’en-tête d’autorisation, le jeton d’accès (access\_token) que vous avez acquis :

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- Vous pouvez également utiliser l’objet `AuthenticationResult` pour afficher des informations concernant l’utilisateur dans votre application. Dans la méthode `QueryGraph(...)`, utilisez le résultat pour afficher l’ID de l’utilisateur sur la page :

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Enfin, vous pouvez également utiliser la bibliothèque ADAL pour déconnecter l’utilisateur de l’application. Lorsque l’utilisateur clique sur le bouton Déconnexion, nous voulons vérifier que l’appel suivant vers `AcquireTokenSilentAsync(...)` échoue. Avec la bibliothèque ADAL, c’est aussi simple que d’effacer le cache de jeton :

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Félicitations ! Vous disposez d’une application Windows Phone fonctionnelle capable d’authentifier les utilisateurs, d’appeler en toute sécurité les API web à l’aide d’OAuth 2.0 et d’obtenir des informations de base concernant l’utilisateur. Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs. Exécutez votre application DirectorySearcher et connectez-vous avec l’un de ces utilisateurs. Recherchez d’autres utilisateurs en fonction de leur UPN. Fermez l’application et exécutez-la de nouveau. Observez que la session utilisateur reste identique. Déconnectez-vous et reconnectez-vous sous le nom d’un autre utilisateur.

La bibliothèque ADAL facilite l’intégration de toutes ces fonctionnalités d’identité communes dans votre application. Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore. La seule chose que vous devez vraiment connaître est un appel unique d’API : `authContext.AcquireToken*(…)`.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Vous pouvez à présent aborder d’autres scénarios d’identité. Par exemple :

[Sécurisation d’une API web .NET avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->