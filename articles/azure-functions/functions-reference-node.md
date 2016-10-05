<properties
	pageTitle="Informations de référence pour les développeurs NodeJS sur Azure Functions | Microsoft Azure"
	description="Découvrez comment développer sur Azure Functions à l’aide de NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="nodejs"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Informations de référence pour les développeurs NodeJS sur Azure Functions

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.JS](../articles/azure-functions/functions-reference-node.md)

L’expérience Node/JavaScript pour Azure Functions facilite l’exportation d’une fonction qui reçoit un objet `context` pour communiquer avec le runtime et pour recevoir et envoyer des données par le biais des liaisons.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md).

## Exporter une fonction

Toutes les fonctions JavaScript doivent exporter un seul `function` par le biais de `module.exports` pour que le runtime trouve la fonction et l’exécute. Cette fonction doit toujours inclure un objet `context`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Les liaisons de `direction === "in"` sont transmises en tant qu’arguments de fonction, ce qui signifie que vous pouvez utiliser [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) pour gérer de manière dynamique les nouvelles entrées (par exemple, en utilisant `arguments.length` pour effectuer une itération sur toutes vos entrées). Cette fonctionnalité est très pratique si vous ne disposez que d’un déclencheur sans entrée supplémentaire, dans la mesure où vous pouvez accéder de manière prévisible aux données de votre déclencheur sans faire référence à votre objet `context`.

Les arguments sont toujours transmis à la fonction dans leur ordre d’apparition dans *function.json*, même si vous ne les spécifiez pas dans votre instruction d’exportation. Par exemple, si vous avez `function(context, a, b)` et le remplacez par `function(context, a)`, vous pouvez toujours obtenir la valeur `b` dans le code de fonction en faisant référence à `arguments[3]`.

Toutes les liaisons, quelle que soit leur direction, sont également transmises sur l’objet `context` (voir ci-dessous).

## Objet de contexte

Le runtime utilise un objet `context` pour transmettre des données vers et à partir de votre fonction et vous permettre de communiquer avec le runtime.

L’objet de contexte est toujours le premier paramètre d’une fonction et doit toujours être inclus, car il possède des méthodes telles que `context.done` et `context.log` nécessaires pour utiliser correctement le runtime. Vous pouvez nommer l’objet comme vous le souhaitez (par exemple, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## context.bindings

L’objet `context.bindings` collecte toutes les données entrantes et sortantes. Les données sont ajoutées à l’objet `context.bindings` par le biais de la propriété `name` de la liaison. Par exemple, étant donné la définition de la liaison suivante dans *function.json*, vous pouvez accéder au contenu de la file d’attente par le biais de `context.bindings.myInput`.

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

La fonction `context.done` indique au runtime que vous avez terminé l’exécution. Il est important d’appeler lorsque vous avez terminé l’exécution de la fonction. Si vous ne le faites pas, le runtime ne saura jamais que votre fonction est terminée.

La fonction `context.done` permet de transmettre une erreur définie par l’utilisateur au runtime, ainsi qu’un conteneur de propriétés qui remplaceront les propriétés de l’objet `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## context.log(message)

La méthode `context.log` permet de sortir les instructions du journal corrélées à des fins de journalisation. Si vous utilisez `console.log`, vos messages ne s’affichent que pour la journalisation au niveau du processus, ce qui n’est pas aussi utile.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

La méthode `context.log` prend en charge le même format de paramètre que celui pris en charge par la [méthode util.format](https://nodejs.org/api/util.html#util_util_format_format) Node. Ainsi, par exemple, le code suivant :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

peut être écrit comme suit :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## Déclencheurs HTTP : context.req and context.res

Dans le cas de déclencheurs HTTP, dans la mesure où il est courant d’utiliser `req` et `res` pour les objets de requête et de réponse HTTP, nous avons décidé de faciliter l’accès à ceux-ci sur l’objet de contexte, au lieu de vous contraindre à utiliser le modèle `context.bindings.name` complet.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## Version du nœud et gestion des packages

La version de Node est actuellement verrouillée sur `5.9.1`. Nous examinons la prise en charge d’autres versions ainsi que le fait de la rendre configurable.

Vous pouvez inclure des packages dans votre fonction en chargeant un fichier *package.json* dans le dossier de votre fonction dans le système de fichiers du conteneur de fonctions. Pour savoir comment charger un fichier, consultez la section **Comment mettre à jour les fichiers du conteneur de fonctions** de la rubrique [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md#fileupdate).

Vous pouvez également utiliser `npm install` dans l’interface de ligne de commande SCM (Kudu) du conteneur de fonctions :

1. Accédez à `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Console de débogage > CMD**.

3. Accédez à `D:\home\site\wwwroot<function_name>`.

4. Exécutez `npm install`.

Une fois les packages nécessaires installés, vous pouvez les importer dans votre fonction de la façon habituelle (c’est-à-dire par le biais de `require('packagename')`).

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## Variables d’environnement

Pour obtenir une variable d’environnement ou une valeur de paramètre d’application, utilisez `process.env`, comme l’illustre l’exemple de code suivant :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## Prise en charge de TypeScript/CoffeeScript

Il n’existe encore aucune prise en charge directe pour l’auto-compilation de TypeScript/CoffeeScript via le runtime, ce qui nécessite une gestion externe au runtime, au moment du déploiement.

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Informations de référence pour les développeurs F# sur Azure Functions](functions-reference-fsharp.md)
* [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->