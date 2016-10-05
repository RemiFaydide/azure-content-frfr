<properties
	pageTitle="Intégration Reach du SDK iOS Azure Mobile Engagement | Microsoft Azure"
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

#Comment intégrer Engagement Reach sur iOS

Vous devez suivre la procédure d'intégration décrite dans la procédure [Intégration d'Engagement sur un document iOS](mobile-engagement-ios-integrate-engagement.md) avant de suivre ce guide.

Cette documentation nécessite XCode 8. Si vous dépendez vraiment de XCode 7, vous pouvez utiliser [iOS SDK Engagement v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu concernant cette version précédente lorsqu’elle est exécutée sur des appareils iOS 10 : les notifications système ne sont pas activées. Pour corriger ce problème, vous devez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre délégué d’application comme suit :

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** : ce comportement peut changer dans une prochaine mise à niveau (même mineure) de la version iOS car cette API iOS est déconseillée. Vous devriez passer à XCode 8 dès que possible.

### Activer votre application pour recevoir des notifications Push Silent

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##Étapes d'intégration

### Incorporez le Kit de développement logiciel (SDK) Engagement Reach dans votre projet iOS

-   Ajoutez le Kit de développement logiciel (SDK) Reach dans votre projet Xcode. Dans Xcode, accédez à **Projet > Ajouter au projet** et choisissez le dossier `EngagementReach`.

### Modifier votre délégué d'Application

-   En haut de votre fichier d'implémentation, importez le module Engagement Reach :

		[...]
		#import "AEReachModule.h"

-   Dans la méthode `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, créez un module Reach et transmettez-le à la ligne d'initialisation d’Engagement existante :

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
		  [...]

		  return YES;
		}

-   Modifiez la chaîne **icon.png** avec le nom de l'image que vous souhaitez en tant qu'icône de notification.
-   Si vous souhaitez utiliser l'option *Mettre à jour la valeur du badge* dans des campagnes Reach ou si vous souhaitez utiliser des campagnes </SaaS/Reach API/Campaign format/Native Push> natives Push, vous devez laisser le module Reach gérer l'icône de badge lui-même (il effacera automatiquement le badge de l'application et réinitialisera également la valeur stockée par Engagement lors de chaque démarrage ou mise au premier plan de l'application). Pour cela, ajoutez la ligne suivante après l'initialisation de module Reach :

		[reach setAutoBadgeEnabled:YES];

-   Si vous souhaitez gérer le Push des données Reach, vous devez laisser votre délégué d'application se conformer au protocole `AEReachDataPushDelegate`. Ajoutez la ligne suivante après l'initialisation de module Reach :

		[reach setDataPushDelegate:self];

-   Vous pourrez ensuite implémenter les méthodes `onDataPushStringReceived:` et `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` dans votre délégué d'application :

		-(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
		{
		   NSLog(@"String data push message with category <%@> received: %@", category, body);
		   return YES;
		}

		-(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
		{
		   NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
		   // Do something useful with decodedBody like updating an image view
		   return YES;
		}

### Catégorie

Le paramètre de catégorie est facultatif lorsque vous créez une campagne d'envoi de données (push) et vous permet de filtrer les envois de données. Cela s’avère utile si vous souhaitez effectuer une transmission de type push pour différents types de données `Base64` et que vous souhaitez identifier leur type avant de les analyser.

**Votre application est prête à recevoir et à afficher des contenus Reach !**

##Réception des annonces et des sondages à tout moment

Engagement peut envoyer des notifications Reach à vos utilisateurs finaux à tout moment en utilisant le Service de Notifications Push Apple.

Pour activer cette fonctionnalité, vous devrez préparer votre application pour les notifications Push Apple et modifier votre délégué d'application.

### Préparer votre application pour les notifications Push Apple

Veuillez suivre le guide [Préparation de votre Application pour les notifications Push Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6).

### Ajoutez le code client nécessaire

*À ce stade, votre application doit avoir un certificat Push Apple enregistré dans le serveur frontal d'Engagement.*

Si ce n'est pas encore le cas, vous devez inscrire votre application afin de recevoir des notifications push. Ajoutez la ligne suivante au démarrage de votre application (en général, dans `application:didFinishLaunchingWithOptions:`) :

	if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
	  	[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
	  	[application registerForRemoteNotifications];
	}
	else {
	  	[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
	}

Ensuite, vous devez fournir à Engagement le jeton de périphérique retourné par les serveurs Apple. Cette opération est effectuée dans la méthode appelée `application:didRegisterForRemoteNotificationsWithDeviceToken:` dans votre délégué d'application :

	- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
	{
	    [[EngagementAgent shared] registerDeviceToken:deviceToken];
	}

Enfin, vous devez informer le Kit de développement logiciel (SDK) Engagement lorsque votre application reçoit une notification à distance. Pour cela, appelez la méthode `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué d'application :

	- (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
	}

> [AZURE.NOTE] La méthode ci-dessus est introduite dans iOS 7. Si vous ciblez iOS < 7, assurez-vous d'implémenter la méthode `application:didReceiveRemoteNotification:` dans votre délégué d'application et d'appeler `applicationDidReceiveRemoteNotification` sur EngagementAgent en transmettant nil au lieu de l'argument `handler` :

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] Par défaut, Engagement Reach contrôle le completionHandler. Si vous souhaitez répondre manuellement au bloc `handler` de votre code, vous pouvez utiliser nil pour l’argument `handler` et contrôler le bloc de fin vous-même. Consultez le type `UIBackgroundFetchResult` d'une liste de valeurs possibles.


