<properties 
   pageTitle="À propos de la passerelle VPN | Microsoft Azure"
   description="En savoir plus sur les connexions par passerelle VPN pour réseaux virtuels Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# À propos de la passerelle VPN


Une passerelle de réseau virtuel est conçue pour faire circuler le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux, mais aussi entre plusieurs réseaux virtuels au sein d’Azure (connexion entre deux réseaux virtuels). Lorsque vous configurez une passerelle VPN, vous devez créer et configurer une passerelle de réseau virtuel et une connexion à la passerelle de réseau virtuel.

Avec le modèle de déploiement Resource Manager, lorsque vous créez une ressource de passerelle de réseau virtuel, vous spécifiez plusieurs paramètres. L’un des paramètres requis est « -GatewayType ». Il existe deux types de passerelle de réseau virtuel : Vpn et ExpressRoute.

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle « ExpressRoute ». C’est ce que l’on appelle une passerelle ExpressRoute. Lorsque le trafic réseau est chiffré et envoyé via une connexion publique, vous utilisez le type de passerelle « Vpn ». Il s’agit alors d’une passerelle VPN. Les connexions site à site, point à site et réseau virtuel à réseau virtuel utilisent toutes une passerelle VPN.

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel par type de passerelle. Par exemple, vous pouvez avoir une passerelle de réseau virtuel qui utilise -GatewayType ExpressRoute et une autre -GatewayType Vpn. Cet article se concentre essentiellement sur la passerelle VPN. Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

## Tarification

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]


## SKU de passerelle

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## Configuration d’une passerelle VPN

Lorsque vous configurez votre passerelle VPN, les instructions que vous utilisez varient selon le modèle de déploiement que vous avez utilisé pour créer votre réseau virtuel. Par exemple, si vous avez créé votre réseau virtuel à l’aide du modèle de déploiement classique, vous utilisez les recommandations et les instructions pour le modèle de déploiement classique afin de créer et configurer les paramètres de votre passerelle VPN. Pour plus d’informations sur les modèles de déploiement, voir [Comprendre les modèles de déploiement Resource Manager et de déploiement classique](../resource-manager-deployment-model.md).

Une connexion par passerelle VPN s’appuie sur plusieurs ressources qui sont configurées avec des paramètres spécifiques. La plupart des ressources peuvent être configurées séparément, mais elles doivent être configurées dans un certain ordre dans certains cas. Vous pouvez commencer par créer et configurer des ressources à l’aide de l’un des outils de configuration, comme le portail Azure. Vous pouvez décider ultérieurement de passer à un autre outil, tel que PowerShell, pour configurer des ressources supplémentaires ou pour modifier les ressources existantes, le cas échéant. Il n’est pour le moment pas possible de configurer toutes les ressources et tous les paramètres des ressources dans le portail Azure. Les instructions fournies dans les articles dédiés à chaque topologie de connexion indiquent si un outil de configuration spécifique est requis. Pour plus d’informations sur les ressources et paramètres spécifiques pour la passerelle VPN, consultez [À propos des paramètres de passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md).

Les sections ci-après contiennent des tableaux répertoriant les éléments suivants :

- Modèle de déploiement disponible
- Outils de configuration disponibles
- Liens vous redirigeant directement vers un article, si disponibles

Utilisez les graphiques et les descriptions pour sélectionner la topologie de connexion répondant à vos besoins. Le graphique présente les principales topologies de base, mais il est possible de créer des configurations plus complexes à l’aide des diagrammes.

## Connexions site à site et multi-sites

### De site à site

Une connexion par passerelle VPN site à site (S2S) est une connexion via un tunnel VPN IPsec/IKE (S2S ou IKEv1). Ce type de connexion requiert un périphérique VPN local auquel est affectée une IP publique, et qui ne se situe pas derrière un NAT. Les connexions S2S peuvent être utilisées pour les configurations hybrides et entre différents locaux.

![Connexion S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "site à site")


### Multi-sites

