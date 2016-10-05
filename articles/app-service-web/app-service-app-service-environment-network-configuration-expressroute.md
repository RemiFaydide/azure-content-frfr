<properties 
	pageTitle="Détails de la configuration réseau pour travailler avec ExpressRoute" 
	description="Détails de la configuration réseau pour exécuter des environnements App Service sur des réseaux virtuels connectés à un circuit ExpressRoute." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2016" 
	ms.author="stefsch"/>

# Détails de la configuration réseau pour les environnements App Service avec ExpressRoute 

## Vue d'ensemble ##
Les clients peuvent connecter un circuit [Azure ExpressRoute][ExpressRoute] à leur infrastructure de réseau virtuel pour étendre leur réseau local à Azure. Vous pouvez créer un environnement App Service dans un sous-réseau de cette infrastructure de [réseau virtuel][virtualnetwork]. Les applications exécutées dans l'environnement App Service peuvent alors établir des connexions sécurisées à des ressources principales accessibles uniquement par le biais de la connexion ExpressRoute.

Un environnement App Service peut être créé **soit** dans un réseau virtuel Azure Resource Manager, **ou** dans un réseau virtuel de modèle de déploiement classique. Grâce à une modification récente effectuée en juin 2016, les ASE peuvent désormais être également déployés dans les réseaux virtuels qui utilisent soit des plages d’adresses publiques soit des espaces d’adressage RFC1918 (par exemple, des adresses privées).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Connectivité réseau requise ##
Il existe des exigences de connectivité réseau pour les environnements App Service qui peuvent ne pas être initialement satisfaites dans un réseau virtuel connecté à ExpressRoute. Les environnements App Service requièrent tous les éléments suivants pour fonctionner correctement :


-  Connectivité réseau sortante à des points de terminaison Azure Storage dans le monde entier sur les ports 80 et 443. Cela inclut les points de terminaison situés dans la même région que l’environnement App Service, ainsi que les points de terminaison de stockage situés dans d’**autres** régions Azure. Les points de terminaison Azure Storage se résolvent dans les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* et *file.core.windows.net*.
-  Connectivité réseau sortante au service de fichiers Azure sur le port 445.
-  Connectivité réseau sortante à des points de terminaison BD SQL situés dans la même région que l'environnement App Service. Les points de terminaison de base de données SQL se résolvent dans le domaine suivant : *database.windows.net*. Cela suppose d’ouvrir l’accès aux ports 1433, 11000-11999 et 14000-14999. Pour plus d’informations, consultez [cet article portant sur l’utilisation du port V12 de la base de données SQL](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Connectivité réseau sortante vers des points de terminaison du plan gestion Azure (points de terminaison ASM et ARM). Cela inclut la connexion sortante à *management.core.windows.net* et *management.azure.com*.
-  Connectivité réseau sortante à *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*. Cela est nécessaire pour prendre en charge la fonctionnalité SSL.
-  La configuration DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et les domaines mentionnés dans les points précédents. Si ces points de terminaison ne peuvent pas être résolus, les tentatives de création d'environnement App Service échoueront et les environnements App Service existants seront marqués comme non intègres.
-  L’accès sortant sur le port 53 est nécessaire pour la communication avec des serveurs DNS.
-  S'il existe un serveur DNS personnalisé à l'autre extrémité d'une passerelle VPN, le serveur DNS doit être accessible depuis le sous-réseau contenant l'environnement App Service.
-  Le chemin d'accès réseau sortant ne peut pas traverser des proxys d'entreprise internes et il ne peut pas non plus être tunnelé de force en local. Ceci modifie l'adresse NAT réelle du trafic réseau sortant à partir de l'environnement App Service. La modification de l'adresse NAT du trafic réseau sortant d'un environnement App Service entraînera des échecs de connectivité vers plusieurs des points de terminaison répertoriés ci-dessus. Cela entraîne des échecs de création d'environnement App Service, ainsi que la désignation comme non intègres des environnements App Service précédemment considérés comme intègres.
-  L'accès réseau entrant vers les ports requis pour les environnements App Service doit être autorisé, comme décrit dans cet [article][requiredports].

