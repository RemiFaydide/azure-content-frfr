<properties
	pageTitle="Migrer vers Resource Manager avec PowerShell | Microsoft Azure"
	description="Cet article décrit pas à pas la procédure de migration de ressources IaaS prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager à l’aide de commandes Azure PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="danlep"/>

# Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell

Ces étapes vous montrent comment utiliser les commandes Azure PowerShell pour migrer des ressources d’infrastructure en tant que service (IaaS) à partir du modèle de déploiement Classic vers le modèle de déploiement Azure Resource Manager. Ces étapes appliquent une approche de commandes à remplir pour la migration de votre environnement personnalisé. Utilisez les commandes et remplacez les variables (lignes commençant par « $ ») par vos propres valeurs.

Si vous le souhaitez, vous pouvez également migrer des ressources à l’aide de [l’Interface de ligne de commande Azure (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

Pour plus d’informations sur les scénarios de migration pris en charge, consultez [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md). Pour un guide détaillé et une procédure pas à pas pour la migration, consultez [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## Étape 1 : Planification de la migration

Voici quelques bonnes pratiques recommandées lorsque vous évaluez la migration de ressources IaaS d’un environnement Classic vers Resource Manager.

- Lisez les [Fonctionnalités et configurations prises en charge et non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez des machines virtuelles qui utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
-	Si vous avez déjà des scripts automatisés qui déploient votre infrastructure et vos applications, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Vous pouvez également configurer des environnements de test à l’aide du portail Azure.

>[AZURE.IMPORTANT]Les passerelles de réseau virtuel (site à site, Azure ExpressRoute, passerelle d’application, point à site) ne sont actuellement pas prises en charge pour la migration de déploiement classique vers Resource Manager. Pour migrer un réseau virtuel classique avec une passerelle, supprimez d’abord la passerelle avant d’exécuter une opération de validation pour déplacer le réseau. (Vous pouvez exécuter une étape de préparation sans supprimer la passerelle.) Ensuite, après avoir effectué la migration, reconnectez la passerelle dans Azure Resource Manager.

## Étape 2 : installation de la dernière version d’Azure PowerShell

Il existe deux options principales d’installation d’Azure PowerShell : [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) et [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu. Cet article est basé sur Azure PowerShell version 2.1.0.

Pour connaître la procédure d’installation, consultez l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## Étape 3 : définir votre abonnement et s’inscrire pour la migration

Commencez par démarrer une invite de commandes PowerShell. Pour les scénarios de migration, vous devez configurer votre environnement à la fois pour l’environnement Classic et pour Resource Manager.

Connectez-vous à votre compte pour le modèle Resource Manager.

	Login-AzureRmAccount

Obtenez tous les abonnements disponibles à l’aide de la commande suivante :

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure pour la session active. Remplacez tous les éléments entre guillemets, notamment les caractères < et >, par le nom approprié.

	Select-AzureRmSubscription –SubscriptionName "<subscription name>"

>[AZURE.NOTE] L’inscription constitue une étape unique, mais vous devez le faire une seule fois avant de tenter la migration. Sans vous inscrire, vous verrez le message d’erreur suivant :

>	*BadRequest : Subscription is not registered for migration.* 

Inscrivez-vous auprès du fournisseur de ressources de migration à l’aide de la commande suivante :
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Veuillez patienter cinq minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante :

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Assurez-vous que RegistrationState est `Registered` avant de continuer.

Connectez-vous à votre compte pour le modèle Classic.

	Add-AzureAccount

Obtenez tous les abonnements disponibles à l’aide de la commande suivante :

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure pour la session active. Remplacez tous les éléments entre guillemets, notamment les caractères < et >, par le nom approprié.

	Select-AzureSubscription –SubscriptionName "<subscription name>"

## Étape 4 : vérification du nombre de cœurs de machines virtuelles Azure Resource Manager dans la région Azure de votre déploiement ou réseau virtuel actuel

Vous pouvez utiliser la commande PowerShell suivante pour vérifier la quantité de cœurs dont vous disposez actuellement dans Azure Resource Manager. Pour en savoir plus sur les quotas de cœurs, consultez [Limites et Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## Étape 5 : exécution de commandes pour effectuer la migration de vos ressources IaaS

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme réessaie alors d’exécuter l’action.

### Migration de machines virtuelles dans un service cloud (ne figurant pas dans un réseau virtuel)

Obtenez la liste des services cloud à l’aide de la commande suivante, puis choisissez le service cloud que vous voulez migrer. La commande renvoie un message d’erreur si les machines virtuelles du service cloud sont dans un réseau virtuel ou si elles ont des rôles web/de travail.

	Get-AzureService | ft Servicename

Obtenez le nom du déploiement du service cloud à l’aide des commandes suivantes :

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Préparez les machines virtuelles dans le service cloud pour la migration. Vous disposez de deux options.

* **Option 1. Migrer les machines virtuelles vers un réseau virtuel créé à partir d’une plateforme**

	La première étape consiste à valider si vous pouvez migrer le service cloud à l’aide des commandes suivantes :

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	La commande ci-dessus affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez passer à l’étape de la préparation :

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

* **Option 2. Migrer vers un réseau virtuel existant dans le modèle de déploiement Resource Manager**

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	La première étape consiste à valider si vous pouvez migrer le service cloud à l’aide de la commande suivante :

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	La commande ci-dessus affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez passer à l’étape de la préparation :

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

Une fois l’opération de préparation effectuée avec une des options précédentes, interrogez l’état de la migration des machines virtuelles. Assurez-vous qu’elles ont l’état `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Vérifiez la configuration pour les ressources préparées à l’aide de PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Migration de machines virtuelles dans un réseau virtuel

Pour migrer des machines virtuelles dans un service cloud, vous migrez le réseau. Les machines virtuelles migrent automatiquement avec le réseau. Sélectionnez le réseau virtuel dont vous souhaitez effectuer la migration.

	$vnetName = "<Virtual Network Name>"

>[AZURE.NOTE] Vous obtenez un message d’erreur pour la validation si le réseau virtuel contient des rôles web/de travail ou des machines virtuelles avec des configurations non prises en charge.

La première étape consiste à valider si vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

La commande ci-dessus affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez passer à l’étape de la préparation :
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Vérifiez la configuration pour les machines virtuelles préparées à l’aide d’Azure PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Migrer un compte de stockage

Une fois que vous avez terminé la migration des machines virtuelles, nous vous recommandons de migrer les comptes de stockage.

Préparez chaque compte de stockage pour la migration à l’aide de la commande suivante :

	$storageAccountName = "<storage account name>"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Vérifiez la configuration pour le compte de stockage préparé à l’aide d’Azure PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Étapes suivantes

- Pour plus d’informations sur la migration, consultez [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).
- Pour migrer des ressources réseau supplémentaires vers Resource Manager à l’aide d’Azure PowerShell, utilisez les mêmes étapes avec **Move-AzureNetworkSecurityGroup**, **Move-AzureReservedIP**, et **Move-AzureRouteTable**.
- Pour les scripts open source que vous pouvez utiliser pour migrer les ressources Azure d’un déploiement classique vers Resource Manager, consultez [Outils de la communauté pour la migration vers Azure Resource Manager](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0921_2016-->