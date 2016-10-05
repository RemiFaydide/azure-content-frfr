<properties
	pageTitle="Déployer un espace de travail Machine Learning à l’aide du modèle Azure Resource Manager | Microsoft Azure"
	description="Comment déployer un espace de travail pour Azure Machine Learning à l’aide du modèle Azure Resource Manager"
	services="machine-learning"
	documentationCenter=""
	authors="ahgyger"
	manager="haining"
	editor="garye"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="ahgyger"/>
# Déployer un espace de travail Machine Learning à l’aide d’Azure Resource Manager

## Introduction
Les modèles de déploiement Azure Resource Manager vous font gagner du temps en vous offrant une méthode évolutive pour déployer des composants interconnectés avec un mécanisme de validation et de nouvelle tentative. Pour configurer des espaces de travail Azure Machine Learning, par exemple, vous devez d’abord configurer un compte de stockage Azure et ensuite déployer votre espace de travail. Imaginez effectuer cette opération manuellement pour des centaines d’espaces de travail. Une alternative plus simple consiste à utiliser un modèle Azure Resource Manager pour déployer un espace de travail Azure Machine Learning et toutes ses dépendances. Cet article vous accompagne tout au long de cette procédure pas à pas. Pour une intéressante présentation d’Azure Resource Manager, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md).

## Pas à pas : créer un espace de travail Machine Learning
Nous créerons un groupe de ressources Azure, puis déploierons un nouveau compte de stockage Azure et un nouvel espace de travail Azure Machine Learning à l’aide d’un modèle Resource Manager. Une fois le déploiement terminé, nous imprimerons des informations importantes sur les espaces de travail créés (clé primaire, workspaceID et URL de l’espace de travail).

### Créer un modèle Azure Resource Manager
Un espace de travail Machine Learning requiert un compte de stockage Azure pour stocker le jeu de données lié. Le modèle suivant utilise le nom du groupe de ressources pour générer le nom du compte de stockage et celui de l’espace de travail. Il utilise également le nom du compte de stockage comme propriété lors de la création de l’espace de travail.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Enregistrez ce modèle en tant que fichier mlworkspace.json sous C:\\temp.

### Déployer le groupe de ressources à partir du modèle
* Ouvrez PowerShell
* Installez les modules d’Azure Resource Manager et d’Azure Service Management

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Ces étapes de téléchargement et d’installation des modules sont nécessaires pour effectuer les étapes restantes. Cette opération ne doit être effectuée qu’une fois dans l’environnement dans lequel vous exécutez les commandes PowerShell.

* Authentifiez-vous sur Azure

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Cette étape doit être répétée pour chaque session. Une fois que vous êtes authentifié, vos informations d’abonnement s’affichent.

![Compte Azure.][1]

Maintenant que nous avons accès à Azure, nous pouvons créer le groupe de ressources.

* Créer un groupe de ressources

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Vérifiez que le groupe de ressources est correctement configuré. **ProvisioningState** doit être « Réussi ». Le nom du groupe de ressources est utilisé par le modèle pour générer le nom du compte de stockage. Le nom du compte de stockage doit comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres.

![Groupe de ressources][2]

* À l’aide du déploiement du groupe de ressources, déployez un nouvel espace de travail Machine Learning.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Une fois le déploiement terminé, il est simple d’accéder aux propriétés de l’espace de travail que vous avez déployé. Par exemple, vous pouvez accéder au jeton de clé primaire.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Une autre méthode pour récupérer des jetons de l’espace de travail existant consiste à utiliser la commande Invoke-AzureRmResourceAction. Par exemple, vous pouvez répertorier les jetons principaux et secondaires de tous les espaces de travail.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Après la configuration de l’espace de travail, vous pouvez également automatiser de nombreuses tâches Azure Machine Learning Studio à l’aide du [Module PowerShell pour Azure Machine Learning](http://aka.ms/amlps).

## Étapes suivantes 
* Pour en savoir plus, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).
* Parcourez le [Référentiel de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).
* Regardez cette vidéo sur [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->

<!---HONumber=AcomDC_0921_2016-->