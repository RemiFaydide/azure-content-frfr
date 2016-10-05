<properties
	pageTitle="Utilisation du stockage d’objets blob à partir de Xamarin | Microsoft Azure"
	description="La bibliothèque cliente de stockage Azure pour Xamarin permet aux développeurs d’utiliser un code base C# partagé pour créer des applications iOS, Android et Windows Store avec leurs interfaces utilisateur natives. Ce didacticiel montre comment utiliser Xamarin pour créer une application qui utilise le stockage d’objets blob Azure."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="micurd;tamram"/>

# Utilisation du stockage d’objets blob à partir de Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d'ensemble

Xamarin permet aux développeurs d’utiliser un code base C# partagé pour créer des applications iOS, Android et Windows Store avec leurs interfaces utilisateur natives. Ce didacticiel vous montre comment utiliser le stockage d’objets blob Azure avec une application Xamarin. Si vous souhaitez en savoir plus sur le stockage Azure, avant de vous plonger dans le code, consultez [Introduction à Microsoft Azure Storage](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## Création d’une application Xamarin

Pour la prise en main, nous allons créer une application qui cible Windows, iOS et Android. Cette application créera simplement un conteneur et chargera un objet blob dans ce conteneur. Nous utiliserons Visual Studio sous Windows pour la prise en main, mais les mêmes méthodes peuvent être appliquées lors de la création d’une application à l’aide de Xamarin Studio sous Mac OS.

Procédez comme suit pour créer votre application :

1. Si vous ne l’avez pas encore fait, téléchargez et installez [Xamarin pour Visual Studio](https://www.xamarin.com/download).
2. Ouvrez Visual Studio et créer une application vide (partagée native) : **Fichier > Nouveau > Projet > Multiplateforme > Application vide (partagée native)**.
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sélectionnez **Gérer les packages NuGet pour la solution**. Recherchez **WindowsAzure.Storage** et installez la dernière version disponible sur tous les projets dans votre solution.
4. Créez et exécutez votre projet.

Vous devez maintenant avoir une application qui vous permet de cliquer sur un bouton et d’incrémenter un compteur.

> [AZURE.NOTE] La bibliothèque cliente de stockage Azure pour Xamarin fonctionne également pour les applications Xamarin.Forms.

## Créer un conteneur et télécharger un objet blob

Ensuite, vous allez ajouter du code à la classe partagée `MyClass.cs` qui crée un conteneur et charge un objet blob dans ce conteneur. `MyClass.cs` doit se présenter comme suit :

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using System.Threading.Tasks;

	namespace XamarinApp
	{
		public class MyClass
		{
			public MyClass ()
			{
			}

		    public static async Task createContainerAndUpload()
		    {
		        // Retrieve storage account from connection string.
		        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

		        // Create the blob client.
		        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

		        // Retrieve reference to a previously created container.
		        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

				// Create the container if it doesn't already exist.
            	await container.CreateIfNotExistsAsync();

		        // Retrieve reference to a blob named "myblob".
		        CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

		        // Create the "myblob" blob with the text "Hello, world!"
		        await blockBlob.UploadTextAsync("Hello, world!");
		    }
		}
	}

Veillez à remplacer « nom\_de\_votre\_compte\_ici » et « clé\_de\_votre\_compte\_ici » par le nom du compte et la clé de compte. Vous pouvez ensuite utiliser cette classe partagée dans votre application iOS, Android et Windows Phone. Vous pouvez simplement ajouter `MyClass.createContainerAndUpload()` à chaque projet. Par exemple :

### XamarinApp.Droid > MainActivity.cs

	using Android.App;
	using Android.Widget;
	using Android.OS;

	namespace XamarinApp.Droid
	{
		[Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
		public class MainActivity : Activity
		{
			int count = 1;

			protected override async void OnCreate (Bundle bundle)
			{
				base.OnCreate (bundle);

				// Set our view from the "main" layout resource
				SetContentView (Resource.Layout.Main);

				// Get our button from the layout resource,
				// and attach an event to it
				Button button = FindViewById<Button> (Resource.Id.myButton);

				button.Click += delegate {
					button.Text = string.Format ("{0} clicks!", count++);
				};

	      	  await MyClass.createContainerAndUpload();
			}
		}
	}

### XamarinApp.iOS > ViewController.cs

	using System;
	using UIKit;

	namespace XamarinApp.iOS
	{
		public partial class ViewController : UIViewController
		{
			int count = 1;

			public ViewController (IntPtr handle) : base (handle)
			{
			}

			public override async void ViewDidLoad ()
			{
				base.ViewDidLoad ();
				// Perform any additional setup after loading the view, typically from a nib.
				Button.AccessibilityIdentifier = "myButton";
				Button.TouchUpInside += delegate {
					var title = string.Format ("{0} clicks!", count++);
					Button.SetTitle (title, UIControlState.Normal);
				};

	            await MyClass.createContainerAndUpload();
	    	}

			public override void DidReceiveMemoryWarning ()
			{
				base.DidReceiveMemoryWarning ();
				// Release any cached data, images, etc that aren't in use.
			}
		}
	}

### XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

	using Windows.UI.Xaml.Controls;
	using Windows.UI.Xaml.Navigation;

	// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

	namespace XamarinApp.WinPhone
	{
	    /// <summary>
	    /// An empty page that can be used on its own or navigated to within a Frame.
	    /// </summary>
	    public sealed partial class MainPage : Page
	    {
	        int count = 1;

	        public MainPage()
	        {
	            this.InitializeComponent();

	            this.NavigationCacheMode = NavigationCacheMode.Required;
	        }

	        /// <summary>
	        /// Invoked when this page is about to be displayed in a Frame.
	        /// </summary>
	        /// <param name="e">Event data that describes how this page was reached.
	        /// This parameter is typically used to configure the page.</param>
	        protected override async void OnNavigatedTo(NavigationEventArgs e)
	        {
	            // TODO: Prepare page for display here.

	            // TODO: If your application contains multiple pages, ensure that you are
	            // handling the hardware Back button by registering for the
	            // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
	            // If you are using the NavigationHelper provided by some templates,
	            // this event is handled for you.
	            Button.Click += delegate {
	                var title = string.Format("{0} clicks!", count++);
	                Button.Content = title;
	            };

	            await MyClass.createContainerAndUpload();
	        }
	    }
	}


## Exécution de l'application

Vous pouvez maintenant exécuter cette application dans un émulateur Android ou Windows Phone. Vous pouvez également exécuter cette application dans un émulateur iOS, mais cette opération nécessite un ordinateur Mac. Pour des instructions spécifiques sur la façon de procéder, lisez la documentation sur la [connexion de Visual Studio à un Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Une fois que vous exécutez votre application, elle crée le conteneur `mycontainer` dans votre compte de stockage. Il doit contenir l’objet blob, `myblob`, qui présente le texte, `Hello, world!`. Vous pouvez le vérifier à l’aide de [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/).

## Étapes suivantes

Dans cette prise en main, vous avez appris à créer une application multiplateforme dans Xamarin qui utilise le stockage Azure. Cette prise en main est spécifiquement axée sur un seul scénario dans le stockage Blob. Toutefois, vous pouvez en faire beaucoup plus, non seulement le Stockage Blob, mais également avec Table, Fichier et Stockage File d’attente. Pour en savoir plus, voir les articles suivants :
- [Prise en main du stockage d’objets blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
- [Prise en main d’Azure Table Storage à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
- [Prise en main d’Azure Queue Storage à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
- [Prise en main du stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

<!---HONumber=AcomDC_0921_2016-->