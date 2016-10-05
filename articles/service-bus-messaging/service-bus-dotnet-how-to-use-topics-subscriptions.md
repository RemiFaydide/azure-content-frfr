<properties
    pageTitle="Utilisation des rubriques Service Bus avec .NET |Microsoft Azure"
    description="Découvrez comment utiliser les rubriques et abonnements Service Bus avec .NET dans Azure. Les exemples de code sont écrits pour les applications .NET."
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# Utilisation des rubriques et abonnements Service Bus

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article explique comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en C# et utilisent les API .NET. Les scénarios couverts incluent la création de rubriques et d’abonnements, la création de filtres d’abonnement, l’envoi de messages à une rubrique, la réception de messages en provenance d’un abonnement, et enfin la suppression de rubriques et d’abonnements. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configuration de l'application pour l'utilisation de Service Bus

Lorsque vous créez une application qui fait appel à Service Bus, vous devez ajouter une référence à l'assembly Service Bus et inclure les espaces de noms correspondants. Le plus simple consiste à télécharger le package [NuGet](https://www.nuget.org) approprié.

## Obtention du package NuGet Service Bus

Le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) constitue le moyen le plus simple de se procurer l’API Service Bus et de configurer votre application avec toutes les dépendances Service Bus nécessaires. Pour installer le package NuGet Service Bus dans votre projet, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.
2.  Recherchez « Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez la boîte de dialogue suivante.

    ![][7]

Vous êtes maintenant en mesure d’écrire du code pour Service Bus.

## Création d’une chaîne de connexion Service Bus

Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d’identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, au lieu de la coder en dur :

- Quand vous utilisez les services Azure, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers .csdef et .cscfg).
- Quand vous utilisez les sites Web Azure ou Microsoft Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, le fichier Web.config).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, comme indiqué plus loin dans cet article.

### Configuration de votre chaîne de connexion

Le mécanisme de configuration de service vous permet de modifier les paramètres de configuration de façon dynamique depuis le [portail Azure][], sans avoir à redéployer votre application. Par exemple, ajoutez une étiquette `Setting` à votre fichier de définition de service (**.csdef**), comme indiqué dans l’exemple suivant.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Vous devez ensuite spécifier des valeurs dans le fichier de configuration de service (.cscfg).

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Utilisez le nom et les valeurs de clé de signature d’accès partagé (SAP) récupérés sur le portail, comme décrit précédemment.

### Configurer votre chaîne de connexion dans le cadre de l’utilisation de sites web Azure ou Machines virtuelles Azure

Quand vous utilisez sites web ou Machines virtuelles, il est recommandé d’utiliser le système de configuration .NET (par exemple, Web.config). Stockez la chaîne de connexion en utilisant l’élément `<appSettings>`.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Utilisez le nom et les valeurs de clé SAP que vous avez récupérées sur le [portail Azure][], comme décrit précédemment.

## Création d’une rubrique

Vous pouvez effectuer des opérations de gestion sur les rubriques et les abonnements Service Bus à l’aide de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Cette classe fournit des méthodes pour créer, énumérer et supprimer des rubriques.

