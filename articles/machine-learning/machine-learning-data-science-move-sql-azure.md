<properties 
	pageTitle="Déplacement de données vers une base de données SQL Azure pour Azure Machine Learning | Azure" 
	description="Création d’une table SQL et chargement de données dans une table SQL" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2016"
	ms.author="bradsev" />

# Déplacement de données vers une base de données SQL Azure pour Azure Machine Learning

Cette rubrique présente les options de déplacement des données à partir de fichiers plats (formats CSV ou TSV) ou de données stockées sur un ordinateur SQL Server local vers une base de données Azure SQL. Ces tâches permettant de déplacer des données vers le cloud font partie du processus TDSP (Team Data Science Process).

Pour la rubrique présentant les options de déplacement de données sur un SQL Server local pour Machine Learning, consultez [Déplacer des données vers SQL Server sur une machine virtuelle Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Le **menu** suivant pointe vers des rubriques qui expliquent comment recevoir des données dans d’autres environnements cibles où les données peuvent être stockées et traitées pendant le processus TDSP (Team Data Science Process).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Le tableau suivant récapitule les options de déplacement de données vers une base de données SQL Azure.

<b>SOURCE</b> |<b>DESTINATION : base de données SQL Azure</b> |
-------------- |--------------------------------|
<b>Fichier plat (mise en forme CSV ou TSV)</b> |<a href="#bulk-insert-sql-query">Requête SQL Bulk Insert |
<b>SQL Server local</b> | 1\. <a href="#export-flat-file">Exporter dans un fichier plat<br> 2. <a href="#insert-tables-bcp">Assistant Migration de la base de données SQL<br> 3. <a href="#db-migration">Sauvegarde et restauration de base de données<br> 4. <a href="#adf">Azure Data Factory |


## <a name="prereqs"></a>Configuration requise
Les procédures décrites ici nécessitent :

* Un **abonnement Azure**. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Dans ce didacticiel, vous utilisez un compte de stockage Azure pour stocker des données. Si vous ne possédez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account). Après avoir créé le compte de stockage, vous devez obtenir la clé du compte utilisée pour accéder au stockage. Consultez [Affichage, copie et régénération de clés d’accès de stockage](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accès à une **base de données SQL Azure** Si vous devez configurer une base de données SQL Azure, l’article [Créer votre première base de données SQL Microsoft Azure](../sql-database/sql-database-get-started.md) fournit des informations sur la configuration d'une nouvelle instance de base de données SQL Azure.
* **Azure PowerShell** installé et configuré localement. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

**Données** : les processus de migration sont illustrés à l’aide du [jeu de données NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/). Le jeu de données NYC Taxi contient des informations sur les données de voyage et les prix. Il est disponible, comme mentionné dans cet article, sur le stockage d’objets blob Azure : [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/). Un échantillon et une description de ces fichiers sont fournis dans la [description du jeu de données des voyages NYC Taxi](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Vous pouvez adapter les procédures décrites ici à un jeu de vos propres données ou suivre les étapes décrites à l'aide du jeu de données NYC Taxi. Pour télécharger le jeu de données NYC Taxi dans votre base de données SQL Server locale, suivez la procédure décrite dans [Bulk Import Data into SQL Server Database](machine-learning-data-science-process-sql-walkthrough.md#dbload). Ces instructions concernent un SQL Server sur une machine virtuelle Azure, mais la procédure de téléchargement vers le serveur local SQL Server est la même.


## <a name="file-to-azure-sql-database"></a> Déplacement des données à partir d'un fichier plat source vers une base de données Azure SQL

Les données de fichiers plats (au format CSV ou TSV) peuvent être déplacées vers une base de données Azure SQL à l'aide d'une requête SQL Bulk Insert.

### <a name="bulk-insert-sql-query"></a>Requête SQL Bulk Insert

Les étapes de la procédure à l'aide de la requête SQL Bulk Insert sont similaires à celles décrites dans les sections de déplacement des données à partir d'une source de fichier plat vers SQL Server sur une machine virtuelle Azure. Pour plus d’informations, consultez [Requête SQL Bulk Insert](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a> Déplacement des données à partir d’un SQL Server local vers une base de données Azure SQL

Si la source de données est stockée dans un serveur local SQL Server, il existe différentes possibilités pour déplacer les données vers une base de données Azure SQL :

1. [Exporter dans un fichier plat](#export-flat-file)
2. [Assistant Migration de la base de données SQL](#insert-tables-bcp)
3. [Sauvegarde et restauration de base de données](#db-migration)
4. [Azure Data Factory](#adf)

Les étapes des trois premières options sont très similaires à celles décrites dans les sections de [Déplacer des données vers un serveur SQL Server sur une machine virtuelle Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) qui traitent des mêmes procédures. Vous trouverez dans les instructions ci-dessous des liens vers les sections appropriées de cette rubrique.

###<a name="export-flat-file"></a>Exporter dans un fichier plat

Les étapes de l’exportation vers un fichier plat sont similaires à celles de la section [Exporter dans un fichier plat](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Assistant Migration de la base de données SQL

Les étapes d’utilisation de l’Assistant de migration de base de données SQL sont semblables à celles de la section [Assistant Migration de la base de données SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Sauvegarde et restauration de base de données

Les étapes d’utilisation de la sauvegarde et de restauration de la base de données sont similaires à celles de la section [Sauvegarde et restauration de base de données](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Azure Data Factory

La procédure de déplacement des données vers une base de données Azure SQL avec Azure Data Factory (ADF) est décrite dans la rubrique [Déplacement de données à partir d'un serveur SQL local vers SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md). Cette rubrique montre comment déplacer des données d’une base de données SQL Server locale vers une base de données Azure via le stockage d’objets blob Azure à l’aide d’ADF.

Envisagez d'utiliser ADF lorsque les données doivent être migrées en permanence dans un scénario hybride qui accède aux ressources locales et cloud, et lorsque les données sont traitées ou doivent être modifiées ou si vous avez une logique métier ajoutée en cours de migration. ADF permet la planification et la surveillance des travaux à l'aide de scripts JSON simples qui gèrent le déplacement des données sur une base périodique. ADF dispose également d'autres fonctionnalités comme la prise en charge des opérations complexes.

<!---HONumber=AcomDC_0921_2016-->