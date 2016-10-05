<properties
   pageTitle="Présentation du réseau virtuel (VNet) Azure"
   description="En savoir plus sur les réseaux virtuels dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# Présentation du réseau virtuel

Un réseau virtuel Azure (VNet) est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Vous pouvez contrôler complètement les blocs d’adresses IP, les paramètres DNS, les stratégies de sécurité et les tables de routage de ce réseau. Vous pouvez également segmenter votre réseau en plusieurs sous-réseaux et lancer des machines virtuelles IaaS Azure et/ou des [services Cloud (instances de rôle PaaS)](../cloud-services/cloud-services-choose-me.md). En outre, vous pouvez connecter le réseau virtuel à votre réseau local à l’aide des [options de connectivité](../vpn-gateway/vpn-gateway-cross-premises-options.md) disponibles dans Azure. En bref, vous pouvez développer votre réseau sur Azure et maîtriser totalement vos blocs d’adresses IP avec les que procurent la mise à l’échelle d’entreprise d’Azure.

Pour mieux comprendre les réseaux virtuels, examinez la figure ci-dessous, qui illustre un réseau local simplifié.

![Réseau local](./media/virtual-networks-overview/figure01.png)

La figure ci-dessus illustre un réseau local connecté à l’Internet public via un routeur. Vous pouvez également voir un pare-feu entre le routeur et une zone DMZ qui héberge un serveur DNS et une batterie de serveurs web. La batterie de serveurs web est équilibrée à l’aide d’un équilibreur de charge matériel qui est exposé à Internet et consomme des ressources à partir du sous-réseau interne. Le sous-réseau interne est séparé de la zone DMZ par un autre pare-feu et héberge les serveurs de contrôleur de domaine Active Directory, les serveurs de base de données et serveurs d’applications.

Le même réseau peut être hébergé dans Azure, comme illustré sur la figure ci-dessous.

![Azure Virtual Network](./media/virtual-networks-overview/figure02.png)

Notez comment l’infrastructure Azure prend le rôle de routeur, autorisant votre routeur de réseau virtuel à accéder à l’Internet public sans aucune configuration. Les pare-feu peuvent être remplacés par des groupes de sécurité réseau (NSG) appliqués à chaque sous-réseau individuel. Et les équilibreurs de charge physiques sont remplacés par des équilibreurs de charge internes et accessibles via internet dans Azure.

>[AZURE.NOTE] Il existe deux modes de déploiement dans Azure : classique (également appelé Service Management) et Azure Resource Manager (ARM). Les réseaux virtuels classiques peuvent être ajoutés à un groupe d’affinités ou créés en tant que réseau virtuel régional. Si vous avez un réseau virtuel dans un groupe d’affinités, il est recommandé de [migrer vers un réseau virtuel régional](virtual-networks-migrate-to-regional-vnet.md).

## Avantages du réseau virtuel

- **Isolement**. Les réseaux virtuels sont totalement isolés les uns des autres. Cela vous permet de créer des réseaux disjoints pour le développement, le test et la production qui utilisent les mêmes blocs d’adresses CIDR.

- **Accès à l’Internet public**. Toutes les machines virtuelles IaaS et les instances de rôle PaaS d’un réseau virtuel ont, par défaut, accès à l’Internet public. Vous pouvez contrôler l’accès grâce aux groupes de sécurité réseau (NSG).

- **Accès aux machines virtuelles dans le réseau virtuel**. Les instances de rôle PaaS et les machines virtuelles IaaS peuvent être démarrées dans le même réseau virtuel et se connecter entre elles avec des adresses IP privées, même si elles se trouvent dans des sous-réseaux différents, et ce, sans avoir recours à la configuration d’une passerelle ou sans utiliser d’adresse IP publique.

- **Résolution de noms**. Azure fournit la résolution de noms interne pour les machines virtuelles IaaS et les instances de rôle PaaS déployées dans votre réseau virtuel. Vous pouvez également déployer vos propres serveurs DNS et configurer le réseau virtuel pour les utiliser.

- **Sécurité**. Le trafic entrant et sortant des machines virtuelles et des instances de rôle PaaS dans un réseau virtuel peut être contrôlé à l’aide de groupes de sécurité du réseau.

