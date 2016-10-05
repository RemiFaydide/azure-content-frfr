<properties
 pageTitle="Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance | Microsoft Azure"
 description="Description de la solution préconfigurée de surveillance à distance Azure IoT et de son architecture"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance

La solution préconfigurée de surveillance à distance Azure IoT Suite montre une approche de gestion des métadonnées d’appareil. Cet article décrit l’approche de cette solution pour vous permettre de comprendre :

- Quelles métadonnées d’appareil sont stockées par la solution.
- Comment la solution gère les métadonnées d’appareil.

## Context

La solution préconfigurée de surveillance à distance utilise [Azure IoT Hub][lnk-iot-hub] pour permettre à vos appareils d’envoyer des données vers le cloud. IoT hub inclut un [registre d’identité des appareils][lnk-identity-registry] pour contrôler l’accès à IoT Hub. Le registre des identités des appareils IoT Hub est distinct du *registre des appareils* spécifique de la solution de surveillance à distance qui stocke les métadonnées des informations d’appareil. La solution de surveillance à distance utilise une base de données [DocumentDB][lnk-docdb] pour implémenter son registre des appareils pour le stockage des métadonnées des informations d’appareil. L’[architecture de référence de Microsoft Azure IoT][lnk-ref-arch] décrit le rôle du registre des appareils dans une solution IoT classique.

> [AZURE.NOTE] La solution préconfigurée de surveillance à distance synchronise le registre des identités des appareils avec le registre des appareils. Les deux utilisent le même id d’appareil pour identifier chaque appareil connecté à votre IoT hub de manière unique.

La [Version préliminaire de la gestion des appareils IoT Hub][lnk-dm-preview] ajoute des fonctionnalités à IoT Hub qui sont similaires aux fonctionnalités de gestion des informations d’appareil décrites dans cet article. Actuellement, la solution de surveillance à distance utilise uniquement les fonctionnalités de IoT Hub généralement disponibles.

## Métadonnées des informations d’appareil

Un document JSON sur les métadonnées relatives aux informations d’appareil stocké dans la base de données DocumentDB du registre des appareils possède la structure suivante :

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: l’appareil lui-même écrit ces propriétés et l’appareil est l’autorité pour ces données. Parmi les exemples de propriétés d’appareil figurent, le fabricant, le numéro de modèle et le numéro de série.
- **DeviceID** : ID d’appareil unique. Cette valeur est la même dans le registre des identités des appareils IoT Hub.
- **HubEnabledState** : état de l’appareil dans IoT Hub. Cette valeur est initialement définie sur **null** jusqu’à la première connexion de l’appareil. Dans le portail de la solution, une valeur **null** se présente sous la forme « enregistré mais absent ».
- **CreatedTime** : heure à laquelle l’appareil a été créé.
- **DeviceState** : état signalé par l’appareil.
- **UpdatedTime** : heure de la dernière mise à jour dans le portail de la solution.
- **SystemProperties** : le portail de la solution écrit les propriétés système sans que l’appareil ait connaissance de ces propriétés. Un exemple de propriété système est **l’ICCID** si la solution est autorisée auprès d’un service gérant les appareils SIM et connectée à ce dernier.
- **Commands** : liste des commandes prises en charge par l’appareil. L’appareil fournit ces informations à la solution.
- **CommandHistory** : liste des commandes envoyées par la solution de surveillance à distance à l’appareil et état de ces commandes.
- **IsSimulatedDevice** : indicateur identifiant un appareil comme étant simulé.
- **id** : identifiant DocumentDB unique pour ce document d’appareil.

> [AZURE.NOTE] Les informations d’appareil peuvent également inclure des métadonnées permettant de décrire la télémétrie que l’appareil envoie au IoT Hub. La solution de surveillance à distance utilise ces métadonnées de télémétrie pour personnaliser la façon dont le tableau de bord affiche la [télémétrie dynamique][lnk-dynamic-telemetry].

## Cycle de vie

Lorsque vous créez pour la première fois un appareil dans le portail de la solution, la solution crée une entrée dans son registre des appareils comme indiqué précédemment. La plupart des informations sont initialement écrasées et **HubEnabledState** est défini sur **null**. À ce stade, la solution crée également une entrée pour l’appareil dans le registre des identités des appareils, qui génère les clés utilisées par l’appareil pour s’authentifier avec IoT Hub.

Lorsqu’un appareil se connecte pour la première fois à la solution, il envoie un message d’information d’appareil. Ce message d’information d’appareil inclut des propriétés d’appareil, telles que le fabricant de l’appareil, le numéro de modèle et le numéro de série. Un message d’information d’appareil inclut également une liste des commandes prises en charge par l’appareil comprenant des informations sur les paramètres de commande. Lorsque la solution reçoit ce message, elle met à jour les métadonnées d’information d’appareil dans le registre des appareils.

