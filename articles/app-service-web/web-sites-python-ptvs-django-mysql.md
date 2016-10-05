<properties 
	pageTitle="Django et MySQL sur Azure avec Python Tools 2.2 pour Visual Studio" 
	description="Découvrez comment utiliser Python Tools pour Visual Studio afin de créer une application Django qui stocke les données dans une instance de base de données MySQL et de la déployer sur Azure App Service Web Apps." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python"
	ms.topic="get-started-article" 
	ms.date="07/07/2016"
	ms.author="huvalo"/>

# Django et MySQL sur Azure avec Python Tools 2.2 pour Visual Studio 

[AZURE.INCLUDE [onglets](../../includes/app-service-web-get-started-nav-tabs.md)]

Dans ce didacticiel, vous allez utiliser [Python Tools pour Visual Studio](PTVS) afin de créer une application web de sondage simple, à l’aide de l’un des exemples de modèle PTVS. Vous allez découvrir comment utiliser un service MySQL hébergé sur Azure, comment configurer l’application web pour utiliser mySQL et comment publier l’application web sur [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

> [AZURE.NOTE] Les informations contenues dans ce didacticiel sont également disponibles dans la vidéo suivante :
> 
> [PTVS 2.1: Django app with MySQL][video] \(PTVS 2.1 : application Django avec MySQL)

Visitez le [Centre de développement Python] pour consulter d’autres articles sur le développement d’applications Azure App Service Web Apps avec PTVS à l’aide des infrastructures web Bottle, Flask et Django, ainsi que des services Azure Table Storage, MySQL et Base de données SQL. Cet article concerne App Service, mais les étapes sont similaires lorsque vous développez pour [Azure Cloud Services].

## Composants requis

 - Visual Studio 2015
 - [Python 2.7 32 bits] ou [Python 3.4 32 bits]
 - [Python Tools 2.2 pour Visual Studio]
 - [Python Tools 2.2 pour Visual Studio Samples VSIX]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015]
 - Django 1.9 ou version ultérieure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est nécessaire et vous ne prenez aucun engagement.

## Création du projet

Dans cette section, vous allez créer un projet Visual Studio à l’aide d’un exemple de modèle. Vous allez créer un environnement virtuel et installer les packages requis. Vous allez créer une base de données locale à l’aide de sqlite. Ensuite, vous allez exécuter l’application localement.

1. Dans Visual Studio, sélectionnez **Fichier** puis **Nouveau projet**.

