<properties
	pageTitle="Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery dans le portail Azure | Microsoft Azure"
	description="Décrit comment déployer Azure Site Recovery pour orchestrer la réplication, le basculement et la récupération de machines virtuelles VMware ou des serveurs physiques Windows/Linux locaux vers Azure avec le portail Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="raynew"/>

# Répliquer des machines virtuelles VMware et des machines physiques sur Azure avec Azure Site Recovery par le biais du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmware-to-azure.md)
- [Azure Classic](site-recovery-vmware-to-azure-classic.md)
- [Azure Classic (version héritée)](site-recovery-vmware-to-azure-classic-legacy.md)

Bienvenue dans Azure Site Recovery ! Cet article vous permet de répliquer des machines virtuelles VMware locales ou des serveurs physiques Windows/Linux sur Azure à l’aide d’Azure Site Recovery, sur le portail Azure.

> [AZURE.NOTE] Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour créer et utiliser des ressources : Azure Resource Manager et le modèle classique. De plus, Azure propose deux portails : le portail Azure Classic, qui prend en charge le modèle de déploiement classique, et le portail Azure, qui gère les deux modèles de déploiement.

Sur le portail Azure, Site Recovery fournit plusieurs nouvelles fonctionnalités :

- Les services Azure Backup et Azure Site Recovery sont combinés en un seul et même coffre Recovery Services, afin de vous permettre de configurer et de gérer les fonctions de récupération d’urgence et de continuité d’activité (BCDR) à partir d’un emplacement unique. Dans le tableau de bord unifié, vous pouvez surveiller et gérer des opérations sur vos sites locaux et le cloud public Azure.
- Les utilisateurs dotés d’abonnements Azure configurés avec le programme du fournisseur de solutions cloud (CSP) peuvent désormais gérer les opérations Site Recovery dans le Portail Azure.
- Dans le portail Azure, Site Recovery peut répliquer des machines sur des comptes de stockage ARM. Lors du basculement, le logiciel Site Recovery crée des machines virtuelles basées sur ARM dans Azure.
- Il continue de prendre en charge la réplication sur les comptes de stockage classiques. Lors du basculement, Site Recovery crée des machines virtuelles en utilisant le modèle classique.

Après avoir lu cet article, n’hésitez pas à poster un commentaire dans la partie inférieure de la page, dans la section des commentaires de Disqus. Publiez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Vue d'ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Votre stratégie BCDR doit assurer la sécurisation et la récupération des données d’entreprise, ainsi que la disponibilité continue des charges de travail suite à un sinistre.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que le service Azure Site Recovery ?](site-recovery-overview.md).

Cet article décrit toutes les informations dont vous avez besoin pour répliquer des machines virtuelles VMware et des serveurs physiques Windows/Linux locaux sur Azure. Il inclut une présentation architecturale et des informations sur la planification, ainsi que les étapes de déploiement pour la configuration d’Azure, les serveurs locaux, les paramètres de réplication et la planification de capacité. Une fois l’infrastructure configurée, vous pouvez activer la réplication sur les machines que vous souhaitez protéger et vérifier que le basculement fonctionne correctement.

## Avantages pour l’entreprise

- Site Recovery assure la protection hors site des charges de travail et des applications professionnelles qui s’exécutent sur des machines virtuelles VMware et des serveurs physiques.
- Le portail Recovery Services offre en emplacement unique pour la configuration, la gestion et la surveillance de la réplication, du basculement et de la récupération.
- Site Recovery découvre automatiquement les machines virtuelles VMware ajoutées aux hôtes vSphere.
- Vous pouvez facilement exécuter des basculements de l’infrastructure locale vers Azure, et assurer la restauration automatique d’Azure vers des serveurs de machine virtuelle VMware sur votre site local.
- Vous pouvez activer plusieurs machines virtuelles et créer des groupes de réplication afin que les charges de travail des applications hiérarchisées sur plusieurs machines se répliquent en même temps. Tous les ordinateurs d’un groupe de réplication ont des points de récupération cohérents après incident et avec les applications lorsqu’ils basculent. Pour le basculement, vous pouvez rassembler plusieurs machines dans des plans de récupération afin que les charges de travail d’applications hiérarchisées basculent ensemble.

## Systèmes d'exploitation pris en charge

### Windows (64 bits uniquement)
- Windows Server 2008 R2 SP1+
- Windows Server 2012
- Windows Server 2012 R2

### Linux (64 bits uniquement)
- Red Hat Enterprise Linux 6.7, 7.1, 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## Architecture du scénario

Voici les composants du scénario :

- **Serveur de configuration** : machine locale qui coordonne la communication et gère les processus de réplication et de récupération des données. Sur cette machine, vous allez exécuter un fichier d’installation unique afin d’installer le serveur de configuration et ces composants supplémentaires :
	- **Serveur de processus** : fait office de passerelle de réplication. Il reçoit les données de réplication des ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement, et les envoie vers Azure Storage. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware. Le serveur de processus par défaut est installé sur le serveur de configuration. Vous pouvez déployer des serveurs de processus autonomes supplémentaires pour étendre votre déploiement.
	- **Serveur cible maître** : gère les données de réplication pendant la restauration automatique à partir d’Azure.

- **Service Mobilité** : ce composant est déployé sur chaque machine (machine virtuelle VMware ou serveur physique) que vous souhaitez répliquer vers Azure. Il enregistre les écritures de données sur la machine et les transmet au serveur de traitement.
- **Azure** : vous n’avez pas besoin de créer de machines virtuelles Azure pour gérer la réplication et le basculement sur Azure. Il vous faut un abonnement à Azure, un compte de stockage Azure pour le stockage des données répliquées et un réseau virtuel Azure afin que les machines virtuelles Azure puissent être connectées à un réseau après un basculement. Le compte de stockage et le réseau doivent se trouver dans la même région que le coffre Recovery Services.
- **Restauration automatique** : lorsque vous êtes prêt à effectuer une restauration automatique d’Azure sur votre site local après un basculement, vous devez créer une machine virtuelle Azure qui jouera le rôle de serveur de processus temporaire. Vous pourrez le supprimer une fois la restauration automatique terminée. Pour la restauration automatique, vous avez également besoin d’une connexion VPN (ou Azure ExpressRoute) entre votre site local et le réseau Azure sur lequel se trouvent vos machines virtuelles Azure. Si le trafic de la restauration automatique est important, vous devrez peut-être aussi définir un serveur cible maître dédié en local. Si le trafic est moins important, il est possible d’utiliser le serveur cible maître par défaut qui s’exécute sur le serveur de configuration.


Le graphique montre comment ces composants interagissent.

