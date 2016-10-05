<properties
	pageTitle="Sortie dans un cache Redis Azure depuis Azure Stream Analytics à l’aide d’Azure Functions | Microsoft Azure"
	description="Découvrez comment utiliser une fonction Azure connectée à une file d’attente Service Bus pour remplir un cache Redis Azure à partir de la sortie d’une tâche Stream Analytics."
	keywords="flux de données, cache redis, file d’attente Service Bus"
	services="stream-analytics"
	authors="ryancrawcour"
	manager="jhubbard"
    documentationCenter=""
	/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/09/2016"
	ms.author="ryancraw"/>

# Comment stocker des données Azure Stream Analytics dans un Cache Redis Azure à l’aide d’Azure Functions

Azure Stream Analytics permet de développer et de déployer rapidement des solutions à faible coût pour obtenir des informations en temps réel à partir d’appareils, de capteurs, d’infrastructures et d’applications, ou de tout flux de données. Il permet de déployer différents cas d’utilisation, notamment la gestion en temps réel, la commande et le contrôle, la détection des fraudes, les voitures connectées et bien plus encore. Dans de nombreux scénarios de ce type, vous souhaiterez peut-être stocker des données de sorties d’Azure Stream Analytics dans un magasin de données distribué tel que le cache Redis Azure.

Supposons que vous faites partie d’une société de télécommunications. Vous essayez de détecter une fraude sur une carte SIM qui reçoit plusieurs appels provenant de la même identité, au même moment, mais depuis des emplacements géographiques différents. Vous êtes chargé de stocker tous les appels téléphoniques potentiellement frauduleux dans un cache Redis Azure. Dans ce blog, nous donnons des conseils sur la manière dont vous pouvez facilement réaliser cette tâche.

## Composants requis
Exécutez la procédure pas à pas de [détection des fraudes en temps réel][fraud-detection] pour ASA

## Présentation de l'architecture
![Capture d’écran de l’architecture](./media/stream-analytics-functions-redis/architecture-overview.png)

Comme indiqué dans la figure précédente, Stream Analytics permet d’interroger des flux de données d’entrée et de les envoyer vers une sortie. En fonction de la sortie, Azure Functions peut ensuite déclencher certains types d’événement.

Dans ce blog, nous nous concentrons sur la partie Azure Functions de ce pipeline, ou plus précisément sur le déclenchement d’un événement qui stocke les données frauduleuses dans le cache. Une fois le didacticiel [Détection des fraudes en temps réel][fraud-detection] exécuté, vous disposez d’une entrée (un event hub), d’une requête et d’une sortie (stockage d’objets blob) déjà configurées et exécutées. Dans ce blog, nous modifions la sortie pour utiliser une file d’attente Service Bus à la place. Après cela, nous connectons une fonction Azure à cette file d’attente.

## Création et connexion d’une sortie de la file d’attente Service Bus
Pour créer une file d’attente Service Bus, suivez les étapes 1 et 2 de la section .NET de la rubrique [Prise en main des files d’attente Service Bus][servicebus-getstarted]. Nous allons maintenant connecter la file d’attente à la tâche Stream Analytics qui a été créée dans la procédure de détection de fraude exécutée précédemment.



