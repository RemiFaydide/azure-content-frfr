<properties
	pageTitle="Vue d’ensemble de l’empaquetage dynamique | Microsoft Azure"
	description="Cette rubrique donne une vue d'ensemble de l'empaquetage dynamique."
	authors="Juliako"
	manager="erikre"
	editor=""
	services="media-services"
	documentationCenter=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016" 
	ms.author="juliako"/>


#Empaquetage dynamique

##Vue d'ensemble

Vous pouvez utiliser Microsoft Azure Media Services pour distribuer de nombreux formats de fichiers sources multimédias, formats de diffusion en continu de contenu multimédia et formats de protection de contenu à diverses technologies clientes (par exemple, iOS, XBOX, Silverlight, Windows 8). Ces clients comprennent différents protocoles. Par exemple, iOS nécessite un format HTTP Live Streaming (HLS) V4, tandis que Silverlight et Xbox nécessitent le format Smooth Streaming. Si vous voulez transmettre un ensemble de fichiers MP4 (ISO Base Media 14496-12) ou de fichiers Smooth Streaming à vitesse de transmission adaptative, à des clients qui utilisent le format MPEG DASH, HLS ou Smooth Streaming, nous vous recommandons de tirer profit de l'empaquetage dynamique Media Services.

Avec l'empaquetage dynamique, il vous suffit de créer un élément multimédia contenant un ensemble de fichiers MP4 ou de fichiers Smooth Streaming à vitesse de transmission adaptative. Ensuite, en fonction du format spécifié dans le manifeste ou la demande de fragment, le serveur de diffusion en continu à la demande s'assure que vous recevez le flux conforme au protocole choisi. Par conséquent, il vous suffit de stocker et de payer les fichiers dans un seul format de stockage. Le service Media Services se charge de créer et de fournir la réponse appropriée en fonction des demandes des clients.

Le diagramme suivant illustre le flux traditionnel d'encodage et d'empaquetage statique.

![Encodage statique](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Le diagramme suivant illustre le flux d'empaquetage dynamique.

![Encodage dynamique](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Pour tirer parti de l'empaquetage dynamique, vous devez d'abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir duquel vous envisagez de distribuer votre contenu. Pour plus d'informations, consultez [Mise à l'échelle de Media Services](media-services-portal-manage-streaming-endpoints.md).

##Scénario courant

1. Téléchargez un fichier d'entrée (appelé fichier mezzanine). Par exemple, H.264, MP4 ou WMV (pour obtenir la liste des formats pris en charge, consultez [Formats pris en charge par Media Encoder Standard](media-services-media-encoder-standard-formats.md)).

1. Encodez votre fichier mezzanine en ensembles de fichiers MP4 à vitesse de transmission adaptative H.264.

1. Publiez l'élément multimédia qui contient l'ensemble de fichiers MP4 à vitesse de transmission adaptative en créant le localisateur à la demande.

1. Générez les URL de diffusion en continu pour accéder à votre contenu et le diffuser en continu.


##Préparation des éléments multimédias pour une diffusion en continu dynamique

Pour préparer un élément multimédia pour sa diffusion en continu dynamique, vous disposez de deux options :

1. [Chargez un fichier maître](media-services-dotnet-upload-files.md).
2. [Utilisez l’encodeur Media Encoder Standard pour produire des ensembles de fichiers MP4 à vitesse de transmission adaptative H.264](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Diffusez votre contenu](media-services-deliver-content-overview.md).

OU

1. Chargez des fichiers MP4 pré-encodés.

>[AZURE.NOTE] Cette option n’est pas recommandée.

2. [Validez vos fichiers pré-encodés](media-services-static-packaging.md#validating-adaptive-bitrate-mp4s-encoded-with-external-encoders).
3. [Diffusez votre contenu](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formats non pris en charge pour l'empaquetage dynamique

Les formats de fichiers sources suivants ne sont pas pris en charge par l'empaquetage dynamique.

- Fichiers MP4 Dolby Digital
- Fichiers Smooth Streaming Dolby Digital

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->