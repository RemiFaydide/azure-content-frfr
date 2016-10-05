<properties
	pageTitle="Application hybride locale/dans le cloud (.NET) | Microsoft Azure"
	description="Découvrez comment créer une application hybride locale/de cloud .NET à l’aide d’Azure Service Bus Relay."
	services="service-bus-relay"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus-relay"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/16/2016"
	ms.author="sethm"/>

# Application .NET locale/cloud hybride avec Azure Service Bus Relay

## Introduction

Cet article décrit comment créer une application cloud hybride avec Microsoft Azure et Visual Studio. Le didacticiel part du principe que vous n’avez pas d’expérience en tant qu’utilisateur d’Azure. En moins de 30 minutes, vous disposez d’une application qui utilise plusieurs ressources Azure s’exécutant dans le cloud.

Vous apprendrez à effectuer les opérations suivantes :

-   créer ou adapter un service Web existant qui sera utilisé par une solution Web ;
-   utiliser le service Azure Service Bus Relay pour partager des données entre une application Azure et un service web hébergé ailleurs.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Avantages de l’utilisation de service bus relay pour les solutions hybrides

Les solutions d'entreprise se composent généralement d'une combinaison de code personnalisé écrit pour répondre aux exigences nouvelles et uniques de l'entreprise ainsi qu'aux fonctionnalités existantes offertes par des solutions et des systèmes déjà en place.

Les architectes de solutions commencent à utiliser le cloud, car celui-ci permet de gérer plus facilement les exigences de mise à l'échelle tout en offrant des coûts opérationnels faibles. Ainsi, ils découvrent que les ressources des services existants qu'ils souhaiteraient exploiter comme des blocs de construction pour leurs solutions se situent à l'intérieur du pare-feu d'entreprise et hors de portée pour une solution cloud. Bon nombre de services internes ne sont pas créés ni hébergés pour être facilement exposés dans le périmètre du réseau d'entreprise.

Service Bus Relay est conçu pour rendre accessibles les services Web WCF (Windows Communication Foundation) existants, d’une manière sécurisée, aux solutions qui résident à l’extérieur du périmètre de l’entreprise sans exiger de modifications intrusives de l’infrastructure de réseau d’entreprise. Ces services de relais Service Bus sont toujours hébergés dans leur environnement existant, mais ils délèguent l’écoute des sessions et demandes entrantes au Service Bus hébergé sur le cloud. Service Bus protège également ces services de tout accès non autorisé à l’aide de l’authentification par [signature d’accès partagé](../service-bus/service-bus-sas-overview.md) (SAP).

## Scénario de la solution

Dans ce didacticiel, vous allez créer un site web ASP.NET qui vous permet de voir une liste de produits sur la page d’inventaire des produits.

![][0]

Ce didacticiel part du principe que vous disposez des informations sur les produits dans un système local existant, et utilise Service Bus Relay pour atteindre ce système. La simulation met en scène un service Web exécuté dans une simple application console et appuyé par un ensemble de produits en mémoire. Vous allez pouvoir exécuter cette application console sur votre propre ordinateur et déployer le rôle Web dans Azure. Ainsi, vous verrez comment procède le rôle Web en cours d'exécution dans le centre de données Azure pour appeler votre ordinateur, même si ce dernier réside certainement derrière au moins un pare-feu et une couche de traduction d'adresses réseau (NAT, network address translation).

La capture d'écran suivante montre la page d'accueil de l'application Web terminée.

![][1]

## Configuration de l’environnement de développement

Avant de commencer à développer votre application Azure, procurez-vous les outils et configurez votre environnement de développement.

1.  Installez le Kit de développement logiciel (SDK) Azure pour .NET depuis la page [Obtenir les outils et le Kit de développement logiciel (SDK)][].

2. 	Cliquez sur **Installer le Kit de développement logiciel (SDK)** correspondant à votre version de Visual Studio. Les étapes de ce didacticiel utilisent Visual Studio 2015.

4.  Lorsque vous êtes invité à exécuter ou à enregistrer le programme d’installation, cliquez sur **Exécuter**.

5.  Dans **Web Platform Installer**, cliquez sur **Installer**, puis poursuivez l’installation.

6.  Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement de l’application. Le Kit de développement logiciel (SDK) comprend des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio. Si Visual Studio n’est pas installé, le Kit de développement logiciel (SDK) installe Visual Studio Express gratuitement.

