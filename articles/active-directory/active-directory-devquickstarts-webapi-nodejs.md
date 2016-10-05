<properties
	pageTitle="Prise en main d’Azure AD NodeJS | Microsoft Azure"
	description="Création d’une API Web REST Node.js qui s’intègre à Azure AD pour l’authentification."
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

# Prise en main de l’API web pour Node

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** est un intergiciel d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter et bien d’autres. Nous avons développé une stratégie pour Microsoft Azure Active Directory. Nous allons installer ce module, puis nous y ajouterons le plug-in `passport-azure-ad` Microsoft Active Directory Azure.

Pour ce faire, vous devez :

1. inscrire une application auprès d’Azure AD ;
2. configurer votre application pour utiliser azure-ad-passport du plug-in Passport ;
3. configurer une application cliente pour appeler l’API web To Do List.

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] Cet article ne couvre pas l'implémentation de la connexion, de l'inscription et de la gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié. Si ce n’est déjà fait, vous devez commencer par le document [Intégration à Azure Active Directory](active-directory-how-to-integrate.md) pour en savoir plus sur les notions de base d’Azure Active Directory.


Nous avons publié tout le code source de cet exemple dans GitHub sous une licence MIT. N’hésitez pas à cloner (ou mieux, à répliquer), à commenter et à soumettre des demandes d’extraction.

## À propos des modules Node.js

Nous utiliserons les modules Node.js au cours de cette procédure détaillée. Les modules sont des packages JavaScript chargeables qui fournissent une fonctionnalité spécifique à votre application. En général, vous installez un module à l’aide de l’outil en ligne de commande NPM Node.js. Néanmoins, certains modules, comme le module HTTP, sont intégrés au package Node.js principal. Les modules installés sont stockés dans le répertoire node\_modules, à la racine de votre répertoire Node.js. Chaque module du répertoire node\_modules conserve son propre répertoire node\_modules qui contient tous les modules dont il dépend. Chaque module requis dispose de son propre répertoire node\_modules. Cette structure de répertoire récursive représente la chaîne de dépendance.

Cette structure de chaîne de dépendance produit une application certes plus encombrante, mais elle garantit que toutes les dépendances sont vérifiées et que la version des modules utilisée dans le développement sera également utilisée en production. Le comportement de l’application de production est ainsi plus prévisible et cela évite les problèmes de version pouvant affecter les utilisateurs.

## 1\. Inscrire un locataire Azure AD

Pour utiliser cet exemple, vous aurez besoin d’un client Azure Active Directory. Si vous n’êtes pas sûr de ce qu’est un client ou si vous ne savez pas comment en obtenir un, consultez la page [Obtention d’un client Azure AD](active-directory-howto-tenant.md).

## 2\. Création d'une application

Vous devez maintenant créer dans votre répertoire une application fournissant à Azure AD certaines informations nécessaires pour communiquer en toute sécurité avec votre application. L’application cliente et l’API web seront alors toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-how-applications-are-added.md). Si vous générez une application métier, [ces instructions supplémentaires peuvent être utiles](active-directory-applications-guiding-developers-for-lob-applications.md).

Veillez à effectuer les opérations suivantes :

- Connectez-vous au portail de gestion Azure.
- Cliquez sur **Active Directory** dans la partie de gauche.
- Sélectionnez le client dans lequel vous souhaitez inscrire l’application.
- Cliquez sur l’onglet **Applications**, puis sur Ajouter dans le menu déroulant inférieur.
- Suivez les invites et créez une **Application Web et/ou API Web**.
    - Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    - L’**URL de connexion** est l’URL de base de votre application. La valeur par défaut de l’exemple de code est `https://localhost:8080`.
    - Un **URI ID d’application** est un identificateur unique pour votre application. L’usage est d’utiliser `https://<tenant-domain>/<app-name>`, par exemple `https://contoso.onmicrosoft.com/my-first-aad-app`.
- Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet Configurer, car vous en aurez besoin dans les sections suivantes.

