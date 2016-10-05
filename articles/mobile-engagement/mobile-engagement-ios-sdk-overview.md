<properties
	pageTitle="Vue d’ensemble du Kit de développement logiciel (SDK) Azure Mobile Engagement pour iOS | Microsoft Azure"
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

#Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement

Cliquez ici pour obtenir tous les détails de l'intégration d'Azure Mobile Engagement dans une application iOS. Si vous souhaitez d’abord faire un essai, pensez à suivre notre [didacticiel de 15 minutes](mobile-engagement-ios-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel (SDK)](mobile-engagement-ios-sdk-content.md)

##Procédures d'intégration
1. Commencez ici : [Intégration de Mobile Engagement dans votre application iOS](mobile-engagement-ios-integrate-engagement.md)

2. Pour les notifications : [Intégration de Reach (Notifications) dans votre application iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implémentation du plan de balise : [Utilisation de l'API avancée de balisage de Mobile Engagement dans votre application iOS](mobile-engagement-ios-use-engagement-api.md)


##Notes de publication

### 4\.0.0 (09/12/2016)

-   Résolution de la notification non affichée sur les appareils iOS 10.
-   Utilisation de XCode 7 déconseillée.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-ios-release-notes.md)

##Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Si vous avez manqué plusieurs versions du Kit de développement logiciel (SDK), vous devrez peut-être effectuer plusieurs procédures. Consultez la version complète des [Procédures mises à jour](mobile-engagement-ios-upgrade-procedure.md).

Pour chaque nouvelle version du Kit de développement logiciel, vous devez d'abord remplacer (supprimer et importer de nouveau dans xcode) les dossiers EngagementSDK et EngagementReach.

###Migration de 3.0.0 vers 4.0.0

### XCode 8
XCode 8 est obligatoire à partir de la version 4.0.0 du SDK.

> [AZURE.NOTE] Si vous dépendez vraiment de XCode 7, vous pouvez utiliser [iOS SDK Engagement v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu concernant le module Reach cette version précédente lorsqu’elle est exécutée sur des appareils iOS 10 : les notifications système ne sont pas activées. Pour corriger ce problème, vous devez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre délégué d’application comme suit :

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** : ce comportement peut changer dans une prochaine mise à niveau (même mineure) de la version iOS car cette API iOS est déconseillée. Vous devriez passer à XCode 8 dès que possible.

#### Infrastructure UserNotifications
Vous devez ajouter l’infrastructure `UserNotifications` à votre onglet Build Phases.

Dans l'Explorateur de projets, ouvrez le volet de votre projet et sélectionnez la cible appropriée. Ouvrez ensuite l'onglet **« Build phases »** et, dans le menu **« Link Binary With Libraries »**, ajoutez l’infrastructure `UserNotifications.framework` - définissez le lien comme `Optional`

#### Fonctionnalité push de l’application
XCode 8 peut réinitialiser la fonctionnalité push de votre application : vérifiez ce point dans l’onglet `capability` de votre cible sélectionnée.

#### Si vous disposez déjà de votre propre implémentation de UNUserNotificationCenterDelegate

Le Kit de développement logiciel (SDK) a sa propre implémentation du protocole UNUserNotificationCenterDelegate. Elle est utilisée par le Kit de développement logiciel pour surveiller le cycle de vie des notifications Engagement sur les appareils iOS 10 ou version ultérieure. Si le Kit de développement logiciel détecte votre délégué, il n’utilisera pas sa propre implémentation car il ne peut y avoir qu’un seul délégué UNUserNotificationCenter par application. Cela signifie que vous devrez ajouter la logique Engagement à votre propre délégué.

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

<!---HONumber=AcomDC_0921_2016-->