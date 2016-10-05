<properties
 pageTitle="Solutions préconfigurées IoT Azure | Microsoft Azure"
 description="Description des solutions préconfigurées IoT Azure et de leur architecture avec des liens vers des ressources supplémentaires."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# Que sont les solutions préconfigurées Azure IoT Suite ?

Les solutions Azure IoT Suite préconfigurées sont des implémentations de modèles de solution IoT courants que vous pouvez déployer sur Azure en utilisant votre abonnement. Vous pouvez utiliser les solutions préconfigurées :

- Comme point de départ de vos propres solutions IoT.
- Pour en savoir plus sur les modèles courants en matière de développement et de conception d’une solution IoT.

Chaque solution préconfigurée est une implémentation complète de bout en bout qui utilise les appareils simulés pour générer la télémétrie.

En plus de déployer et d’exécuter les solutions dans Azure, vous pouvez télécharger le code source complet, puis personnaliser et étendre la solution pour répondre à vos besoins IoT spécifiques.

> [AZURE.NOTE] Pour déployer l’une des solutions préconfigurées, visitez [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L’article [Prise en main des solutions préconfigurées IoT][lnk-getstarted-preconfigured] fournit plus d’informations pour vous expliquer comment déployer et exécuter l’une des solutions.

Le tableau suivant montre le mappage entre les solutions et des fonctionnalités IoT spécifiques :

| Solution | Ingestion de données | Identité d’appareil | Commande et contrôle | Règles et actions | Analyse prédictive |
|------------------------|-----|-----|-----|-----|-----|
| [Surveillance à distance][lnk-getstarted-preconfigured] | Oui | Oui | Oui | Oui | - |
| [Maintenance prédictive][lnk-predictive-maintenance] | Oui | Oui | Oui | Oui | Oui |

- *Données* : entrer des données à l'échelle dans le cloud.
- *Identité d'appareil* : gérer les identités uniques de chaque appareil connecté.
- *Commande et contrôle* : envoyer des messages à un appareil à partir du cloud pour lui demander de prendre des mesures.
- *Règles et actions* : le serveur principal utilise des règles pour agir sur des données d’appareil spécifiques vers le cloud.
- *Analyse prédictive* : le serveur principal applique des analyse de données d’appareil vers le cloud afin de prédire le moment où des actions spécifiques devraient se produire. Par exemple, l’analyse de la télémétrie d’un moteur d'avion permet de déterminer le moment où la maintenance doit avoir lieu.

## Présentation de la solution préconfigurée de surveillance à distance

Nous avons choisi d’aborder dans cet article la solution préconfigurée de surveillance à distance, car elle illustre de nombreux éléments de conception communs que partagent les autres solutions.

Le schéma suivant illustre les éléments clés de la solution de surveillance à distance. Les sections ci-dessous fournissent des informations supplémentaires sur ces éléments.

![Architecture de la solution préconfigurée Surveillance à distance][img-remote-monitoring-arch]

## Appareils

Lorsque vous déployez la solution préconfigurée de surveillance à distance, quatre appareils sont préconfigurés dans la solution pour simuler un appareil de refroidissement. Ces appareils de simulation intègrent un modèle de température et d’humidité qui génère des données de télémétrie. Ces appareils simulés sont inclus pour illustrer le flux de données de bout en bout à travers toute la solution. Ils permettent également de fournir une source pratique de télémétrie et une cible pour les commandes si vous êtes un développeur de serveur Back-end, en utilisant la solution comme point de départ pour une implémentation personnalisée.

Lorsqu’un appareil se connecte pour la première fois à IoT Hub dans la solution préconfigurée de surveillance à distance, le message d’informations sur l’appareil envoyé à IoT Hub énumère la liste des commandes auxquelles l’appareil peut répondre. La solution préconfigurée de surveillance à distance prend en charge les commandes suivantes :

- *Effectuer un test Ping* : l’appareil répond à cette commande par un accusé de réception. Cela est utile pour vérifier que l’appareil est toujours actif et en mode écoute.
- *Démarrer la télémétrie* : demande à l’appareil de démarrer l’envoi des données de télémétrie.
- *Démarrer la télémétrie* : demande à l’appareil d’arrêter l’envoi des données de télémétrie.
- *Modifier la température nominale* : contrôle les valeurs de télémétrie reflétant la température simulée envoyées par l’appareil. Cette commande est utile dans le cadre de tests de logique Back-end.
- *Diagnostiquer la télémétrie* : détermine si l’appareil doit envoyer les données de température externe sous la forme de données de télémétrie.
- *Modifier l’état de l’appareil* : définit la propriété des métadonnées d’état de l’appareil renvoyée par l’appareil. Cette commande est utile dans le cadre de tests de logique Back-end.

Vous pouvez ajouter à la solution d’autres appareils de simulation qui génèrent les mêmes données de télémétrie et répondent aux mêmes commandes.

## IoT Hub

Dans cette solution préconfigurée, l’instance IoT Hub correspond à une *passerelle cloud* dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

Un IoT Hub reçoit la télémétrie provenant des appareils à un seul système d’extrémité. Un IoT Hub gère également les points de terminaison spécifiques des appareils où chaque appareil peut récupérer les commandes qui lui sont envoyées.

L’IoT Hub met les données de télémétrie qu’il reçoit à disposition via un système d’extrémité de lecture de télémétrie côté service.

## Azure Stream Analytics

La solution préconfigurée utilise trois tâches [Azure Stream Analytics][lnk-asa] \(ASA) pour filtrer le flux de télémétrie en provenance des appareils :


- *Tâche DeviceInfo* : génère des données vers un hub d'événements qui envoie des messages relatifs à l’instruction de l’inscription de l’appareil, envoyés lorsqu'un périphérique se connecte d'abord ou en réponse à une commande **Modifier l'état de l'appareil**, dans le registre d’appareils de la solution (une base de données DocumentDB).
- *Tâche Telemetry* : envoie toutes les données de télémétrie brutes vers Azure Blob Storage pour un stockage à froid et calcule les données de télémétrie agrégées qui s’affichent dans le tableau de bord de la solution.
- *Tâche Rules* : filtre le flux de télémétrie sur les valeurs qui dépassent les seuils de la règle et envoie les données vers un hub d’événements. Lorsqu’une règle se déclenche, la vue du tableau de bord du portail de la solution affiche cet événement sous la forme d’une nouvelle ligne dans la table d’historique des alarmes et déclenche une action compte tenu des paramètres définis dans les vues Règles et Actions du portail de la solution.

Dans cette solution préconfigurée, les tâches ASA font partie du **serveur principal de solution IoT** dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

## Processeur d’événements

Dans cette solution préconfigurée, le processeur d’événements fait partie du **serveur principal de solution IoT** dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

Les tâches ASA **DeviceInfo** et **Rules** envoient leur sortie vers des Event hubs pour une transmission à d'autres services principaux. La solution utilise une instance [EventPocessorHost][lnk-event-processor], qui s’exécute dans une [tâche web][lnk-web-job], pour lire les messages à partir de ces Event hubs. L’instance **EventProcessorHost** utilise les données **DeviceInfo** pour mettre à jour les données de l’appareil dans la base de données DocumentDB et utilise les données de **règles** pour appeler l'application logique et mettre à jour l’affichage des alertes dans le portail de la solution.

## Registre d’identité des appareils et DocumentDB

Chaque IoT hub inclut un [registre d’identité des appareils][lnk-identity-registry] qui stocke des clés d’appareils. IoT Hub utilise ces informations pour authentifier les appareils. Un appareil doit être enregistré et disposer d'une clé valide avant de se connecter au concentrateur.

Cette solution stocke des informations supplémentaires sur les appareils, comme leur état, les commandes qu’ils prennent en charge et d'autres métadonnées. La solution utilise une base de données DocumentDB pour stocker ces données d’appareils spécifiques à la solution. Le portail de la solution récupère les données de cette base de données DocumentDB pour pouvoir les afficher et les modifier.

La solution doit également conserver les informations dans le registre d'identité des appareils, synchronisé avec le contenu de la base de données DocumentDB. L’instance **EventProcessorHost** utilise les données de la tâche Stream Analytics **DeviceInfo** pour gérer la synchronisation.

## Portail de la solution

![Tableau de bord de solution][img-dashboard]

Le portail de la solution est une interface utilisateur web qui est déployée dans le cloud dans le cadre de la solution préconfigurée. Il vous permet d’effectuer les opérations suivantes :

- Afficher l’historique de télémétrie et d’alertes dans un tableau de bord.
- Approvisionner de nouveaux appareils.
- Gérer et surveiller des appareils.
- Envoyer des commandes à des appareils spécifiques.
- Gérer les règles et les actions.

Dans cette solution préconfigurée, le portail de la solution fait partie du **serveur principal de solution IoT** et de la **connectivité de traitement et d’entreprise** dans [l’architecture de solution IoT][lnk-what-is-azure-iot] standard.

## Étapes suivantes

Pour plus d’informations sur les architectures de solution IoT, consultez le document [Microsoft Azure IoT services: Reference Architecture][lnk-refarch].

À présent que vous savez en quoi consiste une solution préconfigurée, vous pouvez commencer en déployant la solution préconfigurée de *surveillance à distance* : [Prise en main des solutions préconfigurées][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md

<!---HONumber=AcomDC_0810_2016-->