<properties
	pageTitle="Configurer une machine virtuelle SQL Server comme serveur IPython Notebook pour des analyses avancées | Microsoft Azure"
	description="Configurez une machine virtuelle pour la science des données avec SQL Server et un serveur IPython."
	services="machine-learning"
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
	ms.date="09/19/2016"
	ms.author="xibingao;bradsev" />

# Configurer une machine virtuelle Azure SQL Server comme serveur IPython Notebook pour des analyses avancées

Cette rubrique explique comment approvisionner et configurer une machine virtuelle SQL Server utilisable au sein d’un environnement de science des données dans le cloud. La machine virtuelle Windows est configurée avec des outils connexes, tels que Notebook IPython, l’Explorateur de stockage Azure et AzCopy, ainsi que d’autres utilitaires utiles pour les projets de science des données. Par exemple, l’Explorateur de stockage Azure et AzCopy facilitent le chargement de données dans le stockage d’objets blob Azure depuis votre ordinateur local ou le téléchargement de ces données vers votre ordinateur local à partir du stockage d’objets blob.

La galerie de machines virtuelles Azure inclut différentes images contenant Microsoft SQL Server. Sélectionnez une image de machine virtuelle SQL Server adaptée à vos besoins en matière de données. Les images recommandées sont les suivantes :

- SQL Server 2012 SP2 Enterprise pour les données de taille réduite ou moyenne
- SQL Server 2012 SP2 Enterprise optimisé pour les charges de travail d’entreposage de données pour les données de taille conséquente ou très volumineuse

 > [AZURE.NOTE] L’image SQL Server 2012 SP2 Enterprise **n’inclut aucun disque de données**. Vous devrez ajouter et/ou attacher un ou plusieurs disques durs virtuels pour stocker vos données. Lorsque vous créez une machine virtuelle Azure, elle comporte un disque pour le système d’exploitation mappé au lecteur C et un disque temporaire mappé au lecteur D. Ne stockez pas de données dans le lecteur D. Comme son nom l’indique, il ne permet qu’un stockage temporaire. Il n'offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure.


##<a name="Provision"></a>Se connecter au portail Azure Classic et approvisionner une machine virtuelle SQL Server

1.  Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com/) avec votre compte. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

2.  Dans le portail Azure Classic, en bas à gauche de la page web, cliquez sur **+NOUVEAU**, sur **CALCUL**, sur **MACHINE VIRTUELLE**, puis sur **À PARTIR DE LA GALERIE**.

