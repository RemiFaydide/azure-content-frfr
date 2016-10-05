<properties 
   pageTitle="FAQ sur la passerelle VPN de Virtual Network | Microsoft Azure"
   description="Le FAQ sur la passerelle VPN FAQ sur les connexions entre différents locaux de Microsoft Azure Virtual Network, les connexions de configuration hybride et les passerelles VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# FAQ sur la passerelle VPN

## Connexion à des réseaux virtuels

### Puis-je me connecter à des réseaux virtuels dans différentes régions Azure ?
Oui. En fait, il n'existe aucune contrainte de région. Un réseau virtuel peut se connecter à un autre réseau virtuel dans la même région ou dans une autre région Azure.

### Puis-je me connecter à des réseaux virtuels avec différents abonnements ?
Oui.

### Puis-je me connecter à plusieurs sites à partir d'un seul réseau virtuel ?

Vous pouvez vous connecter à plusieurs sites à l'aide de Windows PowerShell et des API REST Azure. Consultez la rubrique FAQ sur la[Connectivité multisite et de réseau virtuel à réseau virtuel](#multi-site-and-vnet-to-vnet-connectivity).
## Quelles sont mes options de connexion entre différents locaux ?

Les connexions intersites suivantes sont prises en charge :

- [Site à Site](vpn-gateway-site-to-site-create.md) : connexion VPN sur IPsec (IKE v1 et IKE v2). Ce type de connexion requiert un périphérique VPN ou le service RRAS.

- [Point à site](vpn-gateway-point-to-site-create.md) : connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Cette connexion ne nécessite pas un périphérique VPN.

- [Réseau virtuel à réseau virtuel](virtual-networks-configure-vnet-to-vnet-connection.md) : ce type de connexion est identique à une configuration site à site. La connexion de réseau virtuel à réseau virtuel est une connexion VPN sur IPsec (IKE v1 et IKE v2). Cette connexion ne nécessite pas un périphérique VPN.

- [Multisite](vpn-gateway-multi-site.md) : il s’agit d’une variante d’une configuration site à site qui vous permet de connecter plusieurs sites locaux à un réseau virtuel.

- [ExpressRoute](../expressroute/expressroute-introduction.md) : ExpressRoute est une connexion directe à Azure à partir de votre WAN, qui ne passe pas par l’Internet public. Pour plus d'informations, consultez[ Vue d’ensemble technique d’ExpressRoute](../expressroute/expressroute-introduction.md) et le [FAQ ExpressRoute](../expressroute/expressroute-faqs.md).

Pour plus d’informations sur les connexions, consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).

### Quelle est la différence entre une connexion site à site et point à site ?

Les connexions de **site à site** vous permettent de vous connecter à partir de n’importe quel ordinateur se trouvant sur votre serveur local vers une machine virtuelle ou une instance de rôle au sein de votre réseau virtuel, selon le mode de configuration du routage que vous choisissez. Il s’agit d’une excellente solution pour une connexion entre différents locaux toujours disponibles et elle convient parfaitement aux configurations hybrides. Ce type de connexion s'appuie sur une appliance VPN IPsec (matériel ou application logicielle), qui doit être déployée à la périphérie de votre réseau. Pour créer ce type de connexion, vous devrez disposer du matériel VPN nécessaire et d’une adresse IPv4 externe.

Les connexions de **point à site** vous permettent de vous connecter à partir d’un seul ordinateur depuis n’importe où vers n’importe quel emplacement sur votre réseau virtuel. Ces connexions utilisent le client VPN fourni avec Windows. Dans le cadre de la configuration point à site, vous installez un certificat et un package de configuration client VPN qui contient les paramètres permettant à votre ordinateur de se connecter à une machine virtuelle ou à une instance de rôle au sein du réseau virtuel. Cela est très intéressant lorsque vous souhaitez vous connecter à un réseau virtuel mais que vous ne vous trouvez pas en local. Il s’agit également d’une solution intéressante lorsque vous n’avez pas accès au matériel VPN ou à une adresse IPv4 externe, tous deux étant nécessaires à une connexion site à site.

Vous pouvez configurer votre réseau virtuel pour utiliser la connexion site à site et point à site simultanément, à condition de créer votre connexion site à site à l’aide d’un type de réseau privé virtuel basé sur un itinéraire pour votre passerelle. Les types de réseau privé virtuel basé sur un itinéraire sont appelés des passerelles dynamiques dans le modèle de déploiement classique.

### Présentation d’ExpressRoute

