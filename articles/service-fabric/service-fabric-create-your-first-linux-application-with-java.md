<properties
   pageTitle="Créer votre première application Service Fabric sur Linux à l’aide de Java | Microsoft Azure"
   description="Créer et déployer une application Service Fabric à l’aide de Java"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="seanmck"/>


# Créer votre première application Azure Service Fabric

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Service Fabric fournit des Kits de développement logiciel (SDK) pour générer des services Linux dans .NET Core et Java. Dans ce didacticiel, nous allons apprendre à créer une application pour Linux et générer un service à l’aide de Java.

## Composants requis

Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement Linux à boîtier unique sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

## Création de l'application

Une application Service Fabric peut contenir un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre premier service et son ajout ultérieurement. Nous allons utiliser Yeoman pour créer une application avec un seul service.

1. Dans un terminal, tapez **yo azuresfjava**.

2. Donnez un nom à votre application.

3. Choisissez le type de votre premier service et nommez-le. Pour les besoins de ce didacticiel, nous allons choisir Reliable Actor Service (Service d’acteur fiable).

  ![Générateur Yeoman Service Fabric pour Java][sf-yeoman]

>[AZURE.NOTE] Pour plus d’informations sur les options, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).

## Création de l'application

Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal.

  ```bash
  gradle
  ```

## Déployer l’application

Une fois que l’application est générée, vous pouvez la déployer vers le cluster local à l’aide de l’interface de ligne de commande Azure.

1. Connectez-vous au cluster Service Fabric local.

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. Utilisez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    cd myapp
    ./install.sh
    ```

3. Ouvrez un navigateur et accédez à Service Fabric Explorer à l’adresse http://localhost:19080/Explorer (remplacez localhost par l’adresse IP privée de la machine virtuelle si vous utilisez Vagrant sur Mac OS X).

4. Développez le nœud Applications et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.

## Démarrer le client de test et effectuer un basculement

Les projets d’acteur n’effectuent aucune opération automatiquement. Ils ont besoin d’un autre service ou client pour leur envoyer des messages. Le modèle d’acteur inclut un script de test simple que vous pouvez utiliser pour interagir avec le service d’acteur.

1. Exécutez le script à l’aide de l’utilitaire watch pour afficher la sortie du service d’acteur.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Dans Service Fabric Explorer, recherchez le nœud qui héberge le réplica principal pour le service d’acteur. Dans la capture d’écran ci-dessous, il s’agit du nœud 3.

    ![Recherche du réplica principal dans Service Fabric Explorer][sfx-primary]

3. Cliquez sur le nœud trouvé à l’étape précédente, puis sélectionnez **Désactiver (redémarrer)** à partir du menu Actions. Cela permettra de redémarrer l’un des cinq nœuds de votre cluster local et de forcer un basculement sur l’un des réplicas secondaires s’exécutant sur un autre nœud. Dans le même temps, prêtez attention à la sortie du client de test et notez que le compteur continue à être incrémenté malgré le basculement.

## Générer et déployer une application avec le plug-in Eclipse Neon

Si vous avez installé le plug-in Service pour Eclipse Neon, vous pouvez l’utiliser pour créer, générer et déployer des applications Service Fabric générées avec Java.

### Création de l'application

Le plug-in Service Fabric est disponible via l’extensibilité Eclipse.

1. Dans Eclipse, choisissez **Fichier > Autres > Service Fabric**. Un ensemble d’options s’affiche, notamment Acteurs et Conteneurs.

    ![Modèles Service Fabric dans Eclipse][sf-eclipse-templates]

2. Ici, choisissez Service sans état.

3. Vous êtes invité à confirmer l’utilisation de la perspective Service Fabric, qui optimise Eclipse pour une utilisation avec les projets Service Fabric. Choisissez « Oui ».

### Déployer l’application

Les modèles Service Fabric comprennent un ensemble de tâches Gradle pour générer et déployer des applications, que vous pouvez déclencher via Eclipse.

1. Choisissez **Exécuter > Run Configurations (Exécuter des configurations)**.

2. Développez **Gradle Project** (Projet Gradle) et choisissez **ServiceFabricDeployer**.

3. Cliquez sur **Exécuter**.

Votre application sera générée et déployée dans quelques instants. Vous pouvez surveiller son état depuis Service Fabric Explorer.

## Étapes suivantes

- [Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

<!---HONumber=AcomDC_0928_2016-->