<properties
	pageTitle="Vue d’ensemble de Stretch Database | Microsoft Azure"
	description="Apprenez comment Stretch Database migre vos données brutes en toute sécurité et de manière transparente vers le cloud Microsoft Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# Présentation de Stretch Database

Stretch Database transfère vos données brutes en toute sécurité et de manière transparente vers le cloud Microsoft Azure.

Si vous souhaitez commencer à utiliser Stretch Database maintenant, consultez [Prise en main en exécutant l’Assistant Activer la base de données pour Stretch](sql-server-stretch-database-wizard.md).

## Quels sont les avantages de Database Stretch ?
Database Stretch offre les avantages suivants :

### Fournit une disponibilité rentable pour les données à froid
Étend dynamiquement les données transactionnelles à chaud et brutes de SQL Server vers Microsoft Azure avec la base de données SQL Server Stretch. Contrairement au stockage de données brutes par défaut, vos données sont toujours en ligne et disponibles pour interrogation. Vous pouvez fournir des délais de rétention de données plus longs sans entrer dans des banques contenant de grandes tables comme l’historique des commandes client. Tirez parti du faible coût du stockage Azure plutôt que de passer à la mise à l’échelle de stockage sur site. Le choix du niveau de tarification et la configuration des paramètres ont lieu dans le portail Azure et ce afin de garder le contrôle des prix. Augmentation ou réduction d’échelle, selon le cas. Pour plus d’informations, visitez la [page de tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Ne nécessite pas de modifications des requêtes ou des applications
Accédez en toute transparence aux données SQL Server, peu importe qu’elles soient locales ou étendues dans le cloud. Vous définissez la stratégie qui détermine l’endroit où les données sont stockées, et SQL Server gère le déplacement des données en arrière-plan. L’ensemble de la table est toujours en ligne et peut être interrogé. En outre, Stretch Database ne nécessite pas de modification des requêtes ou des applications existantes. L’emplacement des données est totalement transparent pour l’application.

### Rationalise la maintenance locale des données
Réduisez la maintenance et le stockage en local de vos données. Les sauvegardes de vos données locales sur site s’exécutent plus vite et se terminent au sein de la fenêtre de maintenance. Les sauvegardes de la partie cloud de vos données s’exécutent de façon automatique. Vos besoins en stockage local sont considérablement réduits. Le stockage Azure peut être 80 % moins coûteux que l’ajout de disques SSD en local.

### Assure la sécurité des données même lors de la migration
Profitez d’une totale tranquillité d’esprit pendant que vous étirez vos applications les plus importantes en toute sécurité vers le cloud. Le chiffrement intégral SQL Server permet de crypter les données en cours de transfert. La sécurité de niveau de ligne (RLS) et d’autres fonctionnalités de sécurité avancées SQL Server fonctionnent également avec Stretch Database pour protéger vos données.

## Que fait la Stretch Database ?
Une fois Stretch Database activée pour une instance SQL Server, une base de données et au moins une table, Stretch Database commence à migrer vos données brutes en mode silencieux vers Azure.

-   Si vous stockez des données brutes dans une table distincte, vous pouvez migrer la table entière.

-   Si votre table contient à la fois des données brutes et des données à chaud, vous pouvez spécifier une fonction de filtre pour sélectionner les lignes à transférer.

**Vous n’avez pas à modifier les requêtes et les applications client existantes.** Vous continuez d’avoir un accès transparent aux données locales et distantes, même pendant la migration des données. Il existe un peu de latence pour les requêtes à distance, mais vous êtes confronté à cette latence uniquement lorsque vous interrogez les données brutes.

**Stretch Database garantit qu’aucune donnée n’est perdue** en cas de défaillance pendant la migration. Elle est également dotée d’une logique de nouvelle tentative pour gérer les problèmes de connexion susceptibles de se produire pendant la migration. Une vue de gestion dynamique fournit l’état de la migration.

**Vous pouvez suspendre la migration des données** pour résoudre les problèmes sur le serveur local ou optimiser la bande passante réseau disponible.

![Vue d’ensemble de base de données Stretch][StretchOverviewImage1]

## Stretch Database est-il fait pour vous ?
Si vous êtes concerné par l’une des affirmations suivantes, Stretch Database peut répondre à vos exigences et résoudre vos problèmes.

|Si vous êtes un décideur|Si vous être administrateur de base de données|
|------------------------------|-------------------|
|Je dois conserver des données transactionnelles pendant un certain temps.|Je n’arrive plus à gérer la taille de mes tables.|
|Il arrive que je doive interroger les données brutes.|Mes utilisateurs disent qu’ils souhaitent accéder à mes données brutes, mais ils les utilisent rarement.|
|Je dispose d’applications, notamment d’applications anciennes que je ne souhaite pas mettre à jour.|Je dois continuer d’acheter et d’ajouter du stockage.|
|Je veux trouver un moyen de faire des économies sur le stockage.|Je ne peux pas enregistrer ou restaurer des tables aussi grandes au sein du SLA.|

## Quels types de bases de données ou de tables sont des candidats à Stretch Database ?
Stretch Database cible des bases de données transactionnelles contenant de grandes quantités de données brutes, généralement stockées dans quelques tables. Ces tables peuvent contenir plus d’un milliard de lignes.

Si vous utilisez la fonctionnalité de table temporelle de SQL Server 2016, utilisez Stretch Database pour migrer tout ou partie de la table d’historique associée à un stockage économique dans Azure. Pour plus d’informations, consultez [Gérer la rétention de données d’historique dans les tables temporelles de contrôle de version de système](https://msdn.microsoft.com/library/mt637341.aspx).

Utilisez Stretch Database Advisor, fonctionnalité de conseiller de mise à niveau de SQL Server 2016 pour identifier des bases de données et des tables pour Stretch Database. Pour plus d’informations, consultez [Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md). Pour en savoir plus sur d’éventuels problèmes de blocage, consultez [Limites de Stretch Database](sql-server-stretch-database-limitations.md).

## Version d’évaluation de Stretch Database
**Version d’évaluation de Stretch Database avec la base de données AdventureWorks.** Pour obtenir la base de données exemple AdventureWorks, téléchargez au moins le fichier de base de données et les exemples et les fichiers script [ici](https://www.microsoft.com/download/details.aspx?id=49502). Après avoir restauré la base de données exemple sur une instance de SQL Server 2016, décompressez le fichier d’exemple et ouvrez le fichier exemple Stretch DB à partir du dossier Stretch DB. Exécutez les scripts de ce fichier pour vérifier l’espace utilisé par vos données avant et après avoir activé Stretch Database, pour suivre la progression de la migration des données, et pour confirmer que vous pouvez continuer d’interroger les données existantes et insérer de nouvelles données pendant et après la migration des données.

## Étape suivante
**Identifiez les bases de données et les tables candidates pour Stretch Database.** Téléchargez le Conseiller de mise à niveau SQL Server 2016 et exécutez le Stretch Database Advisor pour identifier des bases de données et des tables candidates pour Stretch Database. Stretch Database Advisor identifie également les problèmes de blocage. Pour plus d’informations, consultez [Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!-----HONumber=AcomDC_0629_2016-->