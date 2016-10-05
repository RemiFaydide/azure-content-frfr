<properties 
	pageTitle="Redéployer des machines virtuelles Windows | Microsoft Azure" 
	description="Décrit comment redéployer des machines virtuelles Windows pour atténuer les problèmes de connexion RDP." 
	services="virtual-machines-windows" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-windows" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>


# Redéployer une machine virtuelle vers un nouveau nœud Azure

Si vous avez été confronté à des difficultés pour la résolution des problèmes de connexion de Bureau à distance ou l’accès des applications à une machine virtuelle Azure basée sur Windows, le redéploiement de la machine virtuelle peut vous aider. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise sous tension, conservant tous vos options de configuration et ressources associées. Cet article vous montre comment redéployer une machine virtuelle à l’aide d’Azure PowerShell ou du portail Azure.

> [AZURE.NOTE] Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.

## Utilisation de Microsoft Azure PowerShell

Assurez-vous que vous avez installé la dernière version d’Azure PowerShell 1.x. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Utilisez cette commande Azure PowerShell pour redéployer votre machine virtuelle :

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Étapes suivantes
Si vous rencontrez des problèmes de connexion à votre machine virtuelle, vous pouvez rechercher une aide spécifique sur le [dépannage des connexions Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md) ou sur les [étapes de dépannage détaillées des connexions Bureau à distance](virtual-machines-windows-detailed-troubleshoot-rdp.md). Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](virtual-machines-windows-troubleshoot-app-connection.md) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.

<!---HONumber=AcomDC_0921_2016-->