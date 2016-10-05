<properties 
	pageTitle="Modèle de ressources hiérarchiques et concepts de DocumentDB | Microsoft Azure" 
	description="Découvrez notamment le modèle hiérarchique des bases de données, collections, fonctions définies par l’utilisateur, documents et autorisations de DocumentDB pour gérer les ressources."
	keywords="Modèle hiérarchique, documentdb, azure, Microsoft azure"	
	services="documentdb" 
	documentationCenter="" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="anhoh"/>

# Modèle de ressources hiérarchiques et concepts de DocumentDB

Les entités de base de données que gère DocumentDB sont appelées des **ressources**. Chaque ressource est identifiée de manière unique par un URI logique. Vous pouvez interagir avec ces ressources en utilisant des verbes HTTP standard, des en-têtes de demande/réponse et des codes d'état.

En lisant cet article, vous serez en mesure de répondre aux questions suivantes :

- Qu'est-ce que le modèle de ressource de DocumentDB ?
- Quelles sont les différences entre les ressources définies par le système et celles définies par l'utilisateur ?
- Comment adresser une ressource ?
- Comment travailler avec des collections ?
- Comment utiliser les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur ?

## Modèle de ressource hiérarchique
Comme l'illustre le schéma suivant, le **modèle de ressources** hiérarchique de DocumentDB regroupe des ensembles de ressources dans un compte de base de données, chacun d'eux étant adressable via un URI stable et logique. Les ensembles de ressources sont désignés sous le nom de **flux** dans cet article.

>[AZURE.NOTE] DocumentDB fournit un protocole TCP très performant qui utilise aussi un modèle de communication RESTful, disponible via le [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modèle de ressources hiérarchique de DocumentDB][1]  
**Modèle de ressource hiérarchique**

Pour commencer à travailler avec des ressources, vous devez [créer un compte de base de données DocumentDB](documentdb-create-account.md) à l’aide de votre abonnement Azure. Un compte de base de données peut être composé d'un jeu de **bases de données**. Chacune d'elles contient plusieurs **collections** et chaque collection contient des **procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents** et des **pièces jointes** associées (fonctionnalité en version préliminaire). La base de données a également des **utilisateurs** associés. Chacun d’eux reçoit un ensemble d’**autorisations** pour pouvoir accéder aux collections, aux procédures stockées, aux déclencheurs, aux fonctions définies par l’utilisateur, aux documents ou aux pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents et les pièces jointes contiennent du contenu JSON arbitraire défini par l'utilisateur.

|Ressource |Description
|-----------|-----------
|Compte de base de données |Le compte de base de données est associé à un jeu de bases de données et à une quantité fixe de stockage d’objets blob pour les pièces jointes (fonctionnalité en version préliminaire). Vous pouvez créer un ou plusieurs comptes de base de données à l’aide de votre abonnement Azure. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
|Base de données |Une base de données est un conteneur logique de stockage de documents partitionné entre des collections. Elle sert également de conteneur pour les utilisateurs.
|Utilisateur |Espace de noms logique pour les autorisations d'étendue. 
|Autorisation |Jeton d'autorisation associé à un utilisateur pour un accès à une ressource spécifique.
|Collection |Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le niveau de performances associé à la collection. Les collections peuvent couvrir une ou plusieurs partitions/serveurs et peuvent être mises à l’échelle pour gérer des volumes de stockage ou de débit quasi-illimités.
|Procédure stockée |Logique d'application écrite en JavaScript, inscrite avec une collection et exécutée via des transactions au sein du moteur de base de données.
|Déclencheur |Logique d'application écrite en JavaScript exécutée avant ou après une opération d'insertion, de remplacement ou de suppression.
|Fonctions définies par l'utilisateur |Logique d'application écrite en JavaScript. Les fonctions définies par l'utilisateur permettent de modéliser un opérateur de requête personnalisé et ainsi d'étendre le langage de requête DocumentDB principal.
|Document |Contenu JSON (arbitraire) défini par l'utilisateur. Par défaut, il n'est pas nécessaire de définir des schémas, ni de fournir des index secondaires pour tous les documents ajoutés à une collection.
|(Version préliminaire) Pièce jointe |Les pièces jointes sont des documents spéciaux contenant des références et les métadonnées associées pour un élément multimédia/objet blob externe. Le développeur peut choisir de laisser DocumentDB gérer l'objet blob ou de le stocker avec un fournisseur de service de stockage d'objets blob tel que OneDrive, Dropbox, etc. 


## Ressources système ou ressources définies par l'utilisateur
Les ressources (telles que les comptes de base de données, les bases de données, les collections, les utilisateurs, les autorisations, les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur) ont toutes un schéma fixe et sont nommées « ressources système ». Par contre, les ressources telles que les documents et les pièces jointes n'ont pas de restriction de schéma et sont des exemples de ressources définies par l'utilisateur. Dans DocumentDB, les ressources système et définies par l'utilisateur sont représentées et gérées en tant qu'éléments JSON standard. Toutes les ressources, tant système que définies par l’utilisateur, ont les propriétés communes suivantes.

