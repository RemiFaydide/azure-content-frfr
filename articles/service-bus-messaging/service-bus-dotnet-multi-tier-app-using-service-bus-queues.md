<properties
	pageTitle="Application multiniveau .NET | Microsoft Azure"
	description="Un didacticiel .NET qui vous permet de développer dans Azure une application multiniveau qui utilise les files d’attente de Service Bus pour communiquer entre les différents niveaux."
	services="service-bus-messaging"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus-messaging"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="09/01/2016"
	ms.author="sethm"/>

# Application multiniveau .NET avec les files d’attente Azure Service Bus

## Introduction

Le développement pour Microsoft Azure est simple grâce à Visual Studio et au Kit de développement logiciel (SDK) Azure gratuit pour .NET. Ce didacticiel vous guide lors de la création d’une application qui utilise plusieurs ressources Azure s’exécutant dans votre environnement local. Ces étapes partent du principe que vous n’avez pas d’expérience en tant qu’utilisateur d’Azure.

Vous allez apprendre les opérations suivantes :

-   configuration de votre ordinateur pour le développement Azure avec un seul téléchargement et une seule installation ;
-   utilisation de Visual Studio pour développer pour Azure ;
-   création d'une application multiniveau dans Azure avec les rôles Web et les rôles de travail ;
-   Communication entre niveaux à l’aide des files d’attente Service Bus.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

Dans ce didacticiel, vous allez générer et exécuter l'application multiniveau dans un service cloud Azure. Le composant frontal sera un rôle Web ASP.NET MVC et le composant principal sera un rôle de travail qui utilise une file d’attente Service Bus. Vous pouvez créer la même application multiniveau avec le composant frontal comme projet web déployé sur un site web Azure au lieu d’un service cloud. Pour obtenir des instructions sur les différences à appliquer sur le composant frontal d’un site Web Azure, consultez la section [Étapes suivantes](#nextsteps). Vous pouvez également essayer le didacticiel [Application hybride .NET locale/dans le cloud](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

Les captures d’écran suivantes présentent l’application terminée :

![][0]

## Vue d’ensemble du scénario : communication entre les rôles

Pour envoyer une commande en traitement, le composant frontal d’interface utilisateur qui s’exécute dans le rôle web doit communiquer avec la logique centrale qui s’exécute dans le rôle de travail. Cet exemple utilise la messagerie répartie Service Bus pour la communication entre les niveaux.

L'utilisation de la messagerie répartie entre les niveaux Web et central découple les deux composants. À l’inverse de la messagerie directe (TCP ou HTTP), le niveau web ne se connecte pas directement au niveau central. En effet, il envoie des unités de travail, sous forme de messages, à Service Bus, qui les retient jusqu’à ce que le niveau central soit prêt à les utiliser et à les traiter.

Service Bus fournit deux entités pour prendre en charge la messagerie répartie : les files d’attente et les rubriques. Avec les files d'attente, chaque message envoyé à la file d'attente est utilisé par un seul destinataire. Les rubriques prennent en charge le modèle de publication/d’abonnement, dans lequel chaque message publié est mis à la disposition d’un abonnement inscrit dans la rubrique. Chaque abonnement gère de façon logique sa propre file d’attente de messages. Les abonnements peuvent également être configurés avec des règles de filtrage, qui ne transmettent à la file d’attente de l’abonnement que les messages correspondant aux critères du filtre. L’exemple suivant utilise les files d’attente Service Bus.

![][1]

Ce mécanisme de communication présente plusieurs avantages par rapport à la messagerie directe :

-   **Découplage temporel.** Avec le modèle de messagerie asynchrone, les producteurs et les consommateurs n'ont pas besoin d'être en ligne en même temps. Service Bus stocke de manière fiable les messages jusqu'à ce que le destinataire soit prêt à les recevoir. Ceci permet aux composants de l’application distribuée d’être déconnectés, soit volontairement, par exemple pour des raisons de maintenance, soit en raison de l’échec d’un composant, sans que le système dans sa globalité soit affecté. De plus, l’application qui utilise les messages peut n’avoir besoin d’être en ligne que quelques fois par jour.

