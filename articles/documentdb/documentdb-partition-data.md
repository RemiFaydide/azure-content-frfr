<properties 
	pageTitle="Partitionnement et mise à l’échelle dans Azure DocumentDB | Microsoft Azure"      
	description="Découvrez comme le partitionnement fonctionne dans Azure DocumentDB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application."         
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="arramac"/>

# Partitionnement et mise à l’échelle dans Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est conçu pour vous aider à obtenir des performances rapides et prévisibles, et à monter en charge en toute transparence parallèlement à l’évolution de votre application. Cet article fournit une vue d’ensemble du fonctionnement du partitionnement dans DocumentDB et décrit comment vous pouvez configurer les collections DocumentDB pour mettre à l’échelle vos applications efficacement.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Comment le partitionnement dans Azure DocumentDB fonctionne-t-il ?
- Comment configurer le partitionnement dans DocumentDB ?
- Que sont les clés de partition et comment choisir la bonne clé de partition pour mon application ?

Pour commencer avec le code, téléchargez le projet à partir de [DocumentDB performances test pilote Sample](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark) (Exemple de pilote de test des performances DocumentDB).

## Partitionnement dans DocumentDB

Dans DocumentDB, vous pouvez stocker et interroger des documents JSON sans schéma avec des délais de réponse de l’ordre des millisecondes à n’importe quelle échelle. DocumentDB fournit des conteneurs pour le stockage des données, appelés **collections**. Les collections sont des ressources logiques. Elles peuvent s’étendre sur plusieurs partitions physiques ou serveurs. Le nombre de partitions est déterminé par DocumentDB en fonction de la taille de stockage et du débit approvisionné de la collection. Chaque partition dans DocumentDB dispose d’une quantité fixe de stockage SSD associé. Elle est également répliquée pour offrir une haute disponibilité. La gestion des partitions est entièrement exécutée par Azure DocumentDB. Vous n’avez pas à écrire du code complexe, ni à gérer vos partitions. Les collections DocumentDB sont **pratiquement illimitées** en termes de débit et de stockage.

Le partitionnement est totalement transparent pour votre application. DocumentDB prend en charge les lectures et écritures rapides, les requêtes SQL et LINQ, la logique transactionnelle basée sur JavaScript, les niveaux de cohérence et le contrôle d’accès précis via des appels de l’API REST à une ressource de collection unique. Le service gère la distribution des données entre les partitions et le routage des demandes de requête vers la partition appropriée.

Comment cela fonctionne-t-il ? Lorsque vous créez une collection dans DocumentDB, vous pouvez spécifier une valeur de configuration de **propriété de clé de partition**. Il s’agit de la propriété JSON (ou chemin d’accès) dans vos documents qui peut être utilisée par DocumentDB pour distribuer vos données entre plusieurs serveurs ou partitions. DocumentDB hache la valeur de la clé de partition et utilise le résultat de hachage pour déterminer dans quelle partition le document JSON sera stocké. Tous les documents présentant la même clé de partition sont stockés au sein de la même partition.

