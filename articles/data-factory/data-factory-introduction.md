<properties 
	pageTitle="Présentation de Data Factory, un service d’intégration de données | Microsoft Azure" 
	description="Découvrez Azure Data Factory : un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données." 
	keywords="intégration de données, intégration de données cloud, description d’azure data factory"
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
	ms.topic="get-started-article" 
	ms.date="09/22/2016" 
	ms.author="spelluru"/>

# Présentation du service Azure Data Factory, un service d’intégration de données dans le cloud

## qu'est-ce qu'Azure Data Factory ? 
Data Factory est un service d’intégration de données dans le cloud qui gère et automatise le **déplacement** et la **transformation** des données. Vous pouvez créer des solutions d’intégration de données à l’aide du service Data Factory, qui peut ingérer des données provenant de différentes banques de données, transformer/traiter les données et publier les données résultantes dans les banques de données.

Le service Data Factory vous permet de créer des pipelines de données qui déplacent et transforment les données, puis d’exécuter ces pipelines selon une planification spécifique (une fois par heure, par jour, par semaine, etc.). Il fournit également des affichages élaborés pour visualiser le lignage et les dépendances entre vos pipelines de données, ainsi qu’une vue unifiée depuis laquelle vous pouvez surveiller l’ensemble de ces pipelines afin d’identifier les problèmes et de configurer des alertes d’analyse en toute simplicité.

![Diagramme : présentation de Data Factory, un service d’intégration de données](./media/data-factory-introduction/what-is-azure-data-factory.png) **Figure 1.** Ingérez les données de diverses sources de données, préparez-les, transformez-les et analysez-les, puis publiez des données prêtes à l’emploi en vue de leur consommation.

## Pipelines et activités
Dans une solution Data Factory, vous créez un ou plusieurs **pipelines** de données. Un pipeline constitue un regroupement logique d’activités. Ils sont utilisés pour regrouper des activités dans une unité. Ces activités exécutent alors conjointement une tâche.

Les **activités** définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’une banque de données vers une autre. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge deux types d’activités : les activités de déplacement des données et les activités de transformation des données.
  
## Activités de déplacement des données 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Pour plus d’informations, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).

## Activités de transformation des données
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Pour plus d’informations, consultez l’article [Activités de transformation des données](data-factory-data-transformation-activities.md).

Si vous devez déplacer des données vers ou à partir d’une banque de données qui n’est pas prise en charge par l’activité de copie, ou transformer des données à l’aide de votre propre logique, créez une **activité .NET personnalisée**. Pour plus d’informations sur la création et l’utilisation d’une activité personnalisée, consultez [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md).

## Services liés
Les services liés définissent les informations nécessaires à Data Factory pour se connecter à des ressources externes (exemple : Stockage Azure, instance SQL Server locale, Azure HDInsight). Data Factory fait appel aux services liés pour deux raisons :

- Pour représenter une **banque de données** et notamment une instance SQL Server, une base de données Oracle, un partage de fichiers locaux ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des banques de données prises en charge, consultez la section [Activités de déplacement des données](data-factory-data-movement-activities.md).
- Pour représenter une **ressource de calcul** qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsightHive s’exécute sur un cluster HDInsight Hadoop. pour obtenir la liste des environnements de calcul pris en charge, consultez la section [Activités de transformation des données](data-factory-data-transformation-activities.md).

## Groupes de données 
Les services liés se chargent de lier des banques de données à une fabrique de données Azure. Les jeux de données forment des structures de données au sein des banques de données. Par exemple, un service lié Stockage Azure fournit les informations de connexion à un compte de stockage Azure pour Data Factory. Un jeu de données d’objets blob Azure spécifie le conteneur et le dossier d’objets blob du stockage d’objets Azure à partir desquels le pipeline doit lire les données. De même, un service lié SQL Azure fournit les informations de connexion pour une base de données SQL Azure et un jeu de données SQL Azure spécifie la table qui contient les données.

