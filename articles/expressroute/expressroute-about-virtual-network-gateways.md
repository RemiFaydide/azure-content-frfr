<properties 
   pageTitle="À propos des passerelles de réseau virtuel pour ExpressRoute | Microsoft Azure"
   description="En savoir plus sur les passerelles de réseau virtuel pour ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# À propos des passerelles de réseau virtuel pour ExpressRoute


Une passerelle de réseau virtuel est conçue pour faire circuler le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux. Lorsque vous configurez une connexion ExpressRoute, vous devez créer et configurer une passerelle de réseau virtuel et une connexion à la passerelle de réseau virtuel.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez plusieurs paramètres. L’un des paramètres requis spécifie si la passerelle sera utilisée pour le trafic ExpressRoute ou pour le trafic de la passerelle VPN. Dans le modèle de déploiement de Resource Manager, le paramètre est « -GatewayType ».

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle « ExpressRoute ». C’est ce que l’on appelle une passerelle ExpressRoute. Lorsque le trafic réseau est chiffré et envoyé via une connexion publique, vous utilisez le type de passerelle « Vpn ». Il s’agit alors d’une passerelle VPN. Les connexions site à site, point à site et réseau virtuel à réseau virtuel utilisent toutes une passerelle VPN.

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel par type de passerelle. Par exemple, une passerelle de réseau virtuel peut utiliser le type de passerelle VPN et une autre le type de passerelle ExpressRoute. Cet article décrit la passerelle de réseau virtuel ExpressRoute.

## <a name="gwsku"></a>SKU de passerelle

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si vous utilisez le portail Azure pour créer une passerelle de réseau virtuel Resource Manager, celle-ci est configurée par défaut à l’aide de la référence standard. Actuellement, vous ne pouvez pas spécifier d’autres références SKU pour le modèle de déploiement de Resource Manager dans le portail Azure. Toutefois, après avoir créé votre passerelle, vous pouvez la mettre à niveau vers une référence SKU de passerelle plus puissante (par exemple, de Base/Standard vers HighPerformance) en utilisant l’applet de commande Resize-AzureRmVirtualNetworkGateway de PowerShell.

###  <a name="aggthroughput"></a>Débit agrégé estimé par SKU de passerelle


Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]


## <a name="resources"></a>API REST et applets de commande PowerShell

Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et d’applets de commande PowerShell pour les configurations de passerelles de réseau virtuel, consultez les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponibles, consultez la rubrique [Vue d’ensemble d’ExpressRoute](expressroute-introduction.md).







 

<!---HONumber=AcomDC_0921_2016-->