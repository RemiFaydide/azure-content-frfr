<properties
   pageTitle="Créer votre première application Service Fabric dans Visual Studio | Microsoft Azure"
   description="Créer, déployer et déboguer une application Service Fabric à l’aide de Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="ryanwi"/>


# Créer votre première application Azure Service Fabric

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Le kit de développement logiciel Fabric Service inclut un complément à Visual Studio proposant des outils et des modèles pour la création, le déploiement et le débogage d’applications Service Fabric. Cette rubrique vous guide tout au long du processus de création de votre première application dans Visual Studio.

## Composants requis

Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement](service-fabric-get-started.md).

## Vidéo de procédure pas à pas

La vidéo suivante vous guide à travers les étapes de ce didacticiel :

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Création de l'application

Une application Service Fabric peut contenir un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Avec l’Assistant Nouveau projet, vous avez la possibilité de créer un projet d’application avec votre premier projet de service. Vous pouvez ajouter davantage de services plus tard.

1. Lancez Visual Studio en tant qu'administrateur.

2. Cliquez sur **Fichier > Nouveau projet > Cloud > Application Service Fabric**.

3. Nommez l’application, puis cliquez sur **OK**.

	![Boîte de dialogue Nouveau projet dans Visual Studio][1]

4. Dans la page suivante, choisissez **Service avec état** comme premier type de service à inclure dans votre application. Attribuez-lui un nom, puis cliquez sur **OK**.

	![Boîte de dialogue Nouveau service dans Visual Studio.][2]

	>[AZURE.NOTE] Pour plus d’informations sur les options, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).

	Visual Studio crée le projet d’application et le projet de service avec état et les affiche dans l’Explorateur de solutions.

	![Explorateur de solutions après la création de l’application de service avec état][3]

	Le projet d’application ne contient pas de code directement. Au lieu de cela, il fait référence à un ensemble de projets de service. En outre, il contient trois autres types de contenu :

	- **Profils de publication** : permet de gérer les préférences d’outils pour différents environnements.

	- **Scripts** : contient un script PowerShell de déploiement/mise à niveau de votre application. Visual Studio utilise le script en arrière-plan. Le script peut également être appelé directement dans la ligne de commande.

	- **Définition d’application** : inclut le manifeste d’application dans le dossier *ApplicationPackageRoot*. Les fichiers de paramètres d’application associés, qui définissent l’application et vous permettent de la configurer spécifiquement pour un environnement donné, se trouvent dans le dossier *ApplicationParameters*.

    Pour avoir une vue d’ensemble du contenu du projet de service, consultez l’article [Prise en main de Reliable Services](service-fabric-reliable-services-quick-start.md).

## Déployer et déboguer l’application.

Maintenant que vous disposez d’une application, essayez de l’exécuter.

1. Appuyez sur F5 dans Visual Studio pour déployer l’application en vue d’un débogage.

	>[AZURE.NOTE] Le déploiement prend un certain temps la première fois que Visual Studio crée un cluster local pour le développement. Un cluster local exécute un code de plateforme équivalent à celui qui serait généré dans un cluster regroupant plusieurs machines sur un seul ordinateur. L’état de la création de clusters est affiché dans la fenêtre Sortie de Visual Studio.

	Une fois le cluster prêt, vous obtiendrez une notification de la part de l’application de gestionnaire de la barre d’état système de cluster local incluse avec le kit de développement logiciel.

	![Notification de barre d’état système de cluster local][4]

2. Une fois que l’application a démarré, Visual Studio affiche automatiquement l’Observateur d’événements de diagnostic, où vous pouvez voir la sortie de suivi depuis le service.

	![Observateur d’événements de diagnostic][5]

	Dans le cas du modèle de service avec état, les messages indiquent simplement la valeur du compteur incrémentée dans la méthode `RunAsync` de MyStatefulService.cs.

3. Pour plus d’informations, développez un des événements, et notamment le nœud sur lequel le code s’exécute. Dans le cas présent, il s’agit du nœud _Node_2, mais cela peut être différent sur votre ordinateur.

	![Détails de l’Observateur d’événements de diagnostic][6]

	Le cluster local contient cinq nœuds hébergés sur un seul ordinateur. Il simule un cluster à cinq nœuds dans lequel les nœuds sont hébergés sur des machines distinctes. Examinons un des nœuds du cluster local afin de simuler la perte d’un ordinateur tout en testant le débogueur Visual Studio.

    >[AZURE.NOTE] Les événements de diagnostic d’application émis par le modèle de projet utilisent la classe `ServiceEventSource` incluse. Pour plus d’informations, consultez l’article [Surveiller et diagnostiquer les services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Recherchez dans votre projet de service la classe qui dérive de StatefulService (par exemple, MyStatefulService) et définissez un point d’arrêt sur la première ligne de la méthode `RunAsync`.

	![Point d’arrêt dans la méthode RunAsync de service avec état][7]

5. Cliquez avec le bouton droit sur l’application de barre d’état système du gestionnaire du cluster local et choisissez **Gérer le cluster Local** pour lancer Service Fabric Explorer.

    ![Lancer l’Explorateur de Fabric Service à partir du Gestionnaire de cluster Local.][systray-launch-sfx]

    Service Fabric Explorer offre une représentation visuelle de cluster, notamment l’ensemble des applications et le jeu de nœuds physiques qui le composent. Pour en savoir plus sur Service Fabric Explorer, voir [Visualisation de votre cluster](service-fabric-visualizing-your-cluster.md).

6. Dans le volet gauche, développez **Cluster > Nœuds** et recherchez le nœud sur lequel votre code s’exécute.

7. Cliquez sur **Actions > Désactiver (Redémarrer)** pour simuler le redémarrage de l’ordinateur. (Notez que vous pouvez également procéder à la désactivation depuis le menu contextuel de la vue Liste de nœuds du volet gauche.)

	![Arrêter un nœud Service Fabric Explorer][sfx-stop-node]

	Vous devez voir momentanément le point d’arrêt atteint dans Visual Studio, car le calcul vous faisiez sur un nœud bascule en toute transparence vers un autre.

8. Revenir à l’Observateur d’événements de diagnostic et prenez connaissance des messages. Notez que le compteur a continué de s’incrémenter, même si les événements proviennent en fait d’un autre nœud.

    ![Observateur d’événements de diagnostic après basculement][diagnostic-events-viewer-detail-post-failover]

## Changer de mode de cluster

Par défaut, le cluster de développement local est configuré pour s’exécuter en tant que cluster à 5 nœuds, ce qui s’avère utile pour déboguer les services déployés sur plusieurs nœuds. Cependant, le déploiement d’une application sur le cluster de développement à 5 nœuds peut prendre un certain temps. Si vous souhaitez itérer des modifications de code rapidement, sans exécuter votre application sur 5 nœuds, vous pouvez basculer le cluster de développement sur le mode 1 nœud. Pour exécuter votre code sur un cluster à un nœud, cliquez avec le bouton droit sur le gestionnaire de cluster local dans la barre d’état système, puis sélectionnez **Switch Cluster Mode -> 1 Node** (Changer de mode de cluster -> 1 nœud).

![Changer de mode de cluster][switch-cluster-mode]

Lorsque vous changez de mode de cluster, le cluster du cluster de développement redémarre et toutes les applications approvisionnées ou en cours d’exécution sur le cluster sont supprimées.

## Nettoyage

  Avant de conclure, il est important de se rappeler que le cluster local est très réel. L’arrêt du débogueur supprime votre instance d’application et annule l’inscription du type d’application. Toutefois, le cluster continue de s’exécuter en arrière-plan. Vous disposez de plusieurs options pour gérer le cluster :

  1. Pour arrêter le cluster tout en conservant les données et les traces de l’application, cliquez sur **Arrêter le cluster local** dans l’application de zone de notification.

  2. Pour supprimer complètement le cluster, cliquez sur **Supprimer le cluster local** dans l’application de zone de notification. Notez que cette option se traduira par un autre déploiement lent la prochaine fois que vous appuierez sur F5 dans Visual Studio. Supprimez le cluster uniquement si vous n’envisagez pas d’utiliser le cluster local pendant un certain temps ou si vous avez besoin de libérer des ressources.

## Étapes suivantes

- Apprenez à créer un [cluster dans Azure](service-fabric-cluster-creation-via-portal.md) ou un [cluster autonome sur Windows](service-fabric-cluster-creation-for-windows-server.md).
- Essayez de créer un service en utilisant les modèles de programmation [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
- Découvrez comment exposer vos services sur Internet avec un [service web frontal](service-fabric-add-a-web-frontend.md).
- Suivez des [travaux pratiques](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) afin de créer un service sans état, de configurer des rapports de surveillance et d’intégrité, et d’effectuer une mise à niveau de l’application.

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png

<!---HONumber=AcomDC_0928_2016-->