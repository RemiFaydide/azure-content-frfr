<properties
	pageTitle="Ajouter la synchronisation des données hors connexion à votre application de services mobiles Android | Microsoft Azure"
	description="Découvrez comment utiliser Azure Mobile Services pour stocker dans le cache et synchroniser des données hors connexion dans votre application Android"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Ajouter la synchronisation des données hors connexion à votre application de services mobiles Android

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Activation de la synchronisation hors connexion pour votre application mobile Android](../app-service-mobile/app-service-mobile-android-get-started-offline-data.md).

## Résumé

Les applications mobiles peuvent perdre la connectivité réseau si vous entrez dans une zone sans service, ou en raison de problèmes réseau. Par exemple, une application du secteur de la construction utilisée sur un site distant peut avoir besoin d’intégrer des données de planification qui seront synchronisées ultérieurement sur Azure. Avec la synchronisation hors connexion Azure Mobile Services, vous pouvez continuer à travailler même sans connectivité réseau, ce qui est primordial pour de nombreuses applications mobiles. Grâce à la synchronisation hors connexion, vous travaillez avec une copie locale de votre table Azure SQL Server et synchronisez régulièrement les deux versions.

Ce didacticiel va vous apprendre à mettre à jour l’application à partir du [didacticiel Démarrage rapide de Mobile Services] pour activer la synchronisation hors connexion, puis à tester l’application en ajoutant des données hors connexion, à synchroniser ces éléments dans la base de données en ligne et à vérifier les modifications apportées dans le portail Azure Classic.

Que vous soyez hors connexion ou connecté, des conflits peuvent survenir chaque fois que de multiples modifications sont apportées aux données. Un futur didacticiel explorera la gestion des conflits de synchronisation, et vous pourrez choisir la version des modifications à accepter. Dans ce didacticiel, nous partons du principe qu’aucun conflit de synchronisation ni aucune modification apportée aux données existantes ne seront appliqués directement à Azure SQL Server.


## Ce dont vous avez besoin pour commencer

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Mettre à jour l’application pour prendre en charge la synchronisation hors connexion

Avec la synchronisation hors connexion, vous disposez d’un accès en lecture et en écriture à partir d’une *table de synchronisation* (à l’aide de l’interface *IMobileServiceSyncTable*), qui fait partie d’une base de données **SQLite** sur votre appareil.

Pour envoyer et extraire des modifications entre l’appareil et Azure Mobile Services, faites appel à un *contexte de synchronisation* (*MobileServiceClient.SyncContext*), que vous initialisez avec la base de données locale utilisée pour stocker des données localement.

1. Ajoutez une autorisation pour vérifier la connectivité réseau en intégrant ce code au fichier *AndroidManifest.xml* \:

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. Ajoutez les instructions d’**importation** suivantes au fichier *ToDoActivity.java* :

		import java.util.Map;

		import android.widget.Toast;

		import com.microsoft.windowsazure.mobileservices.table.query.Query;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. Dans la partie supérieure de la classe `ToDoActivity`, modifiez la déclaration de la variable `mToDoTable` en remplaçant la classe `MobileServiceTable<ToDoItem>` par la classe `MobileServiceSyncTable<ToDoItem>`.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	C'est là que vous définissez la table de synchronisation.

