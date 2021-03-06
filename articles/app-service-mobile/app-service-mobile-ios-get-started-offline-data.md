<properties
	pageTitle="Activation de la synchronisation hors connexion pour votre application Azure Mobile App (iOS)"
	description="Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="krisragh"/>

# Activation de la synchronisation hors connexion pour votre application mobile iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Vue d'ensemble

Ce didacticiel traite de la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour iOS. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le backend distant.

Si vous n’avez aucune expérience d’Azure Mobile Apps, vous devez commencer par suivre le didacticiel [Création d’une application iOS]. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="review-sync"></a>Examiner le code de synchronisation client

Le projet client que vous avez téléchargé pour le didacticiel [Création d’une application iOS] contient déjà du code prenant en charge la synchronisation hors connexion à l’aide d’une base de données Core Data locale. Cette section résume ce qui est déjà inclus dans le code du didacticiel. Pour obtenir une vue d'ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps].

La fonctionnalité de synchronisation hors connexion des données d'Azure Mobile Apps permet aux utilisateurs d'interagir avec une base de données locale lorsque le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous devez initialiser le contexte de synchronisation de `MSClient` et référencer un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `MSSyncTable`.

1. Dans **QSTodoService.m** (Objective-C) ou **ToDoTableViewController.swift** (Swift), notez que le type du membre `syncTable` est `MSSyncTable`. La synchronisation hors connexion utilise cette interface de table de synchronisation à la place de `MSTable`. Quand une table de synchronisation est utilisée, toutes les opérations vont vers le magasin local et sont uniquement synchronisées avec le backend distant à l'aide d'opérations push et pull explicites.

    Pour obtenir une référence à une table de synchronisation, utilisez la méthode `syncTableWithName` sur `MSClient`. Pour supprimer la fonctionnalité de synchronisation hors connexion, utilisez plutôt `tableWithName`.

2. Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. Voici le code correspondant.
	
	**Objective-C** :
	
	Dans la méthode `QSTodoService.init` :
	
	
	        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
	        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
	
	
	**Swift** :
	
	Dans la méthode `ToDoTableViewController.viewDidLoad` :
	
	
	        let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
	        let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
	        self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
	        client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
	

	Cette opération crée un magasin local à l’aide de l’interface `MSCoreDataStore`, fournie dans le Kit de développement logiciel (SDK) Mobile App. Vous pouvez aussi fournir un autre magasin local en implémentant le protocole `MSSyncContextDataSource`.
	
	Par ailleurs, le premier paramètre de `MSSyncContext` est utilisé pour spécifier un gestionnaire de conflits. Étant donné que nous avons passé `nil`, nous obtiendrons le gestionnaire de conflits par défaut, qui échoue en cas de conflit.
	
