<properties
	pageTitle="Intégration du SDK iOS Azure Mobile Engagement | Microsoft Azure"
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

#Intégration d'Engagement sur iOS

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette procédure décrit la méthode la plus simple pour activer les fonctions d'analyse et de contrôle d'Engagement dans votre application iOS.

Le SDK Engagement requiert iOS6+ et Xcode 8 : la version de déploiement de votre application doit être au moins iOS 6.

> [AZURE.NOTE]
Si vous dépendez vraiment de XCode 7, vous pouvez utiliser [iOS SDK Engagement v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu concernant le module Reach de cette version précédente lorsqu’elle est exécutée sur des appareils iOS 10, consultez [l’intégration du module Reach](mobile-engagement-ios-integrate-engagement-reach.md) pour plus de détails. Si vous choisissez d’utiliser le Kit de développement logiciel v3.2.4, ignorez l’importation de `UserNotifications.framework` à l’étape suivante.

Les étapes suivantes permettent d'activer la génération des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques. Le rapport des journaux nécessaire pour calculer d’autres statistiques telles que les événements, les erreurs et les tâches, doit être généré manuellement à l’aide de l’API Engagement (consultez la rubrique [Utilisation de l’API de balisage Mobile Engagement avancée dans vos applications iOS](mobile-engagement-ios-use-engagement-api.md) étant donné que ces statistiques dépendent de l’application.

##Incorporer le SDK Engagement à votre projet iOS

- Télécharger le kit de développement logiciel (SDK) iOS [ici](http://aka.ms/qk2rnj).

- Ajoutez le Kit de développement logiciel (SDK) Engagement à votre projet iOS : dans Xcode, cliquez avec le bouton droit sur votre projet, puis sélectionnez **Ajouter des fichiers à...** et choisissez le dossier `EngagementSDK`.

- Engagement nécessite des infrastructures supplémentaires pour fonctionner : dans l'Explorateur de projets, ouvrez le volet de votre projet et sélectionnez la cible appropriée. Ouvrez ensuite l'onglet **« Build phases »** et, dans le menu **« Link Binary With Libraries »**, ajoutez ces infrastructures :

	-   `UserNotifications.framework` - définissez le lien comme `Optional`
> -   `AdSupport.framework` - définissez le lien comme `Optional`
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] L'infrastructure AdSupport peut être supprimée. Engagement en a besoin pour collecter l'IDFA. Toutefois, il est possible de désactiver la collection de l'IDFA \<ios-sdk-engagement-idfa\> pour se conformer à la nouvelle politique d'Apple concernant cet ID.

##Initialiser le SDK Engagement

Vous devez modifier votre délégué d'application :

-   En haut de votre fichier d'implémentation, importez l'agent Engagement :

		[...]
		#import "EngagementAgent.h"

-   Initialisez Engagement au sein de la méthode **applicationDidFinishLaunching:** ou **application:didFinishLaunchingWithOptions:** :

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		  [...]
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
		  [...]
		}

##Génération de rapports de base

### Méthode recommandée : surchargez vos classes `UIViewController`

Pour activer le rapport de tous les journaux requis par Engagement pour calculer les statistiques relatives aux utilisateurs, aux sessions, aux activités, aux incidents et aux informations techniques, il vous suffit de faire hériter toutes vos `UIViewController`sous-classes des classes `EngagementViewController` (même règle pour `UITableViewController` -> `EngagementTableViewController`).