### Exemple complet

Voici un exemple complet d'intégration :

	#pragma mark -
	#pragma mark Application lifecycle

	- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
	{
	  /* Reach module */
	  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	  [reach setAutoBadgeEnabled:YES];

	  /* Engagement initialization */
	  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
	  [[EngagementAgent shared] setPushDelegate:self];

	  /* Views */
	  [window addSubview:[tabBarController view]];
	  [window makeKeyAndVisible];

	  [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
	  return YES;
	}

	- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
	{
	  [[EngagementAgent shared] registerDeviceToken:deviceToken];
	}

	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
	}

### Si vous disposez de votre propre implémentation de UNUserNotificationCenterDelegate

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

##Personnalisation des campagnes marketing

### Notifications

Il existe deux types de notifications : les notifications système et les notifications dans l'application.

Les notifications système sont gérées par iOS et ne peuvent pas être personnalisées.

Les notifications dans l'application sont constituées d'une vue qui est ajoutée dynamiquement à la fenêtre de l'application actuelle. Il s'agit d'une superposition de notification. Les superpositions de notification sont idéales pour une intégration rapide car vous n'avez pas à modifier les vues dans votre application.

#### Disposition

Pour modifier l'apparence de vos notifications dans l'application, il vous suffit de modifier le fichier `AENotificationView.xib` pour l'adapter à vos besoins, en veillant à conserver les valeurs des balises et les types de sous-vues existantes.

Par défaut, les notifications dans l'application se trouvent en bas de l'écran. Si vous préférez les afficher en haut de l'écran, modifiez le `AENotificationView.xib` fourni et modifiez la propriété `AutoSizing` de la vue principale afin qu'elle puisse être conservée en haut de sa superview.

#### Catégories

Lorsque vous modifiez la disposition fournie, vous modifiez l'apparence de toutes vos notifications. Les catégories permettent de définir diverses recherches ciblées (et éventuellement des comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne Reach. N'oubliez pas que les catégories vous permettent également de personnaliser les annonces et les sondages, décrits plus avant dans ce document.

Pour inscrire un gestionnaire de catégorie pour vos notifications, vous devez ajouter un appel lorsque le module Reach est initialisé.

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerNotifier:myNotifier forCategory:@"my_category"];
	...

`myNotifier` doit être une instance d'un objet qui est conforme au protocole `AENotifier`.

Vous pouvez implémenter les méthodes de protocole vous-même ou choisir d'implémenter à nouveau la classe `AEDefaultNotifier` existante qui effectue déjà l'essentiel du travail.

Par exemple, si vous souhaitez redéfinir la vue de notification pour une catégorie spécifique, vous pouvez suivre cet exemple :

	#import "AEDefaultNotifier.h"
	#import "AENotificationView.h"
	@interface MyNotifier : AEDefaultNotifier
	@end

	@implementation MyNotifier

	-(NSString*)nibNameForCategory:(NSString*)category
	{
	  return "MyNotificationView";
	}

	@end

Cet exemple simple de catégorie part du principe que vous possédez un fichier nommé `MyNotificationView.xib` dans votre offre groupée d'applications principale. Si la méthode n'est pas en mesure de trouver un `.xib` correspondant, la notification ne s'affichera pas et Engagement générera un message dans la console.

Le fichier nib fourni doit respecter les règles suivantes :

-   Il doit contenir une seule vue.
-   Les sous-vues doivent être du même type que celles qui se trouvent dans le fichier nib fourni nommé `AENotificationView.xib`
-   Les sous-vues doivent comporter les mêmes balises que celles qui se trouvent dans le fichier nib nommé `AENotificationView.xib`