> [AZURE.NOTE] Toutes les propriétés générées par le système dans une ressource ont comme préfixe un trait de soulignement (\_) dans leur représentation JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriété</strong></p></td>
            <td valign="top"><p><strong>Définie par l’utilisateur ou générée par le système</strong></p></td>
            <td valign="top"><p><strong>Objectif</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Générée par le système</p></td>
            <td valign="top"><p>Identificateur hiérarchique, unique et généré par le système de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Générée par le système</p></td>
            <td valign="top"><p>etag de la ressource nécessaire au contrôle d'accès concurrentiel optimiste</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Générée par le système</p></td>
            <td valign="top"><p>Dernier horodatage mis à jour de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Générée par le système</p></td>
            <td valign="top"><p>URI adressable unique de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Générée par le système</p></td>
            <td valign="top"><p>Nom unique défini par l'utilisateur de la ressource (avec la même valeur de clé partition). Si l'utilisateur ne spécifie pas d'ID, un ID sera généré par le système</p></td>
        </tr>
    </tbody>
</table>

### Représentation en réseau des ressources
DocumentDB n'oblige pas les extensions propriétaires à adopter la norme JSON ou des codages spéciaux ; l'application fonctionne avec les documents JSON standard.
 
### Adressage d'une ressource
Toutes les ressources sont adressables via des URI. La valeur de la propriété **\_self** d’une ressource représente l’URI relatif de la ressource. Le format de l’URI est composé des segments de chemin d’accès /<flux>/{\_rid} :

|Valeur de la propriété \_self |Description
|-------------------|-----------
|/dbs |Flux de bases de données sous un compte de base de données
|/dbs/{dbName} |Base de données avec ID correspondant à la valeur {dbName}
|/dbs/{dbName}/colls/ |Flux de collections dans une base de données
|/dbs/{dbName}/colls/{collName} |Collection avec ID correspondant à la valeur {collName}
|/dbs/{dbName}/colls/{collName}/docs |Flux de documents dans une collection
|/dbs/{dbName}/colls/{collName}/docs/{docId} |Document avec ID correspondant à la valeur {doc}
|/dbs/{dbName}/users/ |Flux d'utilisateurs d'une base de données
|/dbs/{dbName}/users/{userId} |Utilisateur avec ID correspondant à la valeur {user}
|/dbs/{dbName}/users/{userId}/permissions |Flux d'autorisations d'un utilisateur
|/dbs/{dbName}/users/{userId}/permissions/{permissionId} |Autorisation avec ID correspondant à la valeur {permission}
  
Chaque ressource a un nom défini par l'utilisateur unique exposé à l'aide de la propriété d'ID. Remarque : si l'utilisateur ne spécifie pas d'ID de documents, le système génère automatiquement un ID unique pour un document. L'ID est une chaîne de 256 caractères maximum, définie par l'utilisateur et unique dans le contexte d'une ressource parent spécifique.

Chaque ressource a également un identificateur de ressource hiérarchique défini par l'utilisateur (également appelé RID) qui est disponible via la propriété \_rid. Le RID encode la hiérarchie entière d'une ressource donnée. C'est une représentation interne pratique qui permet d'appliquer l'intégrité référentielle de manière distribuée. Le RID est unique au sein d'un compte de base de données. Il est utilisé en interne par DocumentDB pour un routage efficace sans nécessiter de recherche parmi des partitions. Les valeurs des propriétés \_self et \_rid sont des représentations secondaires et canoniques d'une ressource.

Les API REST de DocumentDB prennent en charge l'adressage des ressources et le routage des requêtes par les propriétés id et \_rid.

## Comptes de base de données
Votre abonnement Azure vous permet d’approvisionner un ou plusieurs comptes de base de données DocumentDB.