## Créer un espace de noms

Pour commencer à utiliser les fonctionnalités de Service Bus dans Azure, vous devez d'abord créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Création d’un serveur local

Vous créez d'abord un système local de catalogue de produits (fictif). Cela est assez simple : représentez-vous un système réel de catalogue de produits local avec une surface complète de services que nous essayons d'intégrer.

Ce projet est une application console Visual Studio et utilise le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pour inclure les bibliothèques et les paramètres de configuration Service Bus.

### Création du projet

1.  Avec les privilèges d’administrateur, démarrez Microsoft Visual Studio. Pour démarrer Visual Studio avec des privilèges d’administrateur, cliquez avec le bouton droit sur l’icône du programme **Visual Studio**, puis cliquez sur **Exécuter en tant qu’administrateur**.

2.  Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans **Modèles installés**, sous **Visual C#**, cliquez sur **Application console**. Dans la zone **Nom**, saisissez le nom **ProductsServer** :

    ![][11]

4.  Cliquez sur **OK** pour créer le projet **ProductsServer**.

7.  Si vous avez déjà installé le gestionnaire de package NuGet pour Visual Studio, passez à l'étape suivante. Sinon, accédez à [NuGet][], puis cliquez sur [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Suivez les invites pour installer le gestionnaire de package NuGet, puis redémarrez Visual Studio.

7.  Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsServer**, puis cliquez sur **Gérer les packages NuGet**.

8.  Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **Installer** et acceptez les conditions d’utilisation.

    ![][13]

    Notez que les assemblys client nécessaires sont maintenant référencés.

9.  Ajoutez une nouvelle classe pour votre contrat de produit. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsServer**, cliquez sur **Ajouter**, puis sur **Classe**.

10. Dans la zone **Nom**, entrez le nom **ProductsContract.cs**. Cliquez ensuite sur **Add**.

11. Dans **ProductsContract.cs**, remplacez la définition d’espace de noms existante par le code suivant, qui définit le contrat du service.

	```
	namespace ProductsServer
	{
	    using System.Collections.Generic;
	    using System.Runtime.Serialization;
	    using System.ServiceModel;
	
	    // Define the data contract for the service
	    [DataContract]
	    // Declare the serializable properties.
	    public class ProductData
	    {
	        [DataMember]
	        public string Id { get; set; }
	        [DataMember]
	        public string Name { get; set; }
	        [DataMember]
	        public string Quantity { get; set; }
	    }
	
	    // Define the service contract.
	    [ServiceContract]
	    interface IProducts
	    {
	        [OperationContract]
	        IList<ProductData> GetProducts();
	
	    }
	
	    interface IProductsChannel : IProducts, IClientChannel
	    {
	    }
	}
	```

12. Dans Program.cs, remplacez la définition d’espace de noms existante par le code suivant, qui ajoute le service de profil et l’hôte correspondant :

	```
	namespace ProductsServer
	{
	    using System;
	    using System.Linq;
	    using System.Collections.Generic;
	    using System.ServiceModel;
	
	    // Implement the IProducts interface.
	    class ProductsService : IProducts
	    {
	
	        // Populate array of products for display on website
	        ProductData[] products =
	            new []
	                {
	                    new ProductData{ Id = "1", Name = "Rock",
	                                     Quantity = "1"},
	                    new ProductData{ Id = "2", Name = "Paper",
	                                     Quantity = "3"},
	                    new ProductData{ Id = "3", Name = "Scissors",
	                                     Quantity = "5"},
	                    new ProductData{ Id = "4", Name = "Well",
	                                     Quantity = "2500"},
	                };
	
	        // Display a message in the service console application
	        // when the list of products is retrieved.
	        public IList<ProductData> GetProducts()
	        {
	            Console.WriteLine("GetProducts called.");
	            return products;
	        }
	
	    }
	
	    class Program
	    {
	        // Define the Main() function in the service application.
	        static void Main(string[] args)
	        {
	            var sh = new ServiceHost(typeof(ProductsService));
	            sh.Open();
	
	            Console.WriteLine("Press ENTER to close");
	            Console.ReadLine();
	
	            sh.Close();
	        }
	    }
	}
	```

