<properties
   pageTitle="API REST Resource Manager | Microsoft Azure"
   description="Une vue d’ensemble des exemples d’authentification et de cas d’utilisation des API REST Resource Manager"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# API REST Resource Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
- [Portail](./azure-portal/resource-group-portal.md)
- [API REST](resource-manager-rest-api.md)

Derrière chaque appel au Gestionnaire de ressources Azure, derrière chaque modèle déployé, derrière chaque compte de stockage configuré se trouvent un ou plusieurs appels à une API RESTful de l’Azure Resource Manager. Cette rubrique est consacrée à ces API et à la manière dont vous pouvez les appeler sans utiliser aucun Kit de développement logiciel (SDK). Cela peut être très utile si vous souhaitez un contrôle total de toutes les requêtes à Azure ou si le Kit de développement logiciel (SDK) pour votre langue par défaut n’est pas disponible ou ne prend pas en charge les opérations à effectuer.

Cet article ne traitera pas chaque API exposée dans Azure, mais en utilise certaines comme exemple pour vous montrer comment continuer et vous y connecter. Si vous comprenez les notions de base, vous pouvez continuer et lire la page [Référence des API REST Gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour trouver des informations détaillées sur la manière d’utiliser les autres API.

## Authentification
L’authentification pour ARM est gérée par Azure Active Directory (AD). Pour vous connecter à une API quelconque, vous devez tout d’abord vous authentifier auprès d’Azure AD pour recevoir un jeton d’authentification que vous pouvez transmettre à chaque requête. Comme nous décrivons un appel direct pur des API REST, nous supposerons également que vous ne souhaitez pas vous authentifier avec un mot de passe/nom d’utilisateur normal lorsqu’une fenêtre contextuelle s’ouvre pour vous demander un nom d’utilisateur et un mot de passe ou peut-être même avec d’autres mécanismes d’authentification utilisés dans des scénarios d’authentification à deux facteurs. Pour cette raison, nous allons créer ce que l’on appelle une application Azure AD et un principal du service qui seront utilisés pour la connexion. Mais n’oubliez pas qu’Azure AD prend en charge plusieurs procédures d’authentification et qu’elles peuvent toutes être utilisées pour récupérer le jeton d’authentification dont nous avons besoin pour les requêtes API ultérieures. Référez-vous à [Création de l’application Active Directory et du principal du service](./resource-group-create-service-principal-portal.md) pour obtenir des instructions étape par étape.

### Génération d’un jeton d’accès 
L’authentification auprès d’Azure AD est effectuée en appelant Azure AD à l’adresse login.microsoftonline.com. Vous devez disposer des informations suivantes pour vous authentifier :

* l’ID de client Azure AD (le nom de l’Azure AD que vous utilisez pour vous connecter, souvent celui de votre entreprise, mais pas nécessairement) ;
* l’ID de l’application (récupéré au cours de l’étape de création de l’application Azure AD) ;
* le mot de passe (que vous avez choisi lors de la création de l’application Azure AD).

Dans la requête HTTP ci-dessous, veillez à remplacer l’« Azure AD Tenant ID (ID de client Azure AD) », l’« Application ID (ID de l’application) » et le « Password (Mot de passe) » par des valeurs correctes.

**Requête HTTP générique :**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... générera (une fois l’authentification réussie) une réponse similaire à celle-ci :

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(L’access\_token dans la réponse ci-dessus a été raccourci pour une meilleure lisibilité)

**Génération du jeton d’accès à l’aide d’un interpréteur de commandes (Bash) :**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Génération du jeton d’accès à l’aide de PowerShell :**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La réponse contient un jeton d’accès, des informations sur la durée de validité du jeton est valide et sur la ressource pour laquelle vous pouvez utiliser ce jeton. Le jeton d’accès que vous avez reçu dans le précédent appel HTTP doit être transmis à toutes les requêtes à l’API ARM en tant qu’en-tête nommé « Authorization (Autorisation) » avec la valeur « Bearer YOUR\_ACCESS\_TOKEN ». Notez l’espace entre « Bearer » et votre jeton d’accès.

Comme vous pouvez le voir à partir du résultat HTTP ci-dessus, le jeton est valide pendant un délai spécifique au cours duquel vous devez mettre en cache et réutiliser ce même jeton. Même s’il est possible de s’authentifier auprès d’Azure AD pour chaque appel d’API, cette procédure serait peu efficace.

## Appel d’API ARM REST

[Les API REST Azure Resource Manager sont documentées ci-après](https://msdn.microsoft.com/library/azure/dn790568.aspx), mais ce didacticiel n’aborde pas l’utilisation de chacune. Cette documentation utilise uniquement quelques API afin d’expliquer les bases de leur utilisation. Nous vous renvoyons pour le reste à la documentation officielle.

### Répertorier tous les abonnements

L’une des opérations plus simples à exécuter consiste à répertorier les abonnements disponibles auxquels vous pouvez accéder. Dans la requête ci-dessous, vous pouvez voir comment le jeton d’accès est transmis en tant qu’en-tête.

(Remplacez YOUR\_ACCESS\_TOKEN par votre véritable jeton d’accès.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... et vous obtiendrez comme résultat une liste des abonnements auxquels ce principal du service est autorisé à accéder

(les ID d’abonnement ci-dessous ont été raccourcis pour une meilleure lisibilité)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### Répertorier tous les groupes de ressources dans un abonnement spécifique

Toutes les ressources disponibles avec les API ARM sont imbriquées dans un groupe de ressources. Nous allons interroger ARM pour obtenir les groupes de ressources existants dans notre abonnement à l’aide de la requête HTTP GET ci-dessous. Notez que, cette fois, l’ID d’abonnement est transmis comme élément de l’URL.

(Remplacez YOUR\_ACCESS\_TOKEN et SUBSCRIPTION\_ID par vos véritables jetons d’accès et ID d’abonnement)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La réponse dépend de la définition ou non de groupes de ressources définis et, le cas échéant, de leur nombre.

(les ID d’abonnement ci-dessous ont été raccourcis pour une meilleure lisibilité)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### Créer un groupe de ressources

Jusqu’à présent, nous avons uniquement interrogé les API ARM pour obtenir des informations. Il est temps de plutôt créer des ressources, et nous allons commencer par la plus simple de toutes : un groupe de ressources. La requête HTTP suivante crée un nouveau groupe de ressources dans une région/un emplacement de votre choix et lui ajoute une ou plusieurs balises (l’exemple ci-dessous n’ajoute en fait qu’une balise).

(Remplacez YOUR\_ACCESS\_TOKEN, SUBSCRIPTION\_ID et RESOURCE\_GROUP\_NAME par votre jeton d’accès réel, votre ID d’abonnement et nom du groupe de ressources à créer)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Si l’opération réussit, vous obtiendrez une réponse similaire à celle-ci

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Vous avez créé un groupe de ressources dans Azure ! Félicitations !

### Déployer des ressources dans un groupe de ressources à l’aide d’un modèle ARM

Avec ARM, vous pouvez déployer vos ressources à l’aide de modèles ARM. Un modèle ARM définit plusieurs ressources et leurs dépendances. Pour cette section, nous supposons que vous êtes familiarisé avec les modèles ARM et vous montrons simplement comment effectuer l’appel d’API pour en commencer le déploiement. Vous trouverez ici une documentation détaillée des modèles ARM.

Le déploiement d’un modèle ARM ne diffère pas beaucoup de la manière dont vous appelez d’autres API. Il est à noter que le déploiement d’un modèle peut prendre beaucoup de temps, selon ce qu’il contient et ce que l’appel d’API renvoie. C’est donc à vous, en tant que développeur, d’interroger l’état du déploiement afin de savoir à quel moment le déploiement est terminé.

Pour cet exemple, nous allons utiliser un modèle ARM exposé publiquement disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates). Le modèle que nous allons utiliser déploie une machine virtuelle Linux pour la région ouest des États-Unis. Bien que ce modèle soit disponible dans un référentiel public tel que GitHub, vous pouvez également décider de transmettre le modèle complet dans le cadre de la requête. Notez que nous fournissons des valeurs de paramètre dans le cadre de la requête qui sera utilisée dans le modèle utilisé.

(Remplacez SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME, DEPLOYMENT\_NAME, YOUR\_ACCESS\_TOKEN, GLOBALY\_UNIQUE\_STORAGE\_ACCOUNT\_NAME, ADMIN\_USER\_NAME,ADMIN\_PASSWORD et DNS\_NAME\_FOR\_PUBLIC\_IP par des valeurs appropriées pour votre requête)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La réponse JSON, qui est assez longue pour cette requête, a été omise afin d’améliorer la lisibilité de cette documentation. La réponse contient des informations sur le déploiement basé sur un modèle que vous venez de créer.

<!---HONumber=AcomDC_0921_2016-->