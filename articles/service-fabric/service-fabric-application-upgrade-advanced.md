<properties
   pageTitle="Mise à niveau d’application : rubriques avancées | Microsoft Azure"
   description="Cet article traite de sujets avancés se rapportant à la mise à niveau d’une application Service Fabric."
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

# Mise à niveau d’une application Service Fabric : rubriques avancées

## Ajout ou suppression de services pendant la mise à niveau d'une application

Si un service est ajouté à une application déjà déployée et publiée en tant que mise à niveau, le nouveau service est ajouté à l’application déployée. Une telle mise à niveau n’affecte pas les services faisant déjà partie de l’application. Cependant, une instance du service ajouté doit être démarrée pour activer le nouveau service (à l’aide de l’applet de commande `New-ServiceFabricService`).

Des services peuvent également être supprimés d’une application dans le cadre d’une mise à niveau. Toutefois, toutes les instances en cours du service à supprimer doivent être arrêtées avant de procéder à la mise à niveau (à l’aide de l’applet de commande `Remove-ServiceFabricService`).

## Mode de mise à niveau manuelle

> [AZURE.NOTE]  Le mode manuel non surveillé ne peut être envisagé que pour une mise à niveau ayant échoué ou suspendue. Le mode surveillé est le mode de mise à niveau recommandé pour les applications Service Fabric.

Azure Service Fabric fournit plusieurs modes de mise à niveau pour prendre en charge les clusters de développement et de production. Les options de déploiement choisies peuvent être différentes pour différents environnements.

La mise à niveau d’application propagée surveillée est la mise à niveau la plus courante à utiliser dans un environnement de production. Quand la stratégie de mise à niveau est spécifiée, Service Fabric s'assure que l'application est saine avant que la mise à niveau ne se poursuive.

 L’administrateur d’application peut utiliser le mode de mise à niveau d’application propagée manuelle pour contrôler totalement la progression de la mise à niveau à travers les différents domaines de mise à niveau. Ce mode est utile lorsqu’une stratégie d’évaluation d’intégrité personnalisée ou complexe est nécessaire ou dans le cas d’une mise à niveau non conventionnelle (par exemple, l’application est déjà dans une situation de perte de données).

Enfin, la mise à niveau d’application propagée automatisée permet aux environnements de développement ou de test de fournir un cycle d’itération rapide pendant le développement du service.

## Passer en mode de mise à niveau manuelle
**Mode manuel** : arrêter la mise à niveau de l’application au domaine de mise à niveau actuel et passer en mode de mise à niveau manuelle non surveillée. L’administrateur doit appeler manuellement **MoveNextApplicationUpgradeDomainAsync** pour procéder à la mise à niveau ou déclencher une restauration en initiant une nouvelle mise à niveau. Une fois que la mise à niveau est en mode manuel, elle demeure dans ce mode jusqu'à ce qu'une nouvelle mise à niveau soit initiée. La commande **GetApplicationUpgradeProgressAsync** renvoie FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Effectuer une mise à niveau avec un package différentiel

Une application Service Fabric peut être mise à niveau via une mise en service avec un package d'application autonome et complet. Une application peut également être mise à niveau à l’aide d’un package différentiel qui contient uniquement les fichiers d’application mis à jour, les fichiers manifeste d’application mis à jour et manifeste de service.

Un package d'application complet contient tous les fichiers nécessaires pour démarrer et exécuter une application Service Fabric. Un package différentiel contient uniquement les fichiers qui ont changé entre la dernière mise en service et la mise à niveau actuelle, ainsi que les fichiers manifeste d’application et manifeste de service complets. Toute référence dans le manifeste d’application ou de service qui est introuvable dans la disposition de la build est recherchée dans le magasin d’images.

Des packages d’application complets sont nécessaires pour la première installation d’une application sur le cluster. Les mises à jour suivantes peuvent être un package d'application complet ou un package différentiel.

Situations qui se prêtent à l'utilisation d'un package différentiel :

* Un package différentiel est préférable quand vous disposez d’un package d’application volumineux qui référence plusieurs fichiers manifeste de service et/ou plusieurs packages de code, de configuration ou de données.

* Un package différentiel est préférable quand vous disposez d’un système de déploiement qui génère la disposition de la build directement à partir de votre processus de génération d’application. Dans ce cas, même si le code n’a pas changé, les assemblys nouvellement générés obtiennent une somme de contrôle différente. Si vous utilisez un package d'application complet, vous devez mettre à jour la version installée sur tous les packages de code. Si vous utilisez un package différentiel, vous fournissez uniquement les fichiers qui ont changé et les fichiers manifeste dont la version a changé.

Lorsqu'une application est mise à niveau à l'aide de Visual Studio, le package différentiel est automatiquement publié. Pour créer manuellement un package différentiel, le manifeste d’application et les manifestes de service doivent être mis à jour, mais seuls les packages modifiés doivent être inclus dans le package d’application final.

Commençons par exemple par l'application suivante (numéros de version fournis pour faciliter la compréhension) :

```text
app1       	1.0.0
  service1 	1.0.0
    code   	1.0.0
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

Supposons à présent que vous souhaitiez mettre à jour uniquement le package de code de service1 à l'aide d'un package différentiel avec PowerShell. À présent, votre application mise à jour présente la structure des dossiers suivante :

```text
app1       	2.0.0      <-- new version
  service1 	2.0.0      <-- new version
    code   	2.0.0      <-- new version
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

Dans ce cas, vous mettez à jour le manifeste d'application à la version 2.0.0 ainsi que le manifeste de service pour service1 afin de refléter la mise à jour du package de code. La structure des dossiers de votre package d’application serait la suivante :

```text
app1/
  service1/
    code/
```

## Étapes suivantes

La [mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

La [mise à niveau de votre application à l’aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l’application à l’aide de PowerShell.

Contrôlez les mises à niveau de votre application à l'aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utilisez la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0921_2016-->