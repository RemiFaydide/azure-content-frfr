<properties
	pageTitle="Déploiement de votre application dans Azure App Service"
	description="Apprendre à déployer votre application sur Azure App Service"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="cephalin;dariac"/>
    
# Déploiement de votre application dans Azure App Service

Cet article vous aidera à déterminer la meilleure solution pour déployer les fichiers de votre application web, un serveur d’application mobile ou une application API sur [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), puis de vous orienter vers des articles et des blocs contenant des informations sur les procédures spécifiques à votre option préférée.

## <a name="overview"></a>Vue d’ensemble du déploiement d’Azure App Service

Azure App Service gère l'infrastructure d'application pour vous (ASP.NET, PHP, Node.js, etc.). Certaines infrastructures sont activées par défaut tandis que d’autres, comme Java et Python, peuvent nécessiter une simple configuration avec coche pour l’activer. En outre, vous pouvez personnaliser votre infrastructure d’application, notamment la version de PHP ou le nombre de bits de votre runtime. Pour plus d’informations, consultez [Configurer votre application dans Azure App Service](web-sites-configure.md).

Comme vous n’avez pas à vous soucier du serveur web ou l’infrastructure d’application web, le déploiement de votre application sur le service d’application consiste à déployer votre code, les fichiers binaires, les fichiers de contenu et leur structure de répertoire respective sur l’annuaire [**/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou le répertoire **/site/wwwroot/App\_Data/Jobs/** pour les tâches web). App Service prend en charge les options de déploiement suivantes :

- [FTP ou FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol) : utilisez votre outil compatible FTP ou FTPS favori pour déplacer vos fichiers vers Azure, de [FileZilla](https://filezilla-project.org) à des IDE complets comme [NetBeans](https://netbeans.org). Il s’agit d’un processus de téléchargement de fichier au sens strict. Aucun service supplémentaire n’est fourni par le Service d’application, notamment le contrôle de version, la gestion de structure de fichiers, etc.

- [Kudu (Git/Mercurial ou OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment) : utilisez le [moteur de déploiement](https://github.com/projectkudu/kudu/wiki) dans App Service. Placez votre code directement dans Kudu depuis n’importe quel référentiel. Kudu offre également des services ajoutés à chaque fois que le code lui est transmis, notamment la gestion de version, la restauration de package, MSBuild et des [web hooks](https://github.com/projectkudu/kudu/wiki/Web-hooks) pour un déploiement continu et d’autres tâches Automation. Le moteur de déploiement Kudu prend en charge 3 différents types de sources de déploiement :
    * Synchronisation de contenu depuis OneDrive et Dropbox
    * Déploiement continu basé sur référentiel avec synchronisation automatique à partir de GitHub, Bitbucket et Visual Studio Team Services
    * Déploiement basé sur référentiel avec synchronisation manuelle à partir de Git local

- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy) : déployez le code sur App Service directement à partir de vos outils Microsoft favoris comme Visual Studio, en utilisant le même outil qui automatise le déploiement sur des serveurs IIS. Cet outil prend en charge le déploiement différentiel, la création de base de données, les transformations de chaînes de connexion, etc. Web Deploy diffère de Kudu parce que les fichiers binaires d’application sont générés avant d’être déployés vers Azure. Comme FTP, aucun service supplémentaire n’est fourni par le service d’application.

Les outils de développement web populaires prennent en charge un ou plusieurs de ces processus de déploiement. L’outil que vous choisissez détermine les processus de déploiement que vous pouvez exploiter, la fonctionnalité DevOps réelle à votre disposition dépend de la combinaison du processus de déploiement et les outils spécifiques que vous choisissez. Par exemple, si vous effectuez le déploiement Web à partir de [Visual Studio avec Azure SDK](#vspros), même si vous n’obtenez pas d’automatisation de la part de Kudu, vous obtenez une restauration de package et l’automatisation MSBuild dans Visual Studio.

>[AZURE.NOTE] Ces processus de déploiement [n’approvisionnent pas réellement les ressources Azure](../resource-group-template-deploy-portal.md) dont votre application peut avoir besoin. Toutefois, la plupart des articles sur les procédures montrent comment approvisionner l’application ET déployer votre code dessus de bout en bout. Vous trouverez également des options supplémentaires pour l’approvisionnement des ressources Azure dans la section [Automatiser le déploiement à l’aide des outils de ligne de commande](#automate).
     
## <a name="ftp"></a>Déploiement via FTP par copie manuelle des fichiers dans Azure
Si vous êtes habitué à copier manuellement votre contenu web sur un serveur web, vous pouvez utiliser un utilitaire [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) pour copier des fichiers, notamment l’Explorateur Windows ou [FileZilla](https://filezilla-project.org/).

Les avantages de la copie manuelle de fichiers sont :

- la simplicité d’utilisation d’outils FTP familiers.
- vous savez exactement où vont vos fichiers.
- la sécurité ajoutée avec FTPS.

Les inconvénients de la copie manuelle de fichiers sont :

- Vous devez maîtriser le déploiement des fichiers dans les répertoires appropriés dans App Service.
- Aucun contrôle de version pour la restauration en cas de défaillance.
- Aucun historique de déploiement intégré pour résoudre les problèmes de déploiement.
- Des délais de déploiement potentiellement plus longs car de nombreux outils FTP ne sont pas dotés de la copie différentielle, et se contentent de copier tous les fichiers.

### <a name="howtoftp"></a>Comment assurer le déploiement par copie manuelle des fichiers dans Azure
La copie de fichiers dans Azure fait appel à quelques étapes simples :

1. En supposant que vous disposez déjà des informations d’identification de déploiement, obtenez les informations de connexion FTP en accédant à **Paramètres** > **Propriétés**, puis en copiant les valeurs correspondant à **Utilisateur FTP/Développement**, **Nom d’hôte FTP** et **Nom de l’hôte FTPS**. Copiez la valeur **Utilisateur FTP/déploiement** telle qu'elle est affichée par le portail Azure, y compris le nom de l'application, afin de fournir le contexte approprié pour le serveur FTP.
2. À partir de votre client FTP, utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application.
3. Copiez vos fichiers et la structure de répertoire qui leur correspond dans le répertoire [**/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou dans le répertoire **/site/wwwroot/App\_Data/Jobs/** pour les tâches web).
4. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement.

Pour plus d'informations, consultez les ressources suivantes :

* [Créer une application Web PHP-MySQL dans Azure App Service et la déployer à l’aide de FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Déploiement par synchronisation avec un dossier cloud
Une bonne alternative à la [copie manuelle de fichiers](#ftp) consiste à synchroniser les fichiers et les dossiers avec App Service en utilisant un service de stockage hébergé sur le cloud comme OneDrive et Dropbox. La synchronisation avec un dossier de cloud utilise le processus de Kudu pour le déploiement (voir [Vue d’ensemble du processus de déploiement](#overview)).

Les avantages de la synchronisation avec un dossier cloud sont :

- la simplicité de déploiement. Les services tels que OneDrive et Dropbox fournissent des clients de synchronisation avec un ordinateur de bureau, de sorte que votre répertoire de travail local est également votre répertoire de déploiement.
- le déploiement sur un clic.
- Toutes les fonctionnalités du moteur de déploiement Kudu sont disponibles (par exemple, la restauration de package, l’automatisation).

Les inconvénients de la synchronisation avec un dossier cloud sont :

- Aucun contrôle de version pour la restauration en cas de défaillance.
- Aucun déploiement automatisé, une synchronisation manuelle est nécessaire.

### <a name="howtodropbox"></a>Comment déployer par synchronisation avec un dossier cloud
Dans le [portail Azure](https://portal.azure.com), vous pouvez désigner un dossier pour la synchronisation du contenu dans votre stockage cloud OneDrive ou Dropbox, travailler avec votre code d’application et votre contenu dans ce dossier et à synchroniser avec App Service sur un simple clic.

* [Synchronisez le contenu à partir d’un dossier cloud dans Azure App Service](app-service-deploy-content-sync.md).

## <a name="continuousdeployment"></a>Déploiement en continu à partir d’un système de contrôle source hébergé sur le cloud
Si votre équipe de développement utilise un service de gestion de code source (SCM) basé sur le cloud [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com) ou [BitBucket](https://bitbucket.org/), vous pouvez configurer le Service d’application pour l’intégrer à votre référentiel et déployer en continu.

Les avantages du déploiement à partir d’un système de contrôle source sont :

- Contrôle de version permettant une restauration.
- Possibilité de configurer un déploiement continu pour les référentiels Git (et Mercurial le cas échéant).
- Le déploiement de branche spécifique permet de déployer différentes branches à différents [emplacements](web-sites-staged-publishing.md).
- Toutes les fonctionnalités du moteur de déploiement Kudu sont disponibles (par exemple, le contrôle de version de déploiement, la restauration, la restauration de package, l’automatisation).

Inconvénient du déploiement à partir d’un service de contrôle de source de cloud :

- Implique une certaine connaissance du service SCM respectif.

###<a name="vsts"></a>Comment déployer en continu à partir d’un système de contrôle source hébergé sur le cloud
Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer un déploiement continu à partir de GitHub, Bitbucket et Visual Studio Team Services.

* [Déploiement continu vers Azure App Service](app-service-continuous-deployment.md).

## <a name="localgitdeployment"></a>Déployer à partir de Git local
Si votre équipe de développement utilise un service de gestion du code source local (SCM) basé sur Git, vous pouvez le configurer comme une source de déploiement pour App Service.

Avantages du déploiement à partir de Git local :

- Contrôle de version permettant une restauration.
- Le déploiement de branche spécifique permet de déployer différentes branches à différents [emplacements](web-sites-staged-publishing.md).
- Toutes les fonctionnalités du moteur de déploiement Kudu sont disponibles (par exemple, le contrôle de version de déploiement, la restauration, la restauration de package, l’automatisation).

Inconvénient du déploiement à partir de Git local :

- Implique une certaine connaissance du système SCM respectif.
- Aucune solution clés en main pour un déploiement continu.

###<a name="vsts"></a>Comment déployer à partir de Git local
Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer un déploiement Git local.

* [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md).
* [Publication vers Web Apps à partir de n’importe quel référentiel git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html) (en anglais).

## Déployer avec un IDE
Si vous utilisez déjà [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) avec un [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/) ou d’autres suites IDE comme [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org) et [IntelliJ IDEA](https://www.jetbrains.com/idea/), vous pouvez assurer un déploiement sur Azure directement depuis votre IDE. Cette option est idéale pour un développeur individuel.

Visual Studio prend en charge les trois processus de déploiement (FTP, Git et Web Deploy), selon votre préférence, tandis que les autres IDE peuvent déployer App Service s’ils sont dotés de l’intégration de FTP ou de Git (voir [Vue d’ensemble du déploiement d’Azure App Service](#overview)).

Les avantages du déploiement à l’aide d’un IDE sont :

- La réduction potentielle des outils de votre cycle de vie d’application de bout en bout. Développer, déboguer, suivre et déployer votre application dans Azure sans sortir de votre IDE.

Les inconvénients du déploiement à l’aide d’un IDE sont les suivants :

- Une complexité accrue des outils.
- Nécessite toujours un système de contrôle de code source en cas de projet d’équipe.

<a name="vspros"></a> Voici d’autres avantages du déploiement à l’aide de Visual Studio avec le SDK Azure :

- Le SDK Azure favorise nettement les ressources Azure dans Visual Studio. Créer, supprimer, modifier, démarrer et arrêter les applications, interroger la base de données SQL du serveur principal, déboguer l’application Azure en direct, entre autres.
- Modification en direct des fichiers de code sur Azure.
- Débogage en direct des applications sur Azure.
- Explorateur Azure intégré.
- Déploiement différentiel uniquement.

###<a name="vs"></a>Déploiement direct à partir de Visual Studio

* [Prise en main d'Azure et ASP.NET](web-sites-dotnet-get-started.md). Explique comment créer et déployer un projet Web ASP.NET MVC simple en utilisant Visual Studio et Web Deploy.
* [Déployer des tâches Web à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md). Explique comment configurer les projets d'application console pour qu'ils se déploient sous forme de tâches web WebJobs.
* [Créer une application ASP.NET MVC 5 avec authentification et base de données SQL et la déployer dans Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Explique comment créer et déployer un projet Web ASP.NET MVC avec une base de données SQL, en utilisant Visual Studio, Web Deploy et Entity Framework Code First Migrations.
* [Déploiement Web ASP.NET en utilisant Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Une série de didacticiels en 12 parties présentant un ensemble de tâches de déploiement plus complet que le reste de cette liste. Certaines fonctionnalités de déploiement Azure ont été ajoutées depuis la rédaction de ce didacticiel. Cependant, les notes ajoutées par la suite expliquent les éléments manquants.
* [Déploiement d'un site Web ASP.NET dans Azure avec Visual Studio 2012 directement depuis un référentiel Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Montre comment déployer un projet Web ASP.NET dans Visual Studio, en utilisant le plug-in Git pour valider le code sur Git et connecter Azure au référentiel Git. À partir de Visual Studio 2013, la prise en charge Git est intégrée et ne nécessite pas l’installation d’un plug-in.

###<a name="aztk"></a>Déploiement à l’aide des boîtes à outils Azure pour Eclipse et IntelliJ IDEA

Microsoft permet de déployer des applications web sur Azure directement à partir d’Eclipse et d’IntelliJ via la [boîte à outils Azure pour Eclipse](../azure-toolkit-for-eclipse.md) et la [boîte à outils Azure pour IntelliJ](../azure-toolkit-for-intellij.md). Les didacticiels suivants illustrent les étapes impliquées dans le déploiement d’une simple application web « Hello World » sur Azure à l’aide de l’IDE :

*  [Créer une application web « Hello World » pour Azure dans Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md). Ce didacticiel vous montre comment utiliser le Kit de ressources Azure pour Eclipse pour créer une application web Hello World pour Azure.
*  [Créer une application web « Hello World » pour Azure dans IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). Ce didacticiel vous montre comment utiliser le Kit de ressources Azure pour Eclipse pour créer une application web Hello World pour Azure.


## <a name="automate"></a>Automatiser le déploiement à l’aide d’outils en ligne de commande

* [Automatiser le déploiement avec MSBuild](#msbuild)
* [Copier des fichiers avec des scripts et des outils FTP](#ftp)
* [Automatiser le déploiement avec Windows PowerShell](#powershell)
* [Automatiser le déploiement avec l’API de gestion .NET](#api)
* [Déploiement à partir de l’interface de ligne de commande Azure](#cli)
* [Déploiement à partir de la ligne de commande Web Deploy](#webdeploy)
* [Utilisation des scripts de commandes FTP](http://support.microsoft.com/kb/96269).
 
Une autre option de déploiement consiste à utiliser un service basé sur le cloud comme [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Pour plus d'informations, consultez la page [Déploiement d’applications ASP.NET sur des sites web Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatiser le déploiement avec MSBuild

Si vous utilisez l'[IDE Visual Studio](#vs) pour le développement, vous pouvez utiliser [MSBuild](http://msbuildbook.com/) pour automatiser tout ce que vous pouvez faire dans votre IDE. Vous pouvez configurer MSBuild pour utiliser [Web Deploy](#webdeploy) ou le [FTP/FTPS](#ftp) pour copier vos fichiers. Web Deploy peut également automatiser de nombreuses autres tâches de déploiement, comme le déploiement des bases de données.

Pour plus d'informations sur le déploiement en ligne de commande avec MSBuild, consultez les ressources suivantes :

* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment) (en anglais). Une série d'une dizaine de didacticiels sur le déploiement dans Azure avec Visual Studio. Montre comment utiliser la ligne de commande pour le déploiement après la configuration de profils de publication dans Visual Studio.
* [Présentation de Microsoft Build Engine : utilisation de MSBuild et Team Foundation Build](http://msbuildbook.com/). Manuel comportant des chapitres sur l'utilisation de MSBuild pour le déploiement.

###<a name="powershell"></a>Automatiser le déploiement avec Windows PowerShell

Vous pouvez utiliser des fonctions de déploiement MSBuild ou FTP à partir de [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Ce faisant, vous pouvez également utiliser une collection de cmdlets Windows PowerShell facilitant l'appel de l'API de gestion REST Azure.

Pour plus d’informations, consultez les ressources suivantes :

* [Déployer une application Web liée à un référentiel GitHub](app-service-web-arm-from-github-provision.md)
* [Mettre en service une application Web avec une base de données SQL](app-service-web-arm-with-sql-database-provision.md)
* [Mise en service et déploiement de microservices de manière prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
* [Tout automatiser (développement d'applications de cloud concrètes avec Azure)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Chapitre d'un livre électronique expliquant comment l'exemple d'application contenu dans le livre utilise des scripts Windows PowerShell pour créer un environnement de test Azure et y procéder à un déploiement. Consultez la section [Ressources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) pour obtenir des liens vers une documentation Azure PowerShell supplémentaire.
* [Utilisation des scripts Windows PowerShell pour la publication dans des environnements de développement et de test](../vs-azure-tools-publishing-using-powershell-scripts.md). Explique comment utiliser les scripts Windows PowerShell générés par Visual Studio.

###<a name="api"></a>Automatiser le déploiement avec l’API de gestion .NET

Vous pouvez écrire un code C# pour utiliser des fonctions MSBuild ou FTP pour vos déploiements. Ce faisant, vous pouvez accéder à l'API REST de gestion Azure pour utiliser des fonctions de gestion de site.

Pour plus d'informations, consultez les ressources suivantes :

* [Tout automatiser avec les bibliothèques de gestion Azure et .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Présente l’API de gestion .NET et fournit des liens vers d’autres documentations.

###<a name="cli"></a>Déploiement à partir de l’interface de ligne de commande Azure (Azure CLI)

Vous pouvez utiliser la ligne de commande avec des ordinateurs Windows, Mac ou Linux pour vos déploiements en utilisant le FTP. Ce faisant, vous pouvez également accéder à l'API de gestion REST Azure en utilisant l'interface en ligne de commande interplateforme.

Pour plus d’informations, consultez les ressources suivantes :

* [Outils en ligne de commande Azure](/downloads/#cmd-line-tools). Page du portail Azure.com qui fournit des informations sur les outils en ligne de commande.

###<a name="webdeploy"></a>Déploiement à partir de la ligne de commande Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) est un logiciel Microsoft pour le déploiement dans IIS qui fournit des fonctionnalités de synchronisation des fichiers intelligentes et effectue ou coordonne également de nombreuses autres tâches liées au déploiement qui ne peuvent pas être automatisées lorsque vous utilisez le FTP. Par exemple, Web Deploy peut déployer une nouvelle base de données ou mettre à jour une base de données existante avec votre application web. Web Deploy peut également réduire le délai de mise à jour d'un site existant, en copiant uniquement les fichiers modifiés. Microsoft Visual Studio et Team Foundation Server prennent en charge le logiciel Web Deploy intégré, mais vous pouvez également utiliser Web Deploy directement depuis la ligne de commande pour automatiser le déploiement. Les commandes Web Deploy sont très efficaces, mais leur apprentissage peut être long.

Pour plus d'informations, consultez les ressources suivantes :

* [Web Apps en toute simplicité : déploiement](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/) (en anglais). Blog de David Ebbo sur un outil, qu’il a conçu pour faciliter l’utilisation de Web Deploy.
* [Outils de déploiement Web](http://technet.microsoft.com/library/dd568996). Documentation officielle sur le site Microsoft TechNet. Ancien, mais constitue un bon point de départ.
* [Utilisation de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentation officielle sur le site Microsoft IIS.NET. Également ancien, mais constitue un bon point de départ.
* [Déploiement Web ASP.NET en utilisant Visual Studio : déploiement en ligne de commande](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment) (en anglais). MSBuild est le moteur de génération utilisé par Visual Studio, mais il peut aussi être utilisé à partir de la ligne de commande pour déployer des applications web dans Web Apps. Ce didacticiel fait partie d'une série traitant principalement des déploiements Visual Studio.

##<a name="nextsteps"></a>Étapes suivantes

Dans certains cas, vous souhaiterez probablement pouvoir basculer facilement entre une version intermédiaire et de production de votre application. Pour plus d’informations, consultez [Configurer des environnements intermédiaires pour les applications Web dans Azure App Service](web-sites-staged-publishing.md).

Posséder un programme de sauvegarde et de restauration établi représente une partie essentielle de tout flux de travail de déploiement. Pour plus d’informations sur la fonctionnalité de sauvegarde et de restauration d’App Service, consultez [Sauvegarder une application web dans Azure App Service](web-sites-backup.md).

Pour plus d’informations sur l’utilisation du contrôle d’accès en fonction du rôle Azure afin de gérer l’accès au déploiement App Service, consultez [RBAC and Web App Publishing](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/) (RBAC et publication Web Apps).


 

<!---HONumber=AcomDC_0921_2016-->