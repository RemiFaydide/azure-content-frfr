<properties
	pageTitle="Importer des données dans Machine Learning Studio à partir de sources de données en ligne | Microsoft Azure"
	description="Comment importer vos données d’apprentissage Azure Machine Learning Studio depuis différentes sources en ligne"
	keywords="importer des données, format de données, types de données, sources de données, données d'apprentissage"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="bradsev;garye" />


# Importation de vos données Azure Machine Learning Studio à partir de différentes sources de données en ligne avec le module Importer les données

Cet article décrit le support pour l'importation en ligne de données provenant de plusieurs sources et les informations nécessaires au déplacement depuis ces sources vers l’expérience Azure Machine Learning.

> [AZURE.NOTE] Cet article fournit des informations générales sur le module [Importer les données][import-data]. Pour plus d’informations sur les types de données auxquelles vous pouvez accéder, les formats, les paramètres et les réponses aux questions courantes, consultez la rubrique de référence du module pour le module [Importer les données][import-data].

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## Introduction

Vous pouvez accéder aux données d’Azure Machine Learning Studio à partir d’une des nombreuses sources de données en ligne pendant que votre expérience s’exécute à l’aide du module [Importer les données][import-data] \:

- Une URL web avec HTTP
- Hadoop avec HiveQL
- Stockage des objets blob
- Table Azure
- Base de données Azure SQL ou SQL Server sur les machines virtuelles Azure
- Base de données SQL Server locale
- Un fournisseur de flux de données, actuellement, OData
 
Le flux de travail pour l’exécution d’expériences dans Azure Machine Learning Studio consiste à glisser-déposer des composants sur le canevas. Pour accéder aux sources de données en ligne, ajoutez le module [Importer les données][import-data] à votre expérience, sélectionnez la **source de données**, puis indiquez les paramètres nécessaires pour accéder aux données. Les sources de données en ligne prises en charge sont détaillées dans le tableau ci-dessous. Ce tableau récapitule également les formats de fichier pris en charge et les paramètres qui sont utilisés pour accéder aux données.

Notez que ces données étant accessibles pendant l’exécution de votre expérience, elles ne sont disponibles que pour cette expérience. En revanche, les données stockées dans un module de jeu de données sont disponibles pour n’importe dans votre espace de travail.

> [AZURE.IMPORTANT] Actuellement, les modules [Importer les données][import-data] et [Exporter les données][export-data] peuvent lire et écrire des données uniquement à partir d’un stockage Azure créé à l’aide du modèle de déploiement classique. En d’autres termes, le nouveau type de compte de stockage d’objets blob Azure qui offre un niveau d’accès au stockage chaud ou un niveau d’accès au stockage froid n’est pas encore pris en charge.

> En règle générale, les comptes de stockage Azure que vous avez peut-être créés avant que cette option de service ne soit disponible ne devraient pas être affectés. Si vous avez besoin de créer un nouveau compte, sélectionnez **Classique** comme modèle de déploiement, ou utilisez Resource Manager et, comme **Type de compte**, sélectionnez **Usage général** plutôt que **Stockage d’objets blob**.

> Pour plus d’informations, consultez [Stockage des objets blob Azure : niveaux de stockage chauds et froids](../storage/storage-blob-storage-tiers.md).



## Sources de données en ligne prises en charge
Le module **Importer les données** d’Azure Machine Learning prend en charge les sources de données suivantes :

