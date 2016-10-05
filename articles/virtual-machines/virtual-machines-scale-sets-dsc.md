<properties
   pageTitle="Utilisation de la configuration d’état souhaité avec des groupes identiques de machines virtuelles | Microsoft Azure"
   description="Utilisation des groupes identiques de machines virtuelles avec l’extension DSC Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# Utilisation des groupes identiques de machines virtuelles avec l’extension DSC Azure

Les [groupes identiques de machines virtuelles (VMSS)](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) peuvent être utilisés avec le gestionnaire d’extensions [Configuration d’état souhaité (DSC) Azure](virtual-machines-windows-extensions-dsc-overview.md). VMSS fournit un moyen de déployer et gérer un grand nombre de machines virtuelles et vous pouvez réduire ou augmenter les instances avec flexibilité en réponse à la charge. La DSC est utilisée pour configurer les machines virtuelles à mesure de leur mise en ligne afin qu’elles exécutent le logiciel en production.

## Différences entre le déploiement sur des machines virtuelles et sur un VMSS

La structure sous-jacente du modèle VMSS est légèrement différente de celle d’une machine virtuelle unique. Plus précisément, une machine virtuelle unique déploie les extensions sous le nœud « virtualMachines ». Il existe une entrée de type « extensions » où la configuration DSC est ajoutée au modèle

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Un nœud VMSS comporte une section « properties » avec l’attribut « VirtualMachineProfile », « extensionProfile ». La configuration DSC est ajoutée sous « extensions »

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.9",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## Comportement de VMSS

Le comportement de VMSS est identique à celui d’une machine virtuelle unique. Lorsqu’une machine virtuelle est créée, elle est automatiquement configurée avec l’extension DSC. Si une version plus récente du WMF est requise par l’extension, la machine virtuelle redémarre avant la mise en ligne. Une fois en ligne, elle télécharge le fichier .zip de la configuration DSC et l’approvisionne sur la machine virtuelle. Vous trouverez plus de détails dans [la présentation de l’extension DSC d’Azure](virtual-machines-windows-extensions-dsc-overview.md).

## Étapes suivantes ##
Examinez le [modèle Azure Resource Manager pour l’extension DSC](virtual-machines-windows-extensions-dsc-template.md).

Découvrez comment [l’extension DSC gère en toute sécurité les informations d’identification](virtual-machines-windows-extensions-dsc-credentials.md).

Pour plus d’informations sur le gestionnaire d’extensions DSC Azure, voir [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](virtual-machines-windows-extensions-dsc-overview.md).

Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

<!---HONumber=AcomDC_0921_2016-->