-   **Niveau de charge.** Dans de nombreuses applications, la charge système varie dans le temps, alors que le temps de traitement nécessaire à chaque élément de travail est normalement constant. L'ajout d'une file d'attente entre les producteurs et les consommateurs des messages fait que l'application de destination (le rôle de travail) n'a besoin d'être configurée que pour une charge de travail moyenne, plutôt que pour une charge de travail maximale. La file d’attente s’allonge et se raccourcit en fonction de la charge entrante. Ceci permet de faire des économies en termes d'infrastructures nécessaires pour faire face à la charge de travail de l'application.

-   **Équilibrage de la charge.** À mesure que la charge augmente, d'autres processus de travail peuvent être ajoutés pour lire les éléments de la file d'attente. Chaque message est traité par un seul des processus de travail. De plus, cet équilibrage de la charge basé sur l’extraction permet une utilisation optimale des ordinateurs de travail, même si ceux-ci diffèrent en termes de puissance de traitement, car ils extraient les messages au maximum de leur capacité. Ce modèle est souvent appelé modèle *consommateur concurrent*.

    ![][2]

Les sections qui suivent présentent le code de mise en œuvre de cette architecture.

## Configuration de l’environnement de développement

Avant de commencer à développer votre application Azure, procurez-vous les outils et configurez votre environnement de développement.

1.  Installer le Kit de développement logiciel Azure pour .NET à [obtenir des outils et Kit de développement logiciel][].

2. 	Cliquez sur **Installer le Kit de développement logiciel (SDK)** correspondant à votre version de Visual Studio. Les étapes de ce didacticiel utilisent Visual Studio 2015.

4.  Lorsque vous êtes invité à exécuter ou à enregistrer le programme d’installation, cliquez sur **Exécuter**.

5.  Dans **Web Platform Installer**, cliquez sur **Installer**, puis poursuivez l’installation.

6.  Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement de l’application. Le Kit de développement logiciel (SDK) comprend des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio. Si Visual Studio n’est pas installé, le Kit de développement logiciel (SDK) installe Visual Studio Express gratuitement.

## Créer un espace de noms

L’étape suivante consiste à créer l’espace de noms de service et à obtenir une clé de signature d’accès partagé (SAP). Un espace de noms fournit une limite d’application pour chaque application exposée via Service Bus. Le système génère automatiquement une clé SAP lors de la création d’un espace de noms. La combinaison de l’espace de noms et de la clé SAP fournit à Service Bus des informations d’identification permettant d’authentifier l’accès à une application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Création d'un rôle web

Dans cette section, vous générez le composant frontal de votre application. Tout d’abord, vous créez les pages affichées par votre application. Ensuite, ajoutez le code permettant d’envoyer les éléments à une file d’attente Service Bus et d’afficher les informations d’état de la file d’attente.

### Création du projet

1.  Avec les privilèges d’administrateur, démarrez Microsoft Visual Studio. Pour démarrer Visual Studio avec des privilèges d’administrateur, cliquez avec le bouton droit sur l’icône du programme **Visual Studio**, puis cliquez sur **Exécuter en tant qu’administrateur**. L’émulateur de calcul Azure, présenté plus loin dans cet article , nécessite que Visual Studio soit démarré avec les privilèges d’administrateur.

    Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

2.  Dans **Modèles installés**, sous **Visual C#**, cliquez sur **Cloud**, puis sur **Azure Cloud Services**. Nommez ce projet **MultiTierApp**. Cliquez ensuite sur **OK**.

    ![][9]

3.  Dans les rôles **.NET Framework 4.5**, double-cliquez sur **Rôle Web ASP.NET**.

    ![][10]

4.  Passez la souris sur **WebRole1** sous **Azure Cloud Service solution**, cliquez sur l'icône en forme de crayon et renommez le rôle Web **FrontendWebRole**. Cliquez ensuite sur **OK**. (Entrez bien « Frontend » avec un « e » minuscule, et non « FrontEnd ».)

    ![][11]

5.  Dans la liste **Sélectionner un modèle** de la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **MVC**.

    ![][12]

6. Toujours dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur le bouton **Modifier l’authentification**. Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**. Pour ce didacticiel, vous déployez une application qui n’a pas besoin de connexion de l’utilisateur.

	![][16]

7. Dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **OK** pour créer le projet.

6.  Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Références** dans le projet **FrontendWebRole**, puis cliquez sur **Gérer les packages NuGet**.

