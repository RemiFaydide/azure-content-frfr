<properties
   pageTitle="Configuration d’un réseau virtuel et d’une passerelle pour ExpressRoute dans le portail Classic| Microsoft Azure"
   description="Cet article vous guide pas à pas dans la configuration d’un réseau virtuel pour ExpressRoute à l’aide du modèle de déploiement classique et du portail Classic."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# Créer un réseau virtuel pour ExpressRoute dans le portail Classic

Cet article vous explique pas à pas comment configurer un réseau virtuel et une passerelle de réseau virtuel à utiliser avec ExpressRoute à l’aide du modèle de déploiement classique et du portail Classic.

Si vous recherchez des instructions pour le modèle de déploiement de Resource Manager, vous pouvez utiliser les articles suivants : [Créer un réseau virtuel à l’aide de PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) et [Ajouter une passerelle VPN à un réseau virtuel Resource Manager pour une configuration ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Créer un réseau virtuel classique et une passerelle

Les étapes suivantes vous permettent de créer un réseau virtuel classique et une passerelle de réseau virtuel. Si vous disposez déjà d’un réseau virtuel classique, consultez la section [Configurer un réseau virtuel classique existant](#config) dans cet article.

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).

2. Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

3. Dans la page **Détails du réseau virtuel**, entrez les informations suivantes :

	- **Nom** : nommez votre réseau virtuel. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos instances PaaS, vous préféreriez probablement avoir un nom qui n’est pas trop compliqué.
	- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans la région Est des États-Unis, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.

4. Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.

	- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS ou sélectionnez un serveur DNS précédemment inscrit dans le menu contextuel. Ce paramètre n'entraîne pas la création de serveur DNS. Il vous permet de spécifier le serveur DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
	- **Connectivité de site à site** : activez la case à cocher **Configurer un réseau VPN de site à site**.
	- **ExpressRoute** : activez la case à cocher **Utiliser ExpressRoute**. Cette option apparaît uniquement si vous avez sélectionné **Configurer un réseau VPN de site à site**.
	- **Réseau local** : vous êtes tenu de disposer d’un site de réseau local pour ExpressRoute. Toutefois, dans le cas d’une connexion ExpressRoute, les préfixes d’adresse spécifiés pour le site de réseau local seront ignorés. À la place, les préfixes d’adresse proposés à Microsoft via le circuit ExpressRoute seront utilisés pour le routage.<BR>Si vous disposez déjà d’un réseau local créé pour votre connexion ExpressRoute, vous pouvez le sélectionner dans la liste déroulante. Si vous ne disposez pas d’un réseau local, sélectionnez **Spécifier un nouveau réseau local**.

5. La page **Connectivité de site à site** s’affiche si vous avez choisi de spécifier un nouveau réseau local à l’étape précédente. Pour configurer votre réseau local, entrez les informations ci-dessous, puis cliquez sur la flèche Suivant.

	- **Nom** : nom que vous souhaitez donner à votre site de réseau local.
	- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses (CIDR). Vous pouvez spécifier n’importe quelle plage d’adresses à condition qu’elle ne chevauche pas la plage d’adresses de votre réseau virtuel. Habituellement, les plages d’adresses de vos réseaux locaux sont spécifiées ainsi, mais dans le cas d’ExpressRoute, ces paramètres ne sont pas utilisés. Toutefois, ce paramètre est requis pour créer le réseau local lorsque vous utilisez le portail classique.
	- **Ajouter un espace d’adressage** : ce paramètre ne s’applique pas à ExpressRoute.


6. Sur la page **Virtual Network Address Spaces**, entrez les informations ci-dessous, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau.

	- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.
	- **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Il est inutile d’ajouter des sous-réseaux supplémentaires.
	- **Ajouter un sous-réseau de passerelle** : cliquez sur cette option pour ajouter le sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé uniquement pour la passerelle de réseau virtuel et est requis pour cette configuration.<BR>Le CIDR du sous-réseau de passerelle (nombre d’adresses) pour ExpressRoute doit être /28 ou supérieur (/27, /26, etc.). Ainsi, il y a suffisamment d’adresses IP dans ce sous-réseau pour que la configuration fonctionne. Dans le portail classique, si vous avez activé la case à cocher pour utiliser ExpressRoute, le portail spécifie un sous-réseau de passerelle avec /28. Vous ne pouvez pas ajuster le nombre d’adresses CIDR dans le portail classique. Le sous-réseau de passerelle apparaîtra en tant que **Gateway** dans le portail classique, bien que le nom réel du sous-réseau de passerelle créé soit en fait **GatewaySubnet**. Vous pouvez voir ce nom à l’aide de PowerShell ou dans le Portail Azure.

7. Cliquez sur la coche en bas de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page **Réseaux** du portail classique.

## <a name="gw"></a>Créer la passerelle

1. Dans la page **Réseaux**, cliquez sur le réseau virtuel que vous venez de créer, puis sur **Tableau de bord** en haut de la page.

2. En bas de la page **Tableau de bord**, cliquez sur **Créer la passerelle** et sélectionnez **Routage dynamique**. Cliquez sur **Oui** pour confirmer que vous souhaitez créer une passerelle.

3. Lorsque la création de la passerelle démarre, un message s'affiche pour vous indiquer le démarrage de la passerelle. La création de la passerelle peut durer jusqu’à 45 minutes.

4. Liez votre réseau à un circuit. Suivez les instructions de l’article [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurer un réseau virtuel classique existant pour ExpressRoute

Si vous disposez déjà d’un réseau virtuel classique, vous pouvez le configurer pour vous connecter à ExpressRoute dans le portail classique. Les paramètres sont identiques à ceux des sections ci-dessus. Vous devez donc lire ces sections pour connaître les paramètres requis. Si vous voulez créer une connexion coexistante ExpressRoute/site à site, consultez [cet article](expressroute-howto-coexist-classic.md) pour connaître la procédure. Les étapes sont différentes de celles de cet article.
 
1. Vous devez créer le réseau local avant de mettre à jour le reste de vos paramètres de réseau virtuel. Pour créer un nouveau réseau local (nécessaire pour la configuration d’ExpressRoute via le portail classique), cliquez sur **Nouveau** **>** **Network Services** **>** **Réseau virtuel** **>** **Ajouter un réseau local**. Suivez les étapes de l’Assistant pour créer le réseau local.

2. Utilisez la page **Configurer** pour mettre à jour le reste des paramètres de votre réseau virtuel et associer le réseau virtuel au réseau local.

3. Après avoir configuré les paramètres, accédez à la section [Créer la passerelle](#gw) de cet article pour créer la passerelle.


## Étapes suivantes

- Si vous souhaitez ajouter des machines virtuelles à votre réseau virtuel, consultez [Parcours d’apprentissage de Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Pour plus d’informations sur ExpressRoute, consultez [Vue d’ensemble d’ExpressRoute](expressroute-introduction.md).


 

<!---HONumber=AcomDC_0921_2016-->