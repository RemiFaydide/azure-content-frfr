
<properties
   pageTitle="Personnaliser les paramètres de cluster Service Fabric et la stratégie de mise à niveau de la structure | Microsoft Azure"
   description="Cet article décrit les paramètres de structure et les stratégies de mise à niveau de la structure que vous pouvez personnaliser."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="chackdan"/>

# Personnaliser les paramètres de cluster Service Fabric et la stratégie de mise à niveau de la structure

Ce document vous explique comment personnaliser les différents paramètres de structure et la stratégie de mise à niveau de la structure pour votre cluster Service Fabric. Vous pouvez les personnaliser sur le portail ou à l’aide d’un modèle Resource Manager.

## Paramètres de structure que vous pouvez personnaliser


Voici les paramètres de structure que vous pouvez personnaliser :

### Nom de la section : Sécurité

|**Paramètre**|**Valeurs autorisées**|**Conseils ou brève description**|
|-----------------------|--------------------------|--------------------------|
|ClusterProtectionLevel|None ou EncryptAndSign| None (par défaut) pour les clusters non sécurisés, EncryptAndSign pour les clusters sécurisés. |

### Nom de la section : Hébergement

|**Paramètre**|**Valeurs autorisées**|**Conseils ou brève description**|
|-----------------------|--------------------------|--------------------------|
|ServiceTypeRegistrationTimeout|Durée en secondes, la valeur par défaut est 300| Durée maximale autorisée pour que ServiceType soit inscrit auprès de la structure|
|ServiceTypeDisableFailureThreshold|Nombre entier, la valeur par défaut est 1| Il s’agit du seuil pour le nombre d’échecs après lequel FailoverManager (FM) reçoit une notification de désactiver le type de service sur ce nœud et d’essayer un autre nœud pour le positionnement.|
|ActivationRetryBackoffInterval|Durée en secondes, la valeur par défaut est 5|Intervalle d’interruption sur chaque échec d’activation. Sur chaque cas d’échec d’activation continue, le système retente l’activation pour une durée équivalente à MaxActivationFailureCount. L’intervalle avant nouvelle tentative pour chaque tentative est une combinaison de l’échec de l’activation continue et de l’intervalle d’interruption d’activation.|
|ActivationMaxRetryInterval|Durée en secondes, la valeur par défaut est 300| Pour chaque cas d’échec d’activation continue, le système retente l’activation pour une durée équivalente à ActivationMaxFailureCount. ActivationMaxRetryInterval spécifie l’intervalle de temps d’attente avant une nouvelle tentative après chaque échec d’activation |
|ActivationMaxFailureCount|Nombre entier, la valeur par défaut est 10| Nombre de fois où les nouvelles tentatives système ont échoué à l’activation avant d’abandonner |

### Nom de la section : FailoverManager

|**Paramètre**|**Valeurs autorisées**|**Conseils ou brève description**|
|-----------------------|--------------------------|--------------------------|
|PeriodicLoadPersistInterval|Durée en secondes, la valeur par défaut est 10| Ce paramètre détermine la fréquence à laquelle FM vérifie la présence de nouveaux rapports de charge|

### Nom de la section : Fédération

|**Paramètre**|**Valeurs autorisées**|**Conseils ou brève description**|
|-----------------------|--------------------------|--------------------------|
|LeaseDuration|Durée en secondes, la valeur par défaut est 30|Durée de bail entre un nœud et ses voisins.|
|LeaseDurationAcrossFaultDomain|Durée en secondes, la valeur par défaut est 30|Durée de bail entre un nœud et ses voisins dans des domaines d’erreur.|

### Nom de la section : ClusterManager

|**Paramètre**|**Valeurs autorisées**|**Conseils ou brève description**|
|-----------------------|--------------------------|--------------------------|
|UpgradeStatusPollInterval|Durée en secondes, la valeur par défaut est 60|Fréquence d’interrogation de l’état de mise à niveau de l’application. Cette valeur détermine le taux de mise à jour pour tout appel GetApplicationUpgradeProgress|
|UpgradeHealthCheckInterval|Durée en secondes, la valeur par défaut est 60|Fréquence des contrôles de l’état d’intégrité durant une mise à niveau de l’application surveillée|
|FabricUpgradeStatusPollInterval|Durée en secondes, la valeur par défaut est 60|Fréquence d’interrogation de l’état de mise à niveau de la structure. Cette valeur détermine le taux de mise à jour pour tout appel GetFabricUpgradeProgress |
|FabricUpgradeHealthCheckInterval|Durée en secondes, la valeur par défaut est 60|Fréquence des contrôles de l’état d’intégrité durant une mise à niveau de la structure surveillée|



## Étapes suivantes

Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

[Ajouter, restaurer, supprimer des certificats de votre cluster Azure ](service-fabric-cluster-security-update-certs-azure.md)

<!---HONumber=AcomDC_0921_2016-->