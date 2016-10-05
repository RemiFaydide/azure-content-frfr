<properties 
	pageTitle="Concepts Azure Media Services | Microsoft Azure" 
	description="Cette rubrique fournit une vue d'ensemble des concepts liés à Azure Media Services" 
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

#Concepts Azure Media Services 

Cette rubrique fournit une vue d'ensemble des concepts les plus importants liés à Media Services.

##<a id="assets"></a>Éléments multimédias et stockage

###Éléments multimédias

Un [élément multimédia](https://msdn.microsoft.com/library/azure/hh974277.aspx) contient des fichiers numériques (y compris vidéo, audio, images, collections de miniatures, légendes et fichiers de sous-titres) et les métadonnées associées à ces fichiers. Une fois les fichiers numériques chargés dans un élément multimédia, ils peuvent être utilisés dans des flux de travail de diffusion et d’encodage Media Services.

Un élément multimédia est mappé à un conteneur d’objets blob dans le compte de stockage Azure et les fichiers de l’élément multimédia sont stockés en tant qu’objets blob dans ce conteneur.

Lors du choix du contenu multimédia à télécharger et à stocker dans un élément multimédia, vous devez prendre en compte les considérations suivantes :

- Un élément multimédia doit contenir une seule et unique instance de contenu multimédia. Par exemple, une seule version d’un épisode d’une série TV, d’un film ou d’une publicité.
- Un élément multimédia ne doit pas contenir plusieurs rendus ou versions d’un fichier audiovisuel. Une tentative d’enregistrement de plusieurs épisodes d’une série TV, plusieurs publicités ou plusieurs angles de caméra à partir d’une production unique dans un élément multimédia constitue un exemple d’utilisation incorrect d’un élément multimédia. Le stockage de plusieurs rendus ou versions d’un fichier audiovisuel dans un élément multimédia peut entraîner des difficultés lors de l’envoi des travaux d’encodage, de la diffusion en continu et de la sécurisation de la remise de l’élément multimédia ultérieurement dans le flux de travail.

###Fichier d’élément multimédia 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier vidéo ou audio réel stocké dans un conteneur d’objets blob. Un fichier d’élément multimédia est toujours associé à un élément multimédia et un élément multimédia peut contenir un ou plusieurs fichiers La tâche de Media Services Encoder échoue si un objet de fichier de ressources n’est pas associé à un fichier numérique dans un conteneur d’objets blob.

L’instance **AssetFile** et le fichier multimédia réel sont deux objets distincts. L’instance AssetFile contient des métadonnées concernant le fichier multimédia, tandis que le fichier multimédia contient le contenu multimédia réel.

Vous ne devez pas essayer de modifier le contenu des conteneurs d’objets blob qui ont été générés par Media Services sans utiliser les API Media Services.

###Options de chiffrement d’élément multimédia

Selon le type de contenu que vous souhaitez télécharger, stocker et remettre, Media Services met à votre disposition différentes options de chiffrement.

**None** : aucun chiffrement. Il s’agit de la valeur par défaut. À noter que quand vous utilisez cette option, votre contenu n’est pas protégé pendant le transit ou le repos dans le stockage.

Si vous prévoyez de remettre du contenu MP4 via un téléchargement progressif, utilisez cette option pour télécharger votre contenu.

**StorageEncrypted** : utilisez cette option pour chiffrer votre contenu localement à l’aide du chiffrement AES 256 bits, puis chargez-le vers Azure Storage où il est stocké au repos sous forme chiffrée. Les éléments multimédias protégés par le chiffrement de stockage sont automatiquement déchiffrés et placés dans un système de fichiers chiffré avant d’être encodés, puis éventuellement rechiffrés avant d’être rechargés sous la forme d’un nouvel élément multimédia de sortie. Le principal cas d’utilisation du chiffrement de stockage concerne la sécurisation des fichiers multimédias d’entrée de haute qualité avec un chiffrement renforcé au repos sur le disque.

Pour fournir un élément multimédia avec chiffrement de stockage, vous devez configurer la stratégie de remise de l'élément multimédia afin que Media Services sache comment vous souhaitez remettre votre contenu. Pour que votre élément multimédia puisse être diffusé en continu, le serveur de diffusion supprime le chiffrement de stockage et diffuse votre contenu à l'aide de la stratégie de remise spécifiée (par exemple AES, PlayReady ou aucun chiffrement).

**CommonEncryptionProtected** : utilisez cette option si vous souhaitez chiffrer du contenu (ou charger du contenu déjà chiffré) avec Common Encryption ou PlayReady DRM (par exemple Smooth Streaming protégé par PlayReady DRM).

**EnvelopeEncryptionProtected** : utilisez cette option si vous souhaitez protéger du contenu (ou charger du contenu déjà protégé) HTTP Live Streaming (HLS) chiffré avec AES (Advanced Encryption Standard). Notez que si vous téléchargez du contenu HLS déjà chiffré avec AES, il doit avoir été chiffré par Transform Manager.

###Stratégie d’accès 

La stratégie d’accès ([AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx)) définit les autorisations (telles que lecture, écriture et énumération) et la durée d’accès à un élément multimédia. En général, vous passez un objet AccessPolicy à un localisateur qui est ensuite utilisé pour accéder aux fichiers contenus dans un élément multimédia.


###Conteneur d’objets blob

Un conteneur d’objets blob regroupe un ensemble d’objets blob. Les conteneurs d’objets blob sont utilisés dans Media Services comme points de limite de contrôle d’accès et comme localisateurs de signature d’accès partagé (SAS, Shared Access Signature) sur les éléments multimédias. Un compte de stockage Azure peut contenir un nombre illimité de conteneurs d’objets blob. Un conteneur peut stocker un nombre illimité d’objets blob.

>[AZURE.NOTE]Vous ne devez pas essayer de modifier le contenu des conteneurs d’objets blob qui ont été générés par Media Services sans utiliser les API Media Services.

###<a id="locators"></a>Localisateurs

Les [localisateur](https://msdn.microsoft.com/library/azure/hh974308.aspx)s fournissent un point d’entrée pour accéder aux fichiers contenus dans une ressource. Une stratégie d’accès est utilisée pour définir les autorisations et la durée pendant laquelle un client a accès à un élément multimédia donné. Les localisateurs peuvent avoir une relation plusieurs-à-un avec une stratégie d’accès, de telle sorte que différents localisateurs puissent fournir différentes heures de démarrage et différents types de connexions à différents clients tout en utilisant tous les mêmes paramètres d’autorisation et de durée. Toutefois, en raison d’une restriction de stratégie d’accès partagé définie par les services de stockage Azure, vous ne pouvez pas avoir plus de cinq localisateurs uniques associés simultanément à un élément multimédia donné.

Media Services prend en charge deux types de localisateur : les localisateurs OnDemandOrigin, utilisés pour diffuser du contenu multimédia (par exemple, MPEG DASH, HLS ou Smooth Streaming) ou télécharger progressivement du contenu multimédia et des localisateurs d’URL SAS, qui servent à charger ou à télécharger des fichiers multimédias vers/depuis le stockage Azure.

Notez que l’autorisation de liste (AccessPermissions.List) ne doit pas être utilisée lors de la création d’un localisateur OrDemandOrigin.

###Compte de stockage

Tous les accès à Azure Storage passent par un compte de stockage. Un compte Media Services peut être associé à un ou plusieurs comptes de stockage. Un compte peut contenir un nombre illimité de conteneurs, tant que la taille totale ne dépasse pas 500 To par compte de stockage. Media Services fournit des outils de niveau SDK pour vous permettre de gérer plusieurs comptes de stockage et d’équilibrer la charge de distribution de vos éléments multimédia pendant le téléchargement vers ces comptes en fonction de métriques ou de la distribution aléatoire. Pour plus d’informations, consultez la page [Utilisation d’Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx).

##Travaux et tâches

Un [travail](https://msdn.microsoft.com/library/azure/hh974289.aspx) sert généralement à traiter (par exemple à indexer ou encoder) une présentation audio/vidéo. Si vous traitez plusieurs vidéos, créez un travail pour chaque vidéo à encoder.

Un travail contient des métadonnées concernant le traitement à effectuer. Chaque travail contient une ou plusieurs [tâche](https://msdn.microsoft.com/library/azure/hh974286.aspx)s qui spécifient une tâche de traitement atomique, ses éléments multimédias d’entrée, ses éléments multimédias de sortie, un processeur multimédia et ses paramètres associés. Les tâches d’un travail peuvent être chaînées, c’est-à-dire que la ressource de sortie d’une tâche peut être transmise comme ressource d’entrée à la tâche suivante. De cette façon, un travail peut contenir tout le traitement nécessaire à une présentation multimédia.

##<a id="encoding"></a>Encodage

Azure Media Services fournit plusieurs options pour l’encodage de fichiers multimédias dans le cloud.

Quand vous commencez à utiliser Media Services, il est important de bien comprendre la différence entre les codecs et les formats de fichiers. Les codecs sont les logiciels qui implémentent les algorithmes de compression/décompression, tandis que les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée.

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats.

Pour tirer parti de l’[empaquetage dynamique](media-services-dynamic-packaging-overview.md), vous devez effectuer les opérations suivantes :

- encoder votre fichier mezzanine (source) dans un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-portal-manage-streaming-endpoints.md).

Media Services prend en charge les éléments suivants sur les encodeurs à la demande décrits dans cet article :

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Pour plus d’informations sur les encodeurs pris en charge, consultez la page [Encodeurs](media-services-encode-asset.md).


##Vidéo en flux continu

Dans Azure Media Services, un canal représente un pipeline de traitement du contenu de diffusion en direct. Un canal reçoit des flux d’entrée en direct de l’une des deux manières suivantes :

- Un encodeur en direct local envoie au canal un paquet RTMP ou Smooth Streaming (MP4 fragmenté) à débit binaire multiple. Vous pouvez utiliser les encodeurs dynamiques suivants qui produisent un flux Smooth Streaming à débit binaire multiple : Elemental, Envivio, Cisco. Les encodeurs dynamiques suivants produisent un flux au format RTMP : Adobe Flash Live, Telestream Wirecast et transcodeurs Tricaster. Les flux reçus transitent par les canaux sans traitement supplémentaire. Lorsqu’il y est invité, Media Services fournit le flux aux clients.

- Un flux à débit binaire unique (dans l’un des formats suivants : RTP [MPEG-TS], RTMP ou Smooth Streaming [MP4 fragmenté]) est envoyé au canal qui est activé pour effectuer un encodage en direct avec Media Services. Le canal procède ensuite à l’encodage en temps réel du flux à débit binaire unique entrant en flux vidéo à débit binaire multiple (adaptatif). Lorsqu’il y est invité, Media Services fournit le flux aux clients.

###Canal

Dans Media Services, les [canaux](https://msdn.microsoft.com/library/azure/dn783458.aspx) sont responsables du traitement du contenu de vidéo en flux continu. Un canal fournit un point de terminaison d’entrée (URL de réception) que vous fournissez ensuite à un transcodeur live. Le canal reçoit des flux d’entrée live en provenance du transcodeur et les met à disposition pour la diffusion en continu via un ou plusieurs StreamingEndpoints. Les canaux fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour obtenir un aperçu et valider votre flux avant tout traitement et remise supplémentaires.

Vous pouvez obtenir l’URL de réception et l’URL d’aperçu lors de la création du canal. Pour obtenir ces URL, il n’est pas obligatoire que le canal soit à l’état démarré. Lorsque vous êtes prêt à commencer l’envoi de données à partir d’un transcodeur live dans le canal, celui-ci doit être démarré. Une fois que le transcodeur live a commencé l’ingestion des données, vous pouvez prévisualiser votre flux.

Chaque compte Media Services peut contenir plusieurs canaux, plusieurs programmes et plusieurs StreamingEndpoints. Selon les besoins en matière de bande passante et de sécurité, les services StreamingEndpoint peuvent être affectés à un ou plusieurs canaux. N’importe quel StreamingEndpoint peut assurer l’extraction à partir de n’importe quel canal.


###Programme

Un [programme](https://msdn.microsoft.com/library/azure/dn783463.aspx) vous permet de contrôler la publication et le stockage des segments dans un flux live. Les canaux gèrent des programmes. La relation entre canal et programme est très similaire au contenu multimédia traditionnel où un canal a un flux de contenu constant et un programme est limité à un événement minuté sur ce canal. Vous pouvez spécifier le nombre d’heures pendant lesquelles vous souhaitez conserver le contenu enregistré pour le programme en définissant la propriété **ArchiveWindowLength**. Cette valeur peut être comprise entre 5 minutes et 25 heures.

ArchiveWindowLength détermine également la durée maximale que les clients peuvent rechercher en arrière à partir de la position dynamique actuelle. Les programmes peuvent durer davantage que le laps de temps spécifié, mais le contenu qui se situe en dehors de la longueur de fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la longueur maximale que les manifestes de client peuvent atteindre.

Chaque programme est associé à un élément multimédia. Pour publier le programme, vous devez créer un localisateur pour l’élément multimédia associé. Le fait de posséder ce localisateur vous permettra de générer une URL de diffusion en continu que vous pourrez fournir à vos clients.

Un canal prend en charge jusqu’à trois programmes exécutés simultanément, ce qui rend possible la création de plusieurs archives du même flux entrant. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Par exemple, imaginez que vous devez archiver 6 heures d’un programme, mais diffuser uniquement les 10 dernières minutes. Pour ce faire, vous devez créer deux programmes exécutés simultanément. Un programme est configuré pour archiver 6 heures de l’événement, mais il n’est pas publié. L’autre programme est configuré pour archiver pendant 10 minutes et il est publié.


Pour plus d'informations, consultez les pages suivantes :

- [Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-live-streaming-with-onprem-encoders.md)
- [Quotas et limitations](media-services-quotas-and-limitations.md)

##Protection du contenu

###Chiffrement dynamique

Azure Media Services vous permet de sécuriser votre contenu multimédia dès lors qu’il quitte votre ordinateur via le stockage, le traitement et la remise. Media Services permet de transmettre un contenu chiffré dynamiquement avec la norme AES (Advanced Encryption Standard) (à l’aide de clés de chiffrement 128 bits) et le chiffrement commun CENC (Common Encryption) en utilisant PlayReady et/ou Widevine DRM. Media Services fournit également un service de distribution de clés AES et licences PlayReady aux clients autorisés.

Actuellement, vous pouvez chiffrer les formats de diffusion en continu suivants : HLS, MPEG DASH et Smooth Streaming. Vous ne pouvez pas chiffrer le format de diffusion en continu HDS ni les téléchargements progressifs.

Si vous souhaitez que Media Services chiffre un élément multimédia, vous devez associer une clé de chiffrement (CommonEncryption ou EnvelopeEncryption) à votre élément multimédia et configurer des stratégies d’autorisation pour la clé.

Si vous souhaitez diffuser un élément multimédia avec chiffrement de stockage, vous devez configurer la stratégie de remise de l’élément multimédia de manière à indiquer la façon dont il sera remis.

Quand un lecteur demande un flux de données, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide d’un chiffrement d’enveloppe (avec AES) ou d’un chiffrement commun (avec PlayReady ou Widevine). Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiées pour la clé.


###Restriction à jeton

La stratégie d’autorisation des clés de contenu peut avoir une ou plusieurs restrictions d’autorisations : ouvert, restriction par jeton ou restriction IP. La stratégie de restriction à jeton doit être accompagnée d’un jeton émis par un service de jeton sécurisé (STS). Media Services prend en charge les jetons aux formats SWT (Simple Web Tokens) et JWT (JSON Web Token). Media Services ne fournit pas de services de jeton sécurisé. Vous pouvez créer un STS personnalisé ou utiliser l’ACS Microsoft Azure pour émettre des jetons. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé (ou licence) demandée au client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé (ou licence).

Lorsque vous configurez la stratégie de restriction par jeton, vous devez définir les paramètres principaux de clé de vérification, émetteur et public. La clé de vérification principale contient la clé utilisée pour signer le jeton, l’émetteur est le service de jeton sécurisé qui émet le jeton. Le public (parfois appelé l’étendue) décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

Pour plus d’informations, consultez les articles suivants :

[Vue d’ensemble de la protection du contenu](media-services-content-protection-overview.md) [Protéger avec AES-128](media-services-protect-with-aes128.md) [Protéger par DRM](media-services-protect-with-drm.md)

##Remise

###<a id="dynamic_packaging"></a>Empaquetage dynamique

Lors de l’utilisation de Media Services, il est recommandé de toujours encoder vos fichiers mezzanine en un ensemble de fichiers MP4 à vitesse de transmission adaptative, puis de convertir le jeu au format souhaité en utilisant l’[empaquetage dynamique](media-services-dynamic-packaging-overview.md).


###Point de terminaison de diffusion en continu

Un StreamingEndpoint représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN, Content Delivery Network) en vue d’une distribution supplémentaire (Azure Media Services fournit désormais l’intégration CDN Azure.) Le flux sortant d’un service StreamingEndpoint peut être un flux dynamique ou un élément multimédia de vidéo à la demande dans votre compte Media Services. En outre, vous pouvez contrôler la capacité du service StreamingEndpoint afin de gérer les besoins croissants en matière de bande passante en ajustant les unités d’échelle (également appelées unités de diffusion). Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Les unités d’échelle fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbits/s et une fonctionnalité supplémentaire qui inclut actuellement l’utilisation de l’empaquetage dynamique.

Il est recommandé d’utiliser l’empaquetage dynamique et/ou le chiffrement dynamique. Pour utiliser ces fonctionnalités, vous devez avoir au moins une unité de diffusion en continu pour le point de terminaison à partir duquel vous souhaitez diffuser. Pour plus d’informations, consultez la rubrique [Mise à l’échelle des unités de diffusion en continu](media-services-portal-manage-streaming-endpoints.md).

Par défaut, vous bénéficiez au maximum de deux points de terminaison de diffusion en continu dans votre compte Media Services. Pour demander une limite plus élevée, consultez la rubrique [Quotas et limitations](media-services-quotas-and-limitations.md).

Vous êtes facturé uniquement lorsque votre StreamingEndpoint est en cours d’exécution.

###Stratégie de remise d’élément multimédia

L’une des étapes du flux de travail de remise de contenu Media Services consiste à configurer les [stratégies de remise pour les éléments multimédias](https://msdn.microsoft.com/library/azure/dn799055.aspx) que vous souhaitez diffuser en continu. La stratégie de remise de ressources indique à Media Services comment vous souhaitez distribuer vos ressources : dans quel protocole de diffusion en continu votre ressource doit être empaquetée dynamiquement (par exemple, MPEG DASH, HLS, diffusion en continu lisse ou tous), si vous souhaitez chiffrer dynamiquement votre ressource ou non et comment (chiffrement commun ou d’enveloppe).

Si vous avez un élément multimédia avec chiffrement de stockage, avant de pouvoir le diffuser en continu, le serveur de diffusion supprime le chiffrement de stockage et diffuse en continu votre contenu à l’aide de la stratégie de remise spécifiée. Par exemple, pour remettre votre élément multimédia chiffré avec une clé de chiffrement AES (Advanced Encryption Standard), définissez le type de stratégie sur DynamicEnvelopeEncryption. Pour supprimer le chiffrement de stockage et diffuser la ressource en clair, définissez le type de stratégie sur NoDynamicEncryption.

###Téléchargement progressif

Le téléchargement progressif vous permet de commencer la lecture multimédia avant que l’intégralité du fichier ait été téléchargée. Seul un fichier MP4 peut être téléchargé progressivement.

Notez que vous devez déchiffrer les éléments multimédias chiffrés si vous souhaitez qu’ils soient disponibles pour le téléchargement progressif.

Pour fournir aux utilisateurs des URL de téléchargement progressif, vous devez d’abord créer un localisateur OnDemandOrigin. La création du localisateur vous donne le chemin d’accès de base à l’élément multimédia. Vous devez ensuite ajouter le nom du fichier MP4. Par exemple :

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###URL de diffusion

Diffusion en continu de votre contenu aux clients. Pour fournir aux utilisateurs des URL de diffusion en continu, vous devez d’abord créer un localisateur OnDemandOrigin. La création du localisateur vous donne le chemin d’accès de base à l’élément multimédia qui contient le contenu que vous souhaitez diffuser. Toutefois, pour pouvoir diffuser ce contenu vous devez modifier ce chemin d’accès. Pour construire une URL complète vers le fichier manifeste de diffusion en continu, vous devez concaténer la valeur de chemin d’accès du localisateur et le nom du fichier manifeste (nom\_fichier.ISM). Ensuite, ajoutez /Manifest et un format approprié (si nécessaire) au chemin d’accès du localisateur.

Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion commence par HTTPS.

Notez que vous pouvez uniquement transmettre en continu via le protocole SSL si le point de terminaison à partir duquel vous distribuez votre contenu a été créé après le 10 septembre 2014. Si vos URL de diffusion sont basées sur des points de terminaison créés après le 10 septembre, l’URL contient « streaming.mediaservices.windows.net » (le nouveau format). Les URL de diffusion qui contiennent « origin.mediaservices.windows.net » (ancien format) ne sont pas compatibles avec le protocole SSL. Si votre URL suit l’ancien format et que vous souhaitez être en mesure de diffuser via le protocole SSL, créez un point de terminaison. L’utilisation d’URL créées à partir du nouveau point de terminaison permet de diffuser votre contenu via le protocole SSL.

La liste suivante décrit les différents formats de diffusion en continu et donne des exemples :

- Smooth Streaming

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG DASH

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP Live Streaming (HLS) V4

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP Live Streaming (HLS) V3

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS (pour les titulaires de licences Adobe PrimeTime/Access uniquement)

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->