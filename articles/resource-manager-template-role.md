<properties
   pageTitle="Modèle Resource Manager pour les attributions de rôles | Microsoft Azure"
   description="Affiche le schéma Resource Manager pour le déploiement d'une attribution de rôle par le biais d'un modèle."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Attributions de rôles, schéma de modèle

Attribue un utilisateur, un groupe ou un principal du service à un rôle avec une portée spécifique.

## Format de schéma

Pour créer une attribution de rôle, ajoutez le schéma suivant à la section des ressources de votre modèle.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2014-10-01-preview",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- |
| type | Enum<br />Requis<br />**Microsoft.Authorization/roleAssignments**<br /><br />Type de ressource à créer. |
| apiVersion | Enum<br />Requis<br />**2014-10-01-preview**<br /><br />La version de l'API à utiliser pour la création de la ressource. |  
| name | String<br />Requis<br />**Globally-unique identifier**<br /><br />Un identificateur pour la nouvelle attribution de rôle. |
| dependsOn | Array<br />Facultatif<br />Une liste séparée par des virgules de noms de ressource ou d'identificateurs de ressource uniques.<br /><br />La collection de ressources dont dépend cette affectation de rôle. Si vous affectez un rôle qui s'étend sur une ressource et que la ressource est déployée dans le même modèle, incluez ce nom de ressource dans cet élément pour garantir que la ressource est tout d'abord déployée. | 
| properties | Object<br />Requis<br />[properties object](#properties)<br /><br />Un objet qui identifie la définition de rôle, le principal et l'étendue. |  

<a id="properties" />
### objet propriétés

| Nom | Valeur |
| ------- | ---- |
| roleDefinitionId | String<br />Requis<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}**<br /><br />L'identificateur d'une définition de rôle existante à utiliser dans l'attribution de rôle. |
| principalId | String<br />Requis<br />**Globally-unique identifier**<br /><br />L'identificateur d'un principal existant. Cette opération mappe l'ID au sein du répertoire et peut pointer vers un utilisateur, un principal du service ou un groupe de sécurité. |
| scope | String<br />Requis<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}** (for resource groups) ou<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** (pour les ressources)<br /><br />L'étendue avec laquelle l'attribution de rôle est appliquée. |


## Utilisation de la ressource d'attribution de rôle

Vous ajoutez une attribution de rôle à votre modèle lorsque vous devez ajouter un utilisateur, un groupe ou un principal du service à un rôle au cours du déploiement. Les attributions de rôles sont héritées des niveaux supérieurs de la portée. Si vous avez déjà ajouté un principal à un rôle au niveau d'abonnement, vous n'avez donc pas besoin de le réaffecter au groupe de ressources ou à la ressource.

Vous pouvez générer un nouvel identificateur pour **name** avec :

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Vous pouvez récupérer l'identificateur global unique pour la définition de rôle avec :

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Vous pouvez récupérer l'identificateur du principal avec l'une des commandes suivantes.

Pour un groupe nommé **Auditors** :

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Pour un utilisateur nommé **exampleperson** :

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Pour un principal du service nommé **exampleapp** :

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Exemples

L'exemple suivant affecte un groupe à un rôle pour le groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }

## Modèles de démarrage rapide

Les modèles suivants montrent comment utiliser la ressource d'attribution de rôle :

- [Attribuer un rôle intégré au groupe de ressources](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Attribuer un rôle intégré à une machine virtuelle existante](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Attribuer un rôle intégré à plusieurs machines virtuelles existantes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## Étapes suivantes

- Pour plus d'informations sur la structure du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour plus d'informations sur le contrôle d'accès en fonction du rôle, consultez [Contrôle d'accès en fonction du rôle Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0406_2016-->