> [AZURE.TIP] Copiez simplement le fichier nib fourni, nommé `AENotificationView.xib`, puis commencez à travailler. Attention, la vue à l'intérieur de ce fichier nib est associée à la classe `AENotificationView`. Cette classe a redéfini la méthode `layoutSubViews` pour déplacer et redimensionner ses sous-vues en fonction du contexte. Vous avez peut-être besoin de la remplacer par une `UIView` ou votre classe de vue personnalisée.

Si vous avez besoin de personnaliser davantage vos notifications (si vous souhaitez par exemple charger votre vue directement à partir du code), il est recommandé d'examiner le code source et la documentation de classe fournis de `Protocol ReferencesDefaultNotifier` et `AENotifier`.

Notez que vous pouvez utiliser le même notificateur pour plusieurs catégories.

Vous pouvez également redéfinir le notificateur par défaut comme suit :

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### Gestion des notifications

Lorsque vous utilisez la catégorie par défaut, certaines méthodes de cycle de vie sont appelées sur l'objet `AEReachContent` pour communiquer les statistiques et mettre à jour l'état de la campagne :

-   Lorsque la notification est affichée dans l'application, la méthode `displayNotification` (qui fournit des statistiques) est appelée par `AEReachModule` si `handleNotification:` renvoie `YES`.
-   Si la notification est ignorée, la méthode `exitNotification` est appelée, les statistiques sont signalées et les campagnes suivantes peuvent alors être traitées.
-   En cas de clic sur la notification, `actionNotification` est appelée, les statistiques sont signalées et l'action associée est effectuée.

Si votre implémentation de `AENotifier` contourne le comportement par défaut, vous devez appeler ces méthodes de cycle de vie vous-même. Les exemples suivants illustrent certains cas dans lesquels le comportement par défaut est ignoré :

-   Vous n'étendez pas `AEDefaultNotifier`, par exemple, vous avez implémenté la gestion des catégories à partir de zéro.
-   Vous avez substitué `prepareNotificationView:forContent:`, veillez à mapper au moins `onNotificationActioned` ou `onNotificationExited` sur l'un des contrôles de votre interface utilisateur.

> [AZURE.WARNING] Si `handleNotification:` lève une exception, le contenu est supprimé et `drop` est appelé. Cela est signalé dans les statistiques et les campagnes suivantes peuvent maintenant être traitées.

#### Incluez des notifications dans le cadre d'une vue existante

Les superpositions sont idéales pour une intégration rapide mais peuvent parfois ne pas être pratiques ou avoir des effets secondaires indésirables.

Si vous n'êtes pas satisfait du système de superposition de certains de vos affichages, vous pouvez le personnaliser pour ces affichages.

Vous pouvez décider d'inclure notre disposition de notifications dans vos affichages existants. Pour ce faire, il existe deux styles de mise en œuvre :

1.  Ajouter l'affichage de notification à l'aide d'Interface Builder

	-   Ouvrez *Interface Builder*
	-   Placez un `UIView` 320 x 60 (ou 768 x 60 si vous êtes sur iPad) à l'endroit où vous souhaitez voir apparaître la notification
	-   Définissez la valeur de balise pour cette vue sur : **36822491**

2.  Ajoutez par programme l'affichage de notification. Ajoutez simplement le code suivant lors de l'initialisation de votre affichage :

		UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
		notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
		[self.view addSubview:notificationView];

La macro `NOTIFICATION_AREA_VIEW_TAG` se trouve dans `AEDefaultNotifier.h`.

> [AZURE.NOTE] Le notificateur par défaut détecte automatiquement que la disposition de notification est incluse dans cet affichage et nr lui ajoute pas de superposition.

### Annonces et sondages

#### Mises en forme

Vous pouvez modifier les fichiers `AEDefaultAnnouncementView.xib` et `AEDefaultPollView.xib` tant que vous conservez les valeurs des balises et les types de sous-vues existantes.

#### Catégories

##### Autres dispositions

Telles que les notifications, catégorie de la campagne peut être utilisée pour que les autres dispositions pour vos annonces et les sondages.

Pour créer une catégorie d'annonce, vous devez étendre **AEAnnouncementViewController** et l'enregistrer lorsque le module Reach a été initialisé :

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Chaque fois qu'un utilisateur clique sur une notification pour une annonce avec la catégorie « my\\_category », votre contrôleur d'affichage enregistré (dans ce cas `MyCustomAnnouncementViewController`) sera initialisé en appelant la méthode `initWithAnnouncement:` et l'affichage sera ajouté à la fenêtre de l'application actuelle.