Prenons pour exemple une application qui stocke des données sur les employés et leurs services dans DocumentDB. Choisissons `"department"` en tant que propriété de clé de partition, afin d’assurer la montée en charge des données par service. Chaque document dans DocumentDB doit contenir une propriété `"id"` obligatoire, unique pour chaque document ayant la même valeur de clé de partition, par exemple, `"Marketing`. Chaque document stocké dans une collection doit avoir une combinaison unique de clé de partition et d’ID, par exemple, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }` et `{ "Department": "Sales", "id": "0001" }`. En d’autres termes, la propriété composée de (clé de partition, ID) est la clé principale de votre collection.

### Clés de partition
Le choix de la clé de partition est une décision importante que vous devrez prendre au moment de la conception. Vous devez choisir un nom de propriété JSON avec un large éventail de valeurs et susceptible de disposer de modèles d’accès répartis équitablement. La clé de partition est spécifiée en tant que chemin d’accès JSON. Par exemple, `/department` représente le service de la propriété.

Le tableau suivant présente des exemples de définitions de clé de partition et les valeurs JSON correspondants.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Chemin d’accès de clé de partition</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.department, où doc est le document.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.properties.name, où doc est le document (propriété imbriquée).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.id (l’id et la clé de partition sont la même propriété).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nom de service"</p></td>
            <td valign="top"><p>Correspond à la valeur JSON doc["nom de service"], où doc est le document.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La syntaxe du chemin d’accès de clé de partition est similaire à la spécification de chemin d’accès pour l’indexation des chemins d’accès de stratégie, à la différence notoire que le chemin d’accès correspond à la propriété plutôt qu’à la valeur, de sorte qu’il n’y a pas de caractère générique à la fin. Par exemple, vous devez spécifier /department/? pour indexer les valeurs sous le service, mais spécifier /department comme définition de clé de partition. Le chemin d’accès de clé de partition est indexé de façon implicite, et ne peut pas être exclu de l’indexation à l’aide de remplacements de la stratégie d’indexation.

Examinons comment le choix de la clé de partition a une incidence sur les performances de votre application.

### Partitionnement et débit approvisionné
DocumentDB est conçu pour offrir des performances prévisibles. Lorsque vous créez une collection, vous réservez un débit en termes d’**[unités de requête](documentdb-request-units.md) (RU) par seconde**. Un coût en unités de demande proportionnel à la quantité de ressources système, comme le processeur et les E/S consommés par l’opération, est affecté à chaque demande. La lecture d’un document de 1 Ko avec une cohérence de session consomme 1 unité de demande. Une lecture correspond à 1 RU, quel que soit le nombre d’éléments stockés ou le nombre de demandes simultanées en cours d’exécution. Les documents plus volumineux nécessitent plus d’unités de demande selon leur taille. Si vous connaissez la taille de vos entités et le nombre de lectures nécessaires à prendre en charge pour votre application, vous pouvez approvisionner la quantité exacte de débit requis pour les besoins en lecture de votre application.

Lorsque DocumentDB stocke des documents, il les distribue uniformément entre les partitions en fonction de la valeur de la clé de partition. Le débit est également réparti uniformément entre les partitions disponibles, par exemple, le débit par partition = (débit total par collection) / (nombre de partitions).

>[AZURE.NOTE] Afin d’optimiser le débit total de la collection, vous devez choisir une clé de partition qui vous permet de répartir uniformément les demandes entre plusieurs valeurs de clé de partition distinctes.

## Partition unique et collections partitionnées
DocumentDB prend en charge la création de partitions uniques et de collections partitionnées.

- Les **collections partitionnées** peuvent s’étendre sur plusieurs partitions. Elles prennent en charge des volumes de stockage et de débit très importants. Vous devez spécifier une clé de partition pour la collection.
- Les **collections à partition unique** offrent des options tarifaires plus avantageuses, ainsi que la possibilité d’interroger et d’effectuer des transactions sur toutes les données de la collection. Elles ont les mêmes limites de stockage et d’évolutivité qu’une partition unique. Il n’est pas obligatoire de spécifier une clé de partition pour ces collections.

![Collections partitionnées dans DocumentDB][2]

Les collections à partition unique sont adaptées aux scénarios ne nécessitant pas de grands volumes de stockage ou de débit. Notez que les collections à partition unique ont les mêmes limites de stockage et d’évolutivité qu’une partition unique : jusqu’à 10 Go de stockage et jusqu’à 10 000 unités de demande par seconde.

Les collections partitionnées peuvent prendre en charge de très grandes quantités de stockage et de débit. Toutefois, les offres par défaut sont configurées pour stocker jusqu’à 250 Go de stockage et une mise à l’échelle jusqu’à 250 000 unités de demande par seconde. Si vous avez besoin d’un stockage ou d’un débit plus importants par collection, veuillez contacter le [support Azure](documentdb-increase-limits.md) pour faire augmenter les limites de votre compte.

Le tableau suivant répertorie les différences d’utilisation entre les collections à partition unique et les collections partitionnées :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Collection à partition unique</strong></p></td>
            <td valign="top"><p><strong>Collection partitionnée</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Partition Key</p></td>
            <td valign="top"><p>Aucun</p></td>
            <td valign="top"><p>Requis</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clé principale pour le document</p></td>
            <td valign="top"><p>« ID »</p></td>
            <td valign="top"><p>clé composée : &lt;clé de partition> et « ID »</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Stockage minimal</p></td>
            <td valign="top"><p>0&#160;Go</p></td>
            <td valign="top"><p>0&#160;Go</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Stockage maximal</p></td>
            <td valign="top"><p>10 Go</p></td>
            <td valign="top"><p>Illimité (250 Go par défaut)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit minimal</p></td>
            <td valign="top"><p>400 unités de demande par seconde</p></td>
            <td valign="top"><p>10 000 unités de demande par seconde</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit maximal</p></td>
            <td valign="top"><p>10 000 unités de demande par seconde</p></td>
            <td valign="top"><p>Illimité (250 000 unités de demande par seconde par défaut)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versions d’API</p></td>
            <td valign="top"><p>Tout</p></td>
            <td valign="top"><p>API 2015-12-16 et versions ultérieures</p></td>
        </tr>
    </tbody>
</table>

## Utilisation des kits de développement logiciel (SDK)

Avec l’[API REST version 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx), Azure Document DB a ajouté la prise en charge du partitionnement automatique. Afin de créer des collections partitionnées, vous devez télécharger le Kit de développement logiciel (SDK) version 1.6.0 ou une version plus récente sur l’une des plateformes SDK prises en charge (.NET, Node.js, Java, Python).

### Création de collections partitionnées

L’exemple suivant montre un extrait de code .NET permettant de créer une collection pour stocker les données de télémétrie d’appareil avec un débit de 20 000 unités de demande par seconde. Le Kit de développement logiciel (SDK) définit la valeur OfferThroughput (qui définit à son tour l’en-tête de demande `x-ms-offer-throughput` dans l’API REST). Ici, nous définissons `/deviceId` comme clé de partition. Le choix de la clé de partition est enregistré avec le reste des métadonnées de la collection, telles que le nom et la stratégie d’indexation.

Pour cet exemple, nous avons choisi `deviceId`, car nous savons que (a) dans la mesure où il existe un grand nombre d’appareils, les écritures peuvent être réparties uniformément entre les partitions, ce qui permet de mettre à l’échelle la base de données pour recevoir de très gros volumes de données et (b) plusieurs requêtes, comme l’extraction de la dernière lecture d’un appareil, sont limitées à un identificateur d’appareil unique et peuvent être récupérées à partir d’une seule partition.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Pour créer des collections partitionnées, vous devez spécifier une valeur de débit > 10 000 unités de requête par seconde. Étant donné que le débit est un multiple de 100, il doit s’agir de 10 100 ou d’une valeur supérieure.

Cette méthode passe un appel de l’API REST à DocumentDB et le service approvisionne plusieurs partitions en fonction du débit demandé. Vous pouvez modifier le débit d’une collection à mesure que vos besoins en matière de performances évoluent. Pour plus de détails, voir [Niveaux de performances](documentdb-performance-levels.md).

### Lecture et écriture de documents

À présent, nous allons insérer des données dans DocumentDB. Voici un exemple de classe qui contient la lecture d’un appareil et un appel à CreateDocumentAsync pour insérer une nouvelle lecture d’appareil dans une collection.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Nous allons lire le document avec sa clé de partition et son ID, le mettre à jour et, dans un dernier temps, nous allons le supprimer par clé de partition et ID. Notez que les lectures incluent une valeur PartitionKey (correspondant à l’en-tête de demande `x-ms-documentdb-partitionkey` dans l’API REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### Interrogation de collections partitionnées

Lorsque vous interrogez des données dans des collections partitionnées, DocumentDB achemine automatiquement la requête vers les partitions correspondant aux valeurs de clé de partition spécifiées dans le filtre (le cas échéant). Par exemple, cette requête est acheminée uniquement vers la partition contenant la clé de partition « XMS-0001 ».

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    	UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

Pour la requête suivante, la clé de partition (DeviceId) n’a pas de filtre. La requête est donc distribuée à toutes les partitions où elle est exécutée sur l’index de la partition. Notez que vous devez spécifier la valeur EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` dans l’API REST) pour que le Kit de développement logiciel (SDK) exécute une requête sur plusieurs partitions.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### Exécution de requêtes parallèles

