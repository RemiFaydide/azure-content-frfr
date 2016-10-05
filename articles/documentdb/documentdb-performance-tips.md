<properties 
	pageTitle="Conseils en matière de performances pour DocumentDB | Microsoft Azure" 
	description="Découvrez les options de configuration clientes pour améliorer les performances de base de données Azure DocumentDB"
	keywords="comment améliorer les performances de base de données"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="mimig"/>

# Conseils en matière de performances pour DocumentDB

Azure DocumentDB est une base de données distribuée rapide et flexible qui s’adapte en toute transparence à la latence et au débit garantis. Vous n’avez pas à apporter de modifications d’architecture majeures ou écrire de code complexe pour mettre à l’échelle votre base de données avec DocumentDB. Il suffit d’un simple appel d’API ou de méthode de [kit de développement logiciel (SDK)](documentdb-performance-levels.md#changing-performance-levels-using-the-net-sdk) pour effectuer une mise à l’échelle. Toutefois, étant donné que DocumentDB est accessible via des appels réseau, vous pouvez apporter des optimisations côté client de manière à atteindre des performances de pointe.

Si vous vous demandez comment améliorer les performances de votre base de données, lisez ce qui suit :

## Mise en réseau
<a id="direct-connection"></a>

1. **Stratégie de connexion : utilisation du mode de connexion direct**
    
    La façon dont un client se connecte à Azure DocumentDB a des conséquences importantes sur les performances, notamment en termes de latence côté client. Il existe deux paramètres de configuration essentiels pour la stratégie de connexion client : le *mode* de connexion et le *protocole* [de connexion](#connection-protocol). Les deux modes disponibles sont :

    1. Mode passerelle (par défaut)
    2. Mode direct

    Puisque DocumentDB est un système de stockage distribué, les ressources DocumentDB telles que les collections sont partitionnées entre de nombreux ordinateurs et chaque partition est répliquée à des fins de haute disponibilité. La logique de conversion des adresses physiques est conservée dans une table de routage, qui est également disponible en interne en tant que ressource.

    En mode passerelle, les ordinateurs de passerelle DocumentDB effectuent ce routage, permettant ainsi de simplifier le code client et de réduire sa taille. Une application cliente émet des requêtes aux ordinateurs de passerelle DocumentDB, qui traduisent l’URI logique de la requête à l’adresse physique du nœud principal et envoient la réponse de manière appropriée. À l’inverse, en mode direct les clients doivent conserver (et actualiser périodiquement) une copie de cette table de routage, puis se connecter directement aux nœuds DocumentDB principaux.

    Le mode passerelle est pris en charge sur toutes les plateformes de kit de développement logiciel (SDK) et est l’option configurée par défaut. Si votre application s’exécute dans un réseau d’entreprise avec des restrictions de pare-feu strictes, le mode passerelle est la meilleure option, car il utilise le port HTTPS standard et un seul point de terminaison. Toutefois, il existe un compromis en termes de performances : le mode passerelle implique un tronçon réseau supplémentaire chaque fois que les données sont lues ou écrites dans DocumentDB. Étant donné que le mode direct implique moins de tronçons réseaux, les performances sont meilleures.

2. **Stratégie de connexion : utilisation du protocole TCP**

    Lorsque vous utilisez le mode direct, deux options de protocole sont disponibles :

    - TCP
    - HTTPS

    DocumentDB fournit un modèle de programmation RESTful simple et ouvert sur HTTPS. De plus, il fournit un protocole TCP très performant qui utilise aussi un modèle de communication RESTful, disponible via le Kit de développement logiciel (SDK) .NET. Pour de meilleures performances, utilisez le protocole TCP lorsque cela est possible.

    Le mode connectivité est configuré lors de la construction de l’instance DocumentClient avec le paramètre ConnectionPolicy. Si le mode direct est utilisé, le protocole peut également être défini dans le paramètre ConnectionPolicy.

        var serviceEndpoint = new Uri("https://contoso.documents.net");
        var authKey = new "your authKey from Azure Mngt Portal";
        DocumentClient client = new DocumentClient(serviceEndpoint, authKey, 
        new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        });

    Puisque TCP est uniquement pris en charge en mode direct, si le mode passerelle est activé, c’est le protocole HTTPS qui sera toujours utilisé pour communiquer avec la passerelle, et la valeur de protocole dans le paramètre ConnectionPolicy sera ignorée.

    ![Illustration de la stratégie de connexion DocumentDB](./media/documentdb-performance-tips/azure-documentdb-connection-policy.png)

3. **Appel d’OpenAsync pour éviter la latence de démarrage lors de la première requête**

    Par défaut, la première requête aura une latence plus élevée, car elle doit extraire la table de routage d’adresses. Pour éviter cette latence de démarrage lors de la première requête, vous devez appeler OpenAsync() une seule fois lors de l’initialisation, comme indiqué ci-après.

        await client.OpenAsync();
<a id="same-region"></a>
4. **Colocalisation des clients dans la même région Azure pour les performances**

    Lorsque cela est possible, placez toutes les applications appelant DocumentDB dans la même région que la base de données DocumentDB. Pour une comparaison approximative, les appels à DocumentDB dans la même région s’effectuent en 1 à 2 ms, mais la latence entre les côtes ouest et est des États-Unis est supérieure à 50 ms. Cette latence peut probablement varier d’une requête à l’autre, en fonction de l’itinéraire utilisé par la requête lorsqu’elle passe du client à la limite du centre de données Azure. Pour obtenir la latence la plus faible possible, l’application appelante doit être située dans la même région Azure que le point de terminaison DocumentDB configuré. Pour obtenir la liste des régions disponibles, voir [Régions Azure](https://azure.microsoft.com/regions/#services).

    ![Illustration de la stratégie de connexion DocumentDB](./media/documentdb-performance-tips/azure-documentdb-same-region.png) <a id="increase-threads"></a>
5. **Augmentation du nombre de threads/tâches**

    Étant donné que les appels à DocumentDB sont effectués sur le réseau, vous devrez peut-être modifier le degré de parallélisme de vos requêtes, afin que l’application cliente attende très peu de temps entre les requêtes. Par exemple, si vous utilisez la [bibliothèque parallèle de tâches](https://msdn.microsoft.com//library/dd460717.aspx) .NET, créez plusieurs centaines de tâches de lecture ou d’écriture dans DocumentDB.

## Utilisation du kit de développement logiciel (SDK)

1. **Installation du kit de développement logiciel (SDK) le plus récent**

    Les kits de développement logiciel (SDK) de DocumentDB sont améliorés en permanence pour offrir les meilleures performances. Consultez la page [Kit SDK DocumentDB](documentdb-sdk-dotnet.md) pour déterminer quel est le kit de développement logiciel (SDK) le plus récent et passer en revue les améliorations.

2. **Utilisation d’un client de DocumentDB singleton pour la durée de vie de votre application**
  
    Notez que chaque instance de DocumentClient est thread-safe et effectue une gestion des connexions efficace et une mise en cache d’adresses lorsque le mode direct est sélectionné. Pour permettre une gestion des connexions efficace et améliorer les performances par DocumentClient, nous vous recommandons d’utiliser une seule instance de DocumentClient par AppDomain pour la durée de vie de l’application. <a id="max-connection"></a>
3. **Augmentation de System.Net MaxConnections par hôte**

    Par défaut, les requêtes DocumentDB sont effectuées par le biais de HTTPS/REST et sont soumises aux limites de connexion par défaut par nom d’hôte ou adresse IP. Vous devrez peut-être définir MaxConnections sur une valeur plus élevée (100 à 1000) afin que la bibliothèque cliente puisse utiliser plusieurs connexions simultanées à DocumentDB. Dans le kit de développement logiciel (SDK) .NET 1.8.0 et versions ultérieures, la valeur par défaut pour [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) est 50, et pour modifier la valeur, vous pouvez définir [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/fr-FR/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) sur une valeur plus élevée.

4. **Paramétrage des requêtes parallèles pour les collections partitionnées**

    La version 1.9.0 et versions ultérieures du Kit de développement logiciel .NET de DocumentDB prennent en charge les requêtes parallèles, qui vous permettent d’interroger une collection partitionnée en parallèle. Pour plus d’informations, consultez l’article Utilisation des kits de développement logiciel (SDK) et les exemples de code connexes. Ils sont conçus pour améliorer le débit et la latence des requêtes. Les requêtes parallèles fournissent deux paramètres que les utilisateurs peuvent paramétrer en fonction de leurs besoins, (a) MaxDegreeOfParallelism, pour contrôler le nombre maximal de partitions qui peuvent être interrogées en parallèle, et (b) MaxBufferedItemCount, pour contrôler le nombre de résultats pré-extraits.
    
    (a) La requête parallèle ***Tuning MaxDegreeOfParallelism*** interroge plusieurs partitions en parallèle. Les données d’une collection partitionnée individuelle sont toutefois extraites en série dans le cadre de la requête. La définition du paramètre MaxDegreeOfParallelism sur le nombre de partitions augmente les chances de résultats de la requête, sous réserve que toutes les autres conditions système restent inchangées. Si vous ne connaissez pas le nombre de partitions, vous pouvez définir le paramètre MaxDegreeOfParallelism sur un nombre élevé, et le système sélectionnera le minimum (nombre de partitions, entrée fournie par l’utilisateur) comme paramètre MaxDegreeOfParallelism.
    
    Il est important de noter que les requêtes parallèles produisent de meilleurs résultats si les données sont réparties de manière homogène entre toutes les partitions. Si la collection est partitionnée de telle façon que toutes les données retournées par une requête, ou une grande partie d’entre elles, sont concentrées sur quelques partitions (une partition dans le pire des cas), les performances de la requête sont altérées par ces partitions.
    
    (b) La requête parallèle ***Tuning MaxBufferedItemCount*** pré-extrait les résultats tandis que le lot de résultats est en cours de traitement par le client. La pré-extraction permet d’améliorer la latence globale d’une requête. MaxBufferedItemCount est le paramètre utilisé pour limiter la quantité de résultats pré-extraits. La définition du paramètre MaxBufferedItemCount sur le nombre de résultats attendu (ou un nombre plus élevé) permet à la requête d’optimiser la pré-extraction.
    
    Notez que la pré-extraction fonctionne de la même façon, quel que soit le paramètre MaxDegreeOfParallelism, et il existe une seule mémoire tampon pour les données de toutes les partitions.

5. **Activation de GC côté serveur**
    
    Réduire la fréquence de Garbage Collection peut aider dans certains cas. Dans .NET, définissez [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) sur true.

6. **Implémentation d’interruption à des intervalles de RetryAfter**
 
    Lors du test de performances, vous devez augmenter la charge jusqu’à une limite d’un petit nombre de requêtes. En cas de limitation, l’application cliente doit s’interrompre à la limitation pour l’intervalle de nouvelle tentative spécifié sur le serveur Le respect de l’interruption garantit un temps d’attente minimal entre chaque tentative. La prise en charge de la stratégie de nouvelle tentative est incluse dans les versions 1.8.0 et ultérieures de DocumentDB [.NET](documentdb-sdk-dotnet.md) et [Java](documentdb-sdk-java.md), ainsi que dans les versions 1.9.0 et ultérieures de [Node.js](documentdb-sdk-nodejs.md) et [Python](documentdb-sdk-python.md). Pour plus d’informations, consultez la section [Dépassement des limites de débit réservé](documentdb-request-units.md#exceeding-reserved-throughput-limits) et [Propriété RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).

7. **Augmentation de la taille des instances de votre charge de travail cliente**

    Si vous effectuez des tests à des niveaux de débit élevé (> 50 000 RU/s), l’application cliente peut devenir un goulet d’étranglement en raison du plafonnement sur l’utilisation du processeur ou du réseau. Si vous atteignez ce point, vous pouvez continuer à augmenter le compte DocumentDB en montant en charge vos applications clientes sur plusieurs serveurs.

8. **Mise en cache d’URI de document pour une latence de lecture plus faible**

    Effectuez une mise en cache des URI de document dès que possible pour garantir la meilleure lecture. <a id="tune-page-size"></a>
9. **Réglage de la taille de la page des flux de lecture/requêtes pour de meilleures performances**

    Lors d’une lecture groupée de documents à l’aide de la fonctionnalité de flux de lecture (ReadDocumentFeedAsync) ou lors de l’émission d’une requête SQL DocumentDB, les résultats sont retournés de façon segmentée si le jeu de résultats est trop grand. Par défaut, les résultats sont retournés dans des segments de 100 éléments ou de 1 Mo, selon la limite atteinte en premier.

    Afin de réduire le nombre de boucles réseau nécessaires pour récupérer tous les résultats applicables, vous pouvez augmenter la taille de la page à 1 000 résultats à l’aide de l’en-tête de requête x-ms-max-item-count. Si vous avez besoin d’afficher uniquement quelques résultats, (par exemple, si votre interface utilisateur ou API d’application retourne seulement 10 résultats à la fois), vous pouvez également réduire la taille de la page à 10 résultats, afin de baisser le débit consommé pour les lectures et requêtes.

    Vous pouvez également définir la taille de la page à l’aide des kits de développement logiciel (SDK) DocumentDB disponibles. Par exemple :
    
        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });

10. **Augmentation du nombre de threads/tâches**

	Consultez [Augmentation du nombre de threads/tâches](increase-threads.md) à la section Mise en réseau.

## Stratégie d'indexation

1. **Utilisation de l’indexation différée pour des taux d’ingestion plus rapides en période de pointe**

    DocumentDB vous permet de spécifier, au niveau de la collection, une stratégie d’indexation qui offre la possibilité de choisir si vous souhaitez que les documents d’une collection soient indexés automatiquement ou non. En outre, vous avez le choix entre des mises à jour d’index synchrones (cohérentes) et asynchrones (différées). Par défaut, l'index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d'un document au niveau de la collection. Le mode synchrone permet aux requêtes d’honorer le même [niveau de cohérence](documentdb-consistency-levels.md) que les lectures de document sans que l’index ne soit soumis à un quelconque délai de rattrapage.
    
    L’indexation différée peut être envisagée dans des scénarios où les données sont écrites en rafales et que vous souhaitez amortir le travail requis pour indexer le contenu sur une période de temps plus longue. L’indexation différée permet également d’utiliser le débit configuré de manière efficace et de répondre aux requêtes d’écriture en période de pointe avec une latence minimale. Toutefois, il est important de noter que, si l’indexation différée est activée, les résultats des requêtes seront cohérents, indépendamment du niveau de cohérence configuré pour le compte DocumentDB.

    Par conséquent, le mode d’indexation cohérent (IndexingPolicy.IndexingMode est défini sur Cohérent) implique les frais d’unité de requête les plus élevés par écriture, tandis que le mode d’indexation différé (IndexingPolicy.IndexingMode est défini sur différé) et le mode sans indexation (IndexingPolicy.Automatic est défini sur False) n’implique aucun coût d’indexation au moment de l’écriture.

2. **Exclusion des chemins d’accès inutilisés de l’indexation pour des écritures plus rapides**

    La stratégie d’indexation de DocumentDB vous permet également de spécifier les chemins d’accès de document à inclure ou exclure de l’indexation en tirant parti des chemins d’accès d’indexation (IndexingPolicy.IncludedPaths et IndexingPolicy.ExcludedPaths). L’utilisation des chemins d’accès d’indexation peut offrir des performances d’écriture améliorées et réduire le stockage d’index pour les scénarios dans lesquels les modèles de requête sont connus d’avance, puisque les coûts d’indexation sont directement liés au nombre de chemins d’accès uniques indexés. Par exemple, le code suivant montre comment exclure une section entière de documents (également appelée une sous-arborescence) de l’indexation à l’aide du caractère générique « * ».

        var collection = new DocumentCollection { Id = "excludedPathCollection" };
        collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
        collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
        collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);

    Pour plus d’informations, consultez [Stratégies d’indexation de DocumentDB](documentdb-indexing-policies.md).

## Débit
<a id="measure-rus"></a>

1. **Mesure et réglage pour réduire l’utilisation d’unités de requête par seconde**

    DocumentDB propose un riche ensemble d’opérations de base de données, dont les requêtes hiérarchiques et relationnelles avec les fonctions définies par l’utilisateur, les procédures stockées et les déclencheurs, qui fonctionnent tous au niveau des documents d’une collection de base de données. Le coût associé à chacune de ces opérations varie en fonction du processeur, des E/S et de la mémoire nécessaires à l’exécution de l’opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à la demande de l'application.

    [Les unités de requête](documentdb-request-units.md) sont configurées pour chaque compte de base de données selon le nombre d’unités de capacité achetées. La consommation d'unités de demande est évaluée en fonction d'un taux par seconde. Les applications qui dépassent le taux d’unité de requête configuré pour le compte associé sont limitées jusqu’à ce que le taux soit inférieur au niveau réservé pour le compte. Si votre application a besoin d'un niveau de débit plus élevé, vous pouvez acheter des unités de capacité supplémentaires.

    La complexité d’une requête a un impact sur le nombre d’unités de requête consommées pour une opération. Le nombre de prédicats, la nature des prédicats, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données sources ont tous une influence sur le coût des opérations de requête.

    Pour mesurer les frais de l’opération (création, mise à jour ou suppression), inspectez l’en-tête x-ms-request-charge (ou la propriété RequestCharge équivalente dans ResourceResponse<T> ou FeedResponse<T> dans le Kit de développement logiciel (SDK) .NET) afin de déterminer le nombre d’unités de requête consommées par ces opérations.

        // Measure the performance (request units) of writes
        ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
        Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
        // Measure the performance (request units) of queries
        IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
        while (queryable.HasMoreResults)
             {
                  FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
                  Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
             }
        
    Les frais de la requête retournée dans cet en-tête correspondent à une fraction du débit configuré (c’est-à-dire 2 000 RU/seconde). Par exemple, si la requête ci-dessus renvoie 1 000 documents de 1 Ko, le coût de l’opération sera de 1 000. Par conséquent, en une seconde, le serveur honore uniquement deux requêtes avant de limiter les requêtes suivantes. Pour plus d’informations, consultez [Unités de requête](documentdb-request-units.md) et la [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner).

2. **Gestion de la limite de taux/du taux de requête trop importants**

    Lorsqu’un client tente de dépasser le débit réservé pour un compte, les performances au niveau du serveur ne sont pas affectées et la capacité de débit n’est pas utilisée au-delà du niveau réservé. Le serveur met fin à la requête de manière préventive avec RequestRateTooLarge (code d’état HTTP 429) et il retourne l’en-tête x-ms-retry-after-ms indiquant la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer.
 
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Les kits de développement logiciel (SDK) interceptent tous implicitement cette réponse, respectent l’en-tête retry-after spécifiée par le serveur, puis relancent la requête. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

    Si plusieurs clients opèrent simultanément au-delà du taux de requête, le nombre de nouvelles tentatives par défaut actuellement défini sur 9 en interne par le client peut ne pas suffire. Dans ce cas, le client envoie une exception DocumentClientException avec le code d’état 429 à l’application. Le nombre de nouvelles tentatives par défaut peut être modifié en définissant les RetryOptions sur l’instance ConnectionPolicy. Par défaut, la DocumentClientException avec le code d’état 429 est retournée après un temps d’attente cumulé de 30 secondes si la requête continue à fonctionner au-dessus du taux de requête. Cela se produit même lorsque le nombre de nouvelles tentatives actuel est inférieur au nombre maximal de nouvelles tentatives, qu’il s’agisse de la valeur par défaut de 9 ou d’une valeur définie par l’utilisateur.

    Alors que le comportement de nouvelle tentative automatique permet d’améliorer la résilience et la facilité d’utilisation pour la plupart des applications, il peut se révéler contradictoire lors de l’exécution de tests de performances, en particulier lors de la mesure de la latence. La latence client observée atteindra un pic si l’expérience atteint la limite de serveur et oblige le kit de développement logiciel (SDK) client à effectuer une nouvelle tentative en silence. Pour éviter des pics de latence lors des expériences de performances, mesurez la charge renvoyée par chaque opération et assurez-vous que les requêtes fonctionnent en dessous du taux de requête réservé. Pour plus d’informations, consultez [Unités de requête](documentdb-request-units.md).
   
3. **Conception de documents plus petits pour un débit plus élevé**

    Les frais de requête (p. ex. le coût de traitement de requête) d’une opération donnée sont directement liés à la taille du document. Des opérations sur des documents volumineux coûtent plus cher que des opérations sur de petits documents.

## Niveaux de cohérence

1. **Utilisation de niveaux de cohérence plus faibles pour de meilleures latences de lecture**

    Un autre facteur important à prendre en compte lors du réglage des performances des applications DocumentDB est le niveau de cohérence. Le choix du niveau de cohérence affecte les performances pour les lectures et écritures. Vous pouvez configurer le niveau de cohérence par défaut sur le compte de base de données qui s’applique à toutes les collections (parmi l’ensemble des bases de données) dans le compte DocumentDB. En termes d’opérations d’écriture, l’impact de la modification du niveau de cohérence peut être observé en tant que latence des requêtes. Puisque des niveaux de cohérence plus forts sont utilisés, les latences d’écriture augmentent. En revanche, l’impact du niveau de cohérence sur les opérations de lecture est observé en termes de débit. Des niveaux de cohérence plus faibles offrent un débit de lecture plus élevé pour le client.

    Par défaut, toutes les lectures et requêtes émises vers les ressources définies par l’utilisateur utilisent le niveau de cohérence par défaut spécifié sur le compte de base de données. Vous pouvez néanmoins réduire le niveau de cohérence d’une requête de lecture/requête donnée en spécifiant l’en-tête de requête x-ms-consistency-level. Pour plus d’informations, consultez [Niveaux de cohérence dans DocumentDB](documentdb-consistency-levels.md).

## Étapes suivantes

Pour un exemple d’application permettant d’évaluer DocumentDB lors de scénarios hautes performances sur quelques ordinateurs clients, consultez la page [Test des performances et de la mise à l’échelle avec Azure DocumentDB](documentdb-performance-testing.md).

En outre, pour en savoir plus sur la conception de votre application pour une mise à l’échelle et de hautes performances, consultez [Partitionnement et mise à l’échelle dans Azure DocumentDB](documentdb-partition-data.md).

<!---HONumber=AcomDC_0921_2016-->