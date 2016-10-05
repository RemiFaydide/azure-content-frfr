<properties 
   pageTitle="À propos des paramètres des passerelles VPN de réseau virtuel | Microsoft Azure"
   description="Découvrir les paramètres de passerelle VPN pour réseau virtuel Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# À propos des paramètres de la passerelle VPN

Une solution de connexion de passerelle VPN s’appuie sur la configuration de plusieurs ressources afin d’envoyer le trafic réseau entre les réseaux virtuels et les emplacements locaux. Chaque ressource contient des paramètres configurables. La combinaison des ressources et des paramètres détermine le résultat de la connexion.

Les sections de cet article présentent les ressources et les paramètres relatifs à une passerelle VPN dans le modèle de déploiement **Resource Manager**. Il est parfois utile de visualiser les configurations disponibles à l’aide de diagrammes de topologie de connexion. Vous trouverez les descriptions et les diagrammes de topologie de chaque solution de connexion dans l’article [À propos la passerelle VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Types de passerelle

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel de chaque type. Lorsque vous créez une passerelle de réseau virtuel, vous devez vous assurer que le type de passerelle convient pour votre configuration.

Les valeurs disponibles pour -GatewayType sont :

- Vpn
- ExpressRoute

Une passerelle VPN nécessite le *Vpn* `-GatewayType`.

Exemple :

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased
 

## <a name="gwsku"></a>SKU de passerelle


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

**Spécification de la référence SKU de passerelle dans le portail Azure**

Si vous utilisez le portail Azure pour créer une passerelle de réseau virtuel Resource Manager, la passerelle du réseau virtuel est configurée à l’aide de la référence SKU standard par défaut. Actuellement, vous ne pouvez pas spécifier d’autres références SKU pour le modèle de déploiement de Resource Manager dans le portail Azure. Toutefois, après avoir créé votre passerelle, vous pouvez la mettre à niveau vers une référence SKU de passerelle plus puissante (De base/Standard vers HighPerformance) en utilisant l’applet de commande PowerShell `Resize-AzureRmVirtualNetworkGateway`. Vous pouvez également réduire la taille de référence SKU à l’aide de PowerShell.

**Spécification de la référence SKU de passerelle à l’aide de PowerShell**


L’exemple PowerShell suivant spécifie la `-GatewaySku` en tant que *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased

**Modification d’une référence SKU de passerelle**

Vous pouvez redimensionner une référence SKU de passerelle. L’exemple PowerShell suivant montre une référence SKU de passerelle redimensionnée sur HighPerformance.

	$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

<br>


###  <a name="aggthroughput"></a>Débit agrégé estimé par type de SKU et de passerelle


Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]


## <a name="connectiontype"></a>Types de connexion

Dans le modèle de déploiement de Resource Manager, chaque configuration nécessite un type spécifique de connexion de passerelle de réseau virtuel. Les valeurs de PowerShell pour Resource Manager disponibles pour `-ConnectionType` sont :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Dans l’exemple PowerShell suivant, nous créons une connexion S2S qui nécessite le type de connexion *IPsec*.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Types de VPN

Lorsque vous créez la passerelle de réseau virtuel d’une configuration de passerelle VPN, vous devez spécifier un type de VPN. Le type de VPN que vous choisissez dépend de la topologie de connexion que vous souhaitez créer. Par exemple, une connexion P2S nécessite un VPN de type basé sur un itinéraire. Un type de VPN peut également dépendre du matériel que vous utiliserez. Les configurations S2S nécessitent un périphérique VPN. Certains périphériques VPN seront ne prennent en charge qu’un certain type de VPN.

Le type de VPN que vous choisissez doit satisfaire à toutes les exigences de connexion de la solution que vous souhaitez créer. Par exemple, si vous souhaitez créer une connexion de passerelle VPN S2S et une connexion de passerelle VPN P2S pour le même réseau virtuel, vous utiliserez un VPN de type *basé sur un itinéraire* car P2S requiert un VPN de type basé sur un itinéraire. Vous devez également vérifier que votre périphérique VPN prend en charge une connexion VPN basée sur un itinéraire.

Une fois qu’une passerelle de réseau virtuel a été créée, vous ne pouvez plus modifier le type de VPN. Vous devez supprimer la passerelle de réseau virtuel et en créer une nouvelle. Il existe deux types de VPN :

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


L’exemple PowerShell suivant spécifie la `-VpnType` en tant que *RouteBased*. Lorsque vous créez une passerelle, vous devez vous assurer que -VpnType convient pour votre configuration.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Conditions requises de la passerelle

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Sous-réseau de passerelle

Vous devez créer un sous-réseau de passerelle pour votre réseau virtuel afin de configurer une passerelle de réseau virtuel. Pour fonctionner correctement, le sous-réseau de passerelle doit être nommé *SousRéseau\_Passerelle*. Ce nom indique à Azure que ce sous-réseau doit être utilisé pour la passerelle.

La taille minimale de votre sous-réseau de passerelle dépend entièrement de la configuration que vous voulez créer. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau de passerelle de taille /28 ou supérieure (/28, /27, /26, etc.).

La création d’une passerelle de plus grande taille évite les problèmes liés aux limitations de la taille des passerelles. Par exemple, vous avez peut-être créé une passerelle de réseau virtuel avec une taille de sous-réseau de passerelle /29 pour une connexion S2S. Vous souhaitez à présent définir une configuration coexistante S2S/ExpressRoute. Cette configuration requiert une taille minimale de sous-réseau de passerelle /28. Pour créer votre configuration, vous devez modifier le sous-réseau de passerelle pour prendre en charge la configuration minimale requise pour la connexion, à savoir /28.

L’exemple PowerShell Resource Manager suivant montre un sous-réseau de passerelle nommé GatewaySubnet. Vous pouvez voir que la notation CIDR spécifie une taille /27, ce qui permet d’avoir un nombre suffisamment élevé d’adresses IP pour la plupart des configurations actuelles.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Assurez-vous qu’aucun groupe de sécurité réseau (NSG) n’est appliqué au sous-réseau de passerelle, car cela risque de d’entraîner l’échec des connexions.


## <a name="lng"></a>Passerelles de réseau local

Lorsque vous créez une configuration de passerelle VPN, la passerelle du réseau local représente souvent votre emplacement local. Dans le modèle de déploiement classique, la passerelle de réseau local a été appelée Site local.

Vous donnez un nom à la passerelle de réseau local (l’adresse IP publique du périphérique VPN local) et spécifiez les préfixes d’adresse qui se situent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour votre passerelle de réseau local, et route les paquets en conséquence. Vous spécifiez également des passerelles de réseau local pour les configurations avec interconnexion de réseaux virtuels qui utilisent une connexion de passerelle VPN.

L’exemple PowerShell suivant crée une nouvelle passerelle de réseau local :

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Parfois, vous devez modifier les paramètres de passerelle de réseau local. C’est le cas, par exemple, lorsque vous ajoutez ou modifiez la plage d’adresses, ou lorsque l’adresse IP du périphérique VPN change. Pour un réseau virtuel classique, vous pouvez modifier ces paramètres via la page Réseaux locaux du portail Classic. Pour Resource Manager, voir [Modification des paramètres de passerelle de réseau local à l’aide de PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>API REST et applets de commande PowerShell

Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et d’applets de commande PowerShell pour les configurations de passerelles VPN, consultez les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponible, consultez la rubrique [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).







 

<!---HONumber=AcomDC_0921_2016-->