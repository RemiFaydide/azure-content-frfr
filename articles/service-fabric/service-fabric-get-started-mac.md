<properties
   pageTitle="Configurer votre environnement de développement sur Mac OS X | Microsoft Azure"
   description="Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois la configuration terminée, vous serez prêt à générer des applications sur Mac OS X."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# Configurer votre environnement de développement sur Mac OS X

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Vous pouvez générer des applications Service Fabric pour qu’elles s’exécutent sur les clusters Linux à l’aide de Mac OS X. Cet article explique comment configurer votre Mac pour le développement.

## Composants requis

Service Fabric n’est pas exécuté en mode natif sur OS X. Pour exécuter un cluster Service Fabric local, nous fournissons une machine virtuelle Ubuntu préconfigurée à l’aide de Vagrant et VirtualBox. Avant de commencer, vous avez besoin des éléments suivants :

- [Vagrant (v1.8.4 ou version ultérieure)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## Créer la machine virtuelle locale

Pour créer la machine virtuelle locale contenant un cluster Service Fabric à 5 nœuds, procédez comme suit :

1. Clonez le référentiel Vagrantfile.

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Accédez au clone local du référentiel.

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Facultatif) Modifiez les paramètres de la machine virtuelle par défaut.

    Par défaut, la machine virtuelle locale est configurée comme suit :

    - 3 Go de mémoire allouée
    - Réseau hôte privé configuré sur l’IP 192.168.50.50 activant le transfert du trafic à partir de l’hôte Mac

    Vous pouvez modifier ces paramètres ou ajouter une autre configuration à la machine virtuelle dans Vagrantfile. Consultez la [documentation de Vagrant](http://www.vagrantup.com/docs) pour connaître la liste complète des options de configuration.

4. Création de la machine virtuelle

    ```bash
    vagrant up
    ```

    Cette étape sert à télécharger l’image de machine virtuelle préconfigurée, à la démarrer en local, puis à configurer un cluster Service Fabric dans celle-ci. Cette opération peut prendre plusieurs minutes. Si la configuration se déroule correctement, un message indiquant que le cluster est en train de démarrer s’affichera.

    ![La configuration du cluster démarre après l’approvisionnement de la machine virtuelle][cluster-setup-script]

5. Vérifiez que le cluster a été configuré correctement en accédant à Service Fabric Explorer à l’adresse http://192.168.50.50:19080/Explorer (en supposant que vous avez conservé l’adresse IP du réseau privé par défaut).

    ![Service Fabric Explorer affiché depuis le Mac hôte][sfx-mac]


## Installer le plug-in Service Fabric pour Eclipse Neon (facultatif)

Service Fabric fournit un plug-in pour l’IDE Eclipse Neon qui peut simplifier le processus de génération et de déploiement des services Java.

1. Dans Eclipse, assurez-vous que vous avez Buildship version 1.0.17 ou une version ultérieure. Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’installation**. Vous pouvez mettre à jour Buildship en suivant les instructions [ici][buildship-update].

2. Pour installer le plug-in Service Fabric, choisissez **Aide > Installer un nouveau logiciel...**

3. Dans la zone de texte « Work with » (Utiliser), entrez : http://dl.windowsazure.com/eclipse/servicefabric

4. Cliquez sur Ajouter.

    ![Plug-in Eclipse Neon pour Service Fabric][sf-eclipse-plugin-install]

5. Choisissez le plug-in Service Fabric et cliquez sur Suivant.

6. Suivez les étapes d’installation et acceptez le contrat de licence d’utilisateur final.

## Étapes suivantes

- [Create your first Service Fabric application for Linux](service-fabric-create-your-first-linux-application-with-java.md) (Créer votre première application Service Fabric pour Linux)

<!-- Links -->

- [Création d’un cluster Service Fabric dans Azure à partir du portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Création d’un cluster Service Fabric dans Azure à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
- [Modéliser une application dans Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!---HONumber=AcomDC_0928_2016-->