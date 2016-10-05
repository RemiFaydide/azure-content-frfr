<properties 
	pageTitle="Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium | Microsoft Azure" 
	description="Découvrez comment créer et gérer la prise en charge de réseau virtuel pour vos instances de Cache Redis Azure de niveau Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>

# Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium
Le Cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de cache en toute flexibilité, y compris le nouveau niveau Premium.

Le niveau Premium de Cache Redis Azure comprend des fonctionnalités comme clustering, la persistance et la prise en charge de réseau virtuel (VNet). Un réseau VNet est un réseau privé dans le cloud. Lorsqu’une instance de Cache Redis Azure est configurée avec un réseau virtuel, elle n’est pas adressable publiquement et est accessible uniquement à partir de machines virtuelles et d’applications sur le réseau virtuel. Cet article décrit comment configurer la prise en charge de réseau virtuel pour une instance Premium de Cache Redis Azure.

>[AZURE.NOTE] Le Cache Redis Azure prend en charge les réseaux virtuels classiques et ARM.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, consultez [Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md).

## Pourquoi un réseau virtuel ?
Le déploiement de [réseau virtuel Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) fournit une sécurité et un isolement renforcés pour votre Cache Redis Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès au Cache Redis Azure.

## Prise en charge des réseaux virtuels
La configuration de la prise en charge du réseau virtuel (VNet) s’effectue dans le panneau **Nouveau cache Redis** lors de la création du cache.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Une fois que vous avez sélectionné le niveau tarifaire Premium, vous pouvez configurer l’intégration du réseau virtuel du Cache Redis Azure en sélectionnant un réseau virtuel situé dans le même abonnement et le même emplacement que votre cache. Pour utiliser un nouveau réseau virtuel, vous devez tout d’abord le créer en suivant les étapes indiquées dans [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [Créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Revenez ensuite au panneau **Nouveau cache Redis** pour créer et configurer votre cache Premium.

Pour configurer le réseau virtuel pour votre nouveau cache, cliquez sur **Réseau virtuel** dans le panneau **Nouveau cache Redis**, puis sélectionnez le réseau virtuel souhaité dans la liste déroulante.

![Réseau virtuel][redis-cache-vnet]

Sélectionnez le sous-réseau souhaité dans la liste déroulante **Sous-réseau**, puis spécifiez l’**adresse IP statique** souhaitée. Si vous utilisez un réseau virtuel classique, le champ **Adresse IP statique** est facultatif. Si aucune adresse IP statique n’est spécifiée, une option sera choisie dans le sous-réseau sélectionné.

>[AZURE.IMPORTANT] Lorsque vous déployez un Cache Redis Azure sur un réseau virtuel ARM, le cache doit se trouver dans un sous-réseau dédié qui ne contient aucune autre ressource à l’exception des instances du Cache Redis Azure. Si vous essayez de déployer un Cache Redis Azure sur un réseau virtuel ARM dans un sous-réseau qui contient d’autres ressources, le déploiement échoue.

![Réseau virtuel][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Les quatre premières adresses dans un sous-réseau sont réservées et ne peuvent pas être utilisées. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Une fois le cache créé, vous pouvez afficher la configuration du réseau virtuel en cliquant sur **Réseau virtuel** dans le panneau **Paramètres**.

![Réseau virtuel][redis-cache-vnet-info]


Pour vous connecter à votre instance de Cache Redis Azure lorsque vous utilisez un réseau virtuel, vous devez spécifier le nom d’hôte de votre cache dans la chaîne de connexion, comme indiqué dans l’exemple suivant.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Forum aux questions sur le réseau virtuel de Cache Redis Azure

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du Cache Redis Azure.

-	[Quels sont les problèmes de configuration les plus courants au niveau du Cache Redis Azure et des réseaux virtuels ?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-	[Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-	[Pourquoi la création d’un cache Redis échoue-t-elle dans certains sous-réseaux mais pas d’autres ?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-	[Toutes les fonctionnalités fonctionnent-elles lorsque vous hébergez un cache dans un réseau virtuel ?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## Quels sont les problèmes de configuration les plus courants au niveau du Cache Redis Azure et des réseaux virtuels ?

Lorsque le Cache Redis Azure est hébergé dans un réseau virtuel, les ports du tableau suivant sont utilisés. Si ces ports sont bloqués, le cache risque de ne pas fonctionner correctement. Le blocage d’un ou plusieurs de ces ports constitue le problème de configuration le plus courant lorsque vous utilisez le Cache Redis Azure dans un réseau virtuel.

| Port(s) | Direction | Protocole de transfert | Objectif | Adresse IP distante |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | Règle de trafic sortant | TCP | Dépendances Redis sur Azure Storage/l’infrastructure à clé publique (Internet) | * |
| 53 | Règle de trafic sortant | TCP/UDP | Dépendances Redis sur le DNS (Internet/réseau virtuel) | * |
| 6379, 6380 | Trafic entrant | TCP | Communication client avec Redis, équilibrage de charge Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | Entrant/sortant | TCP | Détail d’implémentation de Redis | VIRTUAL\_NETWORK |
| 8500 | Trafic entrant | TCP/UDP | Équilibrage de charge Azure | AZURE\_LOADBALANCER |
| 10221-10231 | Entrant/sortant | TCP | Détail d’implémentation de Redis (peut restreindre le point de terminaison distant à VIRTUAL\_NETWORK) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | Trafic entrant | TCP | Communication client avec les clusters Redis, équilibrage de charge Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | Trafic entrant | TCP | Communication client avec les clusters Redis, équilibrage de charge Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | Trafic entrant | TCP/UDP | Équilibrage de charge Azure | AZURE\_LOADBALANCER |
| 20226 | Entrant+sortant | TCP | Détail d’implémentation des clusters Redis | VIRTUAL\_NETWORK |


Il existe des exigences de connectivité réseau pour le Cache Redis Azure qui peuvent ne pas être initialement satisfaites dans un réseau virtuel. Le Cache Redis Azure nécessite tous les éléments suivants pour fonctionner correctement lorsqu’il est utilisé dans un réseau virtuel.

-  Connectivité réseau sortante à des points de terminaison Azure Storage dans le monde entier. Cela inclut les points de terminaison situés dans la même région que le cache Redis Azure, ainsi que les points de terminaison de stockage situés dans d’**autres** régions Azure. Les points de terminaison Azure Storage se résolvent dans les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* et *file.core.windows.net*.
-  Connectivité réseau sortante à *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*. Cette connectivité est nécessaire pour prendre en charge la fonctionnalité SSL.
-  La configuration DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et les domaines mentionnés dans les points précédents. La configuration DNS requise peut être satisfaite en garantissant qu'une infrastructure DNS valide est configurée et gérée pour le réseau virtuel.



### Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?

Vous ne pouvez utiliser des réseaux virtuels qu’avec les caches de niveau Premium.

### Pourquoi la création d’un cache Redis échoue-t-elle dans certains sous-réseaux mais pas d’autres ?

Si vous déployez un Cache Redis Azure sur un réseau virtuel ARM, le cache doit se trouver dans un sous-réseau dédié qui ne contient aucune autre type de ressource. Si vous essayez de déployer un Cache Redis Azure sur un sous-réseau de réseau virtuel ARM qui contient d’autres ressources, le déploiement échoue. Vous devez supprimer les ressources existantes dans le sous-réseau avant de pouvoir créer un nouveau cache Redis.

Vous pouvez déployer plusieurs types de ressources sur un réseau virtuel classique, à condition de disposer de suffisamment d’adresses IP disponibles.

### Toutes les fonctionnalités fonctionnent-elles lorsque vous hébergez un cache dans un réseau virtuel ?

Quand votre cache fait partie d’un réseau virtuel, seuls les clients de ce réseau virtuel peuvent accéder au cache. Par conséquent, les fonctionnalités de gestion de cache suivantes ne fonctionnent pas pour l’instant.

-	Console Redis - Étant donné que la Console Redis utilise le client redis-cli.exe hébergé sur des machines virtuelles qui ne font pas partie de votre réseau virtuel, il ne peut pas se connecter à votre cache.


## Utiliser ExpressRoute avec le Cache Redis Azure

Les clients peuvent connecter un circuit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à leur infrastructure de réseau virtuel pour étendre leur réseau local à Azure.

Par défaut, un circuit ExpressRoute nouvellement créé publie un itinéraire par défaut qui autorise la connectivité Internet sortante. Avec cette configuration, les applications clientes sont en mesure de se connecter à d’autres points de terminaison Azure, notamment le Cache Redis Azure.

Toutefois, une configuration client courante consiste à définir un itinéraire par défaut (0.0.0.0/0), ce qui force le trafic Internet sortant à évoluer sur site. Ce flux de trafic interrompt la connectivité avec le Cache Redis Azure, car le trafic sortant est soit bloqué sur site, soit fait l’objet d’une opération NAT sur un jeu d’adresses non reconnaissable qui ne fonctionne plus avec différents points de terminaison Azure.

La solution consiste à définir un (ou plusieurs) itinéraires définis par l’utilisateur (UDR) sur le sous-réseau qui contient le Cache Redis Azure. Un itinéraire défini par l'utilisateur définit des itinéraires spécifiques au sous-réseau qui seront respectés au lieu de l'itinéraire par défaut.

Si possible, il est recommandé d'utiliser la configuration suivante :

- La configuration ExpressRoute annonce 0.0.0.0/0 et par défaut, tunnelise de force tout le trafic sortant sur site.
- L’UDR (itinéraire défini par l’utilisateur) appliqué au sous-réseau contenant le Cache Redis Azure définit 0.0.0.0/0 avec Internet de type saut suivant (un exemple de ceci est présenté plus bas dans cet article).

L’effet combiné de ces étapes est que l’UDR de niveau sous-réseau a la priorité sur le tunneling forcé ExpressRoute, garantissant ainsi un accès Internet sortant à partir du Cache Redis Azure.

Bien que la connexion à une instance de Cache Redis Azure à partir d’une application sur site à l’aide d’ExpressRoute ne constitue pas un scénario d’utilisation classique pour des raisons de performances (pour des performances optimales, les clients de Cache Redis Azure doivent se trouver dans la même région que le Cache Redis Azure), dans ce scénario, le chemin d’accès réseau sortant ne peut pas transiter via des serveurs proxy d’entreprise internes, ni faire l’objet d’un tunneling forcé sur des serveurs locaux. Ceci modifie l’adresse NAT réelle du trafic réseau sortant à partir du Cache Redis Azure. La modification de l’adresse NAT du trafic réseau sortant d’une instance de Cache Redis Azure entraîne des échecs de connectivité vers plusieurs des points de terminaison répertoriés ci-dessus. Cela entraîne l’échec des tentatives de création du Cache Redis Azure.

**IMPORTANT :** les itinéraires définis dans un UDR **doivent** être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L'exemple suivant utilise la plage d'adresses 0.0.0.0/0 étendue qui peut potentiellement être remplacée accidentellement par les annonces de routage à l'aide de plages d'adresses plus spécifiques.

**TRÈS IMPORTANT :** le cache Redis Azure n’est pas pris en charge avec les configurations ExpressRoute qui **publient incorrectement de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée**. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les annonces de routage depuis Microsoft pour un grand ensemble de plages d'adresses IP Microsoft Azure. Si ces plages d’adresses sont incorrectement publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’instance de Cache Redis Azure sont incorrectement acheminés de force vers l’infrastructure réseau sur site d’un client. Ce flux réseau interrompt le Cache Redis Azure. La solution à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d'accès d'homologation publique vers le chemin d'accès d'homologation privée.

Vous trouverez des informations générales sur les itinéraires définis par l'utilisateur dans cette [présentation](../virtual-network/virtual-networks-udr-overview.md).

Pour plus d'informations sur ExpressRoute, consultez la rubrique [ExpressRoute - Aperçu technique](../expressroute/expressroute-introduction.md)

## Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

-	[Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0921_2016-->