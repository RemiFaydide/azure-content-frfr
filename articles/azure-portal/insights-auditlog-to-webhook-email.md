<properties
	pageTitle="Configurer des webhooks sur des alertes de journal d’activité Azure | Microsoft Azure"
	description="Découvrez comment utiliser des alertes de journal d’activité pour appeler des webhooks. "
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="ashwink"/>

# Configurer un webhook sur une alerte de journal d’activité Azure

Les webhooks vous permettent d’acheminer une notification d’alerte Azure vers d’autres systèmes à des fins de post-traitement ou d’exécution d’actions personnalisées. Vous pouvez utiliser un webhook sur une alerte pour acheminer cette dernière vers des services qui envoient un SMS, consignent les bogues, avertissent une équipe par le biais de services de conversation instantanée/messagerie ou exécutent diverses autres actions. Cet article décrit la procédure de définition d’un webhook sur une alerte de journal d’activité Azure, ainsi que l’aspect de la charge utile de la requête HTTP POST vers un webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte de métrique Azure, [consultez plutôt cette page](./insights-webhooks-alerts.md). Vous pouvez également configurer une alerte de journal d’activité pour l’envoi d’un e-mail lors de l’activation.

>[AZURE.NOTE] Cette fonctionnalité n’étant pour l’instant disponible qu’à titre d’évaluation, elle offre une qualité et des performances variables.

Vous pouvez configurer une alerte de journal d’activité à l’aide des [applets de commande Azure PowerShell](./insights-powershell-samples.md#create-alert-rules), de [l’interface de ligne de commande multiplateforme](./insights-cli-samples.md#work-with-alerts) ou de [l’API REST Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Authentification du webhook
Le webhook peut s’authentifier à l’aide de l’une des méthodes suivantes :

1. **Autorisation par jeton** : l’URI du webhook est enregistré avec un ID de jeton, par exemple `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.	**Autorisation de base** : l’URI du webhook est enregistré avec un nom d’utilisateur et un mot de passe, par exemple `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## Schéma de la charge utile
L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes basées sur un journal d’activité. Ce schéma est semblable à celui utilisé par les alertes basées sur des métriques.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nom de l’élément|	Description|
|---|---|
|status |Utilisé pour les alertes de métrique. Toujours défini sur « activated » pour les alertes de journal d’activité.|
|context|Contexte de l’événement.|
|resourceProviderName|Fournisseur de la ressource affectée.|
|conditionType |Toujours défini sur « Event ».|
|name |Nom de la règle d’alerte.|
|id |ID de ressource de l’alerte.|
|description|	Description de l’alerte telle que définie lors de la création de l’alerte.|
|subscriptionId |ID d’abonnement Azure.|
|timestamp|	Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande.|
|resourceId |ID de ressource de la ressource affectée.|
|resourceGroupName|Nom du groupe de ressources de la ressource affectée.|
|properties |Ensemble de paires `<Key, Value>` (par exemple, `Dictionary<String, String>`) incluant des détails sur l’événement.|
|event|Élément contenant des métadonnées relatives à l’événement.|
|autorisation|Propriétés de contrôle d’accès en fonction du rôle (RBAC) de l’événement. Il s’agit généralement des propriétés « action », « role » et « scope ».|
|category | Catégorie de l’événement. Les valeurs prises en charge sont : Administrative, Alert, Security, ServiceHealth, Recommendation.|
|caller|Adresse e-mail de l’utilisateur ayant effectué l’opération, la revendication de nom d’utilisateur principal (UPN) ou la revendication de nom de principal du service (SPN) basée sur la disponibilité. Peut être null pour certains appels système.|
|correlationId|	Généralement un GUID au format chaîne. Les événements avec correlationId appartiennent à la même action et partagent généralement un correlationId.|
|eventDescription |Description textuelle statique de l’événement.|
|eventDataId|Identificateur unique de l’événement.|
|eventSource |Nom de l’infrastructure ou du service Azure ayant généré l’événement.|
|httpRequest|	Inclut généralement clientRequestId, clientIpAddress et la méthode (méthode HTTP PUT, par exemple).|
|level|L’une des valeurs suivantes : « Critical », « Error », « Warning », « Informational » et « Verbose ».|
|operationId|Généralement, GUID partagé par les événements correspondant à une opération unique.|
|operationName|Nom de l’opération.|
|properties |Les propriétés de l’événement.|
|status|Chaîne. État de l’opération. Les valeurs courantes sont : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ».|
|subStatus|	Inclut généralement le code d’état HTTP de l’appel REST correspondant. Il peut également inclure d’autres chaînes décrivant un sous-état. Les valeurs courantes sont : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No Content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not Found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service Unavailable (Code d’état HTTP : 503), Gateway Timeout (Code d’état HTTP : 504).|

## Étapes suivantes
- [En savoir plus sur le journal d’activité](./monitoring-overview-activity-logs.md)
- [Exécuter des scripts Azure Automation (Runbooks) sur des alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
- [Utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
- [Utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.

<!---HONumber=AcomDC_0921_2016-->