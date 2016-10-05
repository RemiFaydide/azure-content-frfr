<properties
	pageTitle="Procédure de mise à niveau du SDK iOS Azure Mobile Engagement | Microsoft Azure"
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Pour chaque nouvelle version du Kit de développement logiciel, vous devez d'abord remplacer (supprimer et importer de nouveau dans xcode) les dossiers EngagementSDK et EngagementReach.

##Migration de 3.0.0 vers 4.0.0

### XCode 8
XCode 8 est obligatoire à partir de la version 4.0.0 du SDK.

> [AZURE.NOTE] Si vous dépendez vraiment de XCode 7, vous pouvez utiliser [iOS SDK Engagement v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu concernant le module Reach cette version précédente lorsqu’elle est exécutée sur des appareils iOS 10 : les notifications système ne sont pas activées. Pour corriger ce problème, vous devez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre délégué d’application comme suit :

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** : ce comportement peut changer dans une prochaine mise à niveau (même mineure) de la version iOS car cette API iOS est déconseillée. Vous devriez passer à XCode 8 dès que possible.

### Infrastructure UserNotifications
Vous devez ajouter l’infrastructure `UserNotifications` à votre onglet Build Phases.

Dans l'Explorateur de projets, ouvrez le volet de votre projet et sélectionnez la cible appropriée. Ouvrez ensuite l'onglet **« Build phases »** et, dans le menu **« Link Binary With Libraries »**, ajoutez l’infrastructure `UserNotifications.framework` - définissez le lien comme `Optional`

### Fonctionnalité push de l’application
XCode 8 peut réinitialiser la fonctionnalité push de votre application : vérifiez ce point dans l’onglet `capability` de votre cible sélectionnée.

### Si vous disposez déjà de votre propre implémentation de UNUserNotificationCenterDelegate

Le Kit de développement logiciel (SDK) a également sa propre implémentation du protocole UNUserNotificationCenterDelegate. Elle est utilisée par le Kit de développement logiciel pour surveiller le cycle de vie des notifications Engagement sur les appareils iOS 10 ou version ultérieure. Si le Kit de développement logiciel détecte votre délégué, il n’utilisera pas sa propre implémentation car il ne peut y avoir qu’un seul délégué UNUserNotificationCenter par application. Cela signifie que vous devrez ajouter la logique Engagement à votre propre délégué.

Il existe deux moyens de parvenir à cet objectif.

Simplement en transférant les appels de votre délégué vers le Kit de développement logiciel :

	#import <UIKit/UIKit.h>
	#import "EngagementAgent.h"
	#import <UserNotifications/UserNotifications.h>


	@interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
	@end

	@implementation MyAppDelegate

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
	}
	@end

Ou en héritant de la classe `AEUserNotificationHandler`

	#import "AEUserNotificationHandler.h"
	#import "EngagementAgent.h"

	@interface CustomUserNotificationHandler :AEUserNotificationHandler
	@end

	@implementation CustomUserNotificationHandler

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
	}

	@end

> [AZURE.NOTE] Vous pouvez déterminer si une notification provient ou non d’Engagement en passant son dictionnaire `userInfo` à la méthode de classe `isEngagementPushPayload:` de l’agent.

##Migration de 2.0.0 vers 3.0.0
Prise en charge d’iOS 4.X abandonnée. À partir de cette version, la cible de déploiement de votre application doit être au moins iOS 6.

Si vous utilisez Reach dans votre application, vous devez ajouter la valeur `remote-notification` au tableau `UIBackgroundModes` dans votre fichier Info.plist pour recevoir des notifications à distance.

La méthode `application:didReceiveRemoteNotification:` doit être remplacée par `application:didReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué d’application.

« AEPushDelegate.h » est une interface déconseillée et vous devez supprimer toutes les références. Cela inclut notamment la suppression `[[EngagementAgent shared] setPushDelegate:self]` et les méthodes de délégation depuis votre délégué d’application :

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##De 1.16.0 à 2.0.0
La section qui suit décrit comment migrer une intégration du SDK à partir du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. Si vous effectuez une migration depuis une version antérieure, veuillez d’abord consulter le site web Capptain pour effectuer une migration vers la version 1.16, puis appliquer la procédure suivante.

>[AZURE.IMPORTANT] Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous explique uniquement comment migrer l'application cliente. La migration du SDK dans l'application ne migre PAS vos données des serveurs Capptain vers les serveurs Engagement Mobile.

### Agent

La méthode `registerApp:` a été remplacée par la nouvelle méthode `init:`. Votre délégué d'application doit être mis à jour en conséquence et utiliser la chaîne de connexion :

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Le suivi SmartAd a été supprimé du Kit de développement logiciel (SDK). Vous devez seulement supprimer toutes les instances de la classe `AETrackModule`

### Modifications de nom de classe

Dans le cadre du repositionnement, quelques classes/noms de fichiers doivent être modifiés.

Toutes les classes avec le préfixe « CP » sont renommées avec le préfixe « AE ».

Exemple :

-   `CPModule.h` est renommé `AEModule.h`.

Toutes les classes avec le préfixe « Capptain » sont renommées avec le préfixe « Engagement ».

Exemples :

-   La classe `CapptainAgent` est renommée `EngagementAgent`.
-   La classe `CapptainTableViewController` est renommée `EngagementTableViewController`.
-   La classe `CapptainUtils` est renommée `EngagementUtils`.
-   La classe `CapptainViewController` est renommée `EngagementViewController`.

<!---HONumber=AcomDC_0921_2016-->