<properties 
   pageTitle="Présentation de StorSimple | Microsoft Azure" 
   description="Décrit StorSimple, notamment le processus de hiérarchisation, l’appareil, l’appareil virtuel, les services et la gestion du stockage, et présente les termes clés utilisés dans StorSimple" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/20/2016"
   ms.author="v-sharos@microsoft.com"/>

# StorSimple série 8000 : une solution de stockage de cloud hybride

## Vue d'ensemble

Bienvenue dans Microsoft Azure StorSimple, solution de stockage intégrée qui gère les tâches de stockage entre les appareils locaux et le stockage cloud Microsoft Azure. StorSimple est une solution SAN efficace, rentable et facile à gérer qui élimine la plupart des problèmes et des frais liés à la protection des données et du stockage d’entreprise. Il utilise l’appareil propriétaire StorSimple série 8000, s’intègre à des services cloud et fournit un ensemble d’outils de gestion pour offrir une vue claire de tout le stockage d’entreprise, y compris le stockage cloud. (Les informations de déploiement StorSimple publiées sur le site web Microsoft Azure s’appliquent aux appareils StorSimple de la série 8000 uniquement. Si vous utilisez un appareil StorSimple de la série 5000/7000, consultez l’[Aide de StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple utilise la [hiérarchisation du stockage](#automatic-storage-tiering) pour gérer les données stockées sur différents supports de stockage. Les données couramment utilisées sont stockées sur site sur des disques SSD, les données utilisées moins fréquemment sont stockées sur des lecteurs de disques durs et les données d’archivage sont placées dans le cloud. De plus, StorSimple utilise la déduplication et la compression pour réduire le volume de stockage utilisé par les données. Pour plus d’informations, consultez [Déduplication et compression](#deduplication-and-compression). Pour obtenir les définitions d’autres termes et concepts clés utilisés dans la documentation de StorSimple série 8000, consultez la section [Terminologie StorSimple](#storsimple-terminology) à la fin de cet article.

Avec StorSimple Update 2, vous pouvez identifier les volumes appropriés comme étant *épinglés localement* afin de garantir que les données primaires restent locales sur l'appareil et ne sont pas hiérarchisées vers le cloud. Cela vous permet d'exécuter des charges de travail qui sont sensibles à la latence de cloud, telles que des charges de travail des machines virtuelles et SQL, sur les volumes épinglés localement, tout en continuant à utiliser le cloud pour la sauvegarde. Pour plus d'informations sur les volumes épinglés localement, consultez [Utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes-u2.md).

StorSimple Update 2 vous permet également de créer des appareils virtuels StorSimple qui tirent parti des faibles latences et des hautes performances fournies par Azure Premium Storage. Pour plus d'informations sur les appareils virtuels StorSimple Premium, consultez [Déployer et gérer un appareil virtuel StorSimple dans Azure](storsimple-virtual-device-u2.md). Pour plus d’informations sur Azure Premium Storage, consultez [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md).

Outre la gestion du stockage, les fonctionnalités de protection des données StorSimple permettent de créer des sauvegardes à la demande et planifiées, puis de les stocker localement ou dans le cloud. Les sauvegardes sont effectuées sous la forme d’instantanés incrémentiels, ce qui signifie qu’elles peuvent être créés et restaurés rapidement. Les instantanés cloud peuvent être d’une importance critique dans les scénarios de récupération d’urgence, car ils remplacent les systèmes de stockage secondaire (comme la sauvegarde sur bande) et vous permettent de restaurer des données sur votre centre de données ou sur d’autres sites si nécessaire.

![icône de vidéo](./media/storsimple-overview/video_icon.png) Regardez la vidéo de présentation générale de Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## Pourquoi utiliser StorSimple ?

Le tableau suivant décrit certains des principaux avantages offerts par Microsoft Azure StorSimple.

| Fonctionnalité | Avantage |
|---------|---------|
|Intégration transparente | Microsoft Azure StorSimple utilise le protocole iSCSI pour lier de façon invisible les équipements de stockage de données. Cela garantit que les données stockées dans le cloud, dans un centre de données ou sur des serveurs distants semblent être stockées dans un emplacement unique.|
|Coûts de stockage réduits|Microsoft Azure StorSimple alloue suffisamment de stockage local ou cloud pour répondre aux besoins actuels et étend le stockage cloud uniquement quand cela est nécessaire. Il réduit les besoins et les dépenses de stockage en éliminant les redondances de données (déduplication) et en utilisant la compression.|
|Gestion simplifiée du stockage|Microsoft Azure StorSimple fournit des outils d’administration système que vous pouvez utiliser pour configurer et gérer les données stockées localement, sur un serveur distant et dans le cloud. En outre, vous pouvez gérer la sauvegarde et la restauration des fonctions depuis un composant logiciel enfichable Microsoft Management Console (MMC). StorSimple fournit une interface distincte et facultative que vous pouvez utiliser pour étendre les services de protection des données et de gestion StorSimple au contenu stocké sur des serveurs SharePoint. |
|Récupération d’urgence et conformité améliorées|Microsoft Azure StorSimple ne nécessite pas un long délai de récupération. Au contraire, il restaure les données lorsque vous en avez besoin. Cela signifie que les opérations normales peuvent se poursuivre, avec une interruption minimale. En outre, vous pouvez configurer des stratégies pour spécifier les planifications de sauvegarde et la conservation des données.
|Mobilité des données|Les données chargées sur Microsoft Azure Cloud Services sont accessibles à partir d’autres sites à des fins de récupération et de migration. En outre, vous pouvez utiliser StorSimple pour configurer les appareils virtuels StorSimple sur les machines virtuelles de Microsoft Azure. Les machines virtuelles peuvent ensuite utiliser les appareils virtuels pour accéder aux données stockées à des fins de test ou de récupération.|
|Prise en charge pour les autres fournisseurs de services cloud |La série StorSimple 8000 avec la mise à jour logicielle 1 ou ultérieure prend en charge Amazon S3 avec les services cloud RRS, HP et OpenStack, ainsi que Microsoft Azure. (Vous aurez néanmoins toujours besoin d’un compte de stockage Microsoft Azure pour la gestion de l’appareil.) Pour plus d’informations, consultez [Nouveautés dans Update 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuité de l'activité | StorSimple Update 1 ou version ultérieure fournit également une nouvelle fonctionnalité de migration qui permet aux utilisateurs de StorSimple série 5000-7000 de migrer leurs données vers un appareil StorSimple série 8000.|
|Disponibilité sur le portail Azure Government | StorSimple Update 1 ou version ultérieure est disponible sur le portail Azure Government. Pour plus d’informations, consultez [Déploiement de votre appareil StorSimple local sur le portail Microsoft Azure Government](storsimple-deployment-walkthrough-gov.md).|
|Disponibilité et protection des données | Les appareils StorSimple série 8000 avec Update 1, ou version ultérieure, prennent en charge le stockage redondant dans une zone (ZRS), en plus du stockage localement redondant (LRS) et du stockage géo-redondant (GRS). Pour en savoir plus sur le stockage redondant dans une zone, consultez [cet article sur les options de redondance d’Azure Storage](https://azure.microsoft.com/documentation/articles/storage-redundancy/).|
|Prise en charge des applications critiques | Avec StorSimple Update 2, vous pouvez identifier les volumes appropriés épinglés localement. Cette fonctionnalité garantit que les données requises par les applications critiques ne sont pas hiérarchisées vers le cloud. Les volumes épinglés localement ne sont pas soumis aux latences de cloud ou à des problèmes de connectivité. Pour plus d'informations sur les volumes épinglés localement, consultez [Utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes-u2.md).|
|Faible latence et hautes performances | StorSimple Update 2 vous permet de créer des périphériques virtuels qui tirent parti des faibles latences et des hautes performances fournies par Azure Premium Storage. Pour plus d'informations sur les appareils virtuels StorSimple Premium, consultez [Déployer et gérer un appareil virtuel StorSimple dans Azure](storsimple-virtual-device-u2.md).|

![icône de vidéo](./media/storsimple-overview/video_icon.png) Regardez [cette vidéo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) pour obtenir une vue d’ensemble des fonctionnalités de StorSimple série 8000 et de ses avantages.

## Composants de StorSimple

La solution Microsoft Azure StorSimple comprend les composants suivants :

- **Appareil Microsoft Azure StorSimple** : une baie de stockage hybride local qui contient des disques SSD et des disques durs, ainsi que des contrôleurs redondants et des fonctionnalités de basculement automatique. Les contrôleurs gèrent la hiérarchisation du stockage en plaçant les données actuellement (ou souvent) utilisées sur le stockage local (sur les serveurs de l'appareil ou sur site), tout en déplaçant les données moins fréquemment utilisées sur le cloud.
- **Appareil virtuel StorSimple** : également appelé appliance virtuelle StorSimple, il s’agit d’une version logicielle du périphérique StorSimple qui réplique l’architecture et la plupart des fonctionnalités de l’appareil de stockage hybride physique. L’appareil virtuel StorSimple s’exécute sur un nœud unique dans une machine virtuelle Azure. Les appareils virtuels Premium, qui tirent parti d’Azure Premium Storage, sont disponibles dans Update 2 et version ultérieure.
- **Service StorSimple Manager** : extension du portail Azure Classic qui vous permet de gérer un appareil physique StorSimple ou un appareil virtuel StorSimple à partir d’une seule interface web. Vous pouvez utiliser le service StorSimple Manager pour créer et gérer les services, afficher et gérer des appareils, afficher les alertes, gérer des volumes, ainsi qu’afficher et gérer des stratégies de sauvegarde et le catalogue de sauvegarde.
- **Windows PowerShell pour StorSimple** : une interface de ligne de commande que vous pouvez utiliser pour gérer l’appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d’inscrire votre appareil StorSimple, de configurer l’interface réseau sur votre appareil, d’installer certains types de mises à jour, de résoudre les problèmes de votre appareil en accédant à la session de support et de modifier l’état de l’appareil. Vous pouvez accéder à Windows PowerShell pour StorSimple en vous connectant à la console série ou à l’aide de l’accès distant Windows PowerShell.
- **Cmdlets PowerShell d’Azure StorSimple** : une collection de cmdlets Windows PowerShell qui vous permettent d'automatiser les tâches de niveau de service et la migration à partir de la ligne de commande. Pour plus d’informations sur les cmdlets Azure PowerShell pour StorSimple, consultez la page [Documentation de référence des cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).
- **Gestionnaire d’instantanés StorSimple** : un composant logiciel enfichable MMC qui utilise des groupes de volumes et Windows Volume Shadow Copy Service pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser le gestionnaire d’instantanés StorSimple pour créer des planifications de sauvegarde et cloner ou restaurer des volumes.
- **Adaptateur StorSimple pour SharePoint** : un outil qui étend le stockage et la protection des données Microsoft Azure StorSimple de manière transparente aux batteries de serveurs SharePoint, tout en permettant d’afficher et de gérer le stockage StorSimple à partir du portail d’administration central de SharePoint.

Le diagramme ci-dessous offre une vue d’ensemble de l’architecture et des composants de Microsoft Azure StorSimple.

![Architecture de StorSimple](./media/storsimple-overview/overview-big-picture.png)

Les sections suivantes décrivent chacun de ces composants plus en détail, et expliquent comment la solution organise les données, alloue le stockage et facilite la gestion du stockage et la protection des données. La dernière section fournit les définitions de quelques termes et concepts importants liés aux composants de StorSimple et à leur gestion.

## Appareil StorSimple

L’appareil Microsoft Azure StorSimple est un groupe de stockage hybride local qui fournit un stockage principal et un accès iSCSI aux données qui y sont stockées. Il gère la communication avec le stockage cloud et contribue à garantir la sécurité et la confidentialité de toutes les données stockées sur la solution Microsoft Azure StorSimple.

L’appareil StorSimple inclut des disques SSD et des disques durs, ainsi que la prise en charge du basculement automatique et du clustering. Il contient un processeur partagé, un stockage partagé et deux contrôleurs en miroir. Chaque contrôleur offre les avantages suivants :

- Connexion à un ordinateur hôte
- Jusqu’à six ports réseau pour se connecter au réseau local (LAN)
- Surveillance du matériel
- Mémoire vive non volatile (NVRAM), qui conserve les informations même si l’alimentation est interrompue
- Mise à jour adaptée aux clusters pour gérer les mises à jour logicielles sur les serveurs d’un cluster de basculement, afin que les mises à jour aient une incidence minime ou inexistante sur la disponibilité du service
- Service de cluster, fonctionnant comme un cluster principal, pour fournir une haute disponibilité et réduire les effets négatifs pouvant se produire si un lecteur de disque dur ou un disque SSD est défaillant ou passe hors connexion

Un seul contrôleur est actif à un instant donné. Si le contrôleur actif est défaillant, le deuxième contrôleur devient automatiquement actif.

Pour plus d’informations, consultez [Composants matériels de StorSimple et leur état](storsimple-monitor-hardware-status.md).

## Appareil virtuel StorSimple

Vous pouvez utiliser StorSimple pour créer un appareil virtuel qui réplique l’architecture et les fonctionnalités de l’appareil physique de stockage hybride. L’appareil virtuel StorSimple (ou appliance virtuelle Storsimple) s’exécute sur un nœud unique dans une machine virtuelle Azure. (Un appareil virtuel peut uniquement être créé sur une machine virtuelle Azure. Il est impossible d’en créer un sur un serveur local ou sur un appareil StorSimple.)

L’appareil virtuel présente les caractéristiques suivantes :

- Il se comporte comme un équipement physique et peut fournir une interface iSCSI aux machines virtuelles dans le cloud.
- Vous pouvez créer un nombre illimité d’appareils virtuels dans le cloud, et les activer/désactiver selon vos besoins.
- Il peut aider à simuler les environnements locaux dans les scénarios de test et de développement ou de récupération d’urgence, tout en facilitant la récupération au niveau des éléments à partir de sauvegardes.

Avec la solution Update 2 et version ultérieure, le périphérique virtuel StorSimple est disponible en deux modèles : l'appareil 8010 (anciennement appelé le modèle 1100) et l’appareil 8020. L’appareil 8010 possède une capacité maximale de 30 To. L’appareil 8020, qui tire parti d’Azure Premium Storage, possède une capacité maximale de 64 To. (Dans des niveaux locaux, Azure Premium Storage stocke les données sur des disques SSD, alors que les données sont stockées sur des disques durs avec un stockage standard.) Notez que vous devez disposer d'un compte Azure Premium Storage pour utiliser le stockage premium. Pour plus d’informations sur le stockage premium, consultez [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md).

Pour plus d'informations sur les appareils virtuels StorSimple, consultez [Déployer et gérer un appareil virtuel StorSimple dans Azure](storsimple-virtual-device-u2.md).

## service StorSimple Manager

Microsoft Azure StorSimple fournit une interface utilisateur web (le service StorSimple Manager) qui vous permet de gérer les centres de données et le stockage cloud de manière centralisée. Vous pouvez utiliser le service StorSimple Manager pour effectuer les tâches suivantes :

- Configurer les paramètres système pour les appareils StorSimple.
- Configurer et gérer les paramètres de sécurité pour les appareils StorSimple.
- Configurer les propriétés et les informations d’identification cloud.
- Configurer et gérer des volumes sur un serveur.
- Configurer des groupes de volumes.
- Sauvegarder et restaurer des données.
- Analyser les performances.
- Passer en revue les paramètres système et identifier les problèmes possibles.

Vous pouvez utiliser le service StorSimple Manager pour effectuer toutes les tâches d’administration, à l’exception de celles nécessitant un temps d’arrêt du système, comme la configuration initiale et l’installation de mises à jour.

Pour plus d’informations, consultez [Utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

## Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour créer et gérer le service Microsoft Azure StorSimple, ainsi que pour configurer et surveiller les appareils StorSimple. C’est une interface de ligne de commande Windows PowerShell, qui inclut des applets de commande dédiées à la gestion de votre appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d’effectuer les opérations suivantes :

- Enregistrer un appareil.
- Configurer l’interface réseau sur un appareil.
- Installer certains types de mises à jour.
- Dépanner votre appareil en accédant à la session de support.
- Modifier l’état de l’appareil.

Vous pouvez accéder à Windows PowerShell pour StorSimple à partir d’une console série (sur un ordinateur hôte connecté directement à l’appareil) ou à distance, à l’aide de l’accès distant Windows PowerShell. Notez que certains travaux Windows PowerShell pour StorSimple, comme l’inscription initiale de l’appareil, peuvent uniquement être effectués sur la console série.

Pour plus d’informations, consultez [Utiliser Windows PowerShell pour StorSimple pour gérer votre appareil](storsimple-windows-powershell-administration.md).

## Applets de commande Azure PowerShell StorSimple

Les applets de commande Azure PowerShell StorSimple sont une collection d'applets de commande Windows PowerShell qui vous permettent d'automatiser les tâches de niveau de service et la migration à partir de la ligne de commande. Pour plus d’informations sur les cmdlets Azure PowerShell pour StorSimple, consultez la page [Documentation de référence des cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).

## StorSimple Snapshot Manager

StorSimple Snapshot Manager est un composant logiciel enfichable MMC (Microsoft Management Console) qui vous permet de créer des copies de sauvegarde à un moment donné, cohérentes, des données locales et du cloud. Le composant logiciel enfichable s’exécute sur un hôte Windows Server. Vous pouvez utiliser StorSimple Snapshot Manager pour :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volumes pour garantir que les données sauvegardées sont cohérentes pour l’application.
- Gérer les stratégies de sauvegarde afin que les données soient sauvegardées selon un calendrier prédéterminé et stockées dans un emplacement désigné (localement ou dans le cloud).
- Restaurer des volumes et des fichiers individuels.

Les sauvegardes sont capturées sous forme d’instantanés, qui enregistrent uniquement les modifications apportées depuis la dernière capture instantanée et nécessitent beaucoup moins d’espace de stockage que les sauvegardes complètes. Vous pouvez créer des planifications de sauvegarde ou effectuer des sauvegardes immédiates en fonction de vos besoins. En outre, vous pouvez utiliser StorSimple Snapshot Manager pour établir des stratégies de rétention qui contrôlent le nombre d’instantanés enregistrés. Si vous avez besoin de restaurer les données à partir d’une sauvegarde par la suite, StorSimple Snapshot Manager vous permet de choisir dans le catalogue d’instantanés locaux ou de cloud.

Si un incident se produit ou si vous devez restaurer des données pour une autre raison, StorSimple Snapshot Manager les restaure de façon incrémentielle, selon les besoins. La restauration des données ne nécessite pas d’arrêter l’ensemble du système lorsque vous restaurez un fichier, remplacez un équipement ou déplacez des opérations vers un autre site.

Pour plus d’informations, consultez [Qu’est-ce que StorSimple Snapshot Manager ?](storsimple-what-is-snapshot-manager.md)

## StorSimple Adapter for SharePoint

Microsoft Azure StorSimple inclut StorSimple Adapter for SharePoint, un composant facultatif qui étend de manière transparente les fonctionnalités de protection des données et de stockage StorSimple aux batteries de serveurs SharePoint. L’adaptateur fonctionne avec un fournisseur de stockage d’objets Blob distants (RBS) et la fonctionnalité RBS de SQL Server, ce qui vous permet de déplacer des objets blob vers un serveur sauvegardé par le système Microsoft Azure StorSimple. Microsoft Azure StorSimple stocke ensuite les données d’objets blob localement ou dans le cloud, selon leur utilisation.

StorSimple Adapter for SharePoint est géré depuis le portail d’Administration centrale de SharePoint. Par conséquent, la gestion de SharePoint reste centralisée et tout le stockage apparaît comme étant dans la batterie de serveurs SharePoint.

Pour plus d’informations, consultez [StorSimple Adapter pour SharePoint](storsimple-adapter-for-sharepoint.md).

## Technologies de gestion de stockage
 
En plus de l’appareil StorSimple dédié, de l’appareil virtuel et de divers autres composants, Microsoft Azure StorSimple utilise les technologies logicielles suivantes pour fournir un accès rapide aux données et réduire la consommation de stockage :

- [Hiérarchisation automatique du stockage](#automatic-storage-tiering)
- [Allocation dynamique](#thin-provisioning)
- [Déduplication et compression](#deduplication-and-compression)

### Hiérarchisation automatique du stockage

Microsoft Azure StorSimple organise automatiquement les données en niveaux logiques, en fonction de l’utilisation actuelle, de l’âge et des relations à d’autres données. Les données les plus actives sont stockées localement, tandis que les données les moins actives ou inactives sont automatiquement transférées vers le cloud. Le diagramme suivant illustre cette méthode de stockage.
 
![Niveaux de stockage StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Pour permettre un accès rapide, StorSimple stocke les données très actives sur des disques SSD de l’appareil StorSimple. Il stocke les données occasionnellement utilisées sur des disques durs de l’appareil ou sur des serveurs du centre de données. Il déplace les données inactives, les données de sauvegarde et les données conservées à des fins d’archivage ou de conformité vers le cloud.

>[AZURE.NOTE] Dans la solution Update 2 ou version ultérieure, vous pouvez spécifier qu’un volume est épinglé localement, auquel cas les données restent sur l’appareil local et ne sont pas hiérarchisées vers le cloud.

StorSimple ajuste et réorganise les données et modifie les affectations de stockage conformément aux modifications du schéma d’utilisation. Par exemple, certaines informations peuvent devenir moins actives au fil du temps. À mesure que leur utilisation diminue, elles sont transférées d’un disque SSD vers le disque dur, puis vers le cloud. Si ces mêmes données redeviennent actives, elles sont transférées à nouveau sur le dispositif de stockage.

Le processus de hiérarchisation du stockage se déroule comme suit :

1. Un administrateur système configure un compte de stockage cloud Microsoft Azure.
2. L’administrateur utilise la console série et le service StorSimple Manager (exécuté dans le portail Azure Classic) pour configurer l’appareil et le serveur de fichiers, en créant des volumes et des stratégies de protection des données. Les machines locales (par exemple les serveurs de fichiers) utilisent l’interface iSCSI (Internet Small Computer System Interface) pour accéder à l’appareil StorSimple.
3. À la base, StorSimple stocke les données sur le niveau SSD rapide de l’appareil.
4. Au fur et à mesure que le niveau SSD approche de sa capacité maximale, StorSimple déduplique et compresse les blocs de données les plus anciens et les déplace vers le niveau disque dur (HDD).
5. Au fur et à mesure que le niveau disque dur (HDD) approche de sa capacité maximale, StorSimple chiffre les blocs de données les plus anciens et les envoie de façon sécurisée au compte de stockage Microsoft Azure via HTTPS.
6. Microsoft Azure crée plusieurs réplicas des données dans son centre de données et dans un centre de données distant, ce qui permet de garantir la récupération des données en cas de sinistre.
7. Quand le serveur de fichiers demande des données stockées dans le cloud, StorSimple les retourne de façon transparente et stocke une copie au niveau SSD de l’appareil StorSimple.

### Allocation dynamique

L’allocation dynamique est une technologie de virtualisation dans laquelle le stockage disponible semble dépasser les ressources physiques. Plutôt que de réserver un espace de stockage suffisant à l’avance, StorSimple utilise l’allocation dynamique pour allouer juste assez d’espace pour répondre aux besoins actuels. La nature évolutive du stockage cloud simplifie cette approche, car StorSimple peut augmenter ou diminuer le stockage cloud pour répondre aux demandes changeantes.

>[AZURE.NOTE] Les volumes épinglés localement ne sont pas alloués de façon dynamique. Le stockage alloué à un volume local est approvisionné dans son intégralité lorsque le volume est créé.

### Déduplication et compression

Microsoft Azure StorSimple utilise la compression des données et la déduplication pour réduire encore davantage les besoins de stockage.

La déduplication réduit la quantité globale de données stockées en éliminant les redondances dans le jeu de données stocké. Lorsque des données sont modifiées, StorSimple ignore les informations non modifiées et enregistre uniquement les modifications. En outre, StorSimple réduit la quantité de données stockées en identifiant et en supprimant les informations inutiles.

>[AZURE.NOTE] Les données sur les volumes épinglés localement ne sont pas dédupliquées ou compressées. Toutefois, les sauvegardes de volumes épinglés localement sont dédupliquées et compressées.

## Résumé des charges de travail StorSimple

Voici un tableau résumant les charges de travail StorSimple prises en charge.

| Scénario | Charge de travail | Pris en charge | Restrictions | Version |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Collaboration | Partage de fichiers | Oui | | Toutes les versions |
| Collaboration | Partage de fichiers distribués| Oui | | Toutes les versions |
| Collaboration | SharePoint | Oui* |Pris en charge uniquement avec des volumes épinglés localement | Update 2 et versions ultérieures |
| Archivage | Archivage de fichiers simple | Oui | | Toutes les versions |
| Virtualisation | Machines virtuelles | Oui* |Pris en charge uniquement avec des volumes épinglés localement | Update 2 et versions ultérieures |
| Base de données | SQL | Oui* |Pris en charge uniquement avec des volumes épinglés localement | Update 2 et versions ultérieures |
| Surveillance vidéo | Surveillance vidéo | Oui* |Pris en charge lorsque l’appareil StorSimple est exclusivement dédié à cette charge de travail| Update 2 et versions ultérieures |
| Sauvegarde | Sauvegarde de la cible principale | Oui* |Pris en charge lorsque l’appareil StorSimple est exclusivement dédié à cette charge de travail| Update 3 et versions ultérieures |
| Sauvegarde | Sauvegarde de la cible secondaire | Oui* |Pris en charge lorsque l’appareil StorSimple est exclusivement dédié à cette charge de travail| Update 3 et versions ultérieures |

*Oui&#42; - Des restrictions et des recommandations sur la solution doivent s’appliquer.*

Les charges de travail suivantes ne sont pas prises en charge par les appareils StorSimple série 8000. En cas de déploiement sur StorSimple, ces charges de travail entraîneront une configuration non prise en charge.

-  Imagerie médicale
-  Microsoft Exchange
-  VDI
-  Oracle
-  SAP
-  Données volumineuses (« Big Data »)
-  Distribution de contenu
-  Démarrage à partir de SCSI

Voici une liste des composants d’infrastructure pris en charge par StorSimple.

| Scénario | Charge de travail | Pris en charge | Restrictions | Version |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Généralités | ExpressRoute | Oui | Toutes les versions |
| Généralités | DataCore FC | Oui* |Prise en charge avec DataCore SANsymphony | Toutes les versions |
| Généralités | DFSR | Oui* |Pris en charge uniquement avec des volumes épinglés localement | Toutes les versions |
| Généralités | Indexation | Oui* |Pour les volumes hiérarchisés, seule l’indexation des métadonnées est prise en charge (aucune donnée).<br>Pour les volumes épinglés localement, l’indexation complète est prise en charge.| Toutes les versions |
| Généralités | Protection contre les virus | Oui* |Pour les volumes hiérarchisés, seule l’analyse des ouvertures et des fermetures est prise en charge.<br> Pour les volumes épinglés localement, l’analyse complète est prise en charge.| Toutes les versions |

*Oui&#42; - Des restrictions et des recommandations sur la solution doivent s’appliquer.*

## Terminologie StorSimple 

Avant de déployer votre solution Microsoft Azure StorSimple, nous vous recommandons de consulter les termes et définitions ci-après.

### Termes et définitions clés

| Terme (acronyme ou abréviation) | Description |
| ------------------------------ | ---------------- |
| enregistrement de contrôle d’accès (ACR) | Enregistrement associé à un volume sur votre appareil Microsoft Azure StorSimple et qui détermine quels sont les hôtes qui peuvent s’y connecter. Ce choix est basé sur le nom complet iSCSI (IQN) des hôtes (dans l’enregistrement de contrôle d’accès) qui se connectent à votre appareil StorSimple.|
| AES-256 | Algorithme AES (Advanced Encryption Standard) 256 bits de chiffrement des données en provenance et à destination du cloud. |
| taille d’unité d’allocation | Plus petite quantité d’espace disque pouvant être allouée pour la conservation d’un fichier sur les systèmes de fichiers Windows. Si la taille d’un fichier n’est pas un multiple pair de la taille du cluster, l’espace supplémentaire doit être utilisé pour la conservation du fichier (jusqu’au prochain multiple de la taille du cluster), ce qui entraîne une perte d’espace et la fragmentation du disque dur. <br>La taille d'unité d'allocation recommandée pour les volumes Azure StorSimple est de 64 Ko, car elle convient aux algorithmes de déduplication.|
| hiérarchisation du stockage automatisé | Déplacement automatique des données moins actives de disques SSD vers des disques durs puis vers un certain niveau du cloud, puis activation de la gestion de l’ensemble du stockage à partir d’une interface utilisateur centrale.|
| catalogue de sauvegarde | Ensemble de sauvegardes habituellement liées par le type d’application utilisé. Cet ensemble est affiché dans la page Catalogue de sauvegarde de l’interface utilisateur du service StorSimple Manager.|
| fichier de catalogue de sauvegarde | Fichier contenant une liste des instantanés disponibles actuellement stockés dans la base de données de sauvegarde de StorSimple Snapshot Manager. |
| stratégie de sauvegarde | Sélection de volumes, type de sauvegarde et emploi du temps qui vous permettent de créer des sauvegardes selon une planification prédéfinie.|
| objets blob (BLOB) | Ensemble de données binaires stockées sous forme d’une seule entité dans un système de gestion de base de données. Les objets blob sont généralement des images, du contenu audio ou d’autres objets multimédias, bien que parfois le code exécutable binaire soit aussi stocké sous forme d’objet blob.|
| protocole CHAP | Protocole utilisé pour authentifier l’homologue d’une connexion, quand l’homologue partage un mot de passe ou un secret. CHAP peut être unidirectionnel ou mutuel. Dans une authentification CHAP unidirectionnelle, la cible authentifie un initiateur. Dans une authentification CHAP mutuelle, la cible authentifie l’initiateur et inversement. | 
| clone | Copie dupliquée d’un volume. |
|Cloud as a Tier (CaaT) | Stockage cloud intégré sous forme de niveau dans l’architecture de stockage pour que l’ensemble du stockage donne l’impression de faire partie d’un seul et même réseau de stockage en entreprise.|
| fournisseur de services cloud (CSP) | Fournisseur de services de cloud computing.|
| instantané cloud | Copie jusqu’à une date et heure des données de volume stockées dans le cloud. Un instantané cloud est équivalent à un instantané répliqué sur un système de stockage hors site distinct. Les instantanés cloud sont particulièrement utiles dans les scénarios de récupération d’urgence.|
| clé de chiffrement de stockage cloud | Mot de passe ou clé permettant à votre appareil StorSimple d’accéder aux données chiffrées envoyées par votre appareil vers le cloud.|
| mise à jour adaptée aux clusters | Gestion des mises à jour logicielles des serveurs dans un cluster de basculement, qui permet d’atténuer ou de supprimer leur impact sur la disponibilité du service.|
| chemin de données | Ensemble d’unités fonctionnelles qui effectuent des opérations de traitement de données interconnectées.|
| désactiver | Action permanente qui interrompt la connexion entre l’appareil StorSimple et le service cloud associé. Les instantanés cloud de l’appareil sont conservés après ce processus et peuvent être clonés ou utilisés pour la récupération d’urgence.|
| mise en miroir de disques | Réplication de volumes de disques logiques sur des disques durs distincts en temps réel pour assurer une disponibilité continue.|
| mise en miroir de disques dynamiques | Réplication de volumes de disques logiques sur des disques dynamiques.|
| disques dynamiques | Format de volume de disque qui utilise le gestionnaire de disque logique (LDM, Logical Disk Manager) pour stocker et gérer les données sur plusieurs disques physiques. Les disques dynamiques peuvent être étendus pour fournir un espace libre plus important.|
| boîtier EBOD | Boîtier secondaire de votre appareil Microsoft Azure StorSimple contenant des disques durs supplémentaires pour étendre la capacité de stockage.|
| allocation fixe | Allocation conventionnelle en matière de stockage, où l’espace de stockage est alloué en fonction des besoins anticipés (généralement au-delà du besoin actuel). Voir aussi *allocation dynamique*.|
| lecteur de disque dur (HDD) | Lecteur qui utilise des plateaux en rotation pour stocker des données.|
| stockage cloud hybride | Architecture de stockage qui utilise des ressources locales et hors site, y compris le stockage cloud.|
| Internet Small Computer System Interface (iSCSI) | Norme réseau de stockage basée sur le protocole IP. Elle permet de lier les équipements ou les installations de stockage de données.|
| initiateur iSCSI | Composant logiciel qui permet à un ordinateur hôte exécutant Windows de se connecter à un réseau de stockage iSCSI externe.|
| nom complet iSCSI (IQN) | Nom unique qui identifie une cible ou un initiateur iSCSI.|
| cible iSCSI | Composant logiciel qui fournit des sous-systèmes de disque iSCSI centralisés dans les réseaux de zone de stockage.|
| archivage dynamique | Approche du stockage dans laquelle les données d’archivage sont accessibles tout le temps (par exemple, elles ne sont pas stockées sur bande hors site). Microsoft Azure StorSimple utilise l’archivage dynamique.|
|volume épinglé localement | un volume qui réside sur l’appareil et n'est jamais hiérarchisé vers le cloud. |
| instantané local | Copie jusqu’à une date et heure des données de volume stockées sur l’appareil Microsoft Azure StorSimple.|
| Microsoft Azure StorSimple | Solution puissante, composée d’un appareil et d’un logiciel de stockage de centre de données. Elle permet aux organisations informatiques de tirer parti du stockage cloud comme s’il s’agissait d’un stockage de centre de données. StorSimple simplifie la protection et la gestion des données tout en réduisant les coûts. La solution regroupe le stockage principal, l’archivage, la sauvegarde et la récupération d’urgence via son intégration transparente au cloud. En associant le stockage SAN et la gestion des données cloud sur une plateforme d’entreprise, les appareils StorSimple fournissent la vitesse, la simplicité et la fiabilité recherchées pour tous les besoins relatifs au stockage.|
| module d’alimentation et de refroidissement (PCM) | Les composants matériels de votre appareil StorSimple incluent l’alimentation électrique et le ventilateur de refroidissement, d’où le nom de module d’alimentation et de refroidissement. Le boîtier principal de l’appareil a deux modules d’alimentation et de refroidissement de 764 W alors que le boîtier EBOD a deux modules d’alimentation et de refroidissement de 580 W.|
| boîtier principal | Boîtier principal de votre appareil StorSimple, qui contient les contrôleurs de la plateforme d’applications.|
| objectif de délai de récupération (RTO) | Délai maximal pour la restauration complète d’un processus ou système métier après une défaillance majeure.| 
|Serial Attached SCSI (SAS) | Type de lecteur de disque dur (HDD).|
| clé de chiffrement de données du service | Clé disponible pour tout nouvel appareil StorSimple qui s’inscrit auprès du service StorSimple Manager. Les données de configuration transférées entre le service StorSimple Manager et l’appareil sont chiffrées à l’aide d’une clé publique. Ensuite, elles ne peuvent être déchiffrées que sur l’appareil à l’aide d’une clé privée. La clé de chiffrement de données du service permet au service d’obtenir cette clé privée pour les opérations de déchiffrement.|
| clé d’inscription du service | Clé qui permet d’inscrire l’appareil StorSimple auprès du service StorSimple Manager. Ainsi, l’appareil s’affiche dans le portail Azure Classic où il peut être géré de manière plus approfondie.|
| Small Computer System Interface (SCSI) | Ensemble de normes permettant de connecter physiquement des ordinateurs et de transférer des données entre ces derniers.|
| Solid State Drive (SSD) | Disque qui ne contient aucune pièce mobile, par exemple un disque mémoire flash.|
| compte de stockage | Ensemble d’informations d’identification d’accès liées à votre compte de stockage pour un fournisseur de services cloud donné.| 
| StorSimple Adapter for SharePoint| Composant Microsoft Azure StorSimple qui étend de manière transparente la protection des données et le stockage StorSimple à des batteries de serveurs SharePoint.|
| service StorSimple Manager | Extension du portail Azure Classic qui vous permet de gérer vos appareils Azure StorSimple locaux et virtuels.|
| StorSimple Snapshot Manager | Composant logiciel enfichable Microsoft Management Console (MMC) pour la gestion des opérations de sauvegarde et de restauration dans Microsoft Azure StorSimple.|
| exécuter une sauvegarde | Fonctionnalité qui permet à l’utilisateur d’exécuter une sauvegarde interactive d’un volume. Il s’agit d’une autre façon d’effectuer une sauvegarde manuelle d’un volume par opposition à une sauvegarde automatique via une stratégie définie.|
| allocation dynamique | Méthode qui permet d’optimiser l’efficacité avec laquelle l’espace de stockage disponible est utilisé dans les systèmes de stockage. Dans une allocation dynamique, le stockage est réparti entre plusieurs utilisateurs en fonction de l’espace minimal dont chaque utilisateur a besoin à un moment donné. Voir aussi *allocation fixe*.|
| hiérarchisation | Organisation des données en regroupements logiques basés sur l’utilisation actuelle, l’âge et les relations aux autres données. StorSimple organise automatiquement les données en niveaux. |
| volume | Zones de stockage logiques présentées sous forme de disques. Les volumes StorSimple correspondent aux volumes montés par l’hôte, notamment ceux détectés via l’utilisation d’iSCSI et d’un appareil StorSimple.|
 | conteneur de volumes | Groupe de volumes et paramètres qui s’y appliquent. Tous les volumes de votre appareil StorSimple sont regroupés dans des conteneurs de volumes. Les paramètres de conteneur de volumes incluent les comptes de stockage, les paramètres de chiffrement des données envoyées vers le cloud avec la clé de chiffrement associée, ainsi que la bande passante consommée pour les opérations impliquant le cloud.|
| groupe de volumes | Dans StorSimple Snapshot Manager, un groupe de volumes est un ensemble de volumes configurés pour faciliter le traitement de la sauvegarde.|
| service VSS| Service du système d’exploitation Windows Server qui facilite la cohérence des applications en communiquant avec les applications compatibles VSS pour coordonner la création d’instantanés incrémentiels. Le service VSS vérifie que les applications sont temporairement inactives quand les instantanés sont créés.|
| Windows PowerShell pour StorSimple | Interface de ligne de commande basée sur Windows PowerShell. Elle permet de faire fonctionner et de gérer votre appareil StorSimple. Tout en conservant certaines des fonctionnalités de base de Windows PowerShell, cette interface offre des applets de commande dédiées supplémentaires pour gérer des appareils StorSimple.|


## Étapes suivantes

En savoir plus sur la [sécurité StorSimple](storsimple-security.md).




 

 

<!---HONumber=AcomDC_0921_2016-->