Vous pouvez [créer et gérer des comptes de base de données DocumentDB](documentdb-create-account.md) via le portail Azure, à l’adresse [http://portal.azure.com/](https://portal.azure.com/). La création et la gestion d’un compte de base de données requièrent un accès administrateur et peuvent uniquement être effectuées avec votre abonnement Azure.

### Propriétés des comptes de base de données
Dans le cadre de l'approvisionnement et de la gestion d'un compte de base de données, vous pouvez configurer et lire les propriétés suivantes :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nom de la propriété</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Stratégie de cohérence</p></td>
            <td valign="top"><p>Définissez cette propriété pour configurer le niveau de cohérence par défaut pour toutes les collections sous votre compte de base de données. Vous pouvez changer le niveau de cohérence en fonction de la demande en utilisant l’en-tête de demande [x-ms-consistency-level]. <p><p>Notez que cette propriété ne s'applique qu'aux <i>ressources définies par l'utilisateur</i>. Toutes les ressources définies par le système sont configurées pour prendre en charge les requêtes/lectures avec une cohérence forte.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clés d’autorisation</p></td>
            <td valign="top"><p>Les clés principales et secondaires en lecture seule fournissent un accès administratif à toutes les ressources sous le compte de base de données.</p></td>
        </tr>
    </tbody>
</table>

Notez qu’en plus d’approvisionner, de configurer et de gérer votre compte de base de données à partir du portail Azure, vous pouvez créer et gérer des comptes de base de données DocumentDB par programme en utilisant les [API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx), ainsi que des [Kits de développement logiciel (SDK) clients](https://msdn.microsoft.com/library/azure/dn781482.aspx).

## Bases de données
Une base de données DocumentDB est un conteneur logique d'une ou plusieurs collections et d'un ou plusieurs utilisateurs, comme l'illustre le schéma suivant. Vous pouvez créer n'importe quel nombre de bases de données sous un compte de base de données DocumentDB en fonction des limites de l'offre.

![Compte de base de données et modèle hiérarchique de regroupements][2] **Une base de données est un conteneur logique d’utilisateurs et de collections**

Une base de données peut contenir un stockage de documents pratiquement illimité, partitionné en collections, qui forment les domaines de transaction pour les documents qu'elles contiennent.

### Évolutivité flexible d'une base de données DocumentDB
Une base de données DocumentDB est flexible par défaut : elle peut aller de quelques Go à plusieurs pétaoctets de stockage de documents SSD et de débit approvisionné.

Contrairement à une base de données de SGBDR classique, une base de données DocumentDB n'est pas étendue à un seul ordinateur. Avec DocumentDB, vous pouvez créer plus de collections et/ou de bases de données à mesure que les besoins d'extensibilité de votre application augmentent. En effet, plusieurs applications de Microsoft utilisent DocumentDB à l'échelle du client en créant des bases de données DocumentDB très volumineuses, contenant chacune des milliers de collections regroupant des téraoctets de stockage de documents. Vous pouvez augmenter ou réduire la taille d'une base de données en ajoutant ou en supprimant des collections pour répondre aux besoins d'extensibilité de vos applications.

Vous pouvez créer autant de collections que vous voulez dans une base de données, en fonction de l’offre. Chaque collection dispose d’un stockage SSD et d’un débit approvisionné pour vous en fonction du niveau de performances sélectionné.

Une base de données DocumentDB est également un conteneur d'utilisateurs. De même, un utilisateur est un espace de noms logique pour un ensemble d'autorisations qui permet d'obtenir une autorisation et un accès affinés aux collections, documents et pièces jointes.
 
Comme pour les autres ressources du modèle de ressource DocumentDB, les bases de données peuvent être facilement créées, remplacées, supprimées, lues ou répertoriées avec des [API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou l’un des [Kits de développement logiciel (SDK) clients](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garantit une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une ressource de base de données. La suppression d'une base de données garantit automatiquement que vous ne pouvez pas accéder ni aux collections ni aux utilisateurs qui y sont inclus.

## Collections
Une collection DocumentDB est un conteneur pour vos documents JSON. Une collection est également une unité de mise à l'échelle pour les transactions et les requêtes.

### Stockage de documents SSD flexible
Une collection est intrinsèquement flexible : elle augmente ou diminue à mesure que vous ajoutez ou supprimez des documents. Les collections sont des ressources logiques. Elles peuvent s’étendre sur plusieurs partitions physiques ou serveurs. Le nombre de partitions dans une collection est déterminé par DocumentDB en fonction de la taille de stockage et du débit approvisionné de votre collection. Chaque partition dans DocumentDB dispose d’une quantité fixe de stockage SSD associé. Elle est également répliquée pour offrir une haute disponibilité. La gestion des partitions est entièrement exécutée par Azure DocumentDB. Vous n’avez pas à écrire du code complexe, ni à gérer vos partitions. Les collections DocumentDB sont **pratiquement illimitées** en termes de débit et de stockage.

### Indexation automatique des collections
DocumentDB est un véritable système de base de données sans schéma. Il ne part pas du principe que vous utilisez des schémas et n'en réclame pas pour les documents JSON. Dès que vous ajoutez des documents à une collection, DocumentDB les indexe automatiquement et vous pouvez les interroger. L'indexation automatique de documents sans schéma ou index secondaire est une fonctionnalité clé de DocumentDB. Elle est rendue possible par des techniques offrant une maintenance d'index structurée par des journaux, sans verrouillage et optimisée pour l'écriture. DocumentDB prend en charge des volumes soutenus d'écritures très rapides, tout en continuant de servir des requêtes cohérentes. Les stockages de documents et d'index permettent de calculer le stockage consommé par chaque collection. Vous pouvez contrôler le stockage et les compromis de performances associés à l'indexation en configurant la stratégie d'indexation d'une collection.

### Configuration de la stratégie d'indexation d'une collection
La stratégie d'indexation de chaque collection vous permet d'établir des compromis entre les performances et les stockages associés à l'indexation. Les options de configuration d'indexation suivantes sont disponibles :

-	Choisissez si la collection indexe ou non automatiquement tous les documents. Par défaut, tous les documents sont automatiquement indexés. Vous pouvez choisir de désactiver l'indexation automatique et d'ajouter de façon sélective uniquement certains documents à l'index. À l'inverse, vous pouvez choisir d'exclure certains documents. Pour ce faire, définissez la propriété automatique sur True ou False dans le paramètre indexingPolicy d'une collection et utilisez l'en-tête de demande [x-ms-indexingdirective] lors de l'insertion, du remplacement ou de la suppression d'un document.
-	Sélectionnez l'inclusion ou l'exclusion de chemins d'accès ou de modèles spécifiques dans vos documents à partir de l'index. Pour ce faire, définissez les paramètres includedPaths et excludedPaths à partir du paramètre indexingPolicy d'une collection. Vous pouvez également configurer les compromis de performances et de stockage pour les requêtes de plage de données et de hachage pour certains formats de chemin d'accès.
-	Choisissez entre des mises à jour d'index synchrones (cohérentes) ou asynchrones (différées). Par défaut, l'index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d'un document au niveau de la collection. Cela permet aux requêtes de fournir le même niveau de cohérence que celui des lectures de document. Alors que DocumentDB est optimisé pour les écritures et prend en charge des volumes soutenus d'écritures de documents, la maintenance d'index synchrone et les requêtes cohérentes, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l'index soit effectuée en différé. L'indexation en différé dynamise les performances en termes d'écriture. Cela est idéal pour les scénarios d'ingestion en bloc pour les collections comportant principalement beaucoup de lectures.

La stratégie d'indexation peut être modifiée en exécutant une commande PUT dans la collection. Pour cela, vous pouvez utiliser le [kit de développement logiciel client](https://msdn.microsoft.com/library/azure/dn781482.aspx), le [portail Azure](https://portal.azure.com) ou les [API REST DocumentDB Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### Interrogation d'une collection
Les documents d'une collection peuvent suivre des schémas arbitraires et vous pouvez les interroger sans fournir de schéma ou d'index secondaires à l'avance. Vous pouvez interroger la collection en utilisant la [syntaxe SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), qui fournit des opérateurs hiérarchiques, relationnels et spatiaux enrichis ainsi qu’une extensibilité à l’aide des fonctions JavaScript définies par l’utilisateur. La syntaxe JSON permet la modélisation de documents JSON en tant qu'arborescences avec des étiquettes, comme les nœuds d'arborescence. Cette capacité est exploitée par les techniques d'indexation automatique de DocumentDB et par le dialecte SQL de DocumentDB. Le langage de requête DocumentDB est caractérisé par trois aspects principaux :

1.	Un ensemble réduit d'opérations de requête mappant naturellement vers l'arborescence incluant des requêtes hiérarchiques et des projections.
2.	Un sous-ensemble d'opérations relationnelles incluant la composition, le filtrage, les projections, les agrégations et les jointures réflexives.
3.	Des fonctions définies par l'utilisateur JavaScript pures, qui fonctionnent avec (1) et (2).

Le modèle de requête DocumentDB tente de garantir un équilibre entre les fonctionnalités, l’efficacité et la simplicité. Le moteur de base de données de DocumentDB compile et exécute de façon native les instructions de requête SQL. Vous pouvez interroger une collection en utilisant les [API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou l’un des [Kits de développement logiciel (SDK) clients](https://msdn.microsoft.com/library/azure/dn781482.aspx). Le Kit de développement logiciel (SDK) .NET comprend un fournisseur LINQ.

> [AZURE.TIP] Vous pouvez essayer DocumentDB et exécuter des requêtes SQL sur notre jeu de données dans le [Query Playground](https://www.documentdb.com/sql/demo).

### Transactions multi-documents
Les transactions de bases de données offrent un modèle de programmation prédictif et sécurisé pour la gestion des modifications simultanées des données. Dans SGBDR, la méthode classique d'écriture de logique métier consiste à écrire des **procédures stockées** et/ou des **déclencheurs** puis à les expédier vers le serveur de base de données pour exécuter la transaction. Dans SGBDR, le programmateur de l'application doit gérer deux langages de programmation différents :

- le langage de programmation d'application (non transactionnel) (par exemple, JavaScript, Python, C#, Java, etc.) ;
- T-SQL, le langage de programmation transactionnel exécuté de manière native par la base de données.

En vertu de son engagement ferme vis-à-vis de JavaScript et JSON directement dans le moteur de base de données, DocumentDB fournit un modèle de programmation intuitif pour l'exécution de logiques d'application JavaScript directement sur les collections en termes de procédures stockées et de déclencheurs. Cela permet les deux opérations suivantes :

- la mise en œuvre efficace simultanée du contrôle, de la récupération, de l'indexation automatique des graphiques d'objet JSON directement dans le moteur de base de données ;
- l'expression naturelle du flux de contrôle, l'étendue variable, attribution et l'intégration directes des primitives de gestion des exceptions directement en termes du langage de programmation JavaScript.

La logique JavaScript enregistrée au niveau d'une collection peut alors émettre des opérations de base de données vers les documents d'une collection donnée. DocumentDB inclut implicitement les procédures stockées et les déclencheurs JavaScript dans des transactions ACID ambiantes avec un isolement de capture instantanée dans les documents d'une collection. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée. Le modèle de programmation qui en résulte est simple mais puissant. Les développeurs JavaScript obtiennent un modèle de programmation « durable » tout en continuant d'utiliser les constructions de langage et les primitives de bibliothèques qui leurs sont familières.

La possibilité d'exécuter directement JavaScript dans le moteur de base de données au sein du même espace d'adressage que le pool de mémoires tampons permet une exécution performante et transactionnelle des opérations de base de données sur les documents d'une collection. De plus, l'engagement ferme du moteur de base de données DocumentDB envers JSON et JavaScript élimine tout risque d'incohérence en matière d'impédance entre les systèmes de type d'application et la base de données.

Après avoir créé une collection, vous pouvez enregistrer des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur dans cette collection en utilisant les [API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou l’un des [Kits de développement logiciel (SDK) clients](https://msdn.microsoft.com/library/azure/dn781482.aspx). Après l'enregistrement, vous pouvez les référencer et les exécuter. Prenons comme exemple la procédure stockée suivante écrite entièrement en JavaScript, le code ci-dessous prend deux arguments (un titre de livre et un nom d'auteur) et crée un document, interroge un document et le met à jour, le tout dans le cadre d'une transaction ACID implicite. Si le JavaScript lève une exception durant son exécution, l'intégralité de la transaction est annulée.

	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();        
	    var collectionLink = collectionManager.getSelfLink()
	        
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        {id: name, author: author},
	        function(err, documentCreated) {
	            if(err) throw new Error(err.message);
	            
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function(err, matchingDocuments) {
	                    if(err) throw new Error(err.message);
	                    
	                    context.getResponse().setBody(matchingDocuments.length);
	                   
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don’t need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);   
	                    }
	                })
	        })
	};

Le client peut « expédier » la logique JavaScript ci-dessus à la base de données pour une exécution transactionnelle via HTTP POST. Pour plus d’informations sur l’utilisation de méthodes HTTP, consultez l’article [Interactions RESTful avec les ressources DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

	client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
	   .then(function(createdStoredProcedure) {
	        return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
	            "NoSQL Distilled",
	            "Martin Fowler");
	    })
	    .then(function(result) {
	        console.log(result);
	    },
	    function(error) {
	        console.log(error);
	    });


Tenez compte du fait que, comme la base de données comprend de manière native les langages JSON et JavaScript, il n'y a pas de risque d'incohérence de système de type, pas de « mappage OR » ou de génération de code requise.

Les procédures stockées et les déclencheurs interagissent avec une collection et les documents d'une collection à travers un modèle d'objet bien défini qui expose le contexte de collection actuel.

Dans DocumentDB, les collections peuvent être facilement créées, supprimées, lues ou répertoriées avec les [API REST Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou l’un des [Kits de développement logiciel (SDK) clients](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB fournit toujours une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une collection. La suppression d'une collection garantit automatiquement que vous ne pouvez pas accéder aux documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l'utilisateur qu'elle contient.

## Procédures stockées, déclencheurs et fonctions définies par l’utilisateur
Comme décrit dans la section précédente, vous pouvez écrire une logique d'application pour qu'elle s'exécute directement dans une transaction dans le moteur de base de données. La logique d'application peut être entièrement écrite en JavaScript et modélisée en tant que procédure stockée, déclencheur ou fonction, définie par l'utilisateur. Le code JavaScript d'une procédure stockée ou d'un déclencheur peut insérer, remplacer, supprimer, lire ou interroger les documents d'une collection. D'autre part, le code JavaScript dans une fonction définie par l'utilisateur ne peut pas insérer, remplacer ou supprimer des documents. Les fonctions définies par l'utilisateur énumèrent les documents du jeu de résultats d'une requête et produisent un autre jeu de résultats. DocumentDB applique une gouvernance des ressources basée sur une réservation stricte aux architectures mutualisées. Chaque procédure stockée, déclencheur ou fonction définie par l'utilisateur obtient un quantum fixe de ressources de systèmes d'exploitation pour effectuer ses tâches. De plus, les procédures stockées, les déclencheurs ou les fonctions définies par l'utilisateur ne peuvent pas créer de liens vers les bibliothèques JavaScript externes et sont placés sur liste noire s'ils dépassent les budgets de ressources qui leurs sont alloués. Vous pouvez enregistrer ou annuler l'enregistrement de procédures stockées, déclencheurs ou fonctions définies par l'utilisateur dans une collection en utilisant des API REST. Une fois enregistrés, ils sont précompilés et stockés en tant que codes d'octets et sont exécutés ultérieurement. La section suivante illustre l'utilisation du Kit de développement logiciel (SDK) JavaScript de DocumentDB pour enregistrer, exécuter et annuler l'enregistrement d'une procédure stockée, d'un déclencheur et d'une fonction définie par l'utilisateur. Le Kit de développement logiciel (SDK) JavaScript est un wrapper simple des [API REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### Enregistrement d'une procédure stockée
L'enregistrement d'une procédure stockée crée une ressource de procédure stockée dans une collection via HTTP POST.

	var storedProc = {
	    id: "validateAndCreate",
	    body: function (documentToCreate) {
	        documentToCreate.id = documentToCreate.id.toUpperCase();
	        
	        var collectionManager = getContext().getCollection();
	        collectionManager.createDocument(collectionManager.getSelfLink(),
	            documentToCreate,
	            function(err, documentCreated) {
	                if(err) throw new Error('Error while creating document: ' + err.message;
	                getContext().getResponse().setBody('success - created ' + 
	                        documentCreated.name);
	            });
	    }
	};
	
	client.createStoredProcedureAsync(collection._self, storedProc)
	    .then(function (createdStoredProcedure) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

### Exécution d'une procédure stockée
L'exécution d'une procédure stockée s'effectue grâce à l'émission d'une instruction HTTP POST sur une ressource de procédure stockée existante en transmettant les paramètres vers la procédure dans le corps de la demande.

	var inputDocument = {id : "document1", author: "G. G. Marquez"};
	client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
	    .then(function(executionResult) {
	        assert.equal(executionResult, "success - created DOCUMENT1");
	    }, function(error) {
	        console.log("Error");
	    });

### Annulation de l'enregistrement d'une procédure stockée
Pour annuler l’enregistrement d’une procédure stockée, il suffit d’émettre une instruction HTTP DELETE sur une ressource de procédure stockée existante.

	client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
	    .then(function (response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });


### Enregistrement d'un pré-déclencheur
L'enregistrement d'un déclencheur s'effectue en créant une ressource de déclencheur dans une collection via HTTP POST. Vous pouvez spécifier si le déclencheur est un pré- ou post-déclencheur, ainsi que le type d'opération qui lui est associée (par exemple Create, Replace, Delete ou All).

	var preTrigger = {
	    id: "upperCaseId",
	    body: function() {
	            var item = getContext().getRequest().getBody();
	            item.id = item.id.toUpperCase();
	            getContext().getRequest().setBody(item);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.All
	}
	
	client.createTriggerAsync(collection._self, preTrigger)
	    .then(function (createdPreTrigger) {
	        console.log("Successfully created trigger");
	    }, function(error) {
	        console.log("Error");
	    });

### Exécution d'un pré-déclencheur
L'exécution d'un déclencheur s'effectue en spécifiant le nom d'un déclencheur existant au moment de l'émission de la demande POST/PUT/DELETE d'une ressource de document via l'en-tête de demande.
 
	client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
	    .then(function(createdDocument) {
	        assert.equal(createdDocument.resource.id, "DOC1");
	    }, function(error) {
	        console.log("Error");
	    });

### Annulation de l'enregistrement d'un pré-déclencheur
Pour annuler l’enregistrement d’un déclencheur, il suffit d’émettre une instruction HTTP DELETE sur une ressource de déclencheur existante.

	client.deleteTriggerAsync(createdPreTrigger._self);
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

### Enregistrement d'une fonction définie par l'utilisateur
L'enregistrement d'une fonction définie par l'utilisateur s'effectue en créant une ressource de fonction définie par l'utilisateur dans une collection via HTTP POST.

	var udf = { 
	    id: "mathSqrt",
	    body: function(number) {
	            return Math.sqrt(number);
	    },
	};
	client.createUserDefinedFunctionAsync(collection._self, udf)
	    .then(function (createdUdf) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

### Exécution d'une fonction définie par l'utilisateur dans le cadre d'une requête
Vous pouvez spécifier une fonction définie par l’utilisateur dans le cadre d’une requête SQL et l’utiliser pour étendre le [langage de requête SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx).

	var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
	client.queryDocuments(collection._self, filterQuery).toArrayAsync();
	    .then(function(queryResponse) {
	        var queryResponseDocuments = queryResponse.feed;
	    }, function(error) {
	        console.log("Error");
	    });

### Annulation de l'enregistrement d'une fonction définie par l'utilisateur 
Pour annuler l’enregistrement d’une fonction définie par l’utilisateur, il suffit d’émettre une instruction HTTP DELETE sur une ressource de fonction définie par l’utilisateur existante.

	client.deleteUserDefinedFunctionAsync(createdUdf._self)
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

Bien que les extraits de code précédents aient montré l’enregistrement (POST), l’annulation de l’enregistrement (PUT), la lecture ou le tri (GET) et l’exécution (POST) via le [Kit de développement logiciel (SDK) JavaScript de DocumentDB](https://github.com/Azure/azure-documentdb-js), vous pouvez également utiliser les [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou d’autres [Kits clients](https://msdn.microsoft.com/library/azure/dn781482.aspx).

## Documents
Vous pouvez insérer, remplacer, supprimer, lire, énumérer et interroger arbitrairement des documents JSON dans une collection. DocumentDB n'impose aucun schéma et ne requiert pas d'index secondaire pour prendre en charge l'interrogation des documents dans une collection.

En étant un service de base de données véritablement ouvert, DocumentDB n'invente pas de types de données spécialisés (par exemple, les valeurs de date et heure) ou d'encodage spécifique pour les documents JSON. Notez que DocumentDB ne requiert aucune convention JSON spéciale pour codifier les relations entre les différents documents. La syntaxe SQL de DocumentDB fournit des opérateurs de requête hiérarchiques et relationnels très puissants qui permettent d'interroger et de projeter des documents sans annotation spécifique ou obligation de codifier des relations entre les documents à l'aide de propriétés distinctes.
 
Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire, énumérer et interroger facilement les documents en utilisant les API REST ou l’un des [Kits de développement logiciel (SDK) clients](https://msdn.microsoft.com/library/azure/dn781482.aspx). La suppression d'un document libère instantanément le quota correspondant à toutes les pièces jointes imbriquées. Le niveau de cohérence de lecture des documents respecte la stratégie de cohérence du compte de base de données. Vous pouvez remplacer cette stratégie en fonction de la demande, selon les besoins de cohérence des données de votre application. Lors d'une interrogation de documents, la cohérence de lecture respecte le mode d'indexation défini pour la collection. Par « cohérence », on entend la stratégie de cohérence du compte.

## Pièces jointes et éléments multimédias
>[AZURE.NOTE] Les pièces jointes et les ressources média sont des fonctionnalités en version préliminaire.
 
DocumentDB vous permet de stocker des objets blob ou des éléments multimédias binaires dans son propre magasin ou votre magasin d'éléments multimédias distant. Il vous permet également de représenter les métadonnées d'un élément multimédia dans les termes d'un document spécifique nommé « pièce jointe ». Dans DocumentDB, une pièce jointe correspond à un document (JSON) spécifique qui référence l'objet blob ou l'élément multimédia stocké ailleurs. Une pièce jointe est tout simplement un document spécifique qui capture les métadonnées (comme l'emplacement, l'auteur, etc.) d'un élément multimédia stocké dans un magasin d'éléments multimédias distant.

Imaginez une application de lecture sociale utilisant DocumentDB pour stocker des annotations manuscrites et des métadonnées incluant des commentaires, des recommandations, des signets, des évaluations, des « j'aime/je n'aime pas », etc. associés au livre électronique d'un utilisateur donné.

-	Le contenu du livre est stocké dans le stockage d'éléments multimédias, qui est disponible dans le compte de base de données de DocumentDB ou dans un magasin d'éléments multimédias distant.
-	Une application peut stocker chaque métadonnée de l'utilisateur dans un document distinct (par exemple, les métadonnées de Jean pour le livre1 sont stockées dans un document référencé par le chemin /colls/jean/docs/livre1).
-	Les pièces jointes pointant vers les pages du contenu du livre d'un utilisateur sont stockées dans le document correspondant, par exemple /colls/jean/docs/livre1/chapitre1, /colls/jean/docs/livre1/chapitre2 etc.

Notez que les exemples répertoriés ci-dessus utilisent des ID conviviaux pour communiquer les ressources de façon hiérarchique. Vous pouvez accéder aux ressources avec des API REST via des ID de ressources uniques.

Pour les éléments multimédias gérés par DocumentDB, la propriété \_media de la pièce jointe va référencer les éléments multimédias en suivant leur URI. DocumentDB va veiller à nettoyer les éléments multimédias lorsque toutes les références en suspens sont supprimées. DocumentDB génère automatiquement les pièces jointes lorsque vous téléchargez les nouveaux éléments multimédias et renseigne la propriété \_media pour pointer vers l'élément multimédia récemment ajouté. Si vous choisissez de stocker l'élément multimédia dans un magasin d'objets blob distant que vous gérez (comme OneDrive, Azure Storage, DropBox, etc.), vous pouvez toujours utiliser les pièces jointes pour le référencer. Dans ce cas, vous créez la pièce jointe vous-même et renseignez sa propriété \_media manuellement.

Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire ou énumérer facilement les pièces jointes en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. Comme pour les documents, le niveau de cohérence de lecture des pièces jointes respecte la stratégie de cohérence du compte de base de données. Vous pouvez remplacer cette stratégie en fonction de la demande, selon les besoins de cohérence des données de votre application. Lors d'une interrogation de pièces jointes, la cohérence de lecture respecte le mode d'indexation défini pour la collection. Par « cohérence », on entend la stratégie de cohérence du compte.  
## Utilisateurs
Un utilisateur de DocumentDB correspond à un espace de noms logique pour le regroupement des autorisations. Un utilisateur de DocumentDB peut correspondre à un utilisateur dans un système de gestion d'identité ou à un rôle d'application prédéfini. Pour DocumentDB, un utilisateur représente simplement une donnée abstraite pour grouper un ensemble d'autorisations dans une base de données.

Pour implémenter une architecture mutualisée dans votre application, vous pouvez créer des utilisateurs dans DocumentDB qui correspondent à vos utilisateurs actuels ou aux clients de votre application. Vous pouvez ensuite créer des autorisations pour un utilisateur donné correspondant au contrôle d'accès de divers collections, documents, pièces jointes, etc.

Lorsque vous devez faire évoluer vos applications en fonction de la croissance de vos utilisateurs, vous pouvez utiliser plusieurs méthodes pour partitionner vos données. Vous pouvez modéliser chacun de vos utilisateurs comme suit :

-	chaque utilisateur mappe vers une base de données ;
-	chaque utilisateur mappe vers une collection ;
-	faire correspondre les documents de plusieurs utilisateurs à une collection dédiée ;
-	faire correspondre les documents de plusieurs utilisateurs à un ensemble de collections.

Indépendamment de la stratégie de partition que vous choisissez, vous pouvez modéliser vos utilisateurs actuels en tant qu'utilisateurs dans la base de données DocumentDB et associer des autorisations affinées à chaque utilisateur.

![Regroupements d’utilisateurs][3] **Stratégies de partitionnement et modélisation des utilisateurs**

Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire ou énumérer facilement les utilisateurs dans DocumentDB en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. DocumentDB fournit toujours une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une ressource d'utilisateur. Il est intéressant de mentionner que la suppression d'un utilisateur garantit automatiquement que vous ne pouvez pas accéder à l'une des autorisations qu'il contient. Même si DocumentDB réclame le quota d'autorisations dans le cadre de la suppression de l'utilisateur en arrière-plan, les autorisations supprimées sont de nouveau disponibles immédiatement pour utilisation.

## Autorisations
Pour le contrôle d’accès, les ressources comme les comptes de base de données, les bases de données, les utilisateurs et les autorisations sont considérées comme ressources d’*administration*, car elles requièrent des autorisations d’administration. D'un autre côté, les ressources incluant les collections, les documents, les pièces jointes, les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur sont étendues sous une base de données et considérées comme des *ressources d'application*. Le modèle d’autorisation correspondant à ces deux types de ressources et aux rôles qui y accèdent (c’est-à-dire l’administrateur et l’utilisateur) définit deux types de *clés d’accès* : la *clé principale* et la *clé de ressource*. La clé principale appartient au compte de base de données et est fournie au développeur (ou à l'administrateur) qui approvisionne le compte de base de données. Cette clé principale ayant la sémantique d'administration, elle permet d'autoriser l'accès pour les ressources d'administration et d'application. Par contre, une clé de ressource est une clé d'accès granulaire qui permet d'accéder à une ressource d'application *spécifique*. Elle capture donc la relation entre l'utilisateur d'une base de données et les autorisations de l'utilisateur pour une ressource spécifique (comme une collection, un document, une pièce jointe, une procédure stockée, un déclencheur ou une fonction définie par l'utilisateur).

La seule façon d'obtenir une clé de ressource est de créer une ressource d'autorisation pour un utilisateur donné. Notez que pour créer ou récupérer une autorisation, une clé principale doit être présentée dans l'en-tête d'autorisation. Une ressource d'autorisation lie la ressource, son accès et l'utilisateur. Une fois la ressource d'autorisation créée, l'utilisateur doit simplement présenter la clé de ressource associée pour obtenir l'accès à la ressource correspondante. Ainsi, une clé de ressource peut être vue comme une représentation logique et compacte d'une ressource d'autorisation.

Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire ou énumérer facilement les autorisations dans DocumentDB en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. DocumentDB fournit toujours une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une autorisation.

## Étapes suivantes
Pour en savoir plus sur l’utilisation des ressources avec des commandes HTTP, consultez l’article [Interactions RESTful avec les ressources DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

<!---HONumber=AcomDC_0921_2016-->