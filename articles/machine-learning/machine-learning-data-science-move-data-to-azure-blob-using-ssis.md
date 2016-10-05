<properties
	pageTitle="Déplacer des données vers ou depuis le stockage d’objets blobs Azure à l’aide de connecteurs SSIS | Microsoft Azure"
	description="Déplacer des données vers ou depuis le stockage d’objets blobs Azure à l’aide de connecteurs SSIS"
	services="machine-learning,storage"
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

# Déplacer des données vers ou depuis le stockage d’objets blobs Azure à l’aide de connecteurs SSIS

Le [Feature Pack SQL Server Integration Services pour Azure](https://msdn.microsoft.com/library/mt146770.aspx) fournit des composants permettant de se connecter à Azure, de transférer des données entre des sources de données Azure et des sources de données sur site et de traiter les données stockées dans Azure.

Des conseils sur les technologies utilisées pour déplacer des données vers et/ou depuis le stockage d’objets blob Azure sont disponibles ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Une fois que les clients ont déplacé les données locales dans le cloud, ils peuvent y accéder à partir de n’importe quel service Azure pour mettre à profit la totalité des performances de la suite de technologies Azure. Il peut être utilisé, par exemple, dans Machine Learning ou sur un cluster HDInsight.

Il s’agit généralement de la première étape pour les procédures pas à pas [SQL](machine-learning-data-science-process-sql-walkthrough.md) et [HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

Pour une présentation de scénarios canoniques utilisant SSIS pour répondre aux besoins de l’entreprise communs dans les scénarios d’intégration hybrides, reportez-vous à la section [En faire plus avec le Feature Pack SQL Server Integration Services pour Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx).

> [AZURE.NOTE] Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Composants requis

Pour exécuter les tâches décrites dans cet article, vous devez disposer d’un abonnement Azure et configurer un compte de stockage Azure. Pour charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

- Pour configurer un **abonnement Azure**, consultez la section [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’**un compte de stockage** et obtenir des informations sur le compte et la clé, consultez la section [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).


Pour utiliser les **connecteurs SSIS** vous devez télécharger :

- **SQL Server 2014 ou 2016 Standard (ou version ultérieure)** : l’installation inclut SQL Server Integration Services.
- **Microsoft SQL Server 2014 ou 2016 Integration Services Feature Pack for Azure** peuvent être téléchargés respectivement depuis les pages [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) et [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492).

> [AZURE.NOTE] SSIS est installé avec SQL Server, mais n’est pas inclus dans la version Express. Pour plus d'informations sur les applications incluses dans les différentes éditions de SQL Server, consultez [Éditions de SQL Server](http://www.microsoft.com/fr-FR/server-cloud/products/sql-server-editions/)

Pour les supports de formation sur SSIS, consultez la [formation pratique SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Pour plus d'informations sur la mise en service à l'aide de SISS pour la création des packages d'extraction, de transformation et de chargement (ETL), consultez [Didacticiel SSIS : création d'un Package ETL Simple](https://msdn.microsoft.com/library/ms169917.aspx).

## Télécharger l’ensemble de données Taxi NYC  
L'exemple décrit ici utilise un groupe de données disponible au public (l'ensemble de données [Courses taxi NYC](http://www.andresmh.com/nyctaxitrips/)). Le groupe de données se compose d’environ 173 millions de courses en taxi à New York dans l’année 2013. Il existe deux types de données : les données détaillées relatives aux voyages et celles relatives aux tarifs des courses. Comme il existe un fichier par élément pour chaque mois, nous avons 24 fichiers, chacun d’eux d’un volume de 2 Go avant compression.


## Téléchargement de données vers le stockage d’objets blob Azure
Pour déplacer des données à l’aide du pack de fonctionnalités du site local pour le stockage d’objets blobs Azure, nous utilisons une instance de la [**tâche de téléchargement d’objets blobs Azure**](https://msdn.microsoft.com/library/mt146776.aspx), comme illustré ici :

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


Les paramètres que la tâche utilise sont décrits ici :


Champ|Description|
----------------------|----------------|
**AzureStorageConnection**|Spécifie un gestionnaire de connexions de stockage Azure existant ou en crée un nouveau faisant référence à un compte de stockage Azure qui pointe vers l’endroit où les fichiers de l’objet blob sont hébergés.|
**BlobContainer**|Spécifie le nom du conteneur d’objets blobs qui contient les fichiers téléchargés en tant qu’objets blobs.|
**BlobDirectory**|Spécifie le répertoire blob où le fichier téléchargé est stocké en tant qu’objet blob de bloc. Le répertoire d’objet blob est une structure hiérarchique virtuelle. Si l’objet blob existe déjà, il est remplacé.|
**LocalDirectory**|Spécifie le répertoire local qui contient les fichiers à télécharger.|
**FileName**|Spécifie un filtre de nom pour sélectionner des fichiers obéissant à un schéma de nom spécifié. Par exemple, MySheet*.xls * inclut des fichiers tels que MySheet001.xls et MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Spécifie une plage de temps pour appliquer un filtre. Les fichiers modifiés après *TimeRangeFrom* et avant *TimeRangeTo* sont inclus.|


> [AZURE.NOTE] Les informations d’identification **AzureStorageConnection** doivent être correctes et **BlobContainer** doit exister avant que le transfert soit tenté.

## Télécharger les données depuis le stockage d’objets blobs Azure

Pour télécharger des données depuis le stockage d’objets blobs Azure vers le stockage sur site avec SSIS, utilisez une instance de la [tâche de téléchargement d’objets blobs Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##Scénarios SSIS-Azure plus élaborés
Nous remarquons ici que le pack de fonctionnalités SSIS permet de gérer des flux de données plus complexes en regroupant plusieurs tâches. Par exemple, les données d’objets blobs peuvent alimenter directement un cluster HDInsight dont le résultat peut être de nouveau téléchargé vers un objet blob, puis dans un espace de stockage local. SSIS peut exécuter des tâches Hive et Pig sur un cluster HDInsight à l’aide de connecteurs SSIS supplémentaires :

- Pour exécuter un script Hive sur un cluster Azure HDInsight avec SSIS, utilisez la [tâche Azure HDInsight Hive](https://msdn.microsoft.com/library/mt146771.aspx).
- Pour exécuter un script Pig sur un cluster Azure HDInsight avec SSIS, utilisez la [tâche Azure HDInsight Pig](https://msdn.microsoft.com/library/mt146781.aspx).

<!---HONumber=AcomDC_0921_2016-->