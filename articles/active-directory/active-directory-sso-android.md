<properties
	pageTitle="En savoir plus sur l’activation d’une authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL | Microsoft Azure"
	description="En savoir plus sur l’utilisation des fonctionnalités de votre Kit de développement logiciel (SDK) ADAL pour activer l’authentification unique sur l’ensemble de vos applications. "
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>


# Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL


Les clients s’attendent désormais à profiter d’une authentification unique, nécessitant des utilisateurs une seule et unique saisie des informations d’identification, qui restent automatiquement actives sur l’ensemble des applications. La difficulté à saisir le nom d’utilisateur et le mot de passe sur des petits formats d’écrans, à laquelle s’ajoute souvent un facteur supplémentaire (2FA) tel qu’un appel ou un code par SMS, mécontente rapidement les utilisateurs contraints d’effectuer plusieurs fois l’opération pour votre produit.

En outre, si vous valorisez une plateforme d’identité que d’autres applications peuvent utiliser, telle que les comptes Microsoft ou un compte professionnel d’Office365, les clients s’attendent à ce que ces informations d’identification soient disponibles sur l’ensemble de leurs applications, quel que soit le fournisseur.

La plateforme Microsoft Identity, combinée à nos Kits de développement logiciel (SDK) Microsoft Identity, vous soulage de ces tâches complexes et vous permet d’offrir à vos utilisateurs l’authentification unique au sein de votre propre suite d’applications, ou comme avec nos applications de répartiteur et Authenticator, sur l’intégralité de l’appareil.

Cette procédure pas à pas vous décrit la configuration de notre Kit de développement logiciel (SDK) au sein de votre application afin d’offrir cet avantage à vos clients.

Cette procédure pas à pas s’applique aux éléments suivants :

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Accès conditionnel Azure Active Directory