4. Pour gérer l’initialisation du magasin local, dans la méthode `onCreate`, ajoutez les lignes suivantes après avoir créé l’instance `MobileServiceClient` :

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. À la suite de la partie de code précédente, qui se trouve dans un bloc `try`, ajoutez un bloc `catch` après le bloc `MalformedURLException` :

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. Ajoutez cette méthode pour vérifier la connectivité réseau :

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. Ajoutez cette méthode pour synchroniser le magasin local *SQL Light* et Azure SQL Server :

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {

					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. Dans la méthode `onCreate`, ajoutez ce code sur l’avant-dernière ligne, juste avant l’appel à `refreshItemsFromTable` :

			syncAsync();

	Au démarrage, cela entraîne la synchronisation de l’appareil avec la table Azure. Sinon, le dernier contenu hors connexion du magasin local s’affiche.



9. Pour utiliser cette requête, mettez à jour le code dans la méthode `refreshItemsFromTable` (première ligne de code dans le bloc `try`) :

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. Dans la méthode `onOptionsItemSelected`, remplacez le contenu du bloc `if` par ce code :

			syncAsync();
			refreshItemsFromTable();

	Ce code s’exécute lorsque vous appuyez sur le bouton **Actualiser** dans le coin supérieur droit. Outre la synchronisation au démarrage, il s’agit de la principale méthode permettant de synchroniser votre magasin local avec Azure. Elle favorise le traitement des modifications de synchronisation et, étant donné que l’extraction à partir d’Azure est une opération relativement coûteuse, elle s’avère efficace. Vous pouvez également concevoir cette application de manière à ce qu’elle se synchronise lors de chaque modification en ajoutant un appel à `syncAsync` aux méthodes `addItem` et `checkItem`, si besoin.


## Test de l'application

Dans cette section, vous testez le comportement de l’application avec le Wi-Fi activé, puis vous désactivez le Wi-Fi pour créer un scénario hors connexion.

Lorsque vous ajoutez des éléments de données, ils sont stockés dans le magasin local SQL Light, mais ils ne sont synchronisés avec le service mobile que si vous appuyez sur le bouton **Actualiser**. D’autres applications peuvent avoir des besoins différents concernant la fréquence de synchronisation des données, mais à des fins de démonstration, l’utilisateur doit en faire explicitement la requête dans le cadre de ce didacticiel.

Lorsque vous appuyez sur ce bouton, une nouvelle tâche démarre en arrière-plan, et toutes les modifications apportées sont d’abord envoyées dans le magasin local, via le contexte de synchronisation. Ensuite, toutes les données modifiées sont extraites d’Azure vers la table locale.


### Test en ligne

Testez les scénarios suivants :

1. Ajoutez de nouveaux éléments sur votre appareil.
2. Vérifiez que les éléments ne s’affichent pas dans le portail.
3. Appuyez ensuite sur **Actualiser** et vérifiez qu’ils s’affichent à présent.
4. Modifiez ou ajoutez un élément dans le portail, puis appuyez sur **Actualiser** et vérifiez que les modifications s’affichent sur votre appareil.

### Test hors connexion

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. Mettez l’appareil ou le simulateur en *Mode avion*. Cela crée un scénario hors connexion.

2. Ajoutez des *tâches*, ou marquez-en certaines comme terminées. Quittez l’appareil ou le simulateur (ou forcez la fermeture de l’application) et redémarrez. Vérifiez que vos modifications persistent sur l’appareil, car elles sont stockées dans le magasin SQL Light local.

3. Affichez le contenu de la table Azure *TodoItem*. Vérifiez que les nouveaux éléments n’ont _pas_ été synchronisés avec le serveur :

   - Pour le backend JavaScript, accédez au portail Azure Classic et cliquez sur l’onglet Données pour afficher le contenu de la table `TodoItem`.
   - Pour le backend .NET, affichez le contenu de la table avec un outil SQL, par exemple *SQL Server Management Studio* ou un client REST, comme *Fiddler* ou *Postman*.

4. Activez le Wi-Fi sur le simulateur ou l’appareil. Appuyez ensuite sur le bouton **Actualiser**.

5. Affichez à nouveau les données TodoItem sur le portail Azure Classic. Les nouveaux éléments TodoItems et ceux modifiés doivent maintenant s'afficher.


## Étapes suivantes

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


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday : applications prenant en charge le mode hors connexion dans Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[didacticiel Démarrage rapide de Mobile Services]: mobile-services-android-get-started.md

<!---HONumber=AcomDC_0727_2016-->