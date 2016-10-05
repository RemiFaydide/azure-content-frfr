<properties
	pageTitle="Application web .NodeJS Azure AD v2.0 | Microsoft Azure"
	description="Génération d’une application web NodeJS qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leurs comptes professionnel ou scolaire."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Ajouter une connexion à une application web NodeJS


> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).


Nous allons utiliser Passport pour :

- Connectez l’utilisateur à l’application à l’aide d’Azure AD et du point de terminaison v2.0.
- afficher des informations sur l’utilisateur ;
- déconnecter l’utilisateur de l’application.

**Passport** est un intergiciel d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter et bien d’autres. Nous avons développé une stratégie pour Microsoft Azure Active Directory. Nous allons installer ce module, puis nous y ajouterons le plug-in `passport-azure-ad` Microsoft Active Directory Azure.

## Télécharger

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

L'application terminée est également fournie à la fin de ce didacticiel.

## 1\. Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- ajouter la plateforme **Web** pour votre application ;
- entrer l’**URI de redirection** approprié. L’URI de redirection indique à Azure AD où les réponses d’authentification doivent être dirigées. La valeur par défaut pour ce didacticiel est `http://localhost:3000/auth/openid/return`.

## 2\. Ajoutez des éléments requis à votre répertoire

Dans la ligne de commande, placez les répertoires dans votre dossier racine s’ils n’y sont pas encore et exécutez les commandes suivantes :

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- En outre, nous avons utilisé `passport-azure-ad` dans la structure du démarrage rapide.

- `npm install passport-azure-ad`


Cela installera les bibliothèques dont dépend passport-azure-ad.

## 3\. Configurez votre application pour utiliser la stratégie passport-nod-js
Ici, nous allons configurer l’intergiciel Express pour utiliser le protocole d’authentification OpenID Connect. Passport sera utilisé notamment pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur.

-	Pour commencer, ouvrez le fichier `config.js` dans la racine du projet, puis entrez les valeurs de configuration de votre application dans la section `exports.creds`.
    -	L’élément `clientID:` est l’**ID d’application** affecté à votre application dans le portail d’inscription.
    -	L’élément `returnURL` est l’**URI de redirection** que vous avez saisi dans le portail.
    - Le `clientSecret` est la clé secrète que vous avez générée dans le portail