ExpressRoute vous permet de créer des connexions privées entre les centres de données Microsoft et une infrastructure locale ou un environnement de colocalisation. Grâce à ExpressRoute, vous pouvez établir des connexions aux services cloud Microsoft tels que Microsoft Azure et Office 365 à un emplacement de colocalisation partenaire ExpressRoute ou vous connecter directement à Azure depuis votre réseau étendu existant (comme un VPN MPLS offert par un fournisseur de services réseau).

Les connexions ExpressRoute offrent une meilleure sécurité, une plus grande fiabilité, une bande passante plus élevée et des latences moindres par rapport aux connexions classiques sur Internet. Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre des réseaux locaux et Azure peut également avoir des avantages significatifs en matière de coûts. Si vous avez déjà créé une connexion entre les locaux de votre réseau local vers Azure, vous pouvez migrer vers une connexion ExpressRoute tout en conservant votre réseau virtuel tel qu’il est.

Pour plus d’informations, consultez le [Forum Aux Questions sur ExpressRoute](../expressroute/expressroute-faqs.md).

## Connexions de site à site et périphériques VPN

### Que dois-je prendre en compte lors de la sélection d'un périphérique VPN ?

Nous avons validé un ensemble de périphériques VPN site à site standard en partenariat avec des fournisseurs de périphériques. Vous trouverez une liste de périphériques VPN compatibles, leurs instructions de configuration correspondantes ainsi que les caractéristiques du périphérique [ici](vpn-gateway-about-vpn-devices.md). Tous les périphériques des familles de périphériques répertoriées et reconnus comme compatibles doivent fonctionner avec Virtual Network. Pour aider à configurer votre périphérique VPN, reportez-vous à l'exemple de configuration de périphérique ou au lien qui correspond à la famille de périphériques appropriée.

### Que faire si je dispose d'un périphérique VPN qui ne figure pas dans la liste de périphériques compatibles connus ?

