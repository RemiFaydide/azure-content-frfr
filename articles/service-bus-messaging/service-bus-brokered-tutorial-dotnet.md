<properties 
    pageTitle="Didacticiel .NET sur la messagerie répartie Service Bus | Microsoft Azure"
    description="Didacticiel .NET sur la messagerie répartie"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# Didacticiel .NET sur la messagerie répartie Service Bus

Azure Service Bus offre deux solutions complètes de messagerie : la première s’appuie sur un service de « relais » centralisé exécuté dans le cloud, qui prend en charge une large gamme de protocoles de transport et de services web standard, notamment SOAP, WS-* et REST. Le client n’a pas besoin d’une connexion directe au service local ni de savoir où se trouve le service, et le service local n’a pas besoin d’ouvrir de ports entrants sur le pare-feu.

La deuxième solution de messagerie offre des capacités de messagerie « répartie ». Ces capacités peuvent être considérées comme des fonctions de messagerie asynchrones ou découplées prenant en charge le découplage temporel de publication-souscription et les scénarios d’infrastructure d’équilibrage de charge à l’aide de l’infrastructure de messagerie Service Bus. La communication découplée présente de nombreux avantages ; par exemple, les clients et les serveurs peuvent se connecter en fonction des besoins et exécuter leurs opérations de manière asynchrone.

Ce didacticiel vise à vous donner un aperçu et une expérience pratique des files d'attente, un des principaux composants de la messagerie répartie Service Bus. Après avoir étudié les différentes rubriques de ce didacticiel, vous disposerez d’une application qui remplit une liste de messages, crée une file d’attente et envoie des messages à cette file d’attente. Enfin, l'application reçoit et affiche les messages de la file d'attente, puis nettoie ses ressources avant de se fermer. Pour obtenir le didacticiel correspondant qui explique comment générer une application utilisant Service Bus Relay, consultez le [didacticiel sur la messagerie relayée Service Bus](../service-bus-relay/service-bus-relay-tutorial.md).

## Introduction et conditions préalables

Les files d’attente permettent la remise de messages à un ou plusieurs destinataires concurrents sur le principe du premier entré, premier sorti (FIFO). Avec le principe FIFO, les messages sont généralement reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message sera reçu et traité par un seul consommateur de message uniquement. Un des principaux avantages de l'utilisation de files d'attente est la possibilité d’effectuer un *découplage temporel* des composants d'application : en d'autres termes, les producteurs et les consommateurs n’ont pas besoin de s’échanger des messages en même temps, puisque les messages sont stockés durablement dans la file d'attente. Un des avantages associés est le *nivellement de charge*, qui permet aux producteurs et aux consommateurs d’envoyer et de recevoir des messages à des vitesses différentes.

Voici quelques étapes d'administration et conditions préalables que vous devez suivre avant de commencer ce didacticiel : La première étape consiste à créer l’espace de noms de service et à obtenir une clé de signature d’accès partagé (SAP). Un espace de noms fournit une limite d’application pour chaque application exposée via Service Bus. Une clé SAP est automatiquement générée par le système lors de la création d’un espace de noms de service. La combinaison de l’espace de noms de service et de la clé SAP fournit à Service Bus une information d’identification permettant d’authentifier l’accès à une application.

### Créer un espace de noms de service et obtenir une clé SAP

La première étape consiste à créer l’espace de noms de service et à obtenir une clé de [signature d’accès partagé](../service-bus/service-bus-sas-overview.md) (SAP). Un espace de noms fournit une limite d’application pour chaque application exposée via Service Bus. Une clé SAP est automatiquement générée par le système lors de la création d’un espace de noms de service. La combinaison de l’espace de noms de service et de la clé SAP fournit à Service Bus une information d’identification permettant d’authentifier l’accès à une application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

L’étape suivante consiste à créer un projet Visual Studio et à écrire deux fonctions d’assistance qui chargent une liste délimitée par des virgules de messages dans un objet [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) fortement typé.

### Créer un projet Visual Studio

1. Ouvrez Visual Studio en tant qu’administrateur en cliquant avec le bouton droit sur le programme dans le menu Démarrer, puis en cliquant sur **Exécuter en tant qu’administrateur**.

1. Créez un projet d’application de console. Cliquez sur le menu **Fichier**, sélectionnez **Nouveau**, puis cliquez sur **Projet**. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Visual C#** (si **Visual C#** n’apparaît pas, regardez sous **Autres langages**), cliquez sur le modèle **Application console** et nommez-le **QueueSample**. Utilisez l’**emplacement** par défaut. Cliquez sur **OK** pour créer le projet.

