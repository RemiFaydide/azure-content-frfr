<properties 
	pageTitle="Configurer la stratégie d’autorisation de clé de contenu à l’aide du Kit de développement logiciel (SDK).NET Media Services | Microsoft Azure" 
	description="Apprenez à configurer une stratégie d’autorisation pour une clé de contenu avec le Kit de développement logiciel (SDK) .NET Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Mingfeiy" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016"
	ms.author="juliako;mingfeiy"/>



# Chiffrement dynamique : configurer la stratégie d’autorisation de clé de contenu

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##Vue d'ensemble

Microsoft Azure Media Services vous permet de fournir des flux MPEG-DASH, Smooth Streaming et HLS (HTTP-Live-Streaming) protégés par AES (Advanced Encryption Standard) à l’aide de clés de chiffrement 128 bits ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS vous permet également de diffuser des flux DASH chiffrés avec Widevine DRM. PlayReady et Widewine sont chiffrés conformément à la spécification de chiffrement commun (ISO/IEC 23001-7 CENC).

Media Services fournit également un **service de remise de clés/de licences** à partir duquel les clients peuvent obtenir des clés AES ou des licences PlayReady/Widevine pour lire le contenu chiffré.

Si vous souhaitez que Media Services chiffre une ressource, vous devez associer une clé de chiffrement (**CommonEncryption** ou **EnvelopeEncryption**) à la ressource (comme décrit [ici](media-services-dotnet-create-contentkey.md)) et configurer les stratégies d'autorisation pour la clé (comme décrit dans cet article).

