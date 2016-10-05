## Associer un compte Azure Storage à IoT Hub

Étant donné que l’appareil simulé télécharge un fichier dans un objet blob Azure Storage, vous devez disposer d’un compte Azure Storage associé à IoT Hub. Vous pouvez utiliser un compte de stockage existant ou suivre les instructions figurant dans [À propos d’Azure Storage] pour en créer un. Vous pouvez associer un compte de stockage Azure à IoT Hub en suivant les instructions de la section [Configuration de hubs IoT par le biais du portail Azure].

## Téléchargement d’un fichier à partir d’un appareil simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans [Envoyer des messages du cloud vers des appareils avec IoT Hub] pour recevoir des messages du cloud vers des appareils à partir du concentrateur IoT.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **SimulatedDevice**, cliquez sur **Ajouter**, puis sur **Élément existant**. Accédez à un fichier image et ajoutez-le à votre projet. Ce didacticiel suppose que l’image a pour nom `image.jpg`.

2. Cliquez avec le bouton droit sur l’image, puis cliquez sur **Propriétés**. Assurez-vous que l’option **Copier dans le répertoire de sortie** est définie sur **Toujours copier**.

    ![][1]

3. Au début du fichier **Program.cs**, ajoutez les instructions suivantes :

        using System.IO;

4. Ajoutez la méthode suivante à la classe **Program** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    La méthode `UploadToBlobAsync` utilise le nom de fichier et la source de flux du fichier à télécharger et gère le téléchargement vers le stockage. L’application console affiche le temps nécessaire pour télécharger le fichier.

5. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

        SendToBlobAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] \(Gestion des erreurs temporaires).

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[À propos d’Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Configuration de hubs IoT par le biais du portail Azure]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->