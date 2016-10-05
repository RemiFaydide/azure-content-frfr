<properties 
	pageTitle="Encoder un élément multimédia avec Media Encoder Standard à l’aide de .NET | Microsoft Azure" 
	description="Cette rubrique montre comment utiliser .NET pour encoder vos éléments multimédias avec Media Encoder Standard." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="09/19/2016"
	ms.author="juliako;anilmur"/>


# Encoder un élément multimédia avec Media Encoder Standard à l’aide de .NET

Les tâches d’encodage sont une des opérations de traitement les plus courantes dans Media Services. Vous créez des tâches d’encodage pour convertir des fichiers multimédias d’un encodage à un autre. Lorsque vous les encodez, vous pouvez utiliser l’encodeur multimédia intégré de Media Services. Vous pouvez aussi utiliser un encodeur fourni par un partenaire Media Services. Ces encodeurs tiers sont disponibles sur Azure Marketplace.

Cette rubrique montre comment utiliser .NET pour encoder vos éléments multimédias avec Media Encoder Standard (MES). Media Encoder Standard se configure à l’aide d’une des présélections d’encodeur décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Nous vous recommandons de toujours encoder vos fichiers mezzanine sous forme de jeu de fichiers MP4 à débit adaptatif, puis de convertir ce jeu au format souhaité au moyen de l’[empaquetage dynamique](media-services-dynamic-packaging-overview.md). Pour tirer parti de l'empaquetage dynamique, vous devez d'abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, voir [Mise à l’échelle de Media Services](media-services-portal-manage-streaming-endpoints.md).

Si votre ressource de sortie est stockée sous forme chiffrée, vous devez configurer une stratégie de remise de ressources. Pour plus d'informations, consultez [Configuration de la stratégie de remise de ressources](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MES génère un fichier de sortie avec un nom qui contient les 32 premiers caractères du nom du fichier d'entrée. Le nom est basé sur ce qui est spécifié dans le fichier prédéfini. Par exemple, « NomFichier » : « {NomBase}\_{Index}{Extension} ». {NomBase} est remplacé par les 32 premiers caractères du nom du fichier d'entrée.

###Formats MES

[Formats et codecs](media-services-media-encoder-standard-formats.md)

###Présélections MES

Media Encoder Standard est configuré avec l’une des présélections d’encodeur décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Métadonnées d’entrée et de sortie

Quand vous encodez un ou plusieurs éléments multimédias d’entrée à l’aide de MES, vous obtenez un élément multimédia de sortie une fois cette tâche d’encodage terminée. L’élément multimédia de sortie contient la vidéo, l’audio, les miniatures, le manifeste, et ainsi de suite, en fonction des paramètres d’encodage prédéfinis.

Il contient également un fichier avec des métadonnées relatives à l’élément multimédia d’entrée. Le nom du fichier XML de métadonnées a le format suivant : <asset\_id>\_metadata.xml (par exemple, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4\_metadata.xml), où <asset\_id> est la valeur AssetId de l’élément multimédia d’entrée. Le schéma de ce XML de métadonnées d’entrée est décrit [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

L’élément multimédia de sortie contient également un fichier avec des métadonnées relatives à l’élément multimédia de sortie. Le nom du fichier XML de métadonnées a le format suivant : <source_file_name>\_manifest.xml (par exemple, BigBuckBunny\_manifest.xml). Le schéma de ce XML de métadonnées de sortie est décrit [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Si vous souhaitez examiner l’un des deux fichiers de métadonnées, vous pouvez créer un localisateur SAS et télécharger le fichier sur votre ordinateur local. Vous trouverez un exemple illustrant comment créer un localisateur SAS et télécharger un fichier dans la rubrique Utilisation des extensions du SDK Media Services .NET.

##Charger l’exemple

Obtenir et exécuter un exemple [ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Exemple

Le code suivant utilise le Kit de développement logiciel (SDK) .NET de Media Services pour effectuer les tâches suivantes :

- Création d’une tâche d’encodage.
- Obtention d’une référence à l’encodeur Media Encoder Standard.
- Indiquez l'utilisation des paramètres prédéfinis « 720p débits binaires multiples H264 ». Vous pouvez afficher tous les paramètres prédéfinis [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Vous pouvez également consulter le schéma auquel ces paramètres de présélection doivent se conformer dans [cette rubrique](https://msdn.microsoft.com/library/mt269962.aspx).
- Ajout d’une tâche d’encodage unique.
- Spécification de l’élément multimédia d’entrée à encoder.
- Création d’un élément multimédia de sortie qui contiendra l’élément multimédia encodé.
- Ajout d’un gestionnaire d’événements pour vérifier la progression de la tâche.
- Envoyez le travail.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		

		    // Create a task with the encoding details, using a string preset.
		    // In this case "H264 Multiple Bitrate 720p" preset is used.
		    ITask task = job.Tasks.AddNew("My encoding task",
		        processor,
		        "H264 Multiple Bitrate 720p",
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
		    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		    job.Submit();
		    job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		    return job.OutputMediaAssets[0];
		}
		
		private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		{
		    Console.WriteLine("Job state changed event:");
		    Console.WriteLine("  Previous state: " + e.PreviousState);
		    Console.WriteLine("  Current state: " + e.CurrentState);
		    switch (e.CurrentState)
		    {
		        case JobState.Finished:
		            Console.WriteLine();
		            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
		            break;
		        case JobState.Canceling:
		        case JobState.Queued:
		        case JobState.Scheduled:
		        case JobState.Processing:
		            Console.WriteLine("Please wait...\n");
		            break;
		        case JobState.Canceled:
		        case JobState.Error:
		
		            // Cast sender as a job.
		            IJob job = (IJob)sender;
		
		            // Display or log error details as needed.
		            break;
		        default:
		            break;
		    }
		}
		
		
		private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		{
		    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		    if (processor == null)
		        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		    return processor;
		}


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Voir aussi 

[Comment générer une miniature à l’aide de Media Encoder Standard avec .NET](media-services-dotnet-generate-thumbnail-with-mes.md) [Vue d’ensemble du codage Media Services](media-services-encode-asset.md)

<!---HONumber=AcomDC_0921_2016-->