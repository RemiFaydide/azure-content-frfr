<properties 
   pageTitle="Connecter plusieurs sites locaux à un réseau virtuel à l’aide d’une passerelle VPN"
   description="Cet article vous guidera pour la connexion de plusieurs sites locaux à un réseau virtuel à l’aide d’une passerelle VPN pour le modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# Connecter plusieurs sites locaux à un réseau virtuel

Cet article s’applique à la connexion de sites locaux à un réseau virtuel créé à l’aide du modèle de déploiement classique (également appelé Gestion des services). Lorsque nous disposerons d’un article de création de réseaux virtuels à l’aide du modèle Gestionnaire de ressources, il sera accessible à partir de cette page.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modèles de déploiement et outils pour les connexions de site à site**

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]


## À propos de la connexion

Vous pouvez connecter plusieurs sites locaux à un même réseau virtuel. Cela est particulièrement intéressant pour la création de solutions de cloud hybrides. La création d’une connexion sur plusieurs sites à votre passerelle de réseau virtuel Azure est très similaire à la création d’autres connexions de site à site. En fait, vous pouvez utiliser une passerelle VPN Azure existante tant que la passerelle est dynamique (basée sur un itinéraire).

Si une passerelle statique est déjà connectée à votre réseau virtuel, vous pouvez changer le type de passerelle en dynamique sans avoir à régénérer le réseau virtuel pour prendre en charge plusieurs sites. Dans ce cas, assurez-vous que votre passerelle VPN locale prend en charge les configurations VPN basées sur un itinéraire.

![diagramme multi-sites](./media/vpn-gateway-multi-site/multisite.png "multi-sites")

## Éléments à prendre en considération

**Vous ne pouvez pas utiliser le portail Azure Classic pour apporter des modifications à ce réseau virtuel.** Pour cette version, vous devez apporter des modifications au fichier de configuration réseau au lieu d’utiliser le portail Azure Classic. Si vous apportez des modifications dans le portail Azure Classic, elles remplacent vos paramètres de référence multisite pour ce réseau virtuel.

Vous aurez le temps de vous familiariser avec le fichier de configuration réseau au terme de la procédure multisite. Toutefois, si plusieurs personnes travaillent sur la configuration de votre réseau, vous devez vous assurer que tout le monde a connaissance de cette limitation. Cela ne signifie pas que vous ne pouvez pas du tout utiliser le portail Azure Classic. Vous pouvez l’utiliser pour toutes les procédures, à l’exception des modifications de configuration de ce réseau virtuel spécifique.

## Avant de commencer