![architecture](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figure 1 : VMware/Physique vers Azure**

## Conditions préalables pour Azure

Voici les éléments requis pour vous permettre de déployer ce scénario dans Azure.

**Configuration requise** | **Détails**
--- | ---
**Compte Azure**| Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer par une version d’[essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.
**Stockage Azure** | Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement. <br/><br/>Pour stocker des données, vous avez besoin d’un compte Standard Storage ou Premium Storage qui se trouve dans la même région que le coffre Recovery Services.<br/><br/>Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée. [En savoir plus](../storage/storage-redundancy.md).<br/><br/> [Premium Storage](../storage/storage-premium-storage.md) est généralement utilisé pour les machines virtuelles nécessitant des performances d’E/S élevées et une faible latence pour héberger les charges de travail nécessitant beaucoup d’E/S.<br/><br/> Si vous souhaitez utiliser un compte Premium pour stocker les données répliquées, vous avez aussi besoin d’un compte Standard Storage afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.<br/><br/> Les comptes de stockage créés dans le portail Azure ne peuvent pas passer d’un groupe de ressources à l’autre. Par ailleurs, la protection des comptes Premium Storage au Centre de l’Inde et en Inde du Sud n’est actuellement pas prise en charge.<br/><br/> [En savoir plus](../storage/storage-introduction.md) sur Azure Storage.
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront au moment du basculement. Le réseau virtuel Azure doit se trouver dans la même région que le coffre Recovery Services.
**Restauration automatique à partir de Microsoft Azure** | Vous avez besoin d’un serveur de processus temporaire qui sera configuré comme une machine virtuelle Azure. Vous pouvez le créer lorsque vous êtes prêt à effectuer une restauration automatique, et le supprimer une fois la restauration automatique terminée.<br/><br/> Pour la restauration automatique, vous avez besoin d’une connexion VPN (ou Azure ExpressRoute) configurée à partir du réseau Azure sur le site local.

## Configuration requise du serveur

Vous allez définir une machine locale en tant que serveur de configuration.

**Configuration requise** | **Détails**
--- | ---
**Serveur de configuration**| Vous avez besoin d’un serveur physique ou d’une machine virtuelle en local, qui exécute Windows Server 2012 R2. Tous les composants Site Recovery locaux sont installés sur cette machine<br/><br/> Pour la réplication de machine virtuelle VMware, nous vous conseillons de déployer le serveur en tant que machine virtuelle VMware hautement disponible. Si vous répliquez des machines physiques, la machine peut être un serveur physique.<br/><br/> La restauration automatique vers le site local à partir d’Azure doit toujours s’effectuer vers des machines virtuelles VMware, que vous ayez basculé sur des machines virtuelles ou des serveurs physiques. Si vous ne déployez pas le serveur de configuration en tant que machine virtuelle VMware, vous devez configurer un serveur cible maître distinct en tant que machine virtuelle VMware pour recevoir le trafic de la restauration automatique.<br/><br/>Si le serveur est une machine virtuelle VMware, la carte réseau doit être de type VMXNET3. Si vous utilisez un autre type de carte réseau, vous devez installer une [mise à jour de VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) sur le serveur vSphere 5.5.<br/><br/>Le serveur doit avoir une adresse IP statique.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le nom d’hôte du serveur doit comporter 15 caractères au maximum.<br/><br/>Le système d’exploitation ne doit être qu’en anglais.<br/><br/> Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur de configuration.<br/><br/>Le serveur de configuration nécessite un accès à Internet. L’accès sortant suivant est requis :<br/><br/>Accès temporaire sur HTTP 80 pendant l’installation des composants de Site Recovery (pour télécharger MySQL)<br/><br/>Accès sortant permanent sur HTTPS 443 pour la gestion de la réplication<br/><br/>Accès sortant permanent sur HTTPS 9443 pour le trafic de réplication (port modifiable)<br/><br/>Le serveur devra aussi accéder aux URL suivantes pour pouvoir se connecter à Azure : *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net ; *.backup.windowsazure.com ; *.blob.core.windows.net ; *.store.core.windows.net<br/><br/>Si vous avez des règles de pare-feu fondées sur l’adresse IP sur le serveur, vérifiez que ces règles autorisent la communication vers Azure. Vous devez autoriser les [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443).<br/><br/>Autorisez les plages d’adresses IP de la région Azure de votre abonnement et de l’Ouest des États-Unis.<br/><br/>Autorisez cette URL pour le téléchargement de MySQL : http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## Configuration requise de l’hôte VMware vCenter/vSphere

**Configuration requise** | **Détails**
--- | ---
**vSphere**| Vous avez besoin d’un ou de plusieurs hyperviseurs VMware vSphere.<br/><br/>Les hyperviseurs doivent exécuter vSphere version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/>Nous vous recommandons de placer les hôtes vSphere et les serveurs vCenter sur le même réseau que le serveur de processus (le réseau dans lequel se trouve le serveur de configuration, sauf si vous avez configuré un serveur dédié).
**vCenter** | Nous vous recommandons de déployer un serveur VMware vCenter pour gérer vos hôtes vSphere. Ce dernier doit exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que Site Recovery ne prend pas en charge les nouvelles fonctionnalités de vCenter et vSphere 6.0, telles que Cross vCenter vMotion, les volumes virtuels et Storage DRS. La prise en charge de Site Recovery est limitée aux fonctionnalités qui étaient déjà disponibles dans la version 5.5.


## Configuration requise pour les machines protégées

**Configuration requise** | **Détails**
--- | ---
**Machines virtuelles VMware locales** | Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles VMware à protéger.<br/><br/> Les machines que vous souhaitez protéger doivent être conformes à la [configuration requise d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création de machines virtuelles Azure.<br/><br/>Sur les machines protégées, la capacité d’un disque ne doit pas dépasser 1 023 Go. Une machine virtuelle peut comporter jusqu’à 64 disques (jusqu’à 64 To). <br/><br/>La protection des machines virtuelles avec disques chiffrés (racine ou données) n’est pas prise en charge.

Les clusters invités de disques partagés ne sont pas pris en charge.<br/><br/>Le **port 20004** doit être ouvert sur les machines virtuelles protégées si vous souhaitez assurer la **cohérence des applications**.

L’amorçage UEFI (Unified Extensible Firmware Interface) / EFI (Extensible Firmware Interface) n’est pas pris en charge.<br/><br/>Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Après avoir activé la réplication d’une machine, vous pouvez modifier le nom Azure.<br/><br/>Si l’association de cartes réseau est activée sur la machine virtuelle source, elle est convertie en carte réseau unique après le basculement vers Azure.<br/><br/>Si les machines virtuelles protégées disposent d’un disque iSCSI, Site Recovery convertit le disque iSCSI de machine virtuelle en un fichier VHD lors du basculement de la machine virtuelle vers Azure. Si la cible iSCSI peut être atteinte par la machine virtuelle Azure, elle s’y connecte et peut voir essentiellement deux disques : le disque dur virtuel de la machine virtuelle Azure et le disque source iSCSI. Dans ce cas, vous devez vous déconnecter de la cible iSCSI qui s’affiche sur la machine virtuelle Azure ayant basculé. **Machines Windows (physiques ou VMware)** | La machine doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, avec au moins SP1.<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\\. Le disque du système d’exploitation doit être un disque de base Windows, et non un disque dynamique. Le disque de données peut être dynamique.<br/><br/>Site Recovery prend en charge les machines virtuelles équipées d’un disque RDM. Lors de la restauration automatique, Site Recovery réutilise le disque RDM si le disque de machine virtuelle source et le disque RDM d’origine sont disponibles. Dans le cas contraire, Site Recovery crée un fichier VMDK pour chaque disque au cours de la restauration automatique. **Machines Linux** | Vous avez besoin d’un système d’exploitation 64 bits pris en charge : Red Hat Enterprise Linux 6.7, 7.1, 7.2 ; CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 ; Oracle Enterprise Linux 6.4, 6.5 exécutant un noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Les fichiers /etc/hosts sur les machines protégées doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau.<br/><br/>Si vous souhaitez vous connecter à une machine virtuelle Azure sous Linux après un basculement à l’aide d’un client Secure Shell (SSH), assurez-vous que le service Secure Shell sur la machine protégée est configuré pour se lancer automatiquement au démarrage du système et que les règles de pare-feu autorisent une connexion SSH à ce dernier.<br/><br/>Le nom d’hôte, les points de montage, les noms d’appareil et les chemins d’accès système et noms de fichiers Linux (par exemple, /etc/ ; /usr) ne doivent être qu’en anglais.<br/><br/>La protection ne peut être activée que pour les machines Linux avec le stockage suivant : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS n’est pris en charge que sur SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery prend en charge les machines virtuelles avec un disque RDM. Pendant la restauration automatique de Linux, Site Recovery ne réutilise pas le disque RDM. Il crée à la place un fichier VMDK pour chaque disque RDM correspondant.<br/><br/>Vérifiez que vous avez défini le paramètre disk.enableUUID=true dans la configuration de la machine virtuelle dans VMware. Créez l’entrée si elle n’existe pas. Cette opération est nécessaire pour fournir un UUID cohérent au VMDK et lui assurer ainsi un montage correct. L’ajout de ce paramètre garantit également que seules les modifications différentielles sont retransférées en local pendant la restauration automatique, et non la réplication complète. **Service de Mobilité** | **Windows** : pour transmettre automatiquement le service Mobilité aux machines virtuelles Windows, vous devez fournir un compte d’administrateur (administrateur local sur la machine Windows) afin que le serveur de processus puisse effectuer une installation Push.<br/><br/> **Linux** : pour transmettre automatiquement le service Mobilité aux machines virtuelles exécutant Linux, vous devez créer un compte utilisable par le serveur de processus afin d’effectuer une installation Push.<br/><br/> Par défaut, tous les disques d’une machine sont répliqués. Pour [exclure un disque de la réplication](#exclude-disks-from-replication), le service Mobilité doit être installé manuellement sur la machine avant l’activation de la réplication.

## Préparation du déploiement

Pour préparer un déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) sur lequel les machines virtuelles Azure sont placées lorsqu’elles sont démarrées après un basculement. De plus, pour une restauration automatique, vous devez disposer d’une connexion VPN (ou d’Azure ExpressRoute) configurée à partir du réseau Azure sur votre site local.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données répliquées.
3. [Préparer un compte](#prepare-an-account-for-automatic-discovery) sur le serveur vCenter ou les hôtes vSphere afin que Site Recovery puisse détecter automatiquement les machines virtuelles VMware ajoutées.
4. [Préparer le serveur de configuration](#prepare-the-configuration-server) afin qu’il puisse accéder aux URL requises et installer vSphere PowerCLI 6.0.


### Configurer un réseau Azure

- Ce réseau doit se trouver dans la même région Azure que celui dans lequel vous allez déployer le coffre Recovery Services.
- Selon le modèle de ressource que vous souhaitez utiliser pour le basculement des machines virtuelles Azure, vous allez configurer le réseau Azure en [mode ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou en [mode Classic](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Pour effectuer une restauration automatique d’Azure vers votre site VMware local, vous avez besoin d’une connexion VPN (ou d’une connexion Azure ExpressRoute) entre le réseau Azure où se trouvent les machines virtuelles Azure répliquées et le réseau local où se trouve le serveur de configuration.
- [Découvrez](../vpn-gateway/vpn-gateway-site-to-site-create.md) les modèles de déploiement pris en charge pour les connexions VPN de site à site et apprenez à [configurer une connexion](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Vous pouvez également configurer [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [Découvrez](../expressroute/expressroute-howto-vnet-portal-classic.md) comment configurer un réseau Azure avec ExpressRoute.

> [AZURE.NOTE] [Migration of networks](../ressources-groupe-move-resources.md) entre les groupes de ressources d’un même abonnement ou de plusieurs abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de Site Recovery.

### Configurer un compte Azure Storage

- Vous avez besoin d’un compte Azure Standard Storage ou Premium Storage pour stocker les données répliquées sur Azure. Ce compte doit se trouver dans la même région que le coffre Recovery Services. Selon le modèle de ressource que vous souhaitez utiliser pour le basculement des machines virtuelles Azure, vous allez configurer un compte en [mode ARM](../storage/storage-create-storage-account.md) ou en [mode Classic](../storage/storage-create-storage-account-classic-portal.md).
- Si vous utilisez un compte Premium pour les données répliquées, vous devez créer un compte Standard supplémentaire afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.

> [AZURE.NOTE] [Migration of storage accounts](../ressources-groupe-move-resources.md) entre les groupes de ressources d’un même abonnement ou de plusieurs abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de Site Recovery.

### Préparer un compte pour la découverte automatique

Le serveur de processus Site Recovery peut découvrir automatiquement les machines virtuelles VMware sur les hôtes vSphere ou sur un serveur vCenter qui gère les hôtes. Pour la découverte automatique, Site Recovery a besoin d’informations d’identification permettant d’accéder au serveur VMware. Cela n’est pas utile si vous répliquez uniquement des machines physiques.

1. Pour utiliser un compte dédié à la découverte automatique, créez un rôle (par exemple, Azure\_Site\_Recovery) au niveau du serveur vCenter, avec les [autorisations requises](#vmware-account-permissions).
2. Créez un utilisateur sur l’hôte vSphere ou vCenter et attribuez le rôle à l’utilisateur. Vous communiquerez ensuite à Site Recovery ces informations d’identification pour permettre la découverte automatique.

	>[AZURE.NOTE] Un compte d’utilisateur vCenter doté d’un rôle en lecture seule peut exécuter le basculement mais il ne peut pas arrêter les machines source protégées. Si vous souhaitez arrêter les machines virtuelles, vous aurez besoin du rôle [Azure\_Site\_Recovery](#vmware-account-permissions). Si vous ne faites que migrer des machines virtuelles de VMware vers Azure sans restauration automatique, le rôle en lecture seule est suffisant.

### Préparer le serveur de configuration

1.	Vérifiez que la machine utilisée pour le serveur de configuration est conforme à la [configuration requise](#configuration-server-prerequisites). Vérifiez que la machine est bien connectée à Internet conformément aux paramètres suivants :

	- Autorisez l’accès à ces URL: *.hypervrecoverymanager.windowsazure.com ; *.accesscontrol.windows.net ; *.backup.windowsazure.com ; *.blob.core.windows.net ; *.store.core.windows.net
	- Autorisez l’accès à [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) pour télécharger MySQL.
	- Autorisez la communication du pare-feu vers Azure avec les [plages IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443).

2.	Téléchargez et installez [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) sur le serveur de configuration. (Actuellement, les autres versions de PowerCLI ne sont pas prises en charge, y compris les versions R 6.0.)


## Créer un coffre Recovery Services

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **Gestion** > **Sauvegarde et récupération de sites (OMS)**. Vous pouvez également cliquer sur **Parcourir** > **Coffre Recovery Services** > **Ajouter**.

	![Nouveau coffre](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Dans **Nom**, spécifiez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez-en un.
4. [Créez un groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en un. Spécifiez une région Azure. Les machines seront répliquées dans cette région. Azure Storage et les réseaux utilisés pour Site Recovery doivent se trouver dans la même région. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

	![Nouveau coffre](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

Le nouveau coffre s’affiche dans la zone **Tableau de bord** > **Toutes les ressources**, et dans le panneau **Coffres Recovery Services** principal.

## Prise en main

Site Recovery propose une expérience de prise en main, qui vous permet d’être très vite opérationnel. Il vérifie les conditions préalables et vous guide tout au long des étapes de déploiement de Site Recovery.

Vous pouvez sélectionner le type de machine que vous souhaitez répliquer, voire l’endroit auquel vous souhaitez effectuer la réplication. Vous configurez l’infrastructure, y compris les serveurs locaux, les paramètres Azure, les stratégies de réplication et la planification de la capacité. Une fois l’infrastructure en place, vous activez la réplication des machines virtuelles et des serveurs physiques. Vous pouvez ensuite exécuter le basculement de machines spécifiques ou créer des plans de récupération pour effectuer le basculement de plusieurs machines.

Lancez la fonction Prise en main en sélectionnant le mode de déploiement de Site Recovery. Le flux de la fonction Prise en main change légèrement selon vos exigences en matière de réplication.


## Étape 1 : sélectionner vos objectifs en matière de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Dans le volet **Coffres Recovery Services**, choisissez votre coffre et cliquez sur **Paramètres**.
2. Dans **Paramètres** > **Prise en main**, cliquez sur **Site Recovery** > **Étape 1 : Préparer l’infrastructure** > **Objectif de protection**.

	![Sélectionner des objectifs](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Dans **Objectif de la protection**, sélectionnez **Vers Azure** puis **Oui, avec hyperviseur vSphere VMware**. Cliquez ensuite sur **OK**.

	![Sélectionner des objectifs](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## Étape 2 : configurer l’environnement source

Configurez le serveur de configuration et inscrivez-le dans le coffre de Recovery Services. Si vous répliquez des machines virtuelles VMware, spécifiez le compte VMware que vous utilisez pour la découverte automatique.

1. Cliquez sur **Étape 1 : Préparer l’infrastructure** > **Source**. Dans **Préparer la source**, si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration** afin d’en ajouter un.

	![Configurer la source](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Dans le panneau **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
3. Avant de configurer le serveur de configuration, vérifiez la [configuration requise](#configuration-server-prerequisites). Vérifiez notamment que l’ordinateur peut accéder aux URL requises.
4.	Téléchargez le fichier d’installation unifiée de Site Recovery.
5.	Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lorsque vous exécuterez le programme d’installation unifiée. Une fois générée, la clé est valide pendant 5 jours.

	![Configurer la source](./media/site-recovery-vmware-to-azure/set-source2.png)

6.	Sur la machine qui vous sert de serveur de configuration, exécutez le programme d’installation unifiée afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître.


### Exécuter le programme d’installation unifiée Site Recovery

1.	Exécutez le fichier d’installation unifiée.
2.	Dans **Avant de commencer**, sélectionnez **Installer le serveur de configuration et le serveur de traitement**.

	![Avant de commencer](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Dans **Licence de logiciel tiers**, cliquez sur **J’accepte** pour télécharger et installer MySQL.

	![Logiciel tiers](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. Dans **Inscription**, recherchez et sélectionnez la clé d’inscription que vous avez téléchargée à partir du coffre.

	![Inscription](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Dans **Paramètres Internet**, indiquez comment le fournisseur s’exécutant sur le serveur de configuration doit se connecter à Azure Site Recovery par le biais d’Internet.

	- Si vous voulez vous connecter avec le proxy actuellement configuré sur la machine, sélectionnez **Se connecter avec les paramètres de proxy existants**.
	- Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement sans proxy**.
	- Si le proxy existant nécessite une authentification, ou que vous voulez utiliser un proxy personnalisé pour la connexion au fournisseur, sélectionnez **Se connecter avec des paramètres de proxy personnalisés**.
		- Si vous utilisez un proxy personnalisé, vous devez spécifier l’adresse, le port et les données d’identification
		- Si vous utilisez un proxy, vous devez avoir déjà autorisé les URL indiquées dans la section relative à la [configuration requise](#configuration-server-prerequisites).

	![Pare-feu](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. Dans **Vérification de la configuration requise**, le programme d’installation exécute une vérification afin de garantir le bon déroulement de l’installation. Si un avertissement s’affiche à propos de la **vérification de la synchronisation globale**, vérifiez que l’heure de l’horloge système (paramètres **Date et heure**) est identique à celle du fuseau horaire.

	![Configuration requise](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Dans **Configuration MySQL**, créez des informations d’identification pour vous connecter à l’instance de serveur MySQL qui sera installée.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Dans **Détails de l’environnement**, indiquez si vous voulez répliquer des machines virtuelles VMware. Si c’est le cas, le programme d’installation vérifie que PowerCLI 6.0 est installé.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Dans **Emplacement d’installation**, sélectionnez où vous voulez installer les fichiers binaires et stocker le cache. Vous pouvez sélectionner un lecteur qui dispose d’au moins 5 Go de stockage disponible. Toutefois, nous vous recommandons d’utiliser un lecteur de cache présentant au moins 600 Go d’espace disponible.

	![Emplacement d’installation](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Dans **Sélection du réseau**, spécifiez l’écouteur (carte réseau et port SSL) sur lequel le serveur de configuration envoie et reçoit les données de réplication. Vous pouvez modifier la valeur par défaut du port (9443). En plus de ce port, le port 443 sera utilisé par un serveur web qui gère les opérations de réplication. Le port 443 ne doit pas être utilisé pour la réception du trafic de réplication.


	![Sélection du réseau](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  Dans **Résumé**, passez en revue les informations, puis cliquez sur **Installer**. Une fois l’installation terminée, une phrase secrète est générée. Vous en aurez besoin lorsque vous activerez la réplication : copiez-la et conservez-la en lieu sûr.

	![Résumé](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Une fois l’inscription terminée, le serveur s’affiche dans le panneau **Paramètres** > **Serveurs** du coffre.



#### Exécution depuis la ligne de commande

Vous pouvez configurer le serveur de configuration à partir de la ligne de commande :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Lorsque l’installation est finie, pour terminer l’inscription :

1. Lancez une application nommée « Microsoft Azure Recovery Services Shell » à partir du menu Démarrer de Windows.
2. Dans la fenêtre de commande qui s’ouvre, exécutez l’ensemble suivant de commandes pour configurer les paramètres du serveur proxy.

		PS C:\Windows\System32> $pwd = ConvertTo-SecureString -String ProxyUserPassword
		PS C:\Windows\System32> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
		PS C:\Windows\System32> net stop obengine.exe

Paramètres :

- /ServerMode : Obligatoire. Spécifie si les serveurs de configuration et de processus doivent être installés ou si seul le serveur de processus doit être installé. Valeurs d’entrée : CS, PS.
- InstallLocation : Obligatoire. Dossier d’installation des composants.
- /MySQLCredsFilePath. Obligatoire. Chemin d’accès du fichier où sont stockées les informations d’identification du serveur MySQL. Le fichier doit respecter le format suivant :
	- [MySQLCredentials]
	- MySQLRootPassword = "<Mot\_de\_passe>"
	- MySQLUserPassword = "<Mot\_de\_passe>"
- /VaultCredsFilePath. Obligatoire. Emplacement du fichier d’informations d’identification du coffre
- / EnvType. Obligatoire. Type d’installation. Valeurs : VMware, NonVMware
- / PSIP et /CSIP. Obligatoire. Adresse IP du serveur de processus et du serveur de configuration.
- /PassphraseFilePath. Obligatoire. Emplacement du fichier de phrase secrète.
- /BypassProxy. facultatif. Indique que le serveur de configuration se connecte à Azure sans proxy.
- /ProxySettingsFilePath. facultatif. Paramètres de proxy (Le proxy par défaut nécessite une authentification, sinon il faut un proxy personnalisé.). Le fichier doit respecter le format suivant :
	- [ProxySettings]
	- ProxyAuthentication = "Oui/Non"
	- Proxy IP = "Adresse IP>"
	- ProxyPort = "<Port>"
	- ProxyUserName="<Nom\_utilisateur>"
	- ProxyPassword="<Mot\_de\_passe>"
- DataTransferSecurePort. facultatif. Numéro de port à utiliser pour les données de réplication.
- SkipSpaceCheck. facultatif. Vérification de l’omission des espaces pour le cache.
- AcceptThirdpartyEULA. Obligatoire. Cet indicateur implique l’acceptation du CLUF tiers.
- ShowThirdpartyEULA. Obligatoire. Affiche le CLUF tiers. S’il est fourni en tant qu’entrée, tous les autres paramètres sont ignorés.

### Ajouter le compte VMware utilisé pour la découverte automatique

 Lors de la préparation au déploiement, vous devez avoir [créé un compte VMware](#prepare-an-account-for-automatic-discovery) que Site Recovery peut utiliser pour la découverte automatique. Ajoutez ce compte comme suit :

1. Ouvrez le fichier **CSPSConfigtool.exe**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \\home\\svsystems\\bin [EMPLACEMENT D’INSTALLATION].
2. Cliquez sur **Gérer les comptes** > **Ajouter un compte**.

	![Ajouter un compte](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Dans **Détails du compte**, ajoutez le compte qui sera utilisé pour la découverte automatique. L’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, cliquez sur **Serveurs de configuration** > nom du serveur > **Actualiser le serveur**.

	![Détails](./media/site-recovery-vmware-to-azure/credentials2.png)

### Se connecter aux hôtes vSphere et aux serveurs vCenter

Si vous répliquez des machines virtuelles VMware, connectez-vous aux hôtes vSphere et aux serveurs vCenter.

1. Vérifiez que le serveur de configuration dispose d’un accès réseau aux hôtes vSphere et aux serveurs vCenter.
2. Cliquez sur **Préparer l’infrastructure** > **Source**. Dans **Préparer la source**, sélectionnez le serveur de configuration, puis cliquez sur **+vCenter** pour ajouter un hôte vSphere ou un serveur vCenter.
3. Dans **Ajouter un serveur vCenter**, spécifiez un nom convivial pour l’hôte vSphere ou le serveur vCenter, et indiquez l’adresse IP ou le nom de domaine complet public du serveur. Conservez le port 443, sauf si vos serveurs VMware sont configurés pour écouter les demandes sur un port différent. Sélectionnez ensuite le compte qui vous permettra de vous connecter au serveur VMware. Cliquez sur **OK**.

	![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

	>[AZURE.NOTE] Si vous ajoutez le serveur vCenter ou un hôte vSphere à l’aide d’un compte qui ne dispose pas des privilèges d’administrateur sur le serveur vCenter ou l’hôte, assurez-vous que le compte bénéficie de ces autorisations : centre de données, magasin de données, dossier, hôte, ressource, machine virtuelle, commutateur distribué vSphere. En outre, le serveur vCenter a besoin du privilège d’affichage de stockage.

Site Recovery se connecte aux serveurs VMware en utilisant les paramètres que vous avez spécifiés, et découvre les machines virtuelles.

## Étape 3 : configurer l’environnement cible

Vérifiez que vous disposez bien d’un compte de stockage pour la réplication, et d’un réseau Azure auquel les machines virtuelles Azure se connecteront après le basculement.

1.	Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2.	Spécifiez le modèle de déploiement que vous souhaitez utiliser pour les machines virtuelles après le basculement.
3.	Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

	![Cible](./media/site-recovery-vmware-to-azure/gs-target.png)

4.	Si vous n’avez pas créé de compte de stockage et que vous souhaitez le faire par le biais d’ARM, cliquez sur **+Compte de stockage** afin de procéder à cette opération en ligne. Dans le panneau **Créer un compte de stockage**, saisissez le nom, le type, l’abonnement associé et l’emplacement du compte de stockage. Ce compte doit se trouver dans la même région que le coffre Recovery Services.

	![Storage](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

	Notez les points suivants :

	- Si vous souhaitez créer un compte de stockage en suivant le modèle classique, vous pouvez utiliser le portail Azure. [En savoir plus](../storage/storage-create-storage-account-classic-portal.md)
	- Si vous utilisez un compte Premium Storage pour les données répliquées, vous devez configurer un compte de stockage standard supplémentaire, afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.
	
	> [AZURE.NOTE] La protection des comptes de stockage Premium en Inde centrale et en Inde du Sud n’est actuellement pas prise en charge.

4.	Sélectionnez un réseau Azure. Si vous n’avez pas créé de réseau et que vous souhaitez le faire par le biais d’ARM, cliquez sur **+Réseau** afin de procéder à cette opération en ligne. Dans le panneau **Créer un réseau virtuel**, spécifiez le nom, la plage d’adresses, les informations sur le sous-réseau associé, l’abonnement et l’emplacement du réseau virtuel. Ce réseau doit se trouver au même emplacement que le coffre Recovery Services.

	![Réseau](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

	Si vous souhaitez créer un réseau en suivant le modèle classique, vous pouvez utiliser le portail Azure. [En savoir plus](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## Étape 4 : configurer les paramètres de réplication

1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.
2. Dans la zone **Créer et associer une stratégie**, indiquez le nom de la stratégie.
3. Dans le champ **Seuil d’objectif de point de récupération**, spécifiez la limite de l’objectif de point de récupération. Des alertes sont générées lorsque la réplication continue dépasse cette limite.
5. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle. Les machines virtuelles répliquées vers Premium Storage peuvent prendre en charge jusqu’à 24 heures de rétention.
6. Dans **Fréquence des captures instantanées de cohérence d’application**, spécifiez la fréquence de création des points de récupération contenant des captures instantanées cohérentes avec les applications (en minutes).
7. Lorsque vous créez une stratégie de réplication, une stratégie de correspondance est automatiquement créée par défaut pour la restauration automatique. Par exemple, si la stratégie de réplication est **rep-policy**, la stratégie de restauration automatique correspond alors à **rep-policy-failback**. Cette stratégie n’est utilisée qu’à partir du moment où vous initiez une restauration automatique.
8. Cliquez sur **OK** pour créer la stratégie.

	![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Lorsque vous créez une stratégie, cette dernière est automatiquement associée au serveur de configuration. Cliquez sur **OK**.

	![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## Étape 5 : planifier la capacité

Votre infrastructure de base est désormais configurée. Vous pouvez donc réfléchir à la planification de la capacité et déterminer si des ressources supplémentaires sont nécessaires.

Site Recovery propose une fonctionnalité, Capacity Planner, qui vous permet d’allouer les bonnes ressources pour l’environnement source, les composants de récupération de sites, la mise en réseau et le stockage. Vous pouvez exécuter Capacity Planner en mode rapide, afin d’obtenir une estimation basée sur le nombre moyen de machines virtuelles et de disques ainsi que sur l’espace de stockage disponible, ou en mode détaillé. Dans ce mode, vous saisissez des chiffres au niveau des charges de travail. Avant de commencer, vous devez :

- collecter les informations relatives à votre environnement de réplication, notamment les machines virtuelles, le nombre de disques par machine virtuelle et le stockage par disque ;
- déterminer le taux de modification (l’évolution) quotidienne des données répliquées. Pour plus de facilité, vous pouvez utiliser [Appliance de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).

1.	Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. Lisez [l’article relatif à cet outil](site-recovery-capacity-planner.md).
2.	Lorsque vous avez terminé, sélectionnez **Oui** dans **Avez-vous terminé la planification de la capacité ?**

	![Planification de la capacité](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

Le tableau suivant illustre différents points qui vous aideront à planifier la capacité de ce scénario.


**Composant** | **Détails**
--- | --- | ---
**Réplication** | **Taux de modification par jour maximal** : une machine protégée ne peut utiliser qu’un serveur de processus, et un seul serveur de processus peut gérer un taux de modification de 2 To par jour au maximum. Par conséquent, 2 To est le taux de modification quotidien maximal de données pris en charge pour une machine protégée.<br/><br/> **Débit maximum** : un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous recommandons de conserver le nombre d’IOPS sur un ordinateur source à 20 000. Pour exemple, si vous disposez d’une machine source à 5 disques et si chaque disque génère 120 IOPS (taille de 8 Ko) sur la source, la limite de 500 IOPS sur le disque Azure est respectée. Nombre de comptes de stockage requis = nombre total d’IOPS source/20 000.
**Serveur de configuration** | Le serveur de configuration doit être en mesure de gérer le taux de modification quotidien pour toutes les charges de travail en cours d’exécution sur des machines protégées, et il a besoin d’une bande passante suffisante pour permettre la réplication continue des données sur Azure Storage.<br/><br/> À titre de meilleure pratique, nous conseillons de placer le serveur de configuration sur le même réseau et le même segment de réseau local que les machines à protéger. Il peut se trouver sur un réseau différent, mais les ordinateurs à protéger doivent avoir une visibilité de réseau L3.<br/><br/> Les recommandations en matière de taille du serveur de configuration sont résumées dans le tableau qui suit.
**Serveur de traitement** | Le premier serveur de processus est installé par défaut sur le serveur de configuration. Vous pouvez déployer des serveurs de processus supplémentaires pour étendre votre environnement. Remarque :<br/><br/> le serveur de processus reçoit les données de réplication des machines protégées et les optimise grâce à la mise en cache, la compression et le chiffrement avant de les transmettre à Azure. Le serveur de processus doit disposer de ressources suffisantes pour effectuer ces tâches.<br/><br/> Le serveur de traitement utilise le cache disque. Nous recommandons un disque de cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement ou de panne.

### Recommandations de taille pour le serveur de configuration

**UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz) | 16 Go | 300 Go | 500 Go ou moins | Répliquez moins de 100 machines.
12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) | 18 Go | 600 Go | 500 Go à 1 To | Répliquez entre 100 et 150 machines.
16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz) | 32 Go | 1 To | 1 To à 2 To | Répliquez entre 150 et 200 machines.
Déployer un autre serveur de traitement | | | > 2 To | Déployez des serveurs de traitement supplémentaires si vous effectuez la réplication de plus de 200 ordinateurs, ou si le taux de changement des données quotidien dépasse 2 To.

Où :

- Chaque ordinateur source est configuré avec 3 disques de 100 Go chacune.
- Nous avons utilisé le stockage de référence de 8 disques SAP de 10 K tr/min avec RAID 10 pour les mesures de disque cache.

### Recommandations de taille pour le serveur de processus

Si vous souhaitez protéger plus de 200 machines ou si le taux de modification quotidien est supérieur à 2 To, vous pouvez ajouter des serveurs de processus supplémentaires pour gérer la charge de réplication. Pour assurer la montée en charge, vous pouvez :

- Augmentez le nombre de serveurs de configuration. Par exemple, vous pouvez protéger jusqu’à 400 machines avec deux serveurs de configuration.
- Ajoutez des serveurs de processus supplémentaires et utilisez-les pour gérer le trafic à la place (ou en plus) du serveur de configuration.

Ce tableau décrit un scénario dans lequel :

- Vous n’envisagez pas d’utiliser le serveur de configuration en tant que serveur de processus.
- Vous avez configuré un serveur de processus supplémentaire.
- Vous avez configuré des machines virtuelles protégées pour utiliser le serveur de processus supplémentaire.
- Chaque ordinateur source protégé est configuré avec trois disques de 100 Go chacun.

**Serveur de configuration** | **Serveur de traitement supplémentaire**| **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 16 Go de mémoire | 4 processeurs virtuels (2 sockets * 2 cœurs à 2,5 GHz), 8 Go de mémoire | 300 Go | 250 Go ou moins | Répliquez 85 machines ou moins.
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 16 Go de mémoire | 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 12 Go de mémoire | 600 Go | 250 Go à 1 To | Répliquez entre 85 et 150 machines.
12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz), 18 Go de mémoire | 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz), 24 Go de mémoire | 1 To | 1 To à 2 To | Répliquez entre 150 et 225 machines.


La façon dont vous allez mettre à niveau vos serveurs dépend de votre préférence pour la montée en puissance ou la montée en charge. Vous pouvez aboutir à une montée en puissance avec le développement de serveurs de configuration et de processus haut de gamme, ou à une montée en charge avec le déploiement d’un nombre de serveurs supérieur avec moins de ressources. Par exemple, si vous avez besoin de protéger les 220 ordinateurs vous pouvez effectuer des opérations suivantes :

- Configurer le serveur de configuration avec 12 processeurs virtuels, 18 Go de mémoire, un serveur de processus supplémentaire comptant 12 processeurs virtuels, 24 Go de mémoire, et configurer des machines protégées pour utiliser uniquement le serveur de processus supplémentaire.
- Vous pouvez également configurer deux serveurs de configuration (2 x 8 processeurs virtuels, 16 Go de RAM) et deux serveurs de processus supplémentaires (1 x 8 processeurs virtuels et 4 processeurs virtuels x 1 pour gérer les machines (220) 135 + 85 machines) et configurer des machines protégées pour utiliser uniquement les serveurs de processus supplémentaires.

[Suivez ces instructions](#deploy-additional-process-servers) pour configurer un serveur de processus supplémentaire.

### Remarques relatives à la bande passante réseau

Vous pouvez utiliser l’outil Capacity Planner pour calculer la bande passante requise par la réplication (réplication initiale, puis delta). Vous disposez de plusieurs options pour déterminer la quantité de bande passante utilisée pour la réplication :

- **Limiter la bande passante** : le trafic VMware qui est répliqué sur Azure passe par un serveur de processus spécifique. Vous pouvez limiter la bande passante sur les machines qui s’exécutent en tant que serveurs de processus.
- **Influer sur la bande passante** : vous pouvez influer sur la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre :
	- La valeur de Registre **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\UploadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données (réplication initiale ou différentielle) sur un disque. Une valeur plus élevée permet d’augmenter la bande passante réseau utilisée pour la réplication.
	- La valeur de Registre **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\DownloadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données pendant la restauration automatique.

#### Limite de bande passante

1. Ouvrez le composant logiciel enfichable MMC Microsoft Azure Backup sur la machine qui fait office de serveur de processus. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le Bureau. Vous pouvez également le trouver ici : C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

	![Limite de bande passante](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Dans l’onglet **Limitation**, sélectionnez **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde** et définissez les limites relatives aux heures de travail et aux heures non travaillées. Les plages valides vont de 512 Kbits/s à 102 Mbits/s par seconde.

	![Limite de bande passante](./media/site-recovery-vmware-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

Le paramètre **Set-OBMachineSetting -NoThrottle** indique qu’aucune limitation n’est requise.


#### Influer sur la bande passante réseau

1. Dans le Registre, accédez à **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication**.
	- Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur du paramètre **UploadThreadsPerVM**, ou créez la clé si elle n’existe pas.
	- Pour influer sur la bande passante utilisée pour le trafic lié à la restauration automatique à partir d’Azure, modifiez la valeur du paramètre **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « surutilisé », ces clés de Registre doivent être modifiées par rapport aux valeurs par défaut. La valeur maximale est de 32. Surveillez le trafic pour optimiser la valeur.

## Étape 6: Répliquer les applications

Vérifiez que les machines à répliquer sont prêtes pour l’installation du service Mobilité, et puis activez la réplication.

### Installer le service Mobilité

La première étape de l’activation de la protection des machines virtuelles et des serveurs physiques consiste à installer le service Mobilité. Vous pouvez accomplir cette opération de différentes manières :

- **Installation Push à partir du serveur de processus** : lorsque vous activez la réplication sur une machine, transmettez et installez le composant du service Mobilité à partir du serveur de processus. L’installation Push n’a pas lieu si les machines exécutent déjà une version actualisée du composant.
- **Installation Push d’entreprise** : installez automatiquement le composant à l’aide de votre processus Push d’entreprise, comme WSUS ou System Center Configuration Manager. Au préalable, vous devez avoir configuré le serveur de configuration.
- **Installation manuelle** : installez manuellement le composant sur chaque machine à répliquer. Au préalable, vous devez avoir configuré le serveur de configuration.


#### Préparez une opération Push automatique sur les machines Windows

Voici comment préparer les ordinateurs Windows afin que le service Mobilité puisse être installé automatiquement par le serveur de traitement.

1.  Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit disposer de privilèges d’administrateur (local ou domaine) et il est utilisé uniquement pour l’installation Push.

	>[AZURE.NOTE] Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur l’ordinateur local. Pour ce faire, dans le registre situé sous HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System, ajoutez l’entrée de Registre DWORD LocalAccountTokenFilterPolicy avec une valeur de 1. Pour ajouter l’entrée de Registre à partir d’une interface de ligne de commande, tapez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Sur le pare-feu Windows de la machine à protéger, sélectionnez **Autoriser une application ou une fonctionnalité à franchir le pare-feu**. Activez **Partage de fichiers et d’imprimantes** et **Infrastructure de gestion Windows**. Pour les machines qui appartiennent à un domaine, vous pouvez configurer les paramètres de pare-feu avec un objet de stratégie de groupe.

	![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Ajoutez le compte que vous venez de créer :

	- Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \\home\\svsystems\\bin [EMPLACEMENT D’INSTALLATION].
	- Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
	- Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir ces informations d’identification au moment où vous activez la réplication d’une machine.


#### Préparez une opération Push automatique sur les serveurs Linux

1.	Vérifiez que la machine Linux à protéger est prise en charge, comme indiqué dans la section [Configuration requise pour les machines protégées](#protected-machine-prerequisites). Vérifiez qu’il existe une connectivité réseau entre la machine Linux et le serveur de processus.

2.	Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit être un utilisateur racine sur le serveur Linux source et être utilisé uniquement pour l’installation Push.

	- Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \\home\\svsystems\\bin [EMPLACEMENT D’INSTALLATION].
	- Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
	- Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir ces informations d’identification au moment où vous activez la réplication d’une machine.

3.	Vérifiez que les fichiers /etc/hosts sur le serveur Linux source contiennent des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
4.	Installez les packages openssh, openssh-server et openssl les plus récents sur la machine à répliquer.
5.	Vérifiez que SSH est activé et exécuté sur le port 22.
6.	Activez le sous-système SFTP et l'authentification par mot de passe dans le fichier sshd\_config comme suit :

	- Connectez-vous en tant qu’utilisateur racine.
	- Dans le fichier /etc/ssh/sshd\_config, recherchez la ligne commençant par **PasswordAuthentication**.
	- Supprimez la mise en commentaire de la ligne et remplacez la valeur **no** par **yes**.
	- Recherchez la ligne commençant par **Subsystem** et supprimez la mise en commentaire de la ligne.

		![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### Installer le service de mobilité manuellement

Sur le serveur de processus, les programmes d’installation sont disponibles à l’emplacement **C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**.

Système d’exploitation source | Fichier d’installation du service Mobilité
--- | ---
Windows Server (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Installer manuellement sur un serveur Windows


1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **Avant de commencer**, sélectionnez **Service Mobilité**.

	![Service de mobilité](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Dans la zone **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur de configuration et la phrase secrète qui a été générée lors de l’exécution du programme d’installation unifiée. Vous pouvez récupérer la phrase secrète en exécutant la commande suivante : **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –v** sur le serveur de configuration.

	![Service de mobilité](./media/site-recovery-vmware-to-azure/mobility6.png)

4. Dans la zone **Emplacement de l’installation**, conservez le paramètre par défaut, puis cliquez sur **Suivant** pour lancer l’installation.
5. Dans la zone **Progression de l’installation**, surveillez l’installation et redémarrez la machine si vous y êtes invité. Après l’installation du service, la mise à jour de l’état sur le portail peut prendre environ 15 minutes.

Vous pouvez également procéder à l’installation à partir de la ligne de commande :

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Réperoire\_installation>] [/CSIP <Adresse IP CS ayant servi pour l’inscription>] [/PassphraseFilePath <chemin\_fichier\_phrase\_secrète>] [/LogFilePath <chemin\_fichier\_journal>]

Où :

- /Role : Obligatoire Indique si le service Mobilité doit être installé.
- /InstallLocation : Obligatoire. Spécifie l’emplacement d’installation du service.
- / PassphraseFilePath : Obligatoire. Phrase secrète du serveur de configuration.
- /LogFilePath : Obligatoire. Emplacement des fichiers journaux d’installation.

#### Désinstaller le service de mobilité manuellement

Le Service Mobilité peut être désinstallé à l’aide de l’application Ajout/suppression de programmes du Panneau de configuration ou à l’aide d’une ligne de commande.

La commande pour désinstaller le Service Mobilité à l’aide de la ligne de commande est

	MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### Installez manuellement sur un serveur Linux :

1. Copiez l’archive tar appropriée en fonction du tableau ci-dessus vers la machine Linux à répliquer.
2. Ouvrez un interpréteur de commandes et extrayez l’archive tar compressée dans un emplacement local en exécutant la commande suivante : `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créez un fichier passphrase.txt dans le répertoire local. Vous y extrairez les contenus de l’archive tar. Pour cela, copiez la phrase secrète à partir de C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase sur le serveur de configuration et enregistrez-la dans le fichier passphrase.txt, en exécutant *`echo <passphrase> >passphrase.txt`* dans l’interpréteur de commandes.
4. Installez le service Mobilité en exécutant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l’adresse IP interne du serveur de configuration et assurez-vous que le port 443 est sélectionné. Après l’installation du service, la mise à jour de l’état sur le portail peut prendre environ 15 minutes.

**Vous pouvez également procéder à l’installation à partir de la ligne de commande** :

1. Copiez la phrase secrète de C:\\Program Files (x86)\\InMage Systems\\private\\connection sur le serveur de configuration et enregistrez-le dans le fichier passphrase.txt sur le serveur de configuration. Exécutez ensuite ces commandes. Dans notre exemple, l’adresse IP du serveur de configuration est 104.40.75.37 et le port HTTPS doit être 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Pour procéder à l’installation sur le serveur cible :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### Activer la réplication

#### Avant de commencer

Si vous effectuez une réplication de machines virtuelles VMware, prenez note des points suivants :

- Les machines virtuelles VMware sont découvertes toutes les 15 minutes et il peut s’écouler 15 minutes ou plus entre leur découverte et leur affichage dans le portail. De même, la découverte peut prendre 15 minutes ou plus lorsque vous ajoutez un serveur vCenter ou un hôte vSphere.
- La mise à jour avec les modifications de l’environnement sur la machine virtuelle (par exemple, l’installation d’outils VMware) peut aussi prendre plus de 15 minutes.
- Vous pouvez vérifier l’heure de la dernière découverte de machines virtuelles VMware dans le champ **Dernier contact à** correspondant au serveur vCenter ou à l’hôte vSphere dans le panneau **Serveurs de configuration**.
- Pour ajouter des machines à répliquer sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (sans cliquer dessus) et cliquez sur le bouton **Actualiser**.
- Lorsque vous activez la réplication, si la machine est prête, le serveur de processus installe automatiquement le service Mobilité sur ce dernier.

#### Exclure les disques de la réplication

Lorsque vous activez la réplication, tous les disques de la machine sont répliqués par défaut. Vous pouvez exclure des disques de la réplication. Par exemple, vous pouvez ne pas répliquer les disques comportant des données temporaires, ou des données actualisées à chaque redémarrage d’une machine ou d’une application (par exemple, pagefile.sys ou tempdb dans SQL Server). Si vous souhaitez exclure des disques, n’oubliez pas que :

- Vous pouvez exclure uniquement les disques sur lesquels le service Mobilité est déjà installé. Vous devez [installer manuellement le service Mobilité](#install-the-mobility-service-manually), car il n’est installé que par la méthode Push après activation de la réplication.
- Seuls les disques de base peuvent être exclus de la réplication. Vous ne pouvez pas exclure de système d’exploitation ni de disque dynamique.
- Une fois la réplication activée, vous ne pouvez pas ajouter ni supprimer de disques pour la réplication. Si vous voulez ajouter ou exclure un disque, vous devez désactiver la protection de la machine, puis la réactiver.
- Si vous excluez un disque requis pour le bon fonctionnement d’une application, après le basculement vers Azure, vous devez le créer manuellement dans Azure afin que l’application répliquée puisse s’exécuter. Vous pouvez également intégrer Azure Automation dans un plan de récupération pour créer le disque pendant le basculement de la machine.
- Les disques que vous créez manuellement dans Azure seront restaurés. Par exemple, si vous basculez trois disques et créez deux disques directement dans Azure, les cinq disques seront restaurés. Vous ne pouvez pas exclure de disques créés manuellement dans la restauration automatique.

**À présent, activez la réplication comme suit** :

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**. Après avoir activé la réplication pour la première fois, cliquez sur l’option **+Répliquer** dans le coffre pour activer la réplication des machines supplémentaires.
2. Dans le panneau **Source** > **Source**, sélectionnez le serveur de configuration.
3. Dans la zone **Type de machine**, sélectionnez **Machines virtuelles** ou **Machines physiques**.
4. Dans la zone **Hyperviseur vCenter/vSphere**, sélectionnez le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte. Ce paramètre n’est pas utile si vous répliquez des machines physiques.
5. Sélectionnez le serveur de processus. Si vous n’avez pas créé de serveur de processus supplémentaire, il s’agit du serveur de configuration. Cliquez ensuite sur **OK**.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Dans **Cible**, sélectionnez l’abonnement du coffre, puis dans **Modèle de déploiement post-basculement**, sélectionnez le modèle (Classic ou Azure Resource Manager) que vous souhaitez utiliser dans Azure après le basculement.
7. Sélectionnez le compte de stockage Azure que vous utiliserez pour les données de réplication. Notez les points suivants :

	- Vous pouvez sélectionner un compte Standard Storage ou Premium Storage. Si vous sélectionnez un compte Premium, vous devez spécifier un compte Standard Storage supplémentaire pour les journaux de réplication en cours. Les comptes doivent se trouver dans la même région que le coffre Recovery Services.
	- Si vous souhaitez utiliser un compte de stockage différent de ceux dont vous disposez, vous pouvez en [créer un](#set-up-an-azure-storage-account). Pour créer un compte de stockage à l’aide du modèle ARM, cliquez sur **Créer**. Si vous souhaitez créer un compte de stockage en suivant le modèle Classic, vous pouvez utiliser le [Portail Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont démarrées après le basculement. Ce réseau doit se trouver dans la même région que le coffre Recovery Services. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine. Si vous n’avez pas de réseau, vous devez en [créer un](#set-up-an-azure-network). Pour créer un réseau en utilisant le modèle ARM, cliquez sur **Créer**. Si vous souhaitez créer un réseau en suivant le modèle Classic, vous pouvez [utiliser le Portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Sélectionnez un sous-réseau, le cas échéant. Cliquez ensuite sur **OK**.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte utilisé par le serveur de processus pour installer automatiquement le service Mobilité sur la machine. Par défaut, tous les disques sont répliqués. Cliquez sur **Tous les disques** et désélectionnez ceux qui ne doivent pas être répliqués. Cliquez ensuite sur **OK**. Vous pouvez opter pour une définition ultérieure des propriétés.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée. Vous pouvez modifier les paramètres de la stratégie de réplication dans **Paramètres** > **Stratégies de réplication** > Nom de la stratégie > **Modifier les paramètres**. Les modifications que vous appliquez à une stratégie seront appliquées à la réplication et aux nouvelles machines.

12. Cochez la case **Cohérence multimachine virtuelle** pour regrouper les machines dans un groupe de réplication, et nommez le groupe. Cliquez ensuite sur **OK**. Notez les points suivants :

	- Toutes les machines d’un groupe de réplication sont répliquées ensemble et elles ont des points de récupération cohérents après incident et avec les applications lorsqu’elles basculent.
	- Nous vous recommandons de rassembler les machines virtuelles et les serveurs physiques afin qu’ils reflètent vos charges de travail. L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail et elle ne doit être utilisée que si les machines exécutent la même charge de travail et si vous avez besoin de cohérence.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Cliquez sur **Activer la réplication**. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.

> [AZURE.NOTE] Si la machine est prête pour l’installation Push, le composant du service Mobilité est installé dès que la protection est activée. Une fois le composant installé sur la machine, une tâche de protection démarre et échoue. Après cet échec, vous devez redémarrer manuellement chaque machine. Après le redémarrage, la tâche de protection recommence et la réplication initiale se produit.

### Afficher et gérer les propriétés des machines virtuelles

Nous vous recommandons de vérifier les propriétés de la machine source. N’oubliez pas que le nom de la machine virtuelle Azure doit respecter la [configuration requise pour les machines virtuelles Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Cliquez sur **Paramètres** > **Éléments répliqués** et sélectionnez la machine. Le panneau **Bases** affiche des informations sur les paramètres et l’état des machines.

2. Dans **Propriétés**, vous pouvez afficher les informations sur la réplication et le basculement de la machine virtuelle.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. Dans **Calcul et réseau** > **Propriétés de calcul**, vous pouvez spécifier la taille de la cible et le nom de la machine virtuelle Azure. Si besoin, modifiez ce nom afin de respecter les exigences d’Azure. Vous pouvez également afficher et ajouter des informations sur le réseau cible, le sous-réseau et l’adresse IP qui seront affectés à la machine virtuelle Azure. Notez les points suivants :

	- Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas d’adresse IP, la machine ayant basculé utilisera le service DHCP. Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue. Vous pouvez utiliser la même adresse IP cible pour le test de basculement si cette adresse est disponible sur le réseau de test de basculement.
	- Le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible, comme suit :

		- Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
		- Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
		- Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.
	- Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau.

	![Activer la réplication](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Les disques de données et du système d’exploitation de la machine virtuelle qui seront répliqués s’affichent dans **Disques**.


## Étape 7 : Tester le déploiement

Pour tester le déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle ou un plan de récupération qui contient une ou plusieurs machines virtuelles.


### Préparer un basculement

- Pour exécuter un test de basculement, nous vous recommandons de créer un réseau Azure isolé de votre réseau de production Azure (comportement par défaut quand vous créez un réseau dans Azure). Découvrez plus d’informations sur [l’exécution des tests de basculement](site-recovery-failover.md#run-a-test-failover).
- Pour obtenir les meilleures performances possibles lorsque vous effectuez un basculement vers Azure, assurez-vous que vous avez installé l’agent Azure sur l’ordinateur protégé. Cet agent permet de démarrer le système plus rapidement et facilite le dépannage. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
- Pour tester entièrement votre déploiement, vous aurez besoin d’une infrastructure pour permettre à la machine répliquée de fonctionner comme prévu. Si vous souhaitez tester Active Directory et DNS, vous pouvez créer une machine virtuelle jouant le rôle de contrôleur de domaine avec DNS, puis la répliquer sur Azure, via Azure Site Recovery. Pour en savoir plus, lisez [Considérations en matière de test de basculement pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Assurez-vous que le serveur de configuration est en cours d’exécution. Le basculement échouera dans le cas contraire.
- Si vous avez exclu des disques de la réplication, vous devrez peut-être créer ces disques manuellement dans Azure après le basculement afin que l’application s’exécute comme prévu.
- Si vous souhaitez exécuter un basculement non planifié au lieu d’un test de basculement, notez les éléments suivants :

	- qu’il est préférable d’arrêter les machines principales avant d’exécuter un basculement non planifié lorsque c’est possible. Vous êtes ainsi sûr que les machines source et les réplicas ne fonctionnent pas en même temps. Si vous répliquez des machines virtuelles VMware, vous pouvez indiquer que Site Recovery doit faire au mieux pour arrêter les ordinateurs source. Selon l’état du site principal, cela peut fonctionner ou peut ne pas fonctionner. Si vous répliquez des serveurs physiques, Site Recovery ne propose pas cette option.
	- Lorsque vous effectuez un basculement non planifié, la réplication des données depuis les machines principales s’arrête et les différences dans les données ne sont pas transférées après qu’un basculement non planifié a commencé. En outre, si vous exécutez un basculement non planifié sur un plan de récupération, il sera exécuté jusqu’à la fin, même si une erreur se produit.

### Préparer la connexion aux machines virtuelles Azure après le basculement

Si vous souhaitez vous connecter à des machines virtuelles Azure à l’aide de RDP après le basculement, assurez-vous que vous effectuez les opérations suivantes :

**Sur la machine locale, avant le basculement** :

- Pour permettre l’accès par Internet, activez la fonction RDP, vérifiez que les règles TCP et UDP sont ajoutées pour **Public** et assurez-vous que RDP est autorisé dans le champ **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** et ce, pour tous les profils.
- Pour permettre l’accès via une connexion site à site, activez RDP sur la machine, en vérifiant que ce dernier est autorisé dans le champ **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux de types **Domaine** et **Privé**.
- Installez [l’agent Azure VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) sur la machine locale.
- [Installez manuellement le service Mobilité](#install-the-mobility-service-manually) sur les machines au lieu d’utiliser le serveur de processus pour transmettre automatiquement le service. En effet, l’installation Push a lieu uniquement lorsque la machine est activée pour la réplication.
- Vérifiez que la stratégie SAN du système d’exploitation est définie sur la valeur OnlineAll. [En savoir plus](https://support.microsoft.com/kb/3031135)
- Désactivez le service IPSec avant d’exécuter le basculement.

**Sur la machine virtuelle Azure, après le basculement** :

- Ajoutez un point de terminaison public pour le protocole RDP (port 3389) et spécifiez les informations d’identification pour la connexion.
- Assurez-vous qu’aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution. Pour accéder à d’autres conseils de dépannage, lisez [cet article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Si vous souhaitez accéder à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un client Secure Shell (ssh), procédez comme suit :

**Sur la machine locale, avant le basculement** :

- Assurez-vous que le service Secure Shell, sur la machine virtuelle Azure, est défini pour démarrer automatiquement au démarrage du système.
- Vérifiez que les règles de pare-feu autorisent une connexion SSH à ce dernier.

**Sur la machine virtuelle Azure, après le basculement** :

- Les règles des groupes de sécurité réseau figurant sur la machine virtuelle basculée et le sous-réseau Azure auquel elle est connectée permettent d’autoriser les connexions entrantes avec le port SSH.
- Un point de terminaison public doit être créé pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).
- Si la machine virtuelle est accessible via une connexion VPN (Express Route ou VPN de site à site), vous pouvez utiliser le client pour vous connecter directement à la machine virtuelle via SSH.

**Sur la machine virtuelle Azure Windows/Linux après basculement** :

Si vous avez associé un groupe de sécurité réseau à la machine virtuelle ou au sous-réseau auquel la machine appartient, assurez-vous que le groupe de sécurité réseau dispose d’une règle sortante pour autoriser HTTP/HTTPS. Assurez-vous également que le serveur DNS du réseau sur lequel la machine virtuelle bascule est correctement configuré. Sinon, le basculement peut expirer avec l’erreur -’Tâche PreFailoverWorkflow, expiration de WaitForScriptExecutionTask’. Pour comprendre ce processus en détail, reportez-vous à la section relative à la récupération du [Guide d’analyse et de dépannage](site-recovery-monitoring-and-troubleshooting.md#recovery).

## Exécution d’un test de basculement

1. Pour effectuer le basculement d’une seule machine, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle, puis sur l’icône **+Test de basculement**.

	![Test de basculement](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Pour effectuer le basculement d’un plan de récupération, dans **Paramètres** > **Plans de récupération**, cliquez avec le bouton droit sur le plan et sélectionnez **Test de basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le champ **Test de basculement**, sélectionnez le réseau Azure auquel les machines virtuelles Azure seront connectées après le basculement.
4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression du basculement en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou en sélectionnant le travail **Test de basculement** dans le nom de l’archivage > **Paramètres** > **Travaux** > **Travaux Site Recovery**.
5. Lorsque le basculement se trouve à l’état **Terminer le test**, procédez comme suit :

	1. Examinez la machine virtuelle de réplication dans le portail Microsoft Azure. Vérifiez que la machine virtuelle démarre correctement.
	2. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
	3. Cliquez sur **Terminer le test** pour finaliser l’opération.

		![Test de basculement](./media/site-recovery-vmware-to-azure/test-failover6.png)


	4. Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
	5. Cliquez sur **Le test de basculement est terminé.** L’environnement de test est alors automatiquement nettoyé. Une fois cette opération terminée, le test de basculement affiche l’état **Terminé**.
	6.  À cette étape, les éléments ou machines virtuelles créés automatiquement par Site Recovery lors du test de basculement sont supprimés. Toutefois, les éléments supplémentaires que vous avez créés pour le test de basculement ne sont pas supprimés.

	> [AZURE.NOTE] Si un test de basculement s’étend sur plus de deux semaines, le système le force à se terminer.


6. Une fois le basculement terminé, vous devez également voir la machine Azure de réplica apparaître dans le Portail Azure > **Machines virtuelles**. Vous devrez peut-être vous assurer que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.
7. Si vous avez [préparé les connexions après le basculement](#prepare-to-connect-to-azure-vms-after-failover), vous devez être à même de vous connecter à la machine virtuelle Azure.

## Surveiller votre déploiement

Voici comment vous pouvez surveiller l’intégrité, l’état et les paramètres de configuration de votre déploiement Site Recovery :

1. Cliquez sur le nom du coffre pour accéder au tableau de bord **Essentials**. Ce tableau affiche l’état de la réplication, les tâches, les plans de récupération, l’intégrité du serveur et les événements Site Recovery. Vous pouvez personnaliser Essentials de manière afficher les vignettes et les dispositions qui sont les plus utiles, y compris l’état des autres coffres Site Recovery et de Sauvegarde.<br> ![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. Dans la mosaïque **Intégrité**, vous pouvez surveiller le fonctionnement des serveurs du site (VMM ou serveurs de configuration) qui rencontrent un problème, ainsi que les événements signalés par Site Recovery au cours des dernières 24 heures.
3. Vous pouvez gérer et surveiller la réplication dans les vignettes **Éléments répliqués**, **Plans de récupération** et **Travaux Site Recovery**. Vous pouvez examiner les travaux plus en détail dans **Paramètres** -> **Travaux** -> **Travaux Site Recovery**.


## Déployer d’autres serveurs de traitement

Si vous devez monter en charge votre déploiement au-delà de 200 machines source ou si votre taux d’évaluation quotidien total dépasse 2 To, vous aurez besoin de serveurs de processus supplémentaires pour gérer le volume de trafic.

Vérifiez les [tailles recommandées pour les serveurs de processus](#size-recommendations-for-the-process-server) et suivez les instructions ci-dessous pour configurer le serveur de processus. Après avoir configuré le serveur, vous pouvez migrer les ordinateurs source pour les utiliser.

### Installer un serveur de processus supplémentaire

1. Dans **Paramètres** > **Serveurs Site Recovery**, cliquez sur le serveur de configuration, puis sur **Serveur de processus**.

	![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. Dans **Type de serveur**, cliquez sur **Serveur de processus (local)**.

	![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Téléchargez le fichier d’installation unifiée Site Recovery et exécutez-le pour installer le serveur de processus et l’inscrire dans le coffre.
4. Dans **Avant de commencer**, sélectionnez **Ajouter des serveurs de processus supplémentaires pour effectuer un déploiement avec montée en charge**.
5. Terminez l’Assistant comme vous l’avez fait lors de la [configuration](#step-2-set-up-the-source-environment) du serveur de configuration.

	![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Dans **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur de configuration et la phrase secrète. Pour récupérer la phrase secrète, exécutez la commande suivante : **<DossierInstallationSiteRecovery>\\home\\sysystems\\bin\\genpassphrase.exe –n** sur le serveur de configuration.

	![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/add-ps2.png)

### Migrez les machines pour utiliser le nouveau serveur de traitement

1. Dans **Paramètres** > **Serveurs Site Recovery**, cliquez sur le serveur de configuration, puis développez **Serveurs de processus**.

	![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Cliquez avec le bouton droit sur le serveur de processus en cours d’utilisation et sélectionnez **Basculer**.

	![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. Dans **Sélectionner un serveur de traitement cible**, sélectionnez le nouveau serveur de processus à utiliser, puis les machines virtuelles que le nouveau serveur de processus va gérer. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. Pour vous aider à prendre des décisions quant à la charge, l’espace moyen nécessaire à la réplication de chaque machine virtuelle sélectionnée vers le nouveau serveur de processus s’affiche. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de traitement.

## Autorisations du compte VMware

Le serveur de traitement peut découvrir automatiquement les machines virtuelles sur un serveur vCenter. Pour effectuer la découverte automatique, vous devez [définir un rôle (Azure\_Site\_Recovery)](#prepare-an-account-for-automatic-discovery) afin de permettre à Site Recovery d’accéder au serveur VMware. Notez que si vous n’avez qu’à migrer des machines VMware vers Azure et que vous n’avez pas besoin de restauration automatique d’Azure, vous devez définir un rôle en lecture seule suffisant. Les autorisations de rôle requises sont indiquées dans le tableau suivant.

**Rôle** | **Détails** | **Autorisations**
--- | --- | ---
Rôle Azure\_Site\_Recovery | Détection de machine virtuelle VMware |Attribuez ces privilèges au serveur v-Center :<br/><br/>Magasin de données -> Allouer de l’espace, Parcourir le magasin de données, Opérations de fichier de bas niveau, Supprimer le fichier, Mettre à jour les fichiers de machine virtuelle<br/><br/>Réseau -> Attribution de réseau<br/><br/>Ressources -> Affecter une machine virtuelle à une liste de ressources partagées, Migrer une machine virtuelle hors tension, Migrer une machine virtuelle sous tension<br/><br/>Tâches -> Créer une tâche, Mettre à jour une tâche <br/><br/>Machine virtuelle -> Configuration<br/><br/>Machine virtuelle -> Interagir -> Répondre à la question, Connexion d’appareil, Configurer un support de CD, Configurer une disquette, Mettre hors tension, Mettre sous tension, Installation des outils VMware<br/><br/>Machine virtuelle -> Stock -> Créer, Inscrire, Désinscrire<br/><br/>Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, Autoriser le chargement de fichiers de machine virtuelle<br/><br/>Machine virtuelle -> Instantanés -> Supprimer les instantanés
Rôle d’utilisateur vCenter | Découverte/Basculement de machine virtuelle VMware sans arrêt de la machine virtuelle source | Attribuez ces privilèges au serveur v-Center :<br/><br/>Objet de centre de données -> Propager vers l’objet enfant, rôle = Lecture seule. <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données. Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec **Propager vers l’objet enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
Rôle d’utilisateur vCenter | Basculement et restauration automatique | Attribuez ces privilèges au serveur v-Center :<br/><br/>Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure\_Site\_Recovery <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données. Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec **Propager vers l’objet enfant** à l’objet enfant (hôtes vSphere, banques de données, machines virtuelles et réseaux).  
## Étapes suivantes

- [Plus d’informations](site-recovery-failover.md) sur les différents types de basculement.
- [Découvrez plus d’informations sur la restauration automatique](site-recovery-failback-azure-to-vmware.md) qui permet de ramener dans votre environnement local vos machines basculées s’exécutant dans Azure.

## Informations et remarques relatives aux logiciels tiers

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0921_2016-->