<properties
	pageTitle="Didacticiel : Opérations de développement avec le portail Azure | Microsoft Azure"
	description="Découvrez les divers flux de travail DevOps dans le portail Azure."
	services="azure-portal"
	documentationCenter=""
	authors="mlearned"
	manager="douge"
	editor="mlearned"/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/05/2016"
	ms.author="mlearned"/>

# Didacticiel : Opérations de développement avec le portail Azure

La plateforme Azure regorge de flux de travail DevOps flexibles. Dans ce didacticiel, vous apprendrez à exploiter les fonctionnalités du portail Azure pour développer, tester, déployer, dépanner, analyser et gérer les applications en cours d’exécution. Ce didacticiel se concentre sur les éléments suivants :

1.  Création d’une application web et possibilité de déploiement continu

2.  Développement et test d’une application

3.  Surveillance et dépannage d’une application

4.  Tâches de gestion des applications générales

## Création d’une application web et possibilité de déploiement continu

Créez une application web avec [Azure App Service](https://azure.microsoft.com/services/app-service/), et utilisez-la pour la suite de ce didacticiel. Vous allez d’abord activer le déploiement continu à partir de votre référentiel de code source dans notre environnement Azure en cours d’exécution.

1.  Se connecter au portail Azure

2.  Choisissez **App Services** &gt; **icône Ajouter**, puis entrez un nom, choisissez votre abonnement et créez un nouveau groupe de ressources qui servira de conteneur pour le service.

    Les groupes de ressources vous permettent de gérer différents aspects de la solution, telles que la facturation, les déploiements et l’analyse, le tout grâce à un groupe unique via [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/).

	![image1][image1]


3.  Après quelques instants, votre App Service est créé. Prenez quelques minutes pour explorer les différentes options de menu du service dans le portail.

	![image2][image2]

4.  Cliquez sur l'URL. Notez les divers outils et référentiels disponibles. Vous pouvez également utiliser les langages et infrastructures de votre choix, notamment .NET, Java et Ruby.

	![image3][image3]

3.  Le portail Azure simplifie le déploiement continu, en le limitant à quelques étapes simples. Dans le portail Azure, choisissez les paramètres en utilisant l’icône de l’App Service que vous venez de créer.

    ![image4][image4]

    Dans le panneau qui s’ouvre sur la droite, accédez à la section de publication.

    ![image5][image5]

4.  Ensuite, configurez certains paramètres pour activer le déploiement continu pour l’application. Cliquez sur Source du déploiement, puis sur Choisir la source. Notez les diverses sources de référentiel disponibles.

	![image6][image6]

1.  Pour cet exemple, sélectionnez GitHub. Vous pouvez également choisir le référentiel de votre choix et configurer les informations d’identification.

    ![image7][image7]

2.  Après la configuration des autorisations pour votre référentiel, vous pouvez choisir un projet et une branche à déployer. Plusieurs exemples fictifs sont répertoriés ci-dessous.

    ![image8][image8]

3.  Une fois votre projet et votre branche choisis, cliquez sur OK. Des notifications de déploiement doivent commencer à apparaître.

    ![image9][image9]

4.  Revenez à GitHub pour voir le webhook qui a été créé afin d’intégrer le référentiel de contrôle du code source à Azure. Le portail Azure permet l’intégration à GitHub en seulement quelques étapes simples.

    ![image10][image10]

5.  Pour illustrer le déploiement continu, ajoutez rapidement du contenu dans le référentiel. Pour obtenir un exemple simple, ajoutez un exemple de fichier texte à un référentiel GitHub. Vous pouvez utiliser .NET, Ruby, Python ou un autre type d’application avec l’App Service. N’hésitez pas à ajouter un fichier texte ou une application ASP.NET MVC, Java ou Ruby au référentiel de votre choix.

    ![image11][image11]

6.  Après avoir effectué des modifications dans votre référentiel, le démarrage d’un nouveau déploiement est signalé dans la zone de notification du portail. Si vous ne voyez aucune modification rapidement après leur validation dans votre référentiel, cliquez sur Synchroniser.

    ![image12][image12]

7.  À ce stade, si vous essayez de charger la page pour l’App Service, il se peut que vous receviez une erreur 403. Dans cet exemple, elle apparaît car il n’existe aucune configuration de document par défaut pour la page, telle qu’un fichier index.htm ou default.html. Vous pouvez résoudre rapidement ce problème à l’aide des outils du portail Azure. Dans le portail Azure, choisissez Paramètres &gt; Paramètres de l’application.

	 ![image13][image13]

8.  Un panneau regroupant les paramètres de l’application s’ouvre. Entrez le nom de la page « SamplePage.html » et cliquez sur Enregistrer. Prenez quelques minutes pour explorer les autres paramètres.

	![image14][image14]

9.  Vous pouvez actualiser l’URL de votre navigateur afin d’être sûr de voir les modifications attendues. Dans ce cas, un texte simple remplit désormais la page. Chaque modification supplémentaire apportée au référentiel entraîne un déploiement automatique.

    ![image15][image15]

    L’activation du déploiement continu avec le portail Azure est une procédure simple. Vous pouvez également créer des pipelines de publication plus complexes et utiliser de nombreuses autres techniques avec contrôle de code source existant et systèmes d’intégration continue à déployer sur Azure, par exemple en exploitant les systèmes de gestion automatisée de la création et de la publication.

## Développement et test d’une application

Ensuite, apportez des modifications à la base de code et déployez-les rapidement. Vous configurerez également des tests de performance pour l’application web.

1.  Dans le portail Azure, sélectionnez App Services dans le volet de navigation, puis recherchez votre App Service.

    ![image16][image16]

2.  Cliquez sur Outils

    ![image17][image17]

3.  Remarquez la catégorie Développer sous Outils. Cette catégorie inclut plusieurs outils utiles qui nous permettront de travailler avec des applications sans quitter le portail Azure. Cliquez sur Console.

    ![image18][image18]

4.  Dans la fenêtre de console, vous pouvez émettre des commandes en direct pour votre application. Tapez la commande dir et appuyez sur Entrée. Notez que les commandes nécessitant des privilèges élevés ne fonctionnent pas.

    ![image19][image19]

5.  Revenez à la catégorie Développer et choisissez Visual Studio Online. Remarque : Visual Studio Online devient Visual Studio Team Services.

    ![image20][image20]

6.  Passez à la fonctionnalité d’édition dans le navigateur de votre application.

    ![image21][image21]

7.  Une extension web pour votre application s’installe. Les extensions permettent d’ajouter rapidement et facilement des fonctionnalités aux applications dans Azure. Découvrez certains autres types d’extension disponibles dans la capture d’écran ci-dessous.

    ![image22][image22]

8.  Une fois que l’extension Visual Studio Online est installée, cliquez sur OK.

    ![image23][image23]

9.  Un onglet de navigateur s’ouvre pour afficher un IDE de développement directement dans le navigateur. Veuillez noter que l’exemple ci-dessous utilise Chrome.

    ![image24][image24]

10. Vous pouvez effectuer plusieurs activités telles que la modification de fichiers, l’ajout de fichiers et dossiers, et le téléchargement de contenu à partir du site en activité. Apportez une modification rapide au fichier SamplePage.html.

    ![image25][image25]

11. Dans quelques instants, les modifications seront automatiquement enregistrées. Si vous revenez à la page, vous pourrez voir les modifications. Gardez à l’esprit que les modifications en direct ne sont généralement pas appropriées pour les environnements de production. Toutefois, les outils facilitent largement la modification rapide des environnements de développement et de test.

    ![image26][image26]

    ![image27][image27]

12. Revenez au panneau Outils et, sous la catégorie Développer, cliquez sur Test de performance.

    ![image28][image28]

13. Vous devez définir un compte Team Services. Pour plus d’informations, consultez la page [Créer un compte Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)

14. Cliquez sur Nouveau pour créer un test de performance.

    ![image29][image29]

    Configurez les différentes valeurs, puis cliquez sur Exécuter le test en bas de la boîte de dialogue pour lancer un test de performance.

    ![image30][image30]

	![image31][image31]

1.  Une fois le test démarré, vous pouvez surveiller l’état.

    ![image32][image32]

    Lorsque le test est terminé, cliquez sur le résultat pour afficher plus de détails.

    ![image33][image33]

2.  Dans cet exemple, vous avez créé une courte série de tests. La quantité de données à analyser est donc limitée, mais vous pouvez voir différentes mesures et réexécuter votre test à partir de cette vue. Le portail Azure facilite la création, l’exécution et l’analyse des tests de performance web. Les captures d’écran ci-dessous présentent les données de performances.

    ![image34][image34]

    ![image35][image35]

    ![image36][image36]

## Surveillance et dépannage d’une application

Azure fournit de nombreuses fonctionnalités de surveillance et de dépannage pour les applications en cours d’exécution.

1.  Dans le portail Azure de votre application web, sélectionnez Outils.

    ![image37][image37]

2.  Sous la catégorie Dépannage, notez les différents choix d’utilisation des outils pour résoudre les problèmes potentiels avec une application en cours d’exécution. Vous pouvez par exemple analyser le trafic HTTP en direct, activer la réparation spontanée, afficher les journaux et bien plus encore.

    ![image38][image38]

3.  Sélectionnez Métriques du site pour obtenir rapidement une vue de certains codes HTTP.

    ![image39][image39]

4.  Sélectionnez Diagnostics en tant que service. Choisissez votre type d’application, puis sélectionnez Exécuter.

    ![image40][image40]

    La collecte commence.

    ![image41][image41]

5.  Vous pouvez choisir le journal approprié pour diagnostiquer les problèmes potentiels. Vous devez activer la journalisation pour voir toutes les options de données disponibles, comme les journaux HTTP.

    ![image42][image42]

    En cliquant sur le fichier de vidage de la mémoire, vous pouvez télécharger et analyser un rapport d’analyse DebugDiag pour détecter les problèmes potentiels.

    ![image43][image43]

6.  Pour afficher davantage de données, vous devez activer la journalisation supplémentaire. Dans le portail Azure, accédez à l’application web et sélectionnez Paramètres.

    ![image44][image44]

7.  Accédez à la catégorie Fonctionnalités, puis choisissez Journaux de diagnostic.

  	 ![image45][image45]

8.  Notez les différentes options de journalisation. Activez la journalisation du serveur web et cliquez sur Enregistrer.

    ![image46][image46]

9.  Revenez à la zone des outils de l’application et sélectionnez Diagnostics en tant que service, puis cliquez sur Exécuter pour exécuter de nouveau la collecte de données.

    ![image47][image47]

10. Lorsque le paramètre de journalisation HTTP est activé, vous voyez les données collectées pour les journaux HTTP.

    ![image48][image48]

11. En cliquant sur le journal des fichiers HTML, vous générez un rapport complet basé sur navigateur, pour un examen approfondi.

    ![image49][image49]

12. Revenez à la section Outils dans le portail Azure de l’application. Accédez à la section Outils et choisissez Explorateur de processus.

    ![image50][image50]

13. En choisissant Explorateur de processus, vous pouvez afficher plus d’informations sur les processus en cours d’exécution. Comme indiqué ci-dessous, vous pouvez explorer les processus, et même les interrompre, à partir du portail Azure.

    ![image51][image51]

    ![image52][image52]

14. Revenez au panneau Paramètres, sur la gauche. Cliquez sur Nouvelle demande de support.

    ![image53][image53]

15. Dans le panneau de droite, vous pouvez fournir des informations sur les problèmes, saisir des coordonnées et télécharger des données de diagnostic. Le portail Azure facilite la collaboration avec le support Microsoft.

    ![image54][image54]

    ![image55][image55]

    Le portail Azure contribue à offrir une utilisation puissante et familière des outils, afin de contrôler et de dépanner nos applications en cours d’exécution. Vous pouvez également prendre rapidement des mesures en effectuant des tâches telles que le recyclage des processus, l’activation et la désactivation de différentes collectes de données, et même l’intégration au support professionnel Microsoft.

## Gestion des applications générales

Lorsque vous gérez des applications, vous devez souvent effectuer de nombreuses tâches, telles que la configuration des stratégies de sauvegarde, l’implémentation et la gestion des fournisseurs d’identité, et la configuration des contrôles d’accès basés sur les rôles. Comme pour les autres expériences DevOps, la plateforme Azure intègre ces tâches directement dans le portail.

1.  Afin de protéger l’application web contre les pertes de données, vous devez configurer des sauvegardes. Accédez à la zone Paramètres de votre application web.

    ![image56][image56]

2.  Dans le panneau de droite, accédez à la catégorie Fonctionnalités.

	 ![image57][image57]

1.  Choisissez Sauvegardes ; un panneau s’ouvre sur la droite.

    ![image58][image58]

2.  Cliquez sur Configurer, puis choisissez un compte de stockage dans le panneau de droite.

    ![image59][image59]

3.  Maintenant, créez et choisissez un conteneur de stockage pour stocker vos sauvegardes. Cliquez sur l’option Créer figurant en bas du panneau. Sélectionnez ensuite le conteneur.

    ![image60][image60]

4.  Une fois que vous avez choisi le conteneur, vous pouvez configurer des planifications, ainsi que des sauvegardes de configuration pour vos bases de données. Pour ce scénario, cliquez sur l’icône Enregistrer.

     ![image61][image61]

5.  Après l’enregistrement, revenez à Sauvegardes dans le panneau de gauche. Cliquez sur Sauvegarder maintenant pour sauvegarder l’application.

     ![image62][image62]

6.  Dans quelques instants, vous verrez qu’une sauvegarde a été créée. Notez la présence de l’option Restaurer maintenant sur la capture d’écran ci-dessous.

     ![image63][image63]

7.  Cliquez sur Restaurer maintenant et examinez les options dans le panneau de droite. Vous pouvez choisir une sauvegarde appropriée et facilement restaurer un état antérieur si nécessaire. Le portail Azure nous a permis de facilement mettre en place une stratégie simple de récupération d’urgence pour l’application.

     ![image64][image64]

8.  Revenez au panneau Paramètres sur la gauche. Sous Fonctionnalités, choisissez Authentification/autorisation.

     ![image65][image65]

9.  Dans le panneau de droite, choisissez Authentification App Service. Notez les diverses options que vous pouvez configurer avec les fournisseurs les plus courants.

     ![image66][image66]

10. Sélectionnez le fournisseur de votre choix et notez les options pour l’étendue. Vous pouvez fournir un ID d’application et une question secrète, et activer rapidement l’authentification Facebook pour l’application. Le portail Azure offre une solution d’authentification clé en main pour les applications.

     ![image67][image67]

11. Revenez au panneau Paramètres et choisissez Utilisateurs dans la catégorie Gestion des ressources.

     ![image68][image68]

12. Dans le panneau de droite, examinez les diverses options permettant d’ajouter des utilisateurs et des rôles. Le portail Azure vous permet de contrôler facilement le contrôle d’accès en fonction du rôle (RBAC) pour l’application.

     ![image69][image69]


## Résumé

Ce didacticiel a présenté succinctement la puissance de la plateforme Azure, qui passe par l’activation rapide d’un déploiement continu pour une application web, la réalisation de diverses activités de développement et de test, l’analyse et le dépannage des applications en activité et enfin la gestion des stratégies clés telles que la récupération d’urgence, l’identification et le contrôle d’accès en fonction du rôle. La plateforme Azure offre une expérience intégrée pour ces flux de travail DevOps. Vous pouvez ainsi travailler efficacement de manière appropriée à la tâche en cours.


## Étapes suivantes 

* Azure Resource Manager est important pour permettre les opérations de développement sur la plateforme Azure. Pour en savoir plus, consultez la page [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md).

* Pour en savoir plus sur le déploiement d’Azure App Service, consultez la page [Déploiement de votre application dans Azure App Service](../app-service-web/web-sites-deploy.md)


[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png

<!-----HONumber=AcomDC_0615_2016-->