7.  Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **Installer** et acceptez les conditions d’utilisation.

    ![][13]

	Notez que les assemblys client nécessaires sont maintenant référencés et que certains nouveaux fichiers de code ont été ajoutés.

9.  Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **Modèles**, cliquez sur **Ajouter**, puis sur **Classe**. Dans la zone **Nom**, tapez le nom **OnlineOrder.cs**. Cliquez ensuite sur **Add**.

### Écriture du code de votre rôle Web

Dans cette section, vous créez les différentes pages affichées par votre application.

1.  Dans le fichier OnlineOrder.cs dans Visual Studio, remplacez la définition d'espace de noms existante par le code suivant :

	```
	namespace FrontendWebRole.Models
	{
	    public class OnlineOrder
	    {
	        public string Customer { get; set; }
	        public string Product { get; set; }
	    }
	}
	```

2.  Dans **Explorateur de solutions**, double-cliquez sur **Controllers\\HomeController.cs**. Ajoutez les instructions **using** suivantes au début du fichier pour inclure les espaces de noms pour le modèle que vous venez de créer, ainsi que Service Bus.

	```
	using FrontendWebRole.Models;
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.ServiceBus;
	```

3.  Également dans le fichier HomeController.cs dans Visual Studio, remplacez la définition d'espace de noms existante par le code suivant. Ce code contient des méthodes pour gérer l’envoi d’éléments dans la file d’attente.

	```
	namespace FrontendWebRole.Controllers
	{
	    public class HomeController : Controller
	    {
	        public ActionResult Index()
	        {
	            // Simply redirect to Submit, since Submit will serve as the
	            // front page of this application.
	            return RedirectToAction("Submit");
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        // GET: /Home/Submit.
	        // Controller method for a view you will create for the submission
	        // form.
	        public ActionResult Submit()
	        {
	            // Will put code for displaying queue message count here.
	
	            return View();
	        }
	
	        // POST: /Home/Submit.
	        // Controller method for handling submissions from the submission
	        // form.
	        [HttpPost]
			// Attribute to help prevent cross-site scripting attacks and
			// cross-site request forgery.  
			[ValidateAntiForgeryToken]
	        public ActionResult Submit(OnlineOrder order)
	        {
	            if (ModelState.IsValid)
	            {
	                // Will put code for submitting to queue here.
	
	                return RedirectToAction("Submit");
	            }
	            else
	            {
	                return View(order);
	            }
	        }
	    }
	}
	```

4.  Dans le menu **Générer**, cliquez sur **Générer la solution** pour vérifier si votre travail est correct.

5.  Maintenant, créez l’affichage de la méthode `Submit()` créée auparavant. Cliquez avec le bouton droit sur la méthode `Submit()` (la surcharge de `Submit()` qui n’accepte aucun paramètre), puis choisissez **Ajouter une vue**.

    ![][14]

6.  La boîte de dialogue qui s’affiche permet de créer l’affichage. Dans la liste **Modèle**, choisissez **Créer**. Dans la liste **Classe de modèle**, cliquez sur la classe **OnlineOrder**.

    ![][15]

7.  Cliquez sur **Add**.

8.  À présent, modifiez le nom affiché de votre application. Dans l'**Explorateur de solutions**, double-cliquez sur le fichier **Views\\Shared\\\\_Layout.cshtml** pour l'ouvrir dans l'éditeur de Visual Studio.

9.  Remplacez toutes les occurrences d'**Application ASP.NET** par **LITWARE'S Products**.

10. Supprimez les liens **Home**, **About** et **Contact**. Supprimez le code en surbrillance :

	![][28]

11. Enfin, modifiez la page d'envoi pour inclure des informations sur la file d'attente. Dans **Explorateur de solutions**, double-cliquez sur le fichier **Views\\Home\\Submit.cshtml** pour l'ouvrir dans l'éditeur de Visual Studio. Ajoutez la ligne suivante après `<h2>Submit</h2>`. À ce stade, `ViewBag.MessageCount` est vide. Vous le remplirez plus tard.

	```
	<p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
	```

12. Vous avez maintenant implémenté votre interface utilisateur. Vous pouvez appuyer sur **F5** pour exécuter votre application et vérifier qu'elle apparaît bien comme vous le souhaitez.

    ![][17]

### Écriture de code pour l'envoi d'éléments dans une file d'attente Service Bus