Les kits de développement logiciel (SDK) de DocumentDB version 1.9.0 et versions ultérieures prennent en charge des options d’exécution de requêtes parallèles, ce qui vous permet d’effectuer des requêtes à faible latence sur les collections partitionnées, même lorsque ces requêtes concernent un grand nombre de partitions. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle sur plusieurs partitions.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Vous pouvez gérer l’exécution de requêtes parallèles en réglant les paramètres suivants :

- En définissant `MaxDegreeOfParallelism`, vous pouvez contrôler le degré de parallélisme, c’est-à-dire le nombre maximal de connexions réseau simultanées aux partitions de la collection. Si vous définissez cette valeur sur -1, le degré de parallélisme est géré par le Kit de développement logiciel (SDK). Si la valeur `MaxDegreeOfParallelism` n’est pas spécifiée ou définie sur 0, qui est la valeur par défaut, il n’y aura qu’une seule connexion réseau aux partitions de la collection.
- En définissant `MaxBufferedItemCount`, vous pouvez compenser l’utilisation de la mémoire côté client et la latence de la requête. Si vous omettez ce paramètre ou que vous lui affectez la valeur -1, le nombre d’éléments mis en mémoire tampon pendant l’exécution de requêtes parallèles est géré par le Kit de développement logiciel (SDK).

