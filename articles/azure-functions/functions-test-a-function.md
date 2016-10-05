<properties
   pageTitle="Test des fonctions Azure | Microsoft Azure"
   description="Testez vos fonctions Azure à l’aide de Postman, cURL et Node.js."
   services="functions"
   documentationCenter="na"
   authors="wesmc7777"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, test"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="wesmc"/>

# Test des fonctions Azure

## Vue d'ensemble

Dans ce didacticiel, nous allons vous présenter différents principes de test des fonctions. Nous allons définir une fonction de déclenchement http qui accepte les entrées via un paramètre de chaîne de requête ou le corps de la requête. Le code du modèle **HttpTrigger Node.js Function** prend en charge un paramètre de chaîne de requête `name`. Nous allons également ajouter un code pour prendre en charge ce paramètre en même temps que des informations `address` pour l’utilisateur dans le corps de la requête.

## Créer une fonction de test

Dans ce didacticiel, nous allons utiliser principalement une version légèrement modifiée du modèle **HttpTrigger Nodejs Function** disponible lors de la création d’une fonction. Si vous avez besoin d’aide pour créer une fonction, vous pouvez consulter le [didacticiel Create your first Azure Function](functions-create-first-azure-function.md) (Créer votre première fonction Azure). Sélectionnez simplement le modèle **HttpTrigger Nodejs Function** lors de la création de la fonction de test dans le [Portail Azure].

Le modèle de fonction par défaut est essentiellement une fonction hello world qui renvoie le nom à partir du corps de la requête ou du paramètre de chaîne de requête, `name=<your name>`. Nous allons mettre à jour le code pour vous permettre également de fournir le nom et l’adresse en tant que contenu JSON dans le corps de la requête. La fonction les renverra vers le client une fois disponibles.

Mettez à jour la fonction avec le code suivant que nous allons utiliser pour le test :

	module.exports = function(context, req) {
	    context.log("Node.js HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
	    context.log("Request Headers = " + JSON.stringify(req.headers));    
	
	    if (req.query.name || (req.body && req.body.name)) {
	        if (typeof req.query.name != "undefined") {
	            context.log("Name was provided as a query string param..."); 
	            ProcessNewUserInformation(context, req.query.name);
	        }
	        else {
	            context.log("Processing user info from request body..."); 
	            ProcessNewUserInformation(context, req.body.name, req.body.address);
	        }
	    }
	    else {
	        context.res = {
	            status: 400,
	            body: "Please pass a name on the query string or in the request body"
	        };
	    }
	    context.done();
	};
	
	function ProcessNewUserInformation(context, name, address)
	{    
	    context.log("Processing User Information...");            
	    context.log("name = " + name);            
	    echoString = "Hello " + name;
	    
	    if (typeof address != "undefined")
	    {
	        echoString += "\n" + "The address you provided is " + address;
	        context.log("address = " + address);            
	    }
	    
	    context.res = {
	            // status: 200, /* Defaults to 200 */
	            body: echoString
	        };
	}


## Test d’une fonction avec des outils

### Test avec cURL

Souvent, lorsque vous testez des logiciels, la ligne de commande suffit pour déboguer votre application. Il en est de même pour les fonctions.

Pour tester la fonction ci-dessus, copiez **l’URL de la fonction** à partir du portail. Elle aura le format suivant :

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
Il s’agit de l’URL qui déclenche votre fonction. Nous pouvons tester cela à l’aide de la commande cURL sur la ligne de commande pour exécuter une requête Get (`-G` ou `--get`) sur notre fonction :

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
Cet exemple particulier exige un paramètre de chaîne de requête qui peut être transmis en tant que données (`-d`) dans la commande cURL :

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>
    
Appuyez sur Entrée. La sortie de la fonction s’affiche sur la ligne de commande.

![](./media/functions-test-a-function/curl-test.png)

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### Test avec un navigateur

Les fonctions qui ne nécessitent pas de paramètres, ou seulement des paramètres de chaîne de requête, peuvent être testées à l’aide d’un navigateur.

Pour tester la fonction que nous avons définie ci-dessus, copiez l’**URL de la fonction** à partir du portail. Elle aura le format suivant :

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Ajoutez le paramètre de chaîne de requête `name` comme suit, en utilisant un nom réel pour l’espace réservé `<Enter a name here>`.

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Collez l’URL dans votre navigateur. Vous devriez obtenir une réponse similaire à la suivante.

![](./media/functions-test-a-function/browser-test.png)

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

	2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
	2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
	2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Wes from a browser
	2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"fr-FR"}
	2016-03-23T07:35:10.338 Name was provided as a query string param.
	2016-03-23T07:35:10.338 Processing User Information...
	2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### Test avec Postman