3. À présent, nous allons exécuter l’opération de synchronisation réelle et obtenir des données à partir du serveur principal distant.

	**Objective-C** :
	
	`syncData` envoie d'abord par push les nouvelles modifications, puis appelle `pullData` pour obtenir des données à partir du serveur principal distant. Ensuite, la méthode `pullData` obtient de nouvelles données qui correspondent à une requête :
	
	
	        -(void)syncData:(QSCompletionBlock)completion
	        {
	            // push all changes in the sync context, then pull new data
	            [self.client.syncContext pushWithCompletion:^(NSError *error) {
	                [self logErrorIfNotNil:error];
	                [self pullData:completion];
	            }];
	        }
	
	        -(void)pullData:(QSCompletionBlock)completion
	        {
	            MSQuery *query = [self.syncTable query];
	
	            // Pulls data from the remote server into the local table.
	            // We're pulling all items and filtering in the view
	            // query ID is used for incremental sync
	            [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
	                [self logErrorIfNotNil:error];
	
	                // Let the caller know that we have finished
	                if (completion != nil) {
	                    dispatch_async(dispatch_get_main_queue(), completion);
	                }
	            }];
	        }
        
        
      **Swift** :
        
        
		func onRefresh(sender: UIRefreshControl!) {
		    UIApplication.sharedApplication().networkActivityIndicatorVisible = true
		    
		    self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
		        (error) -> Void in
		        
		        UIApplication.sharedApplication().networkActivityIndicatorVisible = false
		        
		        if error != nil {
		            // A real application would handle various errors like network conditions,
		            // server conflicts, etc via the MSSyncContextDelegate
		            print("Error: (error!.description)")
		            
		            // We will just discard our changes and keep the servers copy for simplicity
		            if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
		                for opError in opErrors {
		                    print("Attempted operation to item (opError.itemId)")
		                    if (opError.operation == .Insert || opError.operation == .Delete) {
		                        print("Insert/Delete, failed discarding changes")
		                        opError.cancelOperationAndDiscardItemWithCompletion(nil)
		                    } else {
		                        print("Update failed, reverting to server's copy")
		                        opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
		                    }
		                }
		            }
		        }
		        self.refreshControl?.endRefreshing()
		    }
		} 
	
	
	Dans la version Objective-C, dans `syncData`, nous appelons d’abord `pushWithCompletion` sur le contexte de synchronisation. Cette méthode est membre de `MSSyncContext` (plutôt que la table de synchronisation), car elle envoie par push les modifications sur toutes les tables. Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur. Ensuite, l’application d’assistance `pullData` est appelée, et elle appelle `MSSyncTable.pullWithQuery` pour récupérer les données distantes et les stocker dans la base de données locale.
	
	Dans la version Swift, aucun appel n'est effectué vers `pushWithCompletion`. En effet, l'opération push n'était pas strictement nécessaire. S'il existe des modifications en attente dans le contexte de synchronisation pour la table qui effectue une opération push, pull émet toujours d'abord un push. Mais si vous avez plus d'une table de synchronisation, il est préférable d'appeler explicitement push pour vous assurer que tout est cohérent dans les tables associées.
	
	Dans les versions Objective-C et Swift, la méthode `pullWithQuery` vous permet de spécifier une requête pour filtrer les enregistrements que vous souhaitez récupérer. Dans cet exemple, la requête récupère simplement tous les enregistrements de la table `TodoItem` distante.
	
	Le deuxième paramètre `pullWithQuery` est un ID de requête qui est utilisé pour la *synchronisation incrémentielle*. La synchronisation incrémentielle récupère uniquement les enregistrements modifiés depuis la dernière synchronisation, à l'aide de l'horodateur `UpdatedAt` de l'enregistrement, appelé `updatedAt` dans le magasin local. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour refuser la synchronisation incrémentielle, passez `nil` comme ID de requête. Notez que cette opération peut ne pas être très efficace, dans la mesure où elle récupère tous les enregistrements de chaque opération d'extraction.

5. L'application Objective-C se synchronise lorsque nous modifions ou ajoutons des données, lorsqu'un utilisateur effectue le geste d’actualisation, et au lancement. L'application Swift se synchronise lorsqu’un utilisateur effectue le geste d’actualisation et au lancement.

Comme l'application se synchronise lorsque les données sont modifiées (Objective-C) ou à chaque démarrage de l’application (Objective-C et Swift), l’application suppose que l’utilisateur est en ligne. Dans une autre section, nous mettrons à jour l'application afin que les utilisateurs puissent les modifier même lorsqu'ils sont hors connexion.

## <a name="review-core-data"></a>Examen du modèle de données de base

Lorsque vous utilisez un magasin de données de base hors connexion, vous devez définir certaines tables et certains champs dans le modèle de données. L'exemple d'application comprend déjà un modèle de données avec le format correct. Dans cette section, nous allons découvrir ces tables et comment elles sont utilisées.

- Ouvrez **QSDataModel.xcdatamodeld**. Quatre tables sont définies : trois sont utilisées par le Kit de développement logiciel (SDK) et la dernière par les éléments de la tâche :
      * MS\_TableOperations : pour le suivi des éléments qui doivent être synchronisés avec le serveur
      * MS\_TableOperationErrors : pour le suivi des erreurs qui se produisent pendant la synchronisation hors connexion
      * MS\_TableConfig : pour le suivi de la dernière mise à jour de la dernière opération de synchronisation pour toutes les opérations d’extraction.
      * TodoItem : pour le stockage des actions. Les colonnes système **createdAt**, **updatedAt** et **version** sont des propriétés système facultatives.

>[AZURE.NOTE] Le Kit de développement logiciel (SDK) d’Azure Mobile Apps réserve les noms de colonnes commençant par « **``** ». Vous ne devez pas utiliser ce préfixe sur autre chose que les colonnes système. Dans le cas contraire, vos noms de colonnes seront modifiés lors de l'utilisation du backend distant.

- Lorsque vous utilisez la fonctionnalité de synchronisation hors connexion, vous devez définir les tables système comme illustré ci-dessous.

    ### Tables système

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Attribut | Type |
    |----------- |   ------    |
    | id | Integer 64 |
    | itemId | Chaîne |
    | properties | Binary Data |
    | table | Chaîne |
    | tableKind | Integer 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Attribut | Type |
    |----------- |   ------    |
    | id | Chaîne |
    | operationId | Integer 64 |
    | properties | Binary Data |
    | tableKind | Integer 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Attribut | Type |
    |----------- |   ------    |
    | id | Chaîne |
    | key | Chaîne |
    | keyType | Integer 64 |
    | table | Chaîne |
    | value | Chaîne |

    ### Table de données

    **TodoItem**

    | Attribut | Type | Remarque |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | Chaîne, marquée requise | clé primaire dans le magasin distant |
    | terminé | Boolean | champ d'élément todo |
    | texte | Chaîne | champ d'élément todo |
    | createdAt | Date | (facultatif) correspond à la propriété système createdAt |
    | updatedAt | Date | (facultatif) correspond à la propriété système updatedAt |
    | version | Chaîne | (facultatif) permet de détecter les conflits, correspond à version |


