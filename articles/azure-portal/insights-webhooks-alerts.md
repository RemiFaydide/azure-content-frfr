<properties
	pageTitle="Configurer des webhooks sur des alertes de métrique Azure | Microsoft Azure"
	description="Rediriger des alertes Azure vers d’autres systèmes non-Azure"
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

# Configurer un webhook sur une alerte de métrique Azure

Les webhooks vous permettent d’acheminer une notification d’alerte Azure vers d’autres systèmes à des fins de post-traitement ou d’exécution d’actions personnalisées. Vous pouvez utiliser un webhook sur une alerte pour acheminer cette dernière vers des services qui envoient un SMS, consignent les bogues, avertissent une équipe par le biais de services de conversation instantanée/messagerie ou exécutent diverses autres actions. Cet article décrit la procédure de définition d’un webhook sur une alerte de métrique Azure, ainsi que l’aspect de la charge utile de la requête HTTP POST vers un webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte de journal d’activité Azure (alertes sur les événements), [consultez plutôt cette page](./insights-auditlog-to-webhook-email.md).

Les alertes Azure exécutent une requête HTTP POST transmettant le contenu de l’alerte au format JSON, avec le schéma défini ci-après, vers un URI de webhook que vous fournissez lors de la création de l’alerte. Cet URI doit être un point de terminaison HTTP ou HTTPS valide. Azure publie une entrée par demande lorsqu’une alerte est activée.

## Configuration de webhooks par le biais du portail

Vous pouvez ajouter ou mettre à jour l’URI du webhook dans l’écran Créer/mettre à jour des alertes du [Portail](https://portal.azure.com/).

![Ajouter une règle d’alerte](./media/insights-webhooks-alerts/Alertwebhook.png)

Vous pouvez également configurer une alerte à publier vers un URI de webhook à l’aide des [applets de commande Azure PowerShell](./insights-powershell-samples.md#create-alert-rules), de [l’interface de ligne de commande multiplateforme](./insights-cli-samples.md#work-with-alerts) ou de [l’API REST Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Authentification du webhook

Le webhook peut s’authentifier à l’aide de l’une des méthodes suivantes :

1. **Autorisation par jeton** : l’URI du webhook est enregistré avec un ID de jeton, par exemple `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.	**Autorisation de base** : l’URI du webhook est enregistré avec un nom d’utilisateur et un mot de passe, par exemple `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## Schéma de la charge utile

L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes basées sur des métriques.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Champ | Obligatoire | Ensemble fixe de valeurs | Remarques |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|O|"Activated", "Resolved"|État de l’alerte en fonction des conditions que vous avez définies.|
|context| O | | Contexte de l’alerte.|
|timestamp| O | | Heure à laquelle l’alerte a été déclenchée.|
|id | O | | Chaque règle d’alerte possède un ID unique.|
|name |O | | Nom de l’alerte.|
|description |O | |Description de l’alerte.|
|conditionType |O |"Metric", "Event" |Deux types d’alertes sont pris en charge. L’un repose sur une condition de métrique, et l’autre sur un événement dans le journal d’activité. Utilisez cette valeur pour vérifier si l’alerte est basée sur une métrique ou sur un événement.|
|condition |O | | Champs à vérifier en fonction du champ conditionType.|
|metricName |Pour les alertes de métrique | |Nom de la métrique qui définit ce que la règle surveille|
|metricUnit |Pour les alertes de métrique |« Bytes », « BytesPerSecond », « Count », « CountPerSecond », « Percent », « Seconds »|	 Unité autorisée dans la métrique [Les valeurs autorisées sont répertoriées ici](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue |Pour les alertes de métrique | |Valeur réelle de la métrique ayant entraîné l’alerte.|
|threshold |Pour les alertes de métrique | |Valeur de seuil en fonction de laquelle l’alerte est activée.|
|windowSize |Pour les alertes de métrique | |Période de temps qui est utilisée pour surveiller l’activité d’alerte en fonction du seuil. Doit être comprise entre 5 minutes et 1 jour. Le format de la durée répond à la norme ISO 8601.|
|timeAggregation |Pour les alertes de métrique |« Average », « Last », « Maximum », « Minimum », « None », « Total » |	Détermine la façon dont les données collectées doivent être combinées au fil du temps. La valeur par défaut est Average. [Les valeurs autorisées sont répertoriées ici](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operator |Pour les alertes de métrique | |Opérateur utilisé pour la comparaison des données de métrique actuelles au seuil défini.|
|subscriptionId |O | |ID d’abonnement Azure.|
|resourceGroupName |O | |Nom du groupe de ressources de la ressource affectée.|
|resourceName |O | |Nom de la ressource affectée.|
|resourceType |O | |Type de la ressource affectée.|
|resourceId |O | |ID de ressource de la ressource affectée.|
|resourceRegion |O | |Région ou emplacement de la ressource affectée.|
|portalLink |O | |Lien direct vers la page de résumé de la ressource sur le Portail.|
|properties |N |Facultatif |Ensemble de paires `<Key, Value>` (par exemple, `Dictionary<String, String>`) incluant des détails sur l’événement. Le champ properties est facultatif. Dans un workflow basé sur une application logique ou une interface utilisateur personnalisée, les utilisateurs peuvent entrer des paires clé/valeur transmissibles par le biais de la charge utile. L’autre manière de passer des propriétés personnalisées au webhook consiste à utiliser l’URI du webhook (sous la forme de paramètres de requête).|


>[AZURE.NOTE] Le champ properties est uniquement définissable à l’aide de [l’API REST Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Étapes suivantes

- En savoir plus sur les alertes Azure et sur les webhooks par le biais de la vidéo décrivant [l’intégration d’alertes Azure à PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Exécuter des scripts Azure Automation (Runbooks) sur des alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0921_2016-->