- RAPPEL : créez un **secret** pour votre application et notez-le. Vous en aurez besoin rapidement.
- RAPPEL : notez également l’**ID d’application** affecté à votre application. Vous en aurez aussi besoin rapidement.


## 3\. Télécharger node.js pour votre plateforme
Pour réussir à utiliser cet exemple, Node.js doit être installé correctement.

Installez Node.js à partir de l’adresse suivante : [http://nodejs.org](http://nodejs.org).

## 4\. Installer MongoDB sur votre plateforme

Pour réussir à utiliser cet exemple, MongoDB doit être installé correctement. Nous allons utiliser MongoDB afin que notre API REST soit persistante sur plusieurs instances de serveur.

Installez MongoDB à partir de l’adresse suivante : [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Cette procédure détaillée suppose que vous utilisez l’installation et les points de terminaison du serveur par défaut pour MongoDB, c’est-à-dire les suivants au moment de la rédaction : mongodb://localhost


## 5\. Installer les modules Restify sur votre API Web

Nous allons utiliser Restify pour concevoir notre API REST. Restify est une infrastructure d’application Node.js minimale et flexible dérivée d’Express, qui inclut un ensemble complet de fonctionnalités permettant de créer des API REST sur Connect.

### Installation de Restify

À partir de la ligne de commande, accédez au répertoire azuread. Si le répertoire **azuread** n’existe pas, créez-le.

`cd azuread - or- mkdir azuread; cd azuread`

Tapez la commande suivante :

`npm install restify`

Cette commande permet d’installer Restify.

#### UNE ERREUR EST-ELLE SURVENUE ?

Sur certains systèmes d’exploitation, NPM peut générer une erreur Error: EPERM, chmod ’/usr/local/bin/..’ et une demande pour essayer d’exécuter le compte en tant qu’administrateur. Si cette erreur survient, utilisez la commande sudo pour exécuter npm avec des privilèges plus élevés.

#### UNE ERREUR RELATIVE À DTRACE EST-ELLE SURVENUE ?

Lors de l’installation de Restify, vous risquez de voir ceci :

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


Restify fournit un mécanisme puissant pour suivre les appels REST utilisant DTrace. Toutefois, de nombreux systèmes d’exploitation ne disposent pas de DTrace. Vous pouvez ignorer ces erreurs.


Le résultat de cette commande doit ressembler à ceci :


	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)


## 6\. Installer Passport.js dans votre API Web

[Passport](http://passportjs.org/) est un intergiciel d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter et bien d’autres. Nous avons développé une stratégie pour Azure Active Directory. Nous installerons ce module, puis nous y ajouterons le plug-in stratégique Active Directory Azure.

À partir de la ligne de commande, accédez au répertoire azuread.

Saisissez la commande suivante pour installer passport.js.

`npm install passport`

Le résultat de la commande doit ressembler à ceci :

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 7\. Ajouter Passport-Azure-AD à votre API Web

Ensuite, nous allons ajouter la stratégie OAuth, à l’aide de passport-azuread, un ensemble de stratégies qui connectent Azure Active Directory à Passport. Nous allons utiliser cette stratégie pour les jetons du porteur dans cet exemple d’API Rest.

> [AZURE.NOTE] Bien qu’OAuth2 fournisse une infrastructure dans laquelle tout type de jeton connu peut être émis, seuls certains types de jeton sont utilisés de manière généralisée. Pour protéger les points de terminaison qui se sont révélés être des jetons porteurs. Les jetons porteurs sont les jetons les plus largement émis dans OAuth2, et de nombreuses implémentations considèrent qu’ils sont le seul type de jeton émis.

Depuis la ligne de commande, accédez au répertoire azuread

Saisissez la commande suivante pour installer le module passport-azure-ad Passport.js :

`npm install passport-azure-ad`

Le résultat de la commande doit ressembler à ceci :

	``
	passport-azure-ad@1.0.0 node_modules/passport-azure-ad
	├── xtend@4.0.0
	├── xmldom@0.1.19
	├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
	├── underscore@1.8.3
	├── async@1.3.0
	├── jsonwebtoken@5.0.2
	├── xml-crypto@0.5.27 (xpath.js@1.0.6)
	├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
	├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
	├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
	└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## 8\. Ajouter des modules MongoDB à votre API Web

Nous allons utiliser MongoDB comme magasin de données. Par conséquent, nous devons installer le plug-in le plus largement utilisé pour gérer les modèles et schémas, appelé Mongoose, ainsi que le pilote de base de données de MongoDB, également appelé MongoDB.


* `npm install mongoose`

## 9\. Installation de modules supplémentaires

Ensuite, nous devons installer les autres modules requis.


Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`


Entrez les commandes suivantes pour installer les modules suivants dans le répertoire node\_modules :

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## 10\. Créer un fichier server.js avec vos dépendances

Le fichier server.js fournit la majorité des fonctionnalités du serveur de l’API web. La plupart du code sera ajouté à ce fichier. À des fins de production, vous souhaitez refactoriser la fonctionnalité en fichiers plus petits, par exemple des itinéraires et des contrôleurs distincts. Pour les besoins de cette démonstration, nous utiliserons server.js pour cette fonctionnalité.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Créez un fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript
	'use strict';

	/**
 	* Module dependencies.
 	*/

	var fs = require('fs');
	var path = require('path');
	var util = require('util');
	var assert = require('assert-plus');
	var bunyan = require('bunyan');
	var getopt = require('posix-getopt');
	var mongoose = require('mongoose/');
	var restify = require('restify');
	var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Enregistrez le fichier . Nous y reviendrons rapidement.

## 11:. Créer un fichier de configuration pour stocker vos paramètres Azure AD

Ce fichier de code transmet les paramètres de configuration de votre portail Azure Active Directory vers Passport.js. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API web au portail, dans la première partie de la procédure détaillée. Copiez le code ; nous vous expliquerons ensuite ce qu’il faut placer dans les valeurs de ces paramètres.


Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Créez un fichier `config.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration', 
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
Enregistrez le fichier .

## 12\. Ajouter des paramètres de configuration dans votre fichier server.js

Nous avons besoin de lire ces valeurs dans le fichier de configuration que vous venez de créer dans notre application. Pour ce faire, nous ajoutons simplement le fichier .config comme une ressource requise dans notre application, puis nous réglons les variables globales sur celles utilisées dans le document config.js.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ensuite, ajoutez une nouvelle section à `server.js` avec le code suivant :

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        }, 
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Enregistrez le fichier .



## 13\. Ajouter les informations du modèle et du schéma MongoDB à l’aide de Moongoose

Toute cette préparation va maintenant prouver son utilité : associez ces trois fichiers dans un service API REST.

Pour cette procédure détaillée, nous utilisons MongoDB pour stocker les tâches, comme indiqué dans l’***étape 4***.

Si vous vous rappelez le contenu du fichier `config.js` que nous avons créé dans l’***étape 11***, vous savez que nous avons appelé notre base de données `tasklist`, car c’était ce que nous avions placé à la fin de l’URL de connexion mogoose\_auth\_local. Vous n’avez pas besoin de créer cette base de données au préalable dans MongoDB ; il la crée lors de la première exécution de notre application de serveur (à condition qu’elle n’existe pas déjà).

Maintenant que nous avons indiqué au serveur quelle base de données MongoDB nous souhaitons utiliser, nous devons écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches du serveur.

#### Considérations sur le modèle

Notre modèle de schéma est très simple et vous pouvez le développer en fonction de vos besoins.

NOM : le nom de la personne à qui la tâche est assignée. ***Chaîne***

TÂCHE : la tâche elle-même. ***Chaîne***

DATE : la date d’échéance de la tâche. ***DATEHEURE***

TERMINÉ : indique si la tâche est terminée ou non. ***BOOLÉEN***

#### Création du schéma dans le code


Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes sous l’entrée de configuration :

```Javascript
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    task: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Comme vous avez pu le constater dans le code, nous créons notre schéma, puis un objet de modèle que nous utilisons pour stocker nos données dans le code lors du paramétrage de nos ***itinéraires***.

## 14\. Ajouter nos itinéraires pour notre serveur API REST

Maintenant que nous disposons d’un modèle de base de données, ajoutons les itinéraires que nous utiliserons pour notre serveur API REST.

### À propos des itinéraires dans Restify

Dans Restify, les itinéraires fonctionnent de la même façon que dans la pile Express. Vous définissez des itinéraires à l’aide de l’URI qui, selon vous, sera appelé par les applications clientes. En général, vous définissez vos itinéraires dans un fichier distinct. Pour cet exemple, nous placerons nos itinéraires dans le fichier server.js. Nous vous recommandons de les factoriser dans leur propre fichier s’ils sont destinés à la production.

Voici un exemple de modèle d’un itinéraire Restify :

```Javascript
function createObject(req, res, next) {

// do work on Object

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // keep the server going
}

....

server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.

```


Il s’agit d’un modèle très basique. Restify (et Express) fournissent des fonctionnalités bien plus approfondies, comme la définition de types d’application et la création d’un routage complexe entre différents points de terminaison. Dans le cade de cet exemple, nous allons créer des itinéraires très simples.

### 1\. Ajout d’itinéraires par défaut à notre serveur

Nous allons à présent ajouter les itinéraires CRUD de base suivants : Créer, Extraire, Mettre à jour et Supprimer.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes en-dessous des entrées de base de données créées précédemment :

```Javascript

/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### 2\. Nous allons ensuite ajouter une gestion des erreurs dans nos API :

```

///--- Errors for communicating something interesting back to the client

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## 15\. Créer votre serveur

Nous avons défini notre base de données et nos itinéraires sont en place. La dernière étape est d’ajouter l’instance de serveur qui va gérer nos appels.

Restify (et Express) disposent de nombreuses options de personnalisation approfondie applicables à un serveur API REST, mais à nouveau, nous allons utiliser la configuration la plus simple pour cet exemple.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
```

## 16\. Ajouter les itinéraires au serveur (sans authentification à ce stade)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## 17\. Exécutons le serveur avant d’ajouter la prise en charge d’OAuth.

Test de votre serveur avant d’ajouter l’authentification

Pour cela, le plus simple consiste à utiliser curl dans une ligne de commande. Avant cela, nous avons besoin d’un utilitaire simple qui nous permette d’analyser la sortie au format JSON. Pour ce faire, vous devez installer l’outil json, car tous les exemples ci-dessous l’utilisent.

`$npm install -g jsontool`

Cette opération installe totalement l’outil JSON. Maintenant que l’outil JSON est installé, amusons-nous avec le serveur :

Tout d’abord, vérifiez que votre instance MongoDB est en cours d’exécution.

`$sudo mongod`

Accédez ensuite au répertoire et commencez à utiliser curl.

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Nous pouvons à présent ajouter une tâche de la manière suivante :

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

La réponse doit être la suivante :

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Et nous pouvons répertorier des tâches pour Brandon de cette manière :

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si tout cela fonctionne, nous sommes prêts à ajouter OAuth au serveur API REST.

**Vous avez un serveur d’API REST avec MongoDB !**


## 18\. Ajouter une authentification à notre serveur API REST

Maintenant que nous avons une API REST en cours d’exécution (félicitations d’ailleurs !), offrons-lui une utilité dans Azure AD.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

### 1 : utilisation de la stratégie OIDCBearerStrategy incluse avec passport-azure-ad

Jusqu’à présent, nous avons créé un serveur REST TODO standard ne disposant d’aucune autorisation. Nous allons commencer à tout rassembler.

Tout d’abord, nous devons indiquer que nous voulons utiliser Passport. Placez cela juste après la configuration de l’autre serveur :

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Lors de l’écriture d’API, vous devez toujours lier les données à un élément unique du jeton, dont un utilisateur ne peut pas usurper l’identité. Lorsque ce serveur stocke les éléments TODO, il le fait en fonction de l’ID d’objet de l’utilisateur dans le jeton (appelé via token.oid) que nous avons placé dans le champ « propriétaire ». Cela garantit que seul cet utilisateur peut accéder à ses TODO et que personne d’autre ne peut accéder aux TODO entrés. Il n’y a pas d’exposition dans l’API du « propriétaire », afin qu’un utilisateur externe puisse demander les TODO d’un tiers même s’ils sont authentifiés.

Ensuite, nous allons utiliser la stratégie Bearer incluse dans passport-azure-ad. Contentez-vous d’examiner le code pour l’instant, je fournirai des explications sous peu. Placez ceci après ce que vous avez collé ci-dessus :

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/

var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.sub === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var bearerStrategy = new BearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                users.push(token);
                owner = token.sub;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(bearerStrategy);
```

Passport utilise un modèle semblable pour toutes ses stratégies (Twitter, Facebook, etc.), que respectent tous les enregistreurs de stratégie. Comme vous pouvez le voir dans la stratégie, nous transmettons une function() dont les paramètres sont un jeton et un done. La stratégie revient vers nous une fois le travail terminé. Il est alors intéressant de stocker l’utilisateur et le jeton afin de ne pas avoir à les redemander.

> [AZURE.IMPORTANT]
Le code ci-dessus note tout utilisateur s’authentifiant sur notre serveur. C’est ce qu’on appelle l’enregistrement automatique. Dans les serveurs de production, il est préférable de faire passer toute personne qui essaie de se connecter par un processus d’inscription de votre choix. C’est généralement le modèle des applications consommateur qui vous permettent de vous inscrire via Facebook, mais vous demandent ensuite de renseigner des informations supplémentaires. S’il ne s’agissait pas d’un programme de ligne de commande, nous aurions pu simplement extraire l’adresse de messagerie à partir de l’objet de jeton retourné, avant de les inviter à entrer des informations supplémentaires. Étant donné qu’il s’agit d’un serveur de test, nous les ajoutons simplement à la base de données en mémoire.

### 2\. Pour finir, protégez certains points de terminaison

Pour protéger les points de terminaison, spécifiez l’appel `passport.authenticate()` avec le protocole que vous souhaitez utiliser.

Nous allons modifier notre itinéraire dans le code de notre serveur afin d’effectuer quelque chose de plus intéressant :

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 19\. Exécuter à nouveau votre application de serveur et vous assurer de votre rejet

Nous allons de nouveau utiliser `curl` pour voir si nous disposons à présent de la protection OAuth2 sur nos points de terminaison. Nous effectuons ce contrôle avant d’exécuter l’un de nos Kits de développement logiciel (SDK) clients sur ce point de terminaison. Les en-têtes renvoyés doivent être suffisants pour nous dire que nous sommes sur le bon chemin d’accès.

Tout d’abord, vérifiez que votre instance monogoDB est en cours d’exécution :

  $sudo mongod

Accédez ensuite au répertoire et commencez à utiliser curl.

  $ cd azuread $ node server.js

Essayez une commande basique, comme POST :

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Une erreur 401 est la réponse que vous cherchez ici, car cela indique que la couche de Passport tente de rediriger vers le point de terminaison d’autorisation, ce qui est exactement ce que vous souhaitez.

## Félicitations ! Vous avez un service d’API REST utilisant OAuth2 !

Vous êtes allé aussi loin que possible avec ce serveur sans utiliser un client compatible OAuth2. Vous devrez suivre une autre procédure détaillée.

Si vous recherchiez simplement des informations concernant l’implémentation d’une API REST à l’aide de Restify et d’OAuth2, vous avez plus de code que nécessaire pour poursuivre le développement de votre service et l’apprentissage de la création à partir de cet exemple.

Si vous êtes intéressé par les étapes suivantes dans votre parcours de la bibliothèque ADAL, voici quelques clients de la bibliothèque ADAL pris en charge sur lesquels nous vous recommandons de continuer à travailler :

Clonez simplement sur votre ordinateur de développement et configurez comme indiqué dans la procédure détaillée.

[Bibliothèque ADAL pour iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[Bibliothèque ADAL pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->