Notez que le document ci-dessous prend pour acquis que vous savez comment [mettre en service des applications dans le portail hérité dédié Azure Active Directory](active-directory-how-to-integrate.md) et que vous avez intégré votre application avec le [Kit de développement logiciel (SDK) Microsoft Identity Android](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## Concepts de l’authentification unique dans la plateforme Microsoft Identity

### Répartiteurs Microsoft Identity

Sur l’ensemble des plateformes mobiles, Microsoft fournit des applications qui prennent en charge le portage des informations d’identification entre les applications de différents fournisseurs, ainsi que des fonctionnalités spéciales avancées nécessitant un emplacement unique sécurisé de validation des informations d’identification. Nous appelons ces applications **répartiteurs**. Sur iOS et Android, ces composants sont offerts via des applications téléchargeables que les clients installent indépendamment ou sont transmis sur l’appareil par une entreprise qui gère certains ou la totalité des appareils des employés. Ces répartiteurs gèrent la sécurité d’une partie des applications ou de l’intégralité de l’appareil, en fonction des besoins des administrateurs informatiques. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré au système d’exploitation, désigné techniquement sous l’appellation « Répartiteur d’authentification web ».

Pour comprendre comment nous utilisons ces répartiteurs et la manière dont vos utilisateurs peuvent les afficher dans leurs flux de connexion associés à la plateforme Microsoft Identity, consultez les références suivantes.

### Modèles de connexion sur les appareils mobiles

L’accès aux informations d’identification sur les appareils respecte deux modèles de base pour la plateforme Microsoft Identity :

* Connexions assistées sans répartiteur
* Connexions assistées avec répartiteur

#### Connexions assistées sans répartiteur

Les connexions assistées sans répartiteur correspondent à des expériences de connexion intervenant en ligne avec l’application et utilisant le stockage local sur l’appareil pour cette application. Ce stockage peut être partagé entre les applications, mais les informations d’identification sont étroitement liées à l’application ou à la suite d’applications qui les utilisent. C’est l’expérience que vous avez probablement rencontrée sur de nombreuses applications mobiles, dans lesquelles vous devez saisir un nom d’utilisateur et un mot de passe.

Ces connexions présentent les avantages suivants :

-  L’expérience utilisateur existe intégralement au sein de l’application.
-  Les informations d’identification peuvent être partagées entre les applications qui sont signées par le même certificat, ce qui constitue une expérience de connexion unique à votre suite d’applications.
-  Le contrôle de connexion est fourni à l’application avant et après la connexion.

Ces connexions présentent les inconvénients suivants :

- L’utilisateur ne peut pas profiter de l’authentification unique sur l’ensemble des applications utilisant Microsoft Identity, uniquement sur celles que votre application détient et a configuré.
- Votre application ne peut pas être utilisée avec des fonctionnalités commerciales plus avancées, comme l’accès conditionnel ; elle ne peut pas non plus utiliser la suite de produits Intune.
- Votre application ne peut pas prendre en charge l’authentification par certificat des utilisateurs professionnels.

Voici une représentation de la manière dont les Kits de développement logiciel (SDK) Microsoft Identity fonctionnent avec le stockage partagé de vos applications, pour l’activation de l’authentification unique :

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### Connexions assistées avec répartiteur

Les connexions assistées avec répartiteur sont des expériences de connexion se produisant au sein de l’application de répartiteur, qui utilisent le stockage et la sécurité de ce composant pour partager l’ensemble des applications sur l’appareil qui valorise la plateforme Microsoft Identity. Concrètement, vos applications s’appuient sur le répartiteur pour connecter les utilisateurs. Sur iOS et Android, ces composants sont offerts via des applications téléchargeables que les clients installent indépendamment ou sont transmis sur l’appareil par une entreprise qui gère les appareils des utilisateurs. Comme exemple de ce type d’application, citons Azure Authenticator sur iOS. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré au système d’exploitation, désigné techniquement sous l’appellation « Répartiteur d’authentification web ». L’expérience, qui varie en fonction des plateformes, peut parfois perturber les utilisateurs en cas de gestion inappropriée. Vous connaissez probablement davantage ce modèle si vous avez installé l’application Facebook et que vous utilisez sa fonctionnalité de connexion dans une autre application. La plateforme Microsoft Identity valorise le même modèle.

Sur iOS, une animation de transition s’affiche. Votre application est transmise à l’arrière-plan, tandis que les applications Azure Authenticator sont mises en avant-plan, ce qui permet à l’utilisateur de choisir son compte de connexion.

Sur Android et Windows, le sélecteur de compte s’affiche dans la partie supérieure de votre application ; l’utilisateur est ainsi moins perturbé.

#### Appel du répartiteur

Si un répartiteur compatible, tel que l’application Azure Authenticator, est installé sur l’appareil, les Kits de développement logiciel (SDK) Microsoft Identity effectuent automatiquement pour vous l’opération d’appel du répartiteur lorsqu’un utilisateur souhaite se connecter à l’aide d’un compte de la plateforme Microsoft Identity. Il peut s’agir d’un compte personnel Microsoft, d’un compte professionnel ou scolaire, ou d’un compte que vous fournissez et hébergez dans Microsoft Azure à l’aide de nos produits B2C et B2B. À l’aide d’algorithmes et d’un chiffrement extrêmement sécurisés, nous nous assurons que les informations d’identification sont sollicitées et transmises à votre application de manière sûre. Les détails techniques exacts de ces mécanismes ne sont pas publiés, mais ont été développés conjointement par Apple et Google.

**Le développeur détermine si le kit de développement logiciel (SDK) appelle le répartiteur ou utilise un flux assisté sans répartiteur.** Toutefois, si le développeur choisit de ne pas avoir recours au flux assisté avec répartiteur, il ne peut pas utiliser les informations d’identification d’authentification unique déjà saisies par l’utilisateur sur l’appareil. Par ailleurs, il empêche toute utilisation de l’application avec des fonctions commerciales fournies par Microsoft à ses clients, comme l’accès conditionnel, les fonctionnalités de gestion Intune et l’authentification par certificat.

Ces connexions présentent les avantages suivants :

-  L’utilisateur profite de l’authentification unique sur l’ensemble de ses applications, quel que soit le fournisseur.
-  Votre application peut valoriser des fonctionnalités commerciales plus avancées, comme l’accès conditionnel ou utiliser la suite de produits Intune.
-  Votre application peut prendre en charge l’authentification par certificat des utilisateurs professionnels.
- L’expérience de connexion est bien plus sécurisée, dans la mesure où les identités de l’application et de l’utilisateur sont vérifiées par l’application du répartiteur à l’aide d’algorithmes de sécurité et d’un chiffrement supplémentaires.

Ces connexions présentent les inconvénients suivants :

- Dans iOS, l’utilisateur est déplacé à l’extérieur de l’expérience applicative pendant la sélection des informations d’identification.
- Perte de la capacité de gérer l’expérience de connexion des clients au sein de l’application.



Voici une représentation de la manière dont les Kits de développement logiciel (SDK) Microsoft Identity fonctionnent avec les applications de répartiteur pour prendre en charge l’authentification unique :

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```
              
En vous appuyant sur ces informations de base, vous devriez être en mesure de mieux comprendre et d’implémenter l’authentification unique au sein de votre application à l’aide de la plateforme et des Kits de développement logiciel (SDK) Microsoft Identity.


## Activation de l’authentification unique entre applications à l’aide de la bibliothèque ADAL

Ici, nous allons utiliser le Kit de développement logiciel (SDK) ADAL Android pour effectuer les opérations suivantes :

- Activer l’authentification unique assistée sans répartiteur pour votre suite d’applications
- Activer la prise en charge de l’authentification unique assistée avec répartiteur


### Activation de l’authentification unique assistée sans répartiteur

Les Kits de développement logiciel (SDK) Microsoft Identity prennent en charge une grande partie de la complexité de l’authentification unique assistée sans répartiteur entre les applications. Cela inclut l’identification de l’utilisateur approprié dans le cache et la gestion d’une liste d’utilisateurs connectés dans laquelle effectuer votre recherche.

Pour activer l’authentification unique sur l’ensemble des applications que vous possédez, vous devez effectuer les opérations suivantes :

1. Vérifiez que l’ensemble de vos applications utilisent le même ID client ou ID d’application.
* Vérifiez que toutes vos applications ont le même ensemble SharedUserID.
* Vérifiez que toutes vos applications partagent le même certificat de signature fourni par Google Play store, de manière à ce que vous puissiez partager le stockage.

#### Étape 1 : Utilisation du même ID client/ID d’application pour l’ensemble des applications de votre suite d’applications

Pour que votre plateforme Microsoft Identity puisse savoir que le partage des jetons est autorisé entre vos applications, elles doivent toutes partager le même ID client ou d’application. Il s’agit de l’identificateur unique qui vous a été fourni lorsque vous avez inscrit votre première application dans le portail.

Vous vous demandez peut-être comment identifier différentes applications utilisant le même ID d’application sur un service Microsoft Identity. La réponse est à chercher du côté des **URI de redirection**. Chaque application peut posséder plusieurs URI de redirection inscrits sur le portail d’intégration. Chacune des applications de votre suite présente un URI de redirection propre. Voici une représentation possible :

URI de redirection App1 : `msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D`

URI de redirection App2 : `msauth://com.example.userapp1/KmB7PxIytyLkbGHuI%2UitkW%2Fejk%4E`

URI de redirection App3 : `msauth://com.example.userapp2/Pt85PxIyvbLkbKUtBI%2SitkW%2Fejk%9F`

....

Ces éléments sont imbriqués sous le même ID client/ID d’application et recherchés en fonction de l’URI de redirection que vous nous transmettez dans votre configuration du Kit de développement logiciel (SDK).

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Le format de ces URI de redirection est expliqué plus bas. Vous pouvez utiliser l’URI de redirection de votre choix, sauf si vous souhaitez prendre en charge le répartiteur. Le cas échéant, la configuration est similaire à ce qui précède*


#### Étape 2 : Configuration du stockage partagé dans Android

La configuration de `SharedUserID` dépasse le cadre de ce document, mais vous pouvez en savoir plus à ce sujet en lisant la documentation Android de Google sur le paramètre [manifest](http://developer.android.com/guide/topics/manifest/manifest-element.html). L’essentiel est que vous déterminiez le nom que vous souhaitez attribuer à sharedUserID et que vous l’utilisiez dans l’ensemble de vos applications.

Une fois que `SharedUserID` est présent dans toutes vos applications, vous êtes prêt à vous servir de l’authentification unique.

> [AZURE.WARNING] 
Lorsque vous partagez un stockage entre vos applications, chaque application peut supprimer les utilisateurs, ou au pire l’ensemble des jetons de votre application. Cela aura un impact particulièrement désastreux si vous possédez des applications qui s’appuient sur les jetons pour exécuter les tâches d’arrière-plan. Le partage de stockage nécessite de votre part une précaution infinie avec l’ensemble des opérations de suppression effectuées dans les Kits de développement logiciel (SDK) Microsoft Identity.

Et voilà ! Le Kit de développement logiciel (SDK) partage désormais les informations d’identification dans l’ensemble de vos applications. La liste des utilisateurs sera également partagée entre toutes les instances d’application.

### Activation de l’authentification unique assistée avec répartiteur

La capacité d’une application à utiliser un répartiteur installé sur l’appareil est **désactivée par défaut**. Pour utiliser votre application avec le répartiteur, vous devez effectuer une configuration supplémentaire et ajouter du code à votre application.

Voici la procédure à suivre :

1. Activez le mode de répartiteur dans votre appel du code d’application vers le Kit de développement logiciel (SDK) MS.
2. Établir un nouvel URI de redirection à fournir à l’application et à votre inscription d’application.
3. Configuration des autorisations appropriées dans le manifeste Android


#### Étape 1 : Activer le mode répartiteur dans votre application
La capacité de votre application à utiliser le répartiteur est activée lorsque vous créez les paramètres, ou la configuration initiale, de votre instance d’authentification. Pour ce faire, définissez le type ApplicationSettings dans votre code :

```
AuthenticationSettings.Instance.setUseBroker(true);
```


#### Étape 2 : Établissez un nouvel URI de redirection avec votre schéma d’URL

Pour garantir que nous renvoyons toujours les jetons d’identification à l’application appropriée, nous devons nous assurer de respecter une procédure de rappel de votre application pouvant être vérifiée par votre système d’exploitation Android. Le système d’exploitation Android utilise le hachage du certificat dans Google Play Store. Il ne peut pas être falsifié par une application non fiable. Par conséquent, nous tirons parti de cette donnée et de l’URI de notre application de répartiteur pour vérifier que les jetons sont renvoyés à l’application appropriée. Nous vous demandons d’établir cet URI de redirection unique dans votre application et de le définir en tant qu’URI de redirection dans notre portail des développeurs.

Votre URI de redirection doit présenter la forme appropriée suivante :

`msauth://packagename/Base64UrlencodedSignature`

ex : *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Cet URI de redirection doit être spécifié dans l’inscription de votre application avec le [portail Azure Classic](https://manage.windowsazure.com/). Pour plus d’informations sur l’inscription d’applications Azure AD, consultez [Intégration avec Azure Active Directory](active-directory-how-to-integrate.md).


#### Étape 3: Configurer les autorisations appropriées dans votre application

Notre application de répartiteur dans Android utilise la fonctionnalité de gestionnaire de comptes du système d’exploitation Android pour gérer les informations d’identification entre les applications. Pour pouvoir utiliser le répartiteur dans Android, le manifeste de votre application doit être autorisé à utiliser les comptes AccountManager. Pour en savoir plus, consultez la [documentation Google pour la fonctionnalité AccountManager](http://developer.android.com/reference/android/accounts/AccountManager.html).

Ces autorisations sont les suivantes :

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### Vous avez configuré l’authentification unique !

Désormais, le Kit de développement logiciel (SDK) Microsoft Identity partage automatiquement les informations d’identification entre vos applications et appelle l’éventuel répartiteur existant sur l’appareil.

<!---HONumber=AcomDC_0921_2016-->