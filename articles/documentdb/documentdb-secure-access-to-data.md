<properties 
	pageTitle="Apprenez à sécuriser l’accès aux données dans DocumentDB | Microsoft Azure" 
	description="Découvrez les concepts de contrôle d'accès dans DocumentDB, dont les clés principales, les clés en lecture seule, les utilisateurs et les autorisations." 
	services="documentdb" 
	authors="kiratp" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="kipandya"/>

# Sécurisation de l'accès aux données DocumentDB

Cet article fournit une vue d’ensemble de la sécurisation de l’accès aux données stockées dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Après avoir lu cette présentation, vous serez en mesure de répondre aux questions suivantes :

-	Que sont les clés principales DocumentDB ?
-	Que sont les clés en lecture seule DocumentDB ?
-	Que sont les jetons de ressource DocumentDB ?
-	Comment puis-je utiliser les utilisateurs et les autorisations DocumentDB pour sécuriser l'accès aux données DocumentDB ?

## Concepts du contrôle d'accès DocumentDB

DocumentDB fournit des concepts de premier ordre pour contrôler l'accès aux ressources DocumentDB. Aux fins de cette rubrique, les ressources DocumentDB sont divisées en deux catégories :

- Ressources d'administration
	- Compte
	- Base de données
	- Utilisateur
	- Autorisation
- Ressources d'application
	- Collection
	- Offer
	- Document
	- Pièce jointe
	- Procédure stockée
	- Déclencheur
	- Fonction définie par l'utilisateur

Dans le cadre de ces deux catégories, DocumentDB prend en charge trois types de rôle de contrôle d'accès : administrateur de compte, administrateur en lecture seule et utilisateur de base de données. Les droits pour chaque rôle de contrôle d'accès sont les suivants :
 
- Administrateur de compte : accès complet à toutes les ressources (d’administration et d’application) au sein d’un compte DocumentDB donné.
- Administrateur en lecture seule : accès en lecture seule à toutes les ressources (d’administration et d’application) au sein d’un compte DocumentDB donné.
- Utilisateur de la base de données : ressources utilisateur DocumentDB associées à un ensemble spécifique de ressources de base de données DocumentDB (collections, documents, scripts, etc.). Une ou plusieurs ressources utilisateur peuvent être associées à une base de données et chaque ressource utilisateur peut avoir une ou plusieurs autorisations associées.

Le modèle de contrôle d'accès DocumentDB définit trois types de construction d'accès en prenant en compte les catégories et les ressources susmentionnées :

- Clés principales : Lors de la création d’un compte DocumentDB, deux clés principales (primaire et secondaire) sont créées. Ces clés permettent l'accès administratif complet à toutes les ressources au sein du compte DocumentDB.

![Illustration des clés principales DocumentDB](./media/documentdb-secure-access-to-data/masterkeys.png)

- Clés en lecture seule : Lors de la création d’un compte DocumentDB, deux clés en lecture seule (primaire et secondaire) sont créées. Ces clés permettent l'accès en lecture seule à toutes les ressources au sein du compte DocumentDB.

![Illustration des clés en lecture seule DocumentDB](./media/documentdb-secure-access-to-data/readonlykeys.png)

- Jetons de ressource : Un jeton de ressource est associé à une ressource d’autorisation DocumentDB et capture la relation entre l’utilisateur d’une base de données et l’autorisation dont cet utilisateur dispose pour une ressource d’application DocumentDB spécifique (collection, document, etc.).

![Illustration des jetons de ressource DocumentDB](./media/documentdb-secure-access-to-data/resourcekeys.png)

## Utilisation des clés principales et en lecture seule DocumentDB

Comme indiqué précédemment, les clés principales DocumentDB fournissent un accès administratif complet à toutes les ressources au sein d'un compte DocumentDB tandis que les clés en lecture seule permettent l'accès en lecture à toutes les ressources du compte. L'extrait de code suivant indique comment utiliser une clé principale et un point de terminaison de compte DocumentDB pour instancier un DocumentClient et créer une base de données.

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
    private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
    // Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


## Vue d'ensemble des jetons de ressource DocumentDB

Vous pouvez utiliser un jeton de ressource (en créant des utilisateurs et des autorisations DocumentDB) lorsque vous voulez fournir un accès aux ressources dans votre compte DocumentDB à un client qui ne peut pas être approuvé avec la clé principale. Vos clés principales DocumentDB incluent une clé principale et une clé secondaire, qui octroient toutes deux un accès administratif à votre compte et à toutes ses ressources. En exposant l’une ou l’autre de vos clés principales, vous courez le risque d’une utilisation malveillante ou négligente de votre compte.

