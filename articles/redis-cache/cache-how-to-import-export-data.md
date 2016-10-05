<properties 
	pageTitle="Importer et exporter des données dans le Cache Redis Azure | Microsoft Azure" 
	description="Découvrez comment importer et exporter des données vers et depuis un stockage d’objets blob à l’aide de vos instances Premium de Cache Redis Azure" 
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

# Importer et exporter des données dans le Cache Redis Azure

L’Importation/Exportation est une opération de gestion de données de Cache Redis Azure qui vous permet d’importer ou d’exporter des données depuis ou vers le Cache Redis Azure, en important ou en exportant un instantané de base de données du Cache Redis (RDB) depuis un cache premium vers un objet blob de pages dans un compte Azure Storage. L’Importation/Exportation vous permet de migrer entre différentes instances de Cache Redis Azure ou de remplir le cache de données avant utilisation.

Cet article fournit un guide pour l’importation et l’exportation de données avec le Cache Redis Azure et répond aux questions fréquemment posées.

>[AZURE.IMPORTANT] Cette fonctionnalité est en version préliminaire et uniquement disponible pour les caches de [niveau Premium](cache-premium-tier-intro.md).

## Importation

L’importation peut servir à récupérer les fichiers RDB compatibles Redis depuis n’importe quel serveur Redis en cours d’exécution sur n’importe quel environnement ou cloud, y compris si Redis est exécuté sur Linux, Windows ou n’importe quel fournisseur de cloud tel qu’Amazon Web Services. Importer des données est un moyen simple de créer un cache pré-rempli de données. Pendant le processus d’importation, le Cache Azure Redis charge les fichiers RDB d’Azure Storage dans la mémoire, puis insère les clés dans le cache.

