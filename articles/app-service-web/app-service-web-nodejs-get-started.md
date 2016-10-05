<properties
	pageTitle="Prise en main des applications web Node.js dans Azure App Service"
	description="Découvrez comment déployer une application Node.js sur une application web dans Azure App Service."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="07/01/2016"
	ms.author="cephalin"/>

# Prise en main des applications web Node.js dans Azure App Service

[AZURE.INCLUDE [onglets](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel explique comment créer une application [Node.js] simple et la déployer dans [Azure App Service] à partir d’un environnement de ligne de commande comme cmd.exe ou bash. Les instructions de ce didacticiel s’appliquent à tous les systèmes d’exploitation pouvant exécuter Node.js.

<a name="prereq"></a>
## Composants requis

- [Node.JS]
- [Bower]
- [Yeoman]
- [Git]
- [Interface de ligne de commande Azure]
- Un compte Microsoft Azure Si vous ne possédez pas de compte, vous pouvez [vous inscrire à un essai gratuit] ou [activer les avantages de votre abonnement Visual Studio].

## Création et déploiement d’une application Node.js simple

1. Ouvrez le terminal de ligne de commande de votre choix et installez le [générateur Express pour Yeoman].

        npm install -g generator-express

2. Exécutez la commande `CD` dans un répertoire de travail et générez une application expresse en utilisant la syntaxe suivante :

        yo express
        
    Choisissez les options suivantes lorsque vous y êtes invité :

    `? Would you like to create a new directory for your project?` **Yes** `? Enter directory name` **{nom\_app}** `? Select a version to install:` **MVC** `? Select a view engine to use:` **Jade** `? Select a css preprocessor to use (Sass Requires Ruby):` **None** `? Select a database to use:` **None** `? Select a build tool to use:` **Grunt**

3. Exécutez la commande `CD` dans le répertoire racine de votre nouvelle application et démarrez-la pour vérifier qu’elle s’exécute correctement dans votre environnement de développement :

        npm start

    Dans votre navigateur, accédez à <http://localhost:3000> pour vérifier que la page d’accueil Express s’affiche. Une fois que vous êtes sûr que votre application s’exécute correctement, utilisez la commande `Ctrl-C` pour l’arrêter.
    
1. Passez en mode ASM et connectez-vous à Azure (vous avez besoin de [l’interface de ligne de commande Azure](#prereq)) :

        azure config mode asm
        azure login

    Suivez les instructions de l’invite pour poursuivre la connexion à un compte Microsoft associé à votre abonnement Azure.

2. Assurez-vous que vous vous trouvez toujours dans le répertoire racine de votre application, puis créez la ressource d’application App Service dans Azure avec un nom d’application unique à l’aide de la commande suivante. Par exemple : http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Suivez les instructions pour sélectionner une région Azure vers laquelle effectuer le déploiement. Si vous n’avez jamais configuré les informations d’identification de déploiement Git/FTP pour votre abonnement Azure, vous êtes invité à les créer.

3. Ouvrez le fichier ./config/config.js à partir de la racine de votre application, et définissez le port de production sur `process.env.port` ; votre propriété `production` de l’objet `config` doit ressembler à l’exemple suivant :

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Cela permet à votre application Node.js de répondre aux demandes Web sur le port par défaut sur lequel iisnode écoute.
    
4. Ouvrez ./package.json et ajoutez la propriété `engines` pour [spécifier la version de Node.js](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Enregistrez vos modifications, puis utiliser git pour déployer votre application dans Azure :

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Le générateur Express fournit un fichier .gitignore, de sorte que votre commande `git push` ne consomme pas de bande passante en tentant de charger le répertoire node\_modules/.

5. Pour terminer, lancez votre application Azure en direct dans le navigateur :

        azure site browse

    Vous devez maintenant voir votre application web Node.js s’exécuter dans Azure App Service.
    
    ![Exemple d’accès à l’application déployée.][deployed-express-app]

## Mise à jour de votre application web Node.js

Pour mettre à jour votre application web Node.js dans App Service, il vous suffit d’exécuter `git add`, `git commit` et `git push` comme vous l’avez fait lors du premier déploiement de votre application web.
     
## Comment le App Service déploie votre application Node.js

Azure App Service utilise [iisnode] pour exécuter les applications Node.js. L’interface de ligne de commande (CLI) d’Azure et le moteur Kudu (déploiement Git) fonctionnent en parallèle afin de simplifier votre travail de développement et de déploiement d’applications Node.js à partir de la ligne de commande.

- `azure site create --git` reconnaît le schéma Node.js courant de server.js ou d’app.js, et crée un fichier iisnode.yml dans votre répertoire racine. Vous pouvez ensuite utiliser ce fichier pour personnaliser iisnode.
- Sur la ligne `git push azure master`, Kudu automatise les tâches de déploiement suivantes :

    - Si le fichier package.json se trouve à la racine du référentiel, exécutez la commande `npm install --production`.
    - Générez un fichier Web.config pour iisnode, qui pointe vers votre script de démarrage dans le fichier package.json (par exemple, server.js ou app.js).
    - Personnalisez le fichier Web.config pour préparer votre application au débogage avec Node Inspector.
    
## Utilisation d’une infrastructure Node.js

Si vous utilisez une infrastructure Node.js répandue, telle que [Sails.js][SAILSJS] ou [MEAN.js][MEANJS] pour développer des applications, vous pouvez les déployer dans App Service. Les infrastructures Node.js les plus populaires ont des particularités bien spécifiques et leurs dépendances de package sont constamment mises à jour. Avec App Service, cependant, les journaux stdout et stderr sont mis à votre disposition afin que vous sachiez exactement ce qui se passe au niveau de votre application et que vous puissiez apporter les modifications nécessaires. Pour plus d’informations, consultez [Obtenir des journaux stdout et stderr à partir d’iisnode](#iisnodelog).

Consultez les didacticiels suivant pour savoir comment utiliser une infrastructure spécifique dans App Service :

- [Deploy a Sails.js web app to Azure App Service (Déployer une application web Sails.js dans Azure App Service)]
- [Créer une application de conversation instantanée Node.js avec Socket.IO dans Azure App Service]
- [Utilisation de io.js avec Azure App Service Web Apps]

<a name="version"></a>
## Utilisation d’un moteur Node.js spécifique

Dans votre flux de travail classique, vous demandez à App Service d’utiliser un moteur Node.js spécifique de la même manière que dans le fichier package.json. Par exemple :

    "engines": {
        "node": "6.6.0"
    }, 

Le moteur de déploiement Kudu détermine le moteur Node.js à utiliser dans l’ordre suivant :

- Examinons tout d’abord iisnode.yml pour voir si `nodeProcessCommandLine` est spécifié. Si tel est le cas, vous pouvez l’utiliser.
- Ensuite, examinons le fichier package.json pour voir si `"node": "..."` est spécifié dans l’objet `engines`. Si tel est le cas, vous pouvez l’utiliser.
- Par défaut, choisissez une version de Node.js par défaut.

>[AZURE.NOTE] Il est recommandé de définir explicitement le moteur Node.js souhaité. La version de Node.js par défaut peut changer, et vous pouvez obtenir des erreurs dans votre application web Azure, car la version de Node.js par défaut ne convient pas à votre application.

<a name="iisnodelog"></a>
## Obtenir des journaux stdout et stderr à partir d’iisnode

Pour lire les journaux iisnode, suivez les étapes ci-après.

> [AZURE.NOTE] Après ces étapes, il se peut que les fichiers journaux ne soient générés que lorsqu’une erreur se produit.

1. Ouvrez le fichier iisnode.yml fourni par l’interface de ligne de commande Azure.

2. Spécifiez les deux paramètres suivants :

        loggingEnabled: true
        logDirectory: iisnode
    
    Ensemble, ces paramètres demandent à iisnode dans App Service de placer ses sorties stdout et stderror dans le répertoire D:\\home\\site\\wwwroot**iisnode**.

3. Enregistrez vos modifications, puis répercutez-les dans Azure à l’aide des commandes Git suivantes :

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode est désormais configuré. Les étapes suivantes vous montrent comment accéder à ces journaux.
     
4. Dans votre navigateur, accédez à la console de débogage Kudu correspondant à votre application, c’est-à-dire :

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Cette URL diffère de celle de l’application web par l’ajout de « *.scm.* » au nom DNS. Si vous omettez cet ajout à l’URL, vous obtiendrez une erreur 404.

5. Accédez à D:\\home\\site\\wwwroot\\iisnode

    ![Accès à l’emplacement des fichiers journaux iisnode.][iislog-kudu-console-find]

6. Cliquez sur l’icône **Modifier** du journal que vous souhaitez lire. Vous pouvez également cliquer sur **Télécharger** ou **Supprimer** si vous le souhaitez.

    ![Ouverture d’un fichier journal iisnode.][iislog-kudu-console-open]

    Vous pouvez maintenant consulter le journal pour vous aider à résoudre les problèmes liés à votre déploiement d’App Service.
    
    ![Examen d’un fichier journal iisnode.][iislog-kudu-console-read]

## Débogage de votre application avec Node-Inspector

Si vous utilisez Node-Inspector pour déboguer vos applications Node.js, vous pouvez l’utiliser pour votre application App Service active. Node-Inspector est préinstallé avec l’installation d’iisnode pour App Service. Si vous utilisez Git pour le déploiement, le fichier Web.config généré automatiquement à partir de Kudu contient déjà toute la configuration dont vous avez besoin pour activer Node-Inspector.

Pour activer Node-Inspector, procédez comme suit :

1. Ouvrez iisnode.yml à la racine de votre référentiel et spécifiez les paramètres suivants :

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Enregistrez vos modifications, puis répercutez-les dans Azure à l’aide des commandes Git suivantes :

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Il vous suffit maintenant d’accéder au fichier de démarrage de votre application, comme spécifié par le script de démarrage contenu dans votre fichier package.json en ajoutant /debug à l’URL. Par exemple,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## Autres ressources

- [Spécification d'une version Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md)
- [Meilleures pratiques et guide de dépannage pour les applications Node.js sur Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Débogage d’une application web Node.js dans Azure Web Service](web-sites-nodejs-debug.md)
- [Utilisation de modules Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)
- [Azure App Service Web Apps : Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centre de développement Node.js](/develop/nodejs/)
- [Prise en main des applications web dans Azure App Service.](app-service-web-get-started.md)
- [Exploring the Super Secret Kudu Debug Console (Exploration de la console de débogage Kudu Super Secret)]

<!-- URL List -->

[Interface de ligne de commande Azure]: ../xplat-cli-install.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[activer les avantages de votre abonnement Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Créer une application de conversation instantanée Node.js avec Socket.IO dans Azure App Service]: ./web-sites-nodejs-chat-app-socketio.md
[Deploy a Sails.js web app to Azure App Service (Déployer une application web Sails.js dans Azure App Service)]: ./app-service-web-nodejs-sails.md
[Exploring the Super Secret Kudu Debug Console (Exploration de la console de débogage Kudu Super Secret)]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[générateur Express pour Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Utilisation de io.js avec Azure App Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[vous inscrire à un essai gratuit]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png

<!---HONumber=AcomDC_0928_2016-->