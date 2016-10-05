<properties 
	pageTitle="Introduction au niveau Premium du Cache Redis Azure | Microsoft Azure" 
	description="Découvrez comment créer et gérer la persistance et le clustering Redis, ainsi que la prise de charge VNET pour vos instances de Cache Redis Azure de niveau Premium." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>

# Introduction au niveau Premium du Cache Redis Azure
le Cache Redis Azure est un cache distribué et géré qui vous permet de développer des applications hautement évolutives et réactives en fournissant un accès ultra-rapide aux données.

Le nouveau niveau Premium est un niveau professionnel qui inclut toutes les fonctionnalités de niveau Standard, auxquelles s’ajoutent entre-autre une amélioration des performances, un accroissement des charges de travail, une récupération d’urgence plus réactive, un service d’importation/exportation, et une sécurité renforcée. Poursuivez la lecture pour en savoir plus sur les fonctionnalités supplémentaires du niveau de cache Premium.

## Amélioration des performances par rapport au niveau Standard ou De base
**Amélioration des performances par rapport au niveau Standard ou De base.** Les caches au niveau Premium sont déployés sur du matériel qui dispose de processeurs plus rapides et offrent de meilleures performances par rapport au niveau De base ou Standard. Les caches de niveau Premium ont un débit supérieur et des latences moindres.

**Pour un cache de même dimension, le débit du niveau Premium est plus élevé que celui du niveau Standard.** Par exemple, le débit d’un cache de 53 Go P4 (Premium) est de 250 000 demandes par seconde par rapport à 150 000 demandes par seconde pour le débit C6 (Standard).

Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, voir le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## Persistance des données Redis
Le niveau Premium vous permet de conserver les données du cache dans un compte de stockage Azure. Dans un cache De base/Standard, toutes les données sont stockées uniquement dans la mémoire. En cas de problème lié à l’infrastructure sous-jacente, il existe un risque de perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données. Le Cache Redis Azure propose des options RDB et AOF (à venir) de [persistance Redis](http://redis.io/topics/persistence).

Pour obtenir des instructions sur la configuration de la persistance, consultez [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md).

##Cluster Redis
Si vous souhaitez créer des caches de plus de 53 Go ou partager des données parmi plusieurs nœuds Redis, vous pouvez utiliser le clustering Redis disponible au niveau Premium. Chaque nœud se compose d’une paire de caches principal/réplica géré par Azure pour la haute disponibilité.

**Le clustering Redis permet un débit et une capacité d'évolutivité maximum.** Le débit augmente de façon linéaire à mesure que vous augmentez le nombre de partitions (nœuds) dans le cluster. Par exemple, si vous créez un cluster P4 de 10 partitions, le débit disponible est alors de 250 000 * 10 = 2,5 millions de demandes par seconde. Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, consultez le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

Pour commencer votre clustering, consultez [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).

##Amélioration de la sécurité et de l’isolation

Les caches créés avec les niveau De base ou Standard sont accessibles sur Internet. L’accès au cache est limité en fonction de la clé d’accès. Avec le niveau Premium, vous pouvez vous assurer que seuls les clients d’un réseau spécifié peuvent accéder au cache. Vous pouvez déployer le Cache Redis dans un [réseau virtuel Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Vous pouvez utiliser toutes les fonctionnalités de VNet, comme les sous-réseaux, les stratégies de contrôle d’accès et d’autres fonctionnalités pour améliorer la restriction d’accès à Redis.

Pour plus d’informations, consultez [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

## Importation/Exportation

L’Importation/Exportation est une opération de gestion de données de Cache Redis Azure qui vous permet d’importer ou d’exporter des données depuis ou vers le Cache Redis Azure, en important ou en exportant un instantané de base de données du Cache Redis (RDB) depuis un cache premium vers un objet blob de pages dans un compte Azure Storage. Cela vous permet de migrer entre différentes instances de Cache Redis Azure ou de remplir le cache de données avant utilisation.

L’importation peut servir à récupérer les fichiers RDB compatibles Redis depuis n’importe quel serveur Redis en cours d’exécution sur n’importe quel environnement ou cloud, y compris si Redis est exécuté sur Linux, Windows ou n’importe quel fournisseur de cloud tel qu’Amazon Web Services. Importer des données est un moyen simple de créer un cache pré-rempli de données. Pendant le processus d’importation, le Cache Azure Redis charge les fichiers RDB d’Azure Storage dans la mémoire, puis insère les clés dans le cache.

L’exportation vous permet d’exporter les données stockées dans le Cache Azure Redis vers un ou plusieurs fichier(s) RDB compatible(s). Vous pouvez utiliser cette fonctionnalité pour déplacer des données d’une instance de Cache Redis Azure à une autre, ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur la machine virtuelle qui héberge l’instance de serveur de Cache Redis Azure, puis téléchargé vers le compte de stockage désigné. Lorsque l’opération d’exportation se termine avec un état de réussite ou d’échec, le fichier temporaire est supprimé.

Pour plus d’informations, consultez [How to import data into and export data from Azure Redis Cache](cache-how-to-import-export-data.md) (Comment importer et exporter des données vers ou depuis le Cache Redis Azure).

## Reboot

Le niveau premium vous permet de redémarrer un ou plusieurs nœuds de votre cache à la demande. Cela vous permet de tester la résilience de votre application en cas d’échec. Vous pouvez redémarrer les nœuds suivants.

-	Nœud master de votre cache
-	Nœud subordonné de votre cache
-	Nœuds master et subordonné de votre cache
-	Lorsque vous utilisez un cache premium avec clustering, vous pouvez redémarrer le nœud master, le nœud subordonné ou les deux pour les différentes partitions dans le cache

Pour plus d’informations, consultez [Redémarrage](cache-administration.md#reboot) et le [Forum aux questions sur le redémarrage](cache-administration.md#reboot-faq).

## Planification de mises à jour

Ces mises à jour planifiées vous permettent de désigner une fenêtre de maintenance pour votre cache. Lorsque la fenêtre de maintenance est spécifiée, toute mise à jour du serveur Redis est effectuée pendant cet intervalle. Pour désigner une fenêtre de maintenance, sélectionnez les jours choisis et spécifiez l’heure de début de la fenêtre de maintenance pour chaque jour. Notez que l’heure de la maintenance est au format UTC.

Pour plus d’informations, consultez [Planification de mises à jour](cache-administration.md#schedule-updates) et le [Forum aux questions de la planification des mises à jour](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Seules les mises à jour du serveur Redis sont exécutées au cours de la fenêtre de maintenance planifiée. La fenêtre de maintenance ne s’applique pas aux mises à jour d’Azure ou du système d’exploitation de la machine virtuelle.

## Mise à l’échelle au niveau premium

Pour configurer le niveau premium, il suffit de choisir l’un des niveaux premium dans le panneau **Changer le niveau tarifaire**. Vous pouvez également mettre votre cache à l’échelle au niveau premium à l’aide de PowerShell et de l’interface de ligne de commande. Pour obtenir des instructions détaillées, consultez [Mise à l’échelle du cache Redis Azure ](cache-how-to-scale.md) et [Automatisation d’une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## Étapes suivantes

Créez un cache et explorez les nouvelles fonctionnalités de niveau Premium.

-	[Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md)
-	[Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md)
-	[Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md)
-	[How to import data into and export data from Azure Redis Cache (Comment importer et exporter des données vers ou depuis le Cache Redis Azure)](cache-how-to-import-export-data.md)
-	[Comment administrer le Cache Redis Azure](cache-administration.md)
  

<!---HONumber=AcomDC_0921_2016-->