La configuration DNS requise peut être satisfaite en garantissant qu'une infrastructure DNS valide est configurée et gérée pour le réseau virtuel. Si, pour une raison quelconque, la configuration DNS est modifiée après la création d'un environnement App Service, les développeurs peuvent forcer un environnement App Service à récupérer la nouvelle configuration DNS. Le déclenchement d'un redémarrage d'un environnement propagé à l'aide de l'icône « Redémarrer » située en haut du panneau de gestion de l'environnement App Service du [portail Azure][NewPortal] force l'environnement à récupérer la nouvelle configuration DNS.

Les exigences d'accès au réseau entrant peuvent être satisfaites en configurant un [groupe de sécurité réseau][NetworkSecurityGroups] sur le sous-réseau de l'environnement App Service, afin d'autoriser l'accès requis, comme décrit dans cet [article][requiredports].

## Activation de la connectivité réseau sortante pour un environnement App Service##
Par défaut, un circuit ExpressRoute nouvellement créé publie un itinéraire par défaut qui autorise la connectivité Internet sortante. Avec cette configuration, un environnement App Service sera en mesure de se connecter à d'autres points de terminaison Azure.

Toutefois, une configuration client courante consiste à définir un itinéraire par défaut (0.0.0.0/0), ce qui force le trafic Internet sortant à évoluer sur site. Ce flux de trafic décompose invariablement les environnements App Service, car le trafic sortant est soit bloqué sur site, soit fait l'objet d'une opération NAT sur un jeu d'adresses non reconnaissable qui ne fonctionne plus avec différents points de terminaison Azure.

La solution consiste à définir un (ou plusieurs) itinéraires définis par l'utilisateur (UDR) sur le sous-réseau qui contient l'environnement App Service. Un itinéraire défini par l'utilisateur définit des itinéraires spécifiques au sous-réseau qui seront respectés au lieu de l'itinéraire par défaut.

Si possible, il est recommandé d'utiliser la configuration suivante :

- La configuration ExpressRoute annonce 0.0.0.0/0 et par défaut, tunnelise de force tout le trafic sortant sur site.
- L'UDR (itinéraire défini par l'utilisateur) appliqué au sous-réseau contenant l'environnement App Service définit 0.0.0.0/0 avec Internet de type saut suivant (un exemple de ceci est présenté plus bas dans cet article).

L'effet combiné de ces étapes est que l'UDR de niveau sous-réseau a la priorité sur le tunneling forcé ExpressRoute, garantissant ainsi un accès Internet sortant à partir de l'environnement App Service.

> [AZURE.IMPORTANT] Les itinéraires définis dans un UDR **doivent** être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L'exemple ci-dessous utilise la plage d'adresses 0.0.0.0/0 étendue qui peut potentiellement être remplacée accidentellement par les annonces de routage à l'aide de plages d'adresses plus spécifiques.
>
>Les environnements App Service ne sont pas pris en charge avec les configurations ExpressRoute qui **publient incorrectement de façon croisée des itinéraires à partir du chemin d'accès d'homologation publique vers le chemin d'accès d'homologation privée**. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les annonces de routage depuis Microsoft pour un grand ensemble de plages d'adresses IP Microsoft Azure. Si ces plages d'adresses sont incorrectement publiées de façon croisée sur le chemin d'accès d'homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l'environnement App Service seront incorrectement acheminés de force vers l'infrastructure réseau sur site d'un client. Ce flux réseau interrompt les environnements App Service. La solution à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d'accès d'homologation publique vers le chemin d'accès d'homologation privée.

Vous trouverez des informations générales sur les itinéraires définis par l'utilisateur dans cette [présentation][UDROverview].

Pour plus d'informations sur la création et la configuration d'itinéraires définis par l'utilisateur, consultez ce [guide][UDRHowTo].

## Exemple de configuration d'itinéraire défini par l'utilisateur pour un environnement App Service ##

**Conditions préalables**

1. Installez la toute dernière version d'Azure Powershell à partir de la [page Téléchargements Azure][AzureDownloads] \(datant de juin 2015 ou version ultérieure). Sous « Outils de ligne de commande », un lien « Installer » sous « Windows Powershell » permet d'installer les dernières applets de commande Powershell.

2. Nous vous recommandons de créer un sous-réseau unique pour un usage exclusif par un environnement App Service. Ceci garantit que les itinéraires définis par l'utilisateur appliqués au sous-réseau ouvriront uniquement le trafic sortant pour l'environnement App Service.
3. **Important** : ne déployez pas l'environnement App Service **avant** d'avoir exécuté les étapes de configuration suivantes. Ceci garantit que la connectivité réseau sortante est disponible avant la tentative de déploiement d'un environnement App Service.

**Étape 1 : Créer une table d'itinéraires nommée**

L'extrait de code suivant crée une table d'itinéraires appelée « DirectInternetRouteTable » dans la région Azure Ouest des États-Unis :

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Étape 2 : Créer un ou plusieurs itinéraires dans la table**

Vous devrez ajouter un ou plusieurs itinéraires à la table afin d'activer l'accès Internet sortant.

L'approche recommandée pour la configuration d'un accès sortant à Internet consiste à définir un itinéraire pour 0.0.0.0/0 comme indiqué ci-dessous.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

N'oubliez pas que 0.0.0.0/0 est une plage d'adresses étendue et, par conséquent, elle sera remplacée par les plages d'adresses plus spécifiques publiées par ExpressRoute. Pour réitérer la recommandation antérieure, un UDR avec un itinéraire 0.0.0.0/0 doit être utilisé conjointement avec une configuration ExressRoute qui publie uniquement 0.0.0.0/0 également.

Comme alternative, vous pouvez télécharger une liste complète et mise à jour des plages CIDR utilisées par Azure. Le fichier XML qui contient toutes les plages d'adresses IP Azure est disponible dans le [Centre de téléchargement Microsoft][DownloadCenterAddressRanges].

Notez toutefois que ces plages changent au fil du temps, d'où la nécessité d'effectuer des mises à jour manuelles périodiques pour maintenir la synchronisation d'un UDR. En outre, étant donné la limite maximale de 100 itinéraires dans un seul UDR, vous devrez « résumer » les plages d'adresses IP Azure pour respecter la limite de 100 itinéraires. N'oubliez pas que les itinéraires définis par UDR doivent être plus spécifiques que les itinéraires publiés par votre ExpressRoute.


**Étape 3 : Associer la table d'itinéraires au sous-réseau contenant l'environnement App Service**

La dernière étape de configuration consiste à associer la table d'itinéraires au sous-réseau où l'environnement App Service sera déployé. La commande suivante associe « DirectInternetRouteTable » à « ASESubnet » qui contiendra un environnement App Service.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Étape 4 : Étapes finales**

Une fois que la table d'itinéraires est liée au sous-réseau, nous vous recommandons de tester et confirmer le résultat souhaité. Par exemple, déployez une machine virtuelle sur le sous-réseau et vérifiez que :


- Le trafic sortant vers des points de terminaison Azure et non Azure mentionné précédemment dans cet article n'est **pas** transmis vers le circuit ExpressRoute. Il est très important de vérifier ce comportement, car si le trafic sortant à partir du sous-réseau est toujours tunnelisé de force sur site, la création d'un environnement App Service échouera toujours.
- Les recherches DNS de points de terminaison mentionnées plus haut se résolvent toutes correctement.

Une fois les étapes ci-dessus confirmées, vous devrez supprimer la machine virtuelle car le sous-réseau doit être « vide » au moment de la création de l'environnement App Service.
 
Puis, passez à la création d'un environnement App Service !

## Prise en main
Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service](../app-service/app-service-app-service-environments-readme.md).

Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service][IntroToAppServiceEnvironment].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]: https://portal.azure.com
 

<!-- IMAGES -->

<!---HONumber=AcomDC_0713_2016-->