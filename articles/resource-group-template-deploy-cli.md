<properties
   pageTitle="Déployer des ressources avec le modèle et l’interface de ligne de commande Azure | Microsoft Azure"
   description="Utilisez Azure Resource Manager et l’interface de ligne de commande Azure pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
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

# Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique explique comment utiliser l’interface de ligne de commande Azure avec les modèles Resource Manager pour déployer vos ressources dans Azure.

> [AZURE.TIP] Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
>
> - [Afficher les opérations de déploiement avec l’interface CLI Azure](resource-manager-troubleshoot-deployments-cli.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> - [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes

Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

## Étapes à suivre pour le déploiement

Cet article décrit toutes les différentes options disponibles lors du déploiement. Toutefois, souvent, deux commandes suffisent. Pour commencer le déploiement rapidement, utilisez les commandes suivantes :

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Pour en savoir plus sur les options de déploiement mieux adaptées à votre scénario, poursuivez la lecture de cet article.

[AZURE.INCLUDE [déploiements-resource-manager](../includes/resource-manager-deployments.md)]

## Déploiement avec l’interface de ligne de commande Azure

Si vous n’avez pas déjà utilisé Azure CLI avec Azure Resource Manager, consultez [Utilisation d’Azure CLI pour Mac, Linux et Windows avec Azure Resource Management](xplat-cli-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne le résultat de votre connexion.

        azure login
  
        ...
        info:    login command OK

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez vers le module Azure Resource Manager. Vous recevez la confirmation du nouveau mode.

        azure config mode arm
   
        info:     New mode is arm

4. Si vous n’avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Vous devez fournir un emplacement pour le groupe de ressources, car celui-ci stocke des métadonnées sur les ressources. Pour des raisons de conformité, vous souhaiterez peut-être indiquer où sont stockées métadonnées. En règle générale, nous vous recommandons de spécifier l’emplacement où réside la plupart de vos ressources. L’utilisation du même emplacement permet de simplifier votre modèle.

        azure group create -n ExampleResourceGroup -l "West US"

     Un résumé du nouveau groupe de ressources est retourné.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Validez votre déploiement avant son exécution en exécutant la commande **azure group template validate**. Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement (voir l'étape suivante).

        azure group template validate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. Pour déployer des ressources vers votre groupe de ressources, exécutez la commande suivante et indiquez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle et tous les autres paramètres nécessaires à votre scénario.
   
     Vous disposez des trois options suivantes pour fournir les valeurs des paramètres :

     1. Utiliser des paramètres incorporés et un modèle local. Chaque paramètre est au format suivant : `"ParameterName": { "value": "ParameterValue" }`. L’exemple suivant montre les paramètres avec des caractères d’échappement.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Utiliser des paramètres incorporés et un lien vers un modèle.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Utiliser un fichier de paramètres. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Une fois les ressources déployées à l’aide de l’une des trois méthodes ci-dessus, un résumé du déploiement apparaît.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Pour exécuter un déploiement complet, définissez **mode** sur **Complet**. Soyez prudent lorsque vous utilisez ce mode, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Si vous souhaitez consigner des informations supplémentaires sur le déploiement qui peuvent vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **debug-setting**. Vous pouvez demander à ce que le contenu de la demande et/ou de la réponse soit consigné avec l’opération de déploiement.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## Déployer un modèle à partir du stockage avec un jeton SAP

Vous pouvez ajouter vos modèles à un compte de stockage et les lier au cours du déploiement avec un jeton SAP.

> [AZURE.IMPORTANT] Une fois les étapes ci-dessous suivies, l’objet blob contenant le modèle n’est accessible qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAP pour l’objet blob, celui-ci est accessible à toute personne ayant cet URI. Si un autre utilisateur intercepte l’URI, il pourra accéder au modèle. L’utilisation d’un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.

### Ajouter un modèle privé au compte de stockage

Les étapes suivantes configurent un compte de stockage pour les modèles :

1. Créez un groupe de ressources

        azure group create -n "ManageGroup" -l "westus"

2. Créez un compte de stockage. Le nom du compte de stockage doit être unique dans Azure : fournissez donc votre propre nom pour le compte.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Définissez des variables pour le compte de stockage et la clé.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Créez un conteneur. L’autorisation est définie sur **Désactivée**, ce qui signifie que le conteneur n’est accessible qu’au propriétaire.

        azure storage container create --container templates -p Off 
        
4. Ajoutez votre modèle au conteneur.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### Fournir un jeton SAP au cours du déploiement

Pour déployer un modèle dans un compte de stockage privé, récupérez un jeton SAP et incluez-le dans l’URI du modèle.

1. Créez un jeton SAP avec des autorisations de lecture et une date d’expiration pour limiter l’accès. Définissez le délai d’expiration de façon à laisser suffisamment de temps pour terminer le déploiement. Récupérez l’URI complet du modèle, y compris le jeton SAP.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Déployez le modèle en fournissant l’URI qui inclut le jeton SAP.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Pour accéder à un exemple d’utilisation d’un jeton SAP avec des modèles liés, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [fichier-de-paramètre-resource-manager](../includes/resource-manager-parameter-file.md)]

## Étapes suivantes
- Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
- Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).
- Pour plus d’informations sur l’utilisation d’une référence Key Vault pour transmettre des valeurs sécurisées, consultez [Transmettre des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0921_2016-->