1. Dans le portail Azure, accédez au panneau **Sorties** de votre tâche, puis sélectionnez **Ajouter** en haut de la page.

	![Ajout de sorties](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Choisissez **File d’attente Service Bus** comme **Récepteur** et suivez les instructions à l’écran. Veillez à choisir l’espace de noms de la file d’attente Service Bus que vous avez créé dans le cadre de la [Prise en main des files d’attente Service Bus][servicebus-getstarted]. Cliquez sur le bouton « Droite » lorsque vous avez terminé.
3. Spécifiez les valeurs suivantes :
	- **Format du sérialiseur d'événement** : JSON
	- **Encodage** : UTF8
	- **FORMAT** : séparé par une ligne

4. Cliquez sur le bouton **Créer** pour ajouter cette source et vérifier que Stream Analytics peut se connecter au compte de stockage.

5. Dans l’onglet **Requête**, remplacez la requête en cours par l’élément suivant. Remplacez *[VOTRE NOM DE SERVICE BUS]* par le nom de sortie que vous avez créé à l’étape 3.

    ```    

	    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
	    JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## Création d'un cache Redis Azure
Créez un cache Redis Azure en suivant la section .NET de la rubrique [Utilisation du cache Redis Azure][use-rediscache] jusqu'à la section ***Configuration des clients du cache***. Une fois que vous avez terminé, vous disposez d’un nouveau cache Redis. Sous **Tous les paramètres**, sélectionnez **Clés d’accès** et notez la ***Chaîne de connexion principale***.

![Capture d’écran de l’architecture](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## Création d’une fonction Azure
Suivez le didacticiel [Créer votre première fonction Azure][functions-getstarted] pour vous familiariser avec les fonctions d’Azure (Azure Functions). Si vous disposez déjà d’une fonction Azure que vous souhaitez utiliser, passez à [Écriture dans le cache Redis](#Writing-to-Redis-Cache).

1. Dans le portail, sélectionnez App Services dans le volet de navigation gauche, puis cliquez sur le nom de votre application de fonction Azure pour accéder au site Web de l’application de fonction. ![Capture d’écran de la liste des applications App Services](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Cliquez sur **Nouvelle fonction > ServiceBusQueueTrigger – C#**. Pour les champs suivants, suivez les instructions ci-dessous :
	- **Nom de la file d’attente** : le même nom que le nom que vous avez entré lorsque vous avez créé la file d’attente dans [Prise en main des files d’attente Service Bus][servicebus-getstarted] \(pas le nom du service bus). Vérifiez que vous utilisez la file d’attente qui est connectée à la sortie Stream Analytics.
	- **Connexion Service Bus**: sélectionnez **Ajouter une chaîne de connexion**. Pour rechercher la chaîne de connexion, accédez au portail Classic, sélectionnez **Service Bus**, le bus de service que vous avez créé, et **INFORMATIONS DE CONNEXION** en bas de l’écran. Assurez-vous que vous êtes dans l’écran principal de cette page. Copiez et collez la chaîne de connexion. N’hésitez pas à entrer un nom de connexion.
	
		![Capture d’écran de la connexion Service Bus](./media/stream-analytics-functions-redis/servicebus-connection.png)
	- **AccessRights**: choisissez **Gérer**


3. Cliquez sur **Créer**

## Écriture dans le cache Redis
Nous avons maintenant créé une fonction Azure qui lit à partir d’une file d’attente Service Bus. Tout ce qui reste à faire est d’utiliser notre fonction pour écrire ces données dans le cache Redis.

1. Sélectionnez votre **ServiceBusQueueTrigger** nouvellement créé, puis cliquez sur **Paramètres Function App** dans le coin supérieur droit. Sélectionnez **Accédez aux paramètres App Service > Paramètres > Paramètres de l’application**

2. Dans la section Chaînes de connexion, spécifiez un nom dans la section **Nom**. Collez la chaîne de connexion principale que vous avez trouvée à l’étape **Création d’un cache Redis** dans la section **Valeur**. Sélectionnez **Personnalisé** pour la **Base de données SQL**.

3. Cliquez sur **Enregistrer** en haut.

	![Capture d’écran de la connexion Service Bus](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Revenez aux paramètres App Service et sélectionnez **Outils > Éditeur App Service (Préversion) > Activé > Accéder**.

	![Capture d’écran de la connexion Service Bus](./media/stream-analytics-functions-redis/app-service-editor.png)

5. Dans un éditeur de votre choix, créez un fichier JSON nommé **project.json** avec les éléments suivants et enregistrez-le sur votre disque local.

        {
            "frameworks": {
		        "net46": {
		            "dependencies": {
				        "StackExchange.Redis":"1.1.603",
				        "Newtonsoft.Json": "9.0.1"
			        }
		        }
	        }
        }

6. Chargez ce fichier dans le répertoire racine de votre fonction (pas WWWROOT). Vous devriez voir un fichier nommé **project.lock.json** s’afficher automatiquement qui confirme que les packages Nuget « StackExchange.Redis » et « Newtonsoft.Json » ont été importés.

7. Dans le fichier **run.csx**, remplacez le code préalablement généré par le code suivant. Dans la fonction lazyConnection, remplacez « CONN NAME » par le nom que vous avez créé à l’étape 2 de **Stocker des données dans le cache Redis**.

````

	using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
		new Lazy<ConnectionMultiplexer>(() =>
    		{
				var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
        		return ConnectionMultiplexer.Connect();
    		});
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## Démarrage de la tâche Stream Analytics

1. Démarrez l’application telcodatagen.exe. Procédez comme suit : ````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. Dans le panneau Tâche Stream Analytics du portail, cliquez sur **Démarrer** en haut de la page.

	![Capture d’écran de la tâche de démarrage](./media/stream-analytics-functions-redis/starting-job.png)

3. Dans le panneau **Démarrer la tâche** qui s’affiche, sélectionnez **Maintenant**, puis cliquez sur le bouton **Démarrer** au bas de l’écran. L’état de la tâche passe à Démarrage, puis à Exécution après quelques instants.
 
	![Capture d’écran de la sélection de l’heure de la tâche de démarrage](./media/stream-analytics-functions-redis/start-job-time.png)

## Exécution de la solution et vérification des résultats
En revenant à votre page **ServiceBusQueueTrigger**, vous devez maintenant voir les instructions de journaux. Ces journaux indiquent que vous avez copié quelque chose de la file d’attente Service Bus dans la base de données et que vous l’avez récupéré sur la base de l’heure !

Pour vérifier que vos données se trouvent dans votre cache Redis, accédez à votre page Cache Redis dans le nouveau portail (comme indiqué à l’étape précédente [Création d’un cache Redis Azure](#Create-an-Azure-Redis-Cache)), puis sélectionnez Console.

Vous pouvez maintenant écrire des commandes Redis pour confirmer que les données se trouvent vraiment dans le cache.

![Capture d’écran de la console Redis](./media/stream-analytics-functions-redis/redis-console.png)

## Étapes suivantes
Nous sommes heureux de ce que la combinaison d’Azure Functions et de Stream Analytics permet de réaliser, et nous espérons que vous pourrez y trouver de nouvelles opportunités. Si vous avez des commentaires sur la manière dont nous devons faire évoluer nos produits, n’hésitez pas à utiliser le [site Azure UserVoice](https://feedback.azure.com/forums/270577-stream-analytics).

Si vous ne connaissez pas encore Microsoft Azure, nous vous invitons à le découvrir en vous connectant à un [compte d’évaluation Azure gratuit](https://azure.microsoft.com/pricing/free-trial/). Si vous ne connaissez pas encore Stream Analytics, nous vous invitons à [créer votre première tâche Stream Analytics](stream-analytics-create-a-job.md).

Si vous avez besoin d’aide ou avez des questions, postez-les sur les forums [MSDN](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics) ou [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics).

Vous pouvez également vous référer aux ressources suivantes :

- [Informations de référence pour les développeurs sur Azure Functions](../azure-functions/functions-reference.md)
- [Informations de référence pour les développeurs C# sur Azure Functions](../azure-functions/functions-reference-csharp.md)
- [Informations de référence pour les développeurs F# sur Azure Functions](../azure-functions/functions-reference-fsharp.md)
- [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](../azure-functions/functions-reference.md)
- [Déclencheurs et liaisons Azure Functions](../azure-functions/functions-triggers-bindings.md)
- [Surveillance du cache Redis Azure](../redis-cache/cache-how-to-monitor.md)

Pour vous tenir informé des dernières nouveautés et fonctionnalités, suivez [@AzureStreaming](https://twitter.com/AzureStreaming) sur Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

<!---HONumber=AcomDC_0921_2016-->