Avant de commencer la configuration, vérifiez que les conditions suivantes sont remplies :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez activer vos [avantages d’abonné à MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Matériel VPN compatible pour chaque emplacement local. Voir [À propos des périphériques VPN pour la connectivité au réseau virtuel](vpn-gateway-about-vpn-devices.md) pour vérifier si l’appareil que vous souhaitez utiliser est bien compatible.

- Une adresse IP IPv4 publique exposée en externe pour chaque périphérique VPN. L’adresse IP ne peut pas se trouver derrière un NAT. Cela est obligatoire.

- Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

- Un expert en configuration du matériel VPN. Vous ne pouvez pas utiliser les scripts VPN générés automatiquement à partir du portail Azure Classic pour configurer vos périphériques VPN. Cela signifie que vous devez avoir une bonne maîtrise de la façon de configurer votre périphérique VPN ou collaborer avec quelqu’un disposant des connaissances nécessaires.

- Les plages d’adresses IP que vous souhaitez utiliser pour votre réseau virtuel (si vous n’en avez pas déjà créé).

- Les plages d’adresses IP pour chacun des sites du réseau local auxquels vous vous connecterez. Vous devez vous assurer que les plages d’adresses IP pour chacun des sites du réseau local auxquels vous souhaitez vous connecter ne se chevauchent ne pas. Dans le cas contraire, le portail Azure Classic ou l’API REST rejette la configuration en cours de chargement.

	Par exemple, si vous avez deux sites de réseau local contenant la plage d’adresse IP 10.2.3.0/24 et que vous disposez d’un package avec une adresse de destination 10.2.3.3, Azure ne saura pas à quel site envoyer le package, car les plages d’adresses se chevauchent. Pour éviter les problèmes de routage, Azure ne vous permet pas de télécharger un fichier de configuration ayant des plages qui se chevauchent.



## 1\. Créer un VPN de site à site

Si vous avez déjà un VPN de site à site avec une passerelle de routage dynamique, bravo ! Vous pouvez [Exporter les paramètres de configuration de réseau virtuel](#export). Sinon, procédez ainsi :

### Si vous avez déjà un réseau virtuel de site à site, mais que sa passerelle de routage est statique :

1. Modifiez le type de passerelle en routage dynamique. Un VPN multisite requiert une passerelle de routage dynamique (ou basée sur un itinéraire). Pour modifier le type de passerelle, vous devez d’abord supprimer la passerelle existante, puis en créer une nouvelle. Pour obtenir des instructions, consultez la page [Modification du type de routage VPN de votre passerelle](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).

2. Configurez votre nouvelle passerelle et créez votre tunnel VPN. Pour obtenir des instructions, consultez [Configuration d’une passerelle VPN dans le portail Azure Classic](vpn-gateway-configure-vpn-gateway-mp.md). Tout d’abord, changez le type de votre passerelle en dynamique.

### Si vous n’avez pas de réseau virtuel de site à site :

1. Créez votre réseau virtuel de site à site en suivant la procédure décrite dans [Création d’un réseau virtuel avec une connexion VPN de site à site dans le portail Azure Classic](vpn-gateway-site-to-site-create.md).

2. Configurez une passerelle de routage dynamique en suivant la procédure décrite dans [Configuration d’une passerelle VPN](vpn-gateway-configure-vpn-gateway-mp.md). Veillez à sélectionner le **routage dynamique** pour le type de passerelle.

## <a name="export"></a>2. Exporter le fichier de configuration réseau 

Exportez le fichier de configuration réseau. Le fichier que vous exportez permet de configurer vos nouveaux paramètres multisites. Si vous avez besoin d’instructions sur l’exportation d’un fichier, consultez la section dans l’article [Création d’un réseau virtuel à l’aide d’un fichier de configuration réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal).

## 3\. Ouvrir le fichier de configuration réseau

Ouvrez le fichier de configuration réseau que vous avez téléchargé à l’étape précédente. Utilisez l’éditeur xml de votre choix. Le fichier doit se présenter ainsi :

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

## 4\. Ajouter plusieurs références de site

Lorsque vous ajoutez ou supprimez les informations de référence de site, des modifications de configuration sont apportées à ConnectionsToLocalNetwork/LocalNetworkSiteRef. À l’ajout d’une nouvelle référence de site local, Azure crée un nouveau tunnel. Dans l’exemple ci-dessous, la configuration du réseau correspond à une connexion de site unique. Enregistrez le fichier une fois que vous avez terminé d’apporter vos modifications.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## 5\. Importer le fichier de configuration réseau

Importez le fichier de configuration réseau. Lorsque vous importez ce fichier avec les modifications, les nouveaux tunnels sont ajoutés. Les tunnels utilisent la passerelle dynamique que vous avez créée précédemment. Si vous avez besoin d’instructions sur l’importation du fichier, consultez la section dans l’article [Création d’un réseau virtuel à l’aide d’un fichier de configuration réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal).

## 6\. Télécharger les clés

Une fois les nouveaux tunnels ajoutés, utilisez l’applet de commande PowerShell `Get-AzureVNetGatewayKey` pour obtenir les clés prépartagées IPsec/IKE de chaque tunnel.

Par exemple :

	Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

	Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Si vous préférez, vous pouvez également utiliser l’API REST *Obtenir la clé partagée de la passerelle de réseau virtuel* pour obtenir les clés prépartagées.

## 7\. Vérifiez vos connexions

Vérifiez l’état de tunnel multisite. Après avoir téléchargé les clés de chaque tunnel, vérifiez les connexions. Utilisez `Get-AzureVnetConnection` pour obtenir la liste des tunnels de réseau virtuel, comme indiqué dans l’exemple ci-dessous. VNet1 est le nom du réseau virtuel.

	Get-AzureVnetConnection -VNetName VNET1
		
	ConnectivityState         : Connected
	EgressBytesTransferred    : 661530
	IngressBytesTransferred   : 519207
	LastConnectionEstablished : 5/2/2014 2:51:40 PM
	LastEventID               : 23401
	LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
	LastEventTimeStamp        : 5/2/2014 2:51:40 PM
	LocalNetworkSiteName      : Site1
	OperationDescription      : Get-AzureVNetConnection
	OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
	OperationStatus           : Succeeded
		
	ConnectivityState         : Connected
	EgressBytesTransferred    : 789398
	IngressBytesTransferred   : 143908
	LastConnectionEstablished : 5/2/2014 3:20:40 PM
	LastEventID               : 23401
	LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
	LastEventTimeStamp        : 5/2/2014 2:51:40 PM
	LocalNetworkSiteName      : Site2
	OperationDescription      : Get-AzureVNetConnection
	OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
	OperationStatus           : Succeeded

## Étapes suivantes

Pour en savoir plus sur les passerelles VPN, consultez [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=AcomDC_0713_2016-->