<properties
	pageTitle="Prise en main de la synchronisation des données hors connexion dans Mobile Services (iOS) | Microsoft Azure"
	description="Découvrez comment utiliser Azure Mobile Services pour mettre en cache et synchroniser les données hors connexion dans votre application iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="erikre"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="krisragh;donnam"/>

# Prise en main de la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Activation de la synchronisation hors connexion pour votre application mobile iOS](../app-service-mobile/app-service-mobile-ios-get-started-offline-data.md).

La synchronisation hors connexion permet d'afficher, d'ajouter ou de modifier des données dans une application mobile même en l'absence de connexion réseau. Dans ce didacticiel, vous allez apprendre comment votre application peut automatiquement stocker des modifications dans une base de données locale hors connexion et synchroniser ces modifications chaque fois qu'elle est de nouveau en ligne.

La synchronisation hors connexion présente plusieurs avantages :

* Elle améliore la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil.
* Elle rend les applications résistantes face à une connectivité réseau intermittente.
* Elle vous permet de créer et de modifier des données même si la connectivité est réduite ou nulle.
* Elle synchronise les données sur plusieurs appareils.
* Elle détecte les conflits quand un même enregistrement est modifié par deux appareils.

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir des [services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation](https://azure.microsoft.com/pricing/details/mobile-services/). Pour plus d'informations, consultez la page d'[essai gratuit d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target="\_blank").

Ce didacticiel est basé sur le didacticiel [Démarrage rapide de Mobile Services], que vous devez effectuer en premier. Examinons d'abord le code lié à la synchronisation hors connexion présent dans le Démarrage rapide.

## <a name="review-sync"></a>Examen du code de synchronisation de Mobile Services

La synchronisation hors connexion d'Azure Mobile Services permet aux utilisateurs d'interagir avec une base de données locale lorsque le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous devez initialiser le contexte de synchronisation de `MSClient` et référencer un magasin local. Ensuite, référencez votre table par le biais de l'interface `MSSyncTable`.

* Dans **QSTodoService.m**, notez que le type de membre `syncTable` est `MSSyncTable`. La synchronisation hors connexion l'utilise à la place de `MSTable`. Quand une table de synchronisation est utilisée, toutes les opérations vont vers le magasin local et ne sont synchronisées avec le service distant qu'à l'aide d'opérations push et pull explicites.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

Pour obtenir une référence à une table de synchronisation, utilisez la méthode `syncTableWithName`. Pour supprimer la fonctionnalité de synchronisation hors connexion, utilisez plutôt `tableWithName`.

* Dans **QSTodoService.m**, avant que les opérations de table ne soient effectuées, le magasin local est initialisé dans `QSTodoService.init` :

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

Cette opération crée un magasin local à l'aide de l'interface `MSCoreDataStore`. Vous pouvez fournir un autre magasin local en implémentant le protocole `MSSyncContextDataSource`.

Le premier paramètre d'`initWithDelegate` spécifie un gestionnaire de conflits, mais comme nous avons passé `nil`, nous obtenons le gestionnaire de conflits par défaut, qui échoue sur tout conflit. Pour plus d'informations sur l'implémentation d'un gestionnaire de conflits personnalisé, consultez [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services].

* Dans **QSTodoService.m**, `syncData` envoie d'abord par push les nouvelles modifications, puis appelle `pullData` pour obtenir des données à partir du service distant. Dans `syncData`, nous avons d'abord appelé `pushWithCompletion` sur le contexte de synchronisation. Cette méthode est membre de `MSSyncContext`, plutôt que la table de synchronisation, car elle envoie par push les modifications sur toutes les tables. Seuls les enregistrements modifiés d'une certaine façon en local, par le biais d'opérations de création, de mise à jour ou de suppression, sont envoyés au serveur. À la fin de `syncData`, nous appelons l'application d'assistance `pullData`.

Dans cet exemple, l'opération push n'est pas strictement nécessaire. S'il existe des modifications en attente dans le contexte de synchronisation pour la table qui effectue une opération push, pull commence toujours par émettre un push. Toutefois, si vous avez plus d'une table de synchronisation, appelez push explicitement pour garantir une cohérence entre les tables.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* Ensuite dans **QSTodoService.m**, `pullData` obtient de nouvelles données qui correspondent à une requête. `pullData` appelle `MSSyncTable.pullWithQuery` pour récupérer les données distantes et les stocker localement. `pullWithQuery` permet également de spécifier une requête pour filtrer les enregistrements que vous souhaitez récupérer. Dans cet exemple, la requête récupère simplement tous les enregistrements de la table `TodoItem` distante.

Le deuxième paramètre de `pullWithQuery` est un ID de requête qui est utilisé pour la _synchronisation incrémentielle_. La synchronisation incrémentielle récupère uniquement les enregistrements modifiés depuis la dernière synchronisation, à l'aide de l'horodateur `UpdatedAt` de l'enregistrement, appelé `ms_updatedAt` dans le magasin local. L'ID de requête est une chaîne descriptive unique pour chaque requête logique de votre application. Pour refuser la synchronisation incrémentielle, passez `nil` comme ID de requête. Cela est inefficace dans la mesure où tous les enregistrements de chaque opération d'extraction sont alors récupérés.

```
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
```


>[AZURE.NOTE] Pour supprimer des enregistrements du magasin local de l'appareil quand ils ont été supprimés de la base de données de votre service mobile, activez la [Suppression réversible]. Sinon, votre application doit appeler périodiquement `MSSyncTable.purgeWithQuery` pour vider le magasin local.


* Dans **QSTodoService.m**, les méthodes `addItem` et `completeItem` appellent `syncData` une fois les données modifiées. Dans **QSTodoListViewController.m**, la méthode `refresh` appelle également `syncData` afin que l'interface utilisateur affiche les données les plus récentes à chaque actualisation et au lancement (`init` appelle `refresh`).

Étant donné que l'application appelle `syncData` chaque fois que vous modifiez des données, l'application suppose que vous êtes connecté dès lors que vous modifiez des données dans l'application.

## <a name="review-core-data"></a>Examen du modèle de données de base

Lorsque vous utilisez un magasin de données de base hors connexion, vous devez définir certaines tables et certains champs dans le modèle de données. L'exemple d'application comprend déjà un modèle de données avec le format correct. Dans cette section, nous allons découvrir ces tables et comment elles sont utilisées.

- Ouvrez **QSDataModel.xcdatamodeld**. Quatre tables sont définies, trois étant utilisées par le Kit de développement logiciel (SDK) et la dernière par les éléments de la tâche :

      * MS\_TableOperations : pour le suivi des éléments à synchroniser avec le serveur
      * MS\_TableOperationErrors : pour le suivi des erreurs qui se produisent pendant la synchronisation hors connexion
      * MS\_TableConfig : pour le suivi de la dernière mise à jour de la dernière opération de synchronisation de toutes les opérations d'extraction
      * TodoItem : pour le stockage des éléments de la tâche. Les colonnes système **ms\_createdAt**, **ms\_updatedAt** et **ms\_version** sont des propriétés système facultatives.

>[AZURE.NOTE] Le Kit de développement logiciel (SDK) Mobile Services réserve les noms de colonnes qui commencent par « **`ms_`** ». N'utilisez ce préfixe que sur les colonnes système. Sinon, vos noms de colonnes seront modifiés pendant l'utilisation du service distant.

- Lorsque vous utilisez la fonctionnalité de synchronisation hors connexion, vous devez définir les tables système comme illustré ci-dessous.

    ### Tables système

    #### MS\_TableOperations

    | Attribut | Type |
    |-------------- |   ------    |
    | ID (obligatoire) | Integer 64 |
    | itemId | String |
    | properties | Binary Data |
    | table | String |
    | tableKind | Integer 16 |

    #### MS\_TableOperationErrors

    | Attribut | Type |
    |-------------- | ----------  |
    | ID (obligatoire) | Chaîne |
    | operationId | Integer 64 |
    | properties | Binary Data |
    | tableKind | Integer 16 |

    #### MS\_TableConfig


    | Attribut | Type |
    |-------------- | ----------  |
    | ID (obligatoire) | Chaîne |
    | key | String |
    | keyType | Integer 64 |
    | table | String |
    | value | String |

    ### Table de données

    #### TodoItem

    | Attribut | Type | Remarque |
    |-------------- |  ------ | -------------------------------------------------------|
    | ID (obligatoire) | Chaîne | clé primaire dans le magasin distant (obligatoire) |
    | terminé | Boolean | champ d'élément todo |
    | texte | String | champ d'élément todo |
    | ms\_createdAt | Date | (facultatif) correspond à la propriété système \_\_createdAt |
    | ms\_updatedAt | Date | (facultatif) correspond à la propriété système \_\_updatedAt |
    | ms\_version | String | (facultatif) permet de détecter les conflits, correspond à \_\_version |



## <a name="setup-sync"></a>Modification du comportement de la synchronisation d'une application

Dans cette section, vous modifiez l'application afin qu'elle ne se synchronise pas au démarrage ou à l'occasion de l'insertion et de la mise à jour des éléments, mais uniquement quand le mouvement d'actualisation est effectué.

* Dans **QSTodoListViewController.m**, modifiez `viewDidLoad` pour supprimer l'appel à `[self refresh]` à la fin de la méthode. Désormais, les données ne seront pas synchronisées avec le serveur au démarrage de l'application, mais seront simplement stockées localement.

* Dans **QSTodoService.m**, modifiez `addItem` afin que la synchronisation ne se fasse qu'une fois l'élément inséré. Supprimez le bloc `self syncData` et remplacez-le par ce qui suit :

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* De même, de nouveau dans **QSTodoService.m**, dans `completeItem`, supprimez le bloc `self syncData` et remplacez-le par ce qui suit :

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>Test de l'application

Dans cette section, vous allez désactiver le Wi-Fi dans le simulateur pour créer un scénario hors connexion. Lorsque vous ajoutez des éléments de données, ils sont placés dans le magasin local des données de base, mais ne sont pas synchronisés vers le service mobile.

1. Désactivez la connexion Internet sur votre Mac. La désactivation du Wi-Fi uniquement dans le simulateur iOS peut ne pas avoir d'effet, puisque le simulateur peut toujours utiliser la connexion Internet de l'ordinateur Mac hôte. Donc, désactivez Internet directement sur l'ordinateur. Ceci simule un scénario hors connexion.

2. Ajoutez des tâches ou marquez-en certaines comme terminées. Fermez le simulateur (ou forcez la fermeture de l'application) et redémarrez. Vérifiez que vos modifications ont bien été rendues persistantes. Notez que les éléments de données sont toujours affichés, car ils sont stockés dans le magasin local des données de base.

3. Affichez le contenu de la table TodoItem distante. Vérifiez que les nouveaux éléments n'ont _pas_ été synchronisés avec le serveur.

   - Pour le backend JavaScript, accédez au [portail Azure Classic](http://manage.windowsazure.com) et cliquez sur l'onglet Données pour afficher le contenu de la table `TodoItem`.
   - Pour le serveur principal .NET, affichez le contenu de la table avec un outil SQL, par exemple SQL Server Management Studio ou un client REST, comme Fiddler ou Postman.

4. Activez le Wi-Fi dans le simulateur iOS. Ensuite, effectuez l'actualisation en faisant glisser la liste des éléments vers le bas. Vous verrez un compteur de progression et le texte « Synchronisation... ».

5. Affichez de nouveau les données TodoItem. Les nouveaux éléments TodoItems et ceux modifiés doivent maintenant s'afficher.

## Résumé

Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, vous avez utilisé l'interface `MSSyncTable` et initialisé `MSClient.syncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données avec données de base.

Quand vous utilisez un magasin de données de base local, vous définissez certaines tables avec les [propriétés système correctes][Review the Core Data model]. Les opérations normales des services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur le magasin local.

Pour synchroniser le magasin local avec le serveur, vous avez utilisé `MSSyncTable.pullWithQuery` et `MSClient.syncContext.pushWithCompletion` :

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## Étapes suivantes

* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]

* [Utilisation de la suppression réversible dans Mobile Services][Soft Delete]

## Ressources supplémentaires

* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]

* [Azure Friday : applications prenant en charge le mode hors connexion dans Azure Mobile Services] \(Remarque : les démonstrations s'appuient sur Windows, mais la présentation de cette fonctionnalité s'applique à toutes les plateformes)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[Suppression réversible]: mobile-services-using-soft-delete.md

[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday : applications prenant en charge le mode hors connexion dans Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Démarrage rapide de Mobile Services]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0727_2016-->