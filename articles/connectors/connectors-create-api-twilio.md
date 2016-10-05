<properties
pageTitle="Ajouter le connecteur Twilio à vos applications logiques | Microsoft Azure"
description="Vue d’ensemble du connecteur Twilio avec les paramètres d’API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="09/19/2016"
ms.author="mandia"/>

# Prise en main du connecteur Twilio

Connectez-vous à Twilio pour envoyer et recevoir des SMS, des MMS, et des adresses IP.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Twilio, vous pouvez :

- Créer votre flux d'activité en fonction des données que vous obtenez de Twilio.
- Utiliser des actions pour obtenir un message, répertorier les messages, et plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, lorsque vous recevez un nouveau message Twilio, vous pouvez prendre ce message et l’utiliser comme flux de travail Service Bus.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Le connecteur Twilio inclut les actions suivantes. Il n'y a aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
|Aucun| <ul><li>Obtenir le message</li><li>Répertorier les messages</li><li>Envoyer un message</li></ul>|

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## Créer une connexion à Twilio
Quand vous ajoutez ce connecteur à vos applications logiques, entrez les valeurs Twilio suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|ID de compte|Oui|Entrez votre ID de compte Twilio|
|Jeton d'accès|Oui|Entrez votre jeton d’accès Twilio|

>[AZURE.INCLUDE [Procédure de création d’une connexion à Twilio](../../includes/connectors-create-api-twilio.md)]

Si vous n’avez pas de jeton d’accès, consultez [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) pour savoir comment en créer un.


>[AZURE.TIP] Vous pouvez utiliser cette même connexion Twilio dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
#### Cette documentation concerne la version 1.0.

### Obtenir le message.
Retourne un message unique spécifié par l’ID du message fourni. ```GET: /Messages/{MessageId}.json```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|MessageId|string|yes|path|(aucun)|ID de message|

### Réponse
|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|404|Message introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|
|default|L’opération a échoué.|


### Répertorier les messages
Retourne une liste de messages associés à votre compte. ```GET: /Messages.json```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|À|string|no|query|(aucun)|Vers le numéro de téléphone|
|À partir d'une base|string|no|query|(aucun)|À partir du numéro de téléphone|
|DateSent|string|no|query|(aucun)|Afficher uniquement les messages envoyés à cette date (au format GMT), sous la forme AAAA-MM-JJ. Exemple : DateSent=2009-07-06. Vous pouvez également spécifier une inégalité, par exemple DateSent<=AAAA-MM-JJ pour les messages envoyés avant minuit à une certaine date, puis DateSent>=AAAA-MM-JJ pour les messages envoyés minuit à une certaine date.|
|PageSize|integer|no|query|50|Nombre de ressources à retourner dans chaque page de la liste. 50 par défaut.|
|Page|integer|no|query|0|Nombre de page. La valeur par défaut est 0.|

### Réponse
|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|



### Envoyer un message
Envoie un nouveau message à un numéro de mobile. ```POST: /Messages.json```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|sendMessageRequest| |yes|body|(aucun)|Message à envoyer|

### Réponse
|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|
|default|L’opération a échoué.|


## Définitions d’objet

#### SendMessageRequest : modèle de demande pour l'opération d'envoi de message

|Nom de la propriété | Type de données | Requis|
|---|---|---|
|from|string|yes|
|to|string|yes|
|body|string|yes|
|media\_url|array|no|
|status\_callback|string|no|
|messaging\_service\_sid|string|no|
|application\_sid|string|no|
|max\_price|string|no|


#### Message : modèle de message

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|body|string|no|
|from|string|no|
|to|string|no|
|status|string|no|
|sid|string|no|
|account\_sid|string|no|
|api\_version|string|no|
|num\_segments|string|no|
|num\_media|string|no|
|date\_created|string|no|
|date\_sent|string|no|
|date\_updated|string|no|
|direction|string|no|
|error\_code|string|no|
|error\_message|string|no|
|price|string|no|
|price\_unit|string|no|
|URI|string|no|
|subresource\_uris|array|no|
|messaging\_service\_sid|string|no|

#### MessageList : modèle de réponse pour l'opération Répertorier les messages

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|messages|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|URI|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|
|total|integer|no|
|previous\_page\_uri|string|no|

#### IncomingPhoneNumberList : modèle de réponse pour l'opération Répertorier les messages

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|incoming\_phone\_numbers|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|URI|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|


#### AddIncomingPhoneNumberRequest : modèle de demande pour l'opération Ajouter un numéro entrant

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|PhoneNumber|string|yes|
|AreaCode|string|no|
|FriendlyName|string|no|


#### IncomingPhoneNumber : numéro de téléphone entrant

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|sid|string|no|
|account\_sid|string|no|
|date\_created|string|no|
|date\_updated|string|no|
|capabilities|non défini|no|
|status\_callback|string|no|
|status\_callback\_method|string|no|
|api\_version|string|no|


#### Capabilities : fonctionnalités du numéro de téléphone

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|mms|booléenne|no|
|sms|booléenne|no|
|voice|booléenne|no|

#### AvailablePhoneNumbers : numéros de téléphone disponibles

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|lata|string|no|
|latitude|string|no|
|longitude|string|no|
|postal\_code|string|no|
|rate\_center|string|no|
|region|string|no|
|MMS|booléenne|no|
|SMS|booléenne|no|
|voice|booléenne|no|


#### UsageRecords : classe d'enregistrements d'utilisation

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|category|string|no|
|usage|string|no|
|usage\_unit|string|no|
|description|string|no|
|price|number|no|
|price\_unit|string|no|
|count|string|no|
|count\_unit|string|no|
|start\_date|string|no|
|end\_date|string|no|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0921_2016-->