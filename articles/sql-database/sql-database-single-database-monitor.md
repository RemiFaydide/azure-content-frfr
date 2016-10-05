<properties
	pageTitle="Analyse des performances d’une base de données dans une base de données SQL Azure | Microsoft Azure"
	description="Découvrez les options d’analyse de votre base de données à l’aide des outils Azure et des vues de gestion dynamique."
	keywords="analyse de base de données, performances des bases de données du cloud"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="09/27/2016"
	ms.author="carlrab"/>

# Analyse des performances d’une base de données dans une base de données SQL Azure
L’analyse des performances d’une base de données SQL dans Azure démarre par l’analyse de l’utilisation des ressources par rapport au niveau de performances que vous avez choisi pour votre base de données. L’analyse permet de déterminer si votre base de données a une capacité excédentaire ou rencontre des problèmes, car les ressources ont atteint leur maximum et ensuite de décider s’il est temps d’ajuster le niveau de performances et le [niveau de service](sql-database-service-tiers.md) de votre base de données. Vous pouvez analyser votre base de données à l’aide d’outils graphiques dans le [portail Azure](https://portal.azure.com) ou à l’aide de [vues de gestion dynamique](https://msdn.microsoft.com/library/ms188754.aspx) SQL.

## Analyser des bases de données au moyen du portail Azure

Dans le [portail Azure](https://portal.azure.com/), vous pouvez surveiller l’utilisation d’une base de données unique en sélectionnant votre base de données et en cliquant sur le graphique **Analyse**. Une fenêtre **Mesure** apparaît. Vous pouvez la modifier en cliquant sur le bouton **Modifier le graphique**. Ajoutez les mesures suivantes :

- Pourcentage UC
- Pourcentage DTU
- Pourcentage E/S des données
- Pourcentage de la taille de la base de données

Une fois que vous avez ajouté ces mesures, vous pouvez continuer à les afficher dans le graphique **Analyse** avec plus de détails dans la fenêtre **Mesure**. Les quatre mesures montrent le pourcentage d’utilisation moyen correspondant aux **DTU** de votre base de données. Consultez l’article concernant les [niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur les DTU.

![Surveillance des niveaux de service des performances de la base de données.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Vous pouvez également configurer des alertes sur les mesures de performances. Cliquez sur le bouton **Ajouter une alerte** situé dans la fenêtre **Mesure**. Suivez l'assistant pour configurer votre alerte. Vous avez la possibilité de configurer une alerte si les mesures dépassent un certain seuil ou si la mesure tombe en dessous d’un certain seuil.

Par exemple, si vous pensez que la charge de travail dans votre base de données va augmenter, vous pouvez choisir de configurer une alerte par courrier électronique chaque fois que votre base de données atteint 80 % de n'importe quelle mesure de performances. Vous pouvez utiliser cette fonction comme un avertissement pour déterminer le moment auquel il se peut que vous deviez basculer vers le niveau de performance supérieur.

Les mesures de performance peuvent également vous aider à déterminer si vous pouvez passer à un niveau inférieur. Supposons que vous utilisez une base de données standard S2 et que toutes les mesures de performance indiquent que la base de données n'utilise pas plus de 10 % des performances en moyenne. Cette base de données fonctionnerait très bien en version S1 standard. Toutefois, prenez en considération les éventuels pics ou baisses de charges de travail avant de décider de passer à un niveau de performances inférieur.

## Analyser des bases de données à l’aide des vues de gestion dynamique

Les mêmes mesures exposées dans le portail sont également disponibles via des vues système : [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) dans la base de données **master** logique de votre serveur, et [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans la base de données utilisateur. Utilisez **sys.resource\_stats** si vous devez analyser des données moins granulaires sur une longue période de temps. Utilisez **sys.dm\_db\_resource\_stats** si vous devez analyser des données plus granulaires dans un laps de temps plus court. Pour en savoir plus, consultez [Guide des performances de base de données SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **sys.dm\_db\_resource\_stats** renvoie un résultat vide lorsqu’elle est utilisée dans les bases de données Web et Business, qui sont supprimées.

Pour les pools de base de données élastiques, vous pouvez surveiller des bases de données dans le pool avec les techniques décrites dans cette section. Mais vous pouvez également surveiller le pool dans son ensemble. Pour en savoir plus, consultez [Surveiller et gérer un pool élastique de bases de données](sql-database-elastic-pool-manage-portal.md).

<!---HONumber=AcomDC_0928_2016-->