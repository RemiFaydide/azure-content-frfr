<properties
	pageTitle="Gestion des appareils IoT Hub - Représentations d’appareils | Microsoft Azure"
	description="Didacticiel sur la gestion des appareils Azure IoT Hub décrivant l’utilisation des représentations d’appareils."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Didacticiel : Utilisation des représentations d’appareil physique avec C# (version préliminaire)

[AZURE.INCLUDE [iot-hub-device-management-twin-selector](../../includes/iot-hub-device-management-twin-selector.md)]
## Introduction

Gestion des appareils Azure IoT Hub présente la représentation d’appareil (qui est la représentation d’un appareil physique côté service). Voici un diagramme indiquant les différents composants de la représentation d’appareil.

![][img-twin]

Dans ce didacticiel, nous allons nous concentrer sur les propriétés de l’appareil. Pour en savoir plus sur les autres composants, consultez [Vue d’ensemble de la gestion des appareils Azure IoT Hub][lnk-dm-overview].

Propriétés de l’appareil est un dictionnaire prédéfini de propriétés décrivant l’appareil physique. L’appareil physique est le maître de chaque propriété de l’appareil et le magasin de référence de chaque valeur correspondante. Une représentation « cohérente » de ces propriétés est stockée dans la représentation d’appareil au sein du cloud. La cohérence et l’actualisation sont soumis aux paramètres de synchronisation, décrits ci-dessous. Les exemples de propriétés de l’appareil incluent notamment la version du microprogramme, le niveau de batterie et le nom du fabricant.

## Synchronisation des propriétés de l’appareil

L’appareil physique est la source de référence pour les propriétés de l’appareil. Les valeurs sélectionnées sur l’appareil physique sont automatiquement synchronisées avec la représentation d’appareil dans IoT Hub par le biais du modèle *observer/informer* décrit par [LWM2M][lnk-lwm2m].

Quand l’appareil physique se connecte à IoT Hub, le service lance *l’observation* sur les propriétés de l’appareil sélectionné. Ensuite, l’appareil physique *informe* IoT Hub des modifications apportées aux propriétés de l’appareil. Pour implémenter des hystérésis, **pmin** (la durée minimale entre les notifications) est défini sur 5 minutes. Cela signifie que pour chaque propriété, l’appareil physique n’informe pas IoT Hub plus d’une fois toutes les 5 minutes, même s’il y a une modification. Pour assurer l’actualisation, **pmax** (le délai maximum entre les notifications) est défini sur 6 heures. Cela signifie que pour chaque propriété, l’appareil physique informe IoT Hub une fois toutes les 6 heures, même si la propriété n’a pas changé au cours de cette période.

Lorsque l’appareil physique se déconnecte, la synchronisation s’arrête. La synchronisation redémarre lorsque l’appareil se reconnecte au service. Vous pouvez toujours vérifier la dernière heure de mise à jour d’une propriété pour vérifier l’actualisation.

Vous trouverez ci-dessous la liste complète des propriétés de l’appareil qui sont automatiquement observées :

![][img-observed]


## Exécution de l’exemple de représentation de l’appareil

L’exemple suivant étend la fonctionnalité du didacticiel [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started]. Une fois les différents appareils simulés en cours d’exécution, il utilise la représentation d’appareil pour lire et modifier les propriétés sur un appareil simulé.

### Composants requis 

Avant d’exécuter cet exemple, vous devez avoir terminé les étapes de [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started]. Cela signifie que vos appareils simulés doivent être en cours d’exécution. Si vous avez terminé le processus auparavant, redémarrez immédiatement vos appareils simulés.

### Démarrage de l’exemple

Pour démarrer l’exemple, vous devez exécuter le processus **DeviceTwin.exe**. Celui-ci lit les propriétés de l’appareil à partir de la représentation d’appareil et à partir de l’appareil physique. Il modifie également une propriété de l’appareil sur l’appareil physique. Procédez comme suit pour démarrer l’exemple :

1.  À partir du dossier racine dans lequel vous avez cloné le référentiel **azure-iot-sdks**, accédez au dossier **azure-iot-sdks\\csharp\\service\\samples\\bin**.

2.  Exécutez `DeviceTwin.exe <IoT Hub Connection String>`.

Dans la fenêtre de ligne de commande, vous devriez voir la sortie illustrant l’utilisation de la représentation d’appareil. L’exemple se déroule selon le processus suivant :

1.  Imprime toutes les propriétés de l’appareil sur une représentation d’appareil.

2.  Lecture approfondie : lisez la propriété de l’appareil sur le niveau de batterie à partir de l’appareil physique (3 fois).

3.  Écriture approfondie : écrit la propriété de l’appareil **Fuseau horaire** sur l’appareil physique.

4.  Lecture approfondie : lit la propriété de l’appareil **Fuseau horaire** à partir de l’appareil physique pour voir qu’elle a été modifiée.

