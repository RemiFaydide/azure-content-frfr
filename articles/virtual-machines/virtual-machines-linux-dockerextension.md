<properties
   pageTitle="En savoir plus sur l’extension de machine virtuelle Docker sur Azure | Microsoft Azure"
   description="Découvrez comment utiliser l’extension de machine virtuelle Docker sur Azure pour déployer un environnement Docker rapidement et en toute sécurité dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="07/20/2016"
   ms.author="iainfou"/>

# Utilisation de l’extension de machine virtuelle Docker pour déployer votre environnement

> [AZURE.NOTE] Si vous avez un moment, aidez-nous à améliorer la documentation relative aux machines virtuelles Azure Linux en répondant à cette [enquête rapide](https://aka.ms/linuxdocsurvey) concernant vos expériences. Chaque réponse nous aide à vous faciliter la tâche.

Docker est une solution courante de gestion de conteneurs et une plateforme de création d’images qui permet de travailler rapidement avec des conteneurs sous Linux (et Windows). Avec Azure, vous avez la possibilité de déployer Docker de différentes manières selon vos besoins :

- Si vous souhaitez développer rapidement le prototype d’une application, vous pouvez [utiliser le pilote Docker Machine Azure](./virtual-machines-linux-docker-machine.md) pour déployer des hôtes Docker dans Azure.
- L’extension de machine virtuelle Docker pour les machines virtuelles Azure est utilisée dans les déploiements basés sur un modèle. Cette approche peut s’intégrer à des déploiements du modèle Azure Resource Manager et inclut tous les avantages associés tels que l’accès en fonction du rôle, les diagnostics et la configuration post-déploiement.
- L’extension de machine virtuelle Docker prend également en charge Docker Compose. Docker Compose utilise un fichier YAML déclaratif pour utiliser une application modélisée par un développeur dans tout environnement et générer un déploiement cohérent.
- Vous pouvez également [déployer un cluster Docker Swarm complet sur les services de conteneur Azure](../container-service/container-service-deployment.md) pour des déploiements prêts pour la production et évolutifs qui exploitent les outils de planification et de gestion supplémentaires fournis par Swarm.

Cet article se concentre sur l’utilisation de modèles Ressource Manager pour déployer l’extension de machine virtuelle Docker dans un environnement personnalisé et prêt pour la production que vous définissez.

## Extension de machine virtuelle Docker sur Azure pour les déploiements de modèle

L’extension de machine virtuelle Docker Azure installe et configure le démon Docker, le client Docker et Docker Compose dans votre machine virtuelle Linux. Cette extension permet également de définir et déployer des applications de conteneur à l’aide de Docker Compose. L’utilisation de l’extension de machine virtuelle Docker Azure est parfaitement adaptée aux environnements de développement ou de production plus robustes, car vous disposez de contrôles supplémentaires en utilisant simplement Docker Machine ou en créant l’hôte Docker vous-même.

Grâce à Azure Resource Manager, vous pouvez créer et déployer des modèles qui définissent la structure complète de votre environnement. Les modèles vous permettent de définir les hôtes Docker, le stockage, les contrôles d’accès en fonction du rôle (RBAC), les diagnostics, etc. Vous pouvez [lire des documentations supplémentaires sur Ressource Manager](../resource-group-overview.md) et les modèles afin de mieux en comprendre certains avantages. À l’aide de modèles Resource Manager, vous pourrez également reproduire les déploiements en fonction de vos besoins futurs.

## Déployer un modèle avec l’extension de machine virtuelle Docker :

Nous allons utiliser un modèle existant de démarrage rapide pour montrer comment déployer une machine virtuelle Ubuntu comportant une installation de l’extension de machine virtuelle Docker. Vous pouvez voir le modèle ici : [Simple deployment of an Ubuntu VM with Docker (Déploiement simple d’une machine virtuelle Ubuntu avec Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Vous avez également besoin de la [dernière version de l’interface de ligne de commande Azure](../xplat-cli-install.md) en mode Resource Manager (`azure config mode arm`).

Déployez le modèle à l’aide de l’interface de ligne de commande Azure en spécifiant un nom pour le nouveau groupe de ressources (ici `myDockerResourceGroup`), ainsi que l’URI du modèle :

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Répondez aux invites pour nommer votre compte de stockage et indiquer le nom d’utilisateur, le mot de passe et le nom DNS. Le résultat ressemble à ce qui suit en exemple :

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

L’interface de ligne de commande d’Azure vous renvoie à l’invite après quelques secondes, mais le modèle est déployé en arrière-plan dans le groupe de ressources que vous avez créé. Attendez quelques minutes pour que le déploiement se termine avant d’essayer d’utiliser SSH pour la machine virtuelle.

Vous pouvez obtenir plus d’informations sur le déploiement et le nom DNS de la machine virtuelle avec la commande `azure vm show`. Dans l’exemple suivant, remplacez `myDockerResourceGroup` par le nom que vous avez spécifié à l’étape précédente :

```bash
azure vm show -g myDockerResourceGroup -n myDockerVM
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/mydockerresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_F1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :osdisk1
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :http://mydockerstorage.blob.core.windows.net/vhds/osdiskfordockersimple.vhd
data:
data:    OS Profile:
data:      Computer Name                 :MyDockerVM
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mydockergroup.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Vers le haut de la sortie, vous voyez le `ProvisioningState` de la machine virtuelle. Lorsque `Succeeded` s’affiche, le déploiement est terminé et vous pouvez vous connecter par SSH à la machine virtuelle.

Vers la fin de la sortie, `FQDN` affiche le nom de domaine complet basé sur le nom DNS que vous avez fourni et l’emplacement que vous avez sélectionné. Ce nom de domaine complet est ce que vous utilisez pour vous connecter par SSH à la machine virtuelle dans les étapes restantes.


## Déployer votre premier conteneur nginx
Une fois le déploiement terminé, utilisez SSH pour votre nouvel hôte Docker à l’aide du nome DNS fourni pendant le déploiement. Essayons d’exécuter un conteneur nginx :

```
sudo docker run -d -p 80:80 nginx
```

Le résultat ressemble à ce qui suit en exemple :

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Examinez le conteneur en cours d’exécution sur votre ordinateur hôte à l’aide de `sudo docker ps` :

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Pour voir votre conteneur en action, ouvrez un navigateur Web et entrez le nom DNS spécifié lors du déploiement :

![Exécution d’un conteneur ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Vous pouvez configurer le port TCP du démon Docker, la sécurité, ou déployer des conteneurs à l’aide de Docker Compose. Pour plus d’informations, consultez le [projet GitHub Extension de machine virtuelle Azure pour Docker](https://github.com/Azure/azure-docker-extension/).

## Référence de modèle JSON pour l’extension de machine virtuelle Docker

Cet exemple utilise un modèle de démarrage rapide. Pour déployer l’extension de machine virtuelle Azure Docker avec vos propres modèles Resource Manager, ajoutez les éléments JSON suivants :

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Pour découvrir la procédure pas à pas d’utilisation de modèles Resource Manager, voir la [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md)

## Étapes suivantes

Lire des étapes plus détaillées pour les différentes options de déploiement :

1. [Utiliser Docker Machine avec le pilote Azure](./virtual-machines-linux-docker-machine.md)
2. [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Microsoft Azure](./virtual-machines-linux-classic-cli-use-docker.md)
3. [Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples sur une machine virtuelle Azure](virtual-machines-linux-docker-compose-quickstart.md).
3. [Déploiement d’un cluster Azure Container Service](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0921_2016-->