Source de données | Description | Paramètres |
---|---|---|
URL Web via HTTP |Lit les données au format CSV (valeurs séparées par des virgules), TSV (valeurs séparées par des tabulations), ARFF (format de fichier de relation d’attribut) et SVM-light (Machines vectorielles (SVM clair)), à partir de n’importe quelle URL web qui utilise le protocole HTTP | <b>URL</b> : indique le nom complet du fichier, notamment l'URL du site et le nom de fichier, avec n'importe quelle extension. <br/><br/><b>Format de données</b> : spécifie un des formats de données pris en charge : CSV, TSV, ARFF ou SVM-light. Lorsque les données ont une ligne d’en-tête, elle est utilisée pour attribuer des noms de colonne.|
Hadoop/HDFS|Lit les données à partir d’un stockage distribué dans Hadoop. Spécifiez les données souhaitées à l’aide de HiveQL, langage de requête de type SQL. HiveQL peut également servir à agréger et effectuer le filtrage des données avant d’ajouter les données à Machine Learning Studio.|<b>Requête de base de données Hive</b> : spécifie la requête Hive utilisée pour générer les données.<br/><br/><b>URI du serveur HCatalog </b> : spécifie le nom de votre cluster en utilisant le format *&lt; votre nom de cluster et gt;. azurehdinsight.net.*<br/><br/><b>Nom du compte utilisateur Hadoop</b> : spécifie le nom de compte d'utilisateur Hadoop ayant servi à configurer le cluster.<br/><br/><b>Mot de passe utilisateur Hadoop</b> : spécifie les informations d'identification utilisées lors de la configuration du cluster. Pour plus d'informations, consultez [Créer des clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Emplacement des données de sortie</b> : indique si les données sont stockées dans un système de fichiers distribué Hadoop (HDFS) ou dans Azure. <br/><ul>Si vous stockez des données de sortie dans HDFS, spécifiez l'URI du serveur HDFS. (Veillez à utiliser le nom du cluster HDInsight sans le préfixe HTTPS://). <br/><br/>Si vous stockez vos données de sortie dans Azure, vous devez spécifier le nom de compte de stockage Azure, la clé d'accès et le nom de conteneur de stockage.</ul>|
Base de données SQL |Lit les données stockées dans une base de données SQL Azure ou dans une base de données SQL Server s’exécutant sur une machine virtuelle Azure. | <b>Nom du serveur de base de données</b> : spécifie le nom du serveur sur lequel la base de données s'exécute.<br/><ul>S'il s'agit d'une base de données SQL Azure, saisissez le nom du serveur généré. En général, il se présente sous la forme *&lt; generated\_identifier&gt;. database.windows.net.* <br/><br/>Dans le cas d'un serveur SQL server hébergé sur une machine virtuelle Azure, saisissez *tcp: &lt; Nom DNS de Machine virtuelle &gt; 1433*</ul><br/><b>nom de base de données </b> : spécifie le nom de la base de données sur le serveur. <br/><br/><b>Nom du compte utilisateur du serveur</b> : spécifie le nom d'utilisateur du compte disposant des autorisations d'accès à la base de données. <br/><br/><b>Mot de passe utilisateur Server</b> : spécifie le mot de passe du compte d'utilisateur.<br/><br/><b>Accepter tout certificat de serveur</b> : utilisez cette option (moins sûre) si vous souhaitez ignorer la vérification du certificat de site avant de lire vos données.<br/><br/><b>Requête de base de données</b> : saisissez une instruction SQL qui décrit les données que vous souhaitez lire.|
Base de données SQL locale |Lit les données stockées dans une base de données SQL locale. | <b>Passerelle de données</b> : spécifie le nom de la passerelle de gestion de données installée sur un ordinateur à partir duquel elle peut accéder à votre base de données SQL Server. Pour plus d’informations sur la configuration de la passerelle, consultez [Effectuer des analyses avancées avec Azure Machine Learning en utilisant les données d’un serveur SQL local](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nom du serveur de base de données</b> : spécifie le nom du serveur sur lequel la base de données est en cours d’exécution.<br/><br/><b>Nom de la base de données </b>: spécifie le nom de la base de données sur le serveur. <br/><br/><b>Nom du compte utilisateur du serveur</b> : spécifie le nom d'utilisateur du compte disposant des autorisations d'accès à la base de données. <br/><br/><b>Nom d’utilisateur et mot de passe</b> : cliquez sur <b>Entrer des valeurs</b> pour saisir les informations d’identification de votre base de données. Vous pouvez utiliser l’authentification intégrée Windows ou l’authentification SQL Server en fonction de la configuration de votre serveur local SQL Server. <br/><br/><b>Requête de base de données</b> : tapez une instruction SQL qui décrit les données que vous souhaitez lire.|
Table Azure|Lit les données du service de tableau dans le stockage Azure.<br/><br/>Si vous lisez rarement de grandes quantités de données, utilisez le Service de Tableau Azure. Il fournit une solution de stockage ultra disponible, flexible, non relationnelle (NoSQL), économique et hautement évolutive.| Les options du module **Importer les données** changent selon que vous accédez à des informations publiques ou à un compte de stockage privé qui nécessite des informations d’identification de connexion. Cela est déterminé par le <b>Type d'authentification</b> qui peut avoir la valeur « PublicOrSAS » ou « Compte », chacun d'entre eux possède son propre ensemble de paramètres. <br/><br/><b>URl de signature d'accès public ou partage (SAS) </b> : les paramètres sont :<br/><br/><ul><b>URI de tableau</b> : spécifie l'URL SAS publique pour le tableau.<br/><br/><b>Spécifie les lignes dans lesquelles rechercher les noms de propriété</b> : les valeurs sont <i>TopN</i> pour analyser le nombre spécifié de lignes, ou <i>ScanAll</i> pour obtenir toutes les lignes du tableau. <br/><br/>Si les données sont homogènes et prévisibles, il est recommandé de sélectionner *TopN* et de saisir un nombre pour N. Pour les tableaux plus volumineux, cela peut se traduire par des temps de lecture plus courts.<br/><br/>Si les données sont structurées avec des ensembles de propriétés qui varient en fonction de la profondeur et la position du tableau, choisissez le *ScanAll* option pour analyser toutes les lignes. Cela garantit l'intégrité de votre propriété résultante et la conversion de métadonnées.<br/><br/></ul><b>Compte de stockage privé</b> : les paramètres sont: <br/><br/><ul><b>nom de compte</b> : spécifie le nom du compte qui contient la table à lire.<br/><br/><b>Clé de compte</b> : spécifie la clé de stockage associée au compte.<br/><br/><b>Nom de la table</b> : spécifie le nom de la table qui contient les données à lire.<br/><br/><b>Lignes à analyser pour les noms de propriété</b> : les valeurs sont <i>TopN</i> pour analyser le nombre spécifié de lignes, ou <i>ScanAll</i> pour obtenir toutes les lignes de la table.<br/><br/>Si les données sont homogènes et prévisibles, nous vous recommandons de sélectionner *TopN* et d'entrer un nombre pour N. Pour les tables volumineuses, cela peut entraîner des temps de lecture plus rapides.<br/><br/>Si les données sont structurées avec des ensembles de propriétés qui varient en fonction de la profondeur et la position de la table, choisissez l'option *ScanAll* pour analyser toutes les lignes. Cela garantit l'intégrité de la propriété obtenue et de la conversion des métadonnées.<br/><br/>|
un stockage Azure Blob | Lit les données stockées dans le service d'objet Blob dans le stockage Azure, notamment les images, le texte non structuré ou les données binaires.<br/><br/>Vous pouvez utiliser le service Blob pour exposer publiquement des données ou pour stocker en privé des données d'application. Vous pouvez accéder à vos données depuis n’importe où grâce à des connexions HTTP ou HTTPS. | Les options du module **Importer les données** changent selon que vous accédez à des informations publiques ou à un compte de stockage privé qui nécessite des informations d’identification de connexion. Elles sont déterminées par le <b>Type d'authentification</b> qui peut avoir pour valeur « PublicOrSAS » ou « Compte ».<br/><br/><b>URl de signature d'accès public ou partagé (SAS)</b> : les paramètres sont :<br/><br/><ul><b>URI</b> : spécifie l'URL public ou SAS pour l'objet blob de stockage.<br/><br/><b>Format de fichier</b> : spécifie le format des données dans le service Blob. Les formats pris en charge sont CSV, TSV et ARFF.<br/><br/></ul><b>Compte de stockage privé</b> : les paramètres sont : <br/><br/><ul><b>nom de compte</b> : spécifie le nom du compte qui contient l'objet blob à lire.<br/><br/><b>Clé de compte</b> : spécifie la clé de stockage associée au compte.<br/><br/><b>Chemin d'accès au conteneur, au répertoire ou à l'objet blob </b> : spécifie le nom de l'objet blob qui contient les données à lire.<br/><br/><b>Format de fichier Blob</b> : spécifie le format des données dans le service blob. Les formats de données pris en charge sont CSV, TSV, ARFF, CSV avec un codage spécifié et Excel. <br/><br/><ul>Si le format est CSV ou TSV, veillez à indiquer si le fichier contient une ligne d'en-tête.<br/><br/>Vous pouvez utiliser l'option Excel pour lire des données à partir de classeurs Excel. Dans l'option <i>format de données Excel</i>, indiquez si les données se trouvent dans une plage de feuille de calcul Excel ou un tableau Excel. Dans l'option <i>feuille de calcul Excel ou tableau intégré</i>, spécifiez le nom de la feuille ou du tableau que vous souhaitez lire.</ul><br/>|
Fournisseur de flux de données | Lit les données d’un fournisseur de flux pris en charge. Actuellement, seul le protocole Open Data Protocol (OData) est pris en charge. | <b>Type de contenu de données</b> : spécifie le format OData.<br/><br/><b>URL source</b> : spécifie l'URL complète du flux de données. <br/>Par exemple, l'URL suivante lit à partir de la base de données exemple Northwind : http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/

<!---HONumber=AcomDC_0921_2016-->