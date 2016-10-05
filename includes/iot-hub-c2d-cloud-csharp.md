## Envoi d’un message cloud-à-appareil à partir du serveur principal de l’application

Dans cette section, vous allez écrire une application console Windows qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé.

1. Dans la solution Visual Studio actuelle, créez un projet d’application de bureau Visual C# à l’aide du modèle de projet **d’application console**. Nommez le projet **SendCloudToDevice**.

   	![Nouveau projet dans Visual Studio][20]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**.

	La fenêtre **Gérer les packages NuGet** s’ouvre.

3. Recherchez `Microsoft Azure Devices`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

	Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure IoT - Service SDK NuGet].

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices;

5. Ajoutez les champs suivants à la classe **Program** : Remplacez la valeur d’espace réservé par la chaîne de connexion du concentrateur IoT de la section [Prise en main de IoT Hub] :

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Cette méthode envoie un nouveau message cloud-à-appareil avec l’ID `myFirstDevice`. Modifiez ce paramètre en conséquence, au cas où vous avez modifié celui utilisé dans [Prise en main de IoT Hub].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage...**. Sélectionnez **Plusieurs projets de démarrage**, puis l’action **Démarrer** pour les applications **ProcessDeviceToCloudMessages**, **SimulatedDevice** et **SendCloudToDevice**.

9.  Appuyez sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application simulée.

    ![Application recevant le message][21]

## Réception des commentaires de remise
Il est possible de demander des accusés de réception (ou d’expiration) à IoT Hub pour chaque message cloud-à-appareil. Cela permet au serveur principal cloud d’instruire facilement une nouvelle tentative ou une logique de compensation. Reportez-vous au [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D] pour plus d’informations sur les commentaires de messages cloud-à-l’appareil.

Dans cette section, vous allez modifier l’application **SendCloudToDevice** de manière à exiger des commentaires et à en recevoir d’IoT Hub.

1. Dans Visual Studio, dans le projet **SendCloudToDevice**, ajoutez la méthode suivante à la classe **Program**.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

2. Ajoutez la méthode suivante dans la méthode **Main** juste après la ligne `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` :

        ReceiveFeedbackAsync();

3. Pour obtenir des commentaires sur la remise de votre message cloud-à-appareil, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync**. Ajoutez la ligne suivante, immédiatement après la ligne `var commandMessage = new Message(...);` :

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Exécutez les applications en appuyant sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application simulée et, après quelques secondes, le message de commentaires reçu par votre application **SendCloudToDevice**.

    ![Application recevant le message][22]

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] \(Gestion des erreurs temporaires).

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[package Azure IoT - Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Prise en main de IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=AcomDC_0608_2016-->