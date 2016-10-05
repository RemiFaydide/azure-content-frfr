 <properties
   pageTitle="Didacticiel de mise à niveau d'applications Service Fabric | Microsoft Azure"
   description="Cet article vous présente l'expérience de déploiement d'une application Service Fabric, la modification du code et le déploiement d'une mise à niveau à l'aide de Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>



# Didacticiel sur la mise à niveau d'une application Service Fabric à l'aide de Visual Studio

Azure Service Fabric simplifie le processus de mise à niveau des applications cloud en veillant à ce que seuls les services modifiés soient mis à niveau et que l'intégrité de l'application soit analysée tout au long de la mise à niveau. Il restaure automatiquement la version précédente de l’application en cas de problème. Les mises à niveau des applications Service Fabric s'effectuent *sans interruption de service*, étant donné que l'application peut être mise à niveau sans interruption du service. Ce didacticiel explique comment effectuer une mise à niveau propagée à partir de Visual Studio.


## Étape 1 : Créer et publier l'exemple Visual Objects

Tout d’abord, téléchargez l’application [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Actors/VisualObjects) à partir de GitHub. Ensuite, générez et publiez l’application en cliquant avec le bouton droit sur le projet d’application, **VisualObjects** et en sélectionnant la commande **Publier** dans l’élément de menu Service Fabric.

![Menu contextuel pour une application Service Fabric][image1]

La sélection de l’option **Publier** affiche une autre fenêtre. Vous pouvez définir le **Profil cible** sur **PublishProfiles\\Local.xml**. La fenêtre doit ressembler à ce qui suit avant de cliquer sur **Publier**.

![Publication d'une application Service Fabric][image2]

À présent, vous pouvez cliquer sur **Publier** dans la boîte de dialogue. Vous pouvez utiliser l'[Explorateur de Service Fabric pour afficher le cluster et l'application](service-fabric-visualizing-your-cluster.md). L’application Visual Objects possède un service web accessible en tapant [http://localhost:8082/visualobjects/](http://localhost:8082/visualobjects/) dans la barre d’adresse de votre navigateur. Vous devez normalement voir 10 objets visuels flottants en rotation à l'écran.

## Étape 2 : Mettre à jour l'exemple Visual Objects

Vous pouvez remarquer qu'avec la version qui a été déployée à l'étape 1, les objets visuels ne sont pas en rotation. Mettons à niveau cette application vers une application dans laquelle les objets visuels sont en rotation.

Sélectionnez le projet VisualObjects.ActorService dans la solution VisualObjects et ouvrez le fichier **VisualObjectActor.cs**. Dans ce fichier, accédez à la méthode `MoveObject`, placez en commentaire `visualObject.Move(false)` et effacez le commentaire de `visualObject.Move(true)`. Cette modification du code fait pivoter les objets lorsque le service a été mis à niveau. **Vous pouvez maintenant générer (pas régénérer) la solution**, qui génère les projets modifiés. Si vous sélectionnez *Régénérer tout*, vous devez mettre à jour les versions de tous les projets.

Nous devons également attribuer une version à notre application. Pour modifier la version après avoir cliqué avec le bouton droit sur le projet **VisualObjects**, vous pouvez utiliser l’option **Modifier les versions du manifeste** de Visual Studio. La sélection de cette option affiche la boîte de dialogue des versions d’édition comme suit :

![Boîte de dialogue Contrôle de version][image3]

Mettez à jour les versions des projets modifiés et leurs packages de code, ainsi que l’application vers la version 2.0.0. Une fois les modifications effectuées, le manifeste doit se présenter comme suit (les parties en gras présentent les modifications) :

![Mise à jour des versions][image4]

Les outils Visual Studio peuvent déployer automatiquement les versions si vous sélectionnez **Mettre à jour automatiquement les versions de l’application et des services**. Si vous utilisez [SemVer](http://www.semver.org), vous devez mettre à jour le code et/ou la version du package de configuration uniquement si cette option est sélectionnée.

Enregistrez les modifications et cochez la case **Upgrade the Application** (Mettre à niveau l'application).


## Étape 3 : Mettre à niveau votre application

Familiarisez-vous avec les [paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md) et le [processus de mise à niveau](service-fabric-application-upgrade.md) pour bien comprendre les différents paramètres de mise à niveau, les délais d’attente et les critères d’intégrité qui peuvent être appliqués. Pour cette procédure pas à pas, le critère d’évaluation de l’intégrité du service est défini sur la valeur par défaut (mode non surveillé). Vous pouvez configurer ces paramètres en sélectionnant **Configurer les paramètres de mise à niveau** et en les modifiant comme vous le souhaitez.

À présent, nous sommes prêts à démarrer la mise à niveau de l’application en sélectionnant **Publier**. Cette option met à niveau votre application vers la version 2.0.0 dans laquelle les objets pivotent. Service Fabric met à niveau un domaine de mise à jour à la fois (certains objets sont mis à niveau en premier, suivis par d’autres) et le service est accessible pendant la mise à niveau. L’accès au service peut être vérifié par le biais de votre client (navigateur).


À présent, alors que la mise à niveau de l’application se poursuit, vous pouvez la surveiller à l’aide de Service Fabric Explorer en utilisant l’onglet **Upgrades in Progress** (Mises à niveau en cours) sous les applications.

Dans quelques minutes, tous les domaines de mise à jour seront mis à niveau (terminé) et la fenêtre de sortie Visual Studio doit également indiquer que la mise à niveau est terminée. De plus, vous pouvez normalement noter que *tous* les objets visuels sont désormais en rotation dans la fenêtre de votre navigateur.

Vous souhaiterez peut-être essayer de modifier les versions et de passer de la version 2.0.0 à la version 3.0.0 en guise d'exercice, voire même de revenir de la version 2.0.0 à la version 1.0.0. Amusez-vous avec les délais d'attente et les stratégies de contrôle d'intégrité pour vous familiariser avec ceux-ci. Lors du déploiement sur un cluster Azure par opposition à un cluster local, les paramètres utilisés peuvent être différents. Nous vous recommandons de définir les délais d’attente de manière plus restrictive.


## Étapes suivantes

La [mise à niveau de votre application à l’aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l’application à l’aide de PowerShell.

Contrôlez les mises à niveau de votre application à l’aide des [paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utiliser la [sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).



[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=AcomDC_0921_2016-->