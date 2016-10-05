<properties
	pageTitle="Prise en main d’Azure AD Xamarin | Microsoft Azure"
	description="Création d’une application Xamarin qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Intégration d’Azure AD dans une application Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin vous permet d’écrire des applications mobiles en C# qui peuvent s’exécuter sur iOS, Android et Windows (appareils mobiles et PC). Si vous créez une application à l’aide de Xamarin, Azure AD facilite l’authentification de vos utilisateurs avec leurs comptes Active Directory. Il permet également à votre application d’utiliser en toute sécurité une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les applications Xamarin qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d'accès pour votre application. Pour illustrer cette simplicité, nous créerons une application « Directory Searcher » effectuant les actions suivantes :

-	exécution sur iOS, Android, Bureau Windows, Windows Phone et Windows Store ;
- utilisation d’une bibliothèque de classes portable (PCL) unique pour authentifier les utilisateurs et obtenir des jetons pour l’API Graph Azure AD ;
-	recherche, dans un répertoire, d’utilisateurs correspondant à un UPN (nom d’utilisateur principal) donné.

Pour générer l’application fonctionnelle complète, vous devez :

2. configurer votre environnement de développement Xamarin ;
2. inscrire votre application auprès d’Azure AD ;
3. installer et configurer la bibliothèque ADAL ;
5. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Pour commencer, téléchargez [la structure du projet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Chaque option est une solution Visual Studio 2013. Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## *0. Configuration de votre environnement de développement Xamarin*
Étant donné que ce didacticiel inclut des projets pour iOS, Android et Windows, vous avez besoin de Visual Studio et de Xamarin. Pour créer l’environnement nécessaire, suivez les instructions complètes à la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) sur MSDN. Ces instructions incluent des documents que vous pouvez consulter pour en savoir plus sur Xamarin pendant que l’exécution des programmes d’installation se termine.

Une fois vous avez terminé la configuration nécessaire, ouvrez la solution dans Visual Studio pour commencer. Vous trouverez six projets : cinq projets propres à la plateforme et une bibliothèque de classes portable partagée par toutes les plateformes, `DirectorySearcher.cs`.

## *1. Inscription de l’application Directory Searcher*
Pour autoriser votre application à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD :

-	Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
-	Cliquez sur **Active Directory** dans la partie de gauche.
-	Sélectionnez un client dans lequel inscrire l’application.
-	Cliquez sur l’onglet **Applications**, puis sur **Ajouter** dans le menu déroulant inférieur.
-	Suivez les invites et créez une **application cliente native**.
    -	Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	L’**URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons. Entrez une valeur, par exemple `http://DirectorySearcher`.
-	Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet **Configurer**, car vous en aurez besoin dans les sections suivantes.
- Toujours sous l’onglet **Configurer**, cherchez la section Autorisations pour d’autres applications. Pour l’application Azure Active Directory, ajoutez l’autorisation **Accéder au répertoire de l’organisation** sous **Autorisations déléguées**. Cela permet à votre application d’interroger l’API Graph concernant les utilisateurs.

## *2. Installation et configuration de la bibliothèque ADAL*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité. Pour permettre à ADAL de communiquer avec Azure AD, vous devez lui fournir des informations sur l’inscription de votre application.
-	Commencez par ajouter ADAL à chaque projet de la solution à l’aide de la console du gestionnaire de package.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Notez que deux références de bibliothèque sont ajoutées à chaque projet : la partie de la bibliothèque PCL de la bibliothèque ADAL et une partie spécifique de la plateforme.

-	Dans le projet DirectorySearcher Lib, ouvrez `DirectorySearcher.cs`. Modifiez les valeurs de membre de classe afin de refléter les valeurs que vous avez saisies dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	`tenant` est le domaine de votre client Azure AD, par exemple, contoso.onmicrosoft.com.
    -	`clientId` est l’ID client de votre application que vous avez copié à partir du portail.
    - `returnUri` est l’URI de redirection que vous avez entré dans le portail, par exemple, `http://DirectorySearcher`.

## *3. Utilisation de la bibliothèque ADAL pour obtenir des jetons à partir d’AAD*
*Presque* toute la logique d’authentification de l’application réside dans `DirectorySearcher.SearchByAlias(...)`. Tout ce qui est nécessaire dans les projets propres à la plateforme est de transmettre un paramètre contextuel à la PCL `DirectorySearcher`.

- Tout d’abord, ouvrez `DirectorySearcher.cs` et ajoutez un nouveau paramètre à la méthode `SearchByAlias(...)`. `IPlatformParameters` est le paramètre contextuel qui encapsule les objets spécifiques de la plateforme que la bibliothèque ADAL doit authentifier.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	Ensuite, initialisez `AuthenticationContext`, classe principale de la bibliothèque ADAL. C’est à ce moment que vous fournissez à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD. Appelez ensuite `AcquireTokenAsync(...)`, qui accepte l’objet `IPlatformParameters` et appelle le flux d’authentification nécessaire pour renvoyer un jeton à l’application.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)` tente tout d’abord de renvoyer un jeton pour la ressource demandée (l’API Graph dans ce cas) sans inviter l’utilisateur à entrer ses informations d’identification (via la mise en cache ou l’actualisation des anciens jetons). Le cas échéant seulement, il affiche la page de connexion d’Azure AD à l’utilisateur avant d’acquérir le jeton demandé.


- Vous pouvez ensuite joindre le jeton d’accès à la demande de l’API Graph, dans l’en-tête d’autorisation :

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Le rôle de la PCL `DirectorySearcher` et du code lié à l’identité de votre application s’arrête là. Il ne reste qu’à appeler la méthode `SearchByAlias(...)` dans les affichages de chaque plateforme et, le cas échéant, d’ajouter le code permettant de gérer correctement le cycle de vie de l’interface utilisateur.

####Android :
- Dans `MainActivity.cs`, ajoutez un appel à `SearchByAlias(...)` dans le gestionnaire de clic de bouton :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Vous devez également substituer la méthode du cycle de vie `OnActivityResult` pour rediriger toute authentification vers la méthode appropriée. La bibliothèque ADAL fournit une méthode d’assistance pour cela dans Android :

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Bureau Windows :
- Dans `MainWindow.xaml.cs`, appelez simplement `SearchByAlias(...)` en passant un `WindowInteropHelper` dans l’objet `PlatformParameters` du bureau :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS :
- Dans `DirSearchClient_iOSViewController.cs`, l’objet iOS `PlatformParameters` se réfère simplement au contrôleur d’affichage :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows Universal :
- Dans Windows Universal, ouvrez `MainPage.xaml.cs` et implémentez la méthode `Search`, qui utilise une méthode d’assistance dans un projet partagé afin de mettre à jour l’interface utilisateur en fonction des besoins.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Félicitations ! Vous disposez maintenant d’une application Xamarin fonctionnelle capable d’authentifier les utilisateurs et d’appeler en toute sécurité les API web à l’aide d’OAuth 2.0 sur cinq plateformes différentes. Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs. Exécutez votre application DirectorySearcher et connectez-vous avec l’un de ces utilisateurs. Recherchez d’autres utilisateurs en fonction de leur UPN.

La bibliothèque ADAL facilite l’intégration des fonctionnalités d’identité communes dans votre application. Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore. La seule chose que vous devez vraiment connaître est un appel unique d’API : `authContext.AcquireToken*(…)`.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Vous pouvez à présent aborder d’autres scénarios d’identité. Par exemple :

[Sécurisation d’une API web .NET avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->