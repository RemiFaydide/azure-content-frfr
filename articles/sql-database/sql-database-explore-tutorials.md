<properties
   pageTitle="Exploration des didacticiels relatifs à la base de données SQL Azure | Microsoft Azure"
   description="En savoir plus sur les fonctions et capacités de la base de données SQL"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# Exploration des didacticiels relatifs à la base de données SQL Azure

Utilisez les liens ci-dessous pour obtenir une vue d’ensemble de chaque fonctionnalité répertoriée et accéder à un didacticiel de démarrage simple pas à pas pour chaque domaine abordé. Pour consulter des démarrages rapides de solutions qui illustrent l’utilisation de la base de données SQL dans une solution complète à partir de scénarios réels, consultez [Démarrages rapides de la solution de base de données SQL Azure](sql-database-solution-quick-starts.md).

## Avec SQL Server Management Studio

Dans les didacticiels suivants, vous allez apprendre à utiliser SQL Server Management Studio pour administrer et interroger la base de données SQL Azure.


> [AZURE.IMPORTANT] Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Didacticiel | Description |
|---|---|---|
| [Connexion à une base de données SQL Azure à l'aide d'une connexion du principal au niveau du serveur](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Ce didacticiel vous apprend à vous connecter à une base de données SQL Azure à l’aide des identifiants de connexion d’un principal au niveau du serveur.|
| [Connexion à une base de données SQL Azure en tant qu’utilisateur](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Ce didacticiel vous apprend à vous connecter à une base de données SQL Azure à l’aide d’un compte utilisateur au niveau de la base de données.|
||||

## Pools élastiques

Dans les didacticiels suivants, vous apprendrez à utiliser les [pools élastiques](sql-database-elastic-pool.md) pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables et imprévisibles.

| Didacticiel | Description |
|---|---|---|
| [Créer un pool élastique](sql-database-elastic-pool-create-portal.md) | Ce didacticiel vous apprend à créer un pool évolutif de bases de données SQL Azure. |
| [Surveiller une base de données élastique](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | Ce didacticiel vous apprend à surveiller une base de données élastique individuel afin d’identifier d’éventuels problèmes. |
| [Ajouter une alerte à une ressource de pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | Ce didacticiel vous apprend à ajouter des règles à des ressources qui envoient des messages électroniques à des personnes ou des chaînes d’alertes à des points de terminaison d’URL quand la ressource atteint un seuil d’utilisation que vous configurez. |
| [Déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | Ce didacticiel vous apprend à déplacer une base de données vers un pool élastique. |
| [Déplacer une base de données hors d’un pool élastique](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | Ce didacticiel vous apprend à extraire une base de données d’un pool élastique. |
| [Modifier les paramètres de performances d’un pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | Ce didacticiel vous apprend à ajuster les limites de stockage et de performances d’un pool. |
||||

## Tâches de base de données élastiques

Dans les didacticiels suivants, vous apprendrez à utiliser les [tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

| Didacticiel | Description |
|---|---|---|
| [Prise en main des outils de base de données élastiques](sql-database-elastic-scale-get-started.md) | Ce didacticiel vous apprend à utiliser les fonctionnalités des outils de bases de données élastiques à l’aide d’une simple application partitionnée. |
| [Prise en main de tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md) | Ce didacticiel vous apprend à créer et gérer des tâches permettant de gérer un groupe de bases de données associées. | 
||||

## Requêtes élastiques

Dans les didacticiels suivants, vous apprendrez à exécuter des [requêtes élastiques](sql-database-elastic-query-overview.md).

| Didacticiel | Description |
|---|---|---|
| [Prise en main des requêtes de base de données élastiques pour le partitionnement (partitionnement horizontal)](sql-database-elastic-query-getting-started.md) | Ce didacticiel vous apprend à créer des rapports à partir de toutes les bases de données contenues dans une base de données partitionnée horizontalement à l’aide de [requêtes élastiques](sql-database-elastic-query-overview.md) |
| [Prise en main des requêtes de bases de données croisées (partitionnement vertical)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | Ce didacticiel vous apprend à créer des rapports à partir de toutes les bases de données contenues dans une base de données partitionnée verticalement à l’aide de [requêtes élastiques](sql-database-elastic-query-overview.md) |
| [Conversion de bases de données existantes pour utiliser les outils de base de données élastique](sql-database-elastic-convert-to-use-elastic-tools.md)| Ce didacticiel vous apprend à mettre à l’échelle une base de données SQL Azure dans le cadre d’un partitionnement horizontal. |
||||

## Optimisation des performances

Dans les didacticiels suivants, vous apprendrez à optimiser les [performances de bases de données uniques](sql-database-performance-guidance.md). Pour optimiser les performances de plusieurs bases de données, consultez [Pools élastiques](#elastic-pools).

| Didacticiel | Description |
|---|---|---|
| [Modifier les niveaux de service et de performances de votre base de données](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | Ce didacticiel vous apprend à augmenter ou diminuer les performances d’une base de données SQL Azure à l’aide de niveaux de service. |
| [Informations sur les performances des bases de données SQL](sql-database-performance.md#performance-overview) | Ce didacticiel explique comment ouvrir et utiliser le composant SQL Database Advisor Query Performance Insight.|
| [SQL Database Advisor](sql-database-advisor.md#viewing-recommendations) | Ce didacticiel vous montre comment afficher et appliquer les recommandations de performances de SQL Database Advisor. |
| [Consultez les requêtes principales consommatrices d’UC](sql-database-query-performance.md#review-top-cpu-consuming-queries)| Ce didacticiel explique comment utiliser le composant SQL Database Advisor Query Performance Insight pour passer en revue les requêtes qui consomment le plus d’UC.|
| [Affichage des détails d’une requête individuelle](sql-database-query-performance.md#viewing-individual-query-details)| Ce didacticiel explique comment utiliser le composant SQL Database Advisor Query Performance Insight pour afficher les détails de performances de chaque requête.|
||||

## Migration et archivage de la base de données SQL 

Dans les didacticiels suivants, vous allez apprendre à [migrer une base de données SQL Server existante vers une base de données SQL Azure](sql-database-cloud-migrate.md).

| Didacticiel | Description |
|---|---|---|
| [Détection des problèmes de compatibilité à l’aide de SQL Server Data Tools pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Ce didacticiel vous apprend à utiliser SQL Server Data Tools pour Visual Studio afin de déterminer la compatibilité de la base de données SQL Azure. |
| [Résolution des problèmes de compatibilité à l’aide de SQL Server Data Tools pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Ce didacticiel vous apprend à utiliser SQL Server Data Tools pour Visual Studio afin de corriger les problèmes de compatibilité de la base de données SQL Azure. |
| [Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | Ce didacticiel vous apprend à utiliser l’utilitaire de ligne de commande SQLPackage.exe afin de déterminer la compatibilité de la base de données SQL Azure.|
| [Déterminer la compatibilité de Base de données SQL à l’aide de SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Ce didacticiel vous apprend à utiliser SQL Server Management Studio afin de déterminer la compatibilité de la base de données SQL Azure.|
| [Migration d’une base de données SQL Server vers Base de données SQL à l’aide de l’assistant de déploiement de base de données vers Microsoft Azure Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | Ce didacticiel vous apprend à migrer une base de données SQL Server compatible dans votre base de données SQL Azure à l’aide de l’Assistant de déploiement de base de données vers Microsoft Azure Database dans SQL Server Management Studio.
| [Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Ce didacticiel vous apprend à exporter une base de données SQL Server compatible dans un fichier BACPAC à l’aide de l’Assistant Exportation d’une application de couche données dans SQL Server Management Studio.|
| [Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Ce didacticiel vous apprend à exporter une base de données SQL Server compatible vers un fichier BACPAC à l’aide de l’utilitaire de ligne de commande SQLPackage.exe.|
| [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Ce didacticiel vous apprend à importer une base de données SQL Azure à partir d’un fichier BACPAC à l’aide de l’Assistant Exportation d’une application de couche données dans SQL Server Management Studio. |
| [Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | Ce didacticiel vous apprend à importer une base de données dans une base de données SQL Azure à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande SQLPackage. |
| [Importer un fichier BACPAC pour créer une nouvelle base de données SQL Azure](sql-database-import.md) | Ce didacticiel vous apprend à importer une base de données dans une base de données SQL Azure à partir d’un fichier BACPAC stocké dans un objet bloc Azure à l’aide du portail Azure.|
| [Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md) | Ce didacticiel vous apprend à importer une base de données dans une base de données SQL Azure à partir d’un fichier BACPAC à l’aide de PowerShell.|
| [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure](sql-database-export.md#export-your-database) | Ce didacticiel vous apprend à archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure. |
| [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell](sql-database-export-powershell.md) | Ce didacticiel vous apprend à archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell. |
| [Copie d’une base de données SQL Azure](sql-database-copy.md#copy-your-sql-database) | Ce didacticiel vous apprend à copier une base de données SQL Azure à l’aide du portail Azure. |
| [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell#copy-your-sql-database) | Ce didacticiel vous apprend à copier une base de données SQL Azure à l’aide de PowerShell. |
| [Copier une base de données SQL Azure à l’aide de Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | Ce didacticiel vous apprend à copier une base de données SQL Azure à l’aide de Transact-SQL. |
||||

##Développement

Les didacticiels suivants fournissent des informations sur le [Développement de bases de données SQL](sql-database-develop-overview.md) ainsi que sur l’utilisation des [bibliothèques de connectivité](sql-database-libraries.md).

| Didacticiel | Description |
|---|---|---|
| [Connexion à SQL Database à l’aide de .NET (C#)](sql-database-develop-dotnet-simple.md) | Ce didacticiel vous explique comment vous connecter à une base de données SQL Azure à l’aide de C#. |
| [Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows](sql-database-develop-java-simple.md) | Ce didacticiel vous explique comment vous connecter à une base de données SQL Azure à l’aide de Java. |
| [Connexion à SQL Database à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Ce didacticiel vous explique comment vous connecter à une base de données SQL Azure à l’aide de Node.js. |
| [Connexion à SQL Database à l’aide de PHP sur Windows](sql-database-develop-php-simple.md) | Ce didacticiel vous explique comment vous connecter à une base de données SQL Azure à l’aide de PHP. |
| [Connexion à SQL Database à l’aide de Python](sql-database-develop-python-simple.md) | Ce didacticiel vous explique comment vous connecter à une base de données SQL Azure à l’aide de Python. |
| [Connexion à SQL Database à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Ce didacticiel vous explique comment vous connecter à une base de données SQL Azure à l’aide de Ruby. |
||||
 
## Accès à la base de données

Les didacticiels suivants portent sur [la création et la gestion des connexions et des utilisateurs](sql-database-manage-logins.md).

| Didacticiel | Description |
|---|---|---|
| [Configurer un pare-feu sur une base de données Azure SQL à l’aide du portail Azure](sql-database-configure-firewall-settings.md) | Ce didacticiel vous apprend à configurer un pare-feu au niveau du serveur de base de données SQL à l’aide du portail Azure. |
| [Configurer un pare-feu sur une base de données Azure SQL à l’aide de TSQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | Ce didacticiel vous apprend à créer une règle de pare-feu au niveau de la base de données à l’aide de Transact-SQL.|
| [Gérer les règles de pare-feu au niveau du serveur à l’aide de Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | Ce didacticiel vous apprend à gérer un pare-feu au niveau du serveur de base de données SQL Azure à l’aide de Transact-SQL.|
| [Gérer les règles de pare-feu au niveau du serveur à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | Ce didacticiel vous apprend à gérer un pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell.|
| [Gérer les règles de pare-feu au niveau du serveur à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | Ce didacticiel vous apprend à gérer un pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST.|
| [Connexion à une base de données SQL Azure à l'aide d'une connexion du principal au niveau du serveur](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Ce didacticiel vous apprend à vous connecter à une base de données SQL Azure à l’aide des identifiants de connexion d’un principal au niveau du serveur.|
| [Accorder l’accès à une connexion de base de données](sql-database-manage-logins.md#granting-database-access-to-a-login) | Ce didacticiel vous montre comment accorder à une connexion de niveau serveur l’accès à une base de données.|
| [Création d'un utilisateur de base de données à l'aide de SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | Ce didacticiel vous montre comment créer un nouvel utilisateur de base de données dans une base de données existante à l’aide de SSMS.|
| [Accorder des autorisations db\_owner à un nouvel utilisateur de bases de données](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | Ce didacticiel vous montre comment accorder des autorisations db\_owner à un utilisateur de base de données existant.|
| [Connexion à une base de données SQL Azure en tant qu’utilisateur](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Ce didacticiel vous apprend à vous connecter à une base de données SQL Azure à l’aide d’un compte utilisateur au niveau de la base de données.|
||||


## Sécurité des données

Les didacticiels suivants portent sur la [sécurité des données d’une base de données SQL Azure](sql-database-security.md).

| Didacticiel | Description |
|---|---|---|
| [Prise en main de Threat Detection pour la base de données SQL](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | Ce didacticiel vous apprend à configurer la détection de menaces dans le portail Azure pour votre base de données.|
| [Chiffrement intégral - Protéger les données sensibles de la base de données SQL à l’aide du chiffrement de base de données et stocker vos clés de chiffrement dans Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) | Ce didacticiel vous explique comment utiliser l’Assistant Chiffrement intégral pour sécuriser les données sensibles dans une base de données SQL Azure.|
| [Chiffrement transparent des données avec Azure SQL Database](https://msdn.microsoft.com/library/dn948096.aspx)| Ce didacticiel vous apprend à utiliser le chiffrement transparent des données pour sécuriser les données sensibles.|
| [Chiffrer une colonne de données](https://msdn.microsoft.com/library/ms179331.aspx)| Ce didacticiel vous apprend à chiffrer une colonne de données à l’aide de Transact-SQL.|
| [Prise en main du masquage de données dynamiques de base de données SQL (portail Azure)](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) | Ce didacticiel vous explique comment configurer le masquage des données dynamiques pour votre base de données SQL Azure. |
||||

## Continuité d’activité et montée en puissance parallèle des requêtes

Dans les didacticiels suivants, vous apprendrez à utiliser la [géorestauration et la géoréplication active](sql-database-business-continuity.md) pour la récupération d’erreurs, la continuité d’activité et la montée en puissance parallèle des requêtes.

| Didacticiel | Description |
|---|---|---|
| [Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md)| Ce didacticiel explique comment restaurer votre base de données à un point antérieur dans le temps à l’aide du portail Azure.|
| [Restaurer une base de données SQL Azure à un point dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Ce didacticiel explique comment restaurer votre base de données à un point antérieur dans le temps à l’aide de PowerShell.|
| [Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md) | Ce didacticiel vous apprend à restaurer une base de données supprimée à l’aide du portail Azure.|
| [Restaurer une base de données SQL Azure supprimée avec PowerShell](sql-database-restore-deleted-database-powershell.md) | Ce didacticiel vous apprend à restaurer une base de données supprimée à l’aide de PowerShell.|
| [Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md)| Ce didacticiel vous apprend à configurer la géoréplication active à l’aide du portail Azure.|
| [Configurer la géoréplication pour Base de données SQL Azure avec PowerShell](sql-database-geo-replication-powershell.md)| Ce didacticiel vous apprend à configurer la géoréplication active à l’aide de PowerShell.|
| [Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-transact-sql.md)| Ce didacticiel vous apprend à configurer la géoréplication active à l’aide de Transact-SQL.|
| [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md) | Ce didacticiel vous explique comment basculer vers un réplica secondaire géo-répliqué à l’aide du portail Azure.|
| [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md) | Ce didacticiel vous explique comment basculer vers un réplica secondaire géo-répliqué à l’aide de PowerShell.|
| [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Ce didacticiel vous explique comment basculer vers un réplica secondaire géo-répliqué à l’aide de Transact-SQL.|
||||

## Synchronisation des données

Ce didacticiel fournit des informations relatives à la [synchronisation des données](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Didacticiel | Description |
|---|---|---| 
| [Prise en main d’Azure SQL Data Sync (version préliminaire)](sql-database-get-started-sql-data-sync.md) | Dans ce didacticiel, vous allez vous familiariser avec les concepts de base d’Azure SQL Data Sync avec le portail Azure. |
||||

## Étapes suivantes

[Explorer les démarrages rapides de la solution de base de données SQL Azure](sql-database-solution-quick-starts.md)

<!---HONumber=AcomDC_0824_2016-->