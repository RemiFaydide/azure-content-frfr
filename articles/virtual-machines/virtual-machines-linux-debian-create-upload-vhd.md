<properties
	pageTitle="Préparer un disque dur virtuel Debian Linux | Microsoft Azure"
	description="Apprenez à créer des fichiers de disque dur virtuel Debian 7 et 8 pour un déploiement dans Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>



# Préparer un disque dur virtuel Debian pour Azure

## Conditions préalables
Cette section suppose que vous avez déjà installé un système d’exploitation Debian Linux à l’aide d’un fichier .iso téléchargé à partir du [site web Debian](https://www.debian.org/distrib/) sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Hyper-V n’est qu’un exemple parmi d’autres. Pour obtenir des instructions sur l’utilisation de Hyper-V, consultez [Installation du rôle Hyper-V et configuration d’une machine virtuelle](https://technet.microsoft.com/library/hh846766.aspx).


## Notes d'installation

- Consultez également les [Notes générales d’installation sous Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de l’applet de commande **convert-vhd**.
- Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peuvent être utilisées sur des disques de données si vous préférez.
- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. Vous pouvez configurer l’agent Linux Azure pour créer un fichier d’échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## Utiliser Azure-Manage pour créer des disques durs virtuels Debian

Il existe plusieurs outils permettant de créer des disques durs virtuels Debian pour Azure, par exemple les scripts [azure-manage](https://github.com/credativ/azure-manage) de [credativ](http://www.credativ.com/). Cette approche est préférable à la création d'une image à partir de zéro. Par exemple, pour créer un disque dur virtuel Debian 8, exécutez les commandes suivantes pour télécharger azure-manage (et les dépendances) puis exécutez le script azure\_build\_image :

	# sudo apt-get update
	# sudo apt-get install git qemu-utils mbr kpartx debootstrap

	# sudo apt-get install python3-pip
	# sudo pip3 install azure-storage azure-servicemanagement-legacy pytest pyyaml
	# git clone https://github.com/credativ/azure-manage.git
	# cd azure-manage
	# sudo pip3 install .

	# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## Préparer manuellement un disque dur virtuel Debian

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3. Si vous configurez la machine virtuelle par rapport à un fichier ISO, commentez la ligne **deb cdrom** dans `/etc/apt/source.list`.

4. Modifiez le fichier `/etc/default/grub` et le paramètre **GRUB\_CMDLINE\_LINUX** comme suit pour y inclure des paramètres de noyau supplémentaires pour Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=30"

5. Régénérez le grub et exécutez-le :

        # sudo update-grub

6. Ajoutez les référentiels Azure de Debian à /etc/apt/sources.list pour Debian 7 ou 8 :

	**Debian 7.x "Wheezy"**

		deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
		deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


	**Debian 8.x "Jessie"**

		deb http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure jessie main
		deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Installez l'agent Linux Azure :

		# sudo apt-get update
		# sudo apt-get install waagent

8. Pour Debian 7, il est nécessaire d’exécuter le noyau 3.16 à partir du référentiel wheezy-backports. Commencez par créer un fichier nommé /etc/apt/preferences.d/linux.pref avec le contenu suivant :

		Package: linux-image-amd64 initramfs-tools
		Pin: release n=wheezy-backports
		Pin-Priority: 500

	Puis exécutez "sudo apt-get install linux-image-amd64" pour installer le nouveau noyau.

8. Mettez hors service la machine virtuelle, préparez-la pour un approvisionnement sur Azure, puis exécutez :

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


## Étapes suivantes

Vous êtes maintenant prêt à utiliser votre disque dur virtuel Debian pour créer des machines virtuelles dans Azure. S’il s’agit de la première fois que vous chargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel contenant le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0831_2016-->