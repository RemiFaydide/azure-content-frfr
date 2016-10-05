<properties
   	pageTitle="Créer des clusters Hadoop, HBase ou Storm sur Linux dans HDInsight à l’aide de l’interface de ligne de commande Azure inter-plateformes | Microsoft Azure"
   	description="Apprenez à créer des clusters HDInsight sous Linux à l’aide de l’interface de ligne de commande Azure inter-plateformes, de modèles Azure Resource Manager et de l’API REST Azure. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou Storm) ou utiliser des scripts pour installer des composants personnalisés."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="09/20/2016"
   	ms.author="larryfr"/>

#Création de clusters basés sur Linux dans HDInsight à l’aide de l’interface CLI Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

L'interface CLI Azure est un utilitaire de ligne de commande interplateforme qui vous permet de gérer les services Azure. Il peut être utilisé avec les modèles de gestion des ressources Azure pour créer un cluster HDInsight, et avec les comptes de stockage et autres services associés.

Les modèles Azure Resource Management sont des documents JSON qui décrivent un __groupe de ressources__ et toutes les ressources qu’il contient (par exemple, HDInsight). Cette approche à base de modèles vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un modèle. Il vous permet également de gérer les modifications apportées au groupe dans son ensemble par le biais de __déploiements__, qui appliquent les modifications à l’intégralité du groupe.

Les étapes de ce document décrivent le processus de création d’un cluster HDInsight à l’aide de la CLI Azure et d’un modèle :

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le nombre par défaut de nœuds worker (4) pour un cluster HDInsight. Si vous envisagez d’utiliser plus de 32 nœuds worker (lors de la création ou de la mise à l’échelle du cluster), vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Composants requis

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Interface de ligne de commande Azure__. Les étapes décrites dans ce document ont été testées pour la dernière fois avec la version 0.10.1 de l’interface de ligne de commande Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Connexion à votre abonnement Azure

Suivez les étapes décrites dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md) et connectez-vous à votre abonnement à l’aide de la méthode __login__.

##Créer un cluster

La procédure suivante doit être effectuée à partir d’une session d’invite de commande, shell ou terminal après l’installation et la configuration de l’interface CLI Azure.

1. Utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure :

        azure login

    Vous êtes invité à fournir votre nom et mot de passe. Si vous possédez plusieurs abonnements Azure, utilisez `azure account set <subscriptionname>` pour définir l’abonnement que les commandes CLI Azure doivent utiliser.

3. Passez en mode Gestionnaire de ressources Azure en exécutant la commande suivante :

        azure config mode arm

4. Créez un groupe de ressources. Ce groupe de ressources contiendra le cluster HDInsight et le compte de stockage associé.

        azure group create groupname location
        
    * Remplacez __groupname__ par un nom unique pour le groupe.
    * Remplacez __location__ par la région géographique dans laquelle vous souhaitez créer le groupe.
    
        Pour obtenir la liste des emplacements valides, utilisez la commande `azure location list`, puis un des emplacements de la colonne __Nom__.

5. Créez un compte de stockage. Ce compte de stockage sera utilisé comme espace de stockage par défaut pour le cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Remplacez __groupname__ par le nom du groupe créé à l’étape précédente.
     * Remplacez __location__ par le même emplacement que celui utilisé à l’étape précédente.
     * Remplacez __storagename__ par un nom de compte de stockage unique.
     
     > [AZURE.NOTE] Pour plus d’informations sur les paramètres utilisés dans cette commande, utilisez `azure storage account create -h` pour afficher l’aide relative à cette commande.

5. Récupérez la clé utilisée pour accéder au compte de stockage.

        azure storage account keys list -g groupname storagename
        
    * Remplacez __groupname__ par le nom du groupe de ressources.
    * Remplacez __storagename__ par le nom du compte de stockage.
    
    Dans les données renvoyées, enregistrez la valeur de __clé__ pour __key1__.

6. Création d’un cluster HDInsight

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Remplacez __groupname__ par le nom du groupe de ressources.
    * Remplacez __location__ par le même emplacement que celui utilisé lors des étapes précédentes.
    * Remplacez __storagename__ par le nom du compte de stockage.
    * Remplacez __storagekey__ par la clé obtenue à l’étape précédente.
    * Pour le paramètre `--defaultStorageContainer`, utilisez le même nom que celui utilisé pour le cluster.
    * Remplacez __admin__ et __httppassword__ par le nom et le mot de passe à utiliser lors de l’accès au cluster via HTTPS.
    * Remplacez __sshuser__ et __sshuserpassword__ par le nom d’utilisateur et le mot de passe à utiliser lors de l’accès au cluster via SSH.

    Le processus de création de cluster peut prendre plusieurs minutes. En règle générale, il dure environ 15 minutes.

##Étapes suivantes

Vous avez créé un cluster HDInsight à l’aide de l’interface de ligne de commande Azure. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

###Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0921_2016-->