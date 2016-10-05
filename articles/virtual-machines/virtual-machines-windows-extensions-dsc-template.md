<properties
   pageTitle="Modèle Resource Manager de configuration d’état souhaité | Microsoft Azure"
   description="Définition du modèle Resource Manager pour la configuration d’état souhaité dans Azure avec des exemples et informations pour la résolution des problèmes"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# VMSS Windows et Configuration d’état souhaité avec des modèles Azure Resource Manager
Cet article décrit le modèle Resource Manager destiné au [gestionnaire de l’extension Configuration d’état souhaité](virtual-machines-windows-extensions-dsc-overview.md).

## Exemple de modèle pour une machine virtuelle sous Windows

L’extrait de code suivant va dans la section « Resource » du modèle.

```json
			"name": "Microsoft.Powershell.DSC",
			"type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
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

```

## Exemple de modèle pour un VMSS Windows

Un nœud VMSS comporte une section « properties » avec l’attribut « VirtualMachineProfile », « extensionProfile ». L’extension Configuration d’état souhaité (DSC) est ajoutée sous « extensions ».

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
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

## Informations détaillées sur les paramètres

Voici le schéma pour la section « settings » des paramètres de l’extension DSC Azure dans un modèle Azure Resource Manager.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## Détails
| Nom de la propriété | Type | Description |
| --- | --- | --- |
| settings.wmfVersion | string | Spécifie la version de Windows Management Framework qui doit être installée sur votre machine virtuelle. Lorsque cette propriété est définie sur « latest », la dernière version de WMF est installée. Les seules valeurs possibles actuellement pour cette propriété sont **4.0 », « 5.0 », « 5.0PP », and « latest »**. Les valeurs possibles font l’objet de mises à jour. La valeur par défaut est « latest ».|
| settings.configuration.url | string | Spécifie l’adresse URL de téléchargement de votre fichier .zip de configuration DSC. Si l’accès à l’URL fournie nécessite un jeton SAP, vous devez définir la propriété protectedSettings.configurationUrlSasToken sur la valeur du jeton SAP. Cette propriété est requise si la propriété settings.configuration.script ou settings.configuration.function est définie. |
| settings.configuration.script | string | Spécifie le nom de fichier du script qui contient la définition de votre configuration DSC. Ce script doit se trouver dans le dossier racine du fichier .zip téléchargé depuis l’URL spécifiée par la propriété configuration.url. Cette propriété est requise si la propriété settings.configuration.url ou settings.configuration.script est définie. |
| settings.configuration.function | string | Spécifie le nom de votre configuration DSC. La configuration nommée doit se trouver dans le script défini par configuration.script. Cette propriété est requise si la propriété settings.configuration.url ou settings.configuration.function est définie. |
| settings.configurationArguments | Collection | Définit les paramètres à transmettre à votre configuration DSC. Cette propriété n’est pas chiffrée. |
| settings.configurationData.url | string | Spécifie l’URL de téléchargement de votre fichier de données de configuration (.pds1) à utiliser comme entrée pour votre configuration DSC. Si l’accès à l’URL fournie nécessite un jeton SAP, vous devez définir la propriété protectedSettings.configurationDataUrlSasToken sur la valeur du jeton SAP.|
| settings.privacy.dataEnabled | string | Active ou désactive la collecte télémétrique. Les seules valeurs possibles pour cette propriété sont **« Enable », « Disable », « » ou « $null »**. Le fait de laisser cette propriété vide ou de la définir sur $null active la télémétrie. La valeur par défaut est « ». [Plus d’informations](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Collection | Définit d’autres emplacements de téléchargement de WMF. [Plus d’informations](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Collection | Définit les paramètres à transmettre à votre configuration DSC. Cette propriété est chiffrée. |
| protectedSettings.configurationUrlSasToken | string | Spécifie le jeton SAP permettant d’accéder à l’URL définie par configuration.url. Cette propriété est chiffrée. |
| protectedSettings.configurationDataUrlSasToken | string | Spécifie le jeton SAP permettant d’accéder à l’URL définie par configurationData.url. Cette propriété est chiffrée. |

## settings vs protectedSettings
Tous les paramètres sont enregistrés dans un fichier texte de paramètres sur la machine virtuelle. Les propriétés définies sous « settings » sont des propriétés publiques, car elles ne sont pas chiffrées dans le fichier texte de paramètres. Les propriétés définies sous « protectedSettings » sont chiffrées avec un certificat et ne sont pas affichées en texte brut dans ce fichier sur la machine virtuelle.

Si des informations d’identification sont requises pour la configuration, elles peuvent être incluses dans protectedSettings :

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
       	    "userName": "UsernameValue1",
       	    "password": "PasswordValue1"
        }
    }
}
```

## Exemple

L’exemple suivant est tiré de la section « Prise en main » de l’article [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](virtual-machines-windows-extensions-dsc-overview.md). Cet exemple utilise des modèles Resource Manager au lieu d’applets de commande pour déployer l’extension. Enregistrez la configuration « IisInstall.ps1 », placez-la dans un fichier .zip et chargez le fichier dans une URL accessible. Cet exemple utilise le stockage Blob Azure, mais il est possible de télécharger les fichiers .zip depuis n’importe quel emplacement arbitraire.

Dans le modèle Azure Resource Manager, le code suivant demande à la VM de télécharger le fichier correct et d’exécuter la fonction PowerShell appropriée :

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## Mise à jour depuis le format précédent
Tous les paramètres au format précédent (qui contient les propriétés publiques ModulesUrl, ConfigurationFunction, SasToken ou Properties) s’adaptent automatiquement au format actuel et s’exécutent exactement comme avant.

Le schéma suivant est ce à quoi les paramètres précédents ressemblaient :

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Voici comment le format précédent s’adapte au format actuel :

| Nom de la propriété | Équivalent dans le schéma précédent |
| --- | --- |
| settings.wmfVersion | settings.WMFVersion |
| settings.configuration.url | settings.ModulesUrl |
| settings.configuration.script | Première partie de settings.ConfigurationFunction (avant « \\\ ») |
| settings.configuration.function | Deuxième partie de settings.ConfigurationFunction (après « \\\ ») |
| settings.configurationArguments | settings.Properties |
| settings.configurationData.url | protectedSettings.DataBlobUri (sans jeton SAP) |
| settings.privacy.dataEnabled | settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | settings.SasToken |
| protectedSettings.configurationDataUrlSasToken | Jeton SAP de protectedSettings.DataBlobUri |


## Dépannage : code d’erreur 1100
Le code d’erreur 1100 indique qu’il existe un problème avec l’entrée utilisateur pour l’extension DSC. Le texte de ces erreurs est variable et peut changer. Voici certaines des erreurs que vous pouvez rencontrer et comment vous pouvez les résoudre.

### Valeurs non valides
« Privacy.dataCollection is '{0}'. The only possible values are '', 'Enable', and 'Disable' » (Privacy.dataCollection est « {0} ». Les seules valeurs possibles sont « », « Enable » et « Disable ».) « WmfVersion is '{0}'. Only possible values are … and 'latest' » (WmfVersion est « {0} ». Les seules valeurs possibles sont … et « latest ».)

Problème : Une valeur fournie n’est pas autorisée.

Solution : Remplacer la valeur non valide par une valeur valide. Consultez le tableau dans la section Détails.

### URL non valide
« ConfigurationData.url is '{0}'. This is not a valid URL » (configurationData.url est « {0} ». Il ne s’agit pas d’une URL valide.) « DataBlobUri is '{0}'. This is not a valid URL » (DataBlobUri est « {0} ». Il ne s’agit pas d’une URL valide.) « Configuration.url is '{0}'. This is not a valid URL » (configuration.url est « {0} ». Il ne s’agit pas d’une URL valide.)

Problème : Une URL fournie n’est pas valide.

Solution : Vérifiez toutes les URL que vous avez fournies. Assurez-vous que toutes les URL se résolvent en emplacements valides auxquels l’extension peut accéder sur l’ordinateur distant.

### Type configurationArguments non valide
« Invalid configurationArguments type {0} » (Type configurationArguments {0} non valide)

Problème : La propriété configurationArguments ne peut pas se résoudre en objet de table de hachage.

Solution : Faites de votre propriété configurationArguments une table de hachage. Suivez le format fourni dans l’exemple précédent. Prenez garde aux guillemets, aux virgules et aux accolades.

### Propriétés configurationArguments en double
« Found duplicate arguments '{0}' in both public and protected configurationArguments » (Arguments « {0} » en double trouvés dans les paramètres configurationArguments publics et protégés)

Problème : Les arguments configurationArguments dans les paramètres publics et protégés contiennent des propriétés portant le même nom.

Solution : Supprimez l’une des propriétés en double.

### Propriétés manquantes
« Configuration.function requires that configuration.url or configuration.module is specified » (configuration.url ou configuration.module doit être spécifié pour configuration.function.)

« Configuration.url requires that configuration.script is specified » (configuration.script doit être spécifié pour configuration.url)

« Configuration.script requires that configuration.url is specified » (configuration.url doit être spécifié pour configuration.script)

« Configuration.url requires that configuration.function is specified » (configuration.function doit être spécifié pour configuration.url)

« Configuration.script requires that configuration.url is specified » (configuration.url doit être spécifié pour configurationUrlSasToken)

« ConfigurationDataUrlSasToken requires that configurationData.url is specified » (configurationData.url doit être spécifié pour configurationDataUrlSasToken)

Problème : Une propriété définie a besoin d’une autre propriété qui est manquante.

Solutions :
- Fournissez la propriété manquante.
- Supprimez la propriété qui a besoin de la propriété manquante.


## Étapes suivantes
[En savoir plus sur DSC et VMSS](virtual-machines-scale-sets-dsc.md)

En savoir plus sur la [Gestion des informations d’identification sécurisées de DSC](virtual-machines-windows-extensions-dsc-credentials.md).

Pour plus d’informations sur le gestionnaire d’extensions DSC Azure, voir [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](virtual-machines-windows-extensions-dsc-overview.md).

Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

<!---HONumber=AcomDC_0921_2016-->