Vous pouvez créer et configurer une connexion par passerelle VPN entre votre réseau virtuel et plusieurs réseaux locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de VPN basé sur l’itinéraire (passerelle dynamique pour réseaux virtuels classiques). Un réseau virtuel ne pouvant disposer que d’une seule passerelle VPN, toutes les connexions passant par la passerelle partagent la bande passante disponible. Cela est souvent appelé connexion « multi-sites ».
 

![Connexion multi-sites](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-sites")

### Modèles et méthodes de déploiement pour les connexions site à site et multi-sites

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Connexion entre deux réseaux virtuels

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations de connexion multi-sites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels.

Les réseaux virtuels que vous connectez peuvent être situés :

- dans la même région ou dans des régions différentes
- dans le même abonnement ou dans des abonnements différents
- dans le même modèle de déploiement ou dans des modèles de déploiement différents


![Connexion de réseau virtuel à réseau virtuel](./media/vpn-gateway-about-vpngateways/demov2v.png "Connexion entre deux réseaux virtuels")

#### Connexions entre modèles de déploiement

Azure propose actuellement deux modèles de déploiement : le modèle classique et le modèle Resource Manager. Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles et des rôles d’instance Azure exécutés dans un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s’exécutent dans un réseau virtuel créé dans Resource Manager. Vous pouvez créer une connexion entre les réseaux virtuels pour permettre aux ressources dans un réseau virtuel de communiquer directement avec les ressources d’un autre réseau virtuel.

#### Homologation de réseaux virtuels

Vous pouvez utiliser l’homologation de réseau virtuel pour créer votre connexion, tant que votre réseau virtuel répond à certaines exigences. L’homologation de réseau virtuel n’utilise pas de passerelle de réseau virtuel. Pour plus d’informations, consultez l’article [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).


### Modèles et méthodes de déploiement pour les connexions de réseau virtuel à réseau virtuel

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## De point à site

Une connexion par passerelle VPN point à site (P2S) vous permet de créer individuellement une connexion sécurisée à un réseau virtuel à partir d’un ordinateur client. Le P2S est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Vous établissez la connexion VPN en la démarrant depuis l’ordinateur client. Cette solution est utile lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel. Les connexions P2S peuvent être utilisées conjointement avec des connexions S2S via la même passerelle VPN, dans la mesure où toutes les exigences de configuration des deux types de connexion sont compatibles.


![Connexion de point à site](./media/vpn-gateway-about-vpngateways/demop2s.png "point à site")

### Méthodes et modèles de déploiement pour les connexions point à site

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Dans une connexion ExpressRoute, une passerelle de réseau virtuel est configurée avec le type de passerelle « ExpressRoute » plutôt que « Vpn ». Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).


## Coexistence de connexions ExpressRoute et de site à site

ExpressRoute est une connexion directe et dédiée aux services Microsoft, notamment à Azure, à partir de votre WAN, qui ne passe pas par l’Internet public. Le trafic VPN de site à site transite via l’Internet public tout en étant chiffré. La possibilité de configurer des connexions VPN de site à site et ExpressRoute pour le même réseau virtuel présente plusieurs avantages.

Vous pouvez configurer un VPN de site à site comme un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de site à site pour vous connecter à des sites qui ne font pas partie de votre réseau, mais qui sont connectés via ExpressRoute. Notez que cela nécessite deux passerelles de réseau virtuel pour le même réseau virtuel, un utilisant le type de passerelle VPN et l’autre le type de passerelle ExpressRoute.


![Connexion de coexistence](./media/vpn-gateway-about-vpngateways/demoer.png "Expressroute-site à site")


### Méthodes et modèles de déploiement pour les connexions S2S et ExpressRoute

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Étapes suivantes

Planifiez votre configuration de passerelle VPN. Consultez la page [Planification et conception de la passerelle VPN](vpn-gateway-plan-design.md) et [Connecting your on-premises network to Azure (Connexion de votre réseau local à Azure)](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 

<!---HONumber=AcomDC_0928_2016-->