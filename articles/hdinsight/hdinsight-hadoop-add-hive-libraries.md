<properties
pageTitle="Ajouter les bibliothèques Hive lors de la création de cluster HDInsight | Azure"
description="Découvrez comment ajouter des bibliothèques Hive (fichiers jar) à un cluster HDInsight pendant la création du cluster."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/20/2016"
ms.author="larryfr"/>

#Ajouter les bibliothèques Hive lors de la création de cluster HDInsight

Si vous avez des bibliothèques que vous utilisez fréquemment avec Hive sur HDInsight, ce document contient des informations sur l'utilisation d'une action de script pour précharger les bibliothèques lors de la création du cluster. Ainsi, les bibliothèques sont globalement disponibles dans Hive (inutile d'utiliser [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) pour les charger).

##Fonctionnement

Lorsque vous créez un cluster, vous pouvez éventuellement spécifier une action de script qui exécute un script sur les nœuds de cluster lors de leur création. Le script dans ce document accepte un seul paramètre, qui est un emplacement WASB qui contient les bibliothèques (stockées en tant que fichiers jar) qui seront préchargées.

Lors de la création du cluster, le script énumère les fichiers, les copie sur le répertoire `/usr/lib/customhivelibs/` sur le nœud principal et le nœud de travail, puis les ajoute à la propriété `hive.aux.jars.path` dans le fichier `core-site.xml`. Pour les clusters basés sur Linux, il met également à jour le fichier `hive-env.sh` avec l'emplacement des fichiers.

> [AZURE.NOTE] L’utilisation de l’action de script dans cet article rend les bibliothèques disponibles dans les scénarios suivants :
>
> * __HDInsight basé sur Linux__ : lorsque vous utilisez la __ligne de commande Hive__, __WebHCat__ (Templeton) et __HiveServer2__.
> * __HDInsight basé sur Windows__ : lorsque vous utilisez la __ligne de commande Hive__ et __WebHCat__ (Templeton).

##Le script

__Emplacement du script__

Pour les __clusters basés sur Linux__ : [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pour les __clusters basés sur Windows__ : [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Configuration requise__

* Les scripts doivent être appliqués à la fois aux __nœuds principaux__ et aux __nœuds de travail__.

* Les fichiers jar à installer doivent être stockés dans le stockage d'objets Blob Azure dans un __seul conteneur__.

* Le compte de stockage contenant la bibliothèque de fichiers jar __doit__ être lié au cluster HDInsight lors de la création. Cela peut se faire de deux façons :

    * En étant dans un conteneur sur le compte de stockage par défaut pour le cluster.
    
    * En étant dans un conteneur sur un conteneur de stockage lié. Par exemple, dans le portail vous pouvez utiliser __Configuration facultative__, __Comptes de stockage liés__ pour ajouter un stockage supplémentaire.

* Le chemin WASB vers le conteneur doit être spécifié en tant que paramètre à l'action de script. Par exemple, si les fichiers jar sont stockés dans un conteneur nommé __libs__ sur un compte de stockage nommé __mystorage__, le paramètre serait \_\_wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Ce document suppose que vous avez déjà créé un compte de stockage, un conteneur d'objets blob et que vous avez déjà téléchargé les fichiers vers ceux-ci.
    >
    > Si vous n'avez pas créé de compte de stockage, vous pouvez le faire via le [portail Azure](https://portal.azure.com). Vous pouvez ensuite utiliser un utilitaire tel que [Azure Storage Explorer](http://storageexplorer.com/) pour créer un conteneur dans le compte et y télécharger des fichiers.

##Créer un cluster à l'aide du script

> [AZURE.NOTE] Les étapes suivantes créent un cluster HDInsight sous Linux. Pour créer un cluster basé sur Windows, sélectionnez __Windows__ comme système d'exploitation du cluster lors de sa création et utilisez le script Windows (PowerShell) plutôt que le script Bash.
> 
> Vous pouvez également utiliser Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight pour créer un cluster à l’aide de ce script. Pour plus d’informations sur ces méthodes, consultez [Personnaliser des clusters HDInsight à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais n’achevez pas l’approvisionnement.

2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations comme spécifié ci-dessous :

    * __NAME__ : saisissez un nom convivial pour l’action de script.
    * __SCRIPT URI__ : https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __HEAD__ : cochez cette option.
    * __WORKER__ : cochez cette option.
    * __ZOOKEEPER__ : laissez ce champ vide.
    * __PARAMETERS__ : entrez l'adresse WASB du compte de stockage et du conteneur qui contiennent les fichiers jar. Par exemple, \_\_wasbs://libs@mystorage.blob.core.windows.net/__.

3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration.

4. Dans le panneau **Configuration facultative**, sélectionnez __Comptes de stockage liés__ et sélectionnez le lien __Ajouter une clé de stockage__. Sélectionnez le compte de stockage qui contient les fichiers jar et utilisez les boutons de __sélection__ pour enregistrer les paramètres et revenir au panneau __Configuration facultative__.

5. Utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.

6. Continuez l’approvisionnement du cluster, comme indiqué dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Lorsque la création du cluster est terminée, vous pourrez utiliser les fichiers jar ajoutés via ce script à partir de Hive, sans avoir à utiliser l’instruction `ADD JAR`.

##Étapes suivantes

Dans ce document, vous avez appris à ajouter des bibliothèques Hive contenues dans les fichiers jar à un cluster HDInsight pendant la création du cluster. Pour plus d'informations sur l’utilisation de Hive, consultez la rubrique [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)

<!---HONumber=AcomDC_0921_2016-->