## <a name="setup-sync"></a>Modification du comportement de synchronisation de l’application

Dans cette section, vous allez modifier l'application afin qu'elle ne se synchronise pas au démarrage ou lors de l'insertion et de la mise à jour des éléments, mais uniquement lorsque le mouvement d'actualisation est effectué.

**Objective-C** :

1. Dans **QSTodoListViewController.m**, modifiez la méthode **viewDidLoad** pour supprimer l’appel à `[self refresh]` à la fin de la méthode. Maintenant, les données ne seront pas synchronisées avec le serveur au démarrage de l'application, mais seront remplacées par le contenu du magasin local.

2. Dans **QSTodoService.m**, modifiez la définition de `addItem` afin que la synchronisation ne se fasse qu’une fois l’élément inséré. Supprimez le bloc `self syncData` et remplacez-le par ce qui suit :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifiez la définition de `completeItem` comme illustré ci-dessus. Supprimez le bloc de `self syncData` et remplacez-le par ce qui suit :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift** :

1. Dans `viewDidLoad` dans **ToDoTableViewController.swift**, commentez ces deux lignes pour arrêter la synchronisation au démarrage de l’application. Au moment de la rédaction de cet article, l’application Swift Todo ne met pas à jour le service lorsqu’un utilisateur ajoute ou termine un élément, uniquement au démarrage de l’application.

		self.refreshControl?.beginRefreshing()
		self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Test de l’application

Dans cette section, vous allez vous connecter à une URL incorrecte pour simuler un scénario hors connexion. Quand vous ajoutez des éléments de données, ils sont placés dans le magasin local des données de base, mais ne sont pas synchronisés vers le backend mobile.

1. Définissez l’URL de l’application mobile dans **QSTodoService.m** sur une URL non valide, puis réexécutez l’application :

	**Objective-C** dans QSTodoService.m :
	
        	self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
	
	**Swift** dans ToDoTableViewController.swift :

		let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Ajoutez quelques éléments todo. Fermez le simulateur (ou forcez la fermeture de l'application) et redémarrez. Vérifiez que vos modifications ont bien été rendues persistantes.

3. Affichez le contenu de la table TodoItem distante :

    + Pour un backend Node.js, accédez au [portail Azure](https://portal.azure.com/), puis dans votre backend d’application mobile, cliquez sur **Easy Tables** > **TodoItem** pour afficher le contenu de la table `TodoItem`.
   	+ Pour un backend .NET, affichez le contenu de la table avec un outil SQL, par exemple SQL Server Management Studio ou un client REST, comme Fiddler ou Postman.

    Vérifiez que les nouveaux éléments n’ont *pas* été synchronisés avec le serveur :

4. Remplacez l’URL par l’URL correcte dans **QSTodoService.m** et réexécutez l’application. Effectuez l’actualisation en faisant glisser la liste des éléments vers le bas. Vous verrez un compteur de progression.

5. Affichez de nouveau les données TodoItem. Les nouveaux éléments TodoItems et ceux modifiés doivent maintenant s'afficher.

## Résumé

Pour pouvoir prendre en charge la fonctionnalité de synchronisation hors connexion, nous avons utilisé l’interface `MSSyncTable` et initialisé `MSClient.syncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données avec données de base.

Quand vous utilisez un magasin de données de base local, vous devez définir certaines tables avec les [propriétés système correctes](#review-core-data).

Les opérations normales de création, lecture, mise à jour et suppression pour Azure Mobile Apps fonctionnent comme si l’application était toujours connectée, mais toutes les opérations se rapportent au magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé la méthode `MSSyncTable.pullWithQuery`.


## Ressources supplémentaires

* [Synchronisation des données hors connexion dans Azure Mobile Apps]

* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services] \(remarque : le contexte de la vidéo est Mobile Services, mais la synchronisation hors connexion fonctionne de la même manière dans Azure Mobile Apps)

<!-- URLs. -->


[Création d’une application iOS]: ../app-service-mobile-ios-get-started.md
[Synchronisation des données hors connexion dans Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0810_2016-->