**Sans Engagement :**

	#import <UIKit/UIKit.h>

	@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Avec Engagement :**

	#import <UIKit/UIKit.h>
	#import "EngagementViewController.h"

	@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Autre méthode : appeler `startActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger vos classes `UIViewController`, vous pouvez démarrer vos activités en appelant directement les méthodes de `EngagementAgent`.

> [AZURE.IMPORTANT] Le Kit de développement logiciel (SDK) iOS appelle automatiquement la méthode `endActivity()` à la fermeture de l'application. Par conséquent, il est *FORTEMENT* recommandé d'appeler la méthode `startActivity` chaque fois que l'activité de l'utilisateur change et de ne *JAMAIS* appeler la méthode `endActivity`, étant donné que l'appel de cette méthode entraîne l'arrêt de la session en cours.

##Rapports d'emplacement

Les conditions du service Apple n'autorisent pas les applications à utiliser le suivi d'emplacement à des fins statistiques uniquement. Par conséquent, il est recommandé d'activer les rapports d'emplacement uniquement si votre application utilise également le suivi d'emplacement pour une autre raison.

À compter d'iOS 8, vous devez fournir une description de la façon dont votre application utilise les services d'emplacement en définissant une chaîne pour la clé [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] dans le fichier Info.plist de votre application. Si vous voulez signaler l'emplacement en arrière-plan avec Engagement, ajoutez la clé NSLocationAlwaysUsageDescription. Dans tous les autres cas, ajoutez la clé NSLocationWhenInUseUsageDescription.

### Rapports d'emplacement de zone différé

Le rapport d'emplacement de zone différé permet d'indiquer le pays, la région et la localité associés aux appareils. Ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi). La zone de l'appareil est signalée une fois maximum par session. Le GPS n'est jamais utilisé, ce type de rapport d'emplacement a donc très peu d'impact (pour ne pas dire aucun) sur la batterie.

Les zones signalées sont utilisées pour calculer des statistiques géographiques relatives aux utilisateurs, aux sessions, aux événements et aux erreurs. Elles peuvent également servir de critère dans les couvertures campagne. La dernière zone connue signalée pour un appareil peut être récupérée grâce à l'[API de l'appareil].

Pour activer le rapport d'emplacement de zone différé, ajoutez la ligne suivante après l'initialisation de l'agent Engagement :

	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
	{
	  [...]
	  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
	  [...]
	}

### Rapports d'emplacement en temps réel

Le rapport d'emplacement en temps réel permet de signaler la latitude et la longitude associées aux appareils. Par défaut, ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi) et le rapport n'est actif que lorsque l'application s'exécute en premier plan (c'est-à-dire pendant une session).

Les emplacements en temps réel ne sont *PAS* utilisés pour calculer des statistiques. Leur seul but est de permettre l'utilisation d’un critère de géorepérage en temps réel < portée-public-gardiennage virtuel > dans les campagnes Reach.

Pour activer le rapport d'emplacement en temps réel, ajoutez la ligne suivante après l'initialisation de l'agent Engagement :

	[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### Rapports GPS

Par défaut, le rapport d'emplacement en temps réel utilise uniquement des emplacements réseau. Pour activer l'utilisation des emplacements GPS (qui sont beaucoup plus précis), ajoutez ce qui suit :

	[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### Rapports en arrière-plan

Par défaut, le rapport d'emplacement en temps réel est uniquement actif quand l'application s'exécute en premier plan (c'est-à-dire pendant une session). Pour activer la création de rapports également en arrière-plan, ajoutez ce qui suit :

	[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Quand l'application s'exécute en arrière-plan, seuls les emplacements réseau sont signalés, même si vous avez activé le GPS.

L'implémentation de cette fonction appelle [startMonitoringSignificantLocationChanges] quand votre application passe en arrière-plan. N'oubliez pas que votre application sera automatiquement relancée en arrière-plan en cas de nouvel événement d'emplacement.

##Génération de rapports avancés

Éventuellement, si vous voulez signaler des événements, des erreurs et des tâches de l'application spécifiques, vous devez utiliser l'API Engagement par le biais des méthodes de la classe `EngagementAgent`. Un objet de cette classe peut être récupéré en appelant la méthode statique `[EngagementAgent shared]`.

L'API d'Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement et est détaillé dans la procédure d’utilisation de l'API d'Engagement sur iOS (ainsi que dans la documentation technique de la classe `EngagementAgent`).

##Désactiver la collecte de l'IDFA

Par défaut, Engagement utilise l'[IDFA] pour identifier un utilisateur de manière unique. Toutefois, si vous n'utilisez pas de publicités à un autre endroit dans l'application, cette dernière peut être rejetée par le processus de vérification de la boutique d'applications. La collection de l'IDFA peut être désactivée en ajoutant la macro `ENGAGEMENT_DISABLE_IDFA` du préprocesseur dans votre fichier pch (ou dans le `Build Settings` de votre application). De cette façon, vous garantissez qu'il n'existe aucune référence à `ASIdentifierManager`, `advertisingIdentifier` ou `isAdvertisingTrackingEnabled` dans la génération de l'application.

Intégration dans le fichier **prefix.pch** :

	#define ENGAGEMENT_DISABLE_IDFA
	...

Vous pouvez vérifier que la collecte d'IDFA est correctement désactivée dans votre application en consultant les journaux de test d'Engagement. Consultez la documentation sur les tests d'intégration <ios-sdk-engagement-test-idfa> pour plus d'informations.

##Désactiver les rapports de suivi

### En appelant une méthode

Si vous voulez qu'Engagement arrête d'envoyer des journaux, vous pouvez appeler la méthode suivante :

	[[EngagementAgent shared] setEnabled:NO];

Cet appel est persistant : il utilise `NSUserDefaults` pour stocker les informations.

Vous pouvez activer à nouveau la création de rapports de journaux en appelant la même fonction avec `YES`.

### Intégration dans votre groupe de paramètres

Au lieu d'appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre fichier `Settings.bundle` existant. La chaîne `engagement_agent_enabled` doit être utilisée comme identificateur de préférence et doit être associée à un bouton bascule (`PSToggleSwitchSpecifier`).

L'exemple suivant de `Settings.bundle` montre comment l'implémenter :

	<dict>
	    <key>PreferenceSpecifiers</key>
	    <array>
	        <dict>
	            <key>DefaultValue</key>
	            <true/>
	            <key>Key</key>
	            <string>engagement_agent_enabled</string>
	            <key>Title</key>
	            <string>Log reporting enabled</string>
	            <key>Type</key>
	            <string>PSToggleSwitchSpecifier</string>
	        </dict>
	    </array>
	    <key>StringsTable</key>
	    <string>Root</string>
	</dict>

<!-- URLs. -->
[API de l'appareil]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]: http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]: https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!---HONumber=AcomDC_0921_2016-->