- Ouvrez ensuite le fichier `app.js` dans la racine du projet et ajoutez l’appel suivant pour appeler la stratégie `OIDCStrategy` qui est fournie avec `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Après cela, utilisez la stratégie référencée pour gérer les demandes de connexion

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    scope: config.creds.scope
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport utilise un modèle semblable pour toutes ses stratégies (Twitter, Facebook, etc.), que respectent tous les enregistreurs de stratégie. Comme vous pouvez le voir dans la stratégie, nous transmettons une function() dont les paramètres sont un jeton et un done. La stratégie revient vers nous une fois le travail terminé. Il est alors intéressant de stocker l’utilisateur et le jeton afin de ne pas avoir à les redemander.

> [AZURE.IMPORTANT]
Le code ci-dessus note tout utilisateur s’authentifiant sur notre serveur. C’est ce qu’on appelle l’enregistrement automatique. Dans les serveurs de production, il est préférable de faire passer toute personne qui essaie de se connecter par un processus d’inscription de votre choix. C’est généralement le modèle des applications consommateur qui vous permettent de vous inscrire via Facebook, mais vous demandent ensuite de renseigner des informations supplémentaires. S’il ne s’agissait pas d’un exemple d’application, nous aurions pu simplement extraire l’adresse de messagerie à partir de l’objet de jeton retourné, avant de les inviter à entrer des informations supplémentaires. Étant donné qu’il s’agit d’un serveur de test, nous les ajoutons simplement à la base de données en mémoire.

- Ensuite, nous allons ajouter les méthodes qui assureront le suivi des utilisateurs connectés, comme requis par Passport. Cela inclut la sérialisation et la désérialisation des informations d’utilisateur :

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- Ensuite, nous allons ajouter le code pour charger le moteur Express. Comme vous le voyez, nous utilisons le modèle par défaut /views et /routes fourni par Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Enfin, ajoutez les itinéraires POST servant à transmettre les demandes de connexion réelles au moteur `passport-azure-ad` :

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## 4\. Utilisation de Passport pour émettre des demandes de connexion et de déconnexion dans Azure AD

Votre application est maintenant correctement configurée pour communiquer avec le point de terminaison v2.0 à l’aide du protocole d’authentification OpenID Connect. `passport-azure-ad` a pris en charge le laborieux processus d’élaboration des messages d’authentification, de validation des jetons d’Azure AD et de gestion des sessions utilisateur. Il ne reste plus qu’à fournir aux utilisateurs un moyen de se connecter, de se déconnecter et de collecter des informations supplémentaires sur l’utilisateur connecté.

- Tout d’abord, ajoutons les méthodes par défaut de connexion, de compte et de déconnexion à notre fichier `app.js` :

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	Examinons-les en détail :
    -	L’itinéraire `/` redirige vers la vue index.ejs en transmettant l’utilisateur dans la demande (le cas échéant)
    - L’itinéraire `/account` ***s’assure d’abord que nous sommes authentifiés*** (nous implémentons cela ci-dessous), puis transmet l’utilisateur dans la demande afin que nous puissions obtenir des informations supplémentaires sur l’utilisateur.
    - L’itinéraire `/login` appelle notre authentificateur azuread-openidconnect à partir de `passport-azuread`. En cas d’échec, il redirige l’utilisateur vers /login
    - `/logout` appelle simplement logout.ejs (et l’itinéraire) qui efface les cookies, puis renvoie l’utilisateur à index.ejs


- Pour la dernière partie de `app.js`, ajoutons la méthode EnsureAuthenticated utilisée dans `/account` ci-dessus.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- Enfin, nous créons le serveur proprement dit dans `app.js` :

```JavaScript

app.listen(3000);

```


## 5\. Créer des vues et des itinéraires dans Express pour afficher notre utilisateur dans le site Web

Notre `app.js` est complet. À présent, il suffit d'ajouter les itinéraires et les vues qui affichent les informations que nous obtenons de l'utilisateur et traitent les itinéraires `/logout` et `/login` que nous avons créés.

- Créez l’itinéraire `/routes/index.js` sous le répertoire racine.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Création de l’itinéraire `/routes/user.js` sous le répertoire racine

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Ces itinéraires simples transmettent simplement la demande à nos vues, en incluant l’utilisateur le cas échéant.

- Créez la vue `/views/index.ejs` sous le répertoire racine. Il s’agit d’une page simple qui appelle nos méthodes de connexion et de déconnexion et nous permet de récupérer des informations de compte. Notez que nous pouvons utiliser l’instruction conditionnelle `if (!user)`, étant donné que l’utilisateur transmis dans la demande prouve qu’un utilisateur est connecté.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- Créez la vue `/views/account.ejs` sous le répertoire racine, afin d’afficher les informations supplémentaires que `passport-azuread` a placées dans la demande de l’utilisateur.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Enfin, améliorez-en l’apparence à l’aide d’une mise en page. Créez l’affichage « /views/layout.ejs » sous le répertoire racine

```HTML

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> |
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> |
			<a href="/account">Account</a> |
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>
```

Enfin, générez et exécutez votre application.

Exécutez `node app.js` et accédez à `http://localhost:3000`


Connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire, et notez comment l’identité de l’utilisateur est indiquée dans la liste de comptes. Vous disposez désormais d’une application web sécurisée à l’aide de protocoles standard et pouvant authentifier les utilisateurs avec leurs comptes personnels et professionnels/scolaires.

##Étapes suivantes

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Vous pouvez maintenant aborder des rubriques plus sophistiquées. Par exemple :

[Sécuriser une API web node.JS avec le point de terminaison v2.0 >>](active-directory-v2-devquickstarts-node-api.md)

Pour obtenir des ressources supplémentaires, consultez :
- [Guide du développeur 2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active-directory" tag >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## Obtenir les mises à jour de sécurité de nos produits

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0921_2016-->