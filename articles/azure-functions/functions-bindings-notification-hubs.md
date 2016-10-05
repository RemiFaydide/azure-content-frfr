<properties
	pageTitle="Liaison Notification Hubs Azure Functions| Microsoft Azure"
	description="Découvrez comment utiliser les liaisons Azure Notification Hubs dans Azure Functions."
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
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Liaison de sortie Notification Hubs Azure Functions

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Notification Hubs dans Azure Functions.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Vos fonctions peuvent envoyer des notifications Push à l’aide d’un concentrateur de notification Azure configuré avec très peu de lignes de code. Toutefois, le concentrateur de notification doit être configuré pour l’infrastructure Platform Notification System (PNS) que vous souhaitez utiliser. Pour plus d’informations sur la configuration d’un Azure Notification Hub et sur le développement d’applications clientes qui s’inscrivent pour recevoir des notifications, voir [Prise en main de Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) en cliquant sur votre plateforme cliente cible au début de l’article.

## function.json pour les liaisons de sortie Azure Notification Hubs

Le fichier function.json spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour le message du concentrateur de notification.
- `type` : doit être défini sur *"notificationHub"*.
- `tagExpression` : expressions de balise vous permettant de demander que les notifications soient remises à un ensemble d’appareils qui se sont inscrits pour la réception de notifications correspondant à l’expression de balise. Pour plus d’informations, voir [Routage et expressions de balise](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName` : nom de la ressource de concentrateur de notification dans le portail Azure.
- `connection` : cette chaîne de connexion doit correspondre à une chaîne de connexion **Paramètre d’application** définie sur la valeur *DefaultFullSharedAccessSignature* de votre concentrateur de notification.
- `direction` : doit être défini sur *out*.
 
Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

## Configuration de la chaîne de connexion du concentrateur de notification Azure

Pour utiliser une liaison de sortie de concentrateur de notification, vous devez configurer la chaîne de connexion pour le concentrateur. Vous pouvez effectuer cette opération dans l’onglet *Intégrer*. Il vous suffit de sélectionner votre concentrateur de notification ou d’en créer un.

Vous pouvez également ajouter manuellement une chaîne de connexion pour un hub existant en ajoutant cette chaîne pour *DefaultFullSharedAccessSignature* à votre concentrateur de notification. Cette chaîne de connexion fournit l’autorisation d’accès de votre fonction pour l’envoi des messages de notification. La valeur de la chaîne de connexion *DefaultFullSharedAccessSignature* est accessible à partir du bouton **clés** dans le panneau principal de votre ressource Notification Hub, dans le portail Azure. Pour ajouter manuellement une chaîne de connexion pour votre concentrateur, procédez comme suit :

1. Dans le panneau **Conteneur de fonctions** du portail Azure, cliquez sur **Paramètres de conteneur de fonctions > Accéder aux paramètres App Service**.

2. Dans le panneau **Paramètres**, cliquez sur **Paramètres de l’application**.

3. Faites défiler les éléments vers le bas jusqu’à la section **Chaînes de connexion**, puis ajoutez une entrée nommée pour la valeur *DefaultFullSharedAccessSignature*, pour votre concentrateur de notification. Définissez le type sur **Personnalisé**.
4. Référencez votre nom de chaîne de connexion dans les liaisons de sortie. Cette chaîne sera semblable à **MyHubConnectionString** utilisé dans l’exemple ci-dessus.

## Exemple de code de concentrateur de notification Azure pour un déclencheur de minuteur Node.js 

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant `location` et `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

## Exemple de code de concentrateur de notification Azure pour un déclencheur de minuteur F#

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant `location` et `message`.

	let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
	    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]

## Exemple de code de concentrateur de notification Azure pour un déclencheur de file d’attente C#

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient `message` en utilisant une chaîne JSON valide.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

## Exemple de code C# de déclencheur de file d’attente de concentrateur de notification Azure utilisant le type Notification

Cet exemple indique comment utiliser le type `Notification` qui est défini dans la [bibliothèque Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Pour utiliser ce type et la bibliothèque, vous devez charger un fichier *project.json* pour votre conteneur de fonctions. Le fichier project.json est un fichier texte JSON qui ressemblera à celui-ci :

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Pour plus d’informations sur le chargement de votre fichier project.json, voir la [procédure de chargement d’un fichier project.json](functions-reference.md#fileupdate).

Exemple de code :

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->