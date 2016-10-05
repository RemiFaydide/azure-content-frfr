<properties
	pageTitle="Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure | Microsoft Azure"
	description="Premium Storage offre une prise en charge très performante et à faible latence des disques pour les charges de travail utilisant beaucoup d'E/S exécutées sur les machines virtuelles Azure. Les machines virtuelles Azure des séries DS, DSv2 et GS prennent en charge Premium Storage."
	services="storage"
	documentationCenter=""
	authors="aungoo-msft"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="aungoo;robinsh"/>


# Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure

## Vue d'ensemble

Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent Premium Storage stockent les données sur des disques SSD. Vous pouvez migrer les disques de machine virtuelle de votre application dans Azure Premium Storage pour tirer parti de la vitesse et des performances de ces disques.

Une machine virtuelle Azure prend en charge l’association de plusieurs disques Premium Storage afin que vos applications puissent avoir jusqu'à 64 To de stockage par machine virtuelle. Avec Premium Storage, vos applications peuvent atteindre jusqu'à 80 000 IOPS (opérations d'E/S par seconde) par machine virtuelle et un débit de disque de 2 000 Mo par seconde, avec une latence extrêmement faible pour les opérations de lecture.

Avec Premium Storage, Azure offre la possibilité de réellement déplacer vers le cloud vos applications d’entreprise exigeantes comme Dynamics AX, Dynamics CRM, Exchange Server, batteries de serveurs SharePoint et SAP Business Suite. Vous pouvez exécuter une multitude de charges de travail intensives et exigeantes en lien avec les bases de données (comme SQL Server, Oracle, MongoDB, MySQL, Redis) qui nécessitent continuellement des performances élevées et une faible latence sur Premium Storage.

>[AZURE.NOTE] Nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’IOPS dans Azure Premium Storage pour que votre application bénéficie de performances optimales. Si votre disque ne nécessite pas un nombre élevé d'IOPS, vous pouvez limiter les coûts en le conservant dans le stockage Standard qui stocke les données de disque de machine virtuelle sur des disques durs et non des disques SSD.

Pour une prise en main du stockage Azure Premium, consultez la page [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/). Pour plus d’informations sur la migration de vos machines virtuelles existantes vers Premium Storage, consultez la rubrique [Migration vers Azure Premium Storage](storage-migration-to-premium-storage.md).

>[AZURE.NOTE] Premium Storage est actuellement pris en charge dans certaines régions. Pour la liste des régions disponibles, voir [Régions Azure - Services par région](https://azure.microsoft.com/regions/#services).

## Caractéristiques de Premium Storage

**Disques de Stockage Premium** : le Stockage Premium Azure prend en charge les disques de machines virtuelles pouvant être connectés à des machines virtuelles Azure prises en charge par le Stockage Premium (séries DS, DSv2, GS ou Fs). Quand vous utilisez Premium Storage, vous avez le choix entre trois tailles de disque, à savoir, P10 (128 Gio), P20 (512 Gio) et P30 (1 024 Gio), chacun avec ses propres spécifications en matière de performances. Selon les besoins de votre application, vous pouvez connecter un ou plusieurs de ces disques à votre machine virtuelle prise en charge par le Stockage Premium. La section suivante sur les [objectifs de performance et d’extensibilité de Premium Storage](#premium-storage-scalability-and-performance-targets) décrit ces spécifications plus en détail.

**Objet blob de pages Premium** : Premium Storage prend en charge les objets blob de pages Azure, utilisés pour stocker les disques persistants des machines virtuelles Azure. Actuellement, Premium Storage ne prend pas en charge les objets blob de blocs Azure, les objets blob d’ajout Azure, les fichiers Azure, les tables Azure et les files d’attente Azure. Tout autre objet placé dans un compte de stockage Premium est un objet blob de pages, et il s’aligne sur une des tailles configurées prises en charge. Un compte de stockage Premium n’est donc pas conçu pour stocker de petits objets blob.

**Compte Premium Storage** : pour commencer à utiliser Premium Storage, vous devez créer un compte Premium Storage. Si vous préférez utiliser le [portail Azure](https://portal.azure.com), vous pouvez créer un compte Premium Storage en spécifiant le niveau de performances « Premium » et le « stockage localement redondant (LRS) » comme option de réplication. Vous pouvez également créer un compte Premium Storage en spécifiant le type « Premium\_LRS » à l’aide de [l’API REST de stockage](http://msdn.microsoft.com//library/azure/dd179355.aspx) version 2014-02-14 ou ultérieure, [l’API REST de gestion des services](http://msdn.microsoft.com/library/azure/ee460799.aspx) version 2014-10-01 ou ultérieure (déploiements classiques), la [Référence de l’API REST du fournisseur de ressources Azure Storage](http://msdn.microsoft.com/library/azure/mt163683.aspx) (déploiements Resource Manager) et [Azure PowerShell](../powershell-install-configure.md) version 0.8.10 ou ultérieure. Pour en savoir plus sur les limites de compte de stockage Premium, voir la section suivante sur les [objectifs de performance et d’extensibilité de Premium Storage](#premium-storage-scalability-and-performance-targets).

**Stockage Premium localement redondant** : un compte Premium Storage prend uniquement en charge l’option Stockage localement redondant (LRS) pour la réplication et conserve trois copies des données dans une même région. Pour plus d’informations sur la géo-réplication pendant l’utilisation de Premium Storage, voir la section [Captures instantanées et copie d’objets blob](#snapshots-and-copy-blob) dans cet article.

Azure utilise le compte de stockage comme conteneur pour votre système d'exploitation et vos disques de données. Lorsque vous créez une machine virtuelle Azure de série DS, DSv2, GS ou Fs, puis sélectionnez un compte de stockage Premium, votre système d’exploitation et les disques de données sont stockés dans ce compte de stockage.

Vous pouvez utiliser le stockage Premium pour disques de deux manières :
- Commencez par créer un compte Premium Storage. Ensuite, lorsque vous créez une machine virtuelle DS, DSv2, GS ou Fs, sélectionnez le compte de stockage Premium dans les paramètres de configuration du stockage. OU,
- Lors de la création d’une machine virtuelle DS, DSv2, GS ou Fs, créez un compte de stockage Premium dans les paramètres de configuration du stockage, ou laissez le Portail Azure créer un compte de stockage Premium par défaut.

Pour des instructions détaillées, voir la section [Démarrage rapide](#quick-start), plus loin dans cet article.

>[AZURE.NOTE] Un compte de stockage premium ne peut pas être mappé sur un nom de domaine personnalisé.

## Machines virtuelles non prises en charge par le Stockage Premium

Le Stockage Premium prend en charge les machines virtuelles Azure des séries DS, DSv2, GS et Fs. Vous pouvez utiliser des disques de Stockage Standard et Premium avec les machines virtuelles prises en charge par le Stockage Premium. Mais vous ne pouvez pas utiliser des disques de Stockage Premium avec des séries de machines virtuelles non compatibles avec le Stockage Premium.

Pour plus d’informations sur les tailles et les types de machines virtuelles Azure disponibles en ce qui concerne les machines virtuelles Windows, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md). Pour plus d’informations sur les tailles et les types de machines virtuelles disponibles en ce qui concerne les machines virtuelles Linux, consultez [Tailles des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Voici quelques-unes des caractéristiques des machines virtuelles des séries DS, DSv2, GS et Fs :

**Service cloud** : vous pouvez ajouter des machines virtuelles de la série DS à un service cloud incluant uniquement des machines virtuelles de ce type. Évitez d’ajouter des machines virtuelles de la série DS à un service cloud incluant des machines virtuelles d’un autre type. Vous pouvez migrer vos disques durs virtuels vers un nouveau service cloud exécutant uniquement des machines virtuelles de la série DS. Si vous souhaitez conserver la même adresse IP virtuelle (VIP) pour le nouveau service cloud hébergeant vos machines virtuelles de la série DS, utilisez les [adresses IP réservées](../virtual-network/virtual-networks-instance-level-public-ip.md). Des machines virtuelles de la série GS peuvent être ajoutés à un service cloud existant exécutant seulement des machines virtuelles de la série GS.

**Disque du système d’exploitation** : les machines virtuelles Azure prises en charge par le Stockage Premium peuvent être configurées pour utiliser un disque de système d’exploitation (SE) hébergé sur un compte de stockage Standard ou Premium. Nous vous recommandons d’utiliser un disque de système d’exploitation basé sur Premium Storage pour une meilleure expérience.

**Disques de données** : vous pouvez utiliser des disques de stockage tant Standard que Premium dans une même machine virtuelle prise en charge par le Stockage Premium. Avec le Stockage Premium, vous pouvez configurer une machine virtuelle prise en charge par le Stockage Premium, puis connecter plusieurs disques de données persistants à celle-ci. Si nécessaire, vous pouvez répartir les données sur les disques pour augmenter la capacité et les performances du volume.

> [AZURE.NOTE] Si vous équilibrez les disques de données de stockage Premium à l’aide des [espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx), vous devez les configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d'une distribution inégale du trafic sur les disques. Par défaut, l'interface utilisateur (IU) du Gestionnaire de serveur vous permet de créer des colonnes jusqu'à 8 disques. Mais si vous avez plus de 8 disques, vous devez utiliser PowerShell pour créer le volume et spécifier manuellement le nombre de colonnes. Dans le cas contraire, l'IU du Gestionnaire de serveur continue d'utiliser 8 colonnes, même si vous disposez de plusieurs disques. Par exemple, si vous disposez de 32 disques dans un agrégat unique, vous devez spécifier 32 colonnes. Vous pouvez utiliser le paramètre *NumberOfColumns* de l’applet de commande PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) pour spécifier le nombre de colonnes utilisées par le disque virtuel. Pour plus d'informations, consultez [Vue d'ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx) et [Forum aux Questions sur les espaces de stockage](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

**Cache** : les machines virtuelles prises en charge par le Stockage Premium ont une capacité de mise en cache unique, permettant d’obtenir des niveaux élevés de débit et de latence, supérieurs aux performances du disque de Stockage Premium sous-jacent. Vous pouvez configurer une stratégie de mise en cache de disque sur les disques Premium Storage en ReadOnly (lecture seule), ReadWrite (lecture/écriture) ou None (aucune). La stratégie de mise en cache de disque par défaut est ReadOnly pour tous les disques de données Premium, et ReadWrite pour les disques de système d’exploitation. Utilisez le paramètre de configuration approprié pour optimiser les performances de votre application. Ainsi, pour préparer des disques de données lourds ou en lecture seule, contenant par exemple des fichiers de données SQL Server, définissez la stratégie de mise en cache « ReadOnly ». Pour écrire des disques de données lourds ou en écriture seule, contenant, par exemple, des fichiers journaux SQL Server, définissez la stratégie de mise en cache sur « None ». Pour en savoir plus sur l’optimisation de votre conception avec Premium Storage, consultez [Conception optimisée pour les performances avec Premium Storage](storage-premium-storage-performance.md).

**Analyses** : pour analyser les performances de machines virtuelles utilisant des disques sur des comptes Premium Storage, vous pouvez activer les diagnostics de machine virtuelle Azure dans le portail Azure. Pour plus d’informations, reportez-vous à la rubrique [Surveillance des machines virtuelles Microsoft Azure avec l’extension Azure Diagnostics](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). Pour afficher les performances du disque, utilisez les outils du système d’exploitation, tels que l’[Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx) pour les machines virtuelles Windows, et [IOSTAT](http://linux.die.net/man/1/iostat) pour les machines virtuelles Linux.

**Performances et limites de mise à l’échelle des machines virtuelles** : à chaque taille de machine virtuelle prise en charge par le Stockage Premium est associée une spécification relative aux performances et limites de mise à l’échelle en relation avec les opérations d’E/S par seconde (IOPS), la bande passante et le nombre de disques pouvant être connectés par machine virtuelle. Lorsque vous utilisez des disques de Stockage Premium avec des machines virtuelles prises en charge par le Stockage Premium, assurez-vous de disposer de suffisamment d’IOPS et de bande passante sur votre machine virtuelle pour gérer le trafic du disque. Par exemple, une machine virtuelle STANDARD\_DS1 a une bande passante dédiée de 32 Mo par seconde pour le trafic des disques de stockage Premium. Un disque Premium Storage P10 peut fournir 100 Mo par seconde de bande passante. Si un disque Premium Storage P10 est connecté à cette machine virtuelle, il ne peut pas dépasser 32 Mo par seconde, au lieu des 100 Mo par seconde qu’il est capable d’atteindre.

Actuellement, la machine virtuelle DS la plus puissante est STANDARD\_DS14, qui offre un débit maximum de 512 Mo par seconde sur l’ensemble des disques. La machine virtuelle de série GS DS la plus puissante est STANDARD\_GS5, qui offre un débit allant jusqu’à 2 000 Mo par seconde sur l’ensemble des disques. Notez que ces limites ne valent que pour le trafic de disques, pas pour les présences dans le cache ou le trafic réseau. Une bande passante distincte est disponible pour le trafic réseau des machines virtuelles. Il ne s’agit pas de la bande passante dédiée aux disques de stockage Premium.

Pour obtenir les informations les plus récentes sur le maximum d’IOPS et de bande passante concernant les machines virtuelles prises en charge par le Stockage Premium, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md) ou [Tailles des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Pour en savoir plus sur les disques Premium Storage et leurs limites d’E/S par seconde et de débit, voir le tableau de la section relative aux [objectifs de performances et d’extensibilité de Premium Storage](#premium-storage-scalability-and-performance-targets) dans cet article.

## Objectifs de performances et d’extensibilité de Premium Storage

Dans cette section, nous allons décrire les objectifs de performances et d’extensibilité à prendre en considération lors de l’utilisation de Premium Storage.

### Limites de compte Premium Storage

Les objectifs d’extensibilité des comptes Premium Storage sont les suivants :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacité totale du compte</strong></td>
	<td><strong>Bande passante totale d’un compte de stockage localement redondant</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacité du disque&#160;: 35&#160;To</li>
       <li type=round>Capacité d’instantanés&#160;: 10&#160;To</li>
    </ul>
	</td>
	<td>Jusqu'à 50&#160;Go par seconde pour les données entrantes/sortantes</td>
</tr>
</tbody>
</table>

- Les données entrantes sont toutes les données (demandes) envoyées à un compte de stockage.
- Les données sortantes sont toutes les données (réponses) reçues d'un compte de stockage.

Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](storage-scalability-targets.md).

Si les besoins de votre application dépassent les objectifs d'extensibilité d'un compte de stockage unique, générez votre application pour qu'elle utilise plusieurs comptes de stockage et partitionnez vos données sur ces comptes. Par exemple, si vous souhaitez joindre des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage, la limite d’un compte Premium Storage étant de 35 To. Vérifiez qu’un compte de stockage Premium n’a jamais plus de 35 To de disques approvisionnés.

### Limites des disques Premium Storage

Quand vous configurez un disque sur un compte de stockage Premium, le nombre d'opération d'E/S par seconde (IOPS) et le débit (bande passante) qu'il peut recevoir dépend de la taille du disque. Actuellement, il existe trois types de disques de stockage Premium : P10, P20 et P30. Chaque type présente des limites spécifiques en matière d'IOPS et de débit, comme indiqué dans le tableau suivant :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Type de disque de stockage Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Taille du disque</strong></td>
	<td>128 Go</td>
	<td>512 Go</td>
	<td>1024&#160;Go (1 To)</td>
</tr>
<tr>
	<td><strong>IOPS par disque</strong></td>
	<td>500</td>
	<td>2&#160;300</td>
	<td>5&#160;000</td>
</tr>
<tr>
	<td><strong>Débit par disque</strong></td>
	<td>100&#160;Mo par seconde </td>
	<td>150&#160;Mo par seconde </td>
	<td>200&#160;Mo par seconde </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Vérifiez que la bande passante disponible sur votre machine virtuelle est suffisante pour le trafic de disques, comme l’explique la section [Machines virtuelles prises en charge par le Stockage Premium](#ds-dsv2-and-gs-series-vms) plus haut dans cet article. Dans le cas contraire, le débit et l’IOPS du disque seront limitées à des valeurs inférieures basées sur les limites de la machine virtuelle, et non aux valeurs de disque mentionnées dans le tableau précédent.

Voici quelques éléments importants que vous devez savoir sur les objectifs de performances et d’extensibilité de Premium Storage :

- **Capacité et performances configurées** : lorsque vous configurez un disque Premium Storage, contrairement au stockage standard, la capacité, les IOPS et le débit de ce disque sont garantis. Par exemple, si vous créez un disque P30, Azure configure une capacité de stockage de 1 024 Go, 5 000 IOPS et un débit de 200 Mo par seconde pour ce disque. Votre application peut utiliser tout ou partie de la capacité et des performances.

- **Taille du disque** : Azure mappe la taille du disque (arrondie à la valeur supérieure) à l’option de disque Premium Storage la plus proche, comme indiqué dans le tableau. Par exemple, un disque de 100 Go correspond à l’option P10 et peut prendre en charge jusqu'à 500 IOPS et un débit de 100 Mo par seconde. De même, un disque de 400 Go correspond à une option P20 et peut prendre en charge jusqu'à 2 300 IOPS et un débit de 150 Mo par seconde.

	> [AZURE.NOTE] Vous pouvez facilement augmenter la taille des disques existants. Par exemple, vous pouvez augmenter la taille d'un disque de 30 Go à 128 Go ou 1 To. Ou convertir votre disque P20 en disque P30, si vous avez besoin de davantage de capacités ou de plus d’IOPS et de débit. Vous pouvez augmenter la taille du disque à l’aide de l’applet de commande PowerShell « Update-AzureDisk » avec la propriété «-ResizedSizeInGB ». Pour effectuer cette action, le disque doit être détaché de la machine virtuelle ou cette dernière doit être arrêtée.

- **Taille d’E/S** : la taille d’une unité d’E/S est de 256 Ko. Si la taille des données transférées est inférieure à 256 Ko, elles sont considérées comme une seule unité d'E/S. Les tailles d'E/S supérieures sont divisées en plusieurs unités d'E/S de 256 Ko. Par exemple, 1 100 Ko d'E/S correspond à cinq unités d'E/S.

- **Débit** : la limite de débit comprend les écritures sur le disque et les lectures à partir de ce dernier, qui ne sont pas effectuées à partir du cache. Par exemple, un disque P10 offre un débit de 100 Mo par seconde. Les débits valides pour un disque P10 sont, par exemple,
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td><strong>Débit maximum par disque P10</strong></td>
<td><strong>Lectures à partir du disque non mises en cache</strong></td>
<td><strong>Lectures à partir sur le disque non mises en cache</strong></td>
</tr>
<tr>
<td>100&#160;Mo par seconde</td>
<td>100&#160;Mo par seconde</td>
<td>0</td>
</tr>
<tr>
<td>100&#160;Mo par seconde</td>
<td>0</td>
<td>100&#160;Mo par seconde</td>
</tr>
<tr>
<td>100&#160;Mo par seconde </td>
<td>60&#160;Mo par seconde </td>
<td>40&#160;Mo par seconde </td>
</tr>
</tbody>
</table>

- **Présences dans le cache** : les présences dans le cache ne sont pas limitées par les IOPS et le débit du disque alloués. Par exemple, quand vous utilisez un disque de données avec le paramètre de cache ReadOnly sur une machine virtuelle prise en charge par le Stockage Premium, les lectures effectuées à partir du cache ne sont pas soumises aux limites des disques de Stockage Premium. Il est donc possible d’obtenir un très haut débit à partir d'un disque, si la charge de travail concerne essentiellement des lectures. Notez que le cache est soumis à des limites IOPS/Débit séparées au niveau de la machine virtuelle, en fonction de la taille de celle-ci. Les machines virtuelles DS exécutent environ 4000 IOPS et ont un débit de 33 Mo/s par cœur pour les E/S du cache et du disque SSD local. Les machines virtuelles de la série GS sont limitées à 5 000 IOPS et à 50 Mo/s par cœur pour le cache et les E/S du disque SSD local.

## Limitation
Vous pouvez constater une limitation de bande passante si les IOPS ou le débit de votre application dépassent les limites allouées à un disque Premium Storage, ou si le trafic total de l’ensemble des disques de la machine virtuelle dépasse la limite de bande passante de disque disponible pour la machine virtuelle. Pour éviter cette situation, nous vous recommandons de limiter le nombre de demandes d'E/S en attente, en fonction des objectifs d'extensibilité et de performances du disque configuré et de la bande passante de disques disponible pour la machine virtuelle.

Quand votre application est conçue pour éviter les limitations, elle bénéficie d'une latence moindre. En revanche, si le nombre de demandes d'E/S en attente est trop faible, votre application ne peut pas bénéficier des niveaux maximum de débit et d'IOPS disponibles sur le disque.

Les exemples suivants montrent comment calculer les niveaux de limitation. Tous les calculs sont basés sur une taille d’unité d’E/S de 256 Ko.

### Exemple 1 :
Votre application a traité 495 unités d’E/S d’une taille de 16 Ko par seconde sur un disque P10. Celles-ci sont comptées comme 495 unités d’E/S par seconde (IOPS). Si vous tentez une E/S de 2 Mo dans la même seconde, le total d'unités d'E/S est égal à 495 + 8. En effet, une E/S de 2 Mo équivaut à 2 048 Ko / 256 Ko = 8 unités d'E/S, avec une taille d'unité d'E/S égale à 256 Ko. Étant donné que la somme de 495 + 8 dépasse la limite de 500 IOPS du disque, il se produit une limitation.

### Exemple 2 :
Votre application a traité 400 unités d’E/S de 256 Ko sur un disque P10. La bande passante totale consommée est de (400 * 256) / 1 024 = 100 Mo/s. Un disque P10 a une limite de débit de 100 Mo par seconde. Si votre application tente d'effectuer davantage d'E/S dans la même seconde, elle sera limitée, car elle dépassera la limite allouée.

### Exemple 3 :
Vous avez une machine virtuelle DS4 avec deux disques P30. Chaque disque P30 peut gérer un débit de 200 Mo par seconde. Toutefois, une machine virtuelle DS4 a une bande passante disque maximale de 256 Mo par seconde. Par conséquent, vous ne pouvez pas obtenir un débit maximal sur tous les disques de cette machine virtuelle DS4 en même temps. La solution consiste à maintenir un trafic de 200 Mo par seconde sur un disque et un trafic de 56 Mo par seconde sur l'autre disque. Si la somme de votre trafic de disques excède 256 Mo par seconde, il est limité.

>[AZURE.NOTE] si le trafic du disque consiste essentiellement en des E/S de petite taille, il est très probable que votre application atteindra la limite d'IOPS avant la limite de débit. En revanche, si le trafic du disque consiste essentiellement en des E/S de grande taille, il est très probable que votre application atteindra la limite de débit avant la limite d'IOPS. Vous pouvez optimiser la capacité de votre application en termes d’IOPS et de débit en utilisant des tailles d’E/S optimales et en limitant le nombre de demandes d’E/S en attente pour le disque.

Pour en savoir plus sur la conception appropriée permettant des performances élevées à l’aide de Premium Storage, consultez [Conception optimisée pour les performances avec Premium Storage](storage-premium-storage-performance.md).

## Captures instantanées et copie d’objets blob
Vous pouvez créer une capture instantanée pour le stockage Premium de la même façon que pour le stockage Standard. Étant donné que Premium Storage prend uniquement en charge l’option Stockage localement redondant (LRS) pour la réplication, nous vous recommandons de créer des captures instantanées et de les copier sur un compte de stockage Standard géo-redondant. Pour plus d'informations, consultez [Options de redondance du stockage Azure](storage-redundancy.md).

Si un disque est attaché à une machine virtuelle, certaines opérations d'API ne sont pas autorisées sur l'objet blob de pages utilisé par le disque. Par exemple, vous ne pouvez pas effectuer une opération [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) sur cet objet blob, tant que le disque est attaché à une machine virtuelle. À la place, commencez par créer une capture instantanée de cet objet blob à l'aide de la méthode [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) de l'API REST, puis exécutez l'opération [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) de la capture instantanée pour copier le disque attaché. Vous pouvez également détacher le disque et effectuer les opérations nécessaires sur l'objet blob sous-jacent.

Les limites suivantes s’appliquent aux instantanés d’objet blob de Premium Storage :
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Limites de Premium Storage</strong></td>
	<td><strong>Valeur</strong></td>
</tr>
<tr>
	<td>Nombre maximal d’instantanés par objet blob</td>
	<td>100</td>
</tr>
<tr>
	<td>Capacité du compte de stockage pour les instantanés (inclut uniquement les données des instantanés, pas celles d’un objet blob de base)</td>
	<td>10&#160;To</td>
</tr>
<tr>
	<td>Temps minimal entre deux instantanés consécutifs</td>
	<td>10&#160;minutes</td>
</tr>
</tbody>
</table>

Pour conserver des copies géo-redondantes de vos captures instantanées, vous pouvez copier des captures instantanées d’un compte Premium Storage vers un compte de stockage Standard géo-redondant à l’aide des opérations AzCopy ou Copy Blob. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) et [Copie d’un objet blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).

Pour plus d'informations sur l'exécution d'opérations REST sur les objets blob de pages dans les comptes de stockage Premium, consultez [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969) dans la bibliothèque MSDN.

## Utilisation de machines virtuelles Linux avec le stockage Premium
Consultez les instructions importantes ci-dessous pour configurer vos machines virtuelles Linux sur un stockage Premium :

- Pour tous les disques de stockage Premium dont le paramètre de cache est « ReadOnly » ou « None », vous devez désactiver les « barrières » lors du montage du système de fichiers pour atteindre les objectifs d'évolutivité du stockage Premium. Ces barrières sont inutiles dans ce cas de figure, car les écritures sur les disques sauvegardés de stockage Premium sont pérennes avec ces paramètres de cache. Lorsque la demande d'écriture est terminée, les données sont stockées dans le magasin permanent. Utilisez les méthodes suivantes pour désactiver les « barrières » en fonction de votre système de fichiers :
	- Si vous utilisez **reiserFS**, désactivez les barrières à l'aide de l'option de montage « barrier=none ». (Pour activer les barrières, utilisez « barrier=flush ».)
	- Si vous utilisez **ext3/ext4**, désactivez les barrières à l'aide de l'option de montage « barrier=0 ». (Pour activer les barrières, utilisez « barrier=1 ».)
	- Si vous utilisez **XFS**, désactivez les barrières à l'aide de l'option de montage « nobarrier ». (Pour activer les barrières, utilisez l’option « barrier ».)

- Pour les disques de stockage Premium dont le paramètre de cache est « ReadWrite », les barrières doivent être activées pour pérenniser les écritures.
- Pour conserver les étiquettes de volume après le redémarrage de la machine virtuelle, vous devez mettre à jour /etc/fstab avec les références UUID aux disques. Consultez également [Association d’un disque de données à une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-classic-attach-disk.md).

Les distributions Linux suivantes ont été validées avec le stockage Premium. Nous vous recommandons de mettre vos machines virtuelles au niveau de l’une de ces versions (ou d’une version ultérieure) pour améliorer les performances et la stabilité du stockage Premium. De plus, certaines versions nécessitent également la dernière version de LIS (Linux Integration Services version 4.0 pour Microsoft Azure). Cliquez sur le lien ci-dessous pour effectuer le téléchargement et l'installation. Nous allons continuer à ajouter des images à la liste, à chaque validation supplémentaire. Veuillez noter que, selon nos validations, les performances varient pour ces images. Elles dépendent également des caractéristiques de la charge de travail et des paramètres des images. Chaque image est optimisée pour une charge de travail particulière.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Distribution</strong></td>
	<td><strong>Version</strong></td>
	<td><strong>Noyau pris en charge</strong></td>
	<td><strong>Détails</strong></td>
</tr>
<tr>
	<td rowspan="2"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110+</td>
	<td>Ubuntu-12_04_5-LTS-amd64-Server-20150119-fr-FR-30GB</td>
</tr>
<tr>
	<td>14.04+</td>
	<td>3.13.0-44.73+</td>
	<td>Ubuntu-14_04_1-LTS-amd64-Server-20150123-fr-FR-30GB</td>
</tr>
<tr>
	<td><strong>Debian</strong></td>
	<td>7.x, 8.x</td>
	<td>3.16.7-ckt4-1+</td>
    <td> </td>
</tr>
<tr>
	<td rowspan="2"><strong>SUSE</strong></td>
	<td>SLES&#160;12</td>
	<td>3.12.36-38.1+</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td>SLES 11 SP4</td>
    <td>3.0.101-0.63.1+</td>
    <td> </td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0+</td>
	<td>3.18.4+</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5, 6.6, 6.7, 7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 requis</a> <br/>
		*Voir la remarque ci-dessous*
	</td>
</tr>
<tr>
	<td>7.1+</td>
	<td>3.10.0-229.1.2.el7+</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 recommandée</a> <br/>
		*Voir la remarque ci-dessous*
	</td>
</tr>
<tr>
	<td><strong>RHEL</strong></td>
	<td>6.8+, 7.2+</td>
	<td> </td>
	<td></td>
</tr>
<tr>
	<td rowspan="3"><strong>Oracle</strong></td>
    <td>6.8+, 7.2+</td>
    <td> </td>
    <td> UEK4 ou RHCK </td>

</tr>
<tr>
	<td>7.0-7.1</td>
	<td> </td>
	<td>UEK4 ou RHCK avec <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a></td>
</tr>
<tr>
	<td>6.4-6.7</td>
	<td></td>
	<td>UEK4 ou RHCK avec <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a></td>
</tr>
</tbody>
</table>


### Pilotes LIS pour Openlogic CentOS

Les clients exécutant des machines virtuelles OpenLogic CentOS doivent exécuter la commande suivante pour installer les pilotes les plus récents :

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

Un redémarrage est nécessaire pour activer les nouveaux pilotes.

## Tarification et facturation
Les considérations de facturation suivantes s'appliquent à l'utilisation du stockage Premium :
- Taille de disque Premium Storage/de l’objet blob
- Instantanés Premium Storage
- Transferts de données sortantes

**Taille de disque Premium Storage/de l’objet blob** : la facturation pour un disque Premium Storage/un objet blob dépend de la taille configurée du disque/de l’objet blob. Azure mappe la taille approvisionnée (arrondie à la valeur supérieure) à l’option de disque Premium Storage la plus proche, comme indiqué dans le tableau de la section [Objectifs d’extensibilité et de performances avec Premium Storage](#premium-storage-scalability-and-performance-targets). Tous les objets stockés dans un compte de stockage Premium sont mis en correspondance avec l’une des tailles prises en charge configurées et sont facturés en conséquence. Évitez donc d’utiliser un compte Premium Storage pour stocker de petits objets blob. La facturation de n’importe quel disque/objet blob configuré est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage Premium. Par exemple, si vous configurez un disque P10 et le supprimez au bout de 20 heures, vous êtes facturé 20 heures pour l'offre P10. Le montant facturé est indépendant de la quantité de données écrites sur le disque ou de la quantité de débit/IOPS utilisés.

**Instantanés Premium Storage** : les captures instantanées sur Premium Storage sont facturées en fonction de la capacité supplémentaire utilisée par les captures instantanées. Pour plus d'informations sur les captures instantanées, consultez [Création d'un instantané d'objet blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

**Transferts de données sortantes** : les [transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

Pour plus d’informations sur la tarification du stockage Premium et les machines virtuelles prises en charge par le Stockage Premium, consultez :

- [Tarification d’Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Sauvegarde
Les machines virtuelles utilisant Premium Storage peuvent être sauvegardées à l’aide d’Azure Backup. [Détails supplémentaires](../backup/backup-azure-vms-first-look-arm.md).

## Quick Start

## Créer et utiliser un compte de stockage Premium pour un disque de données de machine virtuelle

Dans cette section, nous décrivons les scénarios suivants utilisant le portail Azure, Azure PowerShell et Azure CLI :

- Création d’un compte Premium Storage.
- Création d’une machine virtuelle et connexion d’un disque de données lors de l’utilisation de Premium Storage.
- Modification de la stratégie de mise en cache d’un disque de données connecté à une machine virtuelle.

### Création d’une machine virtuelle Azure utilisant Premium Storage à partir du portail Azure

#### I. Création d’un compte Premium Storage dans le portail Azure

Cette section explique comment créer un compte Premium Storage à partir du portail Azure.

1.	Connectez-vous au [portail Azure](https://portal.azure.com). Consultez l’offre [d’essai gratuit](https://azure.microsoft.com/pricing/free-trial/) si vous n’avez pas encore d’abonnement.

2. Dans le menu Hub, sélectionnez **Nouveau** -> **Données et stockage** -> **Compte de stockage**.

3. Entrez un nom pour votre compte de stockage.

	> [AZURE.NOTE] Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
	>  
	> Le nom de votre compte de stockage doit être unique dans Azure. Le portail Azure indique si le nom du compte de stockage sélectionné est déjà utilisé.

4. Sélectionnez le modèle de déploiement à utiliser : **Resource Manager** ou **Classique**. **Resource Manager** est le modèle de déploiement recommandé. Pour plus d’informations, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).

5. Spécifiez le niveau de performances pour le compte de stockage en tant que **Premium**.

6. **Stockage localement redondant (LRS)** est la seule option de réplication disponible avec Premium Storage. Pour plus d’informations sur les options de réplication d’Azure Storage, consultez [Réplication Azure Storage](storage-redundancy.md).

7. Sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.

8. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md).

9. Sélectionnez l’emplacement géographique de votre compte de stockage. Vous pouvez vérifier que Premium Storage est disponible dans le lieu sélectionné en consultant [Services Azure par région](https://azure.microsoft.com/regions/#services).

10. Cliquez sur **Créer** pour créer le compte de stockage.

#### II. Création d’une machine virtuelle Azure via le portail Azure

Pour pouvoir utiliser le Stockage Premium, vous devez créer une machine virtuelle prise en charge par le Stockage Premium. Pour créer une machine virtuelle DS, DSv2, GS ou Fs, suivez les étapes décrites dans [Créer une machine virtuelle Windows dans le Portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

#### III. Connexion d’un disque de données Premium Storage via le portail Azure

1. Recherchez la machine virtuelle DS, DSv2, GS ou Fs nouvelle ou existante dans le Portail Azure.
2. Dans la machine virtuelle, sous **Tous les paramètres**, accédez à **Disques**, puis cliquez sur **Attacher un nouveau disque**.
3. Entrez le nom de votre disque de données, puis sélectionnez le **Type** **Premium**. Sélectionnez les paramètres **Taille** et **Mise en cache de l’hôte** souhaités.

	![Disque Premium][Image1]

Pour plus de détails, consultez [Connexion d’un disque de données dans le portail Azure](../virtual-machines/virtual-machines-windows-attach-disk-portal.md).

#### IV. Modification de la stratégie de mise en cache via le portail Azure

1. Recherchez la machine virtuelle DS, DSv2, GS ou Fs nouvelle ou existante dans le Portail Azure.
2. Dans la machine virtuelle, sous Tous les paramètres, accédez à Disques, puis cliquez sur le disque que vous souhaitez modifier.
3. Définissez la valeur souhaitée pour l’option Mise en cache de l’hôte : None, ReadOnly ou ReadWrite

>[AZURE.WARNING] La modification du paramètre de cache d’un disque Azure détache et rattache le disque cible. S’il s’agit du disque du système d’exploitation, la machine virtuelle redémarre. Arrêtez toutes les applications et services qui risquent d’être affectés par cette indisponibilité avant de modifier le paramètre de cache du disque.

### Création d’une machine virtuelle Azure utilisant Premium Storage via Azure PowerShell

#### I. Création d’un compte Premium Storage dans Azure PowerShell

Cet exemple PowerShell montre comment créer un compte de stockage Premium et associer un disque de données utilisant ce compte à une nouvelle machine virtuelle Azure.

1. Configurez votre environnement PowerShell en suivant les étapes indiquées à la section [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
2. Démarrez la console PowerShell, connectez-vous à votre abonnement et exécutez l’applet de commande PowerShell suivante dans la fenêtre de console. Comme le montre cette instruction PowerShell, vous devez attribuer au paramètre **Type** la valeur **Premium\_LRS** lors de la création d’un compte de stockage Premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II. Création d’une machine virtuelle Azure via Azure PowerShell

Ensuite, créez une machine virtuelle de série DS et spécifiez que vous voulez un stockage Premium en exécutant les applets de commande PowerShell suivantes dans la fenêtre de console. Vous pouvez créer une machine virtuelle de série GS en utilisant la même procédure. Spécifiez la taille de machine virtuelle appropriée dans les commandes. Par exemple, pour Standard\_GS2 :

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

#### III. Connexion d’un disque de données Premium Storage via Azure PowerShell

Si vous voulez davantage d’espace disque pour votre machine virtuelle, créez et attachez un disque de données à une machine virtuelle prise en charge par le Stockage Premium existante après sa création en exécutant les applets de commande PowerShell suivantes dans la fenêtre de console :

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV. Modification de la stratégie de mise en cache via Azure PowerShell

Pour mettre à jour la stratégie de mise en cache du disque, notez le numéro de LUN du disque de données connecté. Exécutez la commande suivante pour mettre à jour le disque de données connecté au numéro de LUN 2, de façon à ce qu’il soit en lecture seule.

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

>[AZURE.WARNING] La modification du paramètre de cache d’un disque Azure détache et rattache le disque cible. S’il s’agit du disque du système d’exploitation, la machine virtuelle redémarre. Arrêtez toutes les applications et services qui risquent d’être affectés par cette indisponibilité avant de modifier le paramètre de cache du disque.

### Créer une machine virtuelle Azure utilisant le stockage Premium à l’aide de l’interface de ligne de commande Azure

[L’interface de ligne de commande Azure](../xplat-cli-install.md) (Azure CLI) fournit un ensemble de commandes multiplateformes open source, utilisables sur la plateforme Azure. Les exemples suivants montrent comment utiliser cette interface Azure (versions 0.8.14 et ultérieures) pour créer un compte Premium Storage, ainsi qu’une machine virtuelle, et connecter un nouveau disque de données à partir d’un compte Premium Storage.

#### I. Création d’un compte Premium Storage via Azure CLI

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II. Création d’une machine virtuelle de série DS via Azure CLI

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-FR-30GB" -u "myusername" -p "passwd@123"

Afficher les informations sur la machine virtuelle

	azure vm show premium-test-vm

#### III. Connexion d’un nouveau disque de données Premium via Azure CLI

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

Afficher les informations sur le nouveau disque de données

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV. Modification de la stratégie de mise en cache du disque

Pour modifier la stratégie de cache sur l'un de vos disques à l'aide de l’interface de ligne de commande Azure, exécutez la commande suivante :

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

Notez que l’option de la stratégie de mise en cache peut être ReadOnly, None ou ReadWrite. Pour les autres d'options, consultez l'aide en exécutant la commande suivante :

		azure vm disk attach --help

>[AZURE.WARNING] La modification du paramètre de cache d’un disque Azure détache et rattache le disque cible. S’il s’agit du disque du système d’exploitation, la machine virtuelle redémarre. Arrêtez toutes les applications et services qui risquent d’être affectés par cette indisponibilité avant de modifier le paramètre de cache du disque.

## FAQ

1. **Puis-je connecter des disques de données Standard et Premium à une machine virtuelle prise en charge par le Stockage Premium ?**

	Oui. Vous pouvez connecter des disques de données Standard et Premium à une machine virtuelle d’une série prise en charge par le Stockage Premium.

2. **Puis-je connecter des disques de données Standard et Premium à une machine virtuelle de la série D, Dv2, G ou F ?**

	Non. Vous pouvez uniquement connecter un disque de données Standard à des machines virtuelles qui ne sont pas des séries prises en charge par le Stockage Premium.

3. **Si je crée un disque de données Premium à partir d’un disque dur virtuel existant dont la taille était de 80 Go de taille, comment cela me coûte-t-il ?**

	Un disque de données Premium créé à partir d’un disque dur virtuel de 80 Go est considéré comme ayant la taille de disque Premium disponible suivante, soit un disque P10. Vous êtes facturé au tarif d’un disque P10.

4. **L’utilisation de Premium Storage occasionne-t-elle des coûts de transaction ?**

	Il existe un coût fixe pour chaque taille de disque configurée avec un nombre d’IOPS et un débit donnés. Les seuls autres coûts sont liés à la bande passante sortante et à la capacité de captures instantanées, le cas échéant. Pour plus d’informations, consultez [Tarification du Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

5. **Où puis-je stocker les diagnostics de démarrage de ma machine virtuelle d’une série prise en charge par le Stockage Premium ?**

	Créez un compte de stockage Standard pour stocker les diagnostics de démarrage de votre machine virtuelle d’une série prise en charge par le Stockage Premium.

6. **Combien d’IOPS et quel débit puis-je obtenir à partir du cache disque ?**

	Les limites combinées pour le cache et le disque SSD local d’une machine de la série DS sont de 4 000 IOPS par cœur et 33 Mo par seconde par cœur. La série GS offre 5 000 IOPS par cœur et 50 Mo par seconde par cœur.

7. **Qu’est-ce que le disque SSD local dans une machine virtuelle d’une série prise en charge par le Stockage Premium ?**

	Le disque SSD local est un stockage temporaire inclus dans une machine virtuelle d’une série prise en charge par le Stockage Premium. Ce stockage temporaire n’occasionne aucun frais supplémentaire. Il est recommandé de ne pas utiliser ce stockage temporaire ou un SSD local pour le stockage des données de votre application, car il n’est pas persistant dans le Stockage des objets blobs Azure.

8. **Puis-je convertir mon compte de stockage Standard en compte Premium Storage ?**

	Non. Il n’est pas possible de convertir un compte de stockage Standard en compte Premium Storage, ou vice versa. Vous devez créer un compte de stockage du type souhaité, pusi copier des données vers le nouveau compte de stockage, le cas échéant.

9. **Comment convertir ma machine virtuelle de série D en machine virtuelle de série DS ?**

	Pour déplacer votre charge de travail d’une machine virtuelle de série D utilisant un compte de stockage Standard vers une machine virtuelle de série DS utilisant un compte de stockage Premium, consultez le guide de migration intitulé [Migration vers le Stockage Premium Azure](storage-migration-to-premium-storage.md).

## Étapes suivantes

Pour plus d’informations sur Azure Storage Premium, voir les articles suivants.

### Conception et implémentation avec Azure Storage Premium

- [Conception optimisée pour les performances avec Premium Storage](storage-premium-storage-performance.md)
- [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969)

### Instructions d’utilisation

- [Migration vers le stockage Premium Azure](storage-migration-to-premium-storage.md)

### Billets de blog

- [Mise à la disposition générale d’Azure Storage Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
- [Annonce de la série GS : ajout de prise en charge de Premium Storage pour les machines virtuelles les plus grandes dans le Cloud Public](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

[Image1]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!---HONumber=AcomDC_0921_2016-->