<properties
	pageTitle="Prise en main d’Azure AD v2.0 AngularJS | Microsoft Azure"
	description="Génération d’une application à page unique AngularJS qui connecte les utilisateurs avec les comptes Microsoft personnels et les comptes professionnels ou scolaires."
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


# Ajouter une connexion à une application AngularJS à une seule page - NodeJS

Dans cet article, nous allons ajouter la connexion avec des comptes Microsoft à une application AngularJS à l’aide du point de terminaison Azure Active Directory v2.0. Le point de terminaison v2.0 permet d’effectuer une intégration simple dans votre application et d’authentifier les utilisateurs avec des comptes personnels ou professionnels/scolaires.

Cet exemple est une application à page unique de liste de tâches qui stocke les tâches dans une API REST du serveur principal, écrite en NodeJS et sécurisée à l’aide de jetons du porteur OAuth d’Azure AD. L’application AngularJS utilisera notre bibliothèque d’authentification open source JavaScript [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) afin de gérer l’intégralité du processus de connexion et d’acquérir les jetons utilisés pour appeler l’API REST. Le même modèle peut être appliqué pour l’authentification d’autres API REST, comme les API [Microsoft Graph](https://graph.microsoft.com) ou Azure Resource Manager.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).

## Télécharger

Pour commencer, vous devez télécharger et installer [node.js](https://nodejs.org). Vous pouvez ensuite cloner ou [télécharger](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) une application squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

L’application squelette comprend l’ensemble du code réutilisable associé à une application AngularJS simple, mais n’intègre pas l’ensemble des éléments liés à l’identité. Si vous ne souhaitez pas suivre la procédure, vous pouvez cloner ou [télécharger](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) l’exemple terminé.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## Inscription d’une application

Dans un premier temps, créez une application dans le [portail d’inscription des applications](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- ajouter la plateforme **web** pour votre application ;
- entrer l’**URI de redirection** approprié. La valeur par défaut pour cet exemple est `http://localhost:8080`.
- Laissez la case **Autoriser un flux implicite** sélectionnée.

Copiez l’**ID d’application** affecté à votre application ; vous en aurez besoin rapidement.

## Installer adal.js
Pour commencer, accédez au projet téléchargé, puis installez adal.js. Si [bower](http://bower.io/) est installé, il vous suffit d’exécuter cette commande. En cas d’incompatibilité de versions de dépendance, sélectionnez la version la plus élevée.
```
bower install adal-angular#experimental
```

Sinon, vous pouvez télécharger manuellement [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) et [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Ajoutez les deux fichiers au répertoire `app/lib/adal-angular-experimental/dist`.

Ouvrez maintenant le projet dans votre éditeur de texte favori, puis chargez adal.js à la fin du corps de la page :

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## Configurer l’API REST

Pendant la configuration, tentons de lancer l’API REST du serveur principal. Dans une invite de commande, installez l’ensemble des packages en exécutant (assurez-vous d’être dans le répertoire de niveau supérieur du projet) :

```
npm install
```

Ouvrez maintenant `config.js`, puis remplacez la valeur `audience` :

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
	 
	 ...
}
```

L’API REST utilise cette valeur pour valider les jetons reçus de l’application Angular, dans les requêtes AJAX. Notez que cette API REST simple stocke les données en mémoire. Lorsque vous arrêtez le serveur, vous perdez l’ensemble des tâches précédemment créées.

Nous n’évoquerons pas plus en avant le fonctionnement de l’API REST. N’hésitez pas à examiner le code, mais si vous souhaitez en savoir plus sur la sécurisation des API web à l’aide d’Azure AD, consultez [cet article](active-directory-v2-devquickstarts-node-api.md).

## Connecter les utilisateurs
Il est temps d’écrire du code d’identité. Vous avez peut-être remarqué que adal.js contient un fournisseur AngularJS, qui se combine parfaitement aux mécanismes de routage Angular. Commencez par ajouter le module adal à l’application :

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Vous pouvez désormais initialiser l’élément `adalProvider` avec votre ID d’application :

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Très bien, désormais adal.js possède toutes les informations nécessaires pour sécuriser votre application et connecter les utilisateurs. Pour forcer la connexion via un itinéraire particulier dans l’application, il vous suffit d’une ligne de code :

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Désormais, lorsqu’un utilisateur clique sur le lien `TodoList`, adal.js redirige automatiquement vers Azure AD pour la connexion, si nécessaire. Vous pouvez également envoyer explicitement des requêtes de connexion et de déconnexion en invoquant adal.js dans vos contrôleurs :

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## Afficher les informations utilisateur
Maintenant que l’utilisateur est connecté, il vous faudra probablement accéder à ses données d’authentification dans votre application. Adal.js expose ces informations pour vous dans l’objet `userInfo`. Pour accéder à cet objet dans une vue, ajoutez adal.js sur l’étendue racine du contrôleur correspondant :

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Vous pouvez ensuite directement traiter l’objet `userInfo` dans votre vue :

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Vous pouvez également utiliser l’objet `userInfo` afin de déterminer si l’utilisateur est connecté ou non.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## Appeler l’API REST
Enfin, il est temps d’obtenir certains jetons et d’appeler l’API REST afin de créer, de lire, de mettre à jour et de supprimer les tâches. Devinez quoi ? Vous n’avez *rien* à faire. Adal.js se charge automatiquement d’obtenir, de mettre en cache et d’actualiser les jetons. Il joint également ces jetons aux requêtes AJAX sortantes que vous envoyez à l’API REST.

Comment ceci fonctionne-t-il ? Ces résultats sont obtenus à l’aide des [intercepteurs AngularJS](https://docs.angularjs.org/api/ng/service/$http) magiques, grâce auxquels adal.js peut transformer les messages http entrants et sortants. En outre, adal.js suppose que l’ensemble des requêtes envoyées sur le domaine de la fenêtre doivent utiliser les jetons destinés à l’ID d’application de l’application AngularJS. Dès lors, nous avons utilisé un ID d’application identique dans l’application Angular et dans l’API REST NodeJS. Cela va de soi, vous pouvez ignorer ce comportement et demander à adal.js d’obtenir des jetons pour d’autres API REST, si nécessaire. Toutefois, pour ce scénario simple, les valeurs par défaut sont suffisantes.

Voici un extrait qui montre combien il est facile d’envoyer des requêtes à l’aide de jetons du porteur, à partir d’Azure AD :

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Félicitations ! Votre application intégrée Azure AD à page unique est désormais terminée. Vous pouvez lui tirer votre chapeau. Elle peut authentifier les utilisateurs, appeler de manière sécurisée son API REST du serveur principal à l’aide d’OpenID Connect, et obtenir des informations de base sur l’utilisateur. Dès le départ, elle prend en charge l’ensemble des utilisateurs disposant d’un compte Microsoft personnel ou un compte professionnel/scolaire d’Azure AD. Testez l’application en exécutant :

```
node server.js
```

Dans un navigateur, accédez à `http://localhost:8080`. Connectez-vous à un compte personnel Microsoft ou à un compte scolaire/professionnel. Ajoutez des tâches à la liste des tâches de l’utilisateur, puis déconnectez-vous. Essayez de vous connecter avec l’autre type de compte. Si vous avez besoin qu’un locataire Azure AD crée des utilisateurs scolaires/professionnels, [découvrez comment en obtenir un ici](active-directory-howto-tenant.md) (c’est gratuit).

Pour poursuivre l’apprentissage sur le point de terminaison v2.0, consultez de nouveau notre [guide du développeur v2.0](active-directory-appmodel-v2-overview.md). Pour obtenir des ressources supplémentaires, consultez :

- [Exemples Azure sur GitHub >>](https://github.com/Azure-Samples)
- [Azure AD sur Stack Overflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Documentation Azure AD sur [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## Obtenir les mises à jour de sécurité de nos produits

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0921_2016-->