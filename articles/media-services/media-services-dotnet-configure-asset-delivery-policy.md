<properties 
	pageTitle="Configuration de stratégies de remise de ressources à l’aide du Kit de développement logiciel (SDK) .NET | Microsoft Azure" 
	description="Cette rubrique montre comment configurer différentes stratégies de livraison d’éléments multimédias à l’aide du Kit de développement logiciel (SDK) .NET Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Mingfeiy" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako;mingfeiy"/>

#Configuration de stratégies de remise de ressources à l’aide du Kit de développement logiciel (SDK) .NET
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Vue d'ensemble

Si vous envisagez la remise de ressources chiffrées, l'une des étapes du workflow de remise de contenu Media Services consiste à configurer les stratégies de remise pour les ressources. La stratégie de remise de ressources indique à Media Services comment vous souhaitez distribuer vos ressources : dans quel protocole de diffusion en continu votre ressource doit être empaquetée dynamiquement (par exemple, MPEG DASH, HLS, diffusion en continu lisse ou tous), si vous souhaitez chiffrer dynamiquement votre ressource ou non et comment (chiffrement commun ou d’enveloppe).

Cette rubrique explique pourquoi et comment créer et configurer des stratégies de livraison d’éléments multimédias.

>[AZURE.NOTE]Pour pouvoir utiliser l’empaquetage et le chiffrement dynamiques, vous devez vous assurer d’avoir au moins une unité d’échelle (également appelée unité de diffusion). Pour plus d'informations, consultez [Mise à l'échelle d'un service de média](media-services-portal-manage-streaming-endpoints.md).
>
>De plus, votre ressource doit contenir un ensemble de MP4 à débit adaptatif ou des fichiers de diffusion en continu lisse à débit adaptatif.

Vous pouvez appliquer des stratégies différentes à la même ressource. Par exemple, vous pouvez appliquer le chiffrement PlayReady pour la diffusion en continu lisse et AES en enveloppe pour MPEG DASH et TLS. Tous les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, en cas d’ajout d’une stratégie unique qui spécifie uniquement TLS comme protocole) seront bloqués de la diffusion en continu. Cela ne s’applique toutefois pas si vous n’avez défini aucune stratégie de remise de ressources. Tous les protocoles seront alors autorisés.

Si vous souhaitez remettre une ressource à chiffrement de stockage, vous devez configurer la stratégie de remise de la ressource. Avant de pouvoir diffuser votre ressource en continu, le serveur de diffusion supprime le chiffrement de stockage et transmet en continu votre contenu à l’aide de la stratégie de remise spécifiée. Par exemple, pour remettre votre ressource chiffrée avec la clé de chiffrement d'enveloppe AES (Advanced Encryption Standard), définissez le type de stratégie sur **DynamicEnvelopeEncryption**. Pour supprimer le chiffrement de stockage et diffuser la ressource en clair, définissez le type de stratégie sur **NoDynamicEncryption**. Vous trouverez des exemples qui montrent comment configurer ces types de stratégie ci-dessous.

Selon la configuration de la stratégie de remise de ressources, vous pourrez empaqueter dynamiquement, chiffrer dynamiquement et diffuser les protocoles de diffusion en continu suivants : Smooth Streaming, HLS, MPEG DASH et HDS.

La liste suivante présente les formats utilisés pour diffuser en continu lisse, TLS, DASH et HDS.

Smooth Streaming :

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest

HLS :

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=m3u8-aapl)

MPEG DASH

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=mpd-time-csf)

HDS

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID\_de\_localisateur}/{nom\_de\_fichier}.ISM/Manifest(format=f4m-f4f)

Pour savoir comment publier une ressource et générer une URL de diffusion en continu, consultez [Générer une URL de diffusion en continu](media-services-deliver-streaming-content.md).

##Considérations

