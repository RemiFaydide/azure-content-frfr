<properties
   pageTitle="Déterminer la compatibilité d’une base de données SQL à l’aide de SqlPackage.exe | Microsoft Azure"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage"
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
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Cet article explique comment déterminer si une base de données SQL Server est compatible pour la migration vers Base de données SQL à l’aide de l’utilitaire d’invite de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Avec SqlPackage.exe

1. Ouvrez une invite de commandes et modifiez le répertoire contenant la version la plus récente de sqlpackage.exe. Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/fr-FR/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.
2. Exécutez la commande SqlPackage suivante avec les arguments ci-dessous pour votre environnement :

	'sqlpackage.exe /Action:Export /ssn:< nom\_serveur > /sdn:< nom\_base\_de\_données > /tf:< fichier\_cible > /p:TableData=< nom\_schéma.nom\_table > > < fichier\_sortie > 2>&1'

	| Argument | Description |
	|---|---|
	| < nom\_serveur > | nom du serveur source |
	| < nom\_base\_de\_données > | nom de la base de données source |
	| < fichier\_cible > | nom de fichier et emplacement du fichier BACPAC |
	| < nom\_schéma.nom\_table > | tables pour lesquelles les données sont générées dans le fichier cible |
	| < fichier\_sortie > | nom de fichier et emplacement du fichier de sortie avec des erreurs, le cas échéant |

	L’argument /p:TableName est utilisé afin de tester uniquement la compatibilité de base de données en vue d’une exportation vers Azure SQL DB V12. Nous ne cherchons pas en effet à exporter les données de toutes les tables. Malheureusement, l’argument d’exportation de sqlpackage.exe ne prend pas en charge l’extraction de zéro table. Vous devez spécifier au moins une table, telle une petite table simple. Le fichier < fichier\_sortie > contient le rapport d’erreurs. La chaîne "> 2>&1" dirige le résultat standard et l'erreur standard générés par l'exécution de la commande vers le fichier de sortie spécifié.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Ouvrez le fichier de sortie et passez en revue les erreurs de compatibilité, le cas échéant.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Étapes suivantes

- [Dernière version de SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [Dernière version de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Résoudre les problèmes de compatibilité de migration de la base de données](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Ressources supplémentaires

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->