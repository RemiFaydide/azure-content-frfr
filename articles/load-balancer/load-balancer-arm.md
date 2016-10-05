<properties
   pageTitle="Aperçu du support d’Azure Resource Manager pour l’équilibrage de charge | Microsoft Azure "
   description="Utilisation de Powershell pour l’équilibrage de charge avec Azure Resource Manager en version préliminaire. Utilisation de modèles pour l'équilibrage de charge"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2016"
   ms.author="sewhee" />


# Support d’Azure Resource Manager pour l’équilibrage de charge

Azure Resource Manager est l’infrastructure de gestion des services par défaut dans Azure. Azure Load Balancer peut être géré à l’aide des outils et des API basés sur Azure Resource Manager.

## Concepts

Avec Azure Resource Manager, Azure Load Balancer contient les ressources enfants suivantes :

- Configuration d’une adresse IP frontale : un équilibreur de charge peut inclure une ou plusieurs adresses IP frontales, également appelées « adresses IP virtuelles ». Ces adresses IP servent d'entrée pour le trafic.

- Pool d’adresses principal : il s’agit des adresses IP associées à la carte d’interface réseau (NIC) des machines virtuelles vers lesquelles la charge sera distribuée.

- Règles d'équilibrage de charge : une propriété de règle mappe une combinaison donnée d'adresse IP et de port frontaux vers un ensemble de combinaisons d'adresses IP et de port principaux. Un même équilibreur de charge peut avoir plusieurs règles d’équilibrage de charge. Chaque règle est une combinaison d’une adresse IP et d’un port frontaux et d’une adresse IP et d’un port principaux associés aux machines virtuelles.

- Sondes : les sondes vous permettent d'effectuer le suivi de l'intégrité des instances de machine virtuelle. En cas d'échec d'une sonde d'intégrité, l'instance de machine virtuelle est automatiquement mise hors service.

- Règles NAT de trafic entrant : règles NAT définissant le trafic entrant qui transite via l'adresse IP frontale et est distribué à l'adresse IP principale.

![](./media/load-balancer-arm/load-balancer-arm.png)

## Modèles de démarrage rapide

Azure Resource Manager vous permet d’approvisionner vos applications à l'aide d'un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.

Les modèles peuvent inclure des définitions de machines virtuelles, de réseaux virtuels, de groupes à haute disponibilité, d’interfaces réseau (NIC), de comptes de stockage, d’équilibreurs de charge, de groupes de sécurité réseau et d’adresses IP publiques. Avec des modèles, vous pouvez créer tout ce dont vous avez besoin pour une application complexe. Le contrôle de version et la collaboration du fichier de modèle peuvent être vérifiés dans le système de gestion de contenu.

[En savoir plus sur les modèles](http://go.microsoft.com/fwlink/?LinkId=544798)

[En savoir plus sur les ressources de réseau](../virtual-network/resource-groups-networking.md)

Des modèles de démarrage rapide utilisant Azure Load Balancer se trouvent dans un [référentiel GitHub](https://github.com/Azure/azure-quickstart-templates) qui héberge un ensemble de modèles générés par la communauté.

Exemples de modèles :

- [2 machines virtuelles dans un équilibrage de charge et les règles d'équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 machines virtuelles dans un réseau virtuel avec un équilibrage de charge interne et les règles d’équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 machines virtuelles dans un équilibrage de charge et la configuration des règles NAT sur l'équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544801)


## Configuration de l'équilibrage de charge Azure avec PowerShell ou la CLI

Prise en main des applets de commande, des outils de ligne de commande et des API REST Azure Resource Manager

- Les [cmdlets de mise en réseau Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) peuvent être utilisées pour créer un équilibrage de charge.
- [Création d’un équilibrage de charge à l’aide d’Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
- [Utilisation de la CLI Azure avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md)
- [API REST de l'équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## Étapes suivantes

Vous pouvez également [commencer par créer un équilibrage de charge avec accès par Internet](load-balancer-get-started-internet-arm-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement spécifique de trafic réseau d’équilibrage de charge.

Découvrez comment gérer les [paramètres de délai d’expiration TCP inactif pour un équilibreur de charge](load-balancer-tcp-idle-timeout.md). C’est important lorsque votre application a besoin de conserver des connexions actives pour les serveurs situés derrière un équilibreur de charge.

<!---HONumber=AcomDC_0921_2016-->