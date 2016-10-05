<properties
   pageTitle="Configurer votre environnement de développement sur Linux | Microsoft Azure"
   description="Installez le runtime et le Kit de développement logiciel (SDK), puis créez un cluster de développement local sur Linux. Une fois l’installation terminée, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# Préparer votre environnement de développement sur Linux


> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Pour déployer et exécuter des [applications Azure Service Fabric](service-fabric-application-model.md) sur votre ordinateur de développement Linux, installez le runtime et le Kit de développement logiciel (SDK) courant. Vous pouvez également installer les Kits de développement logiciel (SDK) facultatifs pour Java et .NET Core.

## Composants requis
### Versions du système d’exploitation prises en charge
Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

- Ubuntu 16.04 (Xenial Xerus)

## Mettre à jour vos sources apt

Pour installer le Kit de développement logiciel (SDK) et le package runtime associé via apt-get, vous devez tout d’abord mettre à jour vos sources apt.

1. Ouvrez un terminal.
2. Ajoutez le référentiel Service Fabric à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Ajoutez la nouvelle clé GPG à votre porte-clés apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Actualisez vos listes de packages selon les référentiels nouvellement ajoutés.

    ```bash
    sudo apt-get update
    ```

## Installer et configurer le Kit de développement logiciel (SDK)

Une fois vos sources mises à jour, vous pouvez installer le Kit de développement logiciel (SDK).

1. Installez le package de Kit de développement logiciel (SDK) Service Fabric. Vous serez invité à confirmer l’installation et à accepter un contrat de licence.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## Configurer l’interface de ligne de commande interplateforme Azure

[L’interface de ligne de commande interplateforme Azure][azure-xplat-cli-github] inclut des commandes permettant d’interagir avec des entités Service Fabric, y compris des clusters et des applications. Puisqu’elle est basée sur Node.js, [assurez-vous que vous avez installé Node][install-node] avant de suivre les instructions ci-dessous.

1. Clonez le référentiel Github sur votre ordinateur de développement.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Basculez dans le référentiel cloné et installez les dépendances d’interface de ligne de commande à l’aide de Node Package Manager (NPM).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Créez un lien symbolique à partir du dossier bin/azure du référentiel cloné vers /usr/bin/azure, afin qu’il soit ajouté à votre chemin d’accès et que les commandes soient disponibles à partir de n’importe quel répertoire.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Enfin, activez les commandes Service Fabric de saisie semi-automatique.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## Configurer un cluster local

Si tout a été correctement installé, vous pourrez démarrer un cluster local.

1. Exécutez le script de configuration du cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Ouvrez un navigateur web et accédez à http://localhost:19080/Explorer. Si le cluster a démarré, vous devez voir le tableau de bord Service Fabric Explorer.

    ![Service Fabric Explorer sur Linux][sfx-linux]

À ce stade, vous êtes en mesure de déployer des packages d’application Service Fabric préconfigurés ou nouveaux basés sur des conteneurs invités ou des exécutables invités. Pour générer de nouveaux services à l’aide des Kits de développement logiciel (SDK) .Net Core ou Java, suivez les étapes de configuration facultatives ci-dessous.

## Installer le Kit de développement logiciel (SDK) Java et le plug-in Eclipse Neon (facultatif)

Le Kit de développement logiciel (SDK) Java fournit les bibliothèques et les modèles nécessaires pour générer des services Service Fabric à l’aide de Java.

1. Installez le package du Kit de développement logiciel (SDK) Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Vous pouvez installer le plug-in Eclipse pour Service Fabric à partir de l’IDE Eclipse Neon.

1. Dans Eclipse, assurez-vous que vous avez Buildship version 1.0.17 ou une version ultérieure. Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’installation**. Vous pouvez mettre à jour Buildship en suivant les instructions [ici][buildship-update].

2. Pour installer le plug-in Service Fabric, choisissez **Aide > Installer un nouveau logiciel...**

3. Dans la zone de texte « Work with » (Utiliser), entrez : http://dl.windowsazure.com/eclipse/servicefabric

4. Cliquez sur Ajouter.

    ![Plug-in Eclipse][sf-eclipse-plugin]

5. Choisissez le plug-in Service Fabric et cliquez sur Suivant.

6. Suivez les étapes d’installation et acceptez le contrat de licence d’utilisateur final.

## Installer le Kit de développement (SDK) .NET Core (facultatif)

Le Kit de développement logiciel (SDK) .NET Core fournit les bibliothèques et les modèles nécessaires pour générer des services Service Fabric à l’aide de .Net Core interplateforme.

1. Installez le package du Kit de développement (SDK) .NET Core.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## Étapes suivantes

- [Create your first Java application on Linux](service-fabric-create-your-first-linux-application-with-java.md) (Créer votre première application Java sur Linux)

- [Prepare your development environment on OSX](service-fabric-get-started-mac.md) (Préparer votre environnement de développement sur OSX)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

<!---HONumber=AcomDC_0928_2016-->