- **Connectivité**. Les réseaux virtuels peuvent être connectés entre eux et même à votre centre de données local, en utilisant une connexion VPN de site à site ou une connexion ExpressRoute. Pour en savoir plus sur les passerelles VPN, Consultez [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

    >[AZURE.NOTE] Assurez-vous de créer un réseau virtuel avant de déployer des machines virtuelles IaaS ou des instances de rôle PaaS dans votre environnement Azure. Les machines virtuelles sur ARM requièrent un réseau virtuel et, si vous ne spécifiez pas de réseau virtuel existant, Azure crée un réseau virtuel par défaut dont le bloc d’adresses CIDR peut entrer en conflit avec votre réseau local. Ce qui empêche la connexion de votre réseau virtuel à votre réseau local.

## Sous-réseaux

Le sous-réseau est une plage d’adresses IP appartenant au réseau virtuel. Vous pouvez diviser un réseau virtuel en plusieurs sous-réseaux pour l’organisation et la sécurité. Les machines virtuelles et les instances de rôle PaaS déployées sur des sous-réseaux (identiques ou différents) au sein d’un réseau virtuel peuvent communiquer entre elles sans qu’il y ait besoin de configuration supplémentaire. Vous pouvez également configurer des tables d’itinéraire et des groupes de sécurité réseau sur un sous-réseau.

## Adresses IP


Il existe deux types d’adresses IP affectées aux ressources dans Azure : *publique* et *privée*. Les adresses IP publiques permettent aux ressources Azure de communiquer avec Internet et d’autres services publics Azure tels que [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Les adresses IP privées permettent la communication entre les ressources dans un réseau virtuel, au même titre que celles qui sont connectées via un VPN, sans utiliser des adresses IP routables par Internet.

Pour en savoir plus sur les adresses IP dans Azure, visitez [les adresses IP de réseau virtuel](virtual-network-ip-addresses-overview-arm.md)

## Équilibreurs de charge Azure

Les machines virtuelles et les services cloud au sein d’un réseau virtuel peuvent être exposés à Internet par le biais d’équilibreurs de charge Azure. Des applications métiers présentées en interne ne peuvent être équilibrées qu’à l’aide de l’équilibreur de charge interne.

- **Équilibreur de charge externe**. Vous pouvez utiliser un équilibreur de charge externe pour fournir une haute disponibilité pour les machines virtuelles IaaS et les instances de rôle PaaS accessibles depuis l’Internet public.

- **Équilibreur de charge interne**. Vous pouvez utiliser un équilibreur de charge interne pour fournir une haute disponibilité pour les machines virtuelles IaaS et les instances de rôle PaaS accessibles depuis d’autres services sur votre réseau virtuel.

Pour en savoir plus sur l’équilibrage de charge dans Azure, visitez [Présentation de l’équilibreur de charge](../load-balancer/load-balancer-overview.md).

## Groupe de sécurité réseau

Vous pouvez créer des NSG afin de contrôler l’accès entrant et sortant aux interfaces réseau (cartes réseau), aux machines virtuelles et aux sous-réseaux. Chaque NSG contient une ou plusieurs règles spécifiant si le trafic est accepté ou refusé en fonction de l’adresse IP source, du port source, de l’adresse IP de destination et du port de destination. Pour en savoir plus sur les groupes de sécurité réseau, consultez [Qu’est-ce qu’un groupe de sécurité réseau ?](virtual-networks-nsg.md).

## Appliances virtuelles

Une appliance virtuelle est simplement une autre machine virtuelle dans votre réseau virtuel qui exécute une fonction d’appliance logicielle, un pare-feu par exemple, l’optimisation du réseau étendu ou la détection d’intrusion. Vous pouvez créer un itinéraire dans Azure pour acheminer le trafic de votre réseau virtuel via une appliance virtuelle pour utiliser ses fonctionnalités.

Par exemple, les NSG peuvent être utilisés pour assurer la sécurité sur votre réseau virtuel. Toutefois, les NSG fournissent la liste de contrôle d’accès de couche 4 pour les paquets entrants et sortants. Si vous souhaitez utiliser un modèle de sécurité de couche 7, vous devez utiliser une appliance de pare-feu.

Les appliances virtuelles dépendent des [itinéraires définis par l’utilisateur et du transfert d’IP](virtual-networks-udr-overview.md).

## Limites
Il existe des limites pour le nombre de réseaux virtuels autorisés dans un abonnement, veuillez vous reporter à la section [Limites de mise en réseau Azure](../azure-subscription-service-limits.md#networking-limits) pour plus d’informations.

## Tarification
L’utilisation de réseaux virtuels dans Azure ne génère pas de frais supplémentaires. Les instances de calcul lancées dans le réseau virtuel seront facturées à un tarif standard, comme l’indique la section [Tarification liée aux machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/). Les [passerelles VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) et les [adresses IP publiques](https://azure.microsoft.com/pricing/details/ip-addresses/) utilisées dans le réseau virtuel peuvent également être facturés au tarif standard.

## Étapes suivantes

- [Créer un réseau virtuel](virtual-networks-create-vnet-arm-pportal.md) et des sous-réseaux.
- [Créer une machine virtuelle dans un réseau virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- En savoir plus sur les [groupes de sécurité réseau](virtual-networks-nsg.md).
- En savoir plus sur les [Itinéraires définis par l’utilisateur et le transfert IP](virtual-networks-udr-overview.md).

<!-----HONumber=AcomDC_0810_2016-->