<properties
	pageTitle="Différentes façons de créer une machine virtuelle Linux | Microsoft Azure"
	description="Découvrez les différentes façons de créer une machine virtuelle Linux sur Azure, avec des liens vers des outils et des didacticiels pour chaque méthode."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Les différentes méthodes de création d’une machine virtuelle Linux dans Azure

Dans Azure, vous avez la possibilité de créer une machine virtuelle (VM) Linux à l’aide des outils et des flux de travail qui vous conviennent. Cet article résume ces différences et fournit des exemples pour créer vos machines virtuelles Linux.


## Interface de ligne de commande Azure 

L’interface de ligne de commande Azure est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Vous pouvez en savoir plus sur [la manière d’installer et de configurer l’interface de ligne de commande Azure](../xplat-cli-install.md). Les didacticiels suivants fournissent des exemples d’utilisation de l’interface de commande Azure. Lisez chaque article pour en savoir plus sur les commandes de démarrage rapide de l’interface de ligne de commande indiquées :

- [Créer une machine virtuelle Linux à partir de l’interface CLI Azure pour le développement et le test](virtual-machines-linux-quick-create-cli.md)
	- L’exemple suivant crée une VM CoreOS à l’aide d’une clé publique nommée `azure_id_rsa.pub` :

	```bash
	azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
		--image-urn CoreOS
	```

- [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
	- L’exemple suivant crée une machine virtuelle à l’aide d’un modèle stocké sur GitHub :

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

- [Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-create-cli-complete.md)
	- Inclut la création d’un équilibrage de charge et de plusieurs machines virtuelles dans un groupe à haute disponibilité.

- [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md)
	- L’exemple suivant ajoute un disque de 5 Go à une machine virtuelle existante nommée `TestVM` :

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
		--size-in-GB 5
	```

## Portail Azure

Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement une machine virtuelle, puisque vous n’avez rien à installer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

- [Créer une machine virtuelle Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md)
- [Attacher un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md)


## Système d'exploitation et choix d'images
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, téléchargez l’une de vos propres images (voir [la section ci-dessous](#use-your-own-image)).

### Images Microsoft Azure
Utilises les commandes de l’interface de ligne de commande `azure vm image` pour voir ce qui est disponible par éditeur, version de distributeur et build.

Procédez comme suit pour répertorier les éditeurs disponibles :

```bash
azure vm image list-publishers --location WestUS
```

Procédez comme suit pour répertorier les produits disponibles (offres) pour un éditeur donné :

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Procédez comme suit pour répertorier les références disponibles (versions distributeur) d’une offre donnée :

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Procédez comme suit pour répertorier toutes les images disponibles pour une version donnée :

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles, consultez la page [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure](virtual-machines-linux-cli-ps-findimage.md).

Les commandes `azure vm quick-create` et `azure vm create` disposent d’alias que vous pouvez utiliser pour accéder rapidement aux distributeurs les plus courants et à leurs versions les plus récentes. Il est souvent plus rapide d’utiliser des alias que d’avoir à spécifier l’éditeur, l’offre, la référence et la version à chaque fois que vous créez une machine virtuelle :

| Alias | Éditeur | Offer | SKU | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | le plus récent |
| CoreOS | CoreOS | CoreOS | Stable | le plus récent |
| Debian | credativ | Debian | 8 | le plus récent |
| openSUSE | SUSE | openSUSE | 13\.2 | le plus récent |
| RHEL | Redhat | RHEL | 7,2 | le plus récent |
| SLES | SLES | SLES | 12-SP1 | le plus récent |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | le plus récent |

### Utiliser votre propre image

Si vous avez besoin de personnalisations spécifiques, vous pouvez utiliser une image basée sur une machine virtuelle Azure existante, en *capturant* cette machine virtuelle. Vous pouvez également télécharger une image créée sur site. Pour plus d’informations sur les versions prises en charge et comment utiliser vos propres images, consultez les articles suivants :

- [Distributions prises en charge par Azure](virtual-machines-linux-endorsed-distros.md)

- [Informations concernant les distributions non approuvées](virtual-machines-linux-create-upload-generic.md)

- [Comment capturer une machine virtuelle Linux pour utiliser un modèle de gestionnaire de ressources](virtual-machines-linux-capture-image.md).
	- Exemples de commandes rapides pour la capture d’une machine virtuelle existante :

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Étapes suivantes

- Créez une machine virtuelle Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md), avec l’[interface de ligne de commande](virtual-machines-linux-quick-create-cli.md) ou à l’aide d’un [modèle](virtual-machines-linux-cli-deploy-templates.md) Azure Resource Manager.

- Après avoir créé une machine virtuelle Linux, [ajoutez un disque de données](virtual-machines-linux-add-disk.md).

- Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0928_2016-->