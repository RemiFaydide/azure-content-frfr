 <properties
   pageTitle="Azure et Linux | Microsoft Azure"
   description="Décrit les services de calcul, de stockage et de mise en réseau Azure à l’aide de machines virtuelles Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# Azure et Linux

Microsoft Azure propose une gamme croissante de services cloud publics intégrés, comprenant des analyses, des machines virtuelles, des bases de données, mobiles, la mise en réseau, le stockage et le web. En d’autres termes, il s’agit de la méthode idéale pour héberger vos solutions. Microsoft Azure fournit une plate-forme évolutive de cloud computing qui vous permet de payer uniquement ce que vous utilisez, quand vous le souhaitez, sans avoir à investir dans du matériel en local. Azure permet de faire face à toutes les exigences en matière de montée en puissance de vos solutions ou d’augmentation de la taille des instances.

Si vous êtes familiarisé avec les différentes fonctionnalités d’Amazon AWS, vous pouvez examiner le [document de mappage de définition](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) Azure vs AWS.


## Régions
Les ressources Microsoft Azure sont réparties sur plusieurs régions géographiques dans le monde. Une « région » représente plusieurs centres de données au sein d’une seule zone géographique. Au 1er janvier 2016, elles étaient au nombre de 8 en Amérique, 2 en Europe, 6 en Asie-Pacifique, 2 en Chine continentale et 3 en Inde. Si vous souhaitez obtenir la liste complète de toutes les régions Azure, nous tenons une liste des régions existantes et récemment annoncées.

- [Régions Azure](https://azure.microsoft.com/regions/)

## Availability
Afin que votre déploiement puisse bénéficier de notre contrat de niveau de service de 99,95 % pour les machines virtuelles, vous devez déployer au moins deux machines virtuelles exécutant votre charge de travail à l’intérieur d’un groupe à haute disponibilité. Ainsi, vos machines virtuelles sont réparties sur plusieurs domaines d’erreur dans nos centres de données et déployées sur des hôtes ayant des fenêtres de maintenance distinctes. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explique la disponibilité garantie d’Azure dans son ensemble.

## Machines virtuelles et instances Azure
Microsoft Azure prend en charge un certain nombre de distributions Linux populaires fournies et gérées par plusieurs partenaires. Vous trouverez des distributions comme Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD et plus encore dans Azure Marketplace. Nous travaillons activement avec différentes communautés Linux pour enrichir davantage la liste des [distributions Linux approuvées par Azure](virtual-machines-linux-endorsed-distros.md).

Si votre distribution Linux préférée n’est pas présente dans la galerie, vous pouvez « apporter votre propre machine virtuelle Linux » en [créant et téléchargeant un disque dur virtuel dans Azure](virtual-machines-linux-create-upload-generic.md).

Les machines virtuelles Azure vous permettent de déployer un large éventail de solutions informatiques et ce, en toute flexibilité. Vous pouvez déployer pratiquement toute charge de travail et tout langage sur presque n’importe quel système d’exploitation : Windows, Linux ou un système personnalisé créé à partir de l’un de nos nombreux partenaires. Vous ne trouvez toujours pas ce que vous cherchez ? Ne vous inquiétez pas, vous pouvez également ajouter vos propres images en local.

## Tailles de machine virtuelle
Lorsque vous déployez une machine virtuelle dans Azure, vous sélectionnez parmi nos tailles disponibles une taille de machine virtuelle qui convient à votre charge de travail. La taille influe également sur les capacités de traitement, de mémoire et de stockage de la machine virtuelle. Vous êtes facturé en fonction de la durée du fonctionnement de la machine virtuelle et de la consommation de ses ressources allouées. Une liste complète des [tailles de machine virtuelle](virtual-machines-linux-sizes.md).

Voici quelques conseils de base pour la sélection d’une taille de machine virtuelle parmi nos séries (A, D, DS, G et GS).

* Les machines virtuelles de série A sont des machines virtuelles d’entrée de gamme offrant un bon rapport qualité/prix et qui conviennent pour des charges de travail légères et des scénarios de développement/test. Elles sont largement disponibles dans l’ensemble des régions et peuvent se connecter et utiliser toutes les ressources standard disponibles pour les machines virtuelles.
* Les tailles de série A (A8 - A11) sont des configurations spéciales de calcul intensif adaptées aux applications de cluster informatique à hautes performances.
* Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire.
* La série Dv2 (la version la plus récente de la série D) offre un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,2 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.
* Les machines virtuelles de la série G offrent le plus de mémoire et s’exécutent sur des hôtes équipés de processeurs de la famille Intel Xeon E5 V3.

Remarque : les machines virtuelles des séries DS et GS peuvent utiliser un stockage Premium, qui offre un stockage SSD hautes performances à faible latence pour les charges de travail impliquant des E/S intensives. Le stockage Premium est disponible dans certaines régions. Pour plus d'informations, consultez les rubriques :

- [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure.](../storage/storage-premium-storage.md)

## Automatisation
Pour obtenir une culture DevOps appropriée, toute l’infrastructure doit être du code. Lorsque l’ensemble de l’infrastructure se trouve dans le code, elle est facile à recréer (serveurs Phoenix). Azure fonctionne avec tous les principaux outils d’automatisation, comme Ansible, Chef, SaltStack et Puppet. Azure propose également ses propres outils pour l’automatisation :

- [Les modèles Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

Azure déploie la prise en charge de [cloud-init](http://cloud-init.io/) sur la plupart des distributions Linux qui le prennent en charge. Actuellement, les machines virtuelles Ubuntu de Canonical sont déployées avec cloud-init activé par défaut. Fedora, CentOS et RedHat RHEL prennent en charge cloud-init, mais les images Azure maintenues par RedHat ne disposent pas de cloud-init installé. Pour utiliser cloud-init sur un système d’exploitation de la famille RedHat, vous devez créer une image personnalisée avec cloud-init installé.

- [À l’aide de cloud-init sur les machines virtuelles Linux Azure](virtual-machines-linux-using-cloud-init.md)

## Quotas
Chaque abonnement Azure comporte des limites de quota qui peuvent avoir un impact négatif sur le déploiement d’un grand nombre de machines virtuelles pour votre projet. La limite est de 20 machines virtuelles par région et par abonnement. Les limites de quota peuvent être augmentées en soumettant un ticket de support demandant leur hausse. Pour plus d’informations sur les limites de quota :

- [Limites du service d’abonnement Azure](../azure-subscription-service-limits.md)


## Partenaires

Microsoft travaille en étroite collaboration avec nos partenaires afin de garantir que les images disponibles sont mises à jour et optimisées pour un runtime Azure. Pour plus d’informations sur nos partenaires, consultez leurs pages marketplace ci-dessous.

- [Linux dans les distributions approuvées par Azure](virtual-machines-linux-endorsed-distros.md)

Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami Library pour Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - Mesosphere DC/OS sur Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - Azure Container Service avec Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## Installation sur Azure
Pour commencer à utiliser Azure, vous avez besoin d’un compte Azure, de l’interface de ligne de commande Azure installée et d’une paire de clés SSH (publique et privée).

## Création d’un compte
La première étape pour utiliser Azure Cloud consiste à créer un compte Azure. Accédez sur la page [Création d’un compte Azure](https://azure.microsoft.com/pricing/free-trial/) pour commencer.

## Installer l’interface de ligne de commande
Avec votre nouveau compte Azure, vous pouvez commencer immédiatement à utiliser le portail Azure, qui est un panneau d’administration web. Pour gérer Azure Cloud par ligne de commande, vous installez `azure-cli`. Installez [l’interface de ligne de commande Azure](../xplat-cli-install.md) sur votre station de travail Mac ou Linux.

## Création d’une paire de clés SSH
Vous avez maintenant un compte Azure, le portail web Azure et l’interface de ligne de commande Azure. L’étape suivante consiste à créer une paire de clés SSH utilisée pour exécuter SSH dans Linux sans utiliser de mot de passe. [Créez des clés SSH sur Linux et Mac](virtual-machines-linux-mac-create-ssh-keys.md) pour activer les connexions sans mot de passe et améliorer la sécurité.

## Prise en main de Microsoft Azure sur Linux
Avec la configuration de votre compte Azure, l’interface de ligne de commande Azure installée et les clés SSH créées, vous êtes maintenant prêt à créer une infrastructure dans Azure Cloud. La première tâche consiste à créer quelques machines virtuelles.

## Création d’une machine virtuelle sur l’interface de ligne de commande
La création d’une machine virtuelle Linux sur l’interface de ligne de commande est un moyen rapide de déployer une machine virtuelle sans quitter le terminal que vous utilisez. Tout ce que vous pouvez spécifier sur le portail web est disponible via un commutateur ou un indicateur de ligne de commande.

- [Créer une machine virtuelle Linux à l’aide de l’interface de ligne de commande](virtual-machines-linux-quick-create-cli.md)

## Créer une machine virtuelle dans le portail
La création d’une machine virtuelle sur le portail web Azure consiste à facilement définir les différentes options à la souris pour accéder à un déploiement. Au lieu d’utiliser des indicateurs de ligne de commande ou des commutateurs, vous pouvez afficher une jolie disposition d’options et de paramètres. Tous les éléments disponibles via l’interface de ligne de commande sont également disponibles sur le portail.

- [Créer une machine virtuelle Linux à l’aide du portail](virtual-machines-linux-quick-create-portal.md)

## Connexion avec SSH sans mot de passe
La machine virtuelle s’exécute maintenant sur Azure et vous êtes prêt à vous connecter. L’utilisation de mots de passe pour vous connecter via le protocole SSH est peu sûre et prend du temps. L’utilisation de clés SSH est la méthode la plus sûre et la plus rapide pour vous connecter. Lorsque vous créez vos machines virtuelles Linux via le portail ou l’interface de ligne de commande, vous avez deux possibilités pour l’authentification. Si vous choisissez un mot de passe pour SSH, Azure configure la machine virtuelle pour autoriser les connexions via les mots de passe. Si vous choisissez d’utiliser une clé publique SSH, Azure configure la machine virtuelle pour autoriser uniquement les connexions via les clés SSH et désactive les connexions de mot de passe. Pour sécuriser votre machine virtuelle Linux en autorisant uniquement les connexions par clé SSH, utilisez l’option de clé SSH publique lors de la création de machines virtuelles sur le portail ou dans l’interface de ligne de commande.

- [Désactiver les mots de passe SSH sur votre machine virtuelle Linux en configurant SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## Composants Azure connexes

## Storage

- [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md)

- [Ajouter un disque à une machine virtuelle Linux avec l’interface de ligne de commande Azure](virtual-machines-linux-add-disk.md)

- [Attachement d’un disque de données à une machine virtuelle Linux dans le portail Azure](virtual-machines-linux-attach-disk-portal.md)

## Mise en réseau

- [Présentation du réseau virtuel.](../virtual-network/virtual-networks-overview.md)

- [Adresses IP dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Ouverture de ports sur une machine virtuelle Linux dans Azure](virtual-machines-linux-nsg-quickstart.md)

- [Créer un nom de domaine complet dans le portail Azure](virtual-machines-linux-portal-create-fqdn.md)


## Conteneurs

- [Machines virtuelles et conteneurs dans Azure](virtual-machines-linux-containers.md)

- [Présentation d’Azure Container Service](../container-service/container-service-intro.md)

- [Déploiement d’un cluster Azure Container Service](../container-service/container-service-deployment.md)

## Étapes suivantes

Vous avez maintenant une vue d’ensemble de Linux sur Azure. L’étape suivante consiste à aller plus loin et créer plusieurs machines virtuelles !

- [Création d'une machine virtuelle Linux sur Azure à l’aide du portail](virtual-machines-linux-quick-create-portal.md)

- [Créer une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI)](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0921_2016-->