<properties
   pageTitle="Créer des clusters Azure Service Fabric sur Windows Server et Linux | Microsoft Azure"
   description="Les clusters Service Fabric peuvent être exécutés sous Windows Server et Linux, ce qui vous permet de déployer et d’héberger des applications Service Fabric partout où vous pouvez exécuter Windows Server ou Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# Créer des clusters Service Fabric autonomes sur un serveur Windows Server ou Linux
Azure Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server ou Linux. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement, que ce soit en local ou avec un fournisseur cloud, où vous avez un ensemble d’ordinateurs Windows Server ou Linux interconnectés.

**Remarque** : la création d’un cluster dans Azure doit être effectuée via un modèle Azure Resource Manager ou le portail Azure. Pour plus d’informations, consultez les articles [Créer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) or [Créer un cluster Service Fabric à partir du portail Azure](service-fabric-cluster-creation-via-portal.md).

Service Fabric fournit un package d’installation pour créer ces clusters Service Fabric autonomes en local. L’avantage est qu’il n’existe aucune obligation en termes de fournisseur lorsque vous générez une application à l’aide de Service Fabric dans la mesure où vous choisissez où ces applications s’exécutent. Cela augmente également votre capacité à atteindre un ensemble plus large de clients, étant donné que des clients différents peuvent avoir des exigences différentes pour les environnements où ils souhaitent exécuter vos applications. Par exemple, les clients du secteur financier et du secteur de la santé peuvent avoir des besoins différents de ceux d’un constructeur automobile ou d’une agence de voyages.

## Systèmes d’exploitation pris en charge
Vous pouvez créer des clusters sur des machines virtuelles ou des ordinateurs qui exécutent ces systèmes d’exploitation :

* Windows Server 2012 R2
* Windows Server 2016 (une fois annoncé comme étant mis à la disposition générale)
* Linux (prochainement)

Pour plus d’informations sur Windows Server, consultez l’article [Création d’un cluster Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)

## Création et configuration du cluster
Service Fabric fournit un package d’installation que vous pouvez télécharger. Une fois que vous avez téléchargé ce package, vous devez apporter des modifications à un fichier de configuration JSON pour spécifier les paramètres de votre cluster. Une fois que les paramètres du cluster ont été modifiés, vous exécutez un script d’installation qui crée le cluster couvrant les ordinateurs que vous avez spécifiés dans les paramètres de votre cluster. Vous pouvez également exécuter un script pour supprimer un cluster d’un ensemble d’ordinateurs.

## Déploiements cloud ou en local
Le processus de création d’un cluster Service Fabric en local est similaire au processus de création d’un cluster sur un cloud de votre choix avec un ensemble de machines virtuelles. Les étapes initiales d’approvisionnement des machines virtuelles sont régies par le fournisseur de cloud ou l’environnement local que vous utilisez. Une fois que vous avez un ensemble de machines virtuelles avec une connectivité réseau activée entre elles, les étapes suivantes pour configurer le package Service Fabric, modifier les paramètres du cluster et exécuter les scripts de création et de gestion du cluster sont identiques. Cela garantit que vos connaissances et votre expérience en matière d'exploitation et de gestion des clusters Service Fabric est transférable lorsque vous choisissez de cibler de nouveaux environnements d'hébergement.

## Avantages de la création de clusters Service Fabric autonomes
* Étant donné qu’il n’y a aucun assujettissement au fournisseur, vous pouvez choisir l’endroit où créer votre cluster.
* Une fois écrites, les applications Service Fabric peuvent être exécutées dans plusieurs environnements d’hébergement sans modification ou avec des modifications minimales.
* Les connaissances en matière de génération d’applications Service Fabric s’appliquent d’un environnement d’hébergement à un autre.
* L’expérience opérationnelle d’exécution et de gestion de clusters Service Fabric s’applique d’un environnement à un autre.
* Ciblage étendu en termes de clientèle sans contrainte d’environnement d’hébergement.
* Une couche supplémentaire de fiabilité et de protection contre les pannes répandues existe, car vous pouvez déplacer les services vers un autre environnement de déploiement si un centre de données ou le fournisseur de services cloud est confronté à une panne d’électricité.

## Avantages des clusters Service Fabric sur Azure par rapport aux clusters Service Fabric créés en local
L’exécution de clusters Service Fabric sur Azure offre des avantages par rapport à l’option locale. Par conséquent, si vous n’avez pas d’exigences spécifiques concernant l’emplacement où vous exécutez vos clusters, nous vous suggérons de les exécuter en tant que clusters hébergés par Azure. Dans Azure, nous intégrons d’autres fonctionnalités et services Azure qui rendent l’exploitation et la gestion du cluster plus simple et plus fiable.

* **Portail Azure :** facilite la création et la gestion de clusters.

* **Azure Resource Manager :** l’utilisation d’Azure Resource Manager permet de faciliter la gestion de toutes les ressources utilisées par le cluster en tant qu’unité et de simplifier le suivi des coûts et la facturation.
* **Cluster Service Fabric en tant que ressource Azure :** un cluster Service Fabric est une ressource ARM, que vous pouvez modeler comme d’autres ressources ARM dans Azure.
* **Intégration à l’infrastructure Azure :** Service Fabric se coordonne avec l’infrastructure Azure sous-jacente pour que le système d’exploitation, le réseau et d’autres mises à niveau améliorent la disponibilité et la fiabilité de vos applications.
* **Diagnostics :** dans Azure, nous proposons l’intégration d’Azure Diagnostics et de Log Analytics.
* **Mise à l’échelle automatique :** pour les clusters sur Azure, nous fournissons une fonctionnalité de mise à l’échelle automatique intégrée provenant des jeux de mise à l’échelle de machine virtuelle. Dans des environnements locaux ou d’autres environnements cloud, vous devez créer votre propre fonctionnalité de mise à l’échelle automatique ou mettre à l’échelle manuellement à l’aide des API que Service Fabric expose pour la mise à l’échelle des clusters.

## Étapes suivantes
Créer un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création d’un cluster Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Créer un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Service Fabric sur Linux](service-fabric-linux-overview.md)

<!---HONumber=AcomDC_0921_2016-->