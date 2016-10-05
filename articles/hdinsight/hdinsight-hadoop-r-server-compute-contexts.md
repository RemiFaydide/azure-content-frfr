<properties
   pageTitle="Options de contexte de calcul pour R Server sur HDInsight (version préliminaire) | Microsoft Azure"
   description="Découvrez les différentes options de contexte de calcul disponibles pour les utilisateurs avec R Server sur HDInsight (version préliminaire)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="07/21/2016"
   ms.author="jeffstok"
/>

# Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)

Microsoft R Server sur Azure HDInsight (version préliminaire) fournit les dernières fonctionnalités d’analyse basées sur R. Il utilise les données stockées dans HDFS dans un conteneur de votre compte de stockage [d’objets blob Azure](../storage/storage-introduction.md "Stockage d'objets blob Azure") ou le système de fichiers Linux local. Puisque R Server repose sur une version R open source, les applications basées sur R que vous créez peuvent tirer parti des plus de 8 000 packages R open source. Ils peuvent également tirer parti des routines dans [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), le package d’analyse Big Data de Microsoft fourni avec R Server.

Le nœud de périmètre d’un cluster Premium fournit un lieu d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud de périmètre vous permet d’exécuter des fonctions distribuées parallélisées de ScaleR sur les cœurs du serveur de nœud de périmètre. Vous avez également la possibilité de les exécuter sur les nœuds du cluster via l’utilisation des contextes de calcul Hadoop Map Reduce ou Spark de ScaleR.

## Contextes de calcul pour un nœud de périmètre

En général, un script R exécuté dans R Server sur le nœud de périmètre s’exécute au sein de l’interpréteur R sur ce nœud. L’exception est ces étapes qui appellent une fonction ScaleR. Les appels ScaleR s’exécutent dans un environnement de calcul déterminé par la façon dont vous définissez le contexte de calcul ScaleR. Les valeurs possibles du contexte de calcul lors de l’exécution de votre script R à partir d’un nœud de périmètre sont les valeurs local (local sequential), localpar (local parallel), Map Reduce et Spark.

Les options local et localpar diffèrent uniquement par la façon dont les appels rxExec sont exécutés. Elles exécutent toutes les deux d’autres appels de fonction rx de manière parallèle entre tous les cœurs disponibles sauf indication contraire à l’aide de l’option numCoresToUse ScaleR, par exemple, rxOptions (numCoresToUse=6). La liste suivante récapitule les différentes options de contexte de calcul

| Contexte de calcul | Définition | Contexte d’exécution |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local sequential | rxSetComputeContext(‘local’) | Exécution parallélisée sur les cœurs du serveur de nœud de périmètre, à l’exception des appels rxExec qui sont exécutés en série |
| Local parallel | rxSetComputeContext(‘localpar’) | Exécution parallélisée sur les cœurs du serveur de nœud de périmètre |
| Spark | RxSpark() | Exécution distribuée parallélisée via Spark sur les nœuds du cluster HDI |
| Map Reduce | RxHadoopMR() | Exécution distribuée parallélisée via Map Reduce sur les nœuds du cluster HDI |


En supposant que vous souhaitiez une exécution parallélisée à des fins de performances, trois options sont disponibles. L’option que vous choisissez dépend de la nature de votre travail d’analyse, ainsi que de la taille et de l’emplacement de vos données.

## Conseils pour le choix d’un contexte de calcul

Actuellement, il n’existe aucune formule qui vous indique quel contexte de calcul utiliser. Toutefois, il existe quelques principes qui peuvent vous aider à faire le bon choix, ou au moins vous aider à affiner vos choix avant d’exécuter un test d’évaluation. Ces principes sont les suivants :

1.	Le système de fichiers Linux local est plus rapide que HDFS.
2.	Les analyses répétées sont plus rapides si les données sont locales et au format XDF.
3.	Il est préférable de diffuser de petites quantités de données à partir d’une source de données texte. Si la quantité de données est plus importante, convertissez-les au format XDF avant l’analyse.
4.	La surcharge de copie ou de diffusion des données vers le nœud de périmètre pour l’analyse devient ingérable pour les quantités de données très importantes.
5.	Spark est plus rapide que Map Reduce pour l’analyse dans Hadoop.

Une fois ces principes connus, il existe quelques règles générales que vous pouvez suivre pour sélectionner un contexte de calcul :

### Local

- Si la quantité de données à analyser est peu importante et ne nécessite pas d’analyses répétées, diffusez-les directement dans la routine d’analyse et utilisez local ou localpar.
- Si la quantité de données à analyser est peu ou moyennement importante et nécessite des analyses répétées, copiez-les dans le système de fichiers local, importez-les au format XDF et analysez-les via local ou localpar.

### Hadoop Spark

- Si la quantité de données à analyser est importante, importez-les au format XDF dans HDFS (sauf si le stockage est un problème), puis analysez-les via Spark.

### Hadoop Map Reduce

- Utilisez-le uniquement si vous rencontrez un problème avec l’utilisation du contexte de calcul Spark car cela affecte généralement les performanes.

## Aide en ligne sur rxSetComputeContext

Pour obtenir plus d’informations et des exemples de contextes de calcul ScaleR, consultez l’aide en ligne dans R sur la méthode rxSetComputeContext, par exemple :

    > ?rxSetComputeContext

Vous pouvez également vous reporter à « ScaleR Distributed Computing Guide » (Guide des traitements distribués ScaleR) disponible dans la bibliothèque [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server sur MSDN").


## Étapes suivantes

Dans cet article, vous avez appris à créer un cluster HDInsight qui inclut R Server. Vous avez également appris les principes de base de l’utilisation de la console R à partir d’une session SSH. Maintenant, vous pouvez consulter les articles suivants pour découvrir d’autres façons de travailler avec R Server sur HDInsight :

- [Vue d’ensemble : R Server sur HDInsight (version préliminaire)](hdinsight-hadoop-r-server-overview.md)
- [Commencer à utiliser R Server sur HDInsight (version préliminaire)](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter RStudio Server à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Options d’Azure Storage pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0921_2016-->