13. Dans l’Explorateur de solutions, double-cliquez sur le fichier **App.config** pour l’ouvrir dans l’éditeur de Visual Studio. En bas de l’élément **&lt;system.ServiceModel&gt;** (mais toujours dans &lt;system.ServiceModel&gt;), ajoutez le code XML suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms, et *yourKey* par la clé SAS que vous avez récupérée précédemment sur le portail :

    ```
    <system.serviceModel>
	...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Toujours dans le fichier App.config, dans l’élément **&lt;appSettings&gt;**, remplacez la valeur de la chaîne de connexion par la chaîne de connexion obtenue précédemment à partir du portail.

	```
	<appSettings>
   	<!-- Service Bus specific app settings for messaging connections -->
   	<add key="Microsoft.ServiceBus.ConnectionString"
	       value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
	</appSettings>
	```

14. Appuyez sur **Ctrl+Maj+B** ou, dans le menu **Générer**, cliquez sur **Générer la solution** pour générer l’application et vérifier si votre travail est correct.

## Création d’une application ASP.NET

Dans cette section, vous allez générer une application ASP.NET simple qui affiche des données récupérées de votre service de produit.

### Création du projet

1.  Vérifiez que Visual Studio fonctionne avec les privilèges d’administrateur.

2.  Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans **Modèles installés**, sous **Visual C#**, cliquez sur **Application Web ASP.NET**. Nommez le projet **ProductsPortal**. Cliquez ensuite sur **OK**.

    ![][15]

4.  Dans la liste **Sélectionner un modèle**, cliquez sur **MVC**.

6.  Cochez la case **Hôte dans le cloud**.

    ![][16]

5. Cliquez sur le bouton **Modifier l’authentification**. Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**. Pour ce didacticiel, vous déployez une application qui n’a pas besoin de connexion de l’utilisateur.

	![][18]

6. 	Dans la section **Microsoft Azure** de la boîte de dialogue **Nouveau projet ASP.NET**, vérifiez que **Hôte dans le cloud** est sélectionné et que **App Service** est sélectionné dans la liste déroulante.

	![][19]

7. Cliquez sur **OK**.

8. Vous devez maintenant configurer les ressources Azure d’une nouvelle application web. Suivez toutes les étapes de la section [Configurer les ressources Azure d’une nouvelle application web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Ensuite, revenez à ce didacticiel et passez à l’étape suivante.

5.  Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Modèles**, puis cliquez sur **Ajouter** et sur **Classe**. Dans la zone **Nom**, entrez le nom **Products.cs**. Cliquez ensuite sur **Add**.

    ![][17]

### Modification de l’application Web

1.  Dans le fichier Product.cs dans Visual Studio, remplacez la définition d’espace de noms existante par le code suivant.

	```
	// Declare properties for the products inventory.
 	namespace ProductsWeb.Models
	{
    	public class Product
    	{
    	    public string Id { get; set; }
    	    public string Name { get; set; }
    	    public string Quantity { get; set; }
    	}
	}
	```

2.  Dans l’Explorateur de solutions, développez le dossier **Controllers** et double-cliquez sur le fichier **HomeController.cs** pour l’ouvrir dans Visual Studio.

3. Dans le fichier **HomeController.cs**, remplacez la définition d’espace de noms par le code suivant.

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Collections.Generic;
	    using System.Web.Mvc;
	    using Models;
	
	    public class HomeController : Controller
	    {
	        // Return a view of the products inventory.
	        public ActionResult Index(string Identifier, string ProductName)
	        {
	            var products = new List<Product>
	                {new Product {Id = Identifier, Name = ProductName}};
	            return View(products);
	        }
	     }
	}
	```

3.  Dans l’Explorateur de solutions, développez le dossier Views\\Shared, puis double-cliquez sur **\_Layout.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio.

5.  Remplacez toutes les occurrences de **Mon application ASP.NET** par **LITWARE's Products**.

6. Supprimez les liens **Home**, **About** et **Contact**. Dans l’exemple suivant, supprimez le code en surbrillance.

	![][41]

