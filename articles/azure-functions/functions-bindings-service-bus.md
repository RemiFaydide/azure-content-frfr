<properties
	pageTitle="Déclencheurs et liaisons Service Bus Azure Functions | Microsoft Azure"
	description="Découvrez comment utiliser des déclencheurs et des liaisons Azure Service Bus dans Azure Functions."
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

# Déclencheurs et liaisons Service Bus Azure Functions pour les files d’attente et les rubriques

[AZURE.INCLUDE [functions-selector-bindings (liaisons de sélecteur de fonctions)](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des déclencheurs et des liaisons Azure Service Bus dans Azure Functions.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a> Déclencheur de file d’attente ou de rubrique Service Bus

#### function.json

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour la file d’attente ou la rubrique, ou pour le message de file d’attente ou de rubrique.
- `queueName` : pour le déclencheur de file d’attente uniquement, le nom de la file d’attente à interroger.
- `topicName` : pour le déclencheur de rubrique uniquement, le nom de la rubrique à interroger.
- `subscriptionName` : pour le déclencheur de rubrique uniquement, le nom de l’abonnement.
- `connection` : le nom d’un paramètre d’application contenant une chaîne de connexion Service Bus. La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique. Si la chaîne de connexion n’a pas de droits de gestion, définissez la propriété `accessRights`. Si vous laissez `connection` vide, le déclencheur ou la liaison fonctionnera avec la chaîne de connexion de Service Bus par défaut pour le conteneur de fonctions, qui est spécifiée par le paramètre d’application AzureWebJobsServiceBus.
- `accessRights` : spécifie les droits d’accès disponibles pour la chaîne de connexion. La valeur par défaut est `manage`. Définissez la valeur sur `listen` si vous utilisez une chaîne de connexion qui ne fournit pas d’autorisations de gestion. Sinon, le runtime Functions pourrait essayer et faire échouer les opérations qui nécessitent des droits de gestion.
- `type` : doit être défini sur *serviceBusTrigger*.
- `direction` : doit être défini sur *in*.

Exemple *function.json* pour un déclencheur de file d’attente Service Bus :

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Exemple de code C# qui traite un message de file d’attente Service Bus

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Exemple de code F# qui traite un message de file d’attente Service Bus

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### Exemple de code Node.js qui traite un message de file d’attente Service Bus

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### Types pris en charge

Le message de file d’attente Service Bus peut être désérialisé vers l’un des types suivants :

* Objet (de JSON)
* string
* tableau d’octets
* `BrokeredMessage` (C#)

#### <a id="sbpeeklock"></a> Comportement de PeekLock

Le runtime Functions reçoit un message en mode `PeekLock` et appelle `Complete` sur le message si la fonction se termine correctement. Si la fonction échoue, il appelle `Abandon`. Si la fonction s’exécute au-delà du délai imparti à `PeekLock`, le verrou est automatiquement renouvelé.

#### <a id="sbpoison"></a> Gestion des messages incohérents

Service Bus assure sa propre gestion des messages incohérents. Il est impossible de la contrôler ou de la configurer dans la configuration ou le code d’Azure Functions.

#### <a id="sbsinglethread"></a> Monothread

Par défaut, le runtime Functions traite plusieurs messages de file d’attente simultanément. Pour que le runtime ne traite qu’un message de file d’attente ou de rubrique à la fois, définissez `serviceBus.maxConcurrrentCalls` sur 1 dans le fichier *host.json*. Pour plus d’informations sur le fichier *host.json*, consultez [Structure de dossiers](functions-reference.md#folder-structure) dans l’article de référence pour développeurs, et [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) dans le Wiki du référentiel de WebJobs.Script.

## <a id="sboutput"></a> Liaison de sortie de file d’attente ou de rubrique Service Bus

#### function.json

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour la file d’attente ou le message de file d’attente.
- `queueName` : pour le déclencheur de file d’attente uniquement, le nom de la file d’attente à interroger.
- `topicName` : pour le déclencheur de rubrique uniquement, le nom de la rubrique à interroger.
- `subscriptionName` : pour le déclencheur de rubrique uniquement, le nom de l’abonnement.
- `connection` : identique au déclencheur Service Bus.
- `accessRights` : spécifie les droits d’accès disponibles pour la chaîne de connexion. La valeur par défaut est `manage`. Définissez la valeur sur `send` si vous utilisez une chaîne de connexion qui ne fournit pas d’autorisations de gestion. Sinon, le runtime Functions pourrait essayer et faire échouer les opérations qui nécessitent des droits de gestion, comme la création de files d’attente.
- `type` : doit être défini sur *serviceBus*.
- `direction` : doit être défini sur *out*.

Exemple *function.json* pour l’utilisation d’un déclencheur de minuteur afin d’écrire des messages de file d’attente Service Bus :

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Types pris en charge

Azure Functions peut créer un message de file d’attente Service Bus à partir d’un des types suivants.

* Objet (crée toujours un message JSON, crée le message avec un objet null si la valeur est null lorsque la fonction se termine)
* chaîne (crée un message si la valeur du paramètre n’est pas null lorsque la fonction se termine)
* tableau d’octets (fonctionne comme le type string)
* `BrokeredMessage` (C#, fonctionne comme le type string)

Pour créer plusieurs messages dans une fonction C#, vous pouvez utiliser `ICollector<T>` ou `IAsyncCollector<T>`. Un message est créé quand vous appelez la méthode `Add`.

#### Exemples de code C# créant des messages de file d’attente Service Bus

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Exemple de code F# qui crée un message de file d’attente Service Bus

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### Exemple de code Node.js qui crée un message de file d’attente Service Bus

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->