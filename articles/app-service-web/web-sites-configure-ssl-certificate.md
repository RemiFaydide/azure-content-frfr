<properties
	pageTitle="Sécuriser le domaine personnalisé de votre application avec le protocole HTTPS | Microsoft Azure"
	description="Découvrez comment sécuriser le nom de domaine personnalisé pour votre application dans Azure App Service en configurant une liaison de certificat SSL. Vous apprendrez également comment obtenir un certificat SSL à partir de plusieurs outils."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cephalin"/>

# Sécuriser le domaine personnalisé de votre application avec le protocole HTTPS


> [AZURE.SELECTOR]
- [Acheter un certificat SSL dans Azure](web-sites-purchase-ssl-web-site.md)
- [Utiliser un certificat SSL depuis un autre emplacement](web-sites-configure-ssl-certificate.md)


Cet article vous indique comment activer HTTPS pour une application web, un backend d’application mobile ou une application API dans un [Azure App Service](../app-service/app-service-value-prop-what-is.md) utilisant un nom de domaine personnalisé. Seule l’authentification serveur est abordée. Si vous avez besoin de l’authentification mutuelle (y compris l’authentification client), consultez [Comment configurer l’authentification mutuelle TLS pour App Service](app-service-web-configure-tls-mutual-auth.md).

Pour sécuriser une application disposant d’un nom de domaine personnalisé avec le protocole HTTPS, ajoutez un certificat pour ce nom de domaine. Azure sécurisant par défaut le domaine générique **\*.azurewebsites.net** avec un seul certificat SSL, vos clients peuvent donc déjà accéder à votre application via **https://*&lt;appname>*.azurewebsites.net**. Si vous souhaitez utiliser un domaine personnalisé, tel que **contoso.com**, **www.contoso.com** et **\*.contoso.com**, le certificat par défaut ne peut pas le sécuriser. En outre, comme tous les [certificats génériques](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), le certificat par défaut n’offre pas la même sécurité qu’un domaine personnalisé et qu’un certificat pour ce domaine personnalisé.