7.  Dans l’Explorateur de solutions, développez le dossier Views\\Home, puis double-cliquez sur **Index.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio. Remplacez tout le contenu du fichier par le code suivant.

	```
	@model IEnumerable<ProductsWeb.Models.Product>
	
	@{
	 		ViewBag.Title = "Index";
	}
	
	<h2>Prod Inventory</h2>
	
	<table>
	  		<tr>
	      		<th>
	          		@Html.DisplayNameFor(model => model.Name)
	      		</th>
	              <th></th>
	      		<th>
	          		@Html.DisplayNameFor(model => model.Quantity)
	      		</th>
	  		</tr>
	
	@foreach (var item in Model) {
	  		<tr>
	      		<td>
	          		@Html.DisplayFor(modelItem => item.Name)
	      		</td>
	      		<td>
	          		@Html.DisplayFor(modelItem => item.Quantity)
	      		</td>
	  		</tr>
	}
	
	</table>
	```

9.  Pour vérifier la qualité de votre travail, appuyez sur **Ctrl+Maj+B** pour générer le projet.


### Exécutez l’application localement.

Exécutez l'application afin de vérifier qu'elle fonctionne.

1.  Assurez-vous que **ProductsPortal** est le projet actif. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Définir comme projet de démarrage**.
2.  Dans Visual Studio, appuyez sur F5.
3.  Votre application doit s’exécuter dans un navigateur :

    ![][21]

## Assemblage des éléments

La prochaine étape consiste à raccorder le serveur de produits local et l’application ASP.NET.

1.  S’il n’est pas déjà ouvert, rouvrez dans Visual Studio le projet **ProductsPortal** que vous avez créé dans la section [Création d’une application ASP.NET](#create-an-aspnet-application).

2.  Comme vous l’avez fait dans la section « Création d’un serveur local », ajoutez le package NuGet aux références du projet. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsPortal**, puis cliquez sur **Gérer les packages NuGet**.

3.  Recherchez « Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Ensuite, terminez l’installation et fermez cette boîte de dialogue.

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsPortal**, cliquez sur **Ajouter**, puis sur **Élément existant**.

5.  Accédez au fichier **ProductsContract.cs** depuis le projet de console **ProductsServer**. Cliquez sur ProductsContract.cs afin de le mettre en surbrillance. Cliquez sur la flèche vers le bas en regard d’**Ajouter**, puis cliquez sur **Ajouter en tant que lien**.

	![][24]

6.  Ouvrez à présent le fichier **HomeController.cs** dans l'éditeur Visual Studio, puis remplacez la définition d'espace de noms existante par le code suivant. Assurez-vous de remplacer *yourServiceNamespace* par le nom de votre espace de noms de service, puis *yourKey* par votre clé SAP. Le client peut alors appeler le service local, en renvoyant le résultat de l'appel.

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Linq;
	    using System.ServiceModel;
	    using System.Web.Mvc;
	    using Microsoft.ServiceBus;
	    using Models;
	    using ProductsServer;
	
	    public class HomeController : Controller
	    {
	        // Declare the channel factory.
	        static ChannelFactory<IProductsChannel> channelFactory;
	
	        static HomeController()
	        {
	            // Create shared access signature token credentials for authentication.
	            channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
	                "sb://yourServiceNamespace.servicebus.windows.net/products");
	            channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
	                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
	                    "RootManageSharedAccessKey", "yourKey") });
	        }
	
	        public ActionResult Index()
	        {
	            using (IProductsChannel channel = channelFactory.CreateChannel())
	            {
	                // Return a view of the products inventory.
	                return this.View(from prod in channel.GetProducts()
	                                 select
	                                     new Product { Id = prod.Id, Name = prod.Name,
	                                         Quantity = prod.Quantity });
	            }
	        }
	    }
	}
	```

7.  Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution **ProductsPortal** (veillez à cliquer sur la solution et non sur le projet). Cliquez sur **Ajouter**, puis sur **Projet existant**.

8.  Accédez au projet **ProductsServer**, puis double-cliquez sur le fichier solution **ProductsServer.csproj** pour l'ajouter.

9.  **ProductsServer** doit être en cours d’exécution pour afficher les données sur **ProductsPortal**. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution **ProductsPortal**, puis cliquez sur **Propriétés**. La boîte de dialogue **Pages de propriétés** s’affiche.

10. Sur le côté gauche, cliquez sur **Projet de démarrage**. Sur le côté droit, cliquez sur **Plusieurs projets de démarrage**. Vérifiez que **ProductsServer** et **ProductsPortal** apparaissent dans cet ordre et que leur action définie est **Démarrer**.

      ![][25]

11. Toujours dans la boîte de dialogue **Propriétés**, cliquez sur **Dépendances du projet** sur le côté gauche.

12. Dans le menu déroulant **Projets**, cliquez sur **ProductsServer**. Vérifiez que **ProductsPortal** n’est **pas** sélectionné.

14. Dans la liste **Projets**, cliquez sur **ProductsPortal**. Vérifiez que **ProductsServer** est sélectionné.

    ![][26]

15. Cliquez sur **OK** dans la boîte de dialogue **Pages de propriétés**.

## Exécutez le projet localement.

Pour tester l’application localement dans Visual Studio, appuyez sur **F5**. Le serveur local (**ProductsServer**) doit démarrer en premier, puis l’application **ProductsPortal** doit démarrer dans une fenêtre de navigateur. À présent, vous voyez que l'inventaire de produits répertorie des données récupérées du système local de service de produit.

![][10]

Appuyez sur **Actualiser** dans la page **ProductsPortal**. Chaque fois que vous actualisez la page, l’application de serveur affiche un message lors de l’appel de `GetProducts()` à partir de **ProductsServer**.

Fermez les deux applications avant de passer à l’étape suivante.

## Déploiement du projet ProductsPortal dans une application web Azure

L’étape suivante consiste à convertir le serveur frontal **ProductsPortal** en application web Azure. Déployez d’abord le projet **ProductsPortal**, en suivant toutes les étapes de la section [Déploiement du projet web dans l’application web Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Une fois le déploiement terminé, revenez à ce didacticiel et passez à l’étape suivante.

> [AZURE.NOTE] Un message d’erreur peut s’afficher dans la fenêtre du navigateur lorsque le projet web **ProductsPortal** est démarré automatiquement après le déploiement. Ce comportement est normal. Il s’explique par le fait que l’application **ProductsServer** n’est pas encore en cours d’exécution.

Copiez l’URL de l’application web déployée, car vous en aurez besoin à l’étape suivante. Vous pouvez également obtenir cette URL à partir de la fenêtre Activité d’Azure App Service dans Visual Studio :

![][9]

### Définition de ProductsPortal en tant qu’application web

Avant d’exécuter l’application dans le cloud, vous devez vous assurer que **ProductsPortal** est démarré dans Visual Studio en tant qu’application web.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **ProjectsPortal**, puis cliquez sur **Propriétés**.

3. Dans la colonne de gauche, cliquez sur **Web**.

5. Dans la section **Action de démarrage**, cliquez sur le bouton **URL de démarrage** puis, dans la zone de texte, entrez l’URL de l’application web précédemment déployée. Par exemple, `http://productsportal1234567890.azurewebsites.net/`.

	![][27]

6. Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

7. Dans le menu Générer de Visual Studio, cliquez sur **Régénérer la solution**.

## Exécution de l’application

2.  Appuyez sur F5 pour générer et exécuter l’application. Le serveur local (application de console **ProductsServer**) doit démarrer en premier, puis l’application**ProductsPortal** doit démarrer dans une fenêtre de navigateur, comme le montre la capture d’écran suivante. Notez de nouveau que l’inventaire de produits répertorie les données récupérées à partir du système local du service du produit et les affiche dans l’application web. Vérifiez l’URL pour vous assurer que **ProductsPortal** est exécutée dans le cloud comme une application web Azure.

    ![][1]

	> [AZURE.IMPORTANT] L’application console **ProductsServer** doit être en cours d’exécution et en mesure de fournir les données à l’application **ProductsPortal**. Si le navigateur affiche une erreur, patientez quelques secondes, le temps que **ProductsServer** se charge et affiche le message suivant. Appuyez ensuite sur **Actualiser** dans le navigateur.

	![][37]

3. Dans le navigateur, appuyez sur **Actualiser** sur la page **ProductsPortal**. Chaque fois que vous actualisez la page, l’application de serveur affiche un message lors de l’appel de `GetProducts()` à partir de **ProductsServer**.

	![][38]

## Étapes suivantes  

Pour en savoir plus sur Service Bus, consultez les ressources suivantes :

* [Azure Service Bus][sbwacom]
* [Utilisation des files d'attente Service Bus][sbwacomqhowto]


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtenir les outils et le Kit de développement logiciel (SDK)]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

<!---HONumber=AcomDC_0928_2016-->