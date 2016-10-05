<properties
	pageTitle="Liaisons HTTP et webhook Azure Functions | Microsoft Azure"
	description="Découvrez comment utiliser des déclencheurs et des liaisons HTTP et webhook dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Liaisons HTTP et webhook Azure Functions

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des déclencheurs et des liaisons HTTP et webhook dans Azure Functions.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json pour les liaisons HTTP et webhook

Le fichier *function.json* fournit des propriétés qui se rapportent à la fois à la requête et à la réponse.

Propriétés de la requête HTTP :

- `name` : nom de variable utilisé dans le code de fonction pour l’objet de requête (ou pour le corps de la requête dans le cas des fonctions Node.js).
- `type` : doit être défini sur *httpTrigger*.
- `direction` : doit être défini sur *in*.
- `webHookType` : pour les déclencheurs webhook, les valeurs valides sont *github*, *slack* et *genericJson*. Pour un déclencheur HTTP autre qu’un WebHook, définissez cette propriété sur une chaîne vide. Pour plus d’informations sur les webhooks, voir [Déclencheurs webhook](#webhook-triggers) ci-après.
- `authLevel` : ne s’applique pas aux déclencheurs webhook. Définissez cette propriété sur « function » pour demander la clé API, sur « anonymous » pour annuler l’exigence de clé API ou sur « admin » pour exiger la clé API principale. Pour plus d’informations, voir [Clés API](#apikeys) ci-dessous.

Propriétés de la réponse HTTP :

- `name` : nom de variable utilisé dans le code de fonction pour l’objet de réponse.
- `type` : doit être défini sur *http*.
- `direction` : doit être défini sur *out*.
 
Exemple de fichier *function.json* :

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## Déclencheurs WebHook

Un déclencheur WebHook est un déclencheur HTTP qui présente les caractéristiques ci-après conçues pour les WebHooks :

* Pour les fournisseurs de WebHooks spécifiques (les fournisseurs actuellement pris en charge sont GitHub et Slack), le runtime Functions valide la signature du fournisseur.
* Dans le cas des fonctions Node.js, le runtime Functions fournit le corps de requête plutôt que l’objet de requête. Les fonctions C# ne font l’objet d’aucune gestion spéciale, car vous contrôlez les éléments fournis en spécifiant le type de paramètre. Si vous spécifiez `HttpRequestMessage`, vous obtenez l’objet de requête. Si vous spécifiez un type POCO, le runtime Functions essaie d’analyser un objet JSON dans le corps de la requête pour spécifier les propriétés de l’objet.
* Pour déclencher une fonction WebHook, la requête HTTP doit inclure une clé API. Pour les déclencheurs HTTP non-WebHook, cette exigence est facultative.

Pour plus d’informations sur la configuration d’un webhook GitHub, voir [GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (page « Création de webhooks » du site GitHub Developer).

## URL pour déclencher la fonction

Pour déclencher une fonction, vous devez envoyer une requête HTTP à une URL constituée de l’URL du conteneur de fonctions et du nom de la fonction :

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## Clés API

Par défaut, une clé API doit être fournie avec une requête HTTP pour déclencher une fonction HTTP ou WebHook. Cette clé peut être incluse dans une variable de chaîne de requête nommée `code` ou dans un en-tête HTTP `x-functions-key`. Pour les fonctions non-WebHook, vous pouvez indiquer qu’aucune clé API n’est requise en définissant la propriété `authLevel` sur "anonymous" dans le fichier *function.json*.

Les valeurs de clé API figurent dans le dossier *D:\\home\\data\\Functions\\secrets* du système de fichiers du conteneur de fonctions. La clé principale et la clé de fonction sont définies dans le fichier *host.json*, comme illustré dans cet exemple.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La clé de fonction de *host.json* est utilisable pour déclencher n’importe quelle fonction, à l’exception de celles qui sont désactivées. En revanche, la clé principale permet de déclencher tous les types de fonctions, même les fonctions désactivées. Vous pouvez configurer une fonction pour qu’elle exige la clé principale en définissant la propriété `authLevel` sur "admin".

Si le dossier *secrets* contient un fichier JSON portant le même nom qu’une fonction, la propriété `key` définie dans ce fichier est également utilisable pour déclencher la fonction, et cette clé ne fonctionnera qu’avec la fonction à laquelle elle se réfère. Par exemple, la clé API relative à une fonction nommée `HttpTrigger` est spécifiée dans le fichier *HttpTrigger.json* du dossier *secrets*. Voici un exemple :

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Lorsque vous configurez un déclencheur WebHook, ne partagez pas la clé principale avec le fournisseur de WebHooks. Utilisez une clé qui ne fonctionnera qu’avec la fonction traitant le WebHook. La clé principale permet de déclencher tous les types de fonctions, y compris les fonctions désactivées.

## Exemple de code C# pour une fonction de déclencheur HTTP 

Cet exemple de code recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## Exemple de code F# pour une fonction de déclencheur HTTP

Cet exemple de code recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Vous avez besoin d’un fichier `project.json` qui utilise NuGet pour référencer les assemblys `FSharp.Interop.Dynamic` et `Dynamitey`, comme suit :

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Ce code utilisera NuGet pour extraire vos dépendances et les référencera dans votre script.

## Exemple de code Node.js pour une fonction de déclencheur HTTP 

Cet exemple de code recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## Exemple de code C# pour une fonction WebHook GitHub 

Cet exemple de code enregistre les commentaires relatifs aux problèmes GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## Exemple de code F# pour une fonction WebHook GitHub

Cet exemple de code enregistre les commentaires relatifs aux problèmes GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## Exemple de code Node.js pour une fonction WebHook GitHub 

Cet exemple de code enregistre les commentaires relatifs aux problèmes GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->