L’outil recommandé pour tester la plupart de vos fonctions est Postman. Pour installer Postman, consultez [Get Postman](https://www.getpostman.com/). Postman permet de contrôler beaucoup plus d’attributs d’une requête HTTP.

> [AZURE.TIP] Utilisez le Client REST qui vous est le plus familier. Les solutions alternatives à Postman sont les suivantes :
> 
> * [Fiddler](http://www.telerik.com/fiddler)
> * [Paw](https://luckymarmot.com/paw)

Pour tester la fonction avec un corps de requête dans Postman :

1. Lancez Postman à partir du bouton **Applications** dans le coin supérieur gauche d’une fenêtre de navigateur Chrome.
2. Copiez votre **URL de fonction Url** et collez-la dans Postman. Elle inclut le paramètre de chaîne de requête de code d’accès.
3. Changez la méthode HTTP en **POST**.
4. Cliquez sur **Body** > **raw** et ajoutez un corps de requête JSON similaire au suivant :

		{
		    "name" : "Wes testing with Postman",
		    "address" : "Seattle, W.A. 98101"
		}

5. Cliquez sur **Envoyer**.

L’illustration suivante montre un exemple de test de fonction d’écho simple dans ce didacticiel.

![](./media/functions-test-a-function/postman-test.png)

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

	2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
	2016-03-23T08:04:57.763 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
	2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"fr-FR"}
	2016-03-23T08:04:57.763 Processing user info from request body...
	2016-03-23T08:04:57.763 Processing User Information...
	2016-03-23T08:04:57.763 name = Wes testing with Postman
	2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
	2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    
### Test d’un déclencheur d’objet blob à l’aide de l’Explorateur de stockage

Vous pouvez tester une fonction de déclenchement d’objet blob à l’aide de l’[Explorateur de stockage Microsoft Azure](http://storageexplorer.com/).

1. Dans le [portail Azure] de votre application Functions, créez une nouvelle fonction de déclenchement d’objet blob C#, F# ou Node. Définissez le chemin d’accès à surveiller sur le nom de votre conteneur d’objets blob. Par exemple :

		files

2. Cliquez sur le bouton **+** pour sélectionner ou créer le compte de stockage que vous souhaitez utiliser. Cliquez ensuite sur **Créer**.

3. Créez un fichier texte contenant le texte suivant et enregistrez-le :

		A text file for blob trigger function testing.

4. Exécutez l’[Explorateur de stockage Microsoft Azure](http://storageexplorer.com/) et connectez-vous au conteneur d’objets blob dans le compte de stockage surveillé.

5. Cliquez sur le bouton **Télécharger** et téléchargez le fichier texte.

	![](./media/functions-test-a-function/azure-storage-explorer-test.png)


	Le code de la fonction de déclenchement d’objets blob par défaut consigne le traitement de l’objet blob dans les journaux :

		2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
		2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
		2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
		2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## Test d’une fonction au sein de fonctions

### Test avec le bouton d’exécution du portail de fonctions

Le portail fournit un bouton **Exécuter** qui vous permet d’effectuer des tests limités. Vous pouvez fournir un corps de requête à l’aide du bouton d’exécution mais vous ne pouvez pas fournir des paramètres de chaîne de requête ou mettre à jour des en-têtes de demande.

Testez la fonction de déclenchement HTTP que nous avons créée précédemment en ajoutant une chaîne JSON similaire à la suivante dans le champ **Corps de requête**, puis cliquez sur le bouton **Exécuter**.

	{
		"name" : "Wes testing Run button",
		"address" : "USA"
	} 

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

	2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
	2016-03-23T08:03:18.697 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
	2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"fr-FR"}
	2016-03-23T08:03:18.697 Processing user info from request body...
	2016-03-23T08:03:18.697 Processing User Information...
	2016-03-23T08:03:18.697 name = Wes testing Run button
	2016-03-23T08:03:18.697 address = USA
	2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### Test avec un déclencheur de minuteur

Certaines fonctions ne peuvent pas être véritablement testées avec les outils mentionnés précédemment. Par exemple, une fonction de déclenchement de file d’attente qui s’exécute lorsqu’un message est déposé dans le [stockage de files d’attente Azure](../storage/storage-dotnet-how-to-use-queues.md). Vous pouvez toujours écrire du code pour déposer un message dans votre file d’attente. Vous trouverez ci-dessous un exemple appliqué à un projet de console. Toutefois, une autre approche vous permet de tester des fonctions directement.

Vous pouvez utiliser un déclencheur de minuteur configuré avec une liaison de sortie de file d’attente. Ce code de déclenchement du minuteur peut ensuite écrire des messages test dans la file d’attente. Cette section détaille un exemple.

Pour des informations plus détaillées sur l’utilisation des liaisons avec les fonctions Azure, consultez la rubrique [Azure Functions developer reference](functions-reference.md) (Référence pour les développeurs Azure Functions).


#### Créer un déclencheur de file d’attente pour le test

Pour illustrer cette approche, nous allons d’abord créer une fonction de déclenchement de file d’attente que nous voulons tester pour une file d’attente nommée `queue-newusers`. Cette fonction traite les informations de nom et d’adresse d’un nouvel utilisateur déposées dans le stockage de file d’attente Azure.

> [AZURE.NOTE] Si vous utilisez un nom de file d’attente différent, assurez-vous que le nom que vous utilisez est conforme aux règles d’[affectation de noms pour les files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx). Sinon, vous obtiendrez un code d’état HTTP 400 : Requête incorrecte.

1. Dans le [Portail Azure] de votre application Functions, cliquez sur **Nouvelle fonction** > **QueueTrigger - C#**.
2. Entrez le nom de file d’attente que la fonction de file d’attente doit surveiller.

		queue-newusers 

3. Cliquez sur le bouton **+** (ajouter) pour sélectionner ou créer le compte de stockage que vous souhaitez utiliser. Cliquez ensuite sur **Créer**.
4. Laissez cette fenêtre de navigateur du portail ouverte afin de surveiller les entrées de journal du code de modèle de fonction de file d’attente par défaut.


#### Créer un déclencheur de minuteur pour déposer un message dans la file d’attente

1. Ouvrez le [Portail Azure] dans une nouvelle fenêtre de navigateur et accédez à votre application Functions.
2. Cliquez sur **Nouvelle fonction** > **TimerTrigger - C#**. Entrez une expression cron pour définir la fréquence à laquelle le code du minuteur exécutera le test de votre fonction de file d’attente. Cliquez ensuite sur **Créer**. Si vous souhaitez que le test s’exécute toutes les 30 secondes, vous pouvez utiliser l’[expression CRON](https://wikipedia.org/wiki/Cron#CRON_expression) suivante :

		*/30 * * * * *


2. Cliquez sur l’onglet **Intégrer** pour votre nouveau déclencheur de minuteur.
3. Sous **Sortie**, cliquez sur le bouton **+ Nouvelle sortie**. Puis cliquez sur **file d’attente** et sur le bouton **Sélectionner**.
4. Notez le nom que vous utilisez pour l’**objet de message de file d’attente**. Vous l’utiliserez dans le code de la fonction de minuteur.

		myQueue

4. Entrez le nom de la file d’attente vers laquelle le message sera envoyé :

		queue-newusers 

3. Cliquez sur le bouton **+** (ajouter) pour sélectionner le compte de stockage que vous avez utilisé précédemment avec le déclencheur de file d’attente. Cliquez ensuite sur **Enregistrer**.
4. Cliquez sur l’onglet **Développer** de votre déclencheur de minuteur.
5. Vous pouvez utiliser le code suivant pour la fonction de minuteur C# dans la mesure où vous avez utilisé le même nom d’objet de message de file d’attente que celui illustré ci-dessus. Cliquez ensuite sur **Enregistrer**.

		using System;
		
		public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
		{
		    String newUser = 
		    "{"name":"User testing from C# timer function","address":"XYZ"}";
		
		    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
		    log.Verbose($"{newUser}");   
		    
		    myQueue = newUser;
		}

À ce stade, la fonction de minuteur C# s’exécutera toutes les 30 secondes si vous avez utilisé l’exemple d’expression cron. Les journaux de la fonction de minuteur consignent chaque exécution :

	2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
	2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
	2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

Dans la fenêtre du navigateur de la fonction de file d’attente, vous verrez chaque message traité :

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	
## Test d’une fonction avec un code

### Test d’une fonction de déclenchement HTTP avec un code Node.js

Vous pouvez utiliser un code Node.js pour exécuter une requête http afin de tester votre fonction Azure.

Vous devez définir :

- Le paramètre `host` dans les options de requête sur l’hôte de votre application Fonction
- Votre nom de fonction dans le paramètre `path`.
- Votre code d’accès (`<your code>`) dans le paramètre `path`.

Exemple de code :

	var http = require("http");
	
	var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";
	
	var nameBodyJSON = {
	    name : "Wes testing with Node.JS code",
	    address : "Dallas, T.X. 75201"
	};
	
	var bodyString = JSON.stringify(nameBodyJSON);
	
	var options = {
	  host: "functions841def78.azurewebsites.net",
	  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
	  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
	  method: "POST",
	  headers : {
	      "Content-Type":"application/json",
	      "Content-Length": Buffer.byteLength(bodyString)
	    }    
	};
	
	callback = function(response) {
	  var str = ""
	  response.on("data", function (chunk) {
	    str += chunk;
	  });
	
	  response.on("end", function () {
	    console.log(str);
	  });
	}
	
	var req = http.request(options, callback);
	console.log("*** Sending name and address in body ***");
	console.log(bodyString);
	req.end(bodyString);



Output:

	C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
	*** Sending name and address in body ***
	{"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
	Hello Wes testing with Node.JS code
	The address you provided is Dallas, T.X. 75201
		
Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

	2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
	2016-03-23T08:09:01.153 Node.js HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
	2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
	2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
	2016-03-23T08:09:01.153 Request Body Type = object
	2016-03-23T08:09:01.153 Request Body = [object Object]
	2016-03-23T08:09:01.153 Processing User Information...
	2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    

### Test d’une fonction de déclenchement de file d’attente avec un code C# #

Nous avons mentionné plus tôt que vous pouvez tester un déclencheur de file d’attente à l’aide d’un code pour déposer un message dans votre file d’attente. L’exemple de code suivant est basé sur le code C# présenté dans le didacticiel [Prise en main du stockage de files d’attente Azure](../storage/storage-dotnet-how-to-use-queues.md). Le code des autres langages est également disponible via ce lien.

Pour tester ce code dans une application de console, vous devez :

- [Configurer votre chaîne de connexion du stockage dans le fichier app.config](../storage/storage-dotnet-how-to-use-queues.md#setup-a-storage-connection-string).
- Ce code accepte le nom et l’adresse d’un nouvel utilisateur en tant qu’arguments de ligne de commande pendant l’exécution. Transmettez les paramètres `name` et `address` à l’application. Par exemple, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`


Exemple de code C# :

    static void Main(string[] args)
    {
        string name = null;
        string address = null;
        string queueName = "queue-newusers";
        string JSON = null;

        if (args.Length > 0)
        {
            name = args[0];
        }
        if (args.Length > 1)
        {
            address = args[1];
        }

        // Retrieve storage account from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

        // Create the queue client
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

        // Retrieve a reference to a queue
        CloudQueue queue = queueClient.GetQueueReference(queueName);

        // Create the queue if it doesn't already exist
        queue.CreateIfNotExists();

        // Create a message and add it to the queue.
        if (name != null)
        {
            if (address != null)
                JSON = String.Format("{{"name":"{0}","address":"{1}"}}", name, address);
            else
                JSON = String.Format("{{"name":"{0}"}}", name);
        }

        Console.WriteLine("Adding message to " + queueName + "...");
        Console.WriteLine(JSON);

        CloudQueueMessage message = new CloudQueueMessage(JSON);
        queue.AddMessage(message);
    }

Dans la fenêtre du navigateur de la fonction de file d’attente, vous verrez chaque message traité :

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->