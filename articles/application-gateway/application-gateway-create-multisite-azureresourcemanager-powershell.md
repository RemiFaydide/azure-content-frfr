<properties
   pageTitle="Créer une passerelle Application Gateway pour l’hébergement de plusieurs sites | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer une passerelle Application Gateway Azure pour l’hébergement de plusieurs applications web sur la même passerelle."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>


# Créer une passerelle Application Gateway pour l’hébergement de plusieurs applications web

L’hébergement de plusieurs sites vous permet de déployer plusieurs applications web sur la même passerelle Application Gateway. Il s’appuie sur la présence de l’en-tête de l’hôte dans la requête HTTP entrante pour déterminer quel écouteur reçoit le trafic. L’écouteur dirige ensuite le trafic vers le pool principal approprié tel que configuré dans la définition des règles de la passerelle. Dans les applications web SSL, la passerelle Application Gateway s’appuie sur l’extension d’indication du nom du serveur (SNI) pour choisir l’écouteur approprié au trafic web.

Une utilisation courante de l’hébergement de plusieurs sites consiste à équilibrer la charge des demandes pour différents domaines Web entre différents pools de serveurs principaux. De même, plusieurs sous-domaines du même domaine racine pourraient également être hébergés sur la même passerelle Application Gateway.

## Scénario
Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com et fabrikam.com avec deux pools de serveurs principaux : un pool de serveurs contoso et un pool de serveurs fabrikam. Une configuration similaire pourrait servir à héberger des sous-domaines hôtes comme app.contoso.com et blog.contoso.com.


## Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs ajoutés au pool principal pour utiliser la passerelle Application Gateway doivent exister, ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.



## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?


- **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique. Le nom de domaine complet peut également être utilisé.
- **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). Pour les passerelles Application Gateway activées pour plusieurs sites, le nom d’hôte et les indicateurs SNI sont également ajoutés.
- **Règle :** la règle lie l’écouteur et le pool de serveurs principaux, et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique.

## Créez une passerelle d’application

Procédure de création d’une passerelle Application Gateway :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créez une ressource Application Gateway.

## Créer un groupe de ressources pour Resource Manager

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### Étape 1

Connexion à Azure

	Login-AzureRmAccount

Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.

### Étape 2 :

Vérifiez les abonnements associés au compte.

	Get-AzureRmSubscription

### Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

	Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-RG -location "East Asia"

Vous pouvez également créer des balises pour un groupe de ressources pour la passerelle Application Gateway :
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "East Asia" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"} 

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « East Asia ».

>[AZURE.NOTE] Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md).

## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager.

### Étape 1 :

Attribuez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Étape 2 :

Créez un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appwrg » pour la région « West US » à l'aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "East Asia" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Étape 3

Attribuez une variable de sous-réseau pour les prochaines étapes, qui permet de créer une passerelle Application Gateway.

	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -VirtualNetwork $vnet

## Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "East Asia" -AllocationMethod Dynamic

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## Créer une configuration de passerelle Application Gateway

Avant de créer la passerelle d’application, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### Étape 1 :

Créez une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Étape 2

Configurez les pools d’adresses IP principaux nommés « pool01 » et « pool2 » avec les adresses IP « 10.0.0.100, 10.0.0.101,10.0.0.102 » pour « pool1 » et « 10.0.0.103, 10.0.0.104, 10.0.0.105 » pour « pool2 ».


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.100, 10.0.0.101,10.0.0.102 
	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.0.103, 10.0.0.104, 10.0.0.105

Dans cet exemple, il existe deux pools principaux pour acheminer le trafic réseau selon le site demandé. Un pool reçoit le trafic du site « contoso.com » et l’autre le trafic du site « fabrikam.com ». Vous devez remplacer les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP d’application. À la place des adresses IP internes, vous pouvez également utiliser des adresses IP publiques, un nom de domaine complet ou une carte réseau d’une machine virtuelle pour les instances de backend. Utilisez le paramètre « -BackendFQDNs » dans PowerShell pour spécifier les noms de domaine complets au lieu des adresses IP.

### Étape 3

Configurez les paramètres de passerelle Application Gateway « poolsetting01 » et « poolsetting02 » pour le trafic réseau à charge équilibrée dans le pool principal. Dans cet exemple, vous configurez différents paramètres pour les pools principaux. Chaque pool principal peut avoir son propre paramètre de pool principal.


	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Étape 4

Configurez l’adresse IP frontale avec un point de terminaison IP public.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Étape 5

Configurez le port frontal pour une passerelle Application Gateway.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### Étape 6

Configurer deux certificats SSL pour les deux sites Web que nous allons traiter dans cet exemple. Un certificat est pour le trafic de contoso.com et l’autre pour le trafic fabrikam.com. Il doit s’agir de certificats émis par une autorité de certification pour vos sites Web. Les certificats auto-signés sont pris en charge mais déconseillés pour le trafic de production.

	$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name  contosocert -CertificateFile <file path> -Password <password>
	$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### Étape 7

Dans cet exemple, configurez deux écouteurs pour les deux sites Web. Cette étape permet de configurer les écouteurs pour l’adresse IP publique, ainsi que le port et l’hôte utilisés pour recevoir le trafic entrant. Le paramètre HostName est requis pour la prise en charge de plusieurs sites et doit être défini sur le site Web approprié pour lequel le trafic est reçu. Le paramètre RequireServerNameIndication doit être défini sur true pour les sites web ayant besoin d’une prise en charge SSL dans un scénario à plusieurs hôtes. Si la prise en charge SSL est requise, vous devez également spécifier le certificat SSL utilisé pour sécuriser le trafic de cette application web. L’association de FrontendIPConfiguration, FrontendPort et de HostName doit être unique pour un écouteur. Chaque écouteur peut prendre en charge un seul certificat.

	$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
	$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### Étape 8

Dans cet exemple, créez deux paramètres de règle pour les deux applications web. Une règle associe les écouteurs, les pools principaux et les paramètres http. Cette étape configure la passerelle Application Gateway afin d’utiliser la règle de routage de base (une pour chaque site web). Le trafic vers chaque site web est reçu par son écouteur configuré, puis dirigé vers son pool principal configuré, à l’aide des propriétés spécifiées dans BackendHttpSettings.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
	$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### Étape 9

Configurez le nombre d’instances et taille de la passerelle Application Gateway.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## Créer une passerelle Application Gateway

Créez une passerelle Application Gateway avec tous les objets de configuration à partir de la procédure précédente.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "East Asia" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02 	


>[AZURE.IMPORTANT] L’approvisionnement de la passerelle Application Gateway est une opération longue qui peut prendre du temps.

## Obtenir le nom DNS d’une passerelle Application Gateway

Récupérer les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.
	
	Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01
		
	Name                     : publicIP01
	ResourceGroupName        : appgw-RG
	Location                 : eastasia
	Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
	Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
	ResourceGuid             : 00000000-0000-0000-0000-000000000000
	ProvisioningState        : Succeeded
	Tags                     : 
	PublicIpAllocationMethod : Dynamic
	IpAddress                : xx.xx.xxx.xx
	PublicIpAddressVersion   : IPv4
	IdleTimeoutInMinutes     : 4
	IpConfiguration          : {
	                             "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
	                           Configurations/frontend1"
	                           }
	DnsSettings              : {
	                             "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
	                           }

<!---HONumber=AcomDC_0921_2016-->