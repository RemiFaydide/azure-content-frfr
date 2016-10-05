<properties 
   pageTitle="Guide de dépannage d’Azure Mobile Engagement - Kit de développement logiciel (SDK)" 
   description="Résolution des problèmes d’intégration du Kit de développement logiciel (SDK) dans Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# Guide de dépannage pour les problèmes d’intégration du Kit de développement logiciel (SDK)

Les éléments suivants sont des problèmes potentiels liés à la façon dont Azure Mobile Engagement s’intègre dans votre application.

## Problèmes du Kit de développement logiciel (SDK) découverts par un échec dans une autre partie de votre application

### Problème
- Échec de la collecte de données de l'interface utilisateur (dans Analyse, Surveillance, Segmentation ou Tableaux de bord).
- Échec push (push ne fonctionne pas dans l'application, en dehors de l'application, ou les deux).
- Échecs de composants avancés (le suivi, la géolocalisation ou les push spécifiques à une plateforme ne fonctionnent pas).
- Échecs d'API (les API échouent souvent en mode silencieux sans afficher de message d'erreur).
- Défaillances du service (aucun élément d'Azure Mobile Engagement ne fonctionne avec votre application).

### Causes

- La plupart des problèmes qui doivent être résolus avec le Kit de développement logiciel Azure Mobile Engagement sera découvert par un échec dans votre application (par exemple, un échec de collecte des données de l'interface utilisateur, Échec de transmission, Échec de la fonctionnalité avancée, Échec de l'API, Application tombe en panne ou interruption de service apparente).
- Si une fonctionnalité particulière d'Azure Mobile Engagement n'a jamais fonctionné dans votre application auparavant, vous devez terminer l'intégration.
- Si une fonctionnalité particulière de l'Engagement Mobile Azure fonctionnait et arrêté, vous devrez peut-être mettre à niveau vers la dernière version avec l'Engagement de Mobile Azure SDK. N’oubliez pas qu’il existe une autre version du Kit de développement logiciel (SDK) d’Azure Mobile Engagement pour chaque plateforme prise en charge par Azure Mobile Engagement (Android, iOS, Windows et Windows Phone).

#### Intégration du Kit de développement logiciel (SDK)

- Azure Mobile Engagement n'est pas correctement intégré dans le Kit de développement logiciel (Analyse).
- Reach n'est pas correctement intégré dans le Kit de développement logiciel (push dans l'application et en dehors de l'application).
- Certificat expiré ou PROD et DEV incorrects (iOS uniquement).
- GCM ou ADM n'est pas correctement intégré dans le Kit de développement logiciel (Android uniquement - Push spécifique au service).
- Le suivi n'est pas correctement intégré dans le Kit de développement logiciel (installer le suivi de magasin).
- Intégration incorrecte de l'emplacement tardif ou de l'emplacement GPS dans le Kit de développement logiciel (ciblage par emplacement géographique).


**Voir aussi :**

- [Documentation du Kit de développement logiciel - Guide d’intégration][Link 5]
- [Guide de dépannage - Push][Link 23]

#### Mise à niveau du Kit de développement logiciel (SDK) :

- Vous devez mettre à niveau le Kit de développement logiciel pour résoudre des problèmes des versions antérieures du Kit de développement logiciel (souvent liés à des versions plus récentes du système d'exploitation du périphérique).
- Désinstallez toutes les versions précédentes de votre application de votre appareil et réinstallez la version la plus récente de votre application, réenregistrez votre ID de périphérique à partir de l'interface utilisateur d'Azure Mobile Engagement pour confirmer que votre appareil utilise la version la plus récente de votre application.

**Voir aussi :**

- [Documentation du Kit de développement logiciel (SDK) - Notes de publication](http://go.microsoft.com/fwlink/?LinkId= 525554)
- [Documentation du Kit de développement logiciel (SDK) - Guides de mise à niveau](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### Kit de développement logiciel (SDK), autres :

- Des erreurs dans le manifeste d'application « AndroidManifest.xml » peuvent empêcher Azure Mobile Engagement de fonctionner (Android uniquement).
- Un autre problème courant avec l’intégration du Kit de développement logiciel (SDK) et l’utilisation de l’API est la confusion entre la clé du Kit de développement logiciel et la clé d’API.

**Voir aussi :**

- [Concepts - Glossaire][Link 6]

## Problèmes de codage avancé

### Problème
-  Le code spécifique de plate-forme pas directement lié à Azure Mobile Engagement peut provoquer des problèmes sur iOS, Android et Windows Phone.

### Causes

- Beaucoup avancée des problèmes de codage avec Azure Mobile Engagement sont provoquées par le code spécifique de plate-forme mal écrit pas directement lié à l'Engagement de Mobile Azure. Outre la documentation d'Azure Mobile Engagement, vous devez consulter la documentation spécifique à la plateforme sur laquelle vous développez (Android, iOS, Web, Windows et Windows Phone).
- Ne pas configurer correctement « categories » empêche de lier une notification à un autre emplacement à l'intérieur ou en dehors de l'application (Android uniquement).
- Ne pas définir « UIKit.framework » sur « facultatif » dans votre code iOS affiche un « Symbole erreur introuvable » et/ou des blocages sur les anciens périphériques iOS (iOS uniquement).
- Les certificats expirés ou qui ne sont pas correctement configurés à l'aide de la version de développement ou de production Prod du certificat, provoquent des problèmes push (iOS uniquement).
- Il existe certaines limitations inhérentes à une plateforme qu'Azure Mobile Engagement n'est pas en mesure de contrôler (par exemple le fonctionnement de system center pour les push en dehors de l'application dans Android et iOS).
- Azure Mobile Engagement publie une liste complète des packages internes utilisé par Azure Mobile Engagement pour iOS et Android à des fins de référence. N'oubliez pas que certaines fonctionnalités d'Azure Mobile Engagement sont spécifiques à la plate-forme (Android, iOS, Web, Windows et Windows Phone).

### Voir aussi

 - [Guide de dépannage - Push][Link 23]
 - [Documentation du Kit de développement logiciel (SDK) - Notes de publication][Link 5]
 - [Documentation du Kit de développement logiciel - Guides de mise à niveau][Link 5]

## Blocage de l’application

### Problème
- Votre application se bloque sur un périphérique de l'utilisateur final.

### Causes

- Les informations de blocage peuvent être consultées dans l’*interface utilisateur de l’analyse* ou l’*API d’analyse*.
- Vous pouvez trouver l'ID de périphérique de votre périphérique de test et exécuter l'action à l'origine du blocage de votre application chez un utilisateur final afin d'identifier la cause de l'incident.
- Les problèmes connus avec le Kit de développement logiciel (SDK) Mobile Azure Engagement qui provoquent le blocage des applications sont parfois résolus par la mise à niveau vers la dernière version du Kit de développement logiciel. Par conséquent, veillez à vérifier les notes de publication de votre plateforme quand vous recherchez la cause d’un blocage.

### Voir aussi

- [Documentation du Kit de développement logiciel (SDK) - Notes de publication][Link 5]
- [Documentation du Kit de développement logiciel - Guides de mise à niveau][Link 5]

## Échecs de téléchargement App Store

### Problème
- Les erreurs liées au téléchargement de la dernière version de votre application dans Apple, Google ou le magasin d’applications Windows.

### Causes

- Le magasin d’applications Windows bloque parfois les applications avec certaines fonctionnalités activées (l’Apple Store empêche l’utilisation de IDFV dans les applications du magasin et le magasin GooglePlay empêche le partage d’informations d’application entre les applications).
- Veillez à consulter les notes de publication sur votre plateforme et la version actuelle du SDK si vous avez des difficultés à télécharger une application dans le magasin.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=AcomDC_0824_2016-->