## Relation entre des entités Data Factory
Data Factory contient quelques entités clés qui fonctionnent conjointement pour définir les données d’entrée et de sortie, les événements de traitement et le calendrier et les ressources nécessaires pour exécuter le flux de données souhaité.

![Diagramme : Data Factory, un service d’intégration de données cloud - Concepts clés](./media/data-factory-introduction/data-integration-service-key-concepts.png) **Figure 2.** Relations entre jeu de données, activité, pipeline et service lié

Avec ces quatre concepts simples de services liés, de jeux de données, d’activités et de pipelines, vous êtes prêt à vous lancer ! Vous pouvez [générer votre premier pipeline](data-factory-build-your-first-pipeline.md).

## Régions prises en charge
Actuellement, vous pouvez créer des fabriques de données aux **États-Unis de l’Ouest**, aux **États-Unis de l’Est** et en **Europe du Nord**. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul.

Azure Data Factory ne permet pas en soi de stocker des données. Il vous permet de créer des flux pilotés par les données afin d’orchestrer le déplacement de données entre les [magasins de données pris en charge ](data-factory-data-movement-activities.md#supported-data-stores) ainsi que le traitement des données à l’aide [des services de calcul](data-factory-compute-linked-services.md) situés dans d’autres régions ou dans un environnement local. Il vous permet également de [surveiller et gérer des workflows](data-factory-monitor-manage-pipelines.md) au moyen de programmes et à l’aide des mécanismes de l’interface utilisateur.

Même si Azure Data Factory est disponible uniquement aux **États-Unis de l’Ouest**, aux **États-Unis de l’Est** ainsi qu’en **Europe du Nord**, le service de déplacement des données intégré à Data Factory est [mondialement](data-factory-data-movement-activities.md#global) disponible dans plusieurs régions. Lorsqu’un magasin de données se trouve derrière un pare-feu, le déplacement des données est assuré au moyen d’une [passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) installée dans votre environnement local.

Supposons que vos environnements de calcul (cluster Azure HDInsight et Azure Machine Learning, par exemple) s’exécutent hors de la région Europe de l’ouest. Vous pouvez dans ce cas créer et utiliser une instance Azure Data Factory en Europe du Nord et l’utiliser pour planifier des tâches sur vos environnements de calcul en Europe de l’ouest. Quelques millisecondes suffisent à Data Factory pour déclencher la tâche dans votre environnement de calcul, mais l’heure d’exécution du travail dans votre environnement informatique ne change pas.

Nous avons l’intention d’étendre la prise en charge d’Azure Data Factory à toutes les zones géographiques.
  
## Étapes suivantes
Pour découvrir comment créer des fabriques de données avec des pipelines de données, suivez les instructions pas à pas des didacticiels suivants.

Didacticiel | Description
-------- | -----------
[Créer un pipeline de données qui traite les données à l’aide du cluster Hadoop](data-factory-build-your-first-pipeline.md) | Dans ce didacticiel, vous devez générer votre première fabrique de données Azure avec un pipeline de données qui **traite les données** en exécutant le script Hive sur un cluster Azure HDInsight (Hadoop). |
[Build a data pipeline to move data between two cloud data stores (Créer un pipeline de données pour déplacer des données entre deux banques de données cloud)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Dans ce didacticiel, vous créez une fabrique de données avec un pipeline qui **déplace des données** de Blob Storage vers la base de données SQL.
[Build a data pipeline to move data between an on-premises data store and a cloud data store using Data Management Gateway (Créer un pipeline de données pour déplacer des données entre une banque de données locale et une banque de données cloud à l’aide de la passerelle de gestion des données)](data-factory-move-data-between-onprem-and-cloud.md) | Dans ce didacticiel, vous créez une fabrique de données avec un pipeline qui **déplace des données** d’une base de données SQL Server **locale** vers un objet blob Azure. Dans le cadre de la procédure pas à pas, vous installez et configurez la passerelle de gestion des données sur votre ordinateur. 

<!---HONumber=AcomDC_0928_2016-->