>[AZURE.NOTE] Avant de commencer l’opération d’importation, vérifiez que le ou les fichier(s) de votre base de données Redis (RDB) sont téléchargés vers des objets blob de pages dans Azure Storage, dans les mêmes région et abonnement que votre instance de Cache Redis Azure. Pour plus d’informations, consultez l’article [Prise en main du stockage d’objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md). Si vous avez exporté votre fichier RDB à l’aide de la fonctionnalité [Azure Redis Cache Export](#export) (Exportation Azure Redis Cache), votre fichier RDB est déjà stocké dans un objet blob de pages et est prêt pour l’importation.

1. Pour importer un ou plusieurs objet(s) blob de cache exporté(s), [accédez à votre cache](cache-configure.md#configure-redis-cache-settings) dans le portail Azure et cliquez sur **Importer des données** à partir du panneau **Paramètres** de votre instance de cache.

    ![Importer des données][cache-import-data]

2. Cliquez sur **Choisir un ou plusieurs objets blob** et sélectionnez le compte de stockage contenant les données à importer.

    ![Sélection d'un compte de stockage][cache-import-choose-storage-account]

3. Cliquez sur le conteneur des données à importer.

    ![Sélection d’un conteneur][cache-import-choose-container]

4. Sélectionnez un ou plusieurs objets blob à importer en cliquant sur la zone située à gauche de leurs noms, puis cliquez sur **Sélectionnez**.

    ![Sélection d’objets blob][cache-import-choose-blobs]

5. Cliquez sur **Importer** pour commencer le processus d’importation.

    >[AZURE.IMPORTANT] Pendant le processus d’importation, le cache n’est pas accessible par les clients de cache, et toutes les données existantes dans le cache sont supprimées.

    ![Importation][cache-import-blobs]

    Vous pouvez surveiller la progression de l’importation grâce aux notifications du portail Azure ou aux événements dans le [journal d’audit](cache-configure.md#support-amp-troubleshooting-settings).

    ![Progression de l’importation][cache-import-data-import-complete]


## Exportation

L’exportation vous permet d’exporter les données stockées dans le Cache Azure Redis vers un ou plusieurs fichiers RDB compatibles. Vous pouvez utiliser cette fonctionnalité pour déplacer des données d’une instance de Cache Redis Azure à une autre, ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur la machine virtuelle qui héberge l’instance de serveur de Cache Redis Azure, puis téléchargé vers le compte de stockage désigné. Lorsque l’opération d’exportation se termine avec un état de réussite ou d’échec, le fichier temporaire est supprimé.

1. Pour exporter le contenu actuel du cache vers un compte de stockage, [accédez à votre cache](cache-configure.md#configure-redis-cache-settings) dans le portail Azure et cliquez sur **Exporter des données** à partir du panneau **Paramètres** de votre instance de cache.

    ![Sélection d’un conteneur de stockage][cache-export-data-choose-storage-container]

2. Cliquez sur **Choisir un conteneur de stockage** et sélectionnez le compte de stockage souhaité. Le compte de stockage doit être situé dans les mêmes région et abonnement que votre cache.

    >[AZURE.IMPORTANT] La fonctionnalité Importation/Exportation fonctionne avec les objets blob de pages, qui sont pris en charge par les comptes de stockage ARM et classiques, mais pas par les [comptes de stockage d’objets Blob](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) pour l’instant.

    ![Compte de stockage][cache-export-data-choose-account]

3. Choisissez le conteneur d’objets blob souhaité et cliquez sur **Sélectionnez**. Pour utiliser un nouveau conteneur, cliquez sur **Ajouter un conteneur**, puis sélectionnez-le dans la liste.

    ![Sélection d’un conteneur de stockage][cache-export-data-container]

4. Tapez un **préfixe de nom d’objet blob** et cliquez sur **Exporter** pour lancer le processus d’exportation. Le préfixe de nom d’objet blob est utilisé pour préfixer les noms des fichiers générés par cette opération d’exportation.

    ![Exportation][cache-export-data]

    Vous pouvez surveiller la progression de l’exportation grâce aux notifications du portail Azure ou aux événements dans le [journal d’audit](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Les caches restent disponibles pendant le processus d’exportation.


## FAQ sur l’Importation/Exportation

Cette section contient les questions fréquemment posées sur la fonctionnalité d’Importation/Exportation.

-	[Quels niveaux de tarification permettent d’utiliser l’Importation/Exportation ?](#what-pricing-tiers-can-use-importexport)
-	[Puis-je importer des données à partir de n’importe quel serveur Redis ?](#can-i-import-data-from-any-redis-server)
-	[Mon cache est-il disponible pendant une opération d’Importation/Exportation ?](#will-my-cache-be-available-during-an-importexport-operation)
-	[Puis-je utiliser l’Importation/Exportation avec le cluster Redis ?](#can-i-use-importexport-with-redis-cluster)
-	[Comment l’importation/exportation fonctionne avec un paramètre de base de données personnalisé ?](#how-does-importexport-work-with-a-custom-databases-setting)
-	[En quoi l’Importation/Exportation est-elle différente de la persistance Redis ?](#how-is-importexport-different-from-redis-persistence)
-	[Puis-je automatiser l’Importation/Exportation à l’aide de PowerShell, de CLI ou d’autres clients de gestion ?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-	[J’ai reçu une erreur de délai d’attente pendant mon opération d’importation/exportation. Qu’est-ce que cela signifie ?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-	[J’ai obtenu une erreur en exportant mes données vers un stockage d’objets blob Azure. Que s’est-il passé ?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### Quels niveaux de tarification permettent d’utiliser l’Importation/Exportation ?

L’Importation/Exportation n’est disponible que dans le niveau de tarification Premium.

### Puis-je importer des données à partir de n’importe quel serveur Redis ?

Oui, en plus des données exportées à partir d’instances de Cache Redis Azure, vous pouvez importer des fichiers RDB à partir de n’importe quel serveur Redis en cours d’exécution sur n’importe quel environnement ou cloud, tels que Linux, Windows ou tout autre fournisseur de cloud tel qu’Amazon Web Services. Pour ce faire, téléchargez le fichier RDB à partir du serveur Redis souhaité dans un objet blob de pages d’un compte Azure Storage, puis importez-le dans votre instance Premium de Cache Redis. Vous pouvez par exemple exporter les données de votre cache de production et les importer vers un cache faisant partie d’un environnement intermédiaire, à des fins de test ou de migration.

### Mon cache est-il disponible pendant une opération d’Importation/Exportation ?

-	**Exporter** - Les caches restent disponibles et vous pouvez continuer à utiliser votre cache pendant une opération d’exportation.
-	**Importer** - Les caches deviennent indisponibles au démarrage d’une opération d’importation, puis redeviennent disponibles lorsqu’elle est terminée.


### Puis-je utiliser l’Importation/Exportation avec le cluster Redis ?

Oui, et vous pouvez importer/exporter des données entre un cache en cluster et un cache hors cluster. Depuis que le cluster Redis [ne prend en charge que la base de données 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), toutes les données de bases de données différentes de 0 ne sont pas importées. Lorsque vous importez des données d’un cache en cluster, les clés sont redistribuées entre les partitions du cluster.

### Comment l’importation/exportation fonctionne avec un paramètre de base de données personnalisé ?

Certains niveaux tarifaires sont associés à des [limites de bases de données](cache-configure.md#databases) différentes. Certains aspects doivent donc être pris en compte lors d’une importation si vous avez configuré une valeur personnalisée pour le paramètre `databases` lors de la création du cache.

-	Lorsque vous procédez à une importation vers un niveau tarifaire avec une priorité inférieure à `databases` par rapport au niveau à partir duquel vous effectuez l’exportation :
	-	Si vous utilisez le nombre par défaut de `databases`, c’est-à-dire 16 pour tous les niveaux tarifaires, vous ne perdrez aucune donnée.
	-	Si vous utilisez un nombre de `databases` personnalisé qui se situe dans les limites du niveau tarifaire vers lequel vous effectuez l’importation, vous ne perdrez aucune donnée.
	-	Si vos données exportées contenaient des données dans une base de données dépassant les limites du nouveau niveau, les données de ces bases de données plus élevées ne sont pas importées.

### En quoi l’Importation/Exportation est-elle différente de la persistance Redis ?

La persistance du Cache Redis Azure vous permet de conserver dans Azure Storage les données stockées dans Redis. Quand la persistance est configurée, le Cache Redis Azure conserve un instantané du cache Redis dans un format binaire Redis sur le disque selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, les données du cache sont automatiquement rétablies à l’aide de l’instantané le plus récent. Pour plus d’informations, voir [Comment configurer la persistance des données pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md).

L’Importation/Exportation vous permet d’importer ou d’exporter des données vers ou depuis le Cache Redis Azure. Elle ne configure pas la sauvegarde et restauration à l’aide de la persistance Redis.


### Puis-je automatiser l’Importation/Exportation à l’aide de PowerShell, de CLI ou d’autres clients de gestion ?

Oui, pour les instructions PowerShell, voir [Comment importer un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) et [Comment exporter un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### J’ai reçu une erreur de délai d’attente pendant mon opération d’Importation/Exportation. Qu’est-ce que cela signifie ?

Si vous restez sur le panneau **Importer des données** ou **Exporter des données** pendant plus de 15 minutes avant de lancer l’opération, vous recevrez une erreur similaire à ce qui suit.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Pour résoudre ce problème, démarrez l’importation ou l’exportation avant que les 15 minutes ne se soient écoulées.

### J’ai obtenu une erreur en exportant mes données vers un stockage d’objets blob Azure. Que s’est-il passé ?

L’Importation/Exportation ne fonctionne qu’avec les fichiers RDB stockés sous la forme d’objets blob de pages. Les autres types d’objets blob ne sont pas pris en charge pour l’instant, y compris les comptes de stockage d’objets blob de niveaux chaud et froid.


## Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

-	[Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md)

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png

<!---HONumber=AcomDC_0921_2016-->