Maintenant, ajoutez le code pour envoyer des éléments dans une file d’attente. Tout d’abord, vous créez une classe qui contient les informations de connexion à votre file d’attente Service Bus. Ensuite, initialisez votre connexion à partir de Global.aspx.cs. Enfin, mettez à jour le code d’envoi que vous avez créé précédemment dans HomeController.cs pour qu’il envoie réellement les éléments dans une file d’attente Service Bus.

1.  Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **FrontendWebRole** (sur le projet, et non sur le rôle). Cliquez sur **Add**, puis sur **Class**.

2.  Donnez le nom **QueueConnector.cs** à la classe. Cliquez sur **Add** pour créer la classe.

3.  Maintenant, ajoutez le code qui encapsule les informations de connexion et initialise la connexion à une file d’attente Service Bus. Remplacez tout le contenu de QueueConnector.cs par le code suivant, puis entrez des valeurs pour `your Service Bus namespace` (nom de votre espace de noms) et `yourKey` qui correspond à la **clé primaire** précédemment obtenue à partir du portail Azure.

	```
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.ServiceBus;
	
	namespace FrontendWebRole
	{
	    public static class QueueConnector
	    {
	        // Thread-safe. Recommended that you cache rather than recreating it
	        // on every request.
	        public static QueueClient OrdersQueueClient;
	
	        // Obtain these values from the portal.
	        public const string Namespace = "your Service Bus namespace";
	
	        // The name of your queue.
	        public const string QueueName = "OrdersQueue";
	
	        public static NamespaceManager CreateNamespaceManager()
	        {
	            // Create the namespace manager which gives you access to
	            // management operations.
	            var uri = ServiceBusEnvironment.CreateServiceUri(
	                "sb", Namespace, String.Empty);
	            var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
	                "RootManageSharedAccessKey", "yourKey");
	            return new NamespaceManager(uri, tP);
	        }
	
	        public static void Initialize()
	        {
	            // Using Http to be friendly with outbound firewalls.
	            ServiceBusEnvironment.SystemConnectivity.Mode =
	                ConnectivityMode.Http;
	
	            // Create the namespace manager which gives you access to
	            // management operations.
	            var namespaceManager = CreateNamespaceManager();
	
	            // Create the queue if it does not exist already.
	            if (!namespaceManager.QueueExists(QueueName))
	            {
	                namespaceManager.CreateQueue(QueueName);
	            }
	
	            // Get a client to the queue.
	            var messagingFactory = MessagingFactory.Create(
	                namespaceManager.Address,
	                namespaceManager.Settings.TokenProvider);
	            OrdersQueueClient = messagingFactory.CreateQueueClient(
	                "OrdersQueue");
	        }
	    }
	}
	```

4.  Maintenant, assurez-vous que votre méthode **Initialize** est bien appelée. Dans **Explorateur de solutions**, double-cliquez sur **Global.asax\\Global.asax.cs**.

5.  Ajoutez la ligne de code suivante à la fin de la méthode **Application\_Start**.

	```
	FrontendWebRole.QueueConnector.Initialize();
	```

6.  Enfin, mettez à jour le code web que vous avez créé précédemment, pour envoyer des éléments dans la file d'attente. Dans **Explorateur de solutions**, double-cliquez sur **Controllers\\HomeController.cs**.

7.  Mettez à jour la méthode `Submit()` (la surcharge qui n’accepte aucun paramètre) comme suit pour obtenir le nombre de messages dans la file d’attente.

	```
	public ActionResult Submit()
	{
	    // Get a NamespaceManager which allows you to perform management and
	    // diagnostic operations on your Service Bus queues.
	    var namespaceManager = QueueConnector.CreateNamespaceManager();
	
	    // Get the queue, and obtain the message count.
	    var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
	    ViewBag.MessageCount = queue.MessageCount;
	
	    return View();
	}
	```

8.  Mettez à jour la méthode `Submit(OnlineOrder order)` (la surcharge qui accepte un paramètre) comme suit pour envoyer des informations de commande à la file d’attente.

	```
	public ActionResult Submit(OnlineOrder order)
	{
	    if (ModelState.IsValid)
	    {
	        // Create a message from the order.
	        var message = new BrokeredMessage(order);
	
	        // Submit the order.
	        QueueConnector.OrdersQueueClient.Send(message);
	        return RedirectToAction("Submit");
	    }
	    else
	    {
	        return View(order);
	    }
	}
	```

