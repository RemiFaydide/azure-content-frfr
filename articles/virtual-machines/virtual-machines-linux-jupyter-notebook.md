<properties
	pageTitle="Créer un bloc-notes Jupyter/iPython Notebook | Microsoft Azure"
	description="Découvrez comment déployer un bloc-notes Jupyter/iPython Notebook sur une machine virtuelle Linux créée avec le modèle de déploiement du Gestionnaire de ressources Azure."
	services="virtual-machines-linux"
	documentationCenter="python"
	authors="crwilcox"
	manager="wpickett"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="python"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="crwilcox"/>

# Jupyter Notebook sur Azure

Le [projet Jupyter](http://jupyter.org), anciennement appelé [projet iPython](http://ipython.org), fournit un ensemble d’outils destiné au calcul scientifique. Ces outils utilisent des interpréteurs de commandes interactifs et puissants qui associent l’exécution de code à la création d’un document de calcul dynamique. Ces fichiers de l'interpréteur contiennent du texte arbitraire, des formules mathématiques, un code d'entrée, des résultats, des graphiques, des vidéos et d'autres sortes de support qu'un navigateur Web moderne peut afficher. Que vous soyez complètement novice sur Python et que vous souhaitiez apprendre à vous en servir dans un environnement divertissant et interactif ou que vous souhaitiez effectuer d’importants calculs parallèles/techniques, l'interpréteur Jupyter Notebook est un choix optimal.

![Capture d'écran](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) Utilisation des packages SciPy et Matplotlib pour analyser la structure d’un enregistrement.


## Jupyter en deux modes : Azure Notebooks ou déploiement personnalisé
Azure fournit un service qui vous aidera à [prendre rapidement en main Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx). En utilisant Azure Notebook Service, vous avez facilement accès à l’interface web de Jupyter et à des ressources de calcul évolutives avec toute la puissance de Python et de ses nombreuses bibliothèques. Étant donné que l’installation est gérée par le service, les utilisateurs peuvent accéder à ces ressources sans avoir à effectuer aucun travail d’administration et de configuration.

Si le service Notebook n’est pas approprié pour votre scénario, poursuivez la lecture de cet article qui vous indiquera comment déployer Jupyter Notebook sur Microsoft Azure, à l'aide de machines virtuelles Linux (VM).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Créer et configurer une machine virtuelle sur Azure

La première étape consiste à créer une machine virtuelle s’exécutant sur Azure. Cette machine virtuelle est un système d’exploitation complet dans le cloud et sera utilisée pour exécuter l’interpréteur Jupyter Notebook. Azure peut exécuter des machines virtuelles Linux et Windows. C’est pourquoi nous allons étudier la configuration de Jupyter sur les deux types de machines.

### Créer une machine virtuelle Linux et ouvrir un port pour Jupyter

Suivez les instructions fournies [ici][portal-vm-linux] pour créer une machine virtuelle de la distribution *Ubuntu*. Ce didacticiel utilise Ubuntu Server 14.04 LTS. Nous présumerons que le nom d’utilisateur est *azureuser*.

Après le déploiement de la machine virtuelle, nous devons activer une règle de sécurité sur le groupe de sécurité réseau. À partir du portail Azure, accédez à **Groupes de sécurité réseau** et ouvrez l’onglet du groupe de sécurité correspondant à votre machine virtuelle. Vous devez ajouter une règle de sécurité de trafic entrant avec les paramètres suivants : **TCP** pour le protocole, ***** pour le port source (public) et **9999** pour le port de destination (privé).

![Capture d'écran](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Dans votre groupe de sécurité réseau, cliquez sur **Interfaces réseau** et notez l’**adresse IP publique**, car vous en aurez besoin pour vous connecter à votre machine virtuelle à l’étape suivante.

## Installer les logiciels requis sur la machine virtuelle

Pour exécuter Jupyter Notebook sur votre machine virtuelle, vous devez tout d’abord installer Jupyter et ses dépendances. Connectez-vous à votre machine virtuelle Linux à l’aide de SSH et de la combinaison nom d’utilisateur/mot de passe que vous avez choisie au moment de la création de la machine virtuelle. Dans ce didacticiel, nous utiliserons PuTTY pour nous connecter à partir de Windows.

### Installation de Jupyter sur Ubuntu
Installez Anaconda, une distribution de Python courante dans le domaine de la science des données, à partir d’un des liens fournis dans [Continuum Analytics](https://www.continuum.io/downloads). Les liens ci-dessous correspondent aux versions les plus récentes (au moment de la rédaction de ce document).

#### Installations Anaconda pour Linux
<table>
  <th>Python&#160;3.4</th>
  <th>Python&#160;2.7</th>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64&#160;bits</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64&#160;bits</href>
	</td>
  </tr>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
	</td>  
  </tr>
</table>


#### Installation d’Anaconda3 2.3.0 64 bits sur Ubuntu
Voici un exemple d’installation d’Anaconda sur Ubuntu

	# install anaconda
	cd ~
	mkdir -p anaconda
	cd anaconda/
	curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
	sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

	# clean up home directory
	cd ..
	rm -rf anaconda/

	# Update Jupyter to the latest install and generate its config file
	sudo /anaconda3/bin/conda install jupyter -y
	/anaconda3/bin/jupyter-notebook --generate-config


![Capture d'écran](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### Configuration de Jupyter et utilisation de SSL
Après l’installation, nous devons prendre un moment pour configurer les fichiers de configuration pour Jupyter. Si vous rencontrez des problèmes pour configurer Jupyter, nous vous conseillons de consulter la [documentation Jupyter relative à l’exécution d’un serveur Notebook](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Ensuite, nous utilisons la commande `cd` pour accéder au répertoire de profil afin de créer notre certificat SSL et de modifier le fichier de configuration des profils.

Sur Linux, utilisez la commande suivante.

    cd ~/.jupyter

Utilisez la commande suivante pour créer le certificat SSL (Linux et Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Notez que puisque nous créons un certificat SSL auto-signé, lors de la connexion à l'interpréteur, votre navigateur affiche un message d'avertissement. Pour une utilisation à long terme, vous souhaiterez utiliser un certificat correctement signé associé à votre organisation. Comme la gestion des certificats ne figure pas dans cette démonstration, nous nous en tiendrons à un certificat auto-signé pour l'instant.

En plus de l'utilisation d'un certificat, vous devez également fournir un mot de passe pour protéger votre interpréteur contre toute utilisation non autorisée. Pour des raisons de sécurité, Jupyter utilise des mots de passe chiffrés dans son fichier de configuration. De ce fait, vous devez tout d’abord chiffrer votre mot de passe. Pour ce faire, iPython fournit un utilitaire. À une invite de commandes, exécutez la commande suivante.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Vous serez invité à fournir un mot de passe et une confirmation, puis le mot de passe s'affichera. Notez cette information, car vous en aurez besoin à l’étape suivante.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Nous allons ensuite modifier le fichier de configuration du profil (fichier `jupyter_notebook_config.py` du répertoire dans lequel vous vous trouvez). Si ce fichier n’existe pas encore, vous devez le créer. Ce fichier comporte de nombreux champs, tous commentés par défaut. Vous pouvez ouvrir ce fichier avec un éditeur de texte de votre choix et vous devez vous assurer qu’il a au moins le contenu suivant. **Remplacez c.NotebookApp.password dans la configuration par sha1 fourni à l’étape précédente**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Exécutez Jupyter Notebook.

À présent, nous sommes prêts à démarrer Jupyter Notebook. Pour ce faire, accédez au répertoire où vous voulez stocker les blocs-notes, puis démarrez le serveur Jupyter Notebook avec la commande suivante.

    /anaconda3/bin/jupyter-notebook

Vous devez à présent être en mesure d’accéder à Jupyter Notebook à l’adresse `https://[PUBLIC-IP-ADDRESS]:9999`.

Quand vous accédez pour la première fois à votre interpréteur, la page de connexion demande votre mot de passe. Une fois que vous êtes connecté, « Jupyter Notebook Dashboard » s'affiche. Il s'agit du centre de commande de toutes les opérations de bloc-notes. Dans cette page, vous pouvez créer des interpréteurs et ouvrir des interpréteurs existants.

![Capture d'écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### Utilisation de Jupyter Notebook

Si vous cliquez sur le bouton **Nouveau**, la page d’ouverture suivante s’affiche.

![Capture d'écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

La zone marquée par une invite `In []:` est la zone d’entrée. Vous pouvez y taper tout code Python valide qui s’exécutera quand vous appuierez sur les touches `Shift-Enter` ou que vous cliquerez sur l’icône « Lire » (triangle pointant vers la droite dans la barre d’outils).

## Un puissant paradigme : documents de calcul dynamique avec des supports puissants

L’interpréteur lui-même semble très naturel à toute personne ayant utilisé Python et un traitement de texte, car c’est une combinaison des deux à certains égards : vous pouvez exécuter des blocs de code Python, mais vous pouvez également conserver des notes et d’autres types de texte en changeant le style d’une cellule de « Code » en « Texte » à l’aide du menu déroulant dans la barre d’outils.

Jupyter est bien plus qu’un traitement de texte, car il permet l’association de calculs et de supports puissants (texte, graphiques, vidéos et, virtuellement, tout ce qu’un navigateur web actuel peut afficher). Vous pouvez associer du texte, du code, des vidéos et bien plus encore !

![Capture d'écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

De même, avec la puissance des nombreuses et très performantes bibliothèques de Python pour le calcul scientifique et technique, un simple calcul peut être effectué avec la même facilité qu’une analyse complexe d’un réseau, le tout dans un même environnement.

Ce paradigme d'association de la puissance du Web avec le calcul dynamique offre de nombreuses possibilités et est idéalement approprié au cloud. L'interpréteur peut être utilisé :

* en tant que bloc-notes de calcul pour enregistrer le travail exploratoire relatif à un problème ;

* pour partager des résultats avec des collègues, sous la forme d’un calcul « dynamique » ou de formats de sortie papier (HTML, PDF) ;

* pour distribuer et présenter des supports de formation en direct impliquant du calcul, afin que les participants puissent immédiatement utiliser le code réel, le modifier et le réexécuter interactivement ;

* pour fournir d’autres « papiers exécutables » qui présentent les résultats de recherches d’une manière qui peut être immédiatement reproduite, validée et étendue par d’autres ;

* comme plateforme pour le calcul collaboratif : plusieurs utilisateurs peuvent se connecter au même serveur d’interpréteur afin de partager une session de calcul en direct.


Si vous accédez au [référentiel][] de code source iPython, vous y trouverez un répertoire complet avec des exemples de bloc-notes, que vous pouvez télécharger et expérimenter sur votre propre machine virtuelle Azure Jupyter. Téléchargez simplement les fichiers `.ipynb` à partir du site et chargez-les sur le tableau de bord de la machine virtuelle Azure de l’interpréteur (ou téléchargez-les directement dans la machine virtuelle).

## Conclusion

Grâce à son interface performante, Jupyter Notebook offre un accès interactif aux puissantes fonctionnalités de l’écosystème Python sur Azure. Il couvre une large gamme de cas d'utilisation, notamment les simples exploration et apprentissage de Python, l'analyse et la visualisation des données, le calcul de simulation et parallèle. Les documents Notebook obtenus contiennent un enregistrement complet des calculs qui sont effectués et peuvent être partagés avec d’autres utilisateurs de Jupyter. Jupyter Notebook peut être utilisé comme application locale, mais il est plus particulièrement conçu pour des déploiements cloud sur Azure.

Les fonctionnalités principales de Jupyter sont également disponibles dans Visual Studio par le biais des [outils Python pour Visual Studio][] \(PTVS). PTVS est un plug-in open source gratuit de Microsoft qui transforme Visual Studio en environnement de développement Python avancé qui inclut un éditeur avancé avec IntelliSense, ainsi qu'une intégration de débogage, de profilage et de calcul parallèle.

## Étapes suivantes

Pour plus d’informations, consultez le [Centre de développement Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[référentiel]: https://github.com/ipython/ipython
[outils Python pour Visual Studio]: http://aka.ms/ptvs

<!---HONumber=AcomDC_0601_2016-->