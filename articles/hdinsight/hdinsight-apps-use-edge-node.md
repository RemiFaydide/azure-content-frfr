<properties
	pageTitle="Utiliser des nœuds de périmètre vides dans HDInsight | Microsoft Azure"
	description="Découvrez comment ajouter un nœud de périmètre vide au cluster HDInsight, qui peut être utilisé en tant que client, et comment tester/héberger vos applications HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="jgao"/>

# Utiliser des nœuds de périmètre vides dans HDInsight

Découvrez comment ajouter un nœud de périmètre vide à un cluster HDInsight Linux. Un nœud de périmètre vide est une machine virtuelle Linux avec les mêmes outils clients installés et configurés comme dans les nœuds principaux, mais sans exécution de services Hadoop. Vous pouvez utiliser le nœud de périmètre pour accéder au cluster, tester vos applications clientes et héberger vos applications clientes.

Vous pouvez ajouter un nœud de périmètre vide à un cluster HDInsight existant et à un nouveau cluster, lorsque vous créez le cluster. L’ajout d’un nœud de périmètre vide est effectué à l’aide du modèle Azure Resource Manager. L’exemple suivant montre comment procéder à l’aide d’un modèle :

    "resources": [
		{
			"name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
			"type": "Microsoft.HDInsight/clusters/applications",
			"apiVersion": "[variables('clusterApiVersion')]",
			"properties": {
				"marketPlaceIdentifier": "EmptyNode",
				"computeProfile": {
					"roles": [{
						"name": "edgenode",
						"targetInstanceCount": 1,
						"hardwareProfile": {
							"vmSize": "[parameters('edgeNodeSize')]"
						}
					}]
				},
				"installScriptActions": [{
					"name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
					"uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
					"roles": ["edgenode"]
				}],
				"uninstallScriptActions": [],
				"httpsEndpoints": [],
				"applicationType": "CustomApplication"
			}
		}
	],

Comme indiqué dans l’exemple, vous pouvez éventuellement appeler une [action de script](hdinsight-hadoop-customize-cluster-linux.md) pour effectuer une configuration supplémentaire, telle que l’installation [d’Apache Hue](hdinsight-hadoop-hue-linux.md) dans le nœud de périmètre.

Après avoir créé un nœud de périmètre, vous pouvez vous connecter au nœud de périmètre à l’aide de SSH et exécuter les outils clients pour accéder au cluster Hadoop dans HDInsight.

## Ajouter un nœud de périmètre à un cluster existant