### Lecture partielle

Il existe une différence entre les lectures *partielles* et les lectures/écritures *approfondies*. Une lecture partielle renvoie la valeur de la propriété demandée à partir de la représentation de l’appareil stockée dans Azure IoT Hub. Il s’agit de la valeur de l’opération de notification précédente. Vous ne pouvez pas procéder à une écriture partielle car l’appareil physique est la source de référence des propriétés de l’appareil. Une lecture superficielle consiste simplement dans la lecture de la propriété à partir de la représentation d’appareil :

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].Value.ToString();
```

Pour déterminer l’actualisation de ces valeurs, vous pouvez vérifier la dernière heure de mise à jour :

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].LastUpdatedTime.ToString();
```

De la même manière, vous pouvez lire les propriétés du service, qui sont stockées seulement dans la représentation d’appareil. Elles ne sont pas synchronisées sur l’appareil.

### Lecture approfondie

Une lecture approfondie démarre un travail d’appareil pour lire la valeur de la propriété demandée à partir de l’appareil physique. Les travaux d’appareil ont été présentés dans [Vue d’ensemble de la gestion des appareils Azure IoT][lnk-dm-overview] et sont décrits en détail dans [Didacticiel : Utilisation de travaux d’appareils pour mettre à jour le microprogramme des appareils][lnk-dm-jobs]. La lecture approfondie vous fournit une valeur plus actualisée de la propriété de l’appareil, car l’actualisation n’est pas limitée par l’intervalle de notification. Le travail envoie un message à l’appareil physique et met à jour la représentation d’appareil avec la valeur la plus récente pour la propriété spécifiée seulement. Elle n’actualise pas l’ensemble de la représentation d’appareil.

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyReadAsync(Guid.NewGuid().ToString(), deviceId, propertyToRead);
```

Vous ne pouvez pas effectuer une lecture approfondie des propriétés ou balises de service car elles ne sont pas synchronisées sur l’appareil.

### Écriture approfondie

Si vous souhaitez modifier une propriété d’appareil accessible en écriture, vous pouvez le faire avec une écriture approfondie qui démarre un travail de l’appareil pour écrire la valeur sur l’appareil physique. Toutes les propriétés de l’appareil ne sont pas accessibles en écriture. Pour obtenir une liste complète, consultez l’annexe A de [Introducing the Azure IoT Hub device management client library][lnk-dm-library] \(Présentation de la bibliothèque cliente de gestion des appareils Azure IoT Hub).

Le travail envoie un message vers l’appareil physique pour mettre à jour la propriété spécifiée. La représentation d’appareil n’est pas immédiatement mise à jour lorsque le travail est terminé. Vous devez attendre jusqu’au prochain intervalle de notification. Lors de la synchronisation, vous pouvez voir la modification dans la représentation d’appareil avec une lecture partielle.

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyWriteAsync(Guid.NewGuid().ToString(), deviceId, propertyToSet, setValue); TODO
```

### Détails d’implémentation d’un simulateur d’appareil

Nous allons voir ce que vous devez faire côté appareil pour implémenter le modèle observer/informer et les lectures/écritures approfondies.

Étant donné que la synchronisation des propriétés de l’appareil est entièrement gérée via la bibliothèque cliente Azure IoT Hub DM, il vous suffit d’appeler l’API pour définir la propriété de l’appareil (niveau de batterie dans cet exemple) à un intervalle régulier. Lorsque le service effectue une lecture approfondie, la dernière valeur définie est renvoyée. Lorsque le service effectue une écriture approfondie, cette méthode set est appelée. Dans **iotdm\_simple\_sample.c**, vous pouvez en voir un exemple :

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

Au lieu d’utiliser la méthode set, vous pouvez implémenter un rappel. Pour plus d’informations sur cette option, consultez la page [Introducing the Azure IoT Hub device management library][lnk-dm-library] \(Présentation de la bibliothèque de gestion des appareils Azure IoT Hub).

## Étapes suivantes

Pour en savoir plus sur les fonctionnalités de la gestion des appareils Azure IoT Hub, vous pouvez parcourir les didacticiels suivants :

- [Recherche de représentations d’appareil physique à l’aide de requêtes][lnk-tutorial-queries]
- [Utilisation de travaux d’appareils pour mettre à jour le microprogramme des appareils][lnk-tutorial-jobs]
- [Activer les appareils gérés derrière une passerelle IoT][lnk-dm-gateway]
- [Présentation de la bibliothèque cliente de gestion des appareils (DM) Azure IoT Hub][lnk-library-c]
- Les bibliothèques clientes de gestion des appareils fournissent un exemple de bout en bout utilisant un [appareil Intel Edison][lnk-edison].

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Conception de votre solution][lnk-design]
- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]
- [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample


[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->