Dans votre implémentation de la classe `AEAnnouncementViewController`, vous devez lire la propriété `announcement` pour initialiser vos sous-vues. Prenons l'exemple ci-dessous, dans lequel deux étiquettes sont initialisées à l'aide des propriétés `title` et `body` de la classe `AEReachAnnouncement` :

	-(void)loadView
	{
	    [super loadView];

	    UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
	    titleLabel.font = [UIFont systemFontOfSize:32.0];
	    titleLabel.text = self.announcement.title;

	    UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
	    bodyLabel.font = [UIFont systemFontOfSize:24.0];
	    bodyLabel.text = self.announcement.body;

	    [self.view addSubview:titleLabel];
	    [self.view addSubview:bodyLabel];
	}

Si vous ne souhaitez pas charger vos affichages vous-même, mais que vous souhaitez simplement réutiliser la disposition de l'affichage des annonces par défaut, il vous suffit de faire en sorte que votre contrôleur d'affichage personnalisé étende la classe fournie `AEDefaultAnnouncementViewController`. Dans ce cas, dupliquez le fichier nib `AEDefaultAnnouncementView.xib` et renommez-le afin qu'il puisse être chargé par votre contrôleur d'affichage personnalisé (pour un contrôleur nommé `CustomAnnouncementViewController`, vous devez appeler votre fichier nib `CustomAnnouncementView.xib`).

Pour remplacer la catégorie par défaut des annonces, il vous suffit d'enregistrer votre contrôleur d'affichage personnalisé pour la catégorie définie dans `kAEReachDefaultCategory` :

	[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Les sondages peuvent être personnalisés de la même manière :

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Cette fois, les `MyCustomPollViewController` fournis doivent étendre `AEPollViewController`. Vous pouvez également choisir d'étendre à partir du contrôleur par défaut : `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] N'oubliez pas d'appeler `action` (`submitAnswers:` pour les contrôleurs d'affichage personnalisé de sondage) ou la méthode `exit` avant que le contrôleur d'affichage ne soit ignoré. Dans le cas contraire, les statistiques ne seront pas envoyées (c'est-à-dire aucune analyse de la campagne) et surtout, les campagnes suivantes ne recevront pas de notification avant que le processus de l'application soit redémarré.

##### Exemple d'implémentation

Dans cette implémentation, l'affichage d'annonces personnalisé est chargé à partir d'un fichier xib externe.

Comme pour la personnalisation de notification avancée, il est recommandé d'examiner le code source de l'implémentation standard.

`CustomAnnouncementViewController.h`

	//Interface
	@interface CustomAnnouncementViewController : AEAnnouncementViewController {
	  UILabel* titleLabel;
	  UITextView* descTextView;
	  UIWebView* htmlWebView;
	  UIButton* okButton;
	  UIButton* cancelButton;
	}

	@property (nonatomic, retain) IBOutlet UILabel* titleLabel;
	@property (nonatomic, retain) IBOutlet UITextView* descTextView;
	@property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
	@property (nonatomic, retain) IBOutlet UIButton* okButton;
	@property (nonatomic, retain) IBOutlet UIButton* cancelButton;

	-(IBAction)okButtonClicked:(id)sender;
	-(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

	//Implementation
	@implementation CustomAnnouncementViewController
	@synthesize titleLabel;
	@synthesize descTextView;
	@synthesize htmlWebView;
	@synthesize okButton;
	@synthesize cancelButton;

	-(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
	{
	  self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
	  if (self != nil) {
	    self.announcement = anAnnouncement;
	  }
	  return self;
	}

	- (void) dealloc
	{
	  [titleLabel release];
	  [descTextView release];
	  [htmlWebView release];
	  [okButton release];
	  [cancelButton release];
	  [super dealloc];
	}

	- (void)viewDidLoad {
	  [super viewDidLoad];

	  /* Init announcement title */
	  titleLabel.text = self.announcement.title;

	  /* Init announcement body */
	  if(self.announcement.type == AEAnnouncementTypeHtml)
	  {
	    titleLabel.hidden = YES;
	    htmlWebView.hidden = NO;
	    [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
	  }
	  else
	  {
	    titleLabel.hidden = NO;
	    htmlWebView.hidden = YES;
	    descTextView.text = self.announcement.body;
	  }

	  /* Set action button label */
	  if([self.announcement.actionLabel length] > 0)
	    [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

	  /* Set exit button label */
	  if([self.announcement.exitLabel length] > 0)
	    [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
	}

	#pragma mark Actions

	-(IBAction)okButtonClicked:(id)sender
	{
	    [self action];
	}

	-(IBAction)cancelButtonClicked:(id)sender
	{
	    [self exit];
	}

	@end

<!---HONumber=AcomDC_0921_2016-->