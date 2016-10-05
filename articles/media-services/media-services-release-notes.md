<properties 
	pageTitle="Notes de publication Media Services | Microsoft Azure" 
	description="Notes de publication de Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako"/>

# Notes de publication d'Azure Media Services

Ces notes de publication récapitulent les modifications par rapport aux précédentes versions et les problèmes connus.

>[AZURE.NOTE] Nous voulons connaître vos impressions et nous consacrer à la résolution des problèmes que vous rencontrez. Pour signaler un problème ou poser des questions, publiez un billet sur le [Forum MSDN sur Azure Media Services].


##<a id="issues"></a>Problèmes actuellement connus

### <a id="general_issues"></a>Problèmes généraux concernant Media Services

Problème|Description
---|---
Plusieurs en-têtes HTTP courants ne sont pas fournis dans l'API REST.|Si vous développez des applications Media Services à l'aide de l'API REST, vous constaterez que certains champs d'en-tête HTTP courants (notamment CLIENT-REQUEST-ID, REQUEST-ID et RETURN-CLIENT-REQUEST-ID) ne sont pas pris en charge. Les en-têtes seront ajoutés dans une prochaine mise à jour.
L’encodage par pourcentage n’est pas autorisé.|Media Services utilise la valeur de la propriété IAssetFile.Name au moment de la génération des URL pour le contenu de diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Pour cette raison, l'encodage par pourcentage n'est pas autorisé. La valeur de la propriété **Name** ne peut pas comprendre un des [caractères réservés à l’encodage par pourcentage](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) suivants : !*'();:@&=+$,/?%#". En outre, il ne peut exister qu’un « . » pour l’extension de nom de fichier.
La méthode ListBlobs intégrée à la version 3.x du Kit de développement logiciel (SDK) d'Azure Storage échoue.|Media Services génère des URL SAS basées sur la version du [02/12/2012](http://msdn.microsoft.com/library/azure/dn592123.aspx). Si vous voulez utiliser le Kit de développement logiciel (SDK) d'Azure Storage pour répertorier les objets blob dans un conteneur d'objets blob, utilisez la méthode [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) intégrée à la version 2.x de ce kit. La méthode ListBlobs intégrée à la version 3.x du Kit de développement logiciel (SDK) d'Azure Storage échouera.
Le mécanisme de limitation de Media Services restreint l'utilisation des ressources pour les applications qui recourent de manière excessive au service. Le service peut renvoyer le code d'état HTTP Service indisponible (503).|Pour plus d’informations, reportez-vous à la description du code d’état HTTP 503 dans la rubrique [Codes d’erreur d’Azure Media Services](http://msdn.microsoft.com/library/azure/dn168949.aspx).
Lors de l'interrogation des entités, il existe une limite de 1 000 entités retournées simultanément car l'API REST v2 publique limite les résultats des requêtes à 1 000 résultats. | Vous devez utiliser **Skip** et **Take** (.NET)/ **top** (REST) comme le décrivent [cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [cet exemple d’API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Certains clients peuvent rencontrer un problème de répétition de balise dans le manifeste de diffusion en continu lisse.|Pour plus d’informations, consultez [cette](media-services-deliver-content-overview.md#known-issues) section.
Les objets du Kit de développement logiciel (SDK) Azure Media Services ne peuvent pas être sérialisés et, par conséquent, ne fonctionnent pas avec Azure Caching.|Si vous essayez de sérialiser l'objet AssetCollection du Kit de développement logiciel (SDK) pour l'ajouter à Azure Caching, une exception est levée.
Les travaux d’encodage échouent avec une chaîne de message « Étape : DownloadFile. Code : System.NullReferenceException ».|Le flux de travail d’encodage classique consiste à charger un ou des fichiers vidéo d’entrée vers une ressource d’entrée et à envoyer un ou plusieurs travaux d’encodage pour cette ressource d’entrée, sans la modifier davantage. Si toutefois vous la modifiez (par exemple, par ajout / suppression / renommage de fichiers dans la ressource), les travaux suivants peuvent échouer avec une erreur DownloadFile. La solution de contournement consiste à supprimer la ressource d’entrée et à recharger le ou les fichiers d’entrée vers une nouvelle ressource. 

##<a id="rest_version_history"></a>Historique des versions de l’API REST

Pour des informations sur l'historique des versions de l'API REST, consultez la page [Référence de l'API REST d'Azure Media Services].

##<a id="july_changes16"></a>Version de juillet 2016

###Mises à jour apportées au fichier de manifeste (*.ISM) généré par les tâches d’encodage

Lorsqu’une tâche d’encodage est soumise à Media Encoder Standard ou à Azure Media Encoder, elle génère un [fichier manifeste de diffusion en continu](media-services-deliver-content-overview.md) (*.ism) dans la ressource de sortie. Dans la dernière version de service, la syntaxe de ce fichier de manifeste de diffusion en continu a été mise à jour.

>[AZURE.NOTE]La syntaxe du fichier de manifeste de diffusion en continu (.ism) est réservée à un usage interne et est susceptible de changer dans les versions à venir. Veillez à ne pas modifier ou manipuler le contenu de ce fichier.

###Un nouveau fichier de manifeste client (*.ISMC) est généré dans l’élément de sortie lorsqu’une tâche d’encodage produit un ou plusieurs fichiers MP4.

À partir de la dernière version de service, une fois que la tâche de codage a généré plusieurs fichiers MP4, l’élément de sortie contient également un fichier de manifeste de diffusion en continu (*.ismc) client. Le fichier .ismc vous aide à améliorer les performances de diffusion en continu dynamique.

>[AZURE.NOTE]La syntaxe du fichier de manifeste client (.ism) est réservée à un usage interne et est susceptible de changer dans les versions à venir. Veillez à ne pas modifier ou manipuler le contenu de ce fichier.

Pour plus d’informations, consultez [ce](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### Problèmes connus

Certains clients peuvent rencontrer un problème de répétition de balise dans le manifeste de diffusion en continu lisse. Pour plus d’informations, consultez [cette](media-services-deliver-content-overview.md#known-issues) section.

##<a id="apr_changes16"></a>Version d’avril 2016

### Azure Media Analytics

Azure Media Services a intégré Azure Media Analytics pour offrir des fonctionnalités vidéo performantes. Pour obtenir des informations détaillées, consultez [Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md).

### Apple FairPlay (préversion)

Azure Media Services vous permet désormais de chiffrer de manière dynamique votre contenu HLS (HTTP Live Streaming) avec Apple FairPlay. Vous pouvez également utiliser le service de remise de licences AMS pour attribuer des licences FairPlay aux clients. Pour obtenir des informations détaillées, consultez [Utiliser Azure Media Services pour diffuser en continu votre contenu HLS protégé avec Apple FairPlay](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Version de février 2016

La dernière version du Kit de développement logiciel (SDK) Azure Media Services pour .NET (3.5.3) contient un correctif de bogue relatif à Widevine. Le problème était le suivant : AssetDeliveryPolicy ne pouvait pas être réutilisé pour plusieurs éléments multimédia chiffrés avec Widevine. Dans le cadre de ce correctif de bogue, la propriété suivante a été ajoutée au Kit de développement logiciel (SDK) : **WidevineBaseLicenseAcquisitionUrl**.
	
	Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
	    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
	{
	    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
	    
	};

##<a id="jan_changes_16"></a>Version de janvier 2016

Unités réservées d’encodage renommées pour éviter la confusion avec les noms d’encodeur.

Les unités réservées d’encodage De base, Standard et Premium sont renommées S1, S2 et S3, respectivement. Les clients qui utilisaient des unités réservées d’encodage De base, Standard ou Premium verront respectivement S1, S2 ou S3 comme étiquette dans le portail Azure (et dans la facture).

##<a id="dec_changes_15"></a>Version de décembre 2015

L’équipe du Kit de développement logiciel (SDK) Azure a publié une nouvelle version du package [ Kit de développement logiciel (SDK) Azure pour PHP](http://github.com/Azure/azure-sdk-for-php) qui contient des mises à jour et nouvelles fonctionnalités pour Microsoft Azure Media Services. En particulier, le Kit de développement logiciel (SDK) Azure Media Services pour PHP prend désormais en charge les dernières fonctionnalités de [protection du contenu](media-services-content-protection-overview.md) : chiffrement dynamique avec AES et DRM (PlayReady et Widevine), avec et sans restriction de jeton. Il prend également en charge la mise à l’échelle des [Unités d’encodage](media-services-dotnet-encoding-units.md).

Pour plus d’informations, consultez :

- Le blog [Kit de développement logiciel (SDK) Microsoft Azure Media Services pour PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/).
- Les [exemples de code](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) suivants pour vous aider à commencer rapidement :
	- **vodworkflow\_aes.php** : fichier PHP indiquant comment utiliser le chiffrement dynamique AES 128 et le service de remise de clés. Il est basé sur l’exemple .NET expliqué dans [cet](media-services-protect-with-aes128.md) article.
	- **vodworkflow\_aes.php** : fichier PHP indiquant comment utiliser le chiffrement dynamique PlayReady et le service de remise de licences. Il est basé sur l’exemple .NET expliqué dans [cet](media-services-protect-with-drm.md) article.
	- **scale\_encoding\_units.php** : fichier PHP qui indiquant comment mettre à l’échelle l’unité réservée d’encodage.


##<a id="nov_changes_15"></a>Version de novembre 2015

Désormais, Azure Media Services propose un service de distribution de licence Widevine Google sur le cloud. Pour plus d’informations, consultez [ce blog d’annonces](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Consultez également [ce didacticiel](media-services-protect-with-drm.md) et le [référentiel GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

Notez que les services de distribution de licences Widevine fournis par Azure Media Services sont en version préliminaire. Pour plus d’informations, consultez [ce blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Version d’octobre 2015

Azure Media Services (AMS) est désormais également disponible dans les centres de données suivants : sud du Brésil, ouest de l'Inde, sud de l'Inde et Inde centrale. Vous pouvez maintenant utiliser le Portail Azure Classic pour [créer des comptes Service multimédia](media-services-create-account.md#create-a-media-services-account-using-quick-create) et effectuer les diverses tâches décrites [ici](https://azure.microsoft.com/documentation/services/media-services/). La fonctionnalité Live Encoding n'est cependant pas activée dans ces centres de données. En outre, tous les types d'unités réservées d'encodage ne sont pas disponibles dans ces centres de données.

- Sud du Brésil : seules les unités réservées d'encodage standard et de base sont disponibles
- Ouest de l'Inde, sud de l'Inde et Inde centrale : seules les unités réservées d'encodage de base sont disponibles


##<a id="september_changes_15"></a>Version de septembre 2015 

- AMS offre désormais la possibilité de protéger la vidéo à la demande (VOD) et les flux en direct avec la technologie DRM modulaire Widevine. Vous pouvez utiliser les partenaires des services de distribution suivants pour fournir des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Pour plus d’informations, consultez [ce blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

	Vous pouvez utiliser [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (en commençant par la version 3.5.1) ou une API REST pour configurer votre AssetDeliveryConfiguration afin d’utiliser Widevine.

- AMS a ajouté la prise en charge des vidéos Apple ProRes. Vous pouvez à présent télécharger vos fichiers vidéos sources QuickTime qui utilisent Apple ProRes ou d’autres codecs. Pour plus d'informations, consultez [ce blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Vous pouvez à présent utiliser Media Encoder Standard pour effectuer un sous-découpage et une extraction d’archive dynamique. Pour plus d'informations, consultez [ce blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Les mises à jour de filtrage suivantes ont été effectuées :

	- Vous pouvez désormais utiliser le format Apple HTTP Live Streaming (HLS) avec filtre audio uniquement. Cette mise à jour vous permet de supprimer la piste audio uniquement en spécifiant (audio-only=false) dans l'URL.
	- Lorsque vous définissez des filtres pour vos ressources, vous avez désormais la possibilité de combiner plusieurs filtres (jusqu'à 3) dans une même URL.

	Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

- AMS prend désormais en charge I-Frames dans HLS v4. La prise en charge d'I-Frame optimise les opérations d’avance rapide et de retour arrière. Par défaut, toutes les sorties HLS v4 incluent une sélection I-Frame (EXT-X-I-FRAME-STREAM-INF).
 
	Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

##<a id="august_changes_15"></a>Version d’août 2015

- Le Kit de développement logiciel (SDK) Azure Media Services pour Java V0.8.0 et de nouveaux exemples sont désormais disponibles. Pour plus d'informations, consultez les pages suivantes :

	- [Billet de blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
	- [Référentiel d’exemples Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Mise à jour d’Azure Media Player avec prise en charge des flux audio multiples. Pour plus d'informations, consultez les pages suivantes :
	- [Billet de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Version de juillet 2015

- Annonce de la disponibilité générale de Media Encoder Standard. Pour plus d’informations, consultez [ce billet de blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

	Media Encoder Standard utilise les présélections décrites dans [cette](http://go.microsoft.com/fwlink/?LinkId=618336) section. Notez que, lorsque vous utilisez une présélection pour les encodages 4K, vous devez obtenir le type d’unité réservée **Premium**. Pour plus d’informations, consultez [Mise à l’échelle de l’encodage](media-services-scale-media-processing-overview.md).
- Légendes en temps réel en direct avec Azure Media Services et Azure Media Player. Pour plus d’informations, consultez [ce billet de blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

###Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

La version du Kit de développement logiciel (SDK) .NET Azure Media Services est maintenant 3.4.0.0. Les fonctionnalités suivantes ont été ajoutées dans cette version :

- Prise en charge implémentée d'une archive en direct. Notez que vous ne pouvez pas télécharger un actif contenant une archive en direct.
- Prise en charge implémentée des filtres dynamiques.
- Fonctionnalité implémentée qui permet aux utilisateurs de conserver un conteneur de stockage lors de la suppression d'un actif.
- Résolution des bogues liés aux stratégies de nouvelle tentative sur les canaux.
- **Workflow d’encodeur multimédia premium** activé.

##<a id="june_changes_15"></a>Version de juin 2015

###Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

Le Kit de développement logiciel (SDK) Azure Media Services en est maintenant à la version 3.3.0.0. Les fonctionnalités suivantes ont été ajoutées dans cette version :

- prise en charge de la spécification OpenId Connect Discovery,
- prise en charge de la gestion du renouvellement de clés côté fournisseur d’identité.

Si vous utilisez un fournisseur d'identité qui expose le document de découverte OpenID Connect (comme les fournisseurs suivants : Azure Active Directory, Google et Salesforce), vous pouvez demander à Azure Media Services d’obtenir des clés de signature pour la validation des jetons JWT provenant de la spécification de découverte OpenID Connect.

Pour plus d’informations, consultez la page [Utilisation de clés web Json à partir de spécifications de découverte OpenID Connect pour qu’elles fonctionnent avec l’authentification des jetons dans Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Version de mai 2015

Annonce les nouvelles fonctionnalités suivantes :

- [Une version préliminaire de l’encodage en temps réel avec Media Services Live](media-services-manage-live-encoder-enabled-channels.md)
- [Un manifeste dynamique](media-services-dynamic-manifest-overview.md)
- [Une version préliminaire du processeur multimédia Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Version d’avril 2015

 ###Mises à jour générales de Media Services

- [Annonce d’Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- À partir de Media Services REST 2.10, les canaux qui sont configurés pour recevoir un protocole RTMP, sont créés avec des URL de réception principale et secondaire. Pour plus d’informations, consultez la rubrique [Configurations de l'entrée (réception) des canaux](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Mises à jour d’Azure Media Indexer
- Prise en charge de l’espagnol
- Nouveau format de configuration xml

Pour plus d’informations, consultez [ce blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

La version du Kit de développement logiciel (SDK) Azure Media Services est désormais la 3.2.0.0.

Voici certaines mises à jour à destination des clients :

- **Dernière modification** : **TokenRestrictionTemplate.Issuer** et **TokenRestrictionTemplate.Audience** sont désormais de type chaîne.
- Mises à jour relatives à la création de stratégies personnalisées de nouvel essai.
- Correctifs liés au téléchargement/chargement de fichiers.
- La classe **MediaServicesCredentials** accepte désormais le point de terminaison de contrôle d’accès principal et secondaire d’authentification.



##<a id="march_changes_15"></a>Version de mars 2015

### Mises à jour générales de Media Services

- Media Services assure à présent l’intégration au CDN Azure. Pour prendre en charge cette intégration, la propriété **CdnEnabled** a été ajoutée à **StreamingEndpoint**. **CdnEnabled** peut être utilisée avec l’API REST à partir de la version 2.9 (pour plus d’informations, consultez la rubrique [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)). **CdnEnabled** peut être utilisée avec le Kit de développement logiciel (SDK) .NET à partir de la version 3.1.0.2 (pour plus d’informations, consultez la rubrique [StreamingEndpoint])https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Annonce de **Media Encoder Premium Workflow**. Pour plus d’informations, consultez la page [Présentation de l’encodage Premium dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Version de février 2015

### Mises à jour générales de Media Services

La dernière version de l’API REST de Media Services est la version 2.9. À compter de cette version, vous pouvez activer l’intégration du CDN Azure aux points de terminaison de diffusion en continu. Pour plus d’informations, consultez la rubrique [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Version de janvier 2015

### Mises à jour générales de Media Services

Annonce de la disponibilité générale de la protection du contenu avec chiffrement dynamique. Pour plus d’informations, consultez [Azure Media Services améliore la sécurité de la diffusion en continu avec la disponibilité générale de la technologie de DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

La version actuelle du Kit de développement logiciel (SDK) Azure Media Services est désormais la 3.1.0.1.

Cette version a marqué le constructeur Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate par défaut comme obsolète. Le nouveau constructeur prend TokenType comme argument.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Version de décembre 2014

###Mises à jour générales de Media Services

- Certaines mises à jour et nouvelles fonctionnalités ont été ajoutées au processeur Azure Media Indexer. Pour plus d’informations, consultez les [Notes de publication d’Azure Media Indexer 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Ajout d’une nouvelle API REST qui vous permet de mettre à jour les unités réservées d’encodage : [EncodingReservedUnitType avec REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- Ajout de la prise en charge CORS pour le service de distribution des clés.
- Amélioration des performances d'interrogation des options de stratégie d'autorisation.
- Dans le centre de données en Chine, les [URL de distribution des clés](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) fonctionnent désormais par client (comme dans les autres centres de données).
- Ajout de la durée cible TLS automatique. Lors de la diffusion en continu, TLS est toujours empaquetée de façon dynamique. Par défaut, Media Services calcule automatiquement les taux d'empaquetage de segment HLS (FragmentsPerSegment) en fonction de la fréquence des images clés (KeyFrameInterval), également appelé groupe d'images (GOP), qui est reçu de l'encodeur Live. Pour plus d'informations, consultez la page [Utilisation de la diffusion en continu Azure Media Services].
 
###Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

- La version du [Kit de développement logiciel (SDK) Azure Media Services](http://www.nuget.org/packages/windowsazure.mediaservices/) est maintenant la 3.1.0.0.
- Mise à niveau de la dépendance du Kit de développement (SDK) .NET de .NET Framework 4.5.
- Ajout d'une nouvelle API qui vous permet de mettre à jour les unités réservées d'encodage : Pour plus d’informations, consultez la page [Mise à jour du type d’unité réservé et augmentation des RU d’encodage à l’aide de .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Ajout de la prise en charge JWT (Jeton web JSON) de l'authentification des jetons. Pour plus d’informations, consultez la page [Authentification des jetons JWT dans Azure Media Services et chiffrement dynamique](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Ajout de décalages relatifs pour BeginDate et ExpirationDate dans le modèle de licence PlayReady.


##<a id="november_changes_14"></a>Version de novembre 2014

        - Media Services now enables you to ingest a live Smooth Streaming (FMP4) content over an SSL connection. To ingest over SSL, make sure to update the ingest URL to HTTPS.  For more information about live streaming, see [Working with Azure Media Services Live Streaming].
        - Note that currently, you cannot ingest an RTMP live stream over an SSL connection.
        - You can also stream your content over an SSL connection. To do this, make sure your streaming URLs start with HTTPS.
        - Note that you can only stream over SSL if the streaming endpoint from which you deliver your content was created after September 10th, 2014. If your streaming URLs are based on the streaming endpoints created after September 10th, the URL contains “streaming.mediaservices.windows.net” (the new format). Streaming URLs that contain “origin.mediaservices.windows.net” (the old format) do not support SSL. If your URL is in the old format and you want to be able to stream over SSL, [create a new streaming endpoint](media-services-portal-manage-streaming-endpoints.md). Use URLs created based on the new streaming endpoint to stream your content over SSL.

        ##<a id="october_changes_14"></a>October 2014 Release

### <a id="new_encoder_release"></a>Version de l’encodeur Media Services

Annonce de la nouvelle version de l'encodeur multimédia Azure de Media Services. Avec le tout dernier encodeur multimédia Azure, seuls les Go en sortie vous sont facturés. Par ailleurs, les fonctionnalités du nouvel encodeur sont compatibles avec celles du précédent encodeur. Pour plus d'informations, consultez la page [Media Services - Tarification].

### <a id="oct_sdk"></a>Kit de développement logiciel (SDK) .NET de Media Services 

La dernière version du package Extensions du Kit de développement logiciel (SDK) de Media Services pour .NET est la version 2.0.0.3.

La dernière version du Kit de développement logiciel (SDK) de Media Services pour .NET est la version 3.0.0.8.

Les modifications suivantes ont été apportées :

- Refactorisation dans les classes de stratégie de nouvelles tentatives.
- Ajout d'une chaîne d'agent utilisateur pour les en-têtes de requête http.
- Ajout de l'étape de génération de la restauration NuGet.
- Résolution des tests de scénario pour utiliser la certification x509 du dépôt.
- Validation des paramètres lors de la mise à jour du canal et de la fin de la diffusion en continu.
 

### Nouveau dépôt GitHub avec exemples Media Services

Ces exemples se trouvent dans le [référentiel GitHub d’exemples Azure Media Services](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Version de septembre 2014

La dernière version des métadonnées REST de Media Services est la version 2.7. Pour plus d’informations sur les dernières mises à jour REST, consultez la page [Référence de l’API REST d’Azure Media Services].

La dernière version du Kit de développement logiciel (SDK) de Media Services pour .NET est la version 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Dernières modifications

* L'entité **Origin** a été renommée en [StreamingEndpoint].
* Le comportement par défaut, lorsque le **Portail Azure Classic** est utilisé pour encoder puis publier les fichiers MP4, a changé.

Auparavant, lorsque vous utilisiez le portail Azure Classic pour publier un élément multimédia vidéo MP4 correspondant à un seul fichier, une URL SAS était créée (les URL SAS vous permettent de télécharger la vidéo depuis un stockage d'objets blob). Aujourd'hui, lorsque vous vous servez du portail Azure Classic pour encoder, puis publier ce type d'élément, l'URL générée pointe vers un point de terminaison de diffusion en continu Azure Media Services. Cette modification n'a pas d'incidence sur les vidéos MP4 qui sont directement téléchargées vers Media Services et publiées sans être encodées par Azure Media Services.

Actuellement, vous disposez des deux options suivantes pour résoudre le problème.

* Activer les unités de diffusion en continu et utiliser la mise en package dynamique pour diffuser l'élément multimédia .mp4 sous forme d'une présentation de diffusion en continu lisse.

* Créer une URL SAS pour télécharger (ou lire de manière progressive) le fichier .mp4. Pour plus d'informations sur la création d'un localisateur SAS, consultez la page [Distribution de contenu].


### <a id="sept_14_GA_changes"></a>Nouvelles fonctionnalités/nouveaux scénarios intégrés à la version grand public

* **Processeur d'indexation multimédia**. Pour plus d'informations, consultez la page [Indexation des fichiers multimédias avec Azure Media Indexer].

* L'entité [StreamingEndpoint] vous permet désormais d'ajouter des noms de domaine (d'hôte) personnalisés.

	Pour utiliser un nom de domaine personnalisé comme nom de point de terminaison de diffusion en continu Media Services, vous devez ajouter des noms d'hôte personnalisés à votre point de terminaison de diffusion en continu. Utilisez les API REST de Media Services ou le Kit de développement logiciel (SDK) pour .NET pour ajouter des noms d'hôte personnalisés.
	
	Les considérations suivantes s'appliquent :
	
	* Vous devez être propriétaire du nom de domaine personnalisé.
	
	* La propriété du nom de domaine doit être validée par Azure Media Services. Pour valider le domaine, créez un enregistrement CName qui mappe <MediaServicesAccountId>.<parent domain> sur verifydns.<mediaservices-dns-zone>.
	
	* Vous devez créer un autre enregistrement CName qui mappe le nom d’hôte personnalisé (par exemple, sports.contoso.com) vers le nom d’hôte de votre StreamingEndpont Media Services (par exemple, amstest.streaming.mediaservices.windows.net).


	Pour plus d’informations, reportez-vous à la propriété **CustomHostNames** dans la rubrique [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Nouvelles fonctionnalités/nouveaux scénarios intégrés à la version préliminaire publique

* Aperçu de la diffusion en continu. Pour plus d'informations, consultez la page [Utilisation de la diffusion en continu Azure Media Services].

* Service de distribution des clés. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés].

* Chiffrement dynamique AES. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés].

* Service de distribution des licences PlayReady. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady].

* Chiffrement dynamique PlayReady. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady].

* Modèle de licence PlayReady de Media Services. Pour plus d'informations, consultez la page [Présentation du modèle de licence PlayReady de Media Services].

* Diffusion en continu d'éléments multimédias à chiffrement de stockage. Pour plus d'informations, consultez la page [Diffusion en continu de contenu à chiffrement de stockage].

##<a id="august_changes_14"></a>Version d’août 2014

Lorsque vous encodez un élément multimédia, un élément multimédia de sortie est créé à la fin du travail d'encodage. Jusqu'à cette version, l'encodeur Media Services produisait des métadonnées sur les éléments multimédias de sortie. À partir de cette version, l'encodeur produit également des métadonnées sur les éléments multimédias d'entrée. Pour plus d’informations, consultez les rubriques [Métadonnées d’entrée] et [Métadonnées de sortie].


##<a id="july_changes_14"></a>Version de juillet 2014

Plusieurs bogues ont été résolus pour le gestionnaire de package et le chiffreur d'Azure Media Services :

* Seul le son est lu lors du transcodage/multiplexage (transmuxing) d'un élément multimédia d'archive actif pour la diffusion en continu HTTP (HLS). Le problème a été résolu et le son et la vidéo sont tous les deux lus.

* Lors de la mise en package d'un élément multimédia pour la diffusion en continu HTTP (HLS) et le chiffrement d'enveloppe AES 128 bits, les flux packagés ne sont pas lus sur les appareils Android. Ce bogue a été résolu et le flux empaqueté est lu sur les appareils Android qui prennent en charge la diffusion en continu HTTP (HLS).

##<a id="may_changes_14"></a>Version de mai 2014

### <a id="may_14_changes"></a>Mises à jour générales de Media Services

Vous pouvez maintenant utiliser la [mise en package dynamique] pour la diffusion en continu HTTP (HLS) v3. Pour la diffusion en continu HLS v3, ajoutez le format suivant au chemin d'accès du localisateur d’origine : *.ism/manifest(format=m3u8-aapl-v3). Pour plus d'informations, consultez le [blog de Nick Drouin].

Désormais, la mise en package dynamique prend également en charge la transmission du format HLS (v3 et v4) chiffré avec PlayReady sur la base de la diffusion en continu lisse statiquement chiffrée avec PlayReady. Pour des informations sur la façon de chiffrer la diffusion en continu lisse avec PlayReady, consultez la page [Protection de la diffusion en continu lisse avec PlayReady].

### <a name="may_14_donnet_changes"></a>Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

Les améliorations suivantes sont incluses dans la version 3.0.0.5 du Kit de développement logiciel (SDK) .NET de Media Services :

* Vitesse supérieure et meilleure résilience pour le chargement/téléchargement des éléments multimédias.

* Améliorations apportées à la logique de nouvelle tentative et à la gestion des exceptions temporaires :

	* La détection des erreurs temporaires et la logique de nouvelle tentative ont été améliorées pour les exceptions déclenchées par l'interrogation, l'enregistrement des modifications et le chargement ou téléchargement de fichiers.
	
	* En cas d'exceptions Web (par exemple, lors d'une demande de jeton ACS), vous noterez que les erreurs irrécupérables échouent plus rapidement maintenant.

Pour plus d’informations, consultez la page [Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) de Media Services pour .NET].

##<a id="april_changes_14"></a>Version de l’encodeur d’avril 2014

### <a name="april_14_enocer_changes"></a>Mises à jour de l’encodeur Media Services

* Ajout de la prise en charge pour la réception de fichiers AVI créés à l'aide de l'éditeur non linéaire Grass Valley EDIUS où la vidéo est légèrement compressée à l'aide du codec Grass Valley HQ/HQX. Pour plus d'informations, consultez l'article [Grass Valley annonce EDIUS 7 Streaming Through the Cloud].

* Ajout de la prise en charge de la spécification de la convention d'affectation de noms pour les fichiers générés par l'encodeur multimédia. Pour plus d’informations, consultez la page [Contrôle des noms de fichier de sortie de l'encodeur Media Services].

* Ajout de la prise en charge des superpositions vidéo et/ou audio. Pour plus d’informations, consultez la page [Création de superpositions].

* Ajout de la prise en charge de l'assemblage de plusieurs séquences vidéo. Pour plus d'informations, consultez la page [Assemblage de séquences vidéo].

* Correction d'un bogue lié au transcodage de fichiers MP4 dans lesquels la partie audio a été encodée avec MPEG-1 Audio Layer 3 (aka MP3).


##<a id="jan_feb_changes_14"></a>Versions de janvier/février 2014

### <a name="jan_fab_14_donnet_changes"></a>Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1, 3.0.0.2 et 3.0.0.3

Les modifications introduites dans les versions 3.0.0.1 et 3.0.0.2 sont les suivantes :

* Résolution de problèmes concernant l'utilisation de requêtes LINQ avec des instructions OrderBy.

* Division des solutions de test dans [GitHub] en tests unitaires et tests basés sur des scénarios.

Pour plus d’informations sur les modifications, consultez : [Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1 et 3.0.0.2].

Les modifications suivantes ont été apportées à la version 3.0.0.3 :

* Dépendances du stockage Azure mises à niveau pour utiliser la version 3.0.3.0.

* Problème de compatibilité descendante résolu pour les versions 3.0*.*.


##<a id="december_changes_13"></a>Version de décembre 2013

### <a name="dec_13_donnet_changes"></a>Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.0

>[AZURE.NOTE] Les versions 3.0.x.x ne présentent pas de compatibilité descendante avec les versions 2.4.x.x.

La dernière version du Kit de développement logiciel (SDK) Media Services est maintenant la version 3.0.0.0.0. Vous pouvez télécharger le dernier package à partir de Nuget ou obtenir les différents composants sur [GitHub].

À compter de version 3.0.0.0 du Kit de développement logiciel (SDK) Media Services, vous pouvez réutiliser les [jetons ACS (Access Control Service) d'Azure Active Directory]. Pour plus d’informations, consultez la section « Réutilisation des jetons du service de contrôle d’accès » dans la rubrique [Connexion à Media Services à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET].

### <a name="dec_13_donnet_ext_changes"></a>Kit de développement logiciel (SDK) Azure Media Services pour .NET 2.0.0.0

Le package Extensions du Kit de développement logiciel (SDK) Media Services pour .NET est un ensemble de méthodes d'extension et de fonctions d'assistance qui simplifient le code et le développement avec Azure Media Services. Pour obtenir les dernières informations disponibles, consultez la page [Extensions du Kit de développement logiciel (SDK) Media Services pour .NET].

##<a id="november_changes_13"></a>Version de novembre 2013

### <a name="nov_13_donnet_changes"></a>Modifications apportées au Kit de développement logiciel (SDK) Azure Media Services pour .NET

À partir de cette version, le Kit de développement logiciel (SDK) Azure Media Services pour .NET gère toutes les erreurs temporaires qui peuvent se produire lors des appels adressés à la couche API REST de Media Services.

##<a id="august_changes_13"></a>Version d’août 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets PowerShell de Media Services incluses dans les outils du Kit de développement logiciel Azure

Les cmdlets PowerShell de Media Services suivantes sont désormais incluses dans [azure-sdk-tools].

* Get-AzureMediaServices

	Par exemple, `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount

	Par exemple, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* New-AzureMediaServicesKey

	Par exemple, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount

	Par exemple, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Version de juin 2013

### <a name="june_13_general_changes"></a>Modifications apportées à Azure Media Services

Les changements mentionnés dans cette section correspondent aux mises à jour incluses dans les versions de Media Services de juin 2013.

* Possibilité de lier plusieurs comptes de stockage à un compte Media Service.

	StorageAccount
	
	Asset.StorageAccountName and Asset.StorageAccount

* Possibilité de mettre à jour Job.Priority.

* Entités et propriétés liées aux notifications :

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Travail

* Asset.Uri

* Locator.Name

### <a name="june_13_dotnet_changes"></a>Modifications apportées au Kit de développement logiciel (SDK) Azure Media Services pour .NET

Les modifications suivantes sont incluses dans les versions du Kit de développement logiciel (SDK) Media Services de juin 2013. Le dernier Kit de développement logiciel (SDK) Media Services est disponible sur GitHub.

* À partir de la version 2.3.0.0, le Kit de développement logiciel (SDK) Media Services prend en charge la liaison de plusieurs comptes de stockage à un compte Media Services. Les API suivantes prennent en charge cette fonctionnalité :
	
	Le type IStorageAccount type.
	
	La propriété Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
	
	La propriété StorageAccount.
	
	La propriété StorageAccountName.
	
	Pour plus d'informations, consultez la page [Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage].

* API liées aux notifications. À partir de la version 2.2.0.0, vous avez la possibilité d'écouter les notifications du service de stockage de files d'attente Azure. Pour plus d'informations, consultez la page [Gestion des notifications de travaux de Media Services].
	
	La propriété Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
	
	Le type Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
	
	Le type Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
	
	Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
	
	Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
	
	Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dépendance par rapport au Kit de développement logiciel (SDK) du client Azure Storage 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dépendance par rapport à OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Version de décembre 2012

### <a name="dec_12_dotnet_changes"></a>Modifications apportées au Kit de développement logiciel (SDK) Azure Media Services pour .NET

* Intellisense : ajout de la documentation Intellisense manquante pour de nombreux types.

* Microsoft.Practices.TransientFaultHandling.Core : résolution d’un problème dû au fait que le Kit de développement logiciel (SDK) dépendait encore d’une ancienne version de cet assembly. Il fait dorénavant référence à la version 5.1.1209.1 de cet assembly.

Correctifs pour les problèmes détectés dans le Kit de développement logiciel (SDK) de novembre 2012 :

* IAsset.Locators.Count : ce nombre est maintenant indiqué correctement sur les nouvelles interfaces IAsset après la suppression de tous les localisateurs.

* IAssetFile.ContentFileSize : cette valeur est maintenant définie correctement après un téléchargement par IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize : cette propriété peut désormais être définie pendant la création d'un fichier multimédia. Elle était jusqu'à présent en lecture seule.

* IAssetFile.Upload(filepath) : un problème a été résolu avec cette méthode de téléchargement synchrone qui générait l’erreur suivante quand plusieurs fichiers étaient téléchargés vers l’élément multimédia. « Le serveur n'a pas pu authentifier la demande. Vérifiez que la valeur de l'en-tête d'autorisation est formée correctement, avec la signature. »

* IAssetFile.UploadAsync : résolution du problème qui empêchait de télécharger simultanément plus de 5 fichiers.

* IAssetFile.UploadProgressChanged : cet événement est désormais fourni par le Kit de développement logiciel (SDK).

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken) : cette surcharge de méthode est désormais fournie.

* IAssetFile.DownloadAsync : résolution du problème qui empêchait de télécharger simultanément plus de 5 fichiers.

* IAssetFile.Delete() : résolution d’un problème concernant l'appel de la méthode delete, qui pouvait lever une exception si aucun fichier n’était téléchargé pour IAssetFile.

* Travaux : résolution d’un problème concernant le chaînage d’une « tâche MP4 vers diffusion en continu lisse » avec une « tâche de protection PlayReady » à l'aide d’un modèle de travail, qui n’aboutissait à la création d’aucune tâche.

* EncryptionUtils.GetCertificateFromStore() : cette méthode ne lève plus une exception de référence nulle à la suite de l’échec de la recherche du certificat en raison de problèmes de configuration du certificat.

##<a id="november_changes_12"></a>Version de novembre 2012

Les changements mentionnés dans cette section étaient des mises à jour incluses dans le Kit de développement logiciel (SDK) de novembre 2012 (version 2.0.0.0). Ces changements peuvent nécessiter une modification ou une réécriture du code écrit pour la version préliminaire du Kit de développement logiciel (SDK) de juin 2012.

* Éléments multimédias
	
	IAsset.Create(assetName) est la SEULE fonction de création d'éléments multimédias. IAsset.Create ne télécharge plus les fichiers dans le cadre de l'appel de la méthode. Utilisez IAssetFile pour le téléchargement.
	
	La méthode IAsset.Publish et la valeur d'énumération AssetState.Publish ont été supprimées du Kit de développement logiciel (SDK) Services. Tous les codes qui dépendent de cette valeur doivent être réécrits.

* FileInfo

	Cette classe a été supprimée et remplacée par IAssetFile.

	IAssetFiles

	IAssetFile remplace FileInfo et présente un comportement différent. Pour l'utiliser, vous devez instancier l'objet IAssetFiles, puis effectuer un téléchargement de fichier à l'aide du Kit de développement logiciel (SDK) Media Services ou du Kit de développement logiciel (SDK) Azure Storage. Les surcharges IAssetFile.Upload suivantes peuvent être utilisées :

	* IAssetFile.Upload(filePath) : méthode synchrone qui bloque le thread et qui est recommandée uniquement lorsque vous téléchargez un seul fichier.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken) : méthode asynchrone. Il s'agit du mécanisme de téléchargement privilégié.

	Bogue connu : l'utilisation du cancellationToken annulera en effet le téléchargement ; cependant, il existe plusieurs états d'annulation des tâches possibles. Vous devez saisir et traiter les exceptions correctement.

* Localisateurs
	
	Les versions spécifiques d'origine ont été retirées. Les éléments d'associations de sécurité spécifiques context.Locators.CreateSasLocator (asset, accessPolicy) seront marquées comme déconseillés ou supprimés. Consultez la section relative aux localisateurs sous Nouvelles fonctionnalités pour plus d'informations sur le nouveau comportement.


##<a id="june_changes_12"></a>Version préliminaire de juin 2012

La fonctionnalité suivante est une nouveauté de la version de novembre du Kit de développement logiciel (SDK).

* Suppression des entités

	Les objets IAsset, IAssetFile, ILocator, IAccessPolicy et IContentKey sont désormais supprimés au niveau de l'objet, c'est-à-dire IObject.Delete(), au lieu d'exiger une suppression dans la Collection : cloudMediaContext.ObjCollection.Delete(objInstance).

* Localisateurs

	Les localisateurs doivent maintenant être créés avec la méthode CreateLocator et utiliser les valeurs d'énumération LocatorType.SAS ou LocatorType.OnDemandOrigin comme argument pour le type particulier de localisateur que vous souhaitez créer.

	De nouvelles propriétés ont été ajoutées aux localisateurs afin de faciliter l'obtention d'URI utilisables pour votre contenu. Cette nouvelle conception des localisateurs était destinée à fournir une plus grande flexibilité pour une extensibilité tierce ultérieure, et à faciliter davantage l'utilisation pour les applications clientes multimédias.

* Prise en charge de la méthode asynchrone

	La prise en charge asynchrone a été ajoutée à toutes les méthodes.


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Forum MSDN sur Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Référence de l'API REST d'Azure Media Services]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[Référence de l’API REST d’Azure Media Services]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[Media Services - Tarification]: http://azure.microsoft.com/pricing/details/media-services/
[Métadonnées d’entrée]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Métadonnées de sortie]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Distribution de contenu]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexation des fichiers multimédias avec Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Utilisation de la diffusion en continu Azure Media Services]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Présentation du modèle de licence PlayReady de Media Services]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Diffusion en continu de contenu à chiffrement de stockage]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[mise en package dynamique]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protection de la diffusion en continu lisse avec PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) de Media Services pour .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley annonce EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Contrôle des noms de fichier de sortie de l'encodeur Media Services]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Création de superpositions]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Assemblage de séquences vidéo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1 et 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[jetons ACS (Access Control Service) d'Azure Active Directory]: http://msdn.microsoft.com/library/hh147631.aspx
[Connexion à Media Services à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Extensions du Kit de développement logiciel (SDK) Media Services pour .NET]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Gestion des notifications de travaux de Media Services]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 

<!---HONumber=AcomDC_0921_2016-->