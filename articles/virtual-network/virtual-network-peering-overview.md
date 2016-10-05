
<properties
   pageTitle="Homologation de réseaux virtuels Azure | Microsoft Azure"
   description="Découvrez en quoi consiste l’homologation de réseaux virtuels dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# Homologation de réseaux virtuels

L’homologation de réseaux virtuels est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul réseau pour tous les besoins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les machines virtuelles se trouvant dans ces réseaux virtuels peuvent communiquer directement entre elles à l’aide d’adresses IP privées.

Le trafic entre les machines virtuelles des réseaux virtuels homologués est acheminé via l’infrastructure Azure de façon assez similaire au trafic entre des machines virtuelles d’un même réseau virtuel. Voici quelques-uns des avantages de l’homologation de réseaux virtuels :

- Connexion à latence faible et haut débit entre les ressources de différents réseaux virtuels.
- Possibilité d’utiliser des ressources telles que des appliances de réseau virtuel et des passerelles VPN en tant que points de transit dans un réseau virtuel homologué.
- Possibilité de connecter un réseau virtuel utilisant le modèle Azure Resource Manager à un réseau virtuel utilisant le modèle de déploiement classique pour bénéficier d’une connectivité totale entre les ressources de ces réseaux virtuels.

Exigences et principaux aspects de l’homologation de réseaux virtuels :

- Les deux réseaux virtuels à homologuer doivent être situés dans la même région Azure.
- Les espaces d’adressage des réseaux virtuels à homologuer ne doivent pas se chevaucher.
- L’homologation concerne deux réseaux virtuels et aucune relation transitive n’en découle. Par exemple, si le réseau virtuel A est homologué avec le réseau virtuel B et si le réseau virtuel B est homologué avec le réseau virtuel C, cela ne signifie pas que le réseau virtuel 1 est homologué avec le réseau virtuel C.
- L’homologation peut être établie entre des réseaux virtuels dans deux abonnements, à condition qu’un utilisateur privilégié de chacun de ces abonnements autorise l’homologation et que les abonnements soient associés au même locataire Active Directory.
- Un réseau virtuel qui utilise le modèle de déploiement Resource Manager peut être homologué avec un autre réseau virtuel qui utilise ce modèle, ou avec un réseau virtuel qui utilise le modèle de déploiement classique. Cependant, les réseaux virtuels qui utilisent le modèle de déploiement classique ne peuvent pas être homologués entre eux.
- Bien que la communication entre des machines virtuelles de réseaux virtuels homologués ne présente aucune restriction de bande passante supplémentaire, un plafond de bande passante basé sur la taille des machines virtuelles continue de s’appliquer.


![Homologation de réseaux virtuels de base](./media/virtual-networks-peering-overview/figure01.png)

## Connectivité
Une fois deux réseaux virtuels homologués, une machine virtuelle (rôle de travail/Web) d’un des réseaux virtuels peut se connecter directement aux machines virtuelles de l’autre réseau virtuel. Ces deux réseaux bénéficient d’une connectivité de niveau IP totale.

La latence du réseau pour un aller-retour entre deux machines virtuelles de réseaux virtuels homologués est la même qu’au sein d’un réseau virtuel local. Le débit du réseau repose sur la bande passante autorisée pour la machine virtuelle proportionnellement à sa taille. Aucune restriction de bande passante supplémentaire n’est appliquée.

Le trafic entre les machines virtuelles de réseaux virtuels homologués est acheminé directement via l’infrastructure principale d’Azure et non par le biais d’une passerelle.

Les machines virtuelles d’un réseau virtuel peuvent accéder aux points de terminaison d’équilibrage de charge interne du réseau virtuel homologué. Des groupes de sécurité réseau peuvent être appliqués dans l’un ou l’autre des réseaux virtuels pour bloquer l’accès à d’autres réseaux virtuels ou à des sous-réseaux si besoin est.

Lorsque des utilisateurs configurent l’homologation, ils peuvent ouvrir ou fermer les règles de groupe de sécurité réseau entre les réseaux virtuels. Si l’utilisateur choisit d’ouvrir totalement la connectivité entre les réseaux virtuels homologués (ce qui est l’option par défaut), il peut alors appliquer des groupes de sécurité réseau à des sous-réseaux ou des machines virtuelles spécifiques NSG pour bloquer ou refuser certains accès.

La résolution de noms DNS internes fournie par Azure pour les machines virtuelles ne fonctionne pas entre des réseaux virtuels homologués. Les machines virtuelles présentent des noms DNS internes pouvant uniquement être résolus au sein du réseau virtuel local. Cependant, les utilisateurs peuvent configurer des machines virtuelles exécutées dans les réseaux virtuels homologués en tant que serveurs DNS pour un réseau virtuel.

