<properties
	pageTitle="Substitution de la clé de signature dans Azure AD | Microsoft Azure"
	description="Cet article décrit les meilleures pratiques de substitution de clé de signature pour Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="gsacavdm"
	manager="krassk"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="gsacavdm"/>

# Substitution de la clé de signature dans Azure Active Directory

Cette rubrique explique ce que vous devez savoir sur les clés publiques utilisées dans Azure Active Directory (Azure AD) pour la signature des jetons de sécurité. Il est important de noter que ces clés sont substituées régulièrement, voire immédiatement en cas d’urgence. Toutes les applications qui utilisent Azure AD doivent être en mesure de gérer le processus de substitution de clé ou d’établir un processus périodique de substitution manuelle de clé par le biais d’un programme. En lisant cet article, vous allez comprendre le fonctionnement des clés, savoir comment évaluer l’impact de la substitution de votre application et comment mettre à jour votre application ou établir un processus périodique de substitution manuelle de clé pour gérer la substitution de clé si nécessaire.

## Vue d’ensemble des clés de signature dans Azure AD

Azure AD utilise une technique de chiffrement de clés publiques conforme aux normes du secteur pour établir une relation de confiance avec les applications qui l’utilisent. Plus concrètement, cela signifie qu’Azure AD utilise une clé de signature se composant d’une paire clé publique-clé privée. Lorsqu’un utilisateur se connecte à une application qui utilise Azure AD pour s’authentifier, Azure AD crée un jeton de sécurité qui contient des informations sur l’utilisateur. Ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être renvoyé à l’application. Pour vérifier que le jeton est valide et provient bien d’Azure AD, l’application doit valider la signature du jeton à l’aide de la clé publique exposée par Azure AD contenue dans le [document de découverte OpenID Connect](http://openid.net/specs/openid-connect-discovery-1_0.html) ou dans le [document de métadonnées de fédération](active-directory-federation-metadata.md) SAML/WS-Fed.

Pour des raisons de sécurité, les clés de signature d’Azure AD sont substituées régulièrement, voire immédiatement en cas d’urgence. Toute application intégré à Azure AD doit être préparée à gérer un événement de substitution de clé, quelle que soit sa fréquence. Si ce n’est pas le cas et que votre application tente d’utiliser une clé ayant expiré pour vérifier la signature d’un jeton, la demande de connexion échouera.

Plusieurs clés valides sont disponibles à tout moment dans le document de découverte OpenID Connect et dans le document de métadonnées de fédération. Votre application doit être préparée à utiliser n’importe quelle clé spécifiée dans le document, car une clé peut être substituée rapidement et une autre prise en remplacement, et ainsi de suite.

## Comment évaluer si votre application sera affectée et que faire si cela se produit

La manière dont votre application gère la substitution de la clé dépend de variables comme le type d’application ou le protocole d’identité et la bibliothèque utilisés. Les sections ci-dessous évaluent si les types d’application les plus courants sont affectés par la substitution de clé et fournissent des conseils sur la mise à jour de l’application pour prendre en charge la substitution automatique ou mettre la clé à jour manuellement.

* [Applications clientes natives ayant accès aux ressources](#nativeclient)
* [Applications web/API ayant accès aux ressources](#webclient)
* [Applications web/API protégeant les ressources et créées à l’aide d’Azure App Services](#appservices)
* [Applications web/API protégeant les ressources à l’aide du middleware .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Applications web/API protégeant les ressources à l’aide du middleware .NET Core OpenID Connect ou JwtBearerAuthentication](#owincore)
* [Applications web/API protégeant les ressources à l’aide du module passport-azure-ad Node.js](#passport)
* [Applications web/API protégeant les ressources et créées avec Visual Studio 2015](#vs2015)
* [Applications web protégeant les ressources et créées avec Visual Studio 2013](#vs2013)
* [API web protégeant les ressources et créées avec Visual Studio 2013](#vs2013_webapi)
* [Applications web protégeant les ressources et créées avec Visual Studio 2012](#vs2012)
* [Applications web protégeant les ressources et créées avec Visual Studio 2010, 2008 ou avec Windows Identity Foundation](#vs2010)
* [Applications web/API protégeant les ressources avec d’autres bibliothèques ou implémentant manuellement l’un des protocoles pris en charge](#other)

Ce guide n’est **pas** applicable aux :

* Applications ajoutées depuis la galerie d’applications Azure AD (y compris les applications personnalisées), pour lesquelles il existe des directives spécifiques en ce qui concerne les clés de signature. [Plus d’informations.](active-directory-sso-certs.md)
* Applications locales publiées via le proxy d’application, qui ne sont pas concernées par les clés de signature.

### <a name="nativeclient"></a>Applications clientes natives ayant accès aux ressources

En général, les applications qui ont uniquement accès aux ressources (par exemple, Microsoft Graph, KeyVault, l’API Outlook et d’autres API Microsoft) obtiennent uniquement un jeton et le transmettent au propriétaire de la ressource. Étant donné qu’elles ne protègent pas les ressources, elles n’inspectent pas le jeton et n’ont donc pas besoin de s’assurer qu’il est correctement signé.

Les applications clientes natives, de bureau ou mobiles, appartiennent à cette catégorie et ne sont donc pas affectées par la substitution.

### <a name="webclient"></a>Applications web/API ayant accès aux ressources

En général, les applications qui ont uniquement accès aux ressources (par exemple, Microsoft Graph, KeyVault, l’API Outlook et d’autres API Microsoft) obtiennent uniquement un jeton et le transmettent au propriétaire de la ressource. Étant donné qu’elles ne protègent pas les ressources, elles n’inspectent pas le jeton et n’ont donc pas besoin de s’assurer qu’il est correctement signé.

Les applications Web et les API web qui utilisent le flux d’application uniquement (informations d’identification du client/certificat client) appartiennent à cette catégorie et ne sont donc pas affectées par la substitution.

### <a name="appservices"></a>Applications web/API protégeant les ressources et créées à l’aide d’Azure App Services

La fonctionnalité d’authentification/d’autorisation d’Azure App Services (EasyAuth) possède déjà la logique nécessaire pour gérer automatiquement la substitution de clé.

### <a name="owin"></a>Applications web/API protégeant les ressources à l’aide du middleware .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication

Si votre application utilise le middleware .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication, elle possède déjà la logique nécessaire pour gérer automatiquement la substitution de clé.

Vous pouvez vérifier que votre application utilise l’un de ces middlewares en recherchant les extraits de code suivants dans le fichier Startup.cs ou Startup.Auth.cs de l’application.

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
	 // ...
 	});
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
	 new WindowsAzureActiveDirectoryBearerAuthenticationOptions
	 {
	 // ...
	 });
```

### <a name="owincore"></a>Applications web/API protégeant les ressources à l’aide du middleware .NET Core OpenID Connect ou JwtBearerAuthentication

Si votre application utilise le middleware .NET Core OWIN OpenID Connect ou JwtBearerAuthentication, elle possède déjà la logique nécessaire pour gérer automatiquement la substitution de clé.

Vous pouvez vérifier que votre application utilise l’un de ces middlewares en recherchant les extraits de code suivants dans le fichier Startup.cs ou Startup.Auth.cs de l’application.

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
	 // ...
 	});
```

### <a name="passport"></a>Applications web/API protégeant les ressources à l’aide du module passport-azure-ad Node.js

Si votre application utilise le module passport-ad Node.js, elle possède déjà la logique nécessaire pour gérer automatiquement la substitution de clé.

Vous pouvez vérifier que votre application utilise passport-ad en recherchant l’extrait de code suivant dans le fichier app.js de l’application.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
	//...
));
```

### <a name="vs2015"></a>Applications web/API protégeant les ressources et créées avec Visual Studio 2015

Si votre application a été générée à l’aide d’un modèle d’application web dans Visual Studio 2015 et que vous avez sélectionné **Comptes professionnels et scolaires** dans le menu **Modifier l’authentification**, elle possède déjà la logique nécessaire pour gérer automatiquement la substitution de clé. Cette logique, incorporée dans le middleware OWIN OpenID Connect, récupère et met en cache les clés à partir du document de découverte OpenID Connect, et les actualise régulièrement.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application ne possède peut-être pas la logique de substitution de clé nécessaire. Vous devrez l’écrire vous-même ou suivre les étapes décrites dans [Applications web/API protégeant les ressources avec d’autres bibliothèques ou implémentant manuellement l’un des protocoles pris en charge](#other).

### <a name="vs2013"></a>Applications web protégeant les ressources et créées avec Visual Studio 2013

Si votre application a été générée à l’aide d’un modèle d’application web dans Visual Studio 2013 et que vous avez sélectionné **Comptes professionnels** dans le menu **Modifier l’authentification**, elle possède déjà la logique nécessaire pour gérer automatiquement la substitution de clé. Cette logique stocke l’identifiant unique de votre organisation et les informations de clé de signature dans deux tables de base de données associées au projet. Vous trouverez la chaîne de connexion de la base de données dans le fichier Web.config du projet.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application ne possède peut-être pas la logique de substitution de clé nécessaire. Vous devrez l’écrire vous-même ou suivre les étapes décrites dans [Applications web/API protégeant les ressources avec d’autres bibliothèques ou implémentant manuellement l’un des protocoles pris en charge](#other).

Les étapes suivantes vous aideront à vérifier le bon fonctionnement de la logique de votre application.

1. Dans Visual Studio 2013, ouvrez la solution, puis cliquez sur l’onglet **Explorateur de serveurs** dans la fenêtre de droite.
2. Développez **Connexions de données**, **DefaultConnection**, puis **Tables**. Recherchez la table **IssuingAuthorityKeys**, cliquez avec le bouton droit dessus, puis cliquez sur **Afficher les données de la table**.
3. La table **IssuingAuthorityKeys** comporte au moins une ligne correspondant à la valeur de l’empreinte de la clé. Supprimez toutes les lignes de la table.
4. Cliquez avec le bouton droit sur la table **Clients**, puis cliquez sur **Afficher les données de la table**.
5. La table **Clients** comporte au moins une ligne correspondant à un identifiant unique du client de répertoire. Supprimez toutes les lignes de la table. Si vous ne supprimez pas les lignes à la fois dans la table **Clients** et dans la table **IssuingAuthorityKeys**, vous obtiendrez une erreur au moment de l’exécution.
6. Générez et exécutez l’application. Une fois connecté à votre compte, vous pouvez arrêter l’application.
7. Revenez à **l’Explorateur de serveurs** et examinez les valeurs contenues dans les tables **IssuingAuthorityKeys** et **Clients**. Vous remarquerez qu’elles ont été automatiquement complétées avec les informations appropriées extraites du document de métadonnées de fédération.

### <a name="vs2013"></a>API web protégeant les ressources et créées avec Visual Studio 2013

Si vous avez créé une application API web dans Visual Studio 2013 à l’aide du modèle API web et que vous avez sélectionné **Comptes professionnels** dans le menu **Modifier l’authentification**, votre application contient déjà la logique nécessaire.

Si vous avez configuré l’authentification manuellement, suivez les instructions ci-dessous pour savoir comment configurer votre API web afin de mettre automatiquement à jour ses informations de clé.

L’extrait de code suivant montre comment obtenir les clés les plus récentes à partir du document de métadonnées de fédération, puis comment utiliser le [Gestionnaire de jetons JWT](https://msdn.microsoft.com/library/dn205065.aspx) pour valider le jeton. Cet extrait de code suppose que vous allez utiliser votre propre mécanisme de mise en cache pour conserver la clé afin de valider les prochains jetons d’Azure AD (dans une base de données, dans le fichier de configuration ou à un autre emplacement).

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Applications web protégeant les ressources et créées avec Visual Studio 2012

Si votre application a été créée dans Visual Studio 2012, vous avez probablement utilisé l’outil Identité et accès pour configurer votre application. Il est également probable que vous utilisiez le [registre de validation de nom d’émetteur (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). Le VINR est chargé de conserver les informations relatives aux fournisseurs d’identité approuvés (Azure AD) ainsi que les clés utilisées pour valider les jetons qu’ils émettent. Le VINR permet également de mettre à jour automatiquement les informations de clé stockées dans un fichier Web.config en téléchargeant le dernier document de métadonnées de fédération associé à votre annuaire, en vérifiant si la configuration est à jour avec le dernier document et en mettant à jour l’application pour utiliser la nouvelle clé si nécessaire.

Si vous avez créé votre application à l’aide des exemples de code ou de la documentation détaillée fournis par Microsoft, la logique de substitution de clé est déjà incluse dans votre projet. Vous remarquerez que le code ci-dessous existe déjà dans votre projet. Si votre application ne contient pas déjà cette logique, suivez les étapes ci-dessous pour l’ajouter et vérifier qu’elle fonctionne correctement.

1. Dans **l’Explorateur de solutions**, ajoutez une référence à l’assembly **System.IdentityModel** correspondant au projet approprié.
2. Ouvrez le fichier **Global.asax.cs** et ajoutez le code suivant en utilisant les directives :
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Ajoutez la méthode suivante au fichier **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Appelez la méthode **RefreshValidationSettings()** dans la méthode **Application\_Start()** dans **Global.asax.cs** comme illustré ici :
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Une fois ces étapes effectuées, le fichier Web.config de votre application sera mis à jour avec les dernières informations du document de métadonnées de fédération, y compris les clés les plus récentes. Cette mise à jour sera effectuée chaque fois que votre pool d’applications sera recyclé dans IIS. Par défaut, IIS est configuré pour recycler les applications toutes les 29 heures.

Suivez les étapes ci-dessous pour vérifier que la logique de substitution de clé fonctionne correctement.

1. Vérifiez que votre application utilise bien le code ci-dessus, puis ouvrez le fichier **Web.config** et accédez au bloc **<issuerNameRegistry>**. Examinez plus attentivement les quelques lignes suivantes :
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. Dans le paramètre **<add thumbprint=””>**, modifiez la valeur de l’empreinte en remplaçant l’un des caractères par un caractère différent. Enregistrez le fichier **Web.config**.

3. Générez puis exécutez l’application. Si vous pouvez terminer le processus de connexion, votre application mettra correctement à jour la clé en téléchargeant les informations requises à partir du document de métadonnées de fédération de votre répertoire. Si vous rencontrez des problèmes de connexion, vérifiez que les modifications apportées à votre application sont correctes. Pour cela, consultez la rubrique [Ajout de l’authentification à votre application web à l’aide d’Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect), ou téléchargez et examinez l’exemple de code suivant : [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Application cloud mutualisée pour Azure Active Directory).


### <a name="vs2010"></a>Applications web protégeant les ressources et créées avec Visual Studio 2008 ou 2010 et avec Windows Identity Foundation (WIF) version 1.0 pour .NET 3.5

Si vous avez créé une application sur WIF v1.0, aucun mécanisme n’est prévu pour actualiser automatiquement la configuration de votre application afin de permettre l’utilisation d’une nouvelle clé.

- *Moyen le plus simple* Utilisez les outils FedUtil inclus dans le SDK WIF, qui permettent de récupérer le dernier document de métadonnées et de mettre à jour votre configuration.
- Mettez à jour votre application vers .NET 4.5, qui inclut la dernière version de WIF contenue dans l’espace de noms système. Vous pouvez ensuite utiliser le [registre de validation de nom d’émetteur (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) pour mettre à jour automatiquement la configuration de l’application.
- Effectuez une substitution manuelle en suivant les instructions indiquées à la fin de ce document.

Instructions d’utilisation de FedUtil pour mettre à jour votre configuration :

1. Vérifiez que le SDK WIF v1.0 est installé sur votre ordinateur de développement pour Visual Studio 2008 ou 2010. Dans le cas contraire, vous pouvez [le télécharger ici](https://www.microsoft.com/fr-FR/download/details.aspx?id=4451).
2. Dans Visual Studio, ouvrez la solution, puis cliquez avec le bouton droit sur le projet applicable et sélectionnez **Update federation metadata** (Mettre à jour les métadonnées de fédération). Si cette option n’est pas disponible, cela signifie que l’outil FedUtil et/ou le SDK WIF v1.0 n’a pas été installé.
3. À l’invite, sélectionnez **Mettre à jour** pour mettre à jour vos métadonnées de fédération. Si vous avez accès à l’environnement serveur sur lequel l’application est hébergée, vous pouvez éventuellement utiliser le [planificateur de mise à jour automatique des métadonnées](https://msdn.microsoft.com/library/ee517272.aspx) de FedUtil.
4. Cliquez sur **Terminer** pour terminer le processus de mise à jour.

### <a name="other"></a>Applications web/API protégeant les ressources avec d’autres bibliothèques ou implémentant manuellement l’un des protocoles pris en charge

Si vous utilisez une autre bibliothèque ou si vous avez implémenté manuellement l’un des protocoles pris en charge, vous devez examiner la bibliothèque ou votre implémentation afin de vous assurer que la clé est récupérée à partir du document de découverte OpenID Connect ou du document de métadonnées de fédération. Pour le vérifier, vous pouvez rechercher dans votre code ou dans le code de la bibliothèque les appels vers le document de découverte OpenID ou le document de métadonnées de fédération.

Si la clé est stockée quelque part ou codée en dur dans votre application, vous pouvez récupérer la clé et la mettre à jour manuellement en suivant les instructions indiquées à la fin de ce document. **Nous vous recommandons fortement d’améliorer votre application pour prendre en charge la substitution automatique** en utilisant l’une des approches présentées dans cet article pour éviter les perturbations et les surcharges à l’avenir si Azure AD augmente sa cadence de substitution ou doit effectuer une substitution hors bande en urgence.

## Comment tester votre application pour savoir si elle sera affectée

Pour vérifier que votre application prend en charge la substitution automatique de clé, téléchargez les scripts et suivez les instructions de [ce référentiel GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey).

## Comment effectuer une substitution manuelle si votre application ne prend pas en charge la substitution automatique

Si votre application ne prend **pas** en charge la substitution automatique, vous devez établir un processus permettant de surveiller périodiquement les clés de signature d’Azure AD et d’effectuer une substitution manuelle en conséquence. [Ce référentiel GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contient des scripts et des instructions sur la façon de procéder.

<!---HONumber=AcomDC_0921_2016-->