De même, les clés en lecture seule DocumentDB fournissent un accès en lecture à toutes les ressources (à l'exception des ressources d'autorisation, bien sûr) au sein d'un compte DocumentDB et ne peuvent être utilisées pour fournir un accès plus granulaire à des ressources DocumentDB spécifiques.

Les jetons de ressource DocumentDB offrent une alternative sûre qui permet aux clients de lire, d’écrire et de supprimer des ressources dans votre compte DocumentDB en fonction des autorisations que vous avez octroyées, sans avoir besoin d’une clé principale ou en lecture seule.

Voici un modèle de conception standard dans le cadre duquel des jetons de ressource peuvent être demandés, générés et fournis aux clients :

1. Un service de niveau intermédiaire est configuré pour servir une application mobile pour partager les photos de l'utilisateur.
2. Le service de niveau intermédiaire dispose de la clé principale du compte DocumentDB.
3. L'application photo est installée sur les appareils mobiles des utilisateurs finaux.
4. Lors de la connexion, l'application photo établit l'identité de l'utilisateur avec le service de niveau intermédiaire. Ce mécanisme d'identification dépend totalement de l'application.
5. Une fois l'identité établie, le service de niveau intermédiaire demande des autorisations en fonction de l'identité.
6. Le service de niveau intermédiaire renvoie un jeton de ressource à l'application du téléphone.
7. Cette dernière peut continuer à utiliser le jeton de ressource pour accéder directement aux ressources DocumentDB avec les autorisations définies et pendant l'intervalle autorisé.
8. À expiration du jeton de ressource, les demandes suivantes reçoivent une exception non autorisée 401. L'application du téléphone établit alors de nouveau l'identité de l'utilisateur et demande un nouveau jeton de ressource.

![Flux de travail des jetons de ressource DocumentDB](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

## Utilisation des autorisations et des utilisateurs DocumentDB
Une ressource utilisateur DocumentDB est associée à une base de données DocumentDB. Chaque base de données peut contenir zéro utilisateur DocumentDB ou plus. L'extrait de code suivant indique comment créer une ressource utilisateur DocumentDB.

    //Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);

> [AZURE.NOTE] Chaque utilisateur DocumentDB dispose d'une propriété PermissionsLink qui peut être utilisée pour récupérer la liste des autorisations associées à l'utilisateur.

Une ressource d'autorisation DocumentDB est associée à un utilisateur DocumentDB. Chaque utilisateur peut contenir zéro autorisation DocumentDB ou plus. Une ressource d'autorisation donne accès à un jeton de sécurité dont l'utilisateur a besoin lorsqu'il tente d'accéder à une ressource d'application spécifique. Il y a deux niveaux d'accès disponibles qui peuvent être fournis par une ressource d'autorisation :

- Tout : L’utilisateur dispose de toutes les autorisations sur la ressource.
- Lecture : L’utilisateur peut uniquement lire le contenu de la ressource, mais il ne peut pas procéder à des opérations d’écriture, de mise à jour ou de suppression au niveau de la ressource.


> [AZURE.NOTE] Pour exécuter les procédures stockées DocumentDB, l'utilisateur doit disposer de toutes les autorisations sur la collection dans laquelle la procédure stockée sera exécutée.


L’extrait de code suivant indique comment créer une ressource d’autorisation, lire le jeton de ressource de la ressource d’autorisation et associer les autorisations à l’utilisateur créé ci-dessus.

    // Create a permission.
    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
  docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission); Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
  
Si vous avez spécifié une clé de partition pour votre collection, l’autorisation pour les ressources de collection, de document et de pièce jointe doit inclure l’élément ResourcePartitionKey en plus de l’élément ResourceLink.

Pour obtenir facilement toutes les ressources d'autorisation associées à un utilisateur spécifique, DocumentDB met à disposition un flux d'autorisations pour chaque objet utilisateur. L'extrait de code suivant indique comment récupérer l'autorisation associée à l'utilisateur créé ci-dessus, créer une liste d'autorisations et instancier un nouveau DocumentClient pour l'utilisateur.

    //Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
      UriFactory.CreateUserUri("db", "myUser"));

    List<Permission> permList = new List<Permission>();
      
    foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);

> [AZURE.TIP] Les jetons de ressource ont une durée de validité par défaut d'une heure. La durée de vie du jeton peut cependant être définie de manière explicite (cinq heures maximum).

## Étapes suivantes

- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).
- Pour en savoir plus sur la gestion des clés principales et en lecture seule, cliquez [ici](documentdb-manage-account.md).
- Pour en savoir plus sur la construction des jetons d’autorisation DocumentDB, cliquez [ici](https://msdn.microsoft.com/library/azure/dn783368.aspx).
 

<!---HONumber=AcomDC_0921_2016-->