Dans cette section, vous allez utiliser un modèle Resource Manager pour ajouter un nœud de périmètre à un cluster HDInsight existant. Le modèle Resource Manager se trouve dans [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Le modèle Resource Manager appelle un script d’action de script se trouvant à l’adresse https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Le script n’effectue aucune action. Cela sert à illustrer l’appel d’action de script à partir d’un modèle Resource Manager.

**Pour ajouter un nœud de périmètre vide à un cluster existant**

1. Créez un cluster HDInsight si vous n’avez pas encore. Consultez [Didacticiel Hadoop : prise en main de Hadoop sous Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Azure Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurez les propriétés suivantes :

	- CLUSTERNAME : entrez le nom d’un cluster HDInsight existant.
	- EDGENODESIZE : sélectionnez une des tailles de machine virtuelle.
	- EDGENODEPREFIX : la valeur par défaut est **new**. À l’aide de la valeur par défaut, le nom du nœud de périmètre est **new-edgenode**. Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet à partir du modèle.


4. Cliquez sur **OK** pour enregistrer les modifications.
5. Dans **Groupe de ressources**, sélectionnez un groupe de ressources.
6. Cliquez sur **Consulter les termes et conditions**, puis sur **Achat**.
7. Sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.

## Ajouter un nœud de périmètre lors de la création d’un cluster

Dans cette section, vous allez utiliser un modèle Resource Manager pour créer un cluster HDInsight avec un nœud de périmètre. Le modèle Resource Manager se trouve dans un [conteneur de stockage d’objets blob Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json) public. Le modèle Resource Manager appelle un script d’action de script situé à l’adresse https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Le script n’effectue aucune action. Cela sert à illustrer l’appel d’action de script à partir d’un modèle Resource Manager.

**Pour ajouter un nœud de périmètre vide à un cluster existant**

1. Créez un cluster HDInsight si vous n’avez pas encore. Consultez [Didacticiel Hadoop : prise en main de Hadoop sous Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Azure Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurez les propriétés suivantes :
		
	- CLUSTERNAME : entrez un nom pour le nouveau cluster à créer.
	- CLUSTERLOGINUSERNAME : entrez le nom d’utilisateur Hadoop HTTP. Le nom par défaut est **admin**.
	- CLUSTERLOGINUSERNAME : entrez le mot de passe d’utilisateur Hadoop HTTP.
	- SSHUSERNAME : entrez le nom d’utilisateur SSH. Le nom par défaut est **sshuser**.
	- SSHPASSWORD : entrez le mot de passe d’utilisateur SSH.
	- LOCATION : entrez l’emplacement du nom du groupe de ressources, du cluster et du compte de stockage par défaut.
	- CLUSTERTYPE : la valeur par défaut est **hadoop**.
	- CLUSTERWORKERNODECOUNT : la valeur par défaut est 2.
	- EDGENODESIZE : sélectionnez une des tailles de machine virtuelle.
	- EDGENODEPREFIX : la valeur par défaut est **new**. À l’aide de la valeur par défaut, le nom du nœud de périmètre est **new-edgenode**. Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet à partir du modèle.

4. Cliquez sur **OK** pour enregistrer les modifications.
5. Dans **Groupe de ressources**, entrez un nouveau nom pour le groupe de ressources.
6. Cliquez sur **Consulter les termes et conditions**, puis sur **Achat**.
7. Sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.


## Accéder à un nœud de périmètre

Le point de terminaison ssh de nœud de périmètre est <EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22. Par exemple, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Le nœud de périmètre apparaît comme une application sur le portail Azure. Le portail vous donne les informations d’accès au nœud de périmètre à l’aide de SSH.

**Pour vérifier le point de terminaison SSH de nœud de périmètre**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HDInsight avec un nœud de périmètre.
3. Dans le panneau du cluster, cliquez sur **Applications**. Vous devriez voir le nœud de périmètre. Le nom par défaut est **new-edgenode**.
4. Cliquez sur le nœud de périmètre. Vous devriez voir le point de terminaison SSH.

**Pour utiliser Hive sur le nœud de périmètre**

5. Utilisez SSH pour vous connecter au nœud de périmètre. Consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Après vous être connecté au nœud de périmètre à l’aide de SSH, utilisez la commande suivante pour ouvrir la console Hive :

		hive
7. Exécutez la commande suivante pour afficher les tables Hive dans le cluster :

		show tables;

## Supprimer un nœud de périmètre

Vous pouvez supprimer un nœud de périmètre à partir du portail Azure.

**Pour accéder à un nœud de périmètre**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HDInsight avec un nœud de périmètre.
3. Dans le panneau du cluster, cliquez sur **Applications**. Vous devriez voir une liste des nœuds de périmètre.
4. Cliquez avec le bouton droit sur le nœud de périmètre à supprimer, puis cliquez sur **Supprimer**.
5. Cliquez sur **Yes** (Oui) pour confirmer.

## Étapes suivantes

Dans cet article, vous avez appris comment ajouter un nœud de périmètre et comment y accéder. Pour en savoir plus, consultez les articles suivants :

- [Installer des applications HDInsight](hdinsight-apps-install-applications.md) : découvrez comment installer une application HDInsight sur vos clusters.
- [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight inédites vers HDInsight.
- [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md) : découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
- [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
- [Création de clusters Hadoop basés sur Linux dans HDInsight à l’aide de modèles ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md) : apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.

<!---HONumber=AcomDC_0914_2016-->