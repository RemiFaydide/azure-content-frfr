<properties
   pageTitle="Test de SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux | Microsoft Azure"
   description="Test de SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux


Cet article décrit les divers éléments à prendre en compte lorsque vous exécutez SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux. À compter du 19 mai 2016, SAP NetWeaver est officiellement pris en charge sur les machines virtuelles SUSE Linux dans Azure. Vous trouverez tous les détails concernant les versions de Linux, les versions du noyau SAP, etc. dans la note SAP 1928533 « Applications SAP sur Azure : produits et types de machines virtuelles pris en charge (SAP Applications on Azure: Supported Products and Azure VM types) ». Vous trouverez une documentation supplémentaire concernant SAP sur des machines virtuelles Linux ici : [Utilisation de SAP sur des machines virtuelles Linux](virtual-machines-linux-sap-get-started.md).


Les informations suivantes devraient vous aider à éviter certains pièges potentiels.

## Images SUSE sur Azure pour l’exécution de SAP

Pour exécuter SAP NetWeaver sur Azure, utilisez uniquement SUSE Linux Enterprise Server SLES 12 (SPx) - Voir également la note SAP 1928533. Une image SUSE spéciale se trouve dans Azure Marketplace (« SLES 11 SP3 for SAP CAL »), mais elle n’est pas destinée à une utilisation générale. N’utilisez pas cette image, car elle est réservée à la solution [SAP Cloud Appliance Library](https://cal.sap.com/).

Tous les nouveaux tests et toutes les installations sur Azure doivent être effectués avec Azure Resource Manager. Pour rechercher des images et des versions SUSE SLES à l’aide d’Azure PowerShell ou de l’interface de ligne de commande (CLI) Azure, utilisez les commandes suivantes. Vous pouvez ensuite utiliser la sortie, par exemple pour définir l’image du système d’exploitation dans un modèle JSON pour le déploiement d’une nouvelle machine virtuelle SUSE Linux. Ces commandes PowerShell sont valides pour Azure PowerShell version 1.0.1 et ses versions ultérieures.

* Rechercher les éditeurs existants, notamment SUSE :

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Rechercher les offres existantes de SUSE :

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Rechercher les offres SUSE SLES :

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Rechercher une version spécifique d’un SKU SLES :

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Installation de WALinuxAgent dans une machine virtuelle SUSE

L’agent appelé WALinuxAgent fait partie des images SLES dans Azure Marketplace. Pour plus d’informations sur son installation manuelle (par exemple, lors du chargement d’un disque dur virtuel (VHD) du système d’exploitation SLES à partir d’un emplacement local), voir les articles suivants :

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Microsoft Azure](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## « Surveillance améliorée (Enhanced Monitoring) » SAP

La surveillance améliorée de SAP est une condition préalable obligatoire pour l’exécution de SAP sur Azure. Vérifiez les détails dans la note SAP 2191498 « SAP sur Linux avec Azure : surveillance améliorée (SAP on Linux with Azure: Enhanced Monitoring) ».

## Attachement de disques de données Azure à une machine virtuelle Azure Linux

Ne montez jamais de disques de données Azure dans une machine virtuelle Azure Linux en utilisant l’ID de périphérique. À la place, utilisez l’identificateur unique universel (UUID). Soyez prudent, par exemple lorsque vous utilisez des outils graphiques pour monter des disques de données Azure. Vérifiez bien les entrées dans /etc/fstab.

Le problème avec l’ID de périphérique, c’est qu’il peut changer et la machine virtuelle Azure peut se bloquer pendant le processus de démarrage. Pour atténuer ce problème, vous pouvez ajouter le paramètre nofail dans /etc/fstab. Toutefois, utilisez nofail avec précaution, car les applications risquent d’utiliser le point de montage précédent et d’écrire dans le système de fichiers racine au cas où un disque de données Azure externe n’a pas été monté lors du démarrage.

La seule exception au montage par le biais de l’UUID concerne l’attachement d’un disque du système d’exploitation à des fins de résolution des problèmes, comme décrit dans la section suivante.

## Résolution des problèmes d’une machine virtuelle SUSE devenue inaccessible

Il peut arriver qu’une machine virtuelle SUSE dans Azure se bloque pendant le processus de démarrage (par exemple, en raison d’une erreur liée au montage de disques). Vous pouvez vérifier ce problème à l’aide de la fonctionnalité des diagnostics de démarrage pour Azure Virtual Machines v2 dans le portail Azure. Pour plus d’informations, voir [Boot diagnostics](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) (Diagnostics de démarrage).

L’une des solutions pour résoudre le problème consiste à attacher le disque du système d’exploitation de la machine virtuelle endommagée à une autre machine virtuelle SUSE dans Azure. Il convient ensuite d’apporter les changements appropriés, par exemple en modifiant /etc/fstab ou en supprimant les règles udev réseau, comme décrit dans la section suivante.

Un élément important doit cependant être pris en compte. Le déploiement de plusieurs machines virtuelles SUSE à partir de la même image Azure Marketplace (par exemple, SLES 11 SP4) entraîne le montage systématique du disque du système d’exploitation par le même UUID. Par conséquent, l’utilisation de l’UUID pour attacher un disque du système d’exploitation provenant d’une autre machine virtuelle déployée à l’aide de la même image Azure Marketplace produit deux UUID identiques. Cela pose des problèmes et peut indiquer que la machine virtuelle destinée à la résolution des problèmes démarre en réalité à partir du disque du système d’exploitation attaché et endommagé, et non à partir du disque d’origine.

Vous disposez de deux méthodes pour éviter cette situation :

* Utilisez une image Azure Marketplace différente pour la machine virtuelle de dépannage (par exemple, SLES 11 SPx au lieu de SLES 12).
* N’attachez pas le disque du système d’exploitation endommagé d’une autre machine virtuelle en utilisant l’UUID, mais procédez différemment.

## Chargement d’une machine virtuelle SUSE à partir d’un emplacement local vers Azure

Pour obtenir une description de la procédure de chargement d’une machine virtuelle SUSE à partir d’un emplacement local vers Azure, voir [Préparation d’une machine virtuelle SLES ou openSUSE pour Azure](virtual-machines-linux-create-upload-vhd-suse.md).

Si vous souhaitez charger une machine virtuelle en omettant l’étape finale d’annulation de l’approvisionnement (par exemple, pour conserver une installation SAP existante en plus du nom d’hôte), suivez les consignes suivantes :

* Assurez-vous que le disque du système d’exploitation est monté par l’intermédiaire de l’UUID, et non de l’ID de périphérique. Utiliser l’UUID dans /etc/fstab ne suffit pas pour le disque du système d’exploitation. N’oubliez pas non plus d’adapter le chargeur de démarrage par le biais de YaST ou en modifiant /boot/grub/menu.lst.
* Si vous utilisez le format VHDX pour le disque du système d’exploitation SUSE et que vous le convertissez en disque dur virtuel pour le chargement dans Azure, il est très probable que le périphérique réseau bascule d’eth0 vers eth1. Pour éviter tout problème lors du démarrage ultérieur dans Azure, vous devez rétablir eth0 comme décrit dans l’article [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) (Correction d’eth0 dans SLES 11 cloné dans VMware).

En plus de ce qui est décrit dans l’article, nous vous recommandons de supprimer la ligne suivante :

   /lib/udev/rules.d/75-persistent-net-generator.rules

Vous pouvez également installer l’agent Linux Azure (waagent) pour éviter tout problème potentiel, à condition qu’il n’y ait pas plus d’une carte d’interface réseau.

## Déploiement d’une machine virtuelle SUSE sur Azure

Pour créer des machines virtuelles SUSE, vous devez utiliser les fichiers de modèle JSON dans le nouveau modèle Azure Resource Manager. Après la création du fichier de modèle JSON, vous pouvez déployer la machine virtuelle à l’aide de la commande CLI suivante comme alternative à PowerShell :

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Pour plus d’informations sur les fichiers de modèle JSON, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md) et [Modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/documentation/templates/).