Si votre périphérique n’est pas répertorié comme un périphérique VPN compatible connu et que vous souhaitez l’utiliser pour votre connexion VPN, vous devez vérifier qu’il offre les paramètres et les options de configuration IPsec/IKE pris en charge [ici](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Les périphériques qui répondent à la configuration requise minimale sont censés fonctionner avec les passerelles VPN. Contactez le fabricant de votre périphérique pour obtenir une prise en charge et des instructions de configuration supplémentaires.

### Pourquoi mon tunnel VPN basé sur une stratégie tombe-t-il en panne lorsque le trafic est inactif ?

Il s’agit du comportement attendu pour les passerelles VPN basées sur une stratégie (également appelé routage statique). Lorsque le trafic via le tunnel est inactif pendant plus de 5 minutes, le tunnel est détruit. Lorsque le trafic recommence à circuler dans les deux sens, le tunnel est immédiatement rétabli.

### Puis-je utiliser le logiciel des VPN logiciels pour me connecter à Azure ?

Nous prenons en charge les serveurs de routage et d’accès distant (RRAS) Windows Server 2012 pour la configuration site à site entre différents locaux.

D’autres solutions VPN logicielles fonctionnent avec notre passerelle tant qu'elles sont conformes aux implémentations IPsec standard. Contactez le fournisseur du logiciel pour obtenir des instructions de configuration et de prise en charge.

## Connexions de point à site

### Quels systèmes d’exploitation puis-je utiliser avec une connexion point à site ?

Les systèmes d’exploitation pris en charge sont les suivants :

- Windows 7 (32 bits et 64 bits)

- Windows Server 2008 R2 (64 bits uniquement)

- Windows 8 (32 bits et 64 bits)

- Windows 8.1 (32 bits et 64 bits)

- Windows Server 2012 (64 bits uniquement)

- Windows Server 2012 R2 (64 bits uniquement)

- Windows 10

### Puis-je utiliser un client VPN logiciel pour une connexion point à site qui prend en charge le protocole SSTP ?

Non. La prise en charge est limitée aux versions de système d'exploitation Windows répertoriées ci-dessus.

### Combien de points de terminaison clients VPN puis-je avoir dans ma configuration point à site ?

Nous prenons en charge jusqu'à 128 clients VPN pouvant se connecter à un réseau virtuel en même temps.

### Puis-je utiliser ma propre AC racine PKI interne pour une connectivité point à site ?

Oui. Auparavant, seuls les certificats racines auto-signés pouvaient être utilisés. Vous pouvez toujours charger 20 certificats racine.

### Puis-je parcourir les serveurs proxy et les pare-feu à l’aide de la fonctionnalité point à site ?

Oui. Nous utilisons SSTP (Secure Socket Tunneling Protocol) pour avoir un tunnel traversant les pare-feu. Ce tunnel apparaît comme une connexion HTTPS.

### Si je redémarre un ordinateur client avec une configuration point à site, le réseau VPN va-t-il se reconnecter automatiquement ?

Par défaut, l'ordinateur client ne rétablit pas automatiquement la connexion VPN.

### La configuration point à site prend-elle en charge la reconnexion automatique et DDNS sur les clients VPN ?

La reconnexion automatique et DDNS ne sont actuellement pas pris en charge dans les configurations VPN point à site.

### Puis-je avoir des configurations coexistantes site à site et point à site pour un même réseau virtuel ?

Oui. Ces deux solutions fonctionnent si vous avez un type de réseau VPN basé sur un itinéraire pour votre passerelle. Pour le modèle de déploiement Classic, vous avez besoin d’une passerelle dynamique. Nous ne prenons pas en charge la configuration point à site pour les passerelles VPN à routage statique ou les passerelles utilisant -VpnType PolicyBased.

### Puis-je configurer un client point à site pour me connecter à plusieurs réseaux virtuels en même temps ?

Oui, vous pouvez. Néanmoins les réseaux virtuels ne peuvent pas avoir de préfixes IP qui se chevauchent, et les espaces d’adressage point à site ne doivent pas se chevaucher entre les réseaux virtuels.

### Quel débit puis-je attendre des connexions site à site ou point à site ?

Il est difficile de maintenir le débit exact des tunnels VPN. IPsec et SSTP sont des protocoles VPN de chiffrement lourd. Le débit est également limité par la latence et la bande passante entre vos locaux et Internet.

## Passerelles

### Qu’est-ce qu’une passerelle basée sur une stratégie (routage statique) ?

Les passerelles basées sur des stratégies implémentent des VPN basés sur des stratégies. Les VPN basés sur des stratégies chiffrent et acheminent les paquets via les tunnels IPsec basés sur des combinaisons de préfixes d’adresses entre votre réseau local et le réseau virtuel Azure. La stratégie (ou le sélecteur de trafic) est généralement définie en tant que liste d'accès dans les configurations VPN.

### Qu’est-ce qu’une passerelle basée sur l’itinéraire (routage dynamique) ?

Les passerelles basées sur des itinéraires implémentent les VPN basés sur des itinéraires. Les VPN basés sur l'itinéraire utilisent des « itinéraires » dans l'adresse IP de transfert ou la table de routage pour acheminer des paquets dans leurs interfaces de tunnel correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie ou le sélecteur de trafic pour les VPN basés sur l'itinéraire sont configurés comme universels (ou en caractères génériques).

### Puis-je obtenir mon adresse IP de passerelle VPN avant de la créer ?

Non. Vous devez d’abord créer votre passerelle pour obtenir l'adresse IP. L’adresse IP change si vous supprimez et recréez votre passerelle VPN.

### Comment mon tunnel VPN est-il authentifié ?

Azure VPN utilise l'authentification PSK (clé prépartagée). Nous générons une clé prépartagée (PSK) lorsque nous créons le tunnel VPN. Vous pouvez modifier la PSK générée automatiquement pour votre VPN avec la cmdlet PowerShell ou l’API REST de clé prépartagée définie.

### Puis-je utiliser l’API de clé prépartagée définie pour configurer ma passerelle VPN basée sur des itinéraires (routage statique) ?

Oui, l’applet de commande PowerShell et l’API de clé prépartagée définie permettent de configurer des réseaux VPN Azure basés sur des stratégies (statiques) et des réseaux VPN basés sur des itinéraires (dynamiques).

### Puis-je utiliser les autres options d'authentification ?

Nous sommes limités à l'utilisation de clés prépartagées (PSK) pour l'authentification.

### Qu’est-ce que le « sous-réseau de passerelle » et pourquoi est-il nécessaire ?

Nous avons un service de passerelle que nous exécutons pour activer la connectivité entre différents locaux.

Vous devez créer un sous-réseau de passerelle pour votre réseau virtuel afin de configurer une passerelle VPN. Tous les sous-réseaux de passerelle doivent être nommés GatewaySubnet afin de fonctionner correctement. N’attribuez pas un autre nom au sous-réseau de passerelle, et ne déployez pas de machines virtuelles ou d’autres éléments vers le sous-réseau de passerelle.

La taille minimale du sous-réseau de passerelle dépend entièrement de la configuration que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29 pour certaines configurations, nous vous recommandons de créer un sous-réseau de passerelle de /28 ou plus (/28, /27, /26, etc.).

### Puis-je déployer des machines virtuelles ou des instances de rôle vers mon sous-réseau de passerelle ?

Non.

### Comment puis-je indiquer quel trafic traverse la passerelle VPN ?

Si vous utilisez le portail Azure Classic, ajoutez chaque plage que vous souhaitez envoyer par le biais de la passerelle pour votre réseau virtuel dans la page Réseaux sous Réseaux locaux.

### Puis-je configurer un tunneling forcé ?

Oui. Consultez [Configurer un tunneling forcé](vpn-gateway-about-forced-tunneling.md).

### Puis-je configurer mon propre serveur VPN dans Azure et l’utiliser pour me connecter à mon réseau local ?

Oui, vous pouvez déployer vos propres serveurs ou passerelles VPN dans Azure depuis Azure Marketplace ou en créant vos propres routeurs VPN. Vous devez configurer des itinéraires définis par l’utilisateur dans votre réseau virtuel pour vous assurer que le trafic est acheminé correctement entre vos réseaux locaux et les sous-réseaux de votre réseau virtuel.

### Pourquoi certains ports sont-ils ouverts sur ma passerelle VPN ?

Ils sont nécessaires pour la communication avec l’infrastructure Azure. Ils sont protégés (verrouillés) par des certificats Azure. Sans les certificats appropriés, les entités externes (notamment les clients de ces passerelles) ne pourront avoir aucun effet sur ces points de terminaison.

Une passerelle VPN est, fondamentalement, un périphérique multirésident avec une carte réseau qui exploite le réseau privé du client et une autre carte réseau connectée au réseau public. Les entités de l’infrastructure Azure ne peuvent pas se connecter aux réseaux privés des clients pour des raisons de conformité. Elles doivent donc utiliser des points de terminaison publics pour la communication avec l’infrastructure. Les points de terminaison publics sont analysés régulièrement par l’audit de sécurité Azure.


### Plus d'informations sur les types de passerelle, la configuration requise et le débit

Pour plus d’informations , voir [À propos des paramètres de la passerelle VPN] (vpn-gateway-about-vpn gateway-settings.md).

## Connectivité multisite et de réseau virtuel à réseau virtuel

### Quel type de passerelles peut prendre en charge une connectivité multisite et de réseau virtuel à réseau virtuel ?

Uniquement des réseaux VPN basés sur des itinéraires (routage dynamique).

### Puis-je connecter un réseau virtuel avec un type de réseau VPN basé sur des itinéraires à un autre réseau virtuel avec un type de réseau VPN basé sur des stratégies ?

Non, les deux réseaux virtuels DOIVENT utiliser des réseaux VPN basés sur des itinéraires (routage dynamique).

### Le trafic de réseau virtuel à réseau virtuel est-il sécurisé ?

Oui, il est protégé par le chiffrement IPsec/IKE.

### Le trafic de réseau virtuel à réseau virtuel circule-t-il sur la dorsale principale d'Azure ?

Oui.

### À combien de sites locaux et de réseaux virtuels peut se connecter un réseau virtuel ?

Bande passante 10 combinés pour les passerelles de routage dynamique de base et standard ; 30 pour les passerelles VPN de performances élevées.

### Puis-je utiliser des réseaux VPN point à site avec mon réseau virtuel comportant plusieurs tunnels VPN ?

Oui, les réseaux VPN point à site peuvent être utilisés avec les passerelles VPN se connectant à plusieurs sites locaux et à d’autres réseaux virtuels.

### Puis-je configurer plusieurs tunnels entre mon réseau virtuel et mon site local à l'aide d’un VPN multisite ?

Non, les tunnels redondants entre un réseau virtuel Azure et un site local ne sont pas pris en charge.

### Des espaces d'adressage peuvent-ils se chevaucher entre les réseaux virtuels connectés et les sites locaux ?

Non. Le chevauchement des espaces d’adressage entraîne le téléchargement du fichier de configuration réseau ou l’échec de la création d’un réseau virtuel.

### Puis-je obtenir plus de bande passante avec un plus grand nombre de réseaux VPN site à site que si j’utilise un seul réseau virtuel ?

Non, tous les tunnels VPN, y compris les VPN point à site, partagent la même passerelle VPN Azure et la bande passante disponible.

### Puis-je utiliser une passerelle VPN Azure pour faire transiter le trafic entre mes sites locaux ou vers un autre réseau virtuel ?

**Modèle de déploiement Classic**<br> Le transit du trafic via la passerelle VPN Azure est possible à l’aide du modèle de déploiement Classic, mais il s’appuie sur les espaces d’adressage définis de manière statique dans le fichier de configuration réseau. BGP n’est pas encore pris en charge avec les réseaux virtuels Azure et les passerelles VPN à l’aide du modèle de déploiement Classic. Sans BGP, la définition manuelle des espaces d’adressage de transit peut entraîner des erreurs et n’est pas recommandée.<br> **Modèle de déploiement Resource Manager**<br> Si vous utilisez le modèle de déploiement Resource Manager, consultez la section [BGP](#bgp) pour plus d’informations.

### Azure génère-t-il la même clé prépartagée IPsec/IKE pour toutes les connexions VPN d'un même réseau virtuel ?

Non, Azure génère par défaut différentes clés prépartagées pour différentes connexions VPN. Toutefois, vous pouvez utiliser l’API REST de clé de passerelle VPN définie ou la cmdlet PowerShell pour définir la valeur clé de votre choix. La clé DOIT être une chaîne alphanumérique d'une longueur comprise entre 1 et 128 caractères.

### Le trafic entre les réseaux virtuels est-il facturé par Azure ?

Pour le trafic entre différents réseaux virtuels Azure, seul le trafic passant d'une région Azure à une autre est facturé par Azure. Le tarif facturé est répertorié dans la page de [Tarification de la passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) Azure.


### Puis-je me connecter à un réseau virtuel avec les VPN IPsec sur mon circuit ExpressRoute ?

Oui, cette méthode est prise en charge. Pour plus d’informations, consultez [Configurer des connexions ExpressRoute et VPN de site à site pour qu’elles coexistent pour un réseau virtuel](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]



## Connectivité entre locaux et machines virtuelles

### Si ma machine virtuelle est dans un réseau virtuel et que j'ai une connexion entre différents locaux, comment dois-je me connecter à la machine virtuelle ?

Vous disposez de plusieurs options. Si vous avez activé le protocole RDP et que vous avez créé un point de terminaison, vous pouvez vous connecter à votre machine virtuelle à l'aide de l'adresse IP virtuelle. Dans ce cas, vous allez spécifier l'adresse IP virtuelle et le port auquel vous souhaitez vous connecter. Vous devez configurer le port sur votre machine virtuelle pour le trafic. En règle générale, vous accédez au portail Azure Classic et vous enregistrez les paramètres de la connexion RDP sur votre ordinateur. Les paramètres contiennent les informations de connexion nécessaires.

Si vous avez un réseau virtuel avec une connectivité entre différents locaux configurée, vous pouvez vous connecter à votre machine virtuelle à l'aide de l'adresse DIP interne ou de l’adresse IP privée. Vous pouvez également vous connecter à votre machine virtuelle en DIP interne à partir d'une autre machine virtuelle qui se trouve sur le même réseau virtuel. Vous ne pouvez pas vous connecter en RDP à votre machine virtuelle à l'aide d’un DIP si vous vous connectez à partir d'un emplacement en dehors de votre réseau virtuel. Par exemple, si vous avez configuré un réseau virtuel point à site et que vous n’établissez pas de connexion à partir de votre ordinateur, vous ne pouvez pas vous connecter à la machine virtuelle en DIP.

### Si ma machine virtuelle est dans un réseau virtuel avec connectivité entre différents locaux, tout le trafic de ma machine virtuelle passe-t-il par cette connexion ?

Non. Seul le trafic qui possède une IP de destination contenue dans les plages d'adresse IP du réseau local du réseau virtuel que vous avez spécifié passera par la passerelle de réseau virtuel. Le trafic avec une IP de destination qui se trouve dans le réseau virtuel reste dans le réseau virtuel. Tout autre trafic est envoyé par le biais de l'équilibreur de charge aux réseaux publics, ou si le tunneling forcé est utilisé, il est envoyé via la passerelle VPN Azure. Si vous résolvez un problème, il est important de s'assurer que vous disposez de toutes les plages répertoriées dans votre réseau local et que vous souhaitez envoyer via la passerelle. Vérifiez que les plages d'adresses du réseau local ne se chevauchent pas avec les plages d'adresses du réseau virtuel. Par ailleurs, vérifiez que le serveur DNS que vous utilisez résout le nom à l’adresse IP appropriée.


## FAQ Virtual Network

Des informations supplémentaires sur les réseaux virtuels sont disponibles dans le [Forum Aux Questions sur les réseaux virtuels](../virtual-network/virtual-networks-faq.md).
 

<!---HONumber=AcomDC_0928_2016-->