1. Utilisez le gestionnaire de package NuGet pour ajouter les bibliothèques Service Bus à votre projet :
	1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **QueueSample**, puis cliquez sur **Gérer les packages NuGet**.
	2. Dans la boîte de dialogue **Gérer les packages NuGet**, cliquez sur l’onglet **Parcourir** et recherchez **Azure Service Bus**, puis cliquez sur **Installer**. <br />
1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs pour l’ouvrir dans l’éditeur de Visual Studio. Remplacez le nom par défaut de l’espace de noms `QueueSample` par `Microsoft.ServiceBus.Samples`.

	```
	Microsoft.ServiceBus.Samples
	{
	    ...
	```

1. Modifiez les instructions `using` comme indiqué dans le code suivant.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	```

1. Créez un fichier texte nommé Data.csv, puis copiez le texte délimité par des virgules suivant.

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Enregistrez et fermez le fichier Data.csv, puis mémorisez l’emplacement dans lequel vous l’avez enregistré.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom de votre projet (dans cet exemple, **QueueSample**), cliquez sur **Ajouter**, puis sur **Élément existant**.

1. Accédez au fichier Data.csv que vous avez créé à l’étape 6. Cliquez sur le fichier, puis sur **Ajouter**. Vérifiez que l’option **Tous les fichiers (*.*)** est sélectionnée dans la liste des types de fichiers.

### Créer une méthode qui analyse une liste de messages

1. Dans la classe `Program` avant la méthode `Main()`, déclarez deux variables : une de type **DataTable**, qui contiendra la liste des messages dans Data.csv. L’autre doit être un objet de type List, fortement typé en [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Cette dernière variable représente la liste des messages répartis qui sera utilisée dans les étapes suivantes du didacticiel.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. En dehors de `Main()`, définissez une méthode `ParseCSV()` qui analyse la liste des messages dans Data.csv puis charge les messages dans une table [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), comme indiqué ici. La méthode renvoie un objet **DataTable**.

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. Dans la méthode `Main()`, ajoutez une instruction qui appelle la méthode `ParseCSVFile()` :

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### Créer une méthode qui charge la liste des messages

1. En dehors de `Main()`, définissez une méthode `GenerateMessages()` qui prend l’objet **DataTable** renvoyé par `ParseCSVFile()` puis charge la table dans une liste fortement typée de messages répartis. La méthode renvoie alors l’objet **List**, comme dans l'exemple suivant.

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each row
	    foreach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. Dans `Main()`, directement après l’appel à `ParseCSVFile()`, ajoutez une instruction qui appelle la méthode `GenerateMessages()` avec la valeur renvoyée à partir de `ParseCSVFile()` comme argument :

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### Obtenir les informations d'identification de l'utilisateur

1. Commencez par créer trois variables de chaîne globales pour y stocker ces valeurs. Déclarez ces variables directement après les déclarations de variables précédentes ; par exemple :

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList; 

	        // Add these variables
			private static string ServiceNamespace;
	        private static string sasKeyName = "RootManageSharedAccessKey";
	        private static string sasKeyValue;
	        …
	```

1. Ensuite, créez une fonction qui accepte et stocke l'espace de noms de service et la clé SAP. Ajoutez cette méthode à l’extérieur de `Main()`. Par exemple :

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. Dans `Main()`, directement après l’appel à `GenerateMessages()`, ajoutez une instruction qui appelle la méthode `CollectUserInput()` :

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### Générez la solution.

Dans le menu **Générer** de Visual Studio, vous pouvez cliquer sur **Générer la solution** ou appuyer sur **Ctrl+Maj+B** pour confirmer si votre travail est correct.

## Créer des informations d’identification d’administration

Dans cette étape, vous définissez les opérations de gestion que vous allez utiliser pour créer les informations d'identification de signature d’accès partagé (SAP) qui serviront à autoriser votre application.

1. Pour plus de clarté, ce didacticiel place toutes les opérations de la file d'attente dans une méthode distincte. Créez une méthode `Queue()` async dans la classe `Program` après la méthode `Main()`. Par exemple :
 
	```
	public static void Main(string[] args)
	{
	…
	}
	static async Task Queue()
	{
	}
	```

1. L’étape suivante consiste à créer des informations d’identification SAP à l’aide d’un objet [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). Cette méthode de création utilise le nom de la clé SAP et la valeur obtenue avec la méthode `CollectUserInput()`. Ajoutez le code suivant à la méthode `Queue()` :

	```
	static async Task Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```

2. Créez un objet de gestion de l'espace de noms, avec un URI contenant l'espace de noms et les informations d'identification de gestion obtenues à l'étape précédente, en tant qu'arguments. Ajoutez ce code suivant directement après le code que vous avez ajouté à l’étape précédente. Assurez-vous de remplacer `<yourNamespace>` par le nom de votre espace de noms de service :
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
	```

### Exemple

À ce stade, votre code doit être semblable au code suivant :

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Envoyez des messages à la file d'attente

Dans cette étape, vous créez une file d'attente, puis envoyez les messages contenus dans la liste des messages répartis vers cette file d'attente.

### Créer une file d’attente et envoyer des messages vers cette file d’attente

1. Tout d’abord, créez la file d'attente. Par exemple, appelez-la `myQueue`, puis déclarez-la directement après les opérations de gestion que vous avez ajoutées à la méthode `Queue()` lors de l’étape précédente :

	```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. Dans la méthode `Queue()`, créez un objet de fabrique de messagerie avec un URI Bus de Service nouvellement créé en tant qu’argument. Ajoutez le code suivant directement après les opérations de gestion que vous avez ajoutées à l’étape précédente : Assurez-vous de remplacer `<yourNamespace>` par le nom de votre espace de noms de service :

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
	```

1. Puis créez l’objet de file d’attente en utilisant la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Ajoutez le code suivant directement après le code que vous avez ajouté à la dernière étape :

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. Puis ajoutez le code qui parcourt en boucle la liste des messages répartis que vous avez créée précédemment, envoyant ainsi chaque message à la file d'attente. Ajoutez le code suivant directement après l’instruction `CreateQueueClient()` de l'étape précédente :
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    await myQueueClient.SendAsync(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## Recevoir les messages d'une file d'attente

Dans cette étape, vous obtenez la liste des messages provenant de la file d'attente que vous avez créée à l'étape précédente.

### Créer un récepteur et recevoir les messages de la file d'attente

Dans la méthode `Queue()`, effectuez une itération dans la file d’attente et recevez les messages à l’aide de la méthode [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx), en imprimant chaque message sur la console. Ajoutez le code suivant directement après le code que vous avez ajouté à l’étape précédente :

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
	
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Notez que la méthode `Thread.Sleep` est utilisée uniquement pour simuler le traitement du message et qu’elle sera probablement inutile dans une application de messagerie réelle.

### Terminer la méthode Queue et nettoyer les ressources

Directement sous le code précédent, ajoutez le code suivant pour nettoyer la fabrique de messages et les ressources de la file d'attente :

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### Appeler la méthode Queue

La dernière étape consiste à ajouter une instruction qui appelle la méthode `Queue()` à partir de `Main()`. Ajoutez la ligne de code en surbrillance suivante à la fin de Main() :
	
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
	
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
	
    // Add this call
    Queue();
}
```

### Exemple

Le code suivant contient l’application **QueueSample** complète.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## Générer et exécuter l’application QueueSample

Maintenant que vous avez effectué les étapes ci-dessus, vous pouvez générer et exécuter l’application **QueueSample**.

### Générer l’application QueueSample

Dans le menu **Générer** de Visual Studio, cliquez sur **Générer la solution**, ou appuyez sur **Ctrl+Maj+B**. Si vous rencontrez des erreurs, vérifiez que votre code est correct en fonction de l'exemple complet présenté à la fin de l'étape précédente.

## Étapes suivantes

Ce didacticiel vous a montré comment créer une application cliente et un service Service Bus à l’aide des fonctionnalités de messagerie répartie Service Bus. Pour obtenir un didacticiel similaire utilisant Service Bus [Relay](service-bus-messaging-overview.md#Relayed-messaging), consultez le [didacticiel sur la messagerie relayée Service Bus](../service-bus-relay/service-bus-relay-tutorial.md).

Pour en savoir plus sur [Service Bus](https://azure.microsoft.com/services/service-bus/), consultez les rubriques qui suivent.

- [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
- [Concepts de base de Service Bus](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [Architecture de Service Bus](../service-bus/service-bus-architecture.md)

<!---HONumber=AcomDC_0928_2016-->