## Envoi de messages vers Event Hubs

Dans cette section, vous allez écrire une application console Windows pour envoyer des événements à votre hub d'événements.

1. Dans Visual Studio, créez un projet d'application de bureau Visual C# à l'aide du modèle de projet d'**application de console**. Nommez le projet **Sender**.

	![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp1.png)

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution**.

3. Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus`. Vérifiez que le nom du projet (**Expéditeur**) est spécifié dans la zone **Version(s)**. Cliquez sur **Installer** et acceptez les conditions d’utilisation.

	![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp2.png)

	Visual Studio lance le téléchargement, l’installation et ajoute une référence au [Package NuGet Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

	```
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant les valeurs par le nom du Event Hub que vous avez créé dans la section précédente et par la chaîne de connexion au niveau de l’espace de noms, enregistrée précédemment.

	```
	static string eventHubName = "{Event Hub name}";
	static string connectionString = "{send connection string}";
	```

6. Ajoutez la méthode suivante à la classe **Program** :

	```
	static void SendingRandomMessages()
	{
	    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
	    while (true)
	    {
	        try
	        {
	            var message = Guid.NewGuid().ToString();
	            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
	            eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
	        }
	        catch (Exception exception)
	        {
	            Console.ForegroundColor = ConsoleColor.Red;
	            Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
	            Console.ResetColor();
	        }

	        Thread.Sleep(200);
	    }
	}
	```

	Cette méthode envoie en continu les événements à votre hub d’événements avec un délai de 200 ms.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

	```
	Console.WriteLine("Press Ctrl-C to stop the sender process");
	Console.WriteLine("Press Enter to start now");
	Console.ReadLine();
	SendingRandomMessages();
	```

<!---HONumber=AcomDC_0921_2016-->