>[AZURE.NOTE] Pour accéder à tout moment à l’aide des experts Azure, consultez les [forums Azure](https://azure.microsoft.com/support/forums/). Pour un support personnalisé, accédez à [Support Azure](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir de l’aide**.

<a name="bkmk_domainname"></a>
## Ce dont vous avez besoin
Pour sécuriser votre nom de domaine personnalisé avec le protocole HTTPS, vous devez lier un certificat SSL personnalisé à ce domaine personnalisé dans Azure. Avant de lier un certificat personnalisé, vous devez effectuer les opérations suivantes :

- **Configurer le domaine personnalisé** - App Service autorise uniquement l’ajout d’un certificat pour un nom de domaine déjà configuré dans votre application. Pour obtenir des instructions, consultez [Mapper un nom de domaine personnalisé à une application Azure](web-sites-custom-domain-name.md).
- **Monter en puissance vers le niveau De base ou version supérieure** Les plans App Service dans les niveaux de tarification inférieurs ne prennent pas en charge les certificats SSL personnalisés. Pour obtenir des instructions, consultez la page [Montée en puissance d’une application dans Azure](web-sites-scale.md).
- **Obtenir un certificat SSL** - Si vous n’en avez pas déjà un, vous devez l’obtenir auprès d’une [autorité de certification](http://en.wikipedia.org/wiki/Certificate_authority) (CA) approuvée. Le certificat doit répondre à toutes les exigences suivantes :

	- Il est signé par une autorité de certification approuvée (pas de serveurs d’autorité de certification privés).
	- Il contient une clé privée.
	- Il est créé pour l’échange de clés et exporté vers un fichier PFX.
	- Il utilise un chiffrement à 2 048 bits au minimum.
	- Son nom d’objet correspond au domaine personnalisé qu’il doit sécuriser. Pour sécuriser plusieurs domaines avec un certificat, vous devez utiliser un nom générique (par exemple, ***.contoso.com**) ou spécifier des valeurs subjectAltName.
	- Il est fusionné avec tous les **[certificats intermédiaires](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** utilisés par votre autorité de certification. Sinon, vous pouvez rencontrer des problèmes d’interopérabilité non reproductibles sur certains clients.

		>[AZURE.NOTE] Le moyen le plus simple pour obtenir un certificat SSL répondant à toutes les exigences est [d’en acheter un directement dans le portail Azure](web-sites-purchase-ssl-web-site.md). Cet article vous montre comment le faire manuellement et comment le lier à votre domaine personnalisé dans App Service.
		>	
		> Les **certificats de chiffrement à courbe elliptique (ECC)** peuvent fonctionner avec App Service, mais ce sujet n’est pas abordé dans cet article. Consultez votre autorité de certification sur les étapes à suivre pour créer des certificats ECC.

<a name="bkmk_getcert"></a>
## Étape 1. Obtention d'un certificat SSL

Étant donné que les autorités de certification fournissent différents types de certificat SSL à différents prix, vous devez commencer par décider quel type de certificat SSL vous convient. Pour sécuriser un seul nom de domaine (**www.contoso.com**), un simple certificat de base suffit. Pour sécuriser plusieurs noms de domaine,(**contoso.com** *et* **www.contoso.com** *et* **mail.contoso.com**), vous devez obtenir un [certificat générique](http://en.wikipedia.org/wiki/Wildcard_certificate) ou un certificat avec un [autre nom d’objet](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Une fois que vous savez quel certificat SSL acheter, vous devez envoyer une demande de signature de certificat (CSR) à une autorité de certification. Une fois le certificat demandé fourni par l’autorité de certification, vous devez ensuite générer un fichier .pfx à partir du certificat. Vous pouvez effectuer ces étapes à l’aide de l’outil de votre choix. Voici des instructions pour les outils courants :

- [Étapes Certreq.exe](#bkmk_certreq) - utilitaire Windows pour la création de demandes de certificat. Il fait partie de Windows depuis Windows XP/Windows Server 2000.
- [Étapes du Gestionnaire des services IIS](#bkmk_iismgr) - outil de choix si vous êtes déjà familier avec celui-ci.
- [Étapes OpenSSL](#bkmk_openssl) - [outil open source et multiplateforme](https://www.openssl.org). Utilisez-le pour obtenir un certificat SSL à partir de n’importe quelle plateforme.
- [Étapes subjectAltName à l’aide d’OpenSSL](#bkmk_subjectaltname) - étapes d’obtention `subjectAltName` des certificats.

Si vous souhaitez tester le programme d’installation dans App Service avant d’acheter un certificat, vous pouvez générer un [certificat auto-signé](https://en.wikipedia.org/wiki/Self-signed_certificate). Ce didacticiel vous propose de générer un certificat de deux façons :

- [Certificat auto-signé, étapes Certreq.exe](#bkmk_sscertreq)
- [Certificat auto-signé, étapes OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### Obtention d’un certificat à l’aide de Certreq.exe

1. Créez un fichier (par exemple, **myrequest.txt**) et copiez-y le texte suivant, puis enregistrez-le dans un répertoire de travail. Remplacez l’espace réservé `<your-domain>` par le nom de domaine personnalisé de votre application.

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; Required minimum is 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	Pour plus d’informations sur les options de la demande CSR, ainsi que sur les autres options disponibles, consultez la [documentation de référence Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. Dans une invite de commandes, `CD` dans votre répertoire de travail et exécutez la commande suivante pour créer la demande CSR :

		certreq -new myrequest.txt myrequest.csr

	**myrequest.csr** est maintenant créée dans votre répertoire de travail actuel.

5. Envoyez **myrequest.csr** à une autorité de certification pour obtenir un certificat SSL. Vous pouvez télécharger le fichier ou copier son contenu à partir d’un éditeur de texte dans un formulaire web.

	Pour obtenir la liste des autorités de certification approuvées par Microsoft, consultez [Programme de certification racine de confiance Microsoft : Participants][cas].

6. Une fois que l’autorité de certification vous a répondu avec un fichier de certificat (CER), enregistrez-le dans votre répertoire de travail. Ensuite, exécutez la commande suivante pour terminer la demande CSR en attente.

		certreq -accept -user <certificate-name>.cer

	Cette commande stocke le certificat final dans le magasin de certificats Windows.

6. Si votre autorité de certification utilise des certificats intermédiaires, installez-les avant de continuer. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs web. Sélectionnez la version fournie pour Microsoft IIS.

	Une fois les certificats téléchargés, cliquez avec le bouton droit sur chacun d’entre eux dans l’explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l’**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu’à ce que l’importation soit terminée.

7. Pour exporter votre certificat SSL à partir du magasin de certificats, appuyez sur `Win`+`R` et exécutez **certmgr.msc** pour lancer le Gestionnaire de certificats. Sélectionnez **Personnel** > **Certificats**. Dans la colonne **Délivré à**, vous devez voir une entrée portant le nom de votre domaine personnalisé et dans la colonne **Délivré par**, vous devez voir l’autorité de certification que vous avez utilisée pour générer le certificat.

	![insérer l’image du gestionnaire de certificat ici][certmgr]

9. Cliquez avec le bouton droit sur le certificat et sélectionnez **Toutes les tâches** > **Exporter**. Dans **l’Assistant Exportation de certificat**, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez de nouveau sur **Suivant**.

	![Exporter la clé privée][certwiz1]

10. Sélectionnez **Échange d’informations personnelles - PKCS #12**, **Inclure si possible tous les certificats dans le chemin d’accès de certification** et **Exporter toutes les propriétés étendues**. Cliquez ensuite sur **Suivant**.

	![inclure tous les certificats et les propriétés étendues][certwiz2]

11. Sélectionnez **Mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **Next**.

	![spécifier un mot de passe][certwiz3]

12. Indiquez le chemin d’accès et le nom de fichier pour le certificat exporté, avec l’extension **.pfx**. Cliquez sur **Suivant** pour terminer.

	![indiquer le chemin d’accès au fichier][certwiz4]

Vous êtes maintenant prêt à télécharger le fichier PFX exporté vers App Service. Consultez l’étape 2 [. Téléchargez et liez le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>
### Obtention d'un certificat à l'aide du Gestionnaire des services IIS

1. Générez une demande CSR avec le Gestionnaire des services IIS pour l’envoyer à l’autorité de certification. Pour plus d’informations sur la génération d’une demande de signature de certificat, consultez la page [Demander un certificat de serveur Internet (IIS 7)][iiscsr].

3. Soumettez votre demande CSR à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification approuvées par Microsoft, consultez [Programme de certification racine de confiance Microsoft : Participants][cas].


3. Terminez la demande CSR avec le certificat renvoyé par l’autorité de certification. Pour plus d’informations sur la génération d’une demande de signature de certificat, consultez la page [Installer un certificat de serveur Internet (IIS 7)][installcertiis].

4. Si votre autorité de certification utilise des certificats intermédiaires, installez-les avant de continuer. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs web. Sélectionnez la version fournie pour Microsoft IIS.

	Une fois les certificats téléchargés, cliquez avec le bouton droit sur chacun d’entre eux dans l’explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l’**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu’à ce que l’importation soit terminée.

4. Exportez le certificat SSL à partir du Gestionnaire des services IIS. Pour plus d’informations sur l’exportation du certificat, consultez la page [Exporter un certificat de serveur (IIS 7)][exportcertiis].

	>[AZURE.IMPORTANT] Dans **l’Assistant Exportation de certificat**, vérifiez que vous avez sélectionné **Oui, exporter la clé privée**
	>
	>![Exporter la clé privée][certwiz1]
	>
	> et sélectionnez également **Échange d’informations personnelles - PKCS #12 **, **Inclure si possible tous les certificats dans le chemin d’accès de certification** et **Exporter toutes les propriétés étendues**.
	>
	>![inclure tous les certificats et les propriétés étendues][certwiz2]

Vous êtes maintenant prêt à télécharger le fichier PFX exporté vers App Service. Consultez l’étape 2 [. Téléchargez et liez le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_openssl"></a>
### Obtention d’un certificat à l’aide d’OpenSSL

1. Dans un terminal de ligne de commande, `CD` dans un répertoire de travail génère une clé privée et une demande CSR lorsque la commande suivante est exécutée :

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	Lorsque vous avez terminé, vous devez disposer de deux fichiers dans votre répertoire de travail : **myserver.key** et **server.csr**. Le fichier **server.csr** contient la signature, et vous aurez besoin de **myserver.key** ultérieurement.

3. Soumettez votre demande CSR à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification approuvées par Microsoft, consultez [Programme de certification racine de confiance Microsoft : Participants][cas].


4. Une fois que l’autorité de certification vous envoie le certificat demandé, enregistrez-le dans un fichier nommé **myserver.crt** dans votre répertoire de travail. Si votre autorité de certification fournit le fichier au format texte, copiez simplement le contenu dans **myserver.crt** dans un éditeur de texte et enregistrez-le. Le fichier doit ressembler à ceci :

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. Dans le terminal de ligne de commande, exécutez la commande suivante pour exporter **myserver.pfx** depuis **myserver.key** et **myserver.crt** :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	> [AZURE.NOTE] Si votre autorité de certification utilise des certificats intermédiaires, vous devez les inclure avec le paramètre `-certfile`. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs web. Sélectionnez la version avec l’extension `.pem`.
	>
	> Votre commande `openssl -export` doit ressembler à l’exemple suivant, qui crée un fichier .pfx incluant les certificats intermédiaires à partir du fichier **intermediate-cets.PEM** :
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Vous êtes maintenant prêt à télécharger le fichier PFX exporté vers App Service. Consultez l’étape 2 [. Téléchargez et liez le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>
### Obtention d’un certificat SubjectAltName à l’aide d’OpenSSL

1. Créez un fichier nommé **sancert.cnf** et copiez-y le texte suivant, puis enregistrez-le dans un répertoire de travail :

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	Dans la ligne qui commence par `subjectAltName`, remplacez la valeur avec tous les noms de domaine à sécuriser (en plus de `commonName`). Par exemple :

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Il est inutile de modifier d’autres champs, y compris `commonName`. Vous serez invité à les spécifier au cours des prochaines étapes.

1. Dans un terminal de ligne de commande, `CD` dans votre répertoire de travail et exécutez la commande suivante :

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

	Lorsque vous avez terminé, vous devez disposer de deux fichiers dans votre répertoire de travail : **myserver.key** et **server.csr**. Le fichier **server.csr** contient la signature, et vous aurez besoin de **myserver.key** ultérieurement.

3. Soumettez votre demande CSR à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification approuvées par Microsoft, consultez [Programme de certification racine de confiance Microsoft : Participants][cas].


4. Une fois que l’autorité de certification vous envoie le certificat demandé, enregistrez-le dans un fichier nommé **myserver.crt**. Si votre autorité de certification fournit le fichier au format texte, copiez simplement le contenu dans **myserver.crt** dans un éditeur de texte et enregistrez-le. Le fichier doit se présenter comme suit :

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. Dans le terminal de ligne de commande, exécutez la commande suivante pour exporter **myserver.pfx** depuis **myserver.key** et **myserver.crt** :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	> [AZURE.NOTE] Si votre autorité de certification utilise des certificats intermédiaires, vous devez les inclure avec le paramètre `-certfile`. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs web. Sélectionnez la version avec l’extension `.pem`.
	>
	> Votre commande `openssl -export` doit ressembler à l’exemple suivant, qui crée un fichier .pfx incluant les certificats intermédiaires à partir du fichier **intermediate-cets.PEM** :
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Vous êtes maintenant prêt à télécharger le fichier PFX exporté vers App Service. Consultez l’étape 2 [. Téléchargez et liez le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>
### Génération d’un certificat auto-signé à l’aide de Certreq.exe ###

>[AZURE.IMPORTANT] Les certificats auto-signés sont uniquement destinés à des fins de test. La plupart des navigateurs renvoient une erreur lors de la visite d’un site web sécurisé par un certificat auto-signé. Il se peut même que certains navigateurs refusent l’accès au site.

1. Créez un fichier texte (par exemple, **mycert.txt**) et copiez-y le texte suivant, puis enregistrez-le dans un répertoire de travail. Remplacez l’espace réservé `<your-domain>` par le nom de domaine personnalisé de votre application.

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256
		RequestType = Cert            ; Self-signed certificate
		ValidityPeriod = Years
		ValidityPeriodUnits = 1

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	Le paramètre important est `RequestType = Cert`, qui spécifie un certificat auto-signé. Pour plus d’informations sur les options de la demande CSR, ainsi que sur les autres options disponibles, consultez la [documentation de référence Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. Dans l’invite de commandes, `CD` dans votre répertoire de travail et exécutez la commande suivante :

		certreq -new mycert.txt mycert.crt
	
	Votre nouveau certificat auto-signé est désormais installé dans le magasin de certificats.

7. Pour exporter le certificat à partir du magasin de certificats, appuyez sur `Win`+`R` et exécutez **certmgr.msc** pour lancer le Gestionnaire de certificats. Sélectionnez **Personnel** > **Certificats**. Dans la colonne **Délivré à**, vous devez voir une entrée portant le nom de votre domaine personnalisé et dans la colonne **Délivré par**, vous devez voir l’autorité de certification que vous avez utilisée pour générer le certificat.

	![insérer l’image du gestionnaire de certificat ici][certmgr]

9. Cliquez avec le bouton droit sur le certificat et sélectionnez **Toutes les tâches** > **Exporter**. Dans **l’Assistant Exportation de certificat**, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez de nouveau sur **Suivant**.

	![Exporter la clé privée][certwiz1]

10. Sélectionnez **Échange d’informations personnelles - PKCS #12**, **Inclure si possible tous les certificats dans le chemin d’accès de certification** et **Exporter toutes les propriétés étendues**. Cliquez ensuite sur **Suivant**.

	![inclure tous les certificats et les propriétés étendues][certwiz2]

11. Sélectionnez **Mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **Next**.

	![spécifier un mot de passe][certwiz3]

12. Indiquez le chemin d’accès et le nom de fichier pour le certificat exporté, avec l’extension **.pfx**. Cliquez sur **Suivant** pour terminer.

	![indiquer le chemin d’accès au fichier][certwiz4]

Vous êtes maintenant prêt à télécharger le fichier PFX exporté vers App Service. Consultez l’étape 2 [. Téléchargez et liez le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>
###Génération d’un certificat auto-signé à l’aide d'OpenSSL ###

>[AZURE.IMPORTANT] Les certificats auto-signés sont uniquement destinés à des fins de test. La plupart des navigateurs renvoient une erreur lors de la visite d’un site web sécurisé par un certificat auto-signé. Il se peut même que certains navigateurs refusent l’accès au site.

1. Créez un fichier texte nommé **serverauth.cnf**, puis copiez-y le contenu suivant et enregistrez-le dans un répertoire de travail :

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. Dans un terminal de ligne de commande, `CD` dans votre répertoire de travail et exécutez la commande suivante :

		openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Cette commande crée deux fichiers : **myserver.crt** (certificat auto-signé) et **myserver.key** (clé privée), en fonction des paramètres du document **serverauth.cnf**.

3. Exécutez la commande suivante pour exporter le certificat en tant que fichier .pfx :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

Vous êtes maintenant prêt à télécharger le fichier PFX exporté vers App Service. Consultez l’étape 2 [. Téléchargez et liez le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>
## Étape 2. Téléchargez et liez le certificat SSL personnalisé.

Avant de poursuivre, passez en revue la section [Ce dont vous avez besoin](#bkmk_domainname) et vérifiez les éléments suivants :

- Vous avez un domaine personnalisé qui correspond à votre application Azure.
- Votre application s’exécute au niveau **De base** ou supérieur.
- Vous disposez d’un certificat SSL issu par une autorité de certification pour le domaine personnalisé.


1. Dans votre navigateur, ouvrez le **[portail Azure.](https://portal.azure.com/)**
2.	Cliquez sur l’option **App Service** sur le côté gauche de la page.
3.	Cliquez sur le nom de votre application à laquelle vous voulez attribuer ce certificat.
4.	Dans les **Paramètres**, cliquez sur **Certificats SSL**
5.	Cliquez sur **Télécharger un certificat**
6.	Sélectionnez le fichier .pfx que vous avez exporté à [l’étape 1](#bkmk_getcert) et le mot de passe que vous avez créé précédemment. Cliquez ensuite sur **Charger** pour charger le certificat. Vous devriez maintenant voir votre certificat téléchargé dans le panneau **Certificat SSL**.
7. Dans la section **liaisons ssl**, cliquez sur **Ajouter des liaisons**
8. Dans le panneau **Ajouter une liaison SSL**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser à l’aide du protocole SSL, ainsi que le certificat à utiliser. Vous pouvez également indiquer si vous voulez utiliser **[l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou le protocole SSL basé sur IP.

    ![insérer une image de liaisons SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)

       • Le protocole SSL basé sur IP associe un certificat à un nom de domaine en mappant l’adresse IP publique dédiée du serveur au nom de domaine. Chaque nom de domaine (contoso.com, fabricam.com, etc.) associé à votre service doit donc posséder une adresse IP dédiée. Il s’agit de la méthode classique permettant d’associer des certificats SSL à un serveur Web. • Le protocole SSL basé sur SNI est une extension des protocoles SSL et **[TLS (Transport Layer Security)](http://en.wikipedia.org/wiki/Transport_Layer_Security)** qui permet à plusieurs domaines de partager la même adresse IP, avec des certificats de sécurité distincts pour chaque domaine. La plupart des navigateurs modernes (dont Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI. Il se peut toutefois que les navigateurs plus anciens ne proposent pas cette prise en charge. Pour plus d’informations sur SNI, consultez l’article **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** sur Wikipédia.
     
9. Cliquez sur **Ajouter une liaison** pour enregistrer les modifications et activer SSL.

## Étape 3. Modifier votre mappage de nom de domaine (liaison SSL basée sur IP uniquement)

Si vous utilisez uniquement des liaisons **SSL SNI**, vous pouvez ignorer cette section. Plusieurs liaisons **SSL SNI** peuvent fonctionner conjointement sur l’adresse IP partagée existante attribuée à votre application. Toutefois, si vous créez une liaison **SSL basée sur IP**, App Service crée une adresse IP dédiée pour la liaison, car la liaison **SSL basée sur IP** la requiert. Une seule adresse IP dédiée peut être créée, par conséquent une seule liaison **SSL basée sur IP** peut être ajoutée.

En raison de cette adresse IP dédiée, vous devez configurer votre application davantage dans les cas suivants :

- Vous avez [utilisé un enregistrement A pour mapper votre domaine personnalisé](web-sites-custom-domain-name.md#a) à votre application Azure et vous venez d’ajouter une liaison **SSL basée sur IP**. Dans ce scénario, vous devez remapper l’enregistrement A existant pour pointer vers l’adresse IP dédiée en procédant comme suit :

	1. Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre application. Vous trouverez cette adresse IP sur la page des **domaines personnalisés** sous les paramètres de votre application, juste au-dessus de la section **Noms d’hôtes**. Elle est répertoriée en tant qu’**Adresse IP externe**
    
	    ![Adresse IP virtuelle](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)

	2. [Remappez l’enregistrement A de votre nom de domaine personnalisé à cette nouvelle adresse IP](web-sites-custom-domain-name.md#a).

- Vous disposez déjà d’une ou plusieurs liaisons **SSL SNI** dans votre application et vous venez d’ajouter une liaison **SSL basée sur IP**. Une fois la liaison terminée, votre nom de domaine *&lt;appname>*.azurewebsites.net pointe vers la nouvelle adresse IP. Par conséquent, tout mappage [CNAME existant du domaine personnalisé](web-sites-custom-domain-name.md#cname) vers *&lt;appname>*.azurewebsites.net, y compris ceux que la liaison **SSL SNI** sécurise, reçoit également le trafic sur la nouvelle adresse, créé pour la liaison **SSL basée sur IP** uniquement. Dans ce scénario, vous devez renvoyer le trafic **SSL SNI** vers l’adresse IP partagée d’origine en procédant comme suit :

	1. Identifiez tous les mappages [CNAME de domaines personnalisés](web-sites-custom-domain-name.md#cname) vers votre application disposant d’une liaison **SSL SNI**.

	2. Remappez chaque enregistrement CNAME vers **sni.**&lt;appname>.azurewebsites.net au lieu de &lt;appname>.azurewebsites.net.

## Étape 4. Tester HTTPS pour votre domaine personnalisé

Il ne reste plus maintenant qu’à vous assurer que HTTPS fonctionne pour votre domaine personnalisé. Dans différents navigateurs, accédez à `https://<your.custom.domain>` pour vérifier qu’il fournit votre application.

- Si votre application permet de voir les erreurs de validation de certificat, vous utilisez probablement un certificat auto-signé.

- Si ce n’est pas le cas, vous pouvez avoir oublié des certificats intermédiaires lorsque vous avez exporté votre certificat .pfx. Revenez à la section [Ce dont vous avez besoin](#bkmk_domainname) pour vérifier que votre demande CSR répond à toutes les exigences d’App Service.

<a name="bkmk_enforce"></a>
## Exécution de SSL sur votre application

Si vous souhaitez toujours autoriser l’accès HTTP à votre application, ignorez cette étape. App Service n’appliquant *pas* le protocole HTTPS, les visiteurs peuvent donc toujours accéder à votre application à l’aide de HTTP. Si vous souhaitez appliquer le protocole HTTPS pour votre application, vous pouvez définir une règle de réécriture dans le fichier `web.config` pour votre application. Chaque application App Service contient ce fichier, quelle que soit l’infrastructure de langage de votre application.

> [AZURE.NOTE] Certaines redirections de requête sont propres au langage. ASP.NET MVC peut utiliser le filtre [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) au lieu de la règle de réécriture dans `web.config` (voir [Déployer une application ASP.NET MVC 5 sécurisée vers une application web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).

Procédez comme suit :

1. Accédez à la console de débogage Kudu pour votre application. Son adresse est `https://<appname>.scm.azurewebsites.net/DebugConsole`.

2. Dans la console de débogage, accédez à `D:\home\site\wwwroot`.

3. Ouvrez `web.config` en cliquant sur le bouton du crayon.

	![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

	Si vous déployez votre application avec Visual Studio ou Git, App Service génère automatiquement le `web.config` approprié pour votre application .NET, PHP, Node.js ou Python dans la racine de l’application. Si `web.config` n’existe pas, exécutez `touch web.config` dans l’invite de commandes web pour le créer. Vous pouvez également le créer dans votre projet local et redéployer votre code.

4. Si vous avez dû créer un `web.config`, copiez-y le code suivant et enregistrez-le. Si vous avez ouvert un fichier web.config existant, il vous suffit de copier l’intégralité de la balise `<rule>` dans l’élément `configuration/system.webServer/rewrite/rules` de votre `web.config`.

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <rewrite>
		      <rules>
			    <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
		        <rule name="Force HTTPS" enabled="true">
		          <match url="(.*)" ignoreCase="false" />
		          <conditions>
		            <add input="{HTTPS}" pattern="off" />
		          </conditions>
		          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
		        </rule>
				<!-- END rule TAG FOR HTTPS REDIRECT -->
		      </rules>
		    </rewrite>
          </system.webServer>
		</configuration>

	Cette règle renvoie un HTTP 301 (redirection permanente) vers le protocole HTTPS chaque fois que l’utilisateur demande une page à l’aide de HTTP. Il redirige de http://contoso.com à https://contoso.com.

	>[AZURE.IMPORTANT] S’il existe déjà d’autres balises `<rule>` dans votre `web.config`, placez la balise `<rule>` copiée avant les autres balises `<rule>`.

4. Enregistrez le fichier dans la console de débogage Kudu. Toutes les demandes doivent normalement être immédiatement redirigées vers HTTPS.

Pour plus d'informations sur le module Réécriture d'URL d'IIS, consultez la documentation sur la [Réécriture d'URL](http://www.iis.net/downloads/microsoft/url-rewrite).

## Autres ressources ##
- [Centre de gestion de la confidentialité Microsoft Azure](/support/trust-center/security/)
- [Options de configuration déverrouillées dans Sites Web Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activer la journalisation des diagnostics](web-sites-enable-diagnostic-log.md)
- [Configurer des applications web dans Azure App Service](web-sites-configure.md)
- [Portail de gestion Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png

<!---HONumber=AcomDC_0914_2016-->