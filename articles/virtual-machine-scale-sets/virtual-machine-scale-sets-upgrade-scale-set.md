<properties
	pageTitle="Déployer une application sur des groupes identiques de machines virtuelles | Microsoft Azure"
	description="Déployer une application sur des groupes identiques de machines virtuelles"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>


# Mettre à jour un jeu de mise à l’échelle de machine virtuelle

Cet article décrit comment vous pouvez déployer une mise à jour du système d’exploitation sur un jeu de mise à l’échelle de machine virtuelle Azure sans interruption de service. Dans ce contexte, une mise à jour du système d’exploitation est la modification de la version/référence SKI du système d’exploitation, ou la modification de l’URI d’une image personnalisée. Mettre à jour sans interruption de service signifie la mise à jour des machines virtuelles une à la fois ou dans des groupes (par exemple, un domaine d’erreur à la fois), plutôt que toutes simultanément. En procédant ainsi, les machines virtuelles qui ne sont pas mises à niveau peuvent continuer leur exécution.

Pour éviter toute ambiguïté, nous allons distinguer trois types de mise à jour du système d’exploitation que vous pourriez vouloir faire :

1. Modification de la version ou de la référence SKU d’une image de plateforme. Par exemple, la modification d’Ubuntu version 14.04.2-LTS de 14.04.201506100 à 14.04.201507060, ou la modification d’Ubuntu 15.10/dernier SKU vers Ubuntu 16.04.0-LTS/dernière version. Ce scénario est décrit dans cet article.

2. Vous créez une nouvelle version d’une image personnalisée et souhaitez modifier l’URI qui pointe vers l’image (properties->virtualMachineProfile->storageProfile->osDisk->image->uri). Ce scénario est décrit dans cet article.

3. L’application de correctifs sur le système d’exploitation à partir d’une machine virtuelle (par exemple : l’installation d’un correctif de sécurité, avec Windows Update, etc..). Ce scénario est pris en charge mais n’est pas traité dans cet article.

Les 2 premiers sont des exigences prises en charge. Pour le troisième, au moins pour le moment, vous devez créer un nouveau jeu de mise à l’échelle pour le faire. Cet article présente les options 1 et 2. Remarque : Les jeux de mise à l’échelle de machine virtuelle qui sont déployés dans le cadre d’un cluster [Service Fabric](https://azure.microsoft.com/services/service-fabric/) ne sont pas abordés ici.

La séquence de base pour la modification de la version du système d’exploitation/du SKU d’une image de plateforme ou de l’URI d’une image personnalisée se présente comme suit :

* Obtenez le modèle VMSS.

* Modifiez la version, la référence SKU ou la valeur de l’URI dans le modèle.

* Mettez le modèle à niveau.

* Effectuez un appel manualUpgrade sur les machines virtuelles dans le jeu de mise à l’échelle. Cette étape est uniquement pertinente si la propriété upgradePolicy de votre jeu de mise à l’échelle est définie sur « Manuel ». Si elle est définie sur « Automatique », toutes les machines virtuelles sont mises à niveau en même temps, ce qui entraîne une interruption de service.


Avec ces informations générales à l’esprit, voyons comment vous pouvez mettre à jour la version d’un jeu de mise à l’échelle dans PowerShell et en utilisant l’API REST. Ces exemples couvrent le cas d’une image de plateforme, mais nous espérons vous fournir suffisamment d’informations pour vous permettre d’adapter ce processus à une image personnalisée.

## PowerShell

Cet exemple met à jour un jeu de mise à l’échelle de machine virtuelle Windows vers une nouvelle version « 4.0.20160229 ». Après la mise à jour du modèle, il effectue une mise à jour, une instance de machine virtuelle à la fois.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si vous mettiez à jour l’URI pour une image personnalisée au lieu de modifier une version d’image de plateforme, vous remplaceriez la ligne « définir la nouvelle version » par ce qui suit :

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## En utilisant REST API

Voici quelques exemples Python qui utilisent l’API REST Azure pour déployer une mise à jour de la version du système d’exploitation. Les deux utilisent la bibliothèque légère [azurerm](https://pypi.python.org/pypi/azurerm) de fonctions wrapper de l’API REST Azure pour effectuer une opération GET sur le modèle de jeu de mise à l’échelle, puis une commande PUT avec un modèle mis à jour. Les vues d’instances de machine virtuelle seront également analysées pour identifier les machines virtuelles par domaine de mise à jour.

### vmssupgrade

vmssupgrade est le script Python pour déployer une mise à niveau de système d’exploitation sur un jeu de mise à l’échelle de machine virtuelle en cours d’exécution, un domaine de mise à jour à la fois. Vous le trouverez [ici](https://github.com/gbowerman/vmsstools).

![Capture d’écran de vmssupgrade](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Ce script vous permet de choisir des machines virtuelles à mettre à jour, ou de spécifier un domaine de mise à jour, et prend en charge la modification d’une version d’image de plateforme OU la modification de l’URI d’une image personnalisée.

### vmsseditor

Cet outil est un éditeur à usage général pour les jeux de mise à l’échelle de machine virtuelle qui affiche l’état de la machine virtuelle sous forme de carte de chaleur (heatmap) où une ligne représente un domaine de mise à jour. Entre autres choses, vous pouvez mettre à jour le modèle pour un VMSS avec une nouvelle version, une référence SKU ou une URI d’image personnalisée, puis choisir des domaines d’erreur à mettre à niveau. Lorsque vous le faites, toutes les machines virtuelles de ce domaine de mise à jour sont mises à niveau vers le nouveau modèle. Vous pouvez également faire une mise à niveau propagée, en fonction de la taille de lot de votre choix. Vous trouverez vmsseditor dans le [référentiel github](https://github.com/gbowerman/vmssdashboard) suivant

Par exemple, ici, nous mettons à jour le modèle d’un jeu de mise à l’échelle vers Ubuntu 14.04-2LTS version 14.04.201507060. Notez que cette capture d’écran est ancienne ; de nombreuses options ont depuis été ajoutées à cet outil.

![Capture d’écran de vmsseditor 1](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Après avoir cliqué sur Mettre à niveau, puis à nouveau sur Obtenir les détails, les machines virtuelles dans UD 0 commencent à se mettre à jour.

![Capture d’écran de vmsseditor 2](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

<!---HONumber=AcomDC_0921_2016-->