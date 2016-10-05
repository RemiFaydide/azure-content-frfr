<properties
	pageTitle="Installer RStudio avec R Server sur HDInsight (version préliminaire) | Microsoft Azure"
	description="Comment installer RStudio avec R Server sur HDInsight (version préliminaire)."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# Installation de RStudio avec R Server sur HDInsight (version préliminaire)

Il existe actuellement plusieurs environnements de développement intégré (IDE) pour R, notamment [R Tools pour Visual Studio](https://www.visualstudio.com/fr-FR/features/rtvs-vs.aspx) (RTVS), une famille d’outils pour ordinateur de bureau et serveur provenant de [RStudio](https://www.rstudio.com/products/rstudio-server/) dont la sortie a été récemment annoncée par Microsoft, ou [StatET](http://www.walware.de/goto/statet) basé sur Eclipse de Walware. Parmi les serveurs les plus répandus sur Linux, citons [RStudio Server](https://www.rstudio.com/products/rstudio-server/), qui fournit un environnement de développement intégré accessible par des clients distants depuis un navigateur. L’installation de RStudio Server sur le nœud de périmètre d’un cluster HDInsight Premium offre une expérience d’IDE complète pour le développement et l’exécution de scripts R avec R Server sur le cluster, et peut être considérablement plus productive que l’utilisation par défaut de la console R.

Dans cet article, vous allez apprendre à installer la version (gratuite) de la communauté de RStudio Server sur le nœud de périmètre d’un cluster à l’aide d’un script personnalisé. Si vous préférez la version commerciale Pro sous licence de RStudio Server, vous devez suivre les instructions d’installation de [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] La procédure décrite dans ce document nécessite l'installation de R Server sur un cluster HDInsight et ne fonctionnera pas correctement si vous utilisez un cluster HDInsight où R Server a été installé à l'aide de l'option [Installer une action de script R](hdinsight-hadoop-r-scripts-linux.md).

## Composants requis

* Un cluster Azure HDInsight avec R Server installé. Pour obtenir des instructions, consultez la page [Prise en main de R Server sur les clusters HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un client SSH. Pour les distributions Linux et Unix ou pour Macintosh OS X, la commande `ssh` est fournie avec le système d'exploitation. Pour Windows, nous vous recommandons [Cygwin](http://www.redhat.com/services/custom/cygwin/) avec [l’option OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU) ou [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).


## Installation de RStudio sur le cluster à l’aide d’un script personnalisé

1. Identifiez le nœud de périmètre du cluster. Concernant le cluster HDInsight avec R Server, voici la convention d’affectation de noms pour le nœud principal et le nœud de périmètre.

	* Nœud principal - `CLUSTERNAME-ssh.azurehdinsight.net`
	* Nœud de périmètre - `R-Server.CLUSTERNAME-ssh.azurehdinsight.net`

2. Utilisez SSH dans le nœud de périmètre du cluster à l’aide du modèle d’affectation de noms ci-dessus.
 
	* Si vous vous connectez à partir d’un client Linux, consultez la page [Se connecter à un cluster HDInsight sous Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
	* Si vous vous connectez à partir d’un client Windows, consultez la page [Se connecter à un cluster HDInsight sous Linux à l’aide de PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Une fois que vous êtes connecté, devenez un utilisateur racine sur le cluster. Dans la session SSH, utilisez la commande suivante :

		sudo su -

4. Téléchargez le script personnalisé pour installer RStudio. Utilisez la commande suivante :

		wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modifiez les autorisations sur le fichier de script personnalisé et exécutez le script. Utilisez les commandes suivantes.

		chmod 755 InstallRStudio.sh
		./InstallRStudio.sh

6. Si vous avez utilisé un mot de passe SSH en créant un cluster HDInsight avec R Server, vous pouvez ignorer cette étape et passer à la suivante. Si vous avez utilisé à la place une clé SSH pour créer le cluster, vous devez définir un mot de passe pour votre utilisateur SSH. Vous avez besoin de ce mot de passe pour vous connecter à RStudio. Exécutez les commandes suivantes : Lorsque vous êtes invité à entrer le **mot de passe Kerberos actuel**, appuyez simplement sur **ENTRÉE**. Notez que vous devez remplacer `USERNAME` par un utilisateur SSH pour votre cluster HDInsight.

		passwd USERNAME
		Current Kerberos password:
		New password:
		Retype new password:
		Current Kerberos password:
		
	Si votre mot de passe est défini correctement, vous devez voir un message comme suit.

		passwd: password updated successfully


	Quittez la session SSH.

7. Créez un tunnel SSH vers le cluster en mappant `localhost:8787` sur le cluster HDInsight à l’ordinateur client. Vous devez créer un tunnel SSH avant d’ouvrir une nouvelle session de navigateur.

	* Sur un client Linux ou un client Windows avec [Cygwin](http://www.redhat.com/services/custom/cygwin/), ouvrez une session de terminal et utilisez la commande suivante.

			ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
			
		Remplacez **USERNAME** par un utilisateur SSH de votre cluster HDInsight et **CLUSTERNAME** par le nom de votre cluster HDInsight. Vous pouvez également utiliser une clé SSH plutôt qu’un mot de passe en ajoutant `-i id_rsa_key`

	* Si vous utilisez un client Windows avec PuTTY

		1.  Ouvrez PuTTY et saisissez vos informations de connexion. Si vous ne connaissez pas PuTTY, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d'informations sur son utilisation avec HDInsight.
		2.  Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.
		3.  Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH**.

			* **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, **8787**.
			* **Destination** - La destination doit être mappée à l’ordinateur client local. Par exemple, **localhost:8787**.

			![Création d’un tunnel SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Création d’un tunnel SSH")

		4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.
		5. Lorsque vous y êtes invité, connectez-vous au serveur. Cela permettra d'établir une session SSH et d'activer le tunnel.

8. Ouvrez un navigateur web et entrez l’URL suivante basée sur le port que vous avez entré pour le tunnel.

		http://localhost:8787/ 

9. Vous devrez entrer le nom d’utilisateur SSH et le mot de passe pour vous connecter au cluster. Si vous avez utilisé une clé SSH en créant le cluster, vous devez entrer le mot de passe que vous avez créé à l’étape 5 ci-dessus.

	![Connexion à R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Création d’un tunnel SSH")

10. Pour tester si l’installation de RStudio s’est bien déroulée, vous pouvez exécuter un script de test qui exécute les travaux MapReduce et Spark basés sur R sur le cluster. Revenez à la console SSH et entrez les commandes suivantes pour télécharger le script de test à exécuter dans RStudio.

	* Si vous avez créé un cluster Hadoop avec R, utilisez cette commande :
		
			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

	* Si vous avez créé un cluster Spark avec R, utilisez cette commande :

			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. Dans RStudio, vous verrez le script de test que vous avez téléchargé. Double-cliquez sur le fichier pour l’ouvrir, sélectionnez le contenu du fichier, puis cliquez sur **Exécuter**. Vous devez voir la sortie dans le volet **Console**.
 
	![Test de l’installation](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Test de l’installation")

Vous pouvez également taper `source(testhdi.r)` ou `source(testhdi_spark.r)` pour exécuter le script.

## Voir aussi

- [Calcul d’options de contexte pour R Server sur les clusters HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Options d’Azure Storage pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-storage.md)


 

<!---HONumber=AcomDC_0921_2016-->