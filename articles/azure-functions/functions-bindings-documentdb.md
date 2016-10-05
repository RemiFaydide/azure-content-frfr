<properties
	pageTitle="Liaisons DocumentDB Azure Functions | Microsoft Azure"
	description="Découvrez comment utiliser des liaisons Azure DocumentDB dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Liaisons DocumentDB Azure Functions

[AZURE.INCLUDE [functions-selector-bindings (liaisons de sélecteur de fonctions)](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Azure DocumentDB dans Azure Functions.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a> Liaison d’entrée Azure DocumentDB

Les liaisons d’entrée peuvent charger un document à partir d’une collection DocumentDB et le transmettre directement à votre liaison. L’ID du document peut être déterminé en fonction du déclencheur qui a appelé la fonction. Dans une fonction C#, toutes les modifications apportées à l’enregistrement sont automatiquement renvoyées à la collection une fois que la fonction s’est correctement terminée.

#### function.json pour les liaisons d’entrée DocumentDB

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour le document.
- `type` : doit être défini sur « documentdb ».
- `databaseName` : base de données contenant le document.
- `collectionName` : collection contenant le document.
- `id` : ID du document à récupérer. Cette propriété prend en charge les liaisons semblables à "{queueTrigger}", qui utiliseront la valeur de chaîne du message de file d’attente en tant qu’ID du document.
- `connection` : cette chaîne doit correspondre à un Paramètre d’application défini sur le point de terminaison de votre compte DocumentDB. Si vous choisissez votre compte dans l’onglet Intégrer, un nouveau paramètre d’application est créé à votre intention avec un nom indiqué sous la forme VotreCompte\_DOCUMENTDB. Si vous devez créer le paramètre d’application manuellement, la chaîne de connexion réelle doit se présenter sous la forme suivante : AccountEndpoint=<Point de terminaison pour votre compte>;AccountKey=<Votre clé d’accès principale>;.
- direction : doit être défini sur *« in »*.

Exemple de fichier *function.json* :
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemple de code d’entrée Azure DocumentDB pour un déclencheur de file d’attente C#
 
En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée DocumentDB récupérera le document dont l’ID correspond à la chaîne de message de file d’attente et le transmettra au paramètre "document". Si ce document est introuvable, le paramètre "document" présentera la valeur Null. Le document sera ensuite mis à jour avec la nouvelle valeur de texte une fois la fonction terminée.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Exemple de code d’entrée Azure DocumentDB pour un déclencheur de file d’attente F#

En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée DocumentDB récupérera le document dont l’ID correspond à la chaîne de message de file d’attente et le transmettra au paramètre "document". Si ce document est introuvable, le paramètre "document" présentera la valeur Null. Le document sera ensuite mis à jour avec la nouvelle valeur de texte une fois la fonction terminée.

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- "This has changed."

Vous avez besoin d’un fichier `project.json` qui utilise NuGet pour spécifier les packages `FSharp.Interop.Dynamic` et `Dynamitey` en tant que dépendances de packages, comme suit :

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

Ce code utilisera NuGet pour extraire vos dépendances et les référencera dans votre script.

#### Exemple de code d’entrée Azure DocumentDB pour un déclencheur de file d’attente Node.js
 
En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée DocumentDB récupérera le document dont l’ID correspond à la chaîne de message de file d’attente, et le transmettra à la propriété de liaison `documentIn`. Dans les fonctions Node.js, les documents mis à jour ne sont pas renvoyés à la collection. Toutefois, vous pouvez transmettre directement la liaison d’entrée à une liaison de sortie DocumentDB nommée `documentOut` pour prendre en charge les mises à jour. Cet exemple de code met à jour la propriété de texte du document d’entrée et définit cet élément comme document de sortie.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

## <a id="docdboutput"></a> Liaisons de sortie Azure DocumentDB

Vos fonctions peuvent écrire des documents JSON dans une base de données Azure DocumentDB à l’aide de la liaison de sortie **Document Azure DocumentDB**. Pour plus d’informations sur Azure DocumentDB, consultez l’article [Présentation de DocumentDB](../documentdb/documentdb-introduction.md) et le [didacticiel de prise en main](../documentdb/documentdb-get-started.md).

#### function.json pour les liaisons de sortie DocumentDB

Le fichier function.json spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour le nouveau document.
- `type` : doit être défini sur *« documentdb »*.
- `databaseName` : base de données contenant la collection dans laquelle le nouveau document sera créé.
- `collectionName` : collection dans laquelle le nouveau document sera créé.
- `createIfNotExists` : valeur booléenne indiquant si la collection sera ou non créée si elle n’existe pas. La valeur par défaut est *false*. Cette configuration est due au fait que les collections sont créées avec un débit réservé, ce qui a des conséquences sur le plan tarifaire. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection` : cette chaîne doit correspondre à un **Paramètre d’application** défini sur le point de terminaison de votre compte DocumentDB. Si vous choisissez votre compte dans l’onglet **Intégrer**, un nouveau paramètre d’application est créé avec un nom au format suivant : `yourAccount_DOCUMENTDB`. Si vous devez créer le paramètre d’application manuellement, la chaîne de connexion réelle doit se présenter sous la forme suivante : `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`.
- `direction` : doit être défini sur *« out »*.
 
Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Exemple de code de sortie Azure DocumentDB pour un déclencheur de file d’attente Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

Document de sortie :

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Exemple de code de sortie Azure DocumentDB pour un déclencheur de file d’attente F#

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### Exemple de code de sortie Azure DocumentDB pour un déclencheur de file d’attente C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Exemple de code de sortie Azure DocumentDB définissant le nom du fichier

Si vous voulez spécifier le nom du document dans la fonction, définissez simplement la valeur `id`. Par exemple, si le contenu JSON d’un employé, tel que celui ci-dessous, a été supprimé dans la file d’attente :

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Vous pourriez utiliser le code C# suivant dans une fonction de déclencheur de file d’attente :
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Ou le code F # équivalent :

	open FSharp.Interop.Dynamic
	open Newtonsoft.Json

	type Employee = {
	    id: string
	    name: string
	    employeeId: string
	    address: string
	}

	let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
	    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
	    let employee = JObject.Parse(myQueueItem)
	    employeeDocument <-
	        { id = sprintf "%s-%s" employee?name employee?employeeId
	          name = employee?name
	          employeeId = employee?id
	          address = employee?address }

Exemple de sortie :

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->