### Afficher et modifier les informations d’appareil dans le portail de la solution

La liste des appareils du portail de la solution affiche les propriétés d’appareil suivantes sous forme de colonnes : **Status**, **DeviceId**, **Manufacturer**, **Model Number**, **Serial Number**, **Firmware**, **Platform**, **Processor**, et **Installed RAM**. Les propriétés d’appareil **Latitude** et **Longitude** indique l’emplacement dans la carte Bing du tableau de bord.

![Liste des appareils][img-device-list]

Vous pouvez modifier toutes ces propriétés en cliquant sur **Modifier** dans le volet **Détails de l’appareil** du portail de la solution. La modification de ces propriétés met à jour l’enregistrement de l’appareil dans la base de données DocumentDB. Toutefois, si un appareil envoie un message d’informations d’appareil mis à jour, il remplace toutes les modifications apportées dans le portail de la solution. Vous ne pouvez pas modifier les propriétés **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState**, et **UpdatedTime** dans le portail de la solution, car seul l’appareil a autorité sur ces dernières.

![Modification d’appareils][img-device-edit]

Vous pouvez utiliser le portail de la solution pour supprimer un appareil de votre solution. Lorsque vous supprimez un appareil, la solution supprime les métadonnées relatives aux informations d’appareil à partir du registre des appareils de la solution et supprime l’entrée de l’appareil du registre des identités des appareils IoT Hub. Avant de pouvoir supprimer un appareil, vous devez le désactiver.

![Suppression d’appareils][img-device-remove]

## Traitement des messages d’information d’appareil

Les messages d’information d’appareil envoyés par un appareil sont différents des messages de télémétrie. Les messages d’information d’appareil incluent des informations telles que les propriétés de l’appareil, les commandes auxquelles répond un appareil et tous les historiques de commandes. IoT Hub lui-même n’a aucune connaissance des métadonnées contenues dans un message d’information d’appareil et traite le message de la même manière qu’il traite tout message appareil-à-cloud. Dans la solution de surveillance à distance, une tâche [Azure Stream Analytics][lnk-stream-analytics] \(ASA) lit les messages issus de IoT Hub. Le travail **DeviceInfo** Stream Analytics filtre les messages contenant **« ObjectType » : « DeviceInfo »** et les transmet à l’instance hôte **EventProcessorHost** qui s’exécute dans une tâche web. La logique de l’instance **EventProcessorHost** utilise l’ID d’appareil pour rechercher l’enregistrement DocumentDB de l’appareil spécifique et le mettre à jour. L’enregistrement du registre des appareils inclut désormais des informations, telles que les propriétés d’appareil, les commandes et l’historique des commandes.

> [AZURE.NOTE] Un message d’information d’appareil est un message appareil-à-cloud standard. La solution fait la distinction entre les messages d’information d’appareil et les messages de télémétrie en utilisant des requêtes ASA.

## Exemples d’enregistrements d’informations d’appareil

La solution préconfigurée de surveillance à distance utilise deux types d’enregistrements d’informations d’appareil : les enregistrements pour les appareils simulés déployés avec la solution et les enregistrements pour les appareils personnalisés que vous connectez à la solution.

### Appareil simulé

L’exemple suivant illustre l’enregistrement d’informations d’appareil JSON pour un périphérique simulé. Cet enregistrement possède un ensemble de valeurs pour **UpdatedTime** qui indique que l’appareil a envoyé un message **DeviceInfo** à IoT Hub. L’enregistrement inclut certaines propriétés d’appareil communes, définit les six commandes prises en charge par les appareils simulés et a l’indicateur **IsSimulatedDevice** défini sur **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### Appareil personnalisé

L’exemple suivant illustre l’enregistrement d’informations d’appareil JSON pour un appareil personnalisé et a l’indicateur **IsSimulatedDevice** défini sur **0**. Vous pouvez voir que cet appareil personnalisé prend en charge deux commandes et que le portail de la solution a envoyé une commande **SetTemperature** à l’appareil :

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

L’exemple suivant montre le message **DeviceInfo** JSON que l’appareil a envoyé pour mettre à jour les métadonnées relatives aux informations d’appareil :

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## Étapes suivantes

Maintenant que vous savez comment personnaliser les solutions préconfigurées, vous pouvez explorer certaines des autres fonctions et fonctionnalités des solutions préconfigurées de la suite IoT :

- [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive-overview]
- [Forum Aux Questions (FAQ) relatives à IoT Suite][lnk-faq]
- [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

<!---HONumber=AcomDC_0914_2016-->