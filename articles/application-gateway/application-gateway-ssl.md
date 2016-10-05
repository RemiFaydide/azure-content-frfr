<properties
   pageTitle="Configurer une passerelle d’application pour le déchargement SSL en utilisant le déploiement classique| Microsoft Azure"
   description="Cet article fournit des instructions pour créer une passerelle d’application avec le déchargement SSL en utilisant le modèle de déploiement classique Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# Configurer une passerelle d’application pour le déchargement SSL en utilisant le modèle de déploiement classique

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Il est possible de configurer Azure Application Gateway de façon à mettre fin à la session SSL (Secure Sockets Layer) sur la passerelle pour éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.


## Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d'un réseau virtuel qui fonctionne avec un sous-réseau valide. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

Pour configurer le déchargement SSL sur une passerelle d’application, exécutez les étapes suivantes dans l’ordre indiqué.

1. [Créer une passerelle Application Gateway](#create-an-application-gateway)
2. [Télécharger des certificats SSL](#upload-ssl-certificates)
3. [Configurer la passerelle](#configure-the-gateway)
4. [Définir la configuration de la passerelle](#set-the-gateway-configuration)
5. [Démarrer la passerelle](#start-the-gateway)
6. [Vérifier l'état de la passerelle](#verify-the-gateway-status)


## Créez une passerelle d’application

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** en remplaçant les valeurs par les vôtres. La facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Pour vérifier que la passerelle a bien été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway**.

Dans l'exemple, *Description*, *InstanceCount* et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est Medium. Les autres valeurs disponibles sont Small et Large. Les paramètres *VirtualIPs* et *DnsName* sont sans valeur, car la passerelle n’a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d’exécution.

	Get-AzureApplicationGateway AppGwTest

## Télécharger des certificats SSL

Utilisez **Add-AzureApplicationGatewaySslCertificate** pour charger le certificat de serveur au format *pfx* sur la passerelle d’application. Le nom du certificat est choisi par l'utilisateur et doit être unique au sein de la passerelle Application Gateway. Ce certificat est identifié par ce nom dans toutes les opérations de gestion de certificat sur la passerelle Application Gateway.

L’exemple suivant illustre l’applet de commande. Remplacez les valeurs utilisées ici par les vôtres.

	Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

Ensuite, validez le téléchargement du certificat. Utilisez l’applet de commande **Get-AzureApplicationGatewayCertificate**.

Cet exemple montre l'applet de commande sur la première ligne, suivie de la sortie.

	Get-AzureApplicationGatewaySslCertificate AppGwTest

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert
	SubjectName    : CN=gwcert.app.test.contoso.com
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned

>[AZURE.NOTE] Le mot de passe du certificat doit comprendre entre 4 et 12 caractères, lettres ou chiffres. Les caractères spéciaux ne sont pas acceptés.

## Configurer la passerelle

La configuration d'une passerelle Application Gateway se compose de plusieurs valeurs. Les valeurs peuvent être liées ensemble pour construire la configuration.

Les valeurs sont :

- **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
- **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
- **Règle :** la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle de *base* est la distribution de charge par tourniquet.

**Notes de configuration supplémentaires :**

Pour configurer des certificats SSL, le protocole dans **HttpListener** doit passer à *Https* (sensible à la casse). L’élément **SslCert** est ajouté à **HttpListener** avec le même nom que celui utilisé pour le chargement des certificats SSL dans la section précédente. Le port du serveur frontal doit être mis à jour sur 443.

**Pour activer l’affinité basée sur les cookies** : une passerelle Application Gateway peut être configurée pour garantir qu’une requête d’une session client est toujours dirigée vers la même machine virtuelle dans la batterie de serveurs web. Ce scénario est réalisé par l’injection d’un cookie de session, qui permet à la passerelle de diriger le trafic de manière appropriée. Pour activer l’affinité basée sur les cookies, définissez **CookieBasedAffinity** sur *Activé* dans l’élément **BackendHttpSettings**.



Vous pouvez construire votre configuration en créant un objet de configuration ou en utilisant un fichier XML de configuration. Pour construire votre configuration à l’aide d’un fichier XML de configuration, utilisez l’exemple suivant.

**Exemple de configuration XML**

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


## Définir la configuration de la passerelle

Ensuite, vous définissez la passerelle Application Gateway. Vous pouvez utiliser l’applet de commande **Set-AzureApplicationGatewayConfig** avec un objet de configuration ou avec un fichier XML de configuration.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## Démarrer la passerelle

Une fois la passerelle configurée, utilisez l’applet de commande **Start-AzureApplicationGateway** pour démarrer la passerelle. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.


**Remarque :** l’exécution de l’applet de commande **Start-AzureApplicationGateway** peut prendre jusqu’à 15 à 20 minutes.

	Start-AzureApplicationGateway AppGwTest

## Vérifier l'état de la passerelle

Utilisez l’applet de commande **Get-AzureApplicationGateway** pour vérifier l’état de la passerelle. Si l’applet de commande **Start-AzureApplicationGateway** a abouti à l’étape précédente, l’état (*State*) doit être Running et les paramètres *VirtualIPs* et *DnsName* doivent avoir des entrées valides.

Cet exemple montre une passerelle d’application en état de s’exécuter et d’accepter du trafic.

	Get-AzureApplicationGateway AppGwTest

	Name          : AppGwTest2
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## Étapes suivantes


Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0921_2016-->