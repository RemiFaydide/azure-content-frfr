<properties 
	pageTitle="Déplacer des données vers SQL Server sur une machine virtuelle Azure | Azure" 
	description="Déplacer des données à partir de fichiers plats ou d'un SQL Server local vers SQL Server sur une machine virtuelle Azure." 
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

# Déplacer des données vers SQL Server sur une machine virtuelle Azure

Cette rubrique présente les options de déplacement des données à partir de fichiers plats (format CSV ou TSV) ou d’un serveur SQL Server local vers un serveur SQL Server sur une machine virtuelle Azure. Ces tâches permettant de déplacer des données vers le cloud font partie du processus TDSP (Team Data Science Process).

Pour la rubrique présentant les options de déplacement de données sur une base de données SQL Azure pour Machine Learning, consultez [Déplacement de données vers une base de données SQL Azure pour Azure Machine Learning](machine-learning-data-science-move-sql-azure.md).

Le **menu** ci-dessous pointe vers des rubriques qui expliquent comment recevoir des données dans d’autres environnements cibles où les données peuvent être stockées et traitées pendant le processus TDSP (Team Data Science Process).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


Le tableau suivant récapitule les options de déplacement de données vers SQL Server sur une machine virtuelle Azure.

<b>SOURCE</b> |<b>DESTINATION : SQL Server dans les machines virtuelles Azure</b> |
------------------ |-------------------- |
<b>Fichier plat</b> |1\. <a href="#insert-tables-bcp">Utilitaire de copie en bloc à ligne de commande (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Requête SQL Bulk Insert </a><br> 3. <a href="#sql-builtin-utilities">Utilitaires graphiques intégrés dans SQL Server</a>
<b>Serveur SQL Server local</b> | 1\. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Assistant de déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportation dans un fichier plat </a><br> 3. <a href="#sql-migration">Assistant Migration de la base de données SQL </a> <br> 4. <a href="#sql-backup">Sauvegarde et restauration de base de données </a><br>

Notez que ce document suppose que les commandes SQL sont exécutées à partir de SQL Server Management Studio ou Visual Studio Database Explorer.

> [AZURE.TIP] Comme autre solution, vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour créer et planifier un pipeline qui déplace des données vers une machine virtuelle SQL Server sur Azure. Pour plus d’informations, consultez [Copie de données avec Azure Data Factory (activité de copie)](../data-factory/data-factory-data-movement-activities.md)


## <a name="prereqs"></a>Configuration requise
Ce didacticiel part du principe que vous disposez de :

* Un **abonnement Azure**. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Dans ce didacticiel, vous allez utiliser un compte de stockage Azure pour stocker des données. Si vous ne possédez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account). Après avoir créé le compte de stockage, vous devrez obtenir la clé du compte utilisée pour accéder au stockage. Consultez [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Approvisionnement d’un **serveur SQL Server sur une machine virtuelle Azure**. Pour obtenir des instructions, consultez [Configurer une machine virtuelle Azure SQL Server comme serveur IPython Notebook pour des analyses avancées](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* **Azure PowerShell** installé et configuré localement. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a> Déplacement des données à partir d’un fichier plat source vers SQL Server sur une machine virtuelle Azure

Si vos données se trouvent dans un fichier plat (au format ligne/colonne), les méthodes suivantes permettent de les copier dans l’instance SQL Server VM on Azure :

1. [Utilitaire de copie en bloc à ligne de commande (BCP)](#insert-tables-bcp)
2. [Requête SQL Bulk Insert ](#insert-tables-bulkquery)
3. [Utilitaires graphiques intégrés dans SQL Server (Importation/Exportation, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilitaire de copie en bloc à ligne de commande (BCP)

BCP est un utilitaire à ligne de commande, installé avec SQL Server. C’est l’un des outils les plus rapides pour déplacer des données. Il fonctionne sur les trois variantes de SQL Server (instance SQL Server locale, SQL Azure et machine virtuelle SQL Server sur Azure).

> [AZURE.NOTE]**Où mes données doivent-elles se trouver pour BCP ?**  
> Ce n’est pas une obligation, mais le transfert est plus rapide si les fichiers contenant les données source résident sur la même machine que l’instance SQL Server cible (débit du réseau par rapport au débit d’E/S du disque local). Vous pouvez déplacer les fichiers plats contenant les données vers la machine hébergeant SQL Server, en utilisant différents outils de copie, tels que [AZCopy](../storage-use-azcopy.md), [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) ou le copier/coller Windows via le protocole RDP (Remote Desktop Protocol).

1. Vérifiez que la base de données et les tables sont créées dans la base de données SQL Server cible. Voici un exemple montrant comment faire à l’aide des commandes `Create Database` et `Create Table` :

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. Générez le fichier de format qui décrit le schéma de la table, en exécutant la commande suivante dans la ligne de commande de la machine où BCP est installé.

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. Insérez les données dans la base de données, en utilisant la commande BCP suivante. Cette commande fonctionne à partir de la ligne de commande, si SQL Server est installé sur la même machine :

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimisation des insertions BCP** Consultez l’article [Conseils pour optimiser l’importation en bloc](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) pour optimiser ces insertions.


### <a name="insert-tables-bulkquery-parallel"></a>Parallélisation des insertions pour accélérer le déplacement des données

Si le volume de données déplacées est important, vous pouvez accélérer l’opération en exécutant plusieurs commandes BCP simultanément dans un script PowerShell.

> [AZURE.NOTE] **Traitement de volumes importants de données** Pour optimiser le chargement de volumes importants et très importants de jeux de données, partitionnez vos tables de base de données physiques et logiques en utilisant plusieurs groupes de fichiers et tables de partition. Pour plus d’informations sur la création et le chargement de données dans des tables de partition, consultez l’article [Importer des données en bloc et en parallèle à l’aide de tables de partition SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


L’exemple de script PowerShell ci-dessous montre comment effectuer des insertions en parallèle à l’aide de BCP :
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Requête SQL Bulk Insert

[La requête d’insertion en bloc SQL](https://msdn.microsoft.com/library/ms188365) permet d’importer des données provenant de fichiers à lignes/colonnes dans la base de données (les types pris en charge sont présentés dans la section [Préparer les donner pour l’exportation ou l’importation en bloc (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Voici quelques exemples de requêtes Bulk Insert :

1. Analysez vos données et configurez les options de personnalisation souhaitées avant de procéder à l’importation, pour vérifier que la base de données SQL Server attend le même format pour les champs particuliers, comme les dates. Voici un exemple de configuration du format de date année-mois-jour (si vos données contiennent des dates dans ce format) :

		SET DATEFORMAT ymd;	
	
2. Importez des données avec des instructions import en bloc :

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities"></a>Utilitaires intégrés dans SQL Server

Vous pouvez utiliser l’utilitaire SSIS (SQL Server Integrations Services) pour importer les données d’un fichier plat dans SQL Server VM on Azure. SSIS est disponible dans deux environnements Studio. Pour en savoir plus, consultez l’article [ Services d’intégration (SSIS) et environnements Studio](https://technet.microsoft.com/library/ms140028.aspx) :

- Pour en savoir plus sur les outils SQL Server Data Tools, consultez l’article [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx).
- Pour en savoir plus sur l’Assistant Importation et Exportation, consultez l’article [Assistant Importation et Exportation SQL Server](https://msdn.microsoft.com/library/ms141209.aspx).


## <a name="sqlonprem_to_sqlonazurevm"></a>Déplacement des données à partir d’un serveur SQL Server local vers un serveur SQL Server sur une machine virtuelle Azure

Vous pouvez également utiliser les stratégies de migration suivantes :

1. [Assistant de déploiement d'une base de données SQL Server sur une machine virtuelle Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exporter dans un fichier plat](#export-flat-file)
3. [Assistant Migration de la base de données SQL](#sql-migration)
4. [Sauvegarde et restauration de base de données](#sql-backup)

Chacune de ces étapes est décrite ci-après :

### Assistant de déploiement d'une base de données SQL Server sur une machine virtuelle Microsoft Azure

L’**Assistant de déploiement d’une base de données SQL Server sur une machine virtuelle Microsoft Azure** est une méthode simple et recommandée pour déplacer des données d’une instance SQL Server locale vers un serveur SQL Server sur une machine virtuelle Azure. Pour des instructions détaillées, ainsi qu’une discussion des autres possibilités, consultez [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exporter dans un fichier plat

Plusieurs méthodes peuvent être utilisées pour exporter en bloc des données à partir d’un serveur SQL Server local et sont documentées dans la section [Importation et exportation de données en bloc (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx). Ce document utilise l’outil BCP (Bulk Copy Program) à titre d’exemple. Une fois les données exportées dans un fichier plat, il est possible de les importer en bloc dans une autre instance SQL Server.

1. Pour exporter les données de l’instance SQL Server locale vers un fichier à l’aide de l’utilitaire BCP, procédez comme suit :

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. Créez la base de données et la table sur la machine virtuelle SQL Server sur Azure à l’aide de `create database` et de `create table` pour le schéma de table exporté à l’étape 1.

3. Créez un fichier de format décrivant le schéma de table des données exportées ou importées. Les détails du fichier de format sont décrits dans [créer un fichier de Format (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

	Génération du fichier de format en cas d’exécution de BCP à partir de la machine SQL Server

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	Génération du fichier de formation en cas d’exécution de BCP à distance sur une instance SQL Server

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. Utilisez l’une des méthodes décrites dans la section [Fichiers plats](#filesource_to_sqlonazurevm) pour déplacer les données de fichiers plats vers une instance SQL Server.

### <a name="sql-migration"></a>Assistant Migration de la base de données SQL

[Assistant Migration de la base de données SQL Server](http://sqlazuremw.codeplex.com/) fournit une manière conviviale pour déplacer des données entre deux instances de SQL Server. Il permet à l’utilisateur de mapper le schéma de données entre les tables sources et cibles, de choisir les types de colonne et d’utiliser d’autres fonctionnalités. Il exécute BCP en arrière-plan. Voici une copie de l’écran d’accueil de SQL Database Migration Wizard :

![Assistant Migration de SQL Server][2]

### <a name="sql-backup"></a>Sauvegarde et restauration de base de données

SQL Server prend en charge :

1. [La fonctionnalité de sauvegarde et de restauration de base de données](https://msdn.microsoft.com/library/ms187048.aspx) (dans un fichier local ou par exportation d’un bacpac dans un objet blob) et les [applications de la couche Données](https://msdn.microsoft.com/library/ee210546.aspx) (à l’aide de bacpac).
2. La possibilité de créer directement des instances SQL Server VM on Azure avec une base de données copiée ou de copier une base de données SQL Azure. Pour plus d’informations, consultez l’article [Utiliser l’Assistant Copie de base de données](https://msdn.microsoft.com/library/ms188664.aspx).

Voici une copie d’écran des options de sauvegarde/restauration de base de données dans SQL Server Management Studio.

![Outil d’importation SQL Server][1]

## Ressources

[Migration d'une base de données vers SQL Server sur une machine virtuelle Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[Vue d’ensemble de SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

<!---HONumber=AcomDC_0921_2016-->