## Chaînage de services
Les utilisateurs peuvent configurer des tables d’itinéraires définis par l’utilisateur qui pointent vers des machines virtuelles de réseaux virtuels homologués en tant qu’adresse IP du « tronçon suivant », comme illustré dans le schéma plus loin dans cet article. Cela leur permet de mettre en œuvre un chaînage de services par l’intermédiaire duquel ils peuvent diriger le trafic d’un réseau virtuel vers une appliance virtuelle exécutée dans un réseau virtuel homologué via les tables d’itinéraires.

Les utilisateurs peuvent également déployer efficacement des environnements de type hub-and-spoke où le nœud peut héberger des composants d’infrastructure tels qu’une appliance virtuelle réseau. Tous les réseaux virtuels reliés en étoile peuvent être homologués avec ce nœud et diriger un sous-ensemble du trafic vers des appliances exécutées dans le réseau virtuel central. En résumé, l’homologation de réseaux virtuels permet de définir l’adresse IP du tronçon suivant dans la table d’itinéraires définis par l’utilisateur sur l’adresse IP d’une machine virtuelle du réseau virtuel homologué.

## Passerelles et connectivité locale
Chaque réseau virtuel, qu’il soit homologué ou non avec un autre réseau virtuel, peut posséder sa propre passerelle et l’utiliser pour se connecter localement. Les utilisateurs peuvent également configurer des [connexions de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) à l’aide de passerelles, même si les réseaux virtuels sont homologués.

Lorsque les deux options d’interconnexion de réseaux virtuels sont configurées, le trafic entre les réseaux virtuels transite via la configuration d’homologation (c’est-à-dire via le réseau principal Azure).

Lorsque des réseaux virtuels sont homologués, les utilisateurs peuvent également configurer la passerelle du réseau virtuel homologué en tant que point de transit vers la connexion locale. Dans ce cas, le réseau virtuel qui utilise une passerelle distante ne peut pas posséder sa propre passerelle. Un réseau virtuel ne peut posséder qu’une seule passerelle. Il peut s’agir soit d’une passerelle locale, soit d’une passerelle distante (dans le réseau virtuel homologué), comme l’illustre l’image suivante.

Le transit par passerelle n’est pas pris en charge dans la relation d’homologation entre un réseau virtuel utilisant le modèle Resource Manager et un réseau virtuel utilisant le modèle de déploiement classique. Pour que le transit par passerelle fonctionne, les deux réseaux virtuels de la relation d’homologation doivent utiliser le modèle de déploiement Resource Manager.

Lorsque des réseaux virtuels qui partagent une même connexion Azure ExpressRoute sont homologués, le trafic entre eux transite via la relation d’homologation (c’est-à-dire via le réseau principal Azure). Les utilisateurs peuvent toujours utiliser des passerelles locales dans chaque réseau virtuel pour se connecter au circuit local. Ils peuvent également utiliser une passerelle partagée et configurer le transit pour la connectivité locale.

![Transit entre des réseaux virtuels homologués](./media/virtual-networks-peering-overview/figure02.png)

## Approvisionnement
L’homologation de réseaux virtuels est une opération nécessitant des privilèges. Il s’agit d’une fonction distincte sous l’espace de noms VirtualNetworks. Un utilisateur peut se voir accorder des droits spécifiques pour autoriser l’homologation. Un utilisateur qui dispose d’un accès en lecture-écriture au réseau virtuel hérite automatiquement de ces droits.

Un utilisateur administrateur ou disposant des privilèges d’homologation peut lancer une opération d’homologation sur un autre réseau virtuel. S’il existe une demande d’homologation correspondante de l’autre côté et si les autres exigences sont remplies, l’homologation est établie.

Pour plus d’informations sur la création d’une homologation entre deux réseaux virtuels, consultez les articles de la section « Étapes suivantes ».

## Limites
Le nombre d’homologations autorisées pour un même réseau virtuel est limité. Pour plus d’informations, consultez la section [Limites de mise en réseau d’Azure](../azure-subscription-service-limits.md#networking-limits).

## Tarification
L’homologation de réseaux virtuels ne sera pas facturée pendant la période d’évaluation. Une fois la sortie effective, un tarif minime sera appliqué pour le trafic entrant et sortant qui utilise l’homologation. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/virtual-network).


## Étapes suivantes
- [Configurer une homologation entre des réseaux virtuels](virtual-networks-create-vnetpeering-arm-portal.md).
- En savoir plus sur les [groupes de sécurité réseau](virtual-networks-nsg.md).
- En savoir plus sur les [tinéraires définis par l’utilisateur et le transfert IP](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0928_2016-->