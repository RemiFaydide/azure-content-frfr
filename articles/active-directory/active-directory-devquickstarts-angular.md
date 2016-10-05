<properties
	pageTitle="Prise en main d’Azure AD AngularJS | Microsoft Azure"
	description="Création d’une application à page unique Angular JS qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD à l’aide d’OAuth."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Sécurisation d’une application à page unique AngularJS avec Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD simplifie l’ajout d’appels API OAuth de connexion, de déconnexion et de sécurisation à vos applications à page unique. Il permet à votre application d’authentifier les utilisateurs avec leurs comptes Active Directory et de consommer une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les applications javascript s’exécutant dans un navigateur, Azure AD fournit la bibliothèque d’authentification Active Directory (adal.js). Le seul objectif de cette bibliothèque Adal.js est de faciliter l’obtention des jetons d’accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste des tâches AngularJS qui effectue les actions suivantes :

- connecte l’utilisateur à l’application à l’aide d’Azure AD comme fournisseur d’identité ;
- affiche des informations sur l’utilisateur ;
- appelle en toute sécurité l’API de liste des tâches de l’application en utilisant des jetons porteurs d’AAD ;
- déconnecte l’utilisateur de l’application.

Pour générer l’application fonctionnelle complète, vous devez :

2. inscrire votre application auprès d’Azure AD ;
3. installer la bibliothèque ADAL et configurer l’application à page unique ;
5. utiliser la bibliothèque ADAL pour sécuriser les pages de l’application à page unique.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## *1. Inscription de l’application DirectorySearcher*
Pour autoriser votre application à authentifier les utilisateurs et à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre client Azure AD :

-	Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
-	Cliquez sur **Active Directory** dans la partie de gauche.
-	Sélectionnez un client dans lequel inscrire l’application.
-	Cliquez sur l’onglet **Applications**, puis sur **Ajouter** dans le menu déroulant inférieur.
-	Suivez les invites et créez une **Application Web et/ou API Web**.
    -	Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	L’**URI de redirection** est l’emplacement auquel AAD renvoie les jetons. L’emplacement par défaut de cet exemple est le suivant :`https://localhost:44326/`
-	Une fois l’inscription terminée, AAD affecte un **ID client unique** à votre application. Copiez cette valeur à partir de l’onglet **Configurer**, car vous en aurez besoin dans les sections suivantes.
- Adal.js utilise le flux implicite OAuth pour communiquer avec Azure AD. Vous devez activer ce flux pour votre application comme suit :
    - Téléchargez le manifeste de l’application en cliquant sur **Gérer le manifeste**.
    - Ouvrez le manifeste et recherchez la propriété `oauth2AllowImplicitFlow`. Affectez-lui la valeur `true`.
    - Enregistrez et téléchargez le manifeste de l’application en cliquant de nouveau sur **Gérer le manifeste**.

## *2. Installation de la bibliothèque ADAL et configuration de l’application à page unique*
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer adal.js et écrire votre code lié à l’identité.

-	Commencez par ajouter adal.js au projet TodoSPA à l’aide de la console du gestionnaire de package :
  - Téléchargez [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) et ajoutez-le au répertoire du projet `App/Scripts/` .
  - Téléchargez [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) et ajoutez-le au répertoire du projet `App/Scripts/`.
  - Chargez chaque script avant la fin de la section `</body>` dans `index.html` :

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-	Pour que l’API de liste des tâches de serveur principal de l’application à page unique accepte les jetons à partir du navigateur, le serveur principal a besoin d’informations de configuration sur l’inscription de l’application. Dans le projet TodoSPA, ouvrez `web.config`. Remplacez les valeurs des éléments de la section `<appSettings>` afin qu’elles reflètent les valeurs que vous avez saisies dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	`ida:Tenant` est le domaine de votre client Azure AD, par exemple, contoso.onmicrosoft.com.
    -	`ida:Audience` est l’**ID client** de votre application, copié à partir du portail.

## *3. Utilisation de la bibliothèque ADAL pour sécuriser les pages de l’application à page unique*
La bibliothèque Adal.js a été conçue pour s’intégrer aux fournisseurs d’itinéraires et HTTP AngularJS, ce qui vous permet de sécuriser les vues individuelles dans votre application à page unique.

- Dans `App/Scripts/app.js`, ouvrez le module adal.js :

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Vous pouvez maintenant initialiser `adalProvider` avec les valeurs de configuration de l’inscription de votre application, également dans `App/Scripts/app.js` :

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Pour sécuriser la vue `TodoList` dans l’application, une seule ligne de code est nécessaire : `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Vous disposez maintenant d’une application à page unique sécurisée avec la possibilité de connecter les utilisateurs et d’émettre un jeton porteur vers son API de serveur principal. Lorsqu’un utilisateur clique sur le lien `TodoList`, adal.js redirige automatiquement la connexion vers Azure AD, le cas échéant. En outre, adal.js joint automatiquement un jeton d’accès à toutes les demandes ajax envoyées au serveur principal de l’application. Vous venez de découvrir le minimum vital requis pour générer une application à page unique avec adal.js. Il existe toutefois plusieurs autres fonctionnalités qui sont utiles dans les applications de ce type :

- Pour émettre explicitement des demandes de connexion et de déconnexion, vous pouvez définir des fonctions dans vos contrôleurs qui appellent adal.js. Dans `App/Scripts/homeCtrl.js` :

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Vous pouvez également présenter des informations utilisateur dans l’interface utilisateur de l’application. Le service adal a déjà été ajouté au contrôleur `userDataCtrl`. Vous pouvez donc accéder à l’objet `userInfo` dans la vue associée, `App/Views/UserData.html` :

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Il existe également de nombreux scénarios dans lesquels vous souhaitez savoir si l’utilisateur est connecté ou non. Vous pouvez aussi utiliser l’objet `userInfo` pour rassembler ces informations. Par exemple, dans `index.html`, vous pouvez afficher le bouton « Connexion » ou « Déconnexion » en fonction de l’état d’authentification :

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Félicitations ! Votre application à page unique intégrée à Azure AD est maintenant terminée. Elle peut authentifier les utilisateurs, appeler en toute sécurité son serveur principal à l’aide d’OAuth 2.0 et obtenir des informations de base sur l’utilisateur. Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs. Exécutez votre application à page unique de listes des tâches et connectez-vous avec un de ces utilisateurs. Ajoutez des tâches à la liste des tâches des utilisateurs, déconnectez-vous et reconnectez-vous.

La bibliothèque Adal.js facilite l’intégration de toutes ces fonctionnalités d’identité communes dans votre application. Elle effectue les tâches les plus ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Vous pouvez à présent aborder d’autres scénarios. Vous pouvez par exemple :

[Appeler une API web CORS à partir d’une application à page unique >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->