<properties
	pageTitle="Liaisons de concentrateur d’événements Azure Functions | Microsoft Azure"
	description="Découvrez comment utiliser des liaisons Azure Event Hub dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
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
	ms.author="wesmc"/>

# Liaisons de concentrateur d’événements Azure Functions

[AZURE.INCLUDE [functions-selector-bindings (liaisons de sélecteur de fonctions)](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons de [concentrateur d’événements Azure (Azure Event Hub)](../event-hubs/event-hubs-overview.md) pour Azure Functions. Azure Functions prend en charge des liaisons de déclencheur et de sortie pour des Azure Event Hubs.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## Liaison de déclencheur Azure Event Hub

Un déclencheur Azure Event Hub permet de répondre à un événement envoyé à un flux d’événements d’un concentrateur d’événements. Vous devez disposer de l’accès en lecture au concentrateur d’événements pour configurer une liaison de déclencheur.

#### function.json pour la liaison de déclencheur de concentrateur d’événements

Le fichier *function.json* pour un déclencheur Azure Event Hub spécifie les propriétés suivantes :

- `type` : doit être défini sur *eventHubTrigger*.
- `name` : nom de variable utilisé dans le code de fonction pour le message de concentrateur d’événements.
- `direction` : propriété devant être définie sur *in*.
- `path` : nom du concentrateur d’événements.
- `connection` : nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms dans lequel réside le concentrateur d’événements. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour l’espace de noms, et non pour le concentrateur d’événements lui-même. Cette chaîne de connexion doit avoir au moins des droits de lecture pour activer le déclencheur.

		{
		  "bindings": [
		    {
		      "type": "eventHubTrigger",
		      "name": "myEventHubMessage",
		      "direction": "in",
		      "path": "MyEventHub",
		      "connection": "myEventHubReadConnectionString"
		    }
		  ],
		  "disabled": false
		}

#### Exemple C# de déclencheur Azure Event Hub
 
En cas d’utilisation de l’exemple function.json exemple, le corps du message d’événement est journalisé à l’aide du code de fonction c# ci-dessous :
 
	using System;
	
	public static void Run(string myEventHubMessage, TraceWriter log)
	{
	    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
	}

#### Exemple F# de déclencheur Azure Event Hub

En cas d’utilisation de l’exemple function.json exemple, le corps du message d’événement est journalisé à l’aide du code de fonction F# ci-dessous :

	let Run(myEventHubMessage: string, log: TraceWriter) =
	    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### Exemple de Node.js de déclencheur Azure Event Hub
 
En cas d’utilisation de l’exemple function.json exemple, le corps du message d’événement est journalisé à l’aide du code de fonction Node.js ci-dessous :
 
	module.exports = function (context, myEventHubMessage) {
	    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);	
	    context.done();
	};


## Liaison de sortie du concentrateur d’événements Azure (Azure Event Hub)

Une liaison de sortie du concentrateur d’événements Azure est utilisée pour écrire des événements dans un flux d’événements du concentrateur d’événements. Vous devez disposer de l’autorisation d’envoi à un concentrateur d’événements pour y écrire les événements.

#### function.json pour la liaison de sortie de concentrateur d’événements

Le fichier *function.json* pour une liaison de déclencheur Azure Event Hub spécifie les propriétés suivantes :

- `type` : doit être défini sur *eventHub*.
- `name` : nom de variable utilisé dans le code de fonction pour le message de concentrateur d’événements.
- `path` : nom du concentrateur d’événements.
- `connection` : nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms dans lequel réside le concentrateur d’événements. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour l’espace de noms, et non pour le concentrateur d’événements lui-même. Cette chaîne de connexion doit disposer d’autorisations d’envoi pour envoyer le message au flux du concentrateur d’événements.
- `direction` : doit être défini sur *out*.

	    {
	      "type": "eventHub",
	      "name": "outputEventHubMessage",
	      "path": "myeventhub",
	      "connection": "MyEventHubSend",
	      "direction": "out"
	    }


#### Exemple de code C# Azure Event Hub pour une liaison de sortie
 
L’exemple de code de fonction C# suivant illustre l’écriture d’un événement dans un flux d’événements de concentrateur d’événements. Cet exemple représente la liaison de sortie du concentrateur d’événements illustrée ci-dessus appliquée à un déclencheur C# de type Timer.
 
	using System;
	
	public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
	{
	    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
	
	    log.Verbose(msg);   
	    
	    outputEventHubMessage = msg;
	}

#### Exemple de code F# Azure Event Hub pour une liaison de sortie

L’exemple de code de fonction F# suivant illustre l’écriture d’un événement dans un flux d’événements de concentrateur d’événements. Cet exemple représente la liaison de sortie du concentrateur d’événements illustrée ci-dessus appliquée à un déclencheur C# de type Timer.

	let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
	    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
	    log.Verbose(msg);
	    outputEventHubMessage <- msg;

#### Exemple de code Node.js Azure Event Hub pour une liaison de sortie
 
L’exemple de code de fonction Node.js suivant illustre l’écriture d’un événement dans un flux d’événements de concentrateur d’événements. Cet exemple représente la liaison de sortie du concentrateur d’événements illustrée ci-dessus appliquée à un déclencheur Node.js de type Timer.
 
	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	    
	    if(myTimer.isPastDue)
	    {
	        context.log('TimerTriggerNodeJS1 is running late!');
	    }

	    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
	    
	    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
	
	    context.done();
	};

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->