<properties
	pageTitle="Fournir du contenu aux clients | Microsoft Azure"
	description="Cette rubrique donne une vue d’ensemble de ce qu’implique la distribution de votre contenu avec Azure Media Services."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="juliako"/>


# Fournir du contenu aux clients
Quand vous distribuez votre contenu de diffusion en continu ou de vidéo à la demande aux clients, votre objectif est de fournir une vidéo de haute qualité à divers appareils dans différentes conditions de réseau.

Pour atteindre cet objectif, vous pouvez :

- Encoder votre flux dans un flux vidéo multidébit (débit binaire adaptatif). La qualité et les conditions du réseau sont ainsi prises en charge.
- Utiliser [l’empaquetage dynamique](media-services-dynamic-packaging-overview.md) de Microsoft Azure Media Services pour empaqueter de nouveau votre flux dans différents protocoles dynamiquement. La diffusion en continu sur différents appareils est ainsi prise en charge. Media Services prend en charge la distribution des technologies de streaming à débit adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG-DASH et HDS (pour licences Adobe Primetime/Access uniquement).

Cet article offre une vue d’ensemble sur des concepts importants de la distribution de contenu.

Pour vérifier les problèmes connus, consultez [Problèmes connus](media-services-deliver-content-overview.md#known-issues).

## Empaquetage dynamique

Avec l’empaquetage dynamique fourni par Media Services, vous pouvez distribuer votre contenu encodé en MP4 à débit binaire adaptatif ou Smooth Streaming dans un format de diffusion continue pris en charge par Media Services (MPEG-DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats. Nous vous recommandons de distribuer votre contenu avec l’empaquetage dynamique.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

- encoder votre fichier mezzanine (source) en un ensemble de fichiers MP4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif ;
- obtenir au moins une unité de streaming à la demande pour le point de terminaison de streaming à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-portal-manage-streaming-endpoints.md).

Avec l’empaquetage dynamique, vous stockez et payez les fichiers dans un format de stockage unique. Media Services crée et fournit la réponse appropriée à vos demandes.

En plus de donner accès aux fonctionnalités d’empaquetage dynamique, les unités réservées de streaming à la demande vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbit/s. Par défaut, le streaming à la demande est configuré dans un modèle d’instance partagée, pour lequel les ressources du serveur (par exemple, calcul ou capacité de sortie) sont partagées avec tous les autres utilisateurs. Vous pouvez améliorer la vitesse de streaming à la demande en achetant des unités réservées de streaming à la demande.

Pour plus d’informations, consultez [Empaquetage dynamique](media-services-dynamic-packaging-overview.md).

## Filtres et manifestes dynamiques

Vous pouvez définir des filtres pour vos éléments multimédias avec Media Services. Ces filtres sont des règles côté serveur qui permettent à vos clients d’effectuer des opérations comme les suivantes : lecture d’une section donnée d’une vidéo, spécification d’un sous-ensemble de rendus audio et vidéo pouvant être gérés par l’appareil de votre client (au lieu de tous les rendus associés à l’élément multimédia). Ce filtrage est obtenu au moyen de *manifestes dynamiques* créés quand votre client émet une demande pour diffuser une vidéo selon un ou plusieurs filtres spécifiés.

Pour plus d’informations, consultez [Filtres et manifestes dynamiques](media-services-dynamic-manifest-overview.md).

## Localisateurs

Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez d’abord publier votre élément multimédia en créant un localisateur. Un localisateur fournit un point d’entrée pour accéder aux fichiers contenus dans une ressource. Media Services prend en charge deux types de localisateurs :

- Localisateurs OnDemandOrigin. Ils sont utilisés pour diffuser du contenu multimédia (par exemple, MPEG-DASH, HLS ou Smooth Streaming) ou télécharger progressivement des fichiers.
-  Localisateurs d’URL de signature d’accès partagé (SAP). Ils sont utilisés pour télécharger des fichiers multimédias sur un ordinateur local.

Une *stratégie d’accès* est utilisée pour définir les autorisations (écriture, lecture, énumération, etc.) et la durée pendant laquelle le client a accès à une ressource donnée. Notez que l’autorisation de liste (AccessPermissions.List) ne doit pas être utilisée lors de la création d’un localisateur OnDemandOrigin.

Les localisateurs ont une date d’expiration. Le portail Azure définit une date d’expiration 100 ans plus tard pour les localisateurs.

>[AZURE.NOTE] Si vous avez utilisé le portail Azure pour créer des localisateurs avant mars 2015, ces localisateurs ont été configurés pour expirer après deux ans.

Pour mettre à jour la date d’expiration d’un localisateur, utilisez les API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Notez que lorsque vous mettez à jour la date d’expiration d’un localisateur SAS, l’URL est modifiée.

Les localisateurs ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Vous pouvez accorder différents droits d’accès aux utilisateurs en utilisant les solutions de gestion des droits numériques (DRM). Pour plus d’informations, consultez la page [Sécurisation des médias](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Lorsque vous créez un localisateur, il peut y avoir un délai de 30 secondes dû au processus de stockage et de propagation requis dans Azure Storage.


## Diffusion en continu adaptative

Les technologies à débit adaptatif permettent aux applications de lecteur vidéo de déterminer les conditions réseau et de choisir entre plusieurs débits. Si la communication réseau se dégrade, le client peut sélectionner un débit inférieur, ce qui permet au lecteur de continuer avec une qualité vidéo moindre. Dès que les conditions réseau s’améliorent, le client peut passer à un débit binaire supérieur pour une meilleure qualité vidéo. Azure Media Services prend en charge les technologies à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG-DASH et HDS.

Pour fournir aux utilisateurs des URL de diffusion en continu, vous devez d’abord créer un localisateur OnDemandOrigin. La création du localisateur vous donne le chemin d’accès de base à l’élément multimédia qui contient le contenu que vous souhaitez diffuser. Toutefois, pour pouvoir diffuser ce contenu vous devez modifier ce chemin d’accès. Pour construire une URL complète vers le fichier manifeste de diffusion en continu, vous devez concaténer la valeur de chemin d’accès du localisateur et le nom du fichier manifeste (nom\_fichier.ism). Ensuite, ajoutez **/Manifest** et un format approprié (si nécessaire) au chemin d’accès du localisateur.

>[AZURE.NOTE]Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion commence par HTTPS.

Vous ne pouvez transmettre en continu avec le protocole SSL que si le point de terminaison de streaming à partir duquel vous distribuez votre contenu a été créé après le 10 septembre 2014. Si vos URL de diffusion sont basées sur des points de terminaison créés après le 10 septembre 2014, l’URL contient « streaming.mediaservices.windows.net ». Les URL de diffusion qui contiennent « origin.mediaservices.windows.net » (ancien format) ne sont pas compatibles avec le protocole SSL. Si votre URL suit l’ancien format et que vous souhaitez être en mesure de diffuser via le protocole SSL, créez un point de terminaison. Utilisez des URL basées sur le nouveau point de terminaison de streaming pour diffuser votre contenu avec le protocole SSL.


## Formats d’URL de diffusion en continu :

### Format MPEG-DASH

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



### Format Apple HTTP Live Streaming (HLS) V4

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### Format Apple HTTP Live Streaming (HLS) V3

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### Format Apple HTTP Live Streaming (HLS) avec filtre audio uniquement

Par défaut, les pistes uniquement audio sont incluses dans le manifeste HLS. Cette condition est nécessaire pour qu’Apple Store certifie les réseaux cellulaires. Dans ce cas, si un client n’a pas suffisamment de bande passante ou est connecté au moyen d’une connexion 2G, la lecture bascule vers l’audio uniquement. Cela permet de maintenir la diffusion en continu du contenu sans mise en mémoire tampon, mais sans la vidéo. Dans certains scénarios, la mise en mémoire tampon du lecteur est préférable à une diffusion audio uniquement. Si vous souhaitez supprimer la piste audio uniquement, ajoutez **audio-only=false** à l’URL.

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Pour plus d’informations, consultez [Prise en charge la composition du manifeste dynamique et fonctionnalités supplémentaires de sortie HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### Format Smooth Streaming

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest

Exemple :

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Manifeste Smooth Streaming 2.0 (manifeste hérité)

Par défaut, le manifeste Smooth Streaming contient la balise de répétition (r-tag). Toutefois, certains lecteurs ne gèrent pas la balise r-tag. Les clients disposant de ces lecteurs peuvent utiliser un format qui désactive la balise r-tag :

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=fmp4-v20)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### HDS (pour les titulaires de licences Adobe PrimeTime/Access uniquement)

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## Téléchargement progressif

Avec le téléchargement progressif, vous pouvez commencer la lecture multimédia avant que l’intégralité du fichier ait été téléchargée. Vous ne pouvez pas télécharger progressivement des fichiers .ism* (.ismv, .isma, .ismt ou .ismc).

Pour télécharger progressivement du contenu, utilisez le type de localisateur OnDemandOrigin. L’exemple suivant illustre l’URL basée sur le type de localisateur OnDemandOrigin :

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Vous devez déchiffrer les ressources à chiffrement de stockage que vous souhaitez diffuser en continu à partir du service d’origine pour permettre le téléchargement progressif.

## Télécharger

Pour télécharger votre contenu sur un appareil client, vous devez créer un localisateur SAP. Le localisateur SAP vous donne accès au conteneur Azure Storage où votre fichier est stocké. Pour créer l’URL de téléchargement, vous devez inclure le nom du fichier entre l’hôte et la signature SAP.

L’exemple suivant illustre l’URL basée sur le localisateur SAP :

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Les considérations suivantes s'appliquent :

- Vous devez déchiffrer les ressources à chiffrement de stockage que vous souhaitez diffuser en continu à partir du service d’origine pour permettre le téléchargement progressif.
- Si le téléchargement n’est pas terminé au bout de 12 heures, il échoue.

## Points de terminaison de streaming

Un point de terminaison de streaming représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN) en vue de sa redistribution. Le flux sortant d’un service de point de terminaison de streaming peut être un flux dynamique ou une ressource de vidéo à la demande dans votre compte Media Services. Vous pouvez également contrôler la capacité du service de point de terminaison de streaming afin de gérer les besoins croissants en matière de bande passante en ajustant les unités réservées de diffusion en continu. Vous devez allouer au moins une unité réservée pour les applications au sein d’un environnement de production. Pour plus d’informations, consultez [Mise à l’échelle d’un service de média](media-services-portal-manage-streaming-endpoints.md).

## Problèmes connus

### Modifications apportées à la version du manifeste Smooth Streaming

Avant la version de service de juillet 2016, lorsque des éléments multimédias générés par Media Encoder Standard, Media Encoder Premium Workflow ou, précédemment, Azure Media Encoder étaient diffusés en continu à l’aide de l’empaquetage dynamique, le manifeste Smooth Streaming retourné devait être conforme à la version 2.0. Dans la version 2.0, les durées de fragments n’utilisent pas ce que l’on appelle les balises de répétition (« r »). Par exemple :

<?xml version="1.0" encoding="UTF-8"?> <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000"> <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000"> <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" /> <c t="0" d="2000" n="0" /> <c d="2000" /> <c d="2000" /> <c d="2000" /> </StreamIndex> </SmoothStreamingMedia>

Dans la version de service de juillet 2016, le manifeste Smooth Streaming généré est conforme à la version 2.2, avec des durées de fragments utilisant des balises de répétition. Par exemple :

	<?xml version="1.0" encoding="UTF-8"?>
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
		<StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
			<QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
			<c t="0" d="2000" r="4" />
		</StreamIndex>
	</SmoothStreamingMedia>

Certains clients Smooth Streaming hérités peuvent ne pas prendre en charge les balises de répétition et ne parviendront pas à charger le manifeste. Pour résoudre ce problème, vous pouvez utiliser le paramètre de format de manifeste hérité **(format=fmp4-v20)** ou mettre à jour votre client vers la dernière version qui prend en charge les balises de répétition. Pour plus d’informations, consultez [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## Rubriques connexes

[Mettre à jour les localisateurs de Media Services après le déploiement des clés de stockage](media-services-roll-storage-access-keys.md)

<!---HONumber=AcomDC_0921_2016-->