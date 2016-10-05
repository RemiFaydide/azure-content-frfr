<properties
	pageTitle="Copie de données Blob Storage vers une base de données SQL à l’aide de Data Factory | Microsoft Azure"
	description="Ce didacticiel vous montre comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données depuis Blob Storage vers une base de données SQL Azure."
	Keywords="blob sql, blob storage, copie de données"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Copie de données Blob Storage vers une base de données SQL à l’aide de Data Factory 
> [AZURE.SELECTOR]
- [Vue d’ensemble et étapes préalables requises](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistant de copie](data-factory-copy-data-wizard-tutorial.md)

Dans ce didacticiel, vous allez créer une fabrique de données avec un pipeline afin de copier des données entre Blob Storage et la base de données SQL.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. Elle est mise en œuvre par un service disponible dans le monde entier, capable de copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).

> [AZURE.NOTE] Pour obtenir une présentation détaillée du service Data Factory, consultez l’article [Présentation d’Azure Data Factory][data-factory-introduction].

##Configuration requise pour le didacticiel
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement Azure**. Si vous n'êtes pas abonné, vous pouvez créer un compte d'essai gratuit en quelques minutes. Consultez l'article [Essai gratuit][azure-free-trial] pour plus d'informations.
- **Compte Azure Storage**. Dans le cadre de ce didacticiel, le stockage d’objets blob est utilisé comme magasin de données **source**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage][data-factory-create-storage] pour découvrir comment en créer un.
- **Base de données SQL Azure**. Vous allez utiliser une base de données SQL Azure comme magasin de données **cible** dans ce didacticiel. Si vous n'avez pas de base de données SQL Azure pouvant être utilisée pour le didacticiel, consultez [Comment créer et configurer une base de données SQL Azure][data-factory-create-sql-database] pour en créer une.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Vous allez utiliser SQL Server Management Studio ou Visual Studio pour créer un exemple de base de données et afficher les données de résultat dans la base de données.

## Récupération du nom de compte Blob Storage et de la clé d'accès 
Pour réaliser ce didacticiel, vous avez besoin du nom et de la clé de votre compte de stockage Azure. Notez le **nom** et la **clé** de votre compte de stockage Azure.

1. Connectez-vous au [portail Azure][azure-portal].
2. Cliquez sur le hub **PARCOURIR** situé à gauche et sélectionnez **Comptes de stockage**.
3. Dans le panneau **Comptes de stockage**, sélectionnez le **compte de stockage Azure** que vous souhaitez utiliser dans ce didacticiel.
4. Sélectionnez le lien **Clés d’accès** sous **PARAMÈTRES**.
5.  Cliquez sur le bouton **copier** (image) situé en regard de la zone de texte **Nom du compte de stockage** et enregistrez/collez-la quelque part (dans un fichier texte, par exemple).
6. Répétez l'étape précédente pour copier ou noter la **clé1**.
7. Fermez tous les panneaux en cliquant sur **X**.

## Récupérer les noms de serveur SQL, de base de données et d’utilisateur
Pour réaliser ce didacticiel, vous avez besoin des noms du serveur SQL Azure, de la base de données et de l’utilisateur. Notez les noms du **serveur**, de la **base de données** et de **l’utilisateur** pour votre base de données SQL Azure.

1. Dans le **portail Azure**, cliquez sur **PARCOURIR** dans le volet gauche et sélectionnez **Bases de données SQL**.
2. Dans le panneau **Bases de données SQL**, sélectionnez la **base de données** que vous souhaitez utiliser dans le cadre de ce didacticiel. Notez le **nom de la base de données**.
3. Dans le panneau **BASE DE DONNÉES SQL**, cliquez sur la vignette **PROPRIÉTÉS**.
4. Notez les valeurs de **NOM DU SERVEUR** et de **CONNEXION D'ADMINISTRATEUR DU SERVEUR**.
5. Fermez tous les panneaux en cliquant sur **X**.

## Autoriser les services Azure à accéder au serveur 
Vérifiez que le paramètre **Autoriser l'accès aux services Azure** est **ACTIVÉ** pour votre serveur SQL Azure pour que le service Data Factory puisse accéder à votre serveur SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

1. Cliquez sur le hub **PARCOURIR** situé à gauche, puis sur **Serveurs SQL**.
2. Sélectionnez **votre serveur**, puis cliquez sur **PARAMÈTRES** dans le panneau **SQL SERVER**.
3. Dans le panneau **PARAMÈTRES**, cliquez sur **Pare-feu**.
4. Dans le panneau **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l'accès aux services Azure**.
5. Fermez tous les panneaux en cliquant sur **X**.

## Préparer Blob Storage et la Base de données SQL 
À présent, préparez votre stockage d'objets blob Azure et votre base de données SQL Azure pour ce didacticiel, en procédant comme suit :

1. Ouvrez le Bloc-notes, collez le texte suivant, puis enregistrez-le sous le nom **emp.txt** dans le dossier **C:\\ADFGetStarted** sur votre disque dur.

        John, Doe
		Jane, Doe

2. Utilisez des outils tels que l'[Explorateur Azure Storage](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et charger le fichier **emp.txt** vers ce dernier.

    ![Azure Storage Explorer. Copie de données Blob Storage vers une base de données SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données SQL Azure.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Si SQL Server 2012/2014 est installé sur votre ordinateur :** suivez les instructions de l'[Étape 2 : connexion à la base de données SQL de l'article Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio][sql-management-studio] pour vous connecter à votre serveur SQL Azure et exécuter le script SQL. Cet article utilise le [portail Azure Classic](http://manage.windowsazure.com), et non le [nouveau portail Azure](https://portal.azure.com), pour configurer le pare-feu d’un serveur SQL Azure.

	Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure afin d’autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez [cet article](../sql-database/sql-database-configure-firewall-settings.md) pour savoir comment configurer le pare-feu de votre serveur SQL Azure.

Vous avez terminé les étapes préalables requises. Cliquez sur un onglet en haut pour réaliser le didacticiel à l’aide de l’une de ces options :

- Portail Azure
- Visual Studio
- PowerShell
- API REST
- API .NET
- Assistant de copie

<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0921_2016-->