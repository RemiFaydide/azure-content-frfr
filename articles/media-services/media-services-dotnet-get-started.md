<properties
	pageTitle="Prendre en main la diffusion de contenus à la demande à l’aide de .NET | Azure"
	description="Ce didacticiel vous présente les étapes d’implémentation d’une application de diffusion de contenu à la demande avec Azure Media Services pour .NET."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
 	ms.date="08/17/2016"
	ms.author="juliako"/>


# Prendre en main la diffusion de contenus à la demande à l’aide du Kit de développement logiciel (SDK) .NET


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
 
##Vue d'ensemble 

Ce didacticiel vous présente les étapes d'implémentation d'une application de diffusion de contenu vidéo à la demande (VoD) avec le Kit de développement logiciel (SDK) Azure Media Services (AMS) pour .NET.


Il présente le workflow Media Services de base et les objets et tâches de programmation les plus courants requis pour le développement Media Services. À la fin de ce didacticiel, vous pourrez lire en continu ou télécharger de façon progressive un exemple de fichier multimédia que vous aurez chargé, encodé et téléchargé.

## Ce que vous allez apprendre

Ce didacticiel montre comment effectuer les tâches suivantes :

1.  Créer un compte Media Services (avec le portail Azure Classic).
2.  Configurer un point de terminaison de diffusion en continu (à l'aide du portail).
3.  Créer et configurer un projet Visual Studio
5.  Se connecter au compte Media Services.
6.  Créer un nouvel élément et charger un fichier vidéo.
7.  Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif.
8.  Publier les éléments et obtenir les URL de diffusion et de téléchargement progressif
9.  Testez en lisant votre contenu.

## Conditions préalables

Les éléments suivants sont requis pour suivre le didacticiel.

- Pour suivre ce didacticiel, vous avez besoin d'un compte Azure.
	
	Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits que vous pouvez utiliser pour essayer des services Azure payants. Une fois que les crédits sont épuisés, vous pouvez quand même conserver le compte et utiliser les services et fonctionnalités Azure gratuits, comme la fonction Web Apps dans Azure App Service.
- Systèmes d’exploitation : Windows 8 ou ultérieur, Windows 2008 R2, Windows 7.
- .NET Framework 4.0 ultérieur
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou une version ultérieure.


##Charger l’exemple

Obtenir et exécuter un exemple [ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Création d’un compte Media Services grâce au portail

1. Dans le portail Azure Classic, cliquez sur **Nouveau**, **Service de média**, puis sur **Création rapide**.

	![Media Services - Création rapide](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. Dans **NAME**, entrez le nom du nouveau compte. Un nom de compte Media Services se compose de chiffres ou de lettres en minuscules, sans espaces. Sa longueur est comprise entre 3 et 24 caractères.

3. Dans **RÉGION**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements de métadonnées pour votre compte Media Services. Seules les régions Media Services disponibles s'affichent dans la liste déroulante.

4. Dans **COMPTE DE STOCKAGE**, sélectionnez un compte de stockage pour fournir un stockage d'objets blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région.

5. Si vous avez créé un compte de stockage, entrez un nom dans **NOUVEAU NOM DU COMPTE DE STOCKAGE**. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.

6. Cliquez sur **Création rapide** en bas du formulaire.

Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.

Une fois que votre compte est créé, l’état devient **Actif**.

Au bas de la page, le bouton **GÉRER LES CLÉS** s’affiche. Lorsque vous cliquez sur ce bouton, une boîte de dialogue avec le nom du compte Services de médias et les clés primaire et secondaire s'affiche. Vous devez disposer du nom de compte et des informations de clé primaire pour accéder par programme au compte Media Services.

![Media Services Page](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

Quand vous double-cliquez sur le nom de compte, la page **Démarrage rapide** s’affiche par défaut. Elle vous permet d'effectuer des tâches de gestion également disponibles sur d'autres pages du portail. Par exemple, vous pouvez télécharger un fichier vidéo depuis cette page ou depuis la page CONTENU.

##Configurer un point de terminaison de diffusion en continu à l'aide du portail

Lorsque vous utilisez Azure Media Services, l’un des scénarios les plus courants est la diffusion de contenu en continu à débit binaire adaptatif à vos clients. Avec la diffusion à débit binaire adaptatif, le client peut basculer vers un flux à débit binaire supérieur ou inférieur, car la vidéo est affichée en fonction de la bande passante réseau actuelle, de l’utilisation de l’UC et d’autres facteurs. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

- Coder ou transcoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
- Obtenir au moins une unité de diffusion en continu pour le **point de terminaison de diffusion en continu** à partir duquel vous prévoyez de distribuer votre contenu.

Avec l'empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

Pour changer le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Dans le [portail](https://manage.windowsazure.com/), cliquez sur **Services de média**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page POINTS DE TERMINAISON DE DIFFUSION EN CONTINU. Cliquez ensuite sur le point de terminaison de diffusion en continu que vous souhaitez modifier.

3. Pour spécifier le nombre d’unités de diffusion en continu, cliquez sur l’onglet METTRE À L’ÉCHELLE, puis déplacez le curseur de **capacité réservée**.

	![Scale page](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. Sélectionnez **ENREGISTRER** pour enregistrer vos modifications.

L’allocation de nouvelles unités prend environ 20 minutes.

>[AZURE.NOTE] Actuellement, le fait de passer d'une valeur positive à zéro pour le nombre d'unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.
>
> C’est le plus grand nombre d’unités spécifiées sur 24 heures qui est utilisé pour calculer le coût. Pour des informations détaillées sur la tarification, consultez [Détails de la tarification des services de média](http://go.microsoft.com/fwlink/?LinkId=275107).



##Créer et configurer un projet Visual Studio

1. Créez une nouvelle application console C# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, l'**emplacement** et le **nom de solution**, puis cliquez sur **OK**.

2. Utilisez le package Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) pour installer les **extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET**. Les extensions du Kit de développement logiciel (SDK) Media Services pour .NET sont un ensemble de méthodes d'extension et de fonctions d'assistance qui simplifient votre code et le développement avec les Services de média. L'installation de ce package installe également le **Kit de développement logiciel (SDK) Media Services pour .NET** et ajoute toutes les autres dépendances requises.

3. Ajoutez une référence à l’assembly System.Configuration. Cet assembly contient la classe **System.Configuration.ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration, par exemple App.config.

4. Ouvrez le fichier App.config (ajoutez le fichier à votre projet s'il n'a pas été ajouté par défaut) et ajoutez une section *appSettings* au fichier. Définissez les valeurs pour le nom et la clé de votre compte Azure Media Services, comme illustré dans l’exemple suivant. Pour obtenir le nom du compte et les informations sur la clé, ouvrez le portail Azure Classic, sélectionnez votre compte Media Services, puis cliquez sur le bouton **GÉRER LES CLÉS**.

		<configuration>
		...
		  <appSettings>
		    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
		    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
		  </appSettings>
		  
		</configuration>

5. Remplacez les instructions **using** existantes au début du fichier Program.cs par le code suivant.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		

6. Créez un dossier sous le répertoire de projets et copiez-y le fichier .mp4 ou .wmv à encoder et à diffuser en continu ou télécharger. Dans cet exemple, le chemin d'accès « C:\\VideoFiles » est utilisé.

##Se connecter au compte Media Services

Lorsque vous utilisez Media Services avec .NET, vous devez utiliser la classe **CloudMediaContext** pour la plupart des tâches de programmation Media Services : connexion au compte Media Services, création, mise à jour, accès et suppression des objets suivants : éléments multimédia, fichiers multimédias, travaux, stratégies d'accès, localisateurs, etc.

Remplacez la classe Program par défaut par le code ci-dessous. Le code montre comment lire les valeurs de connexion à partir du fichier App.config et comment créer l’objet **CloudMediaContext** pour se connecter à Media Services. Pour plus d’informations sur la connexion à Media Services, consultez la page [Connexion à Media Services avec le Kit de développement logiciel (SDK) Media Services pour .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La fonction **Main** appelle des méthodes qui seront définies ultérieurement dans cette section.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##Créer un nouvel élément et charger un fichier vidéo

Dans Media Services, vous téléchargez (ou réceptionnez) vos fichiers numériques dans un élément multimédia. L’entité **Asset** peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des fichiers de sous-titres codés (et les métadonnées concernant ces fichiers). Une fois les fichiers téléchargés, votre contenu est stocké en toute sécurité dans le cloud et peut faire l’objet d’un traitement et d’une diffusion en continu. Les fichiers de l'élément multimédia sont appelés **fichiers d'élément multimédia**.

La méthode **UploadFile** définie ci-dessous appelle **CreateFromFile** (défini dans les extensions du Kit de développement logiciel (SDK) .NET). **CreateFromFile** crée un nouvel élément multimédia dans lequel le fichier source spécifié est téléchargé.

La méthode **CreateFromFile** prend **AssetCreationOptions**, qui vous permet de spécifier les options de création suivantes :

- **None** : aucun chiffrement. Il s’agit de la valeur par défaut. À noter que quand vous utilisez cette option, votre contenu n'est pas protégé pendant le transit ou le repos dans le stockage. Si vous prévoyez de fournir un MP4 sous forme de téléchargement progressif, utilisez cette option.
- **StorageEncrypted** : utilisez cette option pour chiffrer votre contenu localement à l’aide du chiffrement Advanced Encryption Standard (AES) 256 bits, qui est ensuite chargé sur Azure Storage, où il est stocké au repos sous forme chiffrée. Les éléments multimédias protégés par le chiffrement de stockage sont automatiquement déchiffrés et placés dans un système de fichiers chiffré avant d’être encodés, puis éventuellement rechiffrés avant d’être rechargés sous la forme d’un nouvel élément multimédia de sortie. Le principal cas d'utilisation du chiffrement de stockage concerne la sécurisation de fichiers multimédias d'entrée de haute qualité avec un chiffrement renforcé au repos sur le disque.
- **CommonEncryptionProtected** : utilisez cette option quand vous chargez du contenu qui a déjà été chiffré et protégé avec la DRM Common Encryption ou PlayReady (par exemple Smooth Streaming protégé avec la DRM PlayReady).
- **EnvelopeEncryptionProtected** : utilisez cette option lorsque vous téléchargez un contenu au format TLS chiffré avec AES. Notez que les fichiers doivent avoir été encodés et chiffrés par le gestionnaire de transformation Transform Manager.

La méthode **CreateFromFile** vous permet également de spécifier un rappel pour indiquer la progression du chargement du fichier.

Dans l’exemple suivant, nous spécifions **None** pour les options de l’élément multimédia.

Ajoutez la méthode suivante à la classe Program.

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });

	    Console.WriteLine("Asset {0} created.", inputAsset.Id);

	    return inputAsset;
	}


##Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif

Après avoir reçu des éléments multimédias dans Media Services, vous pouvez encoder un média, modifier le format de ce dernier, lui appliquer un filigrane, etc. avant de le livrer à des clients. Afin de garantir des performances et une disponibilité optimales, ces activités sont planifiées et exécutées dans de nombreuses instances de rôle en arrière-plan. Ces activités s'appellent des travaux et chaque travail se compose de tâches atomiques qui effectuent le travail à proprement parler sur le fichier de ressource.

Comme mentionné précédemment, lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Media Services peut regrouper de façon dynamique un ensemble de fichiers MP4 à débit adaptatif dans un des formats suivants :HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

- Coder ou transcoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif.
- Obtenir au moins une unité de diffusion pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu.

Le code suivant vous explique comment effectuer envoyer une tâche d'encodage. Le travail contient une tâche qui spécifie le fichier mezzanine à transcoder en un ensemble de MP4 à débit adaptatif à l’aide de **Media Encoder Standard**. Le code envoie la tâche et attend qu'elle soit terminée.

Une fois la tâche terminée, vous pourrez diffuser votre élément multimédia ou télécharger progressivement les fichiers MP4 qui ont été créés après le transcodage. Notez que vous n’avez pas besoin d’unité de diffusion en continu pour télécharger progressivement les fichiers MP4.

Ajoutez la méthode suivante à la classe Program.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
	
	    // Prepare a job with a single task to transcode the specified asset
	    // into a multi-bitrate asset.
	
	    IJob job = _context.Jobs.CreateWithSingleTask(
	        "Media Encoder Standard",
	        "H264 Multiple Bitrate 720p",
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
	    Console.WriteLine("Submitting transcoding job...");
	
	
	    // Submit the job and wait until it is completed.
	    job.Submit();
	
	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;
	
	    Console.WriteLine("Transcoding job finished.");
	
	    IAsset outputAsset = job.OutputMediaAssets[0];
	
	    return outputAsset;
	}

##Publier l'élément et obtenir les URL de diffusion et de téléchargement progressif

Pour diffuser en continu ou télécharger un élément multimédia, vous devez tout d'abord le « publier » en créant un localisateur. Les localisateurs assurent l’accès aux fichiers contenus dans l’élément multimédia. Media Services prend en charge deux types de localisateurs : les localisateurs OnDemandOrigin, utilisés pour diffuser du contenu multimédia (par exemple, MPEG DASH, HLS ou Smooth Streaming) et les localisateurs d’URL SAS (signature d’accès partagé), utilisés pour télécharger des fichiers multimédias.

Une fois que vous avez créé les localisateurs, vous pouvez générer les URL utilisées pour transmettre en continu ou télécharger les fichiers.


Les URL de diffusion en continu pour Smooth Streaming ont le format suivant :

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Les URL de diffusion en continu pour HLS ont le format suivant :

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Les URL de diffusion en continu pour MPEG DASH ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Les URL SAS permettant de télécharger les fichiers ont le format suivant :

	{blob container name}/{asset name}/{file name}/{SAS signature}

Les extensions du Kit de développement logiciel (SDK) Media Services pour .NET fournissent des méthodes d'assistance pratiques qui retournent des URL formatées pour la ressource publiée.

Le code suivant utilise les extensions du Kit de développement logiciel (SDK) Media Services pour .NET pour créer des localisateurs, obtenir la diffusion en continu et les URL de téléchargement progressif. Le code montre également comment télécharger les fichiers vers un dossier local.

Ajoutez la méthode suivante à la classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##Tester en lisant votre contenu  

Une fois que vous exécutez le programme défini dans la section précédente, les URL similaires à celles qui suivent seront affichées dans la fenêtre de la console.

URL de diffusion adaptative :

Smooth Streaming

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL de téléchargement progressif (audio et vidéo).

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Pour tester votre vidéo, utilisez le [lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pour tester le téléchargement progressif, collez l'URL dans un navigateur (par exemple, Internet Explorer, Chrome ou Safari).


##Étapes suivantes : Parcours d’apprentissage Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### Vous recherchez quelque chose d’autre ?

Si cette rubrique ne répond pas à vos attentes ou besoins, ou ne contient pas les informations recherchées, faites-nous part de vos commentaires à l’aide du fil de discussion Disqus ci-dessous.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/

<!---HONumber=AcomDC_0824_2016-->