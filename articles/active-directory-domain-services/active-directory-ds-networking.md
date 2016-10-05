<properties
	pageTitle="Services de domaine Azure AD : instructions de mise en réseau | Microsoft Azure"
	description="Considérations relatives à la mise en réseau pour les services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Considérations relatives à la mise en réseau pour les services de domaine Azure AD

## Comment sélectionner un réseau virtuel Azure
Les instructions suivantes vous aident à sélectionner un réseau virtuel en vue de l’utiliser avec les services de domaine Azure AD.

### Type de réseau virtuel Azure

- Vous pouvez activer les services de domaine Azure AD dans un réseau virtuel Azure Classic.

- Les services de domaine Azure AD **ne peuvent pas être activés dans les réseaux virtuels créés à l’aide d’Azure Resource Manager**.

- Vous pouvez connecter un réseau virtuel basé sur Resource Manager à un réseau virtuel Classic dans lequel les services de domaine Azure AD sont activés. Vous pouvez ensuite utiliser les services de domaine Azure AD dans le réseau virtuel basé sur Resource Manager.

- **Réseaux virtuels régionaux** : si vous prévoyez d’utiliser un réseau virtuel existant, assurez-vous qu’il s’agit d’un réseau virtuel régional.

    - Les réseaux virtuels qui recourent au mécanisme des groupes d’affinités hérité ne peuvent pas être utilisés avec les services de domaine Azure AD.

	- Pour utiliser les services de domaine Azure AD, vous devez [migrer les réseaux virtuels hérités vers des réseaux virtuels régionaux](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### Région Azure pour le réseau virtuel

- Votre domaine géré par les services de domaine Azure AD est déployé dans la même région Azure que le réseau virtuel dans lequel vous choisissez d’activer le service.

- Sélectionnez un réseau virtuel dans une région Azure prise en charge par les services de domaine Azure AD.

- Pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/).


### Conditions préalables pour le réseau virtuel

- **Proximité avec les charges de travail Azure** : sélectionnez le réseau virtuel qui héberge actuellement ou qui hébergera des machines virtuelles ayant besoin d’accéder aux services de domaine Azure AD.

- **Serveurs DNS personnalisés/propres** : assurez-vous qu’aucun serveur DNS personnalisé n’est configuré pour le réseau virtuel.

- **Domaines existants portant le même nom de domaine** : vérifiez qu’aucun domaine existant portant le même nom de domaine n’est disponible sur ce réseau virtuel. Supposons par exemple qu’un domaine appelé « contoso.com » soit déjà disponible sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré par les services de domaine Azure AD portant le même nom de domaine (soit « contoso.com ») sur ce réseau virtuel. Vous rencontrez un échec lorsque vous essayez d’activer les services de domaine Azure AD en raison des conflits de noms de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré par les services de domaine Azure AD. Vous pouvez également annuler l’approvisionnement du domaine existant, puis passer à l’activation des services de domaine Azure AD.

> [AZURE.WARNING] Vous ne pouvez pas déplacer les services de domaine vers un autre réseau virtuel une fois le service activé.


## Conception de groupes de sécurité et de sous-réseaux
Un [groupe de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorise ou rejette les instances de machine virtuelle dans un réseau virtuel. Des groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle au sein de ce sous-réseau. Lorsqu’un groupe de sécurité réseau est associé à un sous-réseau, les règles ACL s’appliquent à toutes les instances de machine virtuelle présentes dans ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité par l’association d’un groupe de sécurité réseau directement à la machine virtuelle.

> [AZURE.NOTE] **Déployez les services de domaine Azure AD dans un sous-réseau dédié distinct au sein de votre réseau virtuel Azure. N’appliquez pas de groupe de sécurité réseau à ce sous-réseau dédié. N’activez pas les services de domaine Azure AD dans le sous-réseau de passerelle de votre réseau virtuel.**

![Conception de sous-réseau recommandée](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

> [AZURE.WARNING] Lorsque vous associez un groupe de sécurité réseau et un sous-réseau dans lequel les services de domaine Azure AD sont activés, vous pouvez affecter la capacité de Microsoft à gérer le domaine. En outre, la synchronisation entre votre client Azure AD et votre domaine géré est interrompue. **Le contrat de niveau de service (SLA) ne concerne pas les déploiements dans lesquels un groupe de sécurité réseau a été appliqué au sous-réseau dans lequel les services de domaine Azure AD sont activés.**


## Connectivité réseau
Un domaine géré par les services de domaine Azure AD peut être activé uniquement au sein d’un seul réseau virtuel Classic dans Azure. Les réseaux virtuels créés à l’aide d’Azure Resource Manager ne sont pas pris en charge.

### Scénarios de connexion de réseaux Azure
Connectez des réseaux virtuels Azure en vue d’utiliser le domaine géré dans n’importe lequel des scénarios de déploiement suivants :

#### Utiliser le domaine géré dans plusieurs réseaux virtuels Azure Classic
Vous pouvez connecter d’autres réseaux virtuels Azure Classic au réseau virtuel Azure Classic dans lequel vous avez activé les services de domaine Azure AD. Cette connexion vous permet d’utiliser le domaine géré avec les charges de travail déployées dans d’autres réseaux virtuels.

![Connexion entre des réseaux virtuels Classic](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### Utiliser le domaine géré dans un réseau virtuel basé sur Resource Manager
Vous pouvez connecter un réseau virtuel basé sur Resource Manager au réseau virtuel Azure Classic dans lequel vous avez activé les services de domaine Azure AD. Cette connexion vous permet d’utiliser le domaine géré avec les charges de travail déployées dans le réseau virtuel basé sur Resource Manager.

![Connexion entre un réseau virtuel Resource Manager et un réseau virtuel Classic](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### Options de connexion réseau

- **Connexion entre deux réseaux virtuels à l’aide d’une connexion VPN de site à site** : la connexion entre deux réseaux virtuels est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE.

	![Connexion entre des réseaux virtuels à l’aide d’une passerelle VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Plus d’informations : connecter des réseaux virtuels à l’aide d’une passerelle VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Connexion entre deux réseaux virtuels à l’aide d’une homologation de réseaux virtuels** : l’homologation de réseaux virtuels est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul réseau pour tous les besoins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les machines virtuelles se trouvant dans ces réseaux virtuels peuvent communiquer directement entre elles à l’aide d’adresses IP privées.

    ![Connexion entre des réseaux virtuels à l’aide d’une homologation](./media/active-directory-domain-services-design-guide/vnet-peering.png)

	[Plus d’informations : homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md)



<br>

## Contenu connexe

- [Homologation de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md)

- [Configurer une connexion de réseau virtuel à réseau virtuel pour le modèle de déploiement classique](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Groupes de sécurité réseau Azure](../virtual-network/virtual-networks-nsg.md)

<!---HONumber=AcomDC_0921_2016-->