3.  Dans la page **Créer une machine virtuelle**, sélectionnez une image de machine virtuelle avec SQL Server adaptée à vos besoins en matière de données, puis cliquez sur la flèche suivante dans le coin inférieur droit de la page. Pour obtenir les informations les plus récentes sur les images SQL Server prises en charge sur Azure, consultez la page [Mise en route de SQL Server sur les machines virtuelles Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) dans l’ensemble de documentation [SQL Server dans les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

	![Sélection de la machine virtuelle Serveur SQL][1]

4.  Dans la première page **Configuration de la machine virtuelle**, entrez les informations suivantes :

    -   Entrez un **nom de machine virtuelle**.
    -   Dans la zone **NEW USER NAME**, entrez un nom d'utilisateur unique pour le compte d'administrateur local de machine virtuelle.
    -   Dans la zone **NEW PASSWORD**, entrez un mot de passe fort. Pour plus d'informations, consultez la page [Mots de passe forts](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Dans la zone **CONFIRM PASSWORD** entrez de nouveau le mot de passe.
    -   Sélectionnez la **taille** adéquate dans le menu déroulant.

     > [AZURE.NOTE] La taille de la machine virtuelle est spécifiée lors de l’approvisionnement : A2 est la taille minimale recommandée pour les charges de travail de production. La taille minimale recommandée pour une machine virtuelle utilisant SQL Server Édition Entreprise est A3. Sélectionnez A3 ou plus lorsque vous utilisez SQL Server Enterprise Edition. Sélectionnez A4 lorsque vous utilisez des images SQL Server 2012 ou 2014 Enterprise optimisées pour les charges de travail transactionnelles. Sélectionnez A7 lorsque vous utilisez des images SQL Server 2012 ou 2014 Enterprise optimisées pour les charges de travail pour l’entreposage de données. La taille sélectionnée limite le nombre de disques de données que vous pouvez configurer. Pour obtenir les informations les plus récentes sur les tailles de machines virtuelles disponibles et le nombre de disques de données que vous pouvez attribuer à une machine virtuelle, consultez la page [Tailles de machines virtuelles pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Pour connaître les informations de tarification, consultez la page [Machines virtuelles Tarification](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Cliquez sur la flèche Suivant située en bas à droite pour continuer.

    ![Configuration de machine virtuelle][2]

5.  Dans la deuxième page **Configuration de la machine virtuelle**, configurez les ressources pour la mise en réseau, le stockage et la disponibilité :

    -   Dans la zone **Service cloud**, sélectionnez **Créer un nouveau service de cloud computing**.
    -   Dans la zone **Nom du cloud Service DNS**, entrez la première partie d'un nom DNS de votre choix, pour qu'il complète un nom au format **TESTNAME.cloudapp.net**
    -   Dans la zone **REGION/AFFINITY GROUP/VIRTUAL NETWORK**, sélectionnez une région d'hébergement pour cette image virtuelle.
    -   Dans **Compte de stockage**, sélectionnez un compte de stockage existant ou un compte créé automatiquement.
    -   Dans la zone **AVAILABILITY SET**, sélectionnez **(none)**.
    -   Lisez et acceptez les informations de tarification.

6.	Dans la section **POINTS DE TERMINAISON**, cliquez dans la liste déroulante vide sous **NOM**, sélectionnez **MSSQL**, puis tapez le numéro de port de l’instance du moteur de base de données (**1433** pour l’instance par défaut).

7.  Votre machine virtuelle SQL Server peut également faire office de serveur Notebook IPython, que nous configurerons ultérieurement. Ajoutez un point de terminaison pour spécifier le port à utiliser pour votre serveur Notebook IPython. Entrez un nom dans la colonne **NOM**, puis sélectionnez le numéro de port de votre choix pour le port public et le numéro 9999 pour le port privé.

	Cliquez sur la flèche Suivant située en bas à droite pour continuer.

	![Sélection des ports MSSQL et IPython][3]

8.  Acceptez l’option **Installer l’agent de machine virtuelle** activée par défaut, puis cliquez sur la coche dans le coin inférieur droit de l’Assistant pour achever le processus d’approvisionnement de la machine virtuelle.

	`![Dernières Options de machine virtuelle][4]

9.  Patientez pendant qu'Azure prépare votre machine virtuelle. L'état de la machine virtuelle doit normalement passer par les phases suivantes :

    -   Démarrage (configuration)
    -   Arrêté
    -   Démarrage (configuration)
    -   Exécution (configuration)
    -   Exécution


##<a name="RemoteDesktop"></a>Ouvrir la machine virtuelle à l’aide du Bureau à distance et achever la configuration

1.  Une fois l’approvisionnement terminé, cliquez sur le nom de votre machine virtuelle pour accéder à la page TABLEAU DE BORD. En bas de la page, cliquez sur **Connect**.

2.  Sélectionnez le fichier .rpd à ouvrir en utilisant le programme Bureau à distance Windows (`%windir%\system32\mstsc.exe`).

3.  Dans la boîte de dialogue **Sécurité de Windows**, entrez le mot de passe du compte d’administrateur local indiqué à l’étape précédente. (Il se peut que vous soyez invité à vérifier les informations d’identification de la machine virtuelle.)

4.  Lors de la première connexion à cette machine virtuelle, il est possible que plusieurs processus doivent s'effectuer, par exemple, la configuration de votre bureau, les mises à jour de Windows et l'achèvement des tâches de configuration initiales de Windows (sysprep). Une fois Windows sysprep terminé, les tâches de configuration de SQL Server sont terminées. L’exécution de ces tâches peut prendre quelques minutes. Tant que la configuration de SQL Server n’est pas terminée, il est possible que `SELECT @@SERVERNAME` ne renvoie pas le nom correct et que SQL Server Management Studio ne soit pas visible sur la page d’accueil.

Une fois que vous êtes connecté à la machine virtuelle avec le Bureau à distance Windows, celle-ci fonctionne comme un autre ordinateur. Connectez-vous normalement à l'instance par défaut de SQL Server avec SQL Server Management Studio (exécuté sur la machine virtuelle).


##<a name="InstallIPython"></a>Installer Notebook IPython et les autres outils connexes

Un script de personnalisation spécial est mis à votre disposition pour vous permettre de configurer la nouvelle machine virtuelle SQL Server en tant que serveur Notebook IPython et pour installer des outils connexes complémentaires, tels qu’AzCopy, l’Explorateur de stockage Azure, des packages de science des données Python très utiles, etc. Pour effectuer l’installation :

- Cliquez avec le bouton droit sur l’icône **Démarrage Windows** et cliquez sur **Invite de commandes (Admin)**
- Copiez les commandes ci-après et collez-les au niveau de l’invite de commandes.

    	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
    	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Lorsque vous y êtes invité, entrez un mot de passe de votre choix pour le serveur Notebook IPython.
- Le script de personnalisation automatise plusieurs procédures post-installation répertoriées ci-dessous.
	+ Installation et configuration du serveur Notebook IPython
	+ Ouverture de ports TCP dans le Pare-feu Windows pour les points de terminaison créés précédemment :
	+ pour la connectivité à distance de SQL Server ;
	+ pour la connectivité à distance du serveur Notebook IPython.
	+ Extraction des exemples de notebooks IPython et de scripts SQL
	+ Téléchargement et installation des packages de science des données Python utiles
	+ Téléchargement et installation d’outils Azure tels qu’AzCopy et Azure Storage Explorer <br>
- Vous pouvez accéder à Notebook IPython et l’exécuter depuis n’importe quel navigateur local ou distant en utilisant une URL du type `https://<virtual_machine_DNS_name>:<port>`, où la variable port correspond au port public IPython que vous avez sélectionné lors de l’approvisionnement de la machine virtuelle.
- Le serveur Notebook IPython s’exécute en tant que service d’arrière-plan et sera redémarré automatiquement quand vous redémarrerez la machine virtuelle.

##<a name="Optional"></a>Attacher des disques de données selon vos besoins

Si l’image de machine virtuelle que vous avez sélectionnée n’inclut aucun disque de données, c’est-à-dire aucun disque autre que le lecteur C (disque du système d’exploitation) ou le lecteur D (disque temporaire), vous devez ajouter un ou plusieurs disques de données pour y stocker vos données. L’image de machine virtuelle pour SQL Server 2012 SP2 Enterprise optimisé pour les charges de travail d’entreposage de données est préconfigurée avec des disques supplémentaires pour les fichiers de données et les fichiers journaux SQL Server.

 > [AZURE.NOTE] Ne stockez pas de données dans le lecteur D. Comme son nom l’indique, il ne permet qu’un stockage temporaire. Il n’offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure.

Pour attacher des disques de données supplémentaires, suivez la procédure décrite dans l’article [Attachement d’un disque de données à une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md), qui explique en détail comment effectuer les opérations ci-après.

1. Attachement de disques vides à la machine virtuelle approvisionnée aux étapes précédentes
2. Initialisation des nouveaux disques dans la machine virtuelle


##<a name="SSMS"></a>Se connecter à SQL Server Management Studio et activer l’authentification en mode mixte

Le moteur de base de données de SQL Server ne peut pas utiliser l’authentification Windows sans un environnement de domaine. Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, configurez SQL Server pour l'authentification en mode mixte qui permet l'authentification SQL Server et l'authentification Windows Le mode d’authentification SQL est requis pour la réception de données directement à partir de vos bases de données de machine virtuelle SQL Server dans [Azure Machine Learning Studio](https://studio.azureml.net) à l’aide du module Importer les données.

1.  Lorsque vous êtes connecté à la machine virtuelle à l’aide du Bureau à distance, utilisez le volet Windows **Rechercher** et tapez **SQL Server Management Studio** (SMSS). Cliquez pour démarrer SQL Server Management Studio (SSMS). Vous pouvez ajouter un raccourci pour SSMS sur votre Bureau à des fins d’utilisation ultérieure.

    ![Démarrer SSMS][5]

    Lorsque vous ouvrez Management Studio pour la première fois, il doit créer l’environnement Management Studio pour les utilisateurs. Cette opération peut prendre du temps.

2.  Lorsque vous l’ouvrez, Management Studio affiche la boîte de dialogue **Se connecter au serveur**. Dans la zone **Nom du serveur**, entrez le nom de la machine virtuelle à connecter au moteur de base de données avec Object Explorer (au lieu d'entrer le nom de la machine virtuelle, vous pouvez également utiliser **(local)** ou un simple point pour le **nom du serveur**). Sélectionnez **Authentification Windows** et laissez ***your\_VM\_name*\\your\_local\_administrator** dans la zone **Nom d'utilisateur**. Cliquez sur **Connecter**.

    ![Se connecter au serveur][6]

	<br>

	 > [AZURE.TIP] Vous pouvez changer le mode d’authentification SQL Server en effectuant une modification de clé de Registre Windows ou en utilisant SQL Server Management Studio. Pour changer le mode d’authentification à l’aide d’une modification de clé de Registre, démarrez une **Nouvelle requête** et exécutez le script suivant :

		USE master
    	go

    	EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
    	go


	Pour changer le mode d’authentification au moyen de SQL Server Management Studio :

3.  Dans l’**Explorateur d’objets SQL Server Management Studio**, cliquez avec le bouton droit sur le nom de l’instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.

    ![Propriétés de serveur][7]

4.  Sur la page **Sécurité**, sous **Authentification du serveur**, sélectionnez **Mode d'authentification SQL Server et Windows**, puis cliquez sur **OK**.

    ![Sélectionner le mode d'authentification][8]

5.  Dans la boîte de dialogue **SQL Server Management Studio**, cliquez sur **OK** pour confirmer l'obligation de redémarrer SQL Server.

6.  Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur votre serveur, puis cliquez sur **Redémarrer**. (si SQL Server Agent est exécuté, il doit également être redémarré).

    ![Redémarrer][9]

7.  Dans la boîte de dialogue **SQL Server Management Studio**, cliquez sur **Oui** pour confirmer que vous voulez redémarrer SQL Server.

##<a name="Logins"></a>Créer des connexions d’authentification SQL Server

Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, vous devez créer au moins une connexion d'authentification SQL Server.

Vous pouvez créer des connexions SQL Server par programme ou en utilisant SQL Server Management Studio. Pour créer un utilisateur sysadmin avec l’authentification SQL par programme, démarrez une **Nouvelle requête**, puis exécutez le script ci-après. Remplacez les variables <nouveau nom d’utilisateur> et <nouveau mot de passe> par le *nom d’utilisateur* et le *mot de passe* de votre choix.


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
    	CHECK_POLICY = OFF,
    	CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Ajustez la stratégie de mot de passe selon vos besoins (l’exemple de code désactive la vérification de la stratégie et l’expiration du mot de passe). Pour plus d'informations sur les connexions SQL Server, consultez la page [Créer un compte de connexion](http://msdn.microsoft.com/library/aa337562.aspx).

Pour créer des connexions SQL Server à l’aide de SQL Server Management Studio :

1.  Dans l’**Explorateur d’objets SQL Server Management Studio**, développez le dossier de l’instance de serveur dans laquelle vous voulez créer la connexion.

2.  Cliquez avec le bouton droit sur le dossier **Sécurité**, pointez sur **Nouveau**, puis sélectionnez **Connexion**.

    ![Nouvelle connexion][10]

3.  Dans la boîte de dialogue **Nouvelle connexion**, sur la page **Général**, entrez le nom du nouvel utilisateur dans la zone **Nom de connexion**.

4.  Sélectionnez **Authentification SQL Server**.

5.  Dans la zone **Mot de passe**, entrez un mot de passe pour le nouvel utilisateur. Entrez de nouveau ce mot de passe dans la zone **Confirmer le mot de passe**.

6.  Pour renforcer la complexité et la sécurité des options de stratégie de mot de passe, sélectionnez **Conserver la stratégie de mot de passe** (recommandé). Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

7.  Pour conserver les options de stratégie de mot de passe après expiration, sélectionnez **Conserver l'expiration du mot de passe** (recommandé). La stratégie de conservation de mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

8.  Pour forcer l'utilisateur à créer un mot de passe après sa première connexion, sélectionnez **L'utilisateur doit changer de mot de passe à la prochaine connexion** (recommandé si cette connexion est destinée à une autre personne. Si la connexion est destinée à votre propre usage, ne sélectionnez pas cette option). L'option Conserver l'expiration du mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

9.  Dans la liste **Base de données par défaut**, sélectionnez une base de données par défaut pour la connexion. Par défaut, cette option est réglée sur **principale**. Si vous n'avez pas encore créé de base de données d'utilisateur, gardez la valeur par défaut **master**.

10. Dans la liste **Langue par défaut**, conservez la valeur **par défaut**.

    ![Propriétés de connexion][11]

11. S'il s'agit de votre première connexion, vous pouvez désigner cette connexion en tant qu'administrateur SQL Server. Si cela est le cas, sur la page **Rôles du serveur**, activez la case à cocher **administrateur système**.

    > [AZURE.IMPORTANT] les membres du rôle serveur administrateur système fixe contrôlent complètement le moteur de base de données. Pour des raisons de sécurité, vous devez limiter soigneusement l'appartenance à ce rôle.

    ![administrateur système][12]

12. Cliquez sur OK.

##<a name="DNS"></a>Déterminer le nom DNS de la machine virtuelle

Pour vous connecter au moteur de base de données SQL Server à partir d'un autre ordinateur, vous devez connaître le nom DNS de la machine virtuelle.

Il s'agit du nom utilisé par Internet pour identifier une machine virtuelle. Vous pouvez utiliser l'adresse IP, mais celle-ci peut être modifiée lorsqu'Azure déplace des ressources pour des raisons de redondance ou de maintenance. Le nom DNS reste stable, car il peut être redirigé vers une nouvelle adresse IP.

1.  Dans le portail Azure Classic (ou à partir de l’étape précédente), sélectionnez **VIRTUAL MACHINES**.

2.  Dans la page **INSTANCES D’UNE MACHINE VIRTUELLE**, au niveau de la colonne **NOM DNS**, recherchez et copiez le nom DNS de la machine virtuelle qui apparaît, précédé de la chaîne **http://**. (si l'interface utilisateur n'affiche pas l'intégralité du nom, cliquez dessus avec le bouton droit, puis sélectionnez Copier).

##<a name="cde"></a>Se connecter au moteur de base de données à partir d’un autre ordinateur

1.  Sur un ordinateur connecté à Internet, ouvrez SQL Server Management Studio.

2.  Dans la boîte de dialogue **Se connecter au serveur** ou **Se connecter au moteur de base de données**, dans la zone **Nom du serveur**, entrez le nom DNS de la machine virtuelle (déterminé durant la tâche précédente), ainsi qu'un numéro de port de point de terminaison public au format *NomDNS,numéro\_port* tel que **tutorialtestVM.cloudapp.net,57500**.

3.  Dans la zone **Authentification**, sélectionnez **Authentification SQL Server**.

4.  Dans la zone **Connexion**, entrez le nom d'une connexion créée lors d'une tâche précédente.

5.  Dans la zone **Mot de passe**, entrez le mot de passe de connexion que vous avez créé lors d'une tâche précédente.

6.  Cliquez sur **Connecter**.

##<a name="amlconnect"></a>Se connecter au moteur de base de données à partir d’Azure Machine Learning

Dans les étapes ultérieures du processus TDSP (Team Data Science Process) dans le cloud, vous allez utiliser [Azure Machine Learning Studio](https://studio.azureml.net) pour générer et déployer des modèles d’apprentissage automatique. Pour recevoir des données provenant de vos bases de données de machine virtuelle SQL Server directement dans Azure Machine Learning à des fins d’apprentissage ou de notation, utilisez le module **Importer les données** dans une nouvelle expérience [Azure Machine Learning Studio](https://studio.azureml.net). Des informations détaillées sur ce sujet sont accessibles par le biais des liens du guide du processus TDSP (Team Data Science Process). Pour découvrir une introduction, consultez la page [Azure Machine Learning Studio - De quoi s’agit-il ?](machine-learning-what-is-ml-studio.md).

2.	Dans le volet **Propriétés** du [module Importer les données](https://msdn.microsoft.com/library/azure/dn905997.aspx), sélectionnez **Base de données SQL Azure** dans la liste déroulante **Source de données**.

3.	Dans la zone de texte **Nom du serveur de base de données**, entrez `tcp:<DNS name of your virtual machine>,1433`.

4.	Entrez le nom d’utilisateur SQL dans la zone de texte **Nom de compte d’utilisateur du serveur**.

5.	Entrez le mot de passe de l’utilisateur SQL dans la zone de texte **Mot de passe de compte d’utilisateur du serveur**.

	![Importer les données Azure ML][13]

##<a name="shutdown"></a>Arrêter et libérer une machine virtuelle inutilisée

Le service Azure Virtual Machines est facturé au tarif du **paiement à l’utilisation**. Pour vous assurer que vous n’êtes pas facturé lorsque vous n’utilisez pas votre machine virtuelle, cette dernière doit être définie sur l’état **Arrêté (désalloué)**.

> [AZURE.NOTE] Si vous arrêtez la machine virtuelle depuis cette dernière (à l’aide des options d’alimentation Windows), la machine virtuelle est arrêtée, mais reste allouée. Pour obtenir l’assurance que vous ne continuerez pas à être facturé, arrêtez toujours les machines virtuelles à partir du [portail Azure Classic](http://manage.windowsazure.com/). Vous pouvez également arrêter la machine virtuelle par le biais de Powershell en appelant ShutdownRoleOperation avec « PostShutdownAction » défini sur « StoppedDeallocated ».

Pour arrêter et libérer la machine virtuelle :

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com/) avec votre compte.

2. Sélectionnez **MACHINES VIRTUELLES** dans la barre de navigation gauche.

3. Dans la liste des machines virtuelles, cliquez sur le nom de votre machine virtuelle, puis accédez à la page **TABLEAU DE BORD**.

4. Au bas de la page, cliquez sur **ARRÊT**.

![Arrêt de la machine virtuelle][15]

Cette opération libère la machine virtuelle, mais ne la supprime pas. Vous pouvez redémarrer votre machine virtuelle à tout moment à partir du portail Azure Classic.

## Étapes suivantes une fois votre machine virtuelle Azure SQL Server prête à être utilisée

Votre machine virtuelle est désormais prête à l’emploi dans vos exercices de science des données. Cette machine est également utilisable sous la forme d’un serveur Notebook IPython pour l’exploration et le traitement des données, ainsi que pour l’exécution d’autres tâches avec Azure Machine Learning et le processus TDSP (Team Data Science Process).

Les étapes suivantes du processus de science des données sont présentées dans le [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement, de traitement et d’échantillonnage des données dans HDInsight en vue d’en extraire de l’information pertinente avec Azure Machine Learning.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

<!---HONumber=AcomDC_0921_2016-->