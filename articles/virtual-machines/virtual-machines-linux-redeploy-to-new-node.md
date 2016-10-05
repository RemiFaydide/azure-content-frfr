<properties 
	pageTitle="Redéployer des machines virtuelles Linux | Microsoft Azure" 
	description="Décrit comment redéployer des machines virtuelles Linux pour atténuer les problèmes de connexion SSH." 
	services="virtual-machines-linux" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-linux" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>

# Redéployer une machine virtuelle vers un nouveau nœud Azure

Si vous avez été confronté à des difficultés pour la résolution des problèmes SSH ou l’accès des applications à une machine virtuelle Azure, le redéploiement de la machine virtuelle peut vous aider. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise sous tension, conservant tous vos options de configuration et ressources associées. Cet article vous montre comment redéployer une machine virtuelle à l’aide de l’interface de ligne de commande Azure ou du portail Azure.

> [AZURE.NOTE] Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.


## Utilisation de l’interface de ligne de commande Azure

Assurez-vous que la [dernière interface de ligne de commande Azure est installée](../xplat-cli-install.md) sur votre ordinateur et que vous êtes en mode Resource Manager (`azure config mode arm`).

Utilisez la commande de l’interface de ligne de commande Azure suivante pour redéployer votre machine virtuelle :

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Vous pouvez voir l’état de la machine virtuelle évoluer tandis qu’elle passe par le processus de redéploiement. Le paramètre `PowerState` de la machine virtuelle passe de « Exécution » à « Mise à jour », puis à « Démarrage » et enfin « Exécution » tandis qu’elle passe par le processus de redéploiement sur un nouvel hôte. Vérifiez l’état des machines virtuelles au sein d’un groupe de ressources avec :

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Étapes suivantes
Vous pouvez rechercher une aide spécifique sur le [dépannage des connexions SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) ou sur les [étapes de dépannage détaillées des connexions SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) si vous rencontrez des problèmes de connexion à votre machine virtuelle. Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](virtual-machines-linux-troubleshoot-app-connection.md) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.

<!---HONumber=AcomDC_0921_2016-->