9.  Vous pouvez maintenant réexécuter l'application. À chaque fois que vous envoyez une commande, le nombre de messages augmente.

    ![][18]

## Création du rôle de travail

Vous allez maintenant créer le rôle de travail qui traite les commandes envoyées. Cet exemple utilise le modèle de projet Visual Studio **Worker Role with Service Bus Queue**. Vous avez déjà obtenu les informations d’identification requises à partir du portail.

1. Assurez-vous d’avoir connecté Visual Studio à votre compte Azure.

2.  Dans Visual Studio, dans **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Rôles** sous le projet **MultiTierApp**.

3.  Cliquez sur **Ajouter**, puis sur **Nouveau projet de rôle de travail**. La boîte de dialogue **Ajouter un nouveau projet de rôle** s’affiche.

	![][26]

4.  Dans la boîte de dialogue **Ajouter un nouveau projet de rôle**, cliquez sur **Worker Role with Service Bus Queue**.

	![][23]

5.  Dans la boîte de dialogue **Nom**, entrez le nom du projet **OrderProcessingRole**. Cliquez ensuite sur **Add**.

6.  Copiez la chaîne de connexion que vous avez obtenue à l’étape 9 de la section « Création d’un espace de noms Service Bus » dans le Presse-papiers.

7.  Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **OrderProcessingRole**, créé à l’étape 5 (assurez-vous de bien cliquer avec le bouton droit sur **OrderProcessingRole** sous **Rôles**, et pas dans la classe). Cliquez ensuite sur **Propriétés**.

8.  Sous l’onglet **Paramètres** de la boîte de dialogue **Propriétés**, cliquez dans la zone **Valeur** pour **Microsoft.ServiceBus.ConnectionString**, puis collez la valeur du point de terminaison que vous avez copiée à l’étape 6.

	![][25]

9.  Créez une classe **OnlineOrder** pour représenter les commandes à mesure que vous les traitez dans la file d'attente. Vous pouvez réutiliser une classe que vous avez déjà créée. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la classe **OrderProcessingRole** (sur l’icône de la classe, pas sur le rôle). Cliquez sur **Ajouter**, puis sur **Élément existant**.

10. Accédez au sous-dossier **FrontendWebRole\\Models**, puis double-cliquez sur **OnlineOrder.cs** pour l’ajouter à ce projet.

11. Dans **WorkerRole.cs**, changez la valeur de la variable **QueueName** de `"ProcessingQueue"` en `"OrdersQueue"`, comme dans le code suivant.

	```
	// The name of your queue.
	const string QueueName = "OrdersQueue";
	```

12. Ajoutez l’instruction d’utilisation suivante au début du fichier WorkerRole.cs.

	```
	using FrontendWebRole.Models;
	```

13. Dans l’appel `OnMessage()` de la fonction `Run()`, remplacez le contenu de la clause `try` par le code suivant.

	```
	Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
	// View the message as an OnlineOrder.
	OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
	Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
	receivedMessage.Complete();
	```

14. Vous avez terminé l'application. Vous pouvez tester l'application complète en double-cliquant sur le projet MultiTierApp dans l'Explorateur de solutions, puis en sélectionnant **Définir comme projet de démarrage** et en appuyant sur F5. Notez que le nombre de messages n'augmente pas, car le rôle de travail traite les éléments de la file d'attente et les marque comme terminés. Vous pouvez voir le résultat du suivi de votre rôle de travail en affichant l'interface utilisateur de l'émulateur de calcul Azure. Pour cela, cliquez avec le bouton droit sur l'icône de l'émulateur dans la zone de notification de la barre des tâches, puis sélectionnez **Show Compute Emulator UI**.

    ![][19]

    ![][20]

## Étapes suivantes  

Pour en savoir plus sur Service Bus, consultez les ressources suivantes :

* [Azure Service Bus][sbmsdn]
* [Page du service Service Bus][sbwacom]
* [Utilisation des files d'attente Service Bus][sbwacomqhowto]

Pour en savoir plus sur les scénarios à plusieurs niveaux, voir :

* [Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage Azure.][mutitierstorage]

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [obtenir des outils et Kit de développement logiciel]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  

<!---HONumber=AcomDC_0928_2016-->