Lorsqu’un lecteur demande un flux de données, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement AES ou DRM. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiées pour la clé.

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs effectuant des demandes de clé. La stratégie d’autorisation des clés de contenu peut comporter une ou plusieurs restrictions d’autorisation : restriction **ouverte** ou **par jeton**. La stratégie de restriction à jeton doit être accompagnée d’un jeton émis par un service de jeton sécurisé (STS). Media Services prend en charge les jetons aux formats [SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (**Simple Web Tokens**) et [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (**JSON Web Token**).

Media Services ne fournit pas de services de jeton sécurisé. Vous pouvez créer un STS personnalisé ou utiliser l’ACS Microsoft Azure pour émettre des jetons. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton (comme le décrit cet article). Le service de remise de clé Media Services retourne la clé de chiffrement pour le client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé de contenu.

Pour plus d'informations, consultez la rubrique

[Authentification par jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Intégration d'une application Azure Media Services basée sur OWIN MVC avec Azure Active Directory et une remise de clé de contenu basée sur les revendications JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Utilisation d'ACS Azure pour émettre des jetons](http://mingfeiy.com/acs-with-key-services)

###Certaines considérations s’appliquent :

- Pour pouvoir utiliser l’empaquetage et le chiffrement dynamiques, vous devez vous assurer d’avoir au moins une unité réservée de diffusion en continu. Pour plus d'informations, consultez [Mise à l'échelle d'un service de média](media-services-portal-manage-streaming-endpoints.md).
- Votre ressource doit contenir un ensemble de MP4 à débit adaptatif ou des fichiers de diffusion en continu lisse à débit adaptatif. Pour plus d'informations, consultez [Encoder une ressource](media-services-encode-asset.md).
- Téléchargez et codez vos ressources à l'aide de l'option **AssetCreationOptions.StorageEncrypted**.
- Si vous prévoyez d’avoir plusieurs clés de contenu qui nécessitent la même configuration de stratégie, il est fortement recommandé de créer une stratégie d’autorisation unique et de la réutiliser avec plusieurs clés de contenu.
- Le service de remise de clé met en cache ContentKeyAuthorizationPolicy et ses objets connexes (options de stratégie et restrictions) pendant 15 minutes. Si vous créez une ContentKeyAuthorizationPolicy et que vous spécifiez l’utilisation d’une restriction « Jeton », puis la testez avant de mettre à jour la stratégie de restriction vers « Ouverte », vous devrez attendre environ 15 minutes avant que la stratégie bascule vers la version « Ouverte ».
- Si vous ajoutez ou mettez à jour la stratégie de remise de votre ressource, vous devez supprimer le localisateur existant (le cas échéant) et en créer un nouveau.
- Actuellement, vous ne pouvez pas chiffrer le format de diffusion en continu HDS ni les téléchargements progressifs.


##Chiffrement dynamique AES-128.

###Restriction ouverte

La restriction ouverte signifie que le système fournira la clé à toute personne effectuant une demande de clé. Cette restriction peut être utile à des fins de test.

L’exemple suivant crée une stratégie d’autorisation ouverte et l’ajoute à la clé de contenu.

static public void AddOpenAuthorizationPolicy(IContentKey contentKey) { // Create ContentKeyAuthorizationPolicy with Open restrictions // and create authorization policy IContentKeyAuthorizationPolicy policy = \_context. ContentKeyAuthorizationPolicies. CreateAsync("Open Authorization Policy").Result;

List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


###Restriction par jeton

Cette section décrit comment créer une stratégie d’autorisation de clé de contenu et l’associer à la clé de contenu. La stratégie d’autorisation décrit les conditions d’autorisation devant être remplies pour déterminer si l’utilisateur est autorisé à recevoir la clé (par exemple, la liste « clé de vérification » contient-elle la clé qui a servi à signer le jeton).

Pour configurer l’option de restriction par jeton, vous devez utiliser un document XML pour décrire les exigences du jeton d’autorisation. Le XML de configuration de la restriction par jeton doit être conforme au schéma XML suivant.

####<a id="schema"></a>Schéma de restriction par jeton
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

Lorsque vous configurez la stratégie de restriction par **jeton**, vous devez définir les paramètres principaux de **clé de vérification**, **émetteur** et **public**. La **clé de vérification principale** contient la clé utilisée pour signer le jeton, l**’émetteur** est le service de jeton sécurisé qui émet le jeton. Le **public** (parfois appelé **étendue**) décrit l'objectif du jeton ou la ressource à laquelle le jeton autorise l'accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

Quand vous utilisez le **Kit de développement logiciel (SDK) Media Services pour .NET**, vous pouvez utiliser la classe **TokenRestrictionTemplate** pour générer le jeton de restriction. L’exemple suivant crée une stratégie d’autorisation avec une restriction par jeton. Dans cet exemple, le client devra présenter un jeton contenant : une clé de signature (VerificationKey), un émetteur de jeton et les revendications requises.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

####<a id="test"></a>Jeton de test

Pour obtenir un jeton de test basé sur la restriction par jeton utilisée pour la stratégie d’autorisation de clé, procédez comme suit.
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


##Chiffrement dynamique PlayReady 

Media Services vous permet de configurer les droits et les restrictions que vous souhaitez pour le runtime DRM PlayReady, qui s’appliquent lorsqu’un utilisateur tente de lire un contenu protégé.

Quand vous protégez votre contenu avec PlayReady, vous devez spécifier dans votre stratégie d'autorisation une chaîne XML qui définisse le [modèle de licence PlayReady](media-services-playready-license-template-overview.md). Dans le Kit de développement logiciel (SDK) Media Services pour .NET, les classes **PlayReadyLicenseResponseTemplate** et **PlayReadyLicenseTemplate** vous aideront à définir le modèle de licence PlayReady.

[Cette rubrique](media-services-protect-with-drm.md) montre comment chiffrer votre contenu avec **PlayReady** et **Widevine**.

###Restriction ouverte
	
La restriction ouverte signifie que le système fournira la clé à toute personne effectuant une demande de clé. Cette restriction peut être utile à des fins de test.

L’exemple suivant crée une stratégie d’autorisation ouverte et l’ajoute à la clé de contenu.

	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy          
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Open", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	            Requirements = null
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

###Restriction par jeton

Pour configurer l’option de restriction par jeton, vous devez utiliser un document XML pour décrire les exigences du jeton d’autorisation. Le XML de configuration de la restriction par jeton doit être conforme au schéma XML présenté dans [cette](#schema) section.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
       
        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Pour obtenir un jeton de test basé sur la restriction par jeton utilisée pour la stratégie d'autorisation de clé, consultez [cette](#test) section.

##<a id="types"></a>Types utilisés durant la définition de ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Étape suivante
La stratégie d'autorisation de la clé de contenu étant configurée, consultez la rubrique [Comment configurer une stratégie de remise de ressources](media-services-dotnet-configure-asset-delivery-policy.md).
 

<!---HONumber=AcomDC_0921_2016-->