Dans l’exemple suivant, un objet `NamespaceManager` est construit à l’aide de la classe Azure `CloudConfigurationManager` avec une chaîne de connexion constituée de l’adresse de base d’un espace de noms Service Bus et des informations d’identification SAP appropriées assorties d’autorisations pour les gérer. Cette chaîne de connexion se présente comme suit :

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilisez l’exemple suivant en reprenant les paramètres de configuration de la section précédente.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Les surcharges de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) vous permettent de paramétrer les propriétés de la rubrique, par exemple, pour faire en sorte que la valeur par défaut de durée de vie « time-to-live » (TTL) soit appliquée aux messages envoyés à la rubrique. Ces paramètres sont appliqués à l’aide de la classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). L’exemple suivant montre comment créer une rubrique **TestTopic** d’une taille maximale de 5 Go et un message TTL par défaut de 1 minute.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] Vous pouvez utiliser la méthode [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) sur les objets [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour vérifier s’il existe déjà une rubrique portant un certain nom dans un espace de noms.

## Création d’un abonnement

Vous pouvez également créer des abonnements de rubrique à l’aide de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

### Création d’un abonnement avec le filtre par défaut (MatchAll)

Si aucun filtre n’est spécifié lors de la création d’un abonnement, le filtre **MatchAll** est utilisé par défaut. Lorsque vous utilisez le filtre **MatchAll**, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. Dans l'exemple suivant, l'abonnement « AllMessages » qui est créé utilise le filtre par défaut **MatchAll**.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### Création d’abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Le type de filtre le plus flexible pris en charge par les abonnements est la classe [SqlFilter][], qui implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d’informations sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression][].

Dans l’exemple suivant, l’abonnement **HighMessages** est créé avec un objet [SqlFilter][] qui sélectionne uniquement les messages dont la propriété personnalisée **MessageNumber** présente une valeur supérieure à 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

De même, l’exemple suivant crée l’abonnement **LowMessages** avec un filtre [SqlFilter][] qui sélectionne uniquement les messages dont la propriété **MessageNumber** présente une valeur inférieure ou égale à 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

À présent, dès lors qu'un message est envoyé à `TestTopic`, il est toujours remis aux destinataires abonnés à l'abonnement de rubrique **AllMessages** et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique **HighMessages** et **LowMessages** (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application crée un objet [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) à l’aide de la chaîne de connexion.

Le code qui suit montre comment créer un objet [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) pour la rubrique **TestTopic** créée précédemment à l’aide de l’appel d’API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Les messages envoyés aux rubriques Service Bus sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Les objets [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) possèdent un ensemble de propriétés standard (telles que [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l’objet [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) ; le sérialiseur **DataContractSerializer** approprié est alors utilisé pour sérialiser l’objet. Une autre possibilité consiste à fournir un **System.IO.Stream**.

L’exemple suivant montre comment envoyer cinq messages de test à l’objet **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) obtenu dans l’exemple de code précédent. Notez que la valeur de la propriété [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de chaque message varie en fonction de l’itération de la boucle (ce qui détermine les abonnements qui le reçoivent).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go. Si le partitionnement est activé, la limite supérieure est plus élevée. Pour plus d’informations, voir [Entités de messagerie partitionnées](service-bus-partitioning.md).

## Réception des messages d'un abonnement

Le moyen recommandé pour recevoir les messages d’un abonnement consiste à utiliser un objet [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Les objets [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) peuvent fonctionner dans deux modes différents : [*ReceiveAndDelete* et *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : lorsque Service Bus reçoit une demande de lecture pour un message figurant dans un abonnement, il marque le message comme étant consommé et le renvoie à l’application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Étant donné que Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

En mode **PeekLock** (mode par défaut), le processus de réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) pour le message reçu. Lorsque Service Bus obtient l’appel **Complete**, il marque le message comme consommé et le supprime de l’abonnement.

L’exemple suivant montre comment les messages peuvent être reçus et traités à l’aide du mode par défaut **PeekLock**. Pour spécifier une valeur différente pour [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), vous pouvez utiliser une autre surcharge pour [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Cet exemple utilise le rappel [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) pour traiter les messages dès leur arrivée dans l’abonnement **HighMessages**.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Cet exemple configure le rappel [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) à l’aide d’un objet [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) est défini sur **false** pour permettre le contrôle manuel du moment de l’appel de [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) est défini sur 1 minute. Le client attend donc jusqu’à une minute avant de mettre fin au renouvellement et effectue un nouvel appel pour rechercher les messages. La valeur de cette propriété permet de réduire le nombre de fois où le client effectue des appels facturables qui ne récupèrent aucun message.

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle peut appeler la méthode [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) pour le message reçu (au lieu de la méthode [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Cela amène Service Bus à déverrouiller le message dans l’abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans un abonnement est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’émission de la demande [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), le message est à nouveau remis à l’application lorsqu’elle redémarre. Cette opération est souvent appelée *Au moins une fois*. Chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message, qui reste constante pendant les tentatives de remise.

## Suppression de rubriques et d'abonnements

L’exemple suivant montre comment supprimer la rubrique **TestTopic** de l’espace de noms de service **HowToSample**.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

La suppression d’une rubrique a également pour effet de supprimer les abonnements inscrits dans la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l’abonnement **HighMessages** de la rubrique **TestTopic**.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## Étapes suivantes

Maintenant que vous connaissez les principes de base des rubriques et des abonnements Service Bus, nous vous invitons à consulter les liens suivants pour en savoir plus :

-   [Files d’attente, rubriques et abonnements][].
-   [Exemple de filtres de rubrique.][]
-   Référence d’API pour [SqlFilter][].
-   Créez une application opérationnelle, capable d’envoyer et de recevoir des messages à destination et en provenance d’une file d’attente Service Bus : [Didacticiel .NET sur la messagerie répartie Service Bus][].
-   Exemples Service Bus : téléchargement depuis la page [Exemples Azure][] ou consultation de la [vue d’ensemble](../service-bus/service-bus-samples.md).

  [portail Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Files d’attente, rubriques et abonnements]: service-bus-queues-topics-subscriptions.md
  [Exemple de filtres de rubrique.]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Didacticiel .NET sur la messagerie répartie Service Bus]: service-bus-brokered-tutorial-dotnet.md
  [Exemples Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

<!---HONumber=AcomDC_0928_2016-->