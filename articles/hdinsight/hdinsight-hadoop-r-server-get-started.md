<properties
   pageTitle="Prise en main de R Server sur HDInsight (version préliminaire) | Azure"
   description="Apprenez à créer un Apache Spark sur un cluster HDInsight (Hadoop) incluant R Server (version préliminaire), puis à envoyer un script R sur le cluster."
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
   ms.date="08/19/2016"
   ms.author="jeffstok"
/>

# Commencer à utiliser R Server sur HDInsight (version préliminaire)

L’offre de niveau Premium pour HDInsight inclut R Server dans votre cluster HDInsight (version préliminaire). Cela permet aux scripts R d’utiliser MapReduce et Spark pour exécuter des calculs distribués. Dans ce document, vous allez apprendre à créer un R Server sur HDInsight, puis à exécuter un script R illustrant l’utilisation de Spark pour effectuer des calculs R distribués.

![Diagramme du flux de travail pour ce document](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Conditions préalables

* __Abonnement Azure__ : avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations, voir comment [obtenir la version d’évaluation gratuite d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* __Client Secure Shell (SSH)__ : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Les systèmes Linux, Unix et OS X fournissent un client SSH accessible via la commande `ssh`. Pour les systèmes Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * __Clés SSH (facultatives)__ : vous pouvez sécuriser le compte SSH utilisé pour la connexion au cluster à l’aide d’un mot de passe ou d’une clé publique. Le recours à un mot de passe est plus facile et vous permet de commencer sans avoir à créer une paire de clés publique/privée. Toutefois, l’utilisation d’une clé est plus sûre.
    
        Les étapes décrites dans ce document supposent que vous utilisez un mot de passe. Pour plus d’informations sur la création et l’utilisation de clés SSH avec HDInsight, voir les documents suivants :
        
        * [Utilisation de SSH avec HDInsight à partir de clients Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Utilisation de SSH avec HDInsight à partir de clients Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Création du cluster

> [AZURE.NOTE] Les étapes décrites dans ce document expliquent comment créer R Server sur HDInsight à l’aide des informations de configuration de base. Pour d’autres paramètres de configuration du cluster (par exemple, l’ajout de comptes de stockage à l’aide d’Azure Virtual Network ou la création d’un metastore pour Hive), voir [Créer des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez __NOUVEAU__, __Données et analyse__, puis __HDInsight__.

    ![Image de la création d’un cluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Dans le champ __Nom du cluster__, entrez un nom pour le cluster. Si vous avez plusieurs abonnements Azure, utilisez l’entrée __Abonnement__ pour sélectionner celui que vous voulez utiliser.

    ![Sélections de nom et d’abonnement de cluster](./media/hdinsight-getting-started-with-r/clustername.png)

4. Choisissez __Sélectionner un type de cluster__. Dans le panneau __Type de cluster__, sélectionnez les options suivantes :

    * __Type de cluster__ : R Server sur Spark
    
    * __Niveau de cluster__ : Premium

    Conservez les valeurs par défaut des autres options, puis utilisez le bouton __Sélectionner__ pour enregistrer le type de cluster.
    
    ![Capture d’écran du panneau Type de cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] Vous pouvez également ajouter R Server à d’autres types de clusters HDInsight (par exemple, Hadoop ou HBase,) en sélectionnant le type de cluster, puis __Premium__.

5. Cliquez sur **Groupe de ressources** pour afficher la liste des groupes de ressources existants, puis sélectionnez celui dans lequel créer le cluster. Vous pouvez également sélectionner **Créer un nouveau**, puis saisir le nom du nouveau groupe de ressources. Une coche verte s’affiche pour indiquer si le nouveau nom de groupe est disponible.

    > [AZURE.NOTE] Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.
    
    Pour enregistrer le groupe de ressources, utilisez le bouton __Sélectionner__.

6. Sélectionnez **Informations d’identification**, puis saisissez une valeur dans les champs **Nom de connexion au cluster** et **Mot de passe de connexion au cluster**.

    Entrez un __nom d’utilisateur SSH__. Le protocole SSH est utilisé pour se connecter à distance au cluster à l’aide d’un client __Secure Shell (SSH)__. Vous pouvez spécifier l’utilisateur SSH dans cette boîte de dialogue ou après la création du cluster (onglet Configuration du cluster). R Server est configuré pour attendre un __nom d’utilisateur SSH__ de l’« utilisateur distant ». Si vous utilisez un autre nom d’utilisateur, vous devrez effectuer une étape supplémentaire après la création du cluster.
    
    ![Panneau Informations d’identification](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    __Type d’authentification SSH__ : sélectionnez __MOT DE PASSE__ comme type d’authentification, sauf si vous préférez utiliser une clé publique. Vous aurez besoin d’une paire de clés publique/privée si vous souhaitez accéder au R Server sur le cluster via un client distant (par exemple, RTV, RStudio ou un autre IDE de bureau).

	Pour créer et utiliser une paire de clés publique/privée, sélectionnez « CLÉ PUBLIQUE » et procédez comme suit. Ces instructions supposent que vous avez Cygwin avec ssh-keygen ou équivalent installé.

	-    Générez une paire de clés publique/privée à partir de l’invite de commandes sur votre ordinateur portable :
	  
		    ssh-keygen -t rsa -b 2048 –f <private-key-filename>
      
    -    Cela va créer un fichier de clé privée et un fichier de clé publique sous le nom <nom de fichier de la clé privée>.pub, par exemple, davec et davec.pub. Spécifiez ensuite le fichier de clé publique (*.pub) lors de l’attribution des informations d’identification du cluster HDI :
      
		![Panneau Informations d’identification](./media/hdinsight-getting-started-with-r/publickeyfile.png)
      
	-    Modifier les autorisations sur le fichier de clé privée sur votre ordinateur portable
      
			chmod 600 <private-key-filename>
      
	-    Utilisez le fichier de clé privée avec SSH pour la connexion à distance, par exemple
	  
			ssh –i <private-key-filename> remoteuser@<hostname public ip>
      
	  ou dans le cadre de la définition de votre contexte de calcul Hadoop Spark pour R Server sur le client (voir la section Using Microsoft R Server as a Hadoop Client in the [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Utilisation de Microsoft R Server en tant que client Hadoop pour la création d’un contexte de calcul pour Spark) du guide en ligne de mise en route [RevoScaleR Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).

7. Choisissez **Source de données** pour sélectionner une source de données pour le cluster. Optez pour un compte de stockage existant en choisissant __Sélectionner un compte de stockage__, puis en spécifiant le compte, ou créez un compte à l’aide du lien __Nouveau__ dans la section __Sélectionner un compte de stockage__.

    Si vous sélectionnez __Nouveau__, vous devez entrer un nom pour le nouveau compte de stockage. Une coche verte s’affiche si le nom est accepté.

    Le __Conteneur par défaut__ correspond au nom du cluster. Conservez cette valeur.
    
    Choisissez __Emplacement__ pour sélectionner la région dans laquelle créer le compte de stockage.
    
    > [AZURE.IMPORTANT] La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même zone géographique.

    Pour enregistrer la configuration de la source de données, utilisez le bouton **Sélectionner**.
    
    ![Panneau Source de données](./media/hdinsight-getting-started-with-r/datastore.png)

8. Sélectionnez **Niveaux de tarification du nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Sauf si vous savez que vous aurez besoin d’un cluster plus grand, conservez le nombre de nœuds Worker par défaut `4`. Le coût estimé du cluster s'affiche dans le panneau.

	> [AZURE.NOTE] Si nécessaire, vous pouvez redimensionner votre cluster ultérieurement via le portail (Cluster -> Paramètres -> Cluster de mise à l’échelle) pour augmenter ou diminuer le nombre de nœuds de travail. Cela peut être utile pour faire fonctionner le cluster au ralenti lorsqu’il n’est pas utilisé, ou pour ajouter de la capacité afin de répondre aux besoins des tâches plus intensives.

	Certains facteurs à prendre en compte lors du dimensionnement de votre cluster, les nœuds de données et le nœud de périmètre sont les suivants :
   
    - Les performances des analyses du R Server distribué sur Spark sont proportionnelles au nombre de nœuds Worker lorsque les données sont volumineuses.
    - Les performances des analyses du R Server sont linéaires quant à la taille des données analysées. Par exemple :
        - Pour les volumes de données petits à moyens, les performances sont idéales lorsqu’elles sont analysées dans un contexte de calcul local à la périphérie du nœud. Pour plus d’informations sur les scénarios de fonctionnement des contextes de calcul locaux et Spark, consultez la rubrique Options de contexte de calcul pour R Server sur HDInsight.<br>
        - Si vous vous connectez au nœud de périphérique et y exécutez votre script R toutes les fonctions à l’exception des fonctions rx ScaleR sont exécutées <strong>localement</strong> sur la périphérie du nœud. La mémoire et le nombre de cœurs de la périphérie du nœud doivent donc être dimensionnés en conséquence. Ces conditions s’appliquent si vous utilisez R Server sur HDI comme contexte de calcul à distance à partir de votre ordinateur portable.
    
    ![Panneau Niveaux de tarification du nœud](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Pour enregistrer la configuration de la tarification du nœud, utilisez le bouton **Sélectionner**.
    
9. Dans le panneau **Nouveau cluster HDInsight**, assurez-vous que l’option **Épingler au tableau d’accueil** est activée, puis sélectionnez **Créer**. Le cluster est créé et la vignette correspondante ajoutée au tableau d’accueil de votre portail Azure. L’icône indique que le cluster est en cours de création et sera modifiée pour représenter l’icône HDInsight une fois la création terminée.

    | En cours de création | Création terminée |
    | ------------------ | --------------------- |
    | ![Indicateur de création sur le tableau d’accueil](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Vignette de cluster créé](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus de création.

## Se connecter au nœud de périmètre R Server

Connectez-vous au nœud de périmètre R Server du cluster HDInsight à l’aide de SSH :

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net
    
> [AZURE.NOTE] Vous pouvez également trouver l’adresse `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` dans le Portail Azure en sélectionnant votre cluster, puis __Tous les paramètres__, __Applications__ et __RServer__. Cela a pour effet d’afficher les informations relatives au point de terminaison SSH pour le nœud de périmètre.
>
> ![Image du point de terminaison SSH pour le nœud de périmètre](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple : `ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`.
    
Pour plus d’informations sur l’utilisation de SSH avec HDInsight sous Linux, consultez les articles suivants :

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Une fois connecté, une invite semblable à la suivante s’affiche.

    username@ed00-myrser:~$

## Utiliser la console R

1. Dans la session SSH, utilisez la commande suivante pour démarrer la console R.

        R
    
    Vous devez voir des informations similaires à ce qui suit.
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. Vous pouvez entrer un code R dans l’invite `>`. R Server inclut des packages qui vous permettent d’interagir facilement avec Hadoop et d’exécuter des calculs distribués. Par exemple, utilisez la commande suivante pour afficher la racine du système de fichiers par défaut du cluster HDInsight.

        rxHadoopListFiles("/")
    
    Vous pouvez également utiliser l’adressage de style WASB.
    
        rxHadoopListFiles("wasbs:///")

## À l’aide de R Server sur HDI à partir d’une instance distante de Microsoft R serveur ou Microsoft R Client

Conformément à la section ci-dessus concernant l’utilisation de paires de clés publiques/privées pour accéder au cluster, il est possible de configurer l’accès au contexte de calcul HDI Hadoop Spark à partir d’une instance distante de Microsoft R Server ou Microsoft R Client exécutée sur un ordinateur de bureau ou un ordinateur portable (voir la section Using Microsoft R Server as a Hadoop Client in the [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Utilisation de Microsoft R Server en tant que client Hadoop pour la création d’un contexte de calcul pour Spark) du [guide en ligne de mise en route RevoScaleR Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)). Pour cela, vous devez spécifier les options suivantes lorsque vous définissez le contexte de calcul RxSpark sur votre ordinateur portable : hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches et sshProfileScript. Par exemple :

    
    myNameNode <- "default"
    myPort <- 0 
 
    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key
 
    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")
 
    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

    
 
## Utiliser un contexte de calcul

Un contexte de calcul vous permet de contrôler si calcul sera effectué localement sur le nœud de périmètre, ou si elle sera distribuée entre les nœuds du cluster HDInsight.
        
1. Dans la console R, utilisez ce qui suit pour charger les exemples de données dans le stockage par défaut pour HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Ensuite, créez des données et définissez deux sources de données exploitables.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()
        # Create info list for the airline data
        airlineColInfo <- list(
            DAY_OF_WEEK = list(type = "factor"),
            ORIGIN = list(type = "factor"),
            DEST = list(type = "factor"),
            DEP_TIME = list(type = "integer"),
            ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Exécutez une régression logistique sur les données à l’aide du contexte de calcul local.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)

    Vous devez voir la sortie qui se termine par des lignes similaires à ce qui suit.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
        (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
        ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
        ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
        ......
        DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
        DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
        DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
        DEST=BPT         Dropped    Dropped Dropped  Dropped
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

        Condition number of final variance-covariance matrix: 11904202
        Number of iterations: 7

4. Ensuite, exécutez la même régression logistique en utilisant le contexte Spark. Le contexte Spark répartit le traitement sur tous les nœuds de travail dans le cluster HDInsight.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark()
        # Set the compute context 
        rxSetComputeContext(mySparkCluster)
        # Run a logistic regression 
        system.time(  
            modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        # Display a summary
        summary(modelSpark)

    > [AZURE.NOTE] Vous pouvez également utiliser MapReduce pour répartir le calcul sur des nœuds de cluster. Pour plus d’informations sur le contexte de calcul, consultez [Options de contexte de calcul pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## Distribuer le code R à plusieurs nœuds

Avec R Server, vous pouvez facilement utiliser du code R existant pour l’exécuter sur plusieurs nœuds du cluster à l’aide de `rxExec`. Cela est utile lors d’un balayage paramétrique ou de simulations. L’exemple suivant montre comment utiliser `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Si vous utilisez encore le contexte Spark ou MapReduce, la valeur du nom de nœud (nodename) est renvoyée pour les nœuds Worker sur lesquels le code (`Sys.info()["nodename"]`) est exécuté. Par exemple, sur un cluster à quatre nœuds, vous pouvez recevoir une sortie similaire à ce qui suit.

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

## Installer des packages R

Si vous souhaitez installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser `install.packages()` directement à partir de la console R quand vous êtes connecté au nœud de périphérie par le biais de SSH. Toutefois, si vous avez besoin installer des packages R sur les nœuds de travail du cluster, vous devez utiliser une action de script.

Les actions de script sont des scripts Bash permettant de modifier la configuration du cluster HDInsight ou d’installer des logiciels supplémentaires. Dans ce cas, il s’agit d’installer des packages R supplémentaires. Pour installer des packages supplémentaires à l’aide d’une action de Script, procédez comme suit.

> [AZURE.IMPORTANT] L’utilisation d’actions de script pour installer des packages R supplémentaires n’est possible qu’après la création du cluster. Il ne faut pas y recourir lors de la création du cluster, car le script a besoin que R Server soit entièrement installé et configuré.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre R Server sur le cluster HDInsight.

2. Dans le panneau du cluster, sélectionnez __Tous les paramètres__, puis __Actions de script__. Dans le panneau __Actions de script__, sélectionnez __Envoyer__ pour soumettre une nouvelle action de script.

    ![Image du panneau Actions de script](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. Dans le panneau __Envoyer une action de script__, entrez les informations suivantes.

  - __Nom__ : un nom convivial pour identifier ce script
  - __URI de script Bash__ : `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`
  - __En-tête__ : cette valeur doit être __désactivée__
  - __Worker__ : cette valeur doit être __activée__
  - __Zookeeper__ : cette valeur doit être __désactivée__
  - __Paramètres__ : les packages R à installer. Par exemple, `bitops stringr arules`
  - __Conservez cette action de script...__ : cette option doit être __activée__.

    > [AZURE.NOTE] 1. Par défaut, tous les packages R sont installés à partir d’un instantané du référentiel Microsoft MRAN cohérent avec la version du serveur R qui a été installée. Si vous souhaitez installer des versions plus récentes des packages, il y a des risques d’incompatibilité. Cependant, cela est possible en spécifiant `useCRAN` en tant que premier élément de la liste de packages (par exemple, `useCRAN bitops, stringr, arules`).
    > 2. Certains packages R nécessiteront des bibliothèques de système Linux supplémentaires. Pour plus de commodité, nous avons préinstallé les dépendances requises par les 100 premiers packages R les plus populaires. Toutefois, si les packages R que vous installez nécessitent des bibliothèques supplémentaires, vous devez télécharger le script de base utilisé ici et ajouter des étapes pour installer les bibliothèques système. Vous devez ensuite charger le script modifié dans un conteneur d’objets blob publics dans Azure Storage, puis utiliser le script modifié pour installer les packages. Pour plus d’informations sur le développement d’actions de script, consultez la section [Développer des actions de script](hdinsight-hadoop-script-actions-linux.md).

    ![Ajout d’une action de script](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. Sélectionnez __Créer__ pour exécuter le script. Une fois le script exécuté, les packages R sont disponibles sur tous les nœuds de travail.
    
## Étapes suivantes

Maintenant que vous savez comment créer un cluster HDInsight incluant R Server et connaissez les rudiments de l’utilisation de la console R à partir d’une session SSH, utilisez les informations suivantes pour découvrir d’autres manière d’utiliser R Server sur HDInsight.

- [Ajouter RStudio Server à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Options de contexte de calcul pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Options d’Azure Storage pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

### Modèles Azure Resource Manager

Si vous êtes intéressé par l’automatisation de la création de R Server sur HDInsight à l’aide de modèles Azure Resource Manager, voir les exemples de modèles suivants.

* [Créer un R Server sur un cluster HDInsight à l’aide d’une clé publique SSH](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Créer un R Server sur un cluster HDInsight à l’aide d’un mot de passe SSH](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Les deux modèles créent un cluster HDInsight et un compte de stockage associé, et peuvent être utilisés à partir de l’interface de ligne de commande Azure, d’Azure PowerShell ou du portail Azure.

Pour obtenir des informations générales sur l’utilisation des modèles Azure Resource Manager, consultez la section [Création de clusters Hadoop basés sur Linux dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0921_2016-->