1. Les modèles de projet issus de [Python Tools 2.2 pour Visual Studio Samples VSIX] sont disponibles sous **Python**, **Exemples**. Sélectionnez **Polls Django Web Project** et cliquez sur OK pour créer le projet.

    ![Boîte de dialogue Nouveau projet](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1. Vous allez être invité à installer des packages externes. Sélectionnez **Installer dans un environnement virtuel**.

    ![Boîte de dialogue Packages externes](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1. Sélectionnez **Python 2.7** ou **Python 3.4** comme interpréteur de base.

    ![Boîte de dialogue Ajouter un environnement virtuel](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, et sélectionnez **Python** puis **Django Migrate (Migration de Django)**. Ensuite, sélectionnez **Django Create Superuser (Créer un superutilisateur Django)**.

1. Une console de gestion Django s’ouvre, et une base de données sqlite est créée dans le dossier du projet. Suivez les invites pour créer un utilisateur.

1. Vérifiez que l’application fonctionne en appuyant sur `F5`.

1. Cliquez sur **Log in** dans la barre de navigation en haut.

    ![Barre de navigation Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1. Saisissez les informations d’identification de l’utilisateur que vous avez créé quand vous avez synchronisé la base de données.

    ![Formulaire de connexion](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1. Cliquez sur **Create Sample Polls**.

    ![Création d’exemples de sondage](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1. Cliquez sur un sondage et votez.

    ![Vote dans les exemples de sondage](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Création d’une base de données MySQL

Pour la base de données, vous allez créer une base de données MySQL ClearDB hébergée sur Azure.

Vous pouvez également créer votre propre machine virtuelle s'exécutant dans Azure, puis installer et administrer MySQL vous-même.

Pour créer une base de données avec un plan gratuit, procédez comme suit :

1. Connectez-vous au [portail Azure].

1. En haut du volet de navigation, cliquez sur **Nouveau**, sur **Données + stockage** puis sur **Base de données MySQL**.

1. Configurez la nouvelle base de données MySQL en créant un nouveau groupe de ressources, puis sélectionnez l’emplacement approprié pour celui-ci.

1. Une fois la base de données MySQL créée, cliquez sur **Propriétés** dans le panneau de la base de données.

1. Utilisez le bouton de copie pour placer la valeur de **CONNECTIONSTRING** dans le Presse-papiers.

## Configurer le projet

Dans cette section, vous allez configurer l’application web pour utiliser la base de données MySQL que vous venez de créer. Vous allez également installer les packages Python supplémentaires requis pour utiliser des bases de données MySQL avec Django. Vous exécuterez ensuite l’application web en local.

1. Dans Visual Studio, ouvrez **settings.py** à partir du dossier *ProjectName*. Collez temporairement la chaîne de connexion dans l’éditeur. Celle-ci se présente au format suivant :

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Modifiez le **MOTEUR** de base de données par défaut pour utiliser MySQL, puis définissez les valeurs **NOM**, **UTILISATEUR**, **MOT DE PASSE** et **HÔTE** à partir de **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1. Dans l’Explorateur de solutions, sous **Environnements Python**, cliquez avec le bouton droit sur l’environnement virtuel et sélectionnez **Installer le package Python**.

1. Installez le package `mysqlclient` en utilisant **pip**.

    ![Boîte de dialogue Installer le package](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, et sélectionnez **Python** puis **Django Migrate (Migration de Django)**. Ensuite, sélectionnez **Django Create Superuser (Créer un superutilisateur Django)**.

    Les tables de la base de données MySQL que vous avez créée dans la section précédente sont alors elles aussi créées. Suivez les invites pour créer un utilisateur, qui ne doit pas forcément correspondre à l’utilisateur inclus dans la base de données sqlite que nous avons créée dans la première section de cet article.

1. Exécutez l’application avec `F5`. Les sondages créés à l’aide de la fonction **Create Sample Polls** et les données soumises par vote sont sérialisés dans la base de données MySQL.

## Publication de l’application web dans Azure App Service

Le kit de développement logiciel (SDK) Azure .NET offre un moyen simple de déployer votre application web dans Azure App Service.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

    ![Boîte de dialogue Publier le site Web](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1. Cliquez sur **Microsoft Azure App Service**.

1. Cliquez sur **Nouveau** pour créer une application web.

1. Renseignez les champs suivants et cliquez sur **Créer** :
	- **Nom de l’application web**
	- **Plan App Service**
	- **Groupe de ressources**
	- **Région**
	- Dans **Serveur de base de données**, conservez **Aucune base de données**.

1. Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1. Votre navigateur web ouvre automatiquement l’application web publiée. L’application doit fonctionner comme prévu et utiliser la base de données **MySQL** hébergée sur Azure.

    ![Navigateur web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

    Félicitations ! Vous avez publié votre application web basée sur MySQL dans Azure.

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Django et MySQL.

- [Documentation relative à Python Tools for Visual Studio]
  - [Projets web]
  - [Projets de service cloud]
  - [Débogage à distance sur Microsoft Azure]
- [Documentation Django]
- [MySQL]

Pour plus d’informations, consultez le [Centre pour développeurs Python](/develop/python/).

<!--Link references-->

[Centre de développement Python]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->

[portail Azure]: https://portal.azure.com
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 pour Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation relative à Python Tools for Visual Studio]: http://aka.ms/ptvsdocs
[Débogage à distance sur Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projets web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projets de service cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentation Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo

<!---HONumber=AcomDC_0713_2016-->