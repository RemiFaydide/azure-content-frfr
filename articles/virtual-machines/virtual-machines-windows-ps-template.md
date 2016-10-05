<properties
	pageTitle="Créer une machine virtuelle avec un modèle Resource Manager | Microsoft Azure"
	description="Utilisez un modèle Resource Manager et PowerShell pour créer facilement une machine virtuelle Windows."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources

Cet article vous présente un modèle Azure Resource Manager et vous montre comment utiliser PowerShell pour le déployer. Le modèle déploie une machine virtuelle unique exécutant Windows Server dans un nouveau réseau virtuel avec un seul sous-réseau.

Il vous faudra environ 20 minutes pour effectuer les étapes décrites dans cet article.

> [AZURE.IMPORTANT] Si vous souhaitez que votre machine virtuelle fasse partie d’un groupe à haute disponibilité, vous devez l’y ajouter lors de sa création. Il n’existe actuellement aucun moyen d’ajouter une machine virtuelle dans un groupe à haute disponibilité, une fois celle-ci créée.

## Étape 1 : création du fichier de modèle

Vous pouvez créer votre propre modèle à l’aide des informations figurant dans [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md). Vous pouvez également déployer des modèles qui ont été créés pour vous à partir des [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).

1. Ouvrez votre éditeur de texte et copiez ces informations JSON dans un nouveau fichier nommé *VirtualMachineTemplate.json* :

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [
                    "[variables('addressPrefix')]"
                  ]
                },
                "subnets": [
                  {
                    "name": "[variables('subnetName')]",
                    "properties": {
                      "addressPrefix": "[variables('subnetPrefix')]"
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [
                  {
                    "name": "ipconfig1",
                    "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                      },
                      "subnet": {
                        "id": "[variables('subnetRef')]"
                      }
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computername": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku" : "[variables('windowsOSVersion')]",
                    "version":"latest"
                  },
                  "osDisk" : {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces": [
                    {
                      "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                    }
                  ]
                }
              }
            }
          ]
        }
        
    >[AZURE.NOTE] Cet article crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md).
    
2. Enregistrez le fichier de modèle.

## Étape 2 : création du fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs et envoyez-le au Gestionnaire de ressources avec le modèle.

1. Dans l'éditeur de texte, copiez ces informations JSON dans un nouveau fichier appelé *Parameters.json* :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] En savoir plus sur les [conditions requises pour les noms d’utilisateur et les mots de passe](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Enregistrez le fichier de paramètres.

## Étape 3 : installation d’Azure PowerShell

Pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Sélectionnez l’abonnement à utiliser et connectez-vous à votre compte Azure.

## Étape 4 : création d’un groupe de ressources

Toutes les ressources doivent être déployées dans un groupe de ressources. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md).

1. Obtenez la liste des emplacements disponibles où créer des ressources.

	    Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Remplacez la valeur de **$locName** par un emplacement de la liste, par exemple **États-Unis du Centre**. Créez la variable.

        $locName = "location name"
        
3. Remplacez la valeur de **$rgName** par le nom du nouveau groupe de ressources. Créez la variable et le groupe de ressources.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Le résultat suivant devrait s'afficher :
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

### Étape 5 : création des ressources avec le modèle et les paramètres

1. Remplacez la valeur de **$deployName** par le nom du déploiement. Remplacez la valeur de **$templatePath** par le chemin d’accès et le nom du fichier de modèle. Remplacez la valeur de **$parameterFile** par le chemin d’accès et le nom du fichier de paramètres. Créez les variables.

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

2. Déployez le modèle.

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    Le résultat suivant doit s’afficher :

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] Vous pouvez également déployer des modèles et des paramètres à partir d'un compte de stockage Azure. Pour en savoir plus, consultez la rubrique [Utilisation d’Azure PowerShell avec Azure Storage](../storage-powershell-guide-full.md).

## Étapes suivantes

- Si vous rencontrez des problèmes de déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Pour apprendre à gérer la machine virtuelle que vous venez de créer, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0921_2016-->