Avec un même état de collection, une requête parallèle retourne les résultats dans l’ordre d’exécution en série. Lorsque vous effectuez une requête entre plusieurs partitions qui comporte le tri (ORDER BY et/ou TOP), le Kit de développement logiciel (SDK) de DocumentDB émet la requête en parallèle sur plusieurs partitions et fusionne les résultats partiellement triés côté client pour produire des résultats globalement classés.

### Exécution de procédures stockées

Vous pouvez également exécuter des transactions atomiques sur des documents avec le même ID d’appareil, par exemple, si vous conservez des agrégats ou le dernier état d’un appareil dans un document unique.

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Dans la section suivante, nous examinons la manière de passer de collections à partition unique à des collections partitionnées.

<a name="migrating-from-single-partition"></a>
### Migration de collections à partition unique vers des collections partitionnées
Quand une application utilisant une collection à partition unique a besoin d’un débit supérieur (> 10 000 unités de requête/s) ou d’un stockage de données plus important (> 10 Go), vous pouvez utiliser [l’outil de migration de données DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) pour migrer les données de la collection à partition unique vers une collection partitionnée.

Pour migrer une collection à partition unique vers une collection partitionnée

1. Exportez les données de la collection à partition unique vers un fichier JSON. Pour plus de détails, voir [Exportation vers un fichier JSON](documentdb-import-data.md#export-to-json-file).
2. Importez les données dans une collection partitionnée créée avec une définition de clé de partition et un débit supérieur à 10 000 unités de requête par seconde, comme indiqué dans l’exemple ci-dessous. Pour plus de détails, voir [Importation de DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget).

![Migration de données vers une collection partitionnée dans DocumentDB][3]

>[AZURE.TIP] Pour accélérer l’importation, envisagez de porter le nombre de demandes parallèles à 100 voire davantage, afin de tirer parti du débit plus élevé disponible pour les collections partitionnées.

Après avoir découvert les principes de base, nous allons maintenant examiner quelques considérations de conception importantes lorsque vous utilisez des clés de partition dans DocumentDB.

## Conception du partitionnement
Le choix de la clé de partition est une décision importante que vous devrez prendre au moment de la conception. Cette section décrit certains des inconvénients liés à la sélection d’une clé de partition pour votre collection.

### Clé de partition en tant que limite de la transaction
Votre choix de clé de partition doit équilibrer la nécessité d’utiliser des transactions et la nécessité de répartir les entités sur plusieurs clés de partitions pour garantir une solution évolutive. D’un côté, vous pouvez définir la même clé de partition pour tous vos documents, mais cela peut limiter l’extensibilité de votre solution. D’un autre côté, vous pouvez attribuer une clé de partition unique à chaque document, ce qui optimise l’évolutivité, mais peut vous empêcher d’utiliser des transactions entre les documents par le biais des procédures stockées et des déclencheurs. Une clé de partition idéale vous permet d’utiliser des requêtes efficaces et possède une cardinalité suffisante pour garantir l’évolutivité de votre solution.

### Éviter les goulots d’étranglement des performances et du stockage 
Il est également important de choisir une propriété qui permet de distribuer les écritures entre plusieurs valeurs distinctes. Les demandes auprès de la même clé de partition ne peuvent pas surpasser le débit d’une partition unique et sont limitées. Il est donc important de choisir une clé de partition qui n’entraîne pas de **« zones réactives »** au sein de votre application. La taille totale de stockage des documents avec la même clé de partition ne peut pas non plus dépasser 10 Go de stockage.

### Exemples de clés de partition adéquates
Voici quelques exemples pour savoir comment sélectionner la clé de partition pour votre application :

* Si vous implémentez un service principal de profil utilisateur, alors l’ID utilisateur constitue un bon choix pour la clé de partition.
* Si vous stockez des données IoT, comme l’état d’appareil, un ID d’appareil constitue un bon choix pour la clé de partition.
* Si vous utilisez DocumentDB pour journaliser des données de série chronologique, le nom d’hôte et l’ID de processus constituent un bon choix pour une clé de partition.
* Si vous disposez d’une architecture mutualisée, l’ID client constitue un bon choix pour la clé de partition.

Notez que dans certains cas d’utilisation (comme l’IoT et les profils utilisateurs décrits ci-dessus), la clé de partition peut être identique à votre ID (clé de document). Dans d’autres cas, comme les données de série chronologique, la clé de partition peut être différente de l’ID.

### Partitionnement et journalisation de données de série chronologique
DocumentDB est très souvent utilisé à des fins de journalisation et de télémétrie. Il est important de choisir une bonne clé de partition, car il se peut que vous deviez lire et écrire des volumes importants de données. Le choix dépend de vos taux de lecture et d’écriture, et des types de requêtes que vous prévoyez d’exécuter. Voici quelques conseils sur la façon de choisir une clé de partition appropriée.

- Si votre mode d’utilisation implique un petit taux d’écritures s’accumulant sur une longue période, et la nécessité d’interroger sur la base de plages d’horodatage et d’autres filtres, l’utilisation d’un cumul de l’horodatage, par exemple d’une date, comme clé de partition constitue une bonne approche. Cela vous permet d’interroger toutes les données correspondant à une date à partir d’une seule partition.
- Si votre charge de travail est lourde en écriture, ce qui est généralement plus courant, vous devez utiliser une clé de partition qui n’est pas basée sur l’horodatage, afin que DocumentDB puisse répartir uniformément les écritures sur plusieurs partitions. Dans ce cas, un nom d’hôte, un ID de processus, un ID d’activité ou une autre propriété présentant une cardinalité élevée constituent un bon choix.
- Une troisième solution consiste à adopter une approche hybride, où vous avez plusieurs collections, une pour chaque jour/mois, et où la clé de partition est une propriété granulaire telle que le nom d’hôte. Cela présente l’avantage que vous pouvez définir différents niveaux de performances en fonction de la fenêtre temporelle. Par exemple, la collection pour le mois en cours est approvisionnée avec un débit plus élevé, car elle sert les lectures et écritures, tandis que les mois précédents offrent un débit inférieur, car ils servent uniquement les lectures.

### Partitionnement et mutualisation
Si vous implémentez une application mutualisée à l’aide de DocumentDB, il existe deux modèles principaux pour implémenter la location avec DocumentDB : une clé de partition par client et une collection par client. Voici les avantages et inconvénients de chaque modèle :

* Une clé de partition par client : dans ce modèle, les clients sont colocalisés au sein d’une collection unique. Mais les requêtes et les insertions pour les documents avec un seul client peuvent être exécutées sur une partition unique. Vous pouvez également implémenter une logique transactionnelle sur tous les documents avec un client. Étant donné que plusieurs clients partagent une collection, vous économisez des coûts de stockage et de débit en regroupant les ressources pour les clients dans une seule collection plutôt que d’approvisionner une marge supplémentaire pour chaque client. L’inconvénient est que vous n’avez pas d’isolation des performances par client. Les augmentations de performances/débit s’appliquent à l’ensemble de la collection au lieu d’augmentations ciblées pour les clients.
* Une collection par client : chaque client possède sa propre collection. Dans ce modèle, vous pouvez réserver des performances par client. Avec le nouveau modèle de tarification de DocumentDB basé sur la consommation, ce modèle est plus rentable pour les applications mutualisées avec un petit nombre de clients.

Vous pouvez également utiliser une approche à plusieurs niveaux/combinée qui regroupe les petits clients et migre les clients plus volumineux vers leur propre collection.

## Étapes suivantes
Dans cet article, nous avons décrit le fonctionnement du partitionnement dans Azure DocumentDB, la création de collections partitionnées et la sélection d’une clé de partition adéquate pour votre application.

-   Effectuez un test des performances et de la mise à l’échelle avec DocumentDB. Consultez la page [Test des performances et de la mise à l’échelle avec Azure DocumentDB](documentdb-performance-testing.md) pour obtenir un exemple
-   Commencez à coder avec les [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) ou [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Informez-vous sur le [débit approvisionné dans DocumentDB](documentdb-performance-levels.md)
-   Si vous souhaitez personnaliser la façon dont votre application effectue le partitionnement, vous pouvez incorporer votre propre implémentation de partitionnement côté client. Voir [Prise en charge du partitionnement côté client](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png

 

<!---HONumber=AcomDC_0921_2016-->