- Vous ne pouvez pas supprimer une stratégie AssetDeliveryPolicy associée à un élément multimédia alors qu’un localisateur (de diffusion en continuer) OnDemand existe pour cet élément. Il est recommandé de retirer la stratégie de l’élément multimédia avant de la supprimer.
- Il est impossible de créer un localisateur de diffusion en continu sur un élément multimédia chiffré de stockage quand aucune stratégie de distribution d’éléments multimédias n’est définie. Si l’élément multimédia n’est pas chiffré dans le stockage, le système vous permet de créer un localisateur et de diffuser en continu l’élément multimédia en clair sans stratégie de distribution d’éléments multimédias.
- Vous pouvez avoir plusieurs stratégies de distribution d’éléments multimédias associées à un même élément multimédia, mais vous ne pouvez spécifier qu’une seule façon de traiter un AssetDeliveryProtocol donné. Cela signifie que si vous essayez de lier deux stratégies de distribution qui spécifient le protocole AssetDeliveryProtocol.SmoothStreaming, cela va générer une erreur, car le système ne sait pas laquelle appliquer lorsqu’un client émet une demande Smooth Streaming.
- Si vous avez un élément multimédia avec un localisateur de streaming existant, vous ne pouvez pas lier une nouvelle stratégie à l’élément multimédia. Vous pouvez soit supprimer le lien d’une stratégie existante de l’élément multimédia, soit mettre à jour une stratégie de distribution associée à l’élément multimédia. Vous devez d’abord supprimer le localisateur de streaming, ajuster les stratégies, puis recréer le localisateur de streaming. Vous pouvez utiliser le même ID de localisateur (locatorId) lorsque vous recréez le localisateur de streaming. Toutefois, vous devez vérifier que cela ne crée pas de problèmes pour les clients étant donné que le contenu peut être mis en cache par l’origine ou un réseau de distribution de contenu en aval.


##Stratégie de remise de ressources

La méthode **ConfigureClearAssetDeliveryPolicy** qui suit indique de ne pas appliquer le chiffrement dynamique et de distribuer le flux via un des protocoles suivants : MPEG DASH, HLS et Smooth Streaming. Vous souhaiterez peut-être appliquer cette stratégie à vos ressources de stockage chiffrées.

Pour plus d'informations sur les valeurs que vous pouvez spécifier au moment de la création d'une AssetDeliveryPolicy, consultez la section [Types utilisés au moment de la définition d'AssetDeliveryPolicy](#types).

static public void ConfigureClearAssetDeliveryPolicy(IAsset asset) { IAssetDeliveryPolicy policy = \_context.AssetDeliveryPolicies.Create("Clear Policy", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

asset.DeliveryPolicies.Add(policy); }

##Stratégie de remise de ressources DynamicCommonEncryption


La méthode **CreateAssetDeliveryPolicy** suivante crée l’**AssetDeliveryPolicy** configurée pour appliquer le chiffrement dynamique courant (**DynamicCommonEncryption**) à un protocole Smooth Streaming (les autres protocoles ne peuvent pas être diffusés en continu). La méthode accepte deux paramètres : **Asset** (l'élément multimédia auquel vous souhaitez appliquer la stratégie de remise) et **IContentKey** (la clé de contenu de type **CommonEncryption**. Pour plus d'informations, consultez [Création d'une clé de contenu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Pour plus d'informations sur les valeurs que vous pouvez spécifier au moment de la création d'une AssetDeliveryPolicy, consultez la section [Types utilisés au moment de la définition d'AssetDeliveryPolicy](#types).


static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key) { Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration = new Dictionary<AssetDeliveryPolicyConfigurationKey, string> { {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}, };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure Media Services vous permet également d’ajouter un chiffrement Widevine. L'exemple suivant montre l’ajout de PlayReady et Widevine à la stratégie de remise de ressources.


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
    	// Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        

        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Lors du chiffrement avec Widevine, vous ne pourriez effectuer une remise qu’avec DASH. Veillez à spécifier DASH dans le protocole de distribution d’éléments multimédia.


##Stratégie de remise de ressources DynamicEnvelopeEncryption 

La méthode **CreateAssetDeliveryPolicy** suivante crée **l’AssetDeliveryPolicy** configurée pour appliquer le chiffrement dynamique en enveloppe (**DynamicEnvelopeEncryption**) aux protocoles Smooth Streaming, HLS et DASH (si vous décidez de ne pas spécifier certains protocoles, ils ne pourront pas être diffusés en continu). La méthode accepte deux paramètres : **Asset** (l'élément multimédia auquel vous souhaitez appliquer la stratégie de remise) et **IContentKey** (la clé de contenu de type **EnvelopeEncryption**. Pour plus d'informations, consultez [Création d'une clé de contenu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Pour plus d'informations sur les valeurs que vous pouvez spécifier au moment de la création d'une AssetDeliveryPolicy, consultez la section [Types utilisés au moment de la définition d'AssetDeliveryPolicy](#types).

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Types utilisés durant la définition de AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->