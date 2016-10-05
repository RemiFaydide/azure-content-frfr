<properties 
   pageTitle="Présentation du groupe de sécurité réseau"
   description="Obtenez de plus amples informations sur le pare-feu distribué dans Azure à l’aide des groupes de sécurité réseau, et apprenez à utiliser les groupes de sécurité réseau pour isoler et contrôler le flux de trafic au sein de vos réseaux virtuels."
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
   ms.date="02/11/2016"
   ms.author="jdial" />

# Présentation du groupe de sécurité réseau

Un groupe de sécurité réseau (NSG) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorise ou rejette les instances de machine virtuelle dans un réseau virtuel. Des groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle au sein de ce sous-réseau. Lorsqu’un groupe de sécurité réseau est associé à un sous-réseau, les règles ACL s’appliquent à toutes les instances de machine virtuelle présentes dans ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité par l’association d’un groupe de sécurité réseau directement à la machine virtuelle.

## Ressource du groupe de sécurité réseau

Les groupes de sécurité réseau peuvent présenter les propriétés suivantes.

|Propriété|Description|Contraintes|Considérations|
|---|---|---|---|
|Name|Nom du groupe de sécurité réseau|Doit être unique dans la région<br/>Peut contenir des lettres, des chiffres, des traits de soulignement, des points et des traits d’union<br/>doit commencer par une lettre ou un chiffre<br/>doit se terminer par une lettre, un nombre ou un trait de soulignement<br/>peut contenir jusqu’à 80 caractères|Étant donné que vous devrez peut-être créer plusieurs groupes de sécurité réseau, assurez-vous que vous disposez d’une convention d’affectation de noms qui permet de facilement identifier la fonction de vos groupes de sécurité réseau|
|Région|Région Azure dans laquelle le groupe de sécurité réseau est hébergé|Les groupes de sécurité réseau ne peuvent être appliqués qu’aux ressources de la région dans laquelle ils sont créés|Voir les [limites](#Limits) ci-dessous pour savoir combien de groupes de sécurité réseau vous pouvez avoir dans une région|
|Groupe de ressources|Groupe de ressources auquel le groupe de sécurité réseau appartient|Bien qu’un groupe de sécurité réseau appartienne à un groupe de ressources, il peut être associé à des ressources dans n’importe quel groupe de ressources, tant que le groupe de ressources fait partie de la même région Azure que le groupe de sécurité réseau|Les groupes de ressources servent à gérer plusieurs ressources ensemble, comme une unité de déploiement<br/>Vous pouvez envisager de regrouper le groupe de sécurité réseau avec les ressources auxquelles il est associé|
|Règles|Règles définissant quel trafic est autorisé ou refusé||Voir les [règles de groupe de sécurité réseau](#Nsg-rules) ci-dessous| 

>[AZURE.NOTE] Les contrôles d’accès réseau basés sur le point de terminaison et les groupes de sécurité réseau ne sont pas pris en charge sur la même instance de machine virtuelle. Si vous souhaitez utiliser un groupe de sécurité réseau et une ACL de point de terminaison déjà en place, supprimez d'abord l’ACL de point de terminaison. Pour en savoir plus sur cette procédure, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](virtual-networks-acl-powershell.md).

### Règles de groupe de sécurité réseau

Les règles de groupe de sécurité réseau contiennent les propriétés suivantes.

|Propriété|Description|Contraintes|Considérations|
|---|---|---|---|
|**Name**|Nom de la règle|Doit être unique dans la région<br/>Peut contenir des lettres, des chiffres, des traits de soulignement, des points et des traits d’union<br/>doit commencer par une lettre ou un chiffre<br/>doit se terminer par une lettre, un nombre ou un trait de soulignement<br/>peut contenir jusqu’à 80 caractères|Un groupe de sécurité réseau peut contenir plusieurs règles, alors assurez-vous que vous respectez une convention d’affectation de noms qui vous permet d’identifier la fonction de votre règle|
|**Protocole**|Protocole à faire correspondre pour la règle|TCP, UDP ou *|L’utilisation de * comme protocole inclut ICMP (trafic est-ouest uniquement), ainsi que les protocoles UDP et TCP et peut réduire le nombre de règles dont vous avez besoin<br/>Dans le même temps, l’utilisation de * peut être une approche trop large, alors assurez-vous que vous ne l’utilisez qu’en cas de nécessité|
|**Plage de ports source**|Plage de ports source à faire correspondre pour la règle|Numéro de port unique compris entre 1 et 65535, plage de ports (par exemple, 1-65635) ou * (pour tous les ports)|Les ports source peuvent être éphémères. Privilégiez l’utilisation de « * » dans la plupart des cas, sauf si votre programme client utilise un port spécifique.<br/>Essayez d’utiliser autant de plages de ports que possible afin d’éviter d’avoir plusieurs règles.<br/>Il est impossible de regrouper plusieurs ports ou plages de ports avec une virgule.
|**Plage de ports de destination**|Plage de ports de destination à faire correspondre pour la règle|Numéro de port unique compris entre 1 et 65535, plage de ports (par exemple, 1-65535) ou * (pour tous les ports)|Essayez d’utiliser autant de plages de ports que possible afin d’éviter d’avoir plusieurs règles.<br/>Il est impossible de regrouper plusieurs ports ou plages de ports avec une virgule.
|**Préfixe d’adresse source**|Préfixe d’adresse source à faire correspondre à la règle|Une seule adresse IP (par exemple 10.10.10.10), sous-réseau IP (par exemple, 192.168.1.0/24) [balise par défaut](#default-tags), ou * (pour toutes les adresses)|Envisagez d’utiliser des plages, balises par défaut et * pour réduire le nombre de règles|
|**Préfixe d’adresse de destination**|Préfixe d’adresse de destination ou balise pour faire correspondre la règle|une seule adresse IP (par exemple 10.10.10.10), sous-réseau IP (par exemple, 192.168.1.0/24) [balise par défaut](#default-tags), ou * (pour toutes les adresses)|Envisagez d’utiliser des plages, balises par défaut et * pour réduire le nombre de règles|
|**Direction**|Direction du trafic à faire correspondre pour la règle|entrant ou sortant|Les règles entrantes et sortantes sont traitées séparément, en fonction de la direction|
|**Priorité**|Les règles sont vérifiées dans l’ordre de priorité ; une fois qu’une règle s’applique, plus aucune correspondance de règle n’est testée.|Nombre compris entre 100 et 4096.|Envisagez de créer des règles de passage des priorités par 100 pour chaque règle, de laisser de la place pour les nouvelles règles à venir entre les règles existantes|
|**Access**|Type d'accès à appliquer si la règle correspond|autoriser ou refuser|N’oubliez pas que si la règle d’autorisation d’un paquet est introuvable, le paquet est abandonné|

Les groupes de sécurité réseau contiennent deux ensembles de règles : les règles de trafic entrant et les règles de trafic sortant. La priorité d’une règle doit être unique dans chaque ensemble.

![Traitement des règles de groupe de sécurité réseau](./media/virtual-network-nsg-overview/figure3.png)

La figure ci-dessus illustre le mode de traitement des règles de groupe de sécurité réseau.

### Balises par défaut

Les balises par défaut sont des identificateurs fournis par le système pour adresser une catégorie d'adresses IP. Vous pouvez utiliser les balises par défaut dans les propriétés du **préfixe d’adresse source** et du **préfixe d’adresse de destination** de toute règle. Il existe trois balises par défaut que vous pouvez utiliser.

- **VIRTUAL\_NETWORK :** cette balise par défaut indique tous les espaces d’adressage de votre réseau. Elle inclut l’espace d’adressage du réseau virtuel (plages CIDR définies dans Azure), ainsi que tous les espaces d’adressage local connecté et les réseaux virtuels Azure connectés (réseaux locaux).

- **AZURE\_LOADBALANCER :** cette balise par défaut indique l’équilibreur de charge de l’infrastructure d’Azure. Il convertit en une adresse IP de centre de données Azure l’emplacement d’où proviennent les sondes d’intégrité d’Azure.

- **INTERNET :** cette balise par défaut indique l’espace d’adresse IP qui se trouve en dehors du réseau virtuel et est accessible par l’Internet public. Cette plage inclut [espace IP public d’Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Règles par défaut

Tous les groupes de ressources réseau contiennent un ensemble de règles par défaut. Les règles par défaut ne peuvent pas être supprimées, mais comme la priorité la plus basse leur est attribuée, elles peuvent être remplacées par les règles que vous créez.

Comme illustré par les règles par défaut ci-dessous, le trafic d’origine et de fin d’un réseau virtuel est autorisé à la fois dans les directions entrante et sortante. Tandis que la connectivité à Internet est autorisée pour la direction sortante, elle est bloquée par défaut pour la direction entrante. Il existe une règle par défaut pour autoriser l’équilibreur de charge d’Azure à tester l’intégrité des machines virtuelles et les instances de rôle. Vous pouvez remplacer cette règle si vous n’utilisez pas un ensemble de charges équilibré.

**Les règles par défaut sont :**

| Name | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| AUTORISER LE TRAFIC ENTRANT DU RÉSEAU VIRTUEL | 65 000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | AUTORISER |
| AUTORISER LE TRAFIC ENTRANT DE L'ÉQUILIBREUR DE CHARGE AZURE | 65 001 | AZURE\_LOADBALANCER | * | * | * | * | AUTORISER |
| REFUSER TOUT TRAFIC ENTRANT | 65 500 | * | * | * | * | * | REFUSER |

**Les règles sortantes par défaut sont :**

| Name | Priorité | IP Source | Port source | IP de destination | Port de destination | Protocole | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| AUTORISER LE TRAFIC SORTANT DU RÉSEAU VIRTUEL | 65 000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | AUTORISER |
| AUTORISER LE TRAFIC SORTANT D’INTERNET | 65 001 | * | * | INTERNET | * | * | AUTORISER |
| REFUSER TOUT TRAFIC SORTANT | 65 500 | * | * | * | * | * | REFUSER |

## Association de groupe de sécurité réseau

Vous pouvez associer un groupe de sécurité réseau aux machines virtuelles, aux cartes réseau et sous-réseau, selon le modèle de déploiement que vous utilisez.

[AZURE.INCLUDE [learn-about-deployment-models-both-include.md](../../includes/learn-about-deployment-models-both-include.md)]
 
- **Association d’un groupe de sécurité réseau à une machine virtuelle (uniquement pour les déploiements classiques).** Lorsque vous associez un groupe de sécurité réseau à une machine virtuelle, les règles d’accès réseau du groupe de sécurité réseau sont appliquées à tout le trafic à destination et en provenance de la machine virtuelle.

- **Association d’un groupe de sécurité réseau à une carte réseau (uniquement pour les déploiements de gestionnaire de ressources).** Lorsque vous associez un groupe de sécurité réseau à une carte réseau, les règles d’accès réseau du groupe de sécurité réseau sont appliquées uniquement à cette carte d’interface réseau. Cela signifie que dans une machine virtuelle dotée de plusieurs cartes d’interface réseau, si un groupe de sécurité réseau est appliqué à une seule carte d’interface réseau, il n’affecte pas le trafic lié à d’autres cartes d’interface réseau.

- **Association d’un groupe de sécurité réseau à un sous-réseau et une machine virtuelle**. Lorsqu’un groupe de sécurité réseau est affecté à un sous-réseau, les règles d’accès réseau au sein du groupe de sécurité réseau sont appliquées à toutes les ressources IaaS et PaaS dans le sous-réseau.

Vous pouvez associer différents groupes de sécurité réseau à une machine virtuelle (ou une carte réseau, selon le modèle de déploiement) et le sous-réseau auquel une carte réseau ou la machine virtuelle est liée. Lorsque cela se produit, toutes les règles d’accès réseau sont appliquées au trafic, par ordre de priorité dans chaque groupe de sécurité réseau, dans l’ordre suivant :

- **Trafic entrant**
	1. groupe de sécurité réseau appliqué au sous-réseau
	
           Si le groupe de sécurité réseau du sous-réseau dispose d’une règle de correspondance pour refuser le trafic, le paquet est abandonné ici.
	2. groupe de sécurité réseau appliqué à la carte réseau (Gestionnaire de ressources) ou machine virtuelle (classique).
	   
           Si la machine virtuelle\\groupe de sécurité réseau appliqué à la carte d’interface réseau dispose d’une règle de correspondance pour refuser le trafic, le paquet est abandonné au niveau de la machine virtuelle\\carte d’interface réseau, bien que le groupe de sécurité réseau du sous-réseau dispose d’une règle de correspondance pour autoriser le trafic.
- **Trafic sortant**
	1. groupe de sécurité réseau appliqué à la carte réseau (Gestionnaire de ressources) ou machine virtuelle (classique).
	  
           Si la machine virtuelle\\groupe de sécurité réseau appliqué à la carte d’interface réseau dispose d’une règle de correspondance pour refuser le trafic, le paquet est abandonné ici.
	2. groupe de sécurité réseau appliqué au sous-réseau
	   
           Si le groupe de sécurité réseau du sous-réseau dispose d’une règle de correspondance pour refuser le trafic, le paquet est abandonné ici, bien que la machine virtuelle\\groupe de sécurité réseau appliqué à la carte d’interface réseau dispose d’une règle de correspondance pour autoriser le trafic.

	![ACL de groupe de sécurité réseau](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE] Bien que vous ne puissiez associer qu’un seul groupe de sécurité réseau à un sous-réseau, une machine virtuelle ou une carte d’interface réseau, vous pouvez associer le même groupe de sécurité réseau au nombre de ressources que vous souhaitez.

## Implémentation
Vous pouvez implémenter des groupes de sécurité réseau dans les modèles de déploiement standard ou du Gestionnaire de ressources à l’aide des différents outils répertoriés ci-dessous.

|Outil de déploiement|Classique|Gestionnaire de ressources|
|---|---|---|
|Portail classique|![Non](./media/virtual-network-nsg-overview/red.png)|![Non](./media/virtual-network-nsg-overview/red.png)|
|Portail Azure|![Oui](./media/virtual-network-nsg-overview/green.png)|[![Oui][green]](virtual-networks-create-nsg-arm-pportal.md)|
|PowerShell|[![Oui][green]](virtual-networks-create-nsg-classic-ps.md)|[![Oui][green]](virtual-networks-create-nsg-arm-ps.md)|
|Interface de ligne de commande Azure|[![Oui][green]](virtual-networks-create-nsg-classic-cli.md)|[![Oui][green]](virtual-networks-create-nsg-arm-cli.md)|
|Modèle ARM|![Non](./media/virtual-network-nsg-overview/red.png)|[![Oui][green]](virtual-networks-create-nsg-arm-template.md)|

|**Clé**|![Oui](./media/virtual-network-nsg-overview/green.png) Pris en charge.|![Non](./media/virtual-network-nsg-overview/red.png) Non pris en charge.|
|---|---|---|

## Planification

Avant d’implémenter des groupes de sécurité réseau, vous devez répondre aux questions ci-dessous :

1. Quels sont les types de ressources depuis ou vers lesquels vous voulez filtrer le trafic (cartes réseau dans la même machine virtuelle, machines virtuelles ou autres ressources telles que les services de cloud ou des environnements de service d’application connectées au même sous-réseau, ou entre les ressources connectées à différents sous-réseaux) ?

2. Les ressources vers ou depuis lesquelles vous voulez filtre le trafic à partir de sous-réseau dans les réseaux virtuels existants sont-elles connectées à des réseaux dans des réseaux virtuels existants ou seront-elles connectées à de nouveaux réseaux virtuels ou sous-réseaux ?
 
Pour plus d’informations sur la planification de la sécurité réseau dans Azure, consultez les [pratiques recommandées pour les services cloud et la sécurité réseau](../best-practices-network-security.md).

## Remarques relatives à la conception

Une fois que vous connaissez les réponses aux questions dans la section [Planification](#Planning), consultez les rubriques suivantes avant de définir vos groupes de sécurité réseau.

### Limites

Vous devez tenir compte des limites suivantes en concevant vos groupes de sécurité réseau.

|**Description**|**Limite par défaut**|**Implications**|
|---|---|---|
|Nombre de groupes de sécurité réseau que vous pouvez associer à un sous-réseau, une machine virtuelle ou une carte réseau|1|Cela signifie que vous ne pouvez pas combiner des groupes de sécurité réseau. Vérifiez que toutes les règles nécessaires à un ensemble donné de ressources sont incluses dans un seul groupe de sécurité réseau.|
|Groupes de sécurité réseau par région et par abonnement|100|Par défaut, un nouveau groupe de sécurité réseau est créé pour chaque machine virtuelle que vous créez dans le portail Azure. Si vous autorisez ce comportement par défaut, épuiserez rapidement vos groupes de sécurité réseau. Veillez à garder cette limite à l’esprit pendant votre conception et à séparer vos ressources en plusieurs régions ou abonnements si nécessaire. |
|Règles de groupe de sécurité réseau par groupe de sécurité réseau|200|Utiliser une large plage d’adresses IP et ports pour vous garantir de ne pas aller au-delà de cette limite. |

>[AZURE.IMPORTANT] Assurez-vous que vous pouvez afficher toutes les [limites liées aux services de mise en réseau dans Azure](../azure-subscription-service-limits.md#networking-limits) avant de concevoir votre solution. Il est possible d’augmenter certaines limites par le biais d’un ticket d’assistance.

### Conception de réseau virtuel et de sous-réseau

Comme les groupes de sécurité réseau peuvent être appliqués à des sous-réseaux, vous pouvez réduire le nombre de groupes de sécurité réseau en regroupant vos ressources par sous-réseau et en appliquant des groupes de sécurité réseau aux sous-réseaux. Si vous décidez d’appliquer des groupes de sécurité réseau à des sous-réseaux, il se peut que vous trouviez des réseaux virtuels et les sous-réseaux existants qui n’ont pas été définis avec des groupes de sécurité réseau à l’esprit. Vous pouvez peut-être définir de nouveaux réseaux virtuels et sous-réseaux pour prendre en charge votre conception groupe de sécurité réseau. Et déployer vos nouvelles ressources sur vos nouveaux sous-réseaux. Vous pouvez ensuite définir une stratégie de migration pour déplacer des ressources existantes vers les nouveaux sous-réseaux.

### Règles spéciales

Vous devez prendre en compte le compte des règles spéciales répertoriées ci-dessous. Assurez-vous que vous ne bloquez pas le trafic autorisé par ces règles, sinon votre infrastructure ne sera pas en mesure de communiquer avec des services Azure essentiels.

- **Adresse IP virtuelle du nœud hôte :** des services d’infrastructure de base tels que DHCP, DNS et l’analyse du fonctionnement sont fournis via l'adresse IP d’hôte virtualisé 168.63.129.16. Cette adresse IP publique appartient à Microsoft et la seule adresse IP virtualisée utilisée dans toutes les régions à cet effet. Cette adresse IP mappe vers l'adresse IP physique de l’ordinateur (nœud hôte) du serveur qui héberge la machine virtuelle. Le nœud hôte agit en tant que relais DHCP, le programme de résolution récursif DNS et la sonde source de la sonde d’intégrité de l’équilibreur de charge et de la sonde d’intégrité de la machine. La communication à cette adresse IP ne doit pas être considérée comme une attaque.

- **Gestion des licences (service de gestion de clés) :** les images Windows en cours d'exécution sur les machines virtuelles doivent être acquises sous licence. Pour cela, une demande de licence est envoyée aux serveurs hôtes du service de gestion de clés qui gèrent ces requêtes. Ce sera toujours sur le port 1688 sortant.

### Trafic ICMP

Les règles de groupe de sécurité réseau actuelles autorisent uniquement les protocoles *TCP* ou *UDP*. Il n’existe aucune balise spécifique pour *ICMP*. Toutefois, le trafic ICMP est autorisé dans un réseau virtuel par défaut via les règles de trafic entrant du réseau virtuel (règle de trafic entrant par défaut 65000) qui autorisent le trafic de/vers n’importe quel port et protocole dans le réseau virtuel.

### Sous-réseaux

- Tenez compte du nombre de niveaux que requiert votre charge de travail. Chaque niveau peut être isolé à l’aide d’un sous-réseau, avec un groupe de sécurité réseau appliqué au sous-réseau.
- Si vous avez besoin d’implémenter un sous-réseau de passerelle VPN ou de circuit ExpressRoute, vous devez vous assurer de ne **PAS** appliquer un groupe de sécurité réseau à ce sous-réseau. Si vous procédez ainsi, votre connectivité inter réseau ou entre sites ne fonctionnera pas.
- Si vous avez besoin d’implémenter un équipement virtuel, assurez-vous de déployer l’application virtuelle sur son propre sous-réseau, de sorte que votre propre UDR puisse fonctionner correctement. Vous pouvez implémenter un niveau de sous-réseau groupe de sécurité réseau pour filtrer le trafic vers et depuis ce sous-réseau. En savoir plus sur [comment contrôler le flux de trafic et utiliser des équipements virtuels](virtual-networks-udr-overview.md).

### Équilibreurs de charge

- Envisagez l’équilibrage de charge et les règles NAT pour chaque équilibreur de charge utilisé par chacune de vos charges de travail. Ces règles sont liées à un pool principal qui contient des cartes réseau (déploiements de Gestionnaire de ressources) ou machines virtuelles/instances de rôle (déploiements classiques). Envisagez de créer un groupe de sécurité réseau pour chaque pool principal, ce qui permet uniquement le trafic mappé via les règles implémentées dans les équilibreurs de charge. Cela garantit que le trafic entrant directement vers le pool principal sans passer par l’équilibreur de charge est également filtré.
- Dans les déploiements classiques, vous pouvez créer des points de terminaison qui mappent des ports d’un équilibreur de charge sur des ports sur vos machines virtuelles ou instances de rôle. Vous pouvez également créer votre propre équilibreur de charge public individuel dans un déploiement du Gestionnaire de ressources. Si vous limitez le trafic aux machines virtuelles et instances de rôle qui font partie d’un pool de serveur principal dans un équilibreur de charge à l’aide de groupes de sécurité réseau, n’oubliez pas que le port de destination pour le trafic entrant est le port réel de la machine virtuelle ou de l’instance de rôle, et non le port exposé par l’équilibreur de charge. Gardez à l’esprit que le port source et l’adresse de connexion à la machine virtuelle sont un port et une adresse sur l’ordinateur distant sur Internet, pas le port et l’adresse exposés par l’équilibreur de charge.
- Comme en cas de port public présenté aux équilibreurs de charge, lorsque vous créez des groupes de sécurité réseau pour filtrer le trafic provenant d’un équilibreur de charge interne, vous devez comprendre que le port source et la plage d’adresses appliqués sont ceux de l’ordinateur à l’origine de l’appel, et pas l’équilibreur de charge. Et le port de destination et la plage d’adresses sont associés à l’ordinateur recevant le trafic, et non à l’équilibreur de charge.

### Autres

- Les contrôles d’accès réseau basés sur le point de terminaison et les groupes de sécurité réseau ne sont pas pris en charge sur la même instance de machine virtuelle. Si vous souhaitez utiliser un groupe de sécurité réseau et une ACL de point de terminaison déjà en place, supprimez d'abord l’ACL de point de terminaison. Pour plus d’informations, consultez [Gérer les ACL de point de terminaison](virtual-networks-acl-powershell.md).
- Dans le modèle de déploiement de gestionnaire de ressources, vous pouvez utiliser un groupe de sécurité réseau associé à une carte réseau pour les machines virtuelles contenant plusieurs cartes réseau pour activer la gestion (accès à distance) par carte réseau, ce qui revient à séparer le trafic.
- Comme en cas d’utilisation des équilibreurs de charge, lorsque vous filtrez le trafic vers d’autres réseaux virtuels, vous devez utiliser la plage d’adresses source de l’ordinateur distant, et non la passerelle qui connecte les réseaux virtuels.
- De nombreux services Azure ne peuvent pas être connectés aux réseaux virtuels Azure et donc, le trafic depuis et vers ces derniers ne peuvent être filtrés à l’aide des groupes de sécurité réseau. Lisez la documentation des services vous permettant de déterminer si oui ou non ils peuvent être connectés à des réseaux virtuels.

## Exemple de déploiement

Pour montrer l’application des informations dans cet article, nous allons définir des groupes de sécurité réseau pour filtrer le trafic réseau d’une solution de charge de travail à deux niveaux avec les spécifications suivantes :

1. Séparation du trafic entre le serveur frontal (serveurs web Windows) et le serveur principal (serveurs de base de données SQL).
2. Règles d’équilibrage de charge du transfert de trafic pour l’équilibreur de charge sur tous les serveurs web sur le port 80.
3. Les règles NAT de transfert de trafic arrivant au port 50001 de l’équilibreur de charge vers le port 3389 une seule machine virtuelle dans la partie frontale.
4. Aucun accès aux machines virtuelles frontales ou principales depuis Internet, à l’exception du numéro de la spécification 1.
5. Aucun accès à partir des serveurs frontaux ou principaux vers Internet.
6. Accès au port 3389 vers n’importe quel serveur frontal, le trafic entrant du sous-réseau frontal lui-même.
7. Accès au port 3389 pour toutes les machines virtuelles SQL Server dans le sous-réseau entre le réseau principal et le réseau frontal uniquement.
8. Accès au port 1433 pour toutes les machines virtuelles SQL Server dans le sous-réseau entre le réseau principal et le réseau frontal uniquement.
9. Séparation du trafic de gestion (port 3389) et du trafic de base de données (1433) sur les différentes cartes réseau dans les machines virtuelles principales.

![Groupes de sécurité réseau](./media/virtual-network-nsg-overview/figure1.png)

Comme indiqué dans le diagramme ci-dessus, les machines virtuelles *Web1* et *Web2* sont connectées au sous-réseau *frontal* et les machines virtuelles *DB1* et *DB2* sont connectées au sous-réseau *principal*. Les deux sous-réseaux font partie du réseau virtuel *TestVNet*. Toutes les ressources sont affectées à la région Azure *Ouest des États-Unis*.

Les configurations requises 1 à 6 (à l’exception de 3) ci-dessus sont limitées aux espaces de sous-réseau confinés. Pour réduire le nombre de règles requises pour chaque groupe de sécurité réseau, et pour faciliter l’ajout de machines virtuelles supplémentaires aux sous-réseaux exécutant les mêmes types de charge de travail que les machines virtuelles, nous pouvons mettre en œuvre le niveau de sous-réseau suivant.

### Groupe de sécurité réseau pour le sous-réseau frontal

**Règles de trafic entrant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|Autoriser HTTP|Autoriser|100|INTERNET|*|*|80|TCP|
|Autoriser RDP à partir du serveur frontal|Autoriser|200|192\.168.1.0/24|*|*|3389|TCP|
|refuser tout élément en provenance d’Internet|Deny|300|INTERNET|*|*|*|TCP|

**Règles de trafic sortant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|refuser Internet|Deny|100|*|*|INTERNET|*|*|

### Groupe de sécurité réseau pour le sous-réseau principal

**Règles de trafic entrant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|refuser Internet|Deny|100|INTERNET|*|*|*|*|

**Règles de trafic sortant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|refuser Internet|Deny|100|*|*|INTERNET|*|*|

### Groupe de sécurité réseau pour machine virtuelle (carte réseau) dans un serveur frontal pour RDP depuis Internet

**Règles de trafic entrant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|Autoriser RDP à partir d’Internet|Autoriser|100|INTERNET|*|\*|3389|TCP|

>[AZURE.NOTE] Notez que la plage d’adresses source pour cette règle est **Internet**, et non l’adresse IP virtuelle de l’équilibreur de charge et le port source **\***, pas 500001. Ne confondez pas les règles NAT/règles d’équilibre de charge et règles de groupe de sécurité réseau. Les règles du groupe de sécurité réseau sont toujours associées à la source d’origine et la destination finale du trafic, et **PAS** à l’équilibreur de charge entre les deux.

### Le groupe de sécurité réseau pour la gestion des cartes réseau dans le serveur principal

**Règles de trafic entrant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|Autoriser RDP à partir du serveur frontal|Autoriser|100|192\.168.1.0/24|*|\*|3389|TCP|

### Groupe de sécurité réseau pour la gestion des cartes réseau dans le serveur principal

**Règles de trafic entrant**

|Règle|Access|Priorité|Plage d’adresses source|Port source|Plage d’adresses de destination|Port de destination|Protocole|
|---|---|---|---|---|---|---|---|
|Autoriser SQL à partir du serveur frontal|Autoriser|100|192\.168.1.0/24|*|\*|1433|TCP|

Étant donné que certains des groupes de sécurité réseau ci-dessus doivent être associés à des cartes réseau individuelles, vous devez déployer ce scénario en tant que déploiement de gestionnaire de ressources. Notez comment les règles sont combinées au niveau du sous-réseau et de la carte réseau, selon la façon dont ils doivent être appliqués.

## Étapes suivantes

- [Déploiement des groupes de sécurité réseau dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).
- [Déploiement des groupes de sécurité réseau dans Resource Manager](virtual-networks-create-nsg-arm-pportal.md).
- [Gestion des journaux de groupe de sécurité réseau](virtual-network-nsg-manage-log.md).

[green]: ./media/virtual-network-nsg-overview/green.png
[yellow]: ./media/virtual-network-nsg-overview/yellow.png
[red]: ./media/virtual-network-nsg-overview/red.png

<!-----HONumber=AcomDC_0907_2016-->