<properties
   pageTitle="Gestion de vos applications dans Visual Studio | Microsoft Azure"
   description="Utilisez Visual Studio pour créer, développer, packager, déployer et déboguer vos applications et services Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# Utilisation de Visual Studio pour simplifier l'écriture et la gestion des applications Service Fabric

Vous pouvez gérer vos applications et services Azure Service Fabric via Visual Studio. Après avoir [configuré votre environnement de développement](service-fabric-get-started.md), vous pouvez utiliser Visual Studio pour créer des applications Service Fabric, ajouter des services ou empaqueter, enregistrer et déployer des applications dans votre cluster de développement local.

## Déploiement de votre application Service Fabric

Par défaut, le déploiement d’une application regroupe les étapes suivantes en une simple opération :

1. Création du package d'application
2. Téléchargement du package d'application dans le magasin d'images
3. Enregistrement du type d'application
4. Suppression des instances d'application en cours d'exécution
5. Création d'une instance d'application

Dans Visual Studio, vous pouvez aussi appuyer sur **F5** pour déployer votre application et attacher le débogueur à toutes les instances de l'application. Vous pouvez utiliser **Ctrl + F5** pour déployer une application sans débogage ou la publier sur un cluster local ou distant à l’aide du profil de publication. Pour plus d’informations, reportez-vous à la section [Publication d’une application dans un cluster à distance avec Visual Studio](service-fabric-publish-app-remote-cluster.md).

### Mode de débogage d’application

Par défaut, Visual Studio supprime les instances existantes de votre type d’application quand vous arrêtez le débogage ou (si vous avez déployé l’application sans attacher le débogueur) quand vous redéployez l’application. Dans ce cas, toutes les données de l’application sont supprimées. Lors du débogage en local, vous pouvez souhaiter conserver les données que vous avez déjà créées lorsque vous testez une nouvelle version de l’application, vous souhaitez que l’application continue à s’exécuter ou vous souhaitez que les sessions de débogage suivantes mettent l’application à niveau. Les outils Service Fabric de Visual Studio fournissent une propriété appelée **Mode de débogage d’application** qui vérifie si **F5** doit désinstaller l’application, la conserver après une session de débogage ou permettre à l’application d’être mise à niveau lors de sessions de débogage suivantes, plutôt que d’être supprimée et redéployée.

#### Pour définir la propriété Mode de débogage d’application

1. Dans le menu contextuel du projet d’application, cliquez sur **Propriétés** (ou appuyez sur la touche **F4**).
2. Dans la fenêtre **Propriétés**, définissez la propriété **Mode de débogage d’application**.

    ![Définir la propriété Mode de débogage d’application][debugmodeproperty]

Il s’agit des options disponibles pour le **Mode de débogage d’application**.

1. **Mise à niveau automatique** : l’application continue à s’exécuter lorsque la session de débogage se termine. Le **F5** suivant traite le déploiement comme une mise à niveau, en utilisant le mode automatique non contrôlé pour mettre rapidement à niveau l’application vers une version plus récente avec une chaîne de date ajoutée. Le processus de mise à niveau conserve les données que vous avez saisies au cours de la précédente session de débogage.

2. **Conserver l’application** : l’application continue à s’exécuter dans le cluster lorsque la session de débogage se termine. Le **F5** suivant supprime l’application et l’application nouvellement créée sera déployée sur le cluster.

3. L’option **Supprimer l’application** entraîne la suppression de l’application lorsque la session de débogage se termine.

Avec l’option **Mise à niveau automatique**, les données sont conservées en appliquant les fonctionnalités de mise à niveau d’application de Service Fabric, mais avec une optimisation des performances plutôt que de la sécurité. Pour plus d’informations sur la mise à niveau des applications et sur la façon d’effectuer une mise à niveau dans un environnement réel, consultez [Mise à niveau d’application Service Fabric](service-fabric-application-upgrade.md).

![Exemple d’une nouvelle version de l'application avec une date ajoutée][preservedata]

>[AZURE.NOTE] Cette propriété n’existe pas avant la version 1.1 des outils Service Fabric pour Visual Studio. Avant la version 1.1, veuillez utiliser la propriété **Conserver les données au démarrage** pour obtenir le même comportement. L’option « Conserver l’application » a été introduite dans la version 1.2 des outils Service Fabric pour Visual Studio.

## Ajouter un service à votre application Service Fabric

Vous pouvez ajouter de nouveaux services à votre application pour étendre ses fonctionnalités. Pour garantir que le service est inclus dans votre package d’application, ajoutez-le via l’élément de menu **Nouveau service Service Fabric…**.

![Ajoutez un nouveau service Service Fabric à votre application][newservice]

Sélectionnez un type de projet Service Fabric à ajouter à votre application et spécifiez un nom pour le service. Consultez [Choix d’une infrastructure pour votre service](service-fabric-choose-framework.md) pour vous aider à choisir le type de service à utiliser.

![Sélectionner un type de projet Service Fabric à ajouter à votre application][addserviceproject]

Le nouveau service est ajouté à votre solution et au package d'application existant. Les références du service et une instance de service par défaut sont ajoutés au manifeste d'application. Le service est créé et démarré la prochaine fois que vous déployez l'application.

![Le nouveau service est ajouté à votre manifeste d'application][newserviceapplicationmanifest]

## Empaquetage de votre application Service Fabric

Pour déployer l’application et ses services dans un cluster, vous devez créer un package d’application. Le package organise le manifeste d'application, le ou les manifestes de service et les autres fichiers nécessaires dans une disposition spécifique. Visual Studio configure et gère le package dans le dossier du projet d'application, dans le répertoire « pkg ». Cliquez sur **Package** dans le menu contextuel **Application** pour créer ou mettre à jour le package d'application. Cela peut être utile si vous déployez l’application à l’aide de scripts Powershell personnalisés.

## Supprimer des applications et des types d’applications à l’aide de Cloud Explorer

Vous pouvez effectuer des opérations de gestion de cluster de base à partir de Visual Studio à l’aide de Cloud Explorer, que vous pouvez lancer à partir du menu **Affichage**. Par exemple, vous pouvez supprimer des applications et annuler la mise en service de types d’applications sur des clusters locaux ou distants.

![Supprimer une application](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Pour obtenir des fonctionnalités de gestion de clusters enrichies, consultez [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

- [Modèle d'application Service Fabric](service-fabric-application-model.md)
- [Déploiement d'applications Service Fabric](service-fabric-deploy-remove-applications.md)
- [Gestion des paramètres d’application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)
- [Débogage de votre application Service Fabric](service-fabric-debugging-your-application.md)
- [Visualisation de votre cluster à l’aide de l’explorateur Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]: ./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png

<!---HONumber=AcomDC_0921_2016-->