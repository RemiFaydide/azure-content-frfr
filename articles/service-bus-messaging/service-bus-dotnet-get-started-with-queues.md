<properties
    pageTitle="Prise en main des files d’attente Service Bus | Microsoft Azure"
    description="Comment écrire une application de console C# pour la messagerie Service Bus"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>

# Prise en main des files d’attente Service Bus

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## Les opérations que nous allons effectuer

Dans ce didacticiel, nous allons :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.

2. Créer une file d’attente Messagerie Service Bus à l’aide du Portail Azure.

3. Écrire une application de console pour envoyer un message.

4. Écrire une application de console pour recevoir des messages.

## Composants requis

1. [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2015.

2. Un abonnement Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1\. Créer un espace de noms à l’aide du Portail Azure

Si vous disposez déjà d’un espace de noms Service Bus créé précédemment, passez directement à la section [Créer une file d’attente à l’aide du Portail Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2\. Créer une file d’attente à l’aide du Portail Azure

Si vous disposez déjà d’une file d’attente Service Bus créée précédemment, passez directement à la section [Envoyer des messages à la file d’attente](#3-send-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3\. Envoyez des messages à la file d'attente

Pour envoyer des messages vers la file d’attente, nous allons écrire une application de console C# à l’aide de Visual Studio.

### Création d’une application console

1. Ouvrez Visual Studio et créez une nouvelle application de console.

### Ajout du package NuGet Service Bus

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.

2. Cliquez sur l’onglet **Parcourir**, puis recherchez « Microsoft Azure Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

    ![Sélectionner un package NuGet][nuget-pkg]

### Écrire du code pour envoyer un message vers la file d’attente

1. Ajoutez l’instruction using suivante au début du fichier Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Ajoutez le code ci-après à la méthode `Main` et définissez la variable **connectionString** en tant que chaîne de connexion obtenue lors de la création de l’espace de noms, et la variable **queueName** en tant que nom de file d’attente utilisé lors de la création de la file d’attente.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Voici à quoi doit ressembler votre fichier Program.cs.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. Exécutez le programme et vérifiez le Portail Azure. Cliquez sur le nom de votre file d’attente dans le panneau de l’espace de noms **Vue d’ensemble**. Notez que la valeur **Nombre de messages actifs** doit à présent être 1.
    
      ![Nombre de messages][queue-message]
    
## 4\. Recevoir les messages d'une file d'attente

1. Créez une autre application de console et ajoutez une référence au package NuGet Service Bus, identique à celle de l’application émettrice précédente.

2. Ajoutez l’instruction `using` suivante au début du fichier Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Ajoutez le code ci-après à la méthode `Main` et définissez la variable **connectionString** en tant que chaîne de connexion obtenue lors de la création de l’espace de noms, et la variable **queueName** en tant que nom de file d’attente utilisé lors de la création de la file d’attente.

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

	Voici à quoi doit ressembler votre fichier Program.cs :

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. Exécutez le programme et vérifiez le portail. Notez que la valeur **Longueur de la file d’attente** doit désormais correspondre à 0.

    ![Longueur de la file d’attente][queue-message-receive]
  
Félicitations ! Vous avez maintenant créé une file d’attente, envoyé un message et reçu un message.

## Étapes suivantes

Consultez les [exemples fournis dans notre référentiel GitHub](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Azure Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->