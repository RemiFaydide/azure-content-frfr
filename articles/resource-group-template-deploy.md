<properties
   pageTitle="Déployer des ressources avec le modèle et PowerShell | Microsoft Azure"
   description="Utilisez Azure Resource Manager et Azure PowerShell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique explique comment utiliser Azure PowerShell avec les modèles Resource Manager pour déployer vos ressources dans Azure.

> [AZURE.TIP] Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
>
> - [Afficher les opérations de déploiement Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> - [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes

Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

## Étapes à suivre pour le déploiement

Cet article décrit toutes les différentes options disponibles lors du déploiement. Toutefois, souvent, deux commandes suffisent. Pour commencer le déploiement rapidement, utilisez les commandes suivantes :

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Pour en savoir plus sur les options de déploiement mieux adaptées à votre scénario, poursuivez la lecture de cet article.

[AZURE.INCLUDE [déploiements-resource-manager](../includes/resource-manager-deployments.md)]

## Déployer avec PowerShell

1. Connectez-vous à votre compte Azure.

        Add-AzureRmAccount

     Un résumé de votre compte est renvoyé.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement avec la commande **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. En règle générale, lors du déploiement d’un nouveau modèle, vous souhaitez créer un groupe de ressources contenant les ressources. Si vous disposez déjà d’un groupe de ressources sur lequel vous souhaitez effectuer le déploiement, vous pouvez ignorer cette étape et utiliser ce groupe de ressources.

     Afin de créer un groupe de ressources, fournissez un nom et un emplacement pour votre groupe de ressources. Vous devez fournir un emplacement pour le groupe de ressources, car celui-ci stocke des métadonnées sur les ressources. Pour des raisons de conformité, vous souhaiterez peut-être indiquer où sont stockées métadonnées. En règle générale, nous vous recommandons de spécifier l’emplacement où réside la plupart de vos ressources. L’utilisation du même emplacement permet de simplifier votre modèle.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Un résumé du nouveau groupe de ressources est retourné.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Avant d’exécuter votre déploiement, vous pouvez valider vos paramètres de déploiement. L’applet de commande **Test-AzureRmResourceGroupDeployment** vous permet de rechercher les problèmes avant de créer des ressources réelles. L’exemple suivant indique comment valider un déploiement.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Afin de déployer des ressources pour votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et fournissez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle que vous avez créé et tous les autres paramètres nécessaires à votre scénario. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut **Incremental** est utilisée. Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete**. Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.

     Pour déployer un modèle local, utilisez le paramètre **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Pour déployer un modèle externe, utilisez le paramètre **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Vous disposez des options suivantes pour fournir les valeurs des paramètres :
   
     1. Utiliser des paramètres incorporés.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Utiliser un objet de paramètres.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Utiliser un fichier de paramètres local. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Utiliser un fichier de paramètres externe. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas passer d’autres valeurs inline ou tirées d’un fichier local. Pour plus d’informations, consultez [Priorité des paramètres](#parameter-precendence).

     Une fois les ressources déployées, un résumé du déploiement apparaît.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Si votre modèle inclut un paramètre utilisant le même nom que l’un des paramètres dans la commande PowerShell, vous êtes invité à fournir une valeur pour ce paramètre. Le paramètre depuis votre modèle inclut le suffixe **FromTemplate**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans votre applet de commande [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx). Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

6. Si vous souhaitez consigner des informations supplémentaires sur le déploiement qui peuvent vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **DeploymentDebugLogLevel**. Vous pouvez demander à ce que le contenu de la demande et/ou de la réponse soit consigné avec l’opération de déploiement.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Pour plus d’informations sur l’utilisation du contenu relatif au débogage afin de résoudre les problèmes liés aux déploiements, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## Déployer un modèle à partir du stockage avec un jeton SAP

Vous pouvez ajouter vos modèles à un compte de stockage et les lier au cours du déploiement avec un jeton SAP.

> [AZURE.IMPORTANT] Une fois les étapes ci-dessous suivies, l’objet blob contenant le modèle n’est accessible qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAP pour l’objet blob, celui-ci est accessible à toute personne ayant cet URI. Si un autre utilisateur intercepte l’URI, il pourra accéder au modèle. L’utilisation d’un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.

### Ajouter un modèle privé au compte de stockage

Les étapes suivantes configurent un compte de stockage pour les modèles :

1. Créez un groupe de ressources

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Créez un compte de stockage. Le nom du compte de stockage doit être unique dans Azure : fournissez donc votre propre nom pour le compte.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Définissez le compte de stockage actuel.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Créez un conteneur. L’autorisation est définie sur **Désactivée**, ce qui signifie que le conteneur n’est accessible qu’au propriétaire.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Ajoutez votre modèle au conteneur.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### Fournir un jeton SAP au cours du déploiement

Pour déployer un modèle dans un compte de stockage privé, récupérez un jeton SAP et incluez-le dans l’URI du modèle.

1. Si vous avez modifié le compte de stockage actuel, définissez comme compte de stockage actuel celui qui contient vos modèles.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Créez un jeton SAP avec des autorisations de lecture et une date d’expiration pour limiter l’accès. Récupérez l’URI complet du modèle, y compris le jeton SAP.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Déployez le modèle en fournissant l’URI qui inclut le jeton SAP.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Pour accéder à un exemple d’utilisation d’un jeton SAP avec des modèles liés, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [fichier-de-paramètre-resource-manager](../includes/resource-manager-parameter-file.md)]

## Priorité des paramètres

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Toutefois, vous ne pouvez pas utiliser les paramètres inline avec un fichier de paramètres externe. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri**, tous les paramètres inline sont ignorés. Vous devez fournir toutes les valeurs de paramètre dans le fichier externe. Si votre modèle inclut une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajoutez cette valeur dans un coffre de clés et référencez ce dernier dans votre fichier de paramètres externe, ou fournissez de manière dynamique toutes des valeurs de paramètre inline.

Pour plus d’informations sur l’utilisation d’une référence KeyVault afin de transmettre des valeurs sécurisées, consultez [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md).

## Étapes suivantes
- Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
- Pour obtenir des instructions sur le déploiement de votre solution dans des environnements différents, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).

<!---HONumber=AcomDC_0921_2016-->