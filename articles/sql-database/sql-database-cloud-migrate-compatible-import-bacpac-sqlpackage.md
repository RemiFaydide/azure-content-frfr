<properties
   pageTitle="Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage"
   description="Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, importer un fichier BACPAC, sqlpackage"
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

# Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portail Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Cet article explique comment effectuer une importation vers la SQL Database à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/fr-FR/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.


> [AZURE.NOTE] Les étapes suivantes supposent que vous avez déjà configuré un serveur SQL Database, que vous disposez des informations de connexion et que vous avez vérifié la compatibilité de votre base de données source.

## Importer un fichier BACPAC dans une base de données SQL Azure à l’aide de SqlPackage

Procédez comme suit pour importer une base de données SQL Server (ou Base de données SQL Azure) compatible à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).

> [AZURE.NOTE] Les étapes suivantes supposent que vous avez déjà configuré un serveur de base de données SQL Azure et que vous disposez des informations de connexion.

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe. Cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande sqlpackage.exe suivante avec les arguments ci-dessous pour votre environnement :

	`sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

	| Argument | Description |
	|---|---|
	| < nom\_serveur > | nom du serveur cible |
	| < nom\_base\_de\_données > | nom de la base de données cible |
	| < nom\_utilisateur > | nom d'utilisateur sur le serveur cible |
	| < mot\_de\_passe > | mot de passe de l'utilisateur |
	| < fichier\_source > | nom de fichier et emplacement du fichier BACPAC en cours d'importation |

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Ressources supplémentaires

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->