Pour plus d’informations sur l’interface de ligne de commande et sur Azure Resource Manager, consultez la rubrique [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## Licence SAP et clé matérielle

Pour la certification SAP-Azure officielle, un nouveau mécanisme a été introduit afin de calculer la clé matérielle SAP utilisée pour la licence SAP. Le noyau SAP a dû être adapté pour utiliser ce mécanisme. Les anciennes versions du noyau SAP pour Linux n’incluent pas cette modification du code. Ainsi, dans certaines situations (par exemple, en cas de redimensionnement de machine virtuelle Azure), la clé matérielle SAP est modifiée, et la licence SAP risque de ne plus être valide. Ce problème est résolu dans les derniers noyaux Linux SAP. Pour plus d’informations, consultez la note SAP 1928533.

## Package sapconf SUSE / tuned-adm

SUSE fournit un package appelé « sapconf » qui gère un ensemble de paramètres propres à SAP. Pour plus d’informations sur le rôle, l’installation et l’utilisation de ce package, voir [Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems (Utilisation de sapconf pour préparer un serveur SUSE Linux Enterprise Server à l’exécution de systèmes SAP)](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) et [What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems? (Qu’est-ce que sapconf et comment préparer un serveur SUSE Linux Enterprise Server à l’exécution de systèmes SAP ?)](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

En attendant, il existe un nouvel outil qui remplace sapconf - tuned-adm. Des informations supplémentaires sur cet outil sont disponibles en suivant les deux liens ci-dessous.

Vous trouverez [ici](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) la documentation SLES sur le profil tuned-adm sap-hana

Les systèmes de paramétrage pour les charges de travail SAP avec tuned-adm se trouvent [ici](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf), au chapitre 6.2


## Partage NFS dans les installations SAP distribuées

Si vous disposez d’une installation distribuée, par exemple lorsque vous souhaitez installer la base de données et les serveurs d’applications SAP sur des machines virtuelles distinctes, vous pouvez partager le répertoire/sapmnt par l’intermédiaire du système de gestion de fichiers en réseau (NFS). Si vous rencontrez des problèmes lors des étapes d’installation après la création du partage NFS pour /sapmnt, vérifiez si « no\_root\_squash » est défini pour le partage.

## Volumes logiques

Si un gros volume logique était nécessaire sur plusieurs disques de données Azure (par exemple, pour la base de données SAP) dans le passé, il était recommandé d’utiliser mdadm, car lvm n’était pas encore entièrement validé sur Azure. Pour plus d’informations sur la procédure de configuration de RAID Linux sur Azure à l’aide de mdadm, voir [Configuration d’un RAID logiciel sur Linux](virtual-machines-linux-configure-raid.md). À partir de début de mai 2016 lvm sera également entièrement pris en charge sur Azure et pourra être utilisé comme alternative à mdadm. Pour plus d’informations sur lvm sur Azure, consultez la rubrique [Configurer LVM sur une machine virtuelle Linux dans Azure](virtual-machines-linux-configure-lvm.md).


## Dépôt SUSE Azure

Si vous rencontrez un problème d’accès au référentiel Azure SUSE standard, vous pouvez réinitialiser ce dernier à l’aide d’une commande simple. Ce problème peut se produire si vous créez une image de système d’exploitation privée dans une région Azure, puis que vous la copiez dans une autre région où vous souhaitez déployer de nouvelles machines virtuelles basées sur cette image. Il vous suffit d’exécuter la commande suivante à l’intérieur de la machine virtuelle :

   ```
   service guestregister restart
   ```

## Bureau Gnome

Si vous souhaitez utiliser le bureau Gnome pour installer un système complet de démonstration SAP dans une même machine virtuelle, comprenant une interface utilisateur SAP, un navigateur et une console de gestion SAP, suivez le conseil ci-après pour l’installer sur les images Azure SLES :

   Pour SLES 11 :

   ```
   zypper in -t pattern gnome
   ```

   Pour SLES 12 :

   ```
   zypper in -t pattern gnome-basic
   ```

## Prise en charge par SAP d’Oracle sur Linux dans le cloud

Il existe une restriction de prise en charge d’Oracle sur Linux dans les environnements virtualisés. Bien que ce sujet ne soit pas spécifique d’Azure, il est important de bien le comprendre. SAP ne prend pas en charge Oracle sur SUSE ni Red Hat dans un cloud public comme Azure. Pour discuter de ce sujet, contactez directement Oracle.

<!---HONumber=AcomDC_0921_2016-->