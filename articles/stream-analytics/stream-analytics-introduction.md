<properties 
	pageTitle="Présentation de Stream Analytics | Microsoft Azure" 
	description="Découvrez Stream Analytics, un service géré qui vous permet d’analyser les données de diffusion en continu à partir de l’Internet des objets (IoT) en temps réel." 
	keywords="analyse en tant que service, services gérés, traitement des données de diffusion en continu, analyse de diffusion en continu, qu’est-ce que Stream Analytics"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/26/2016" 
	ms.author="jeffstok"/>


# Qu’est-ce que Stream Analytics ?

Azure Stream Analytics est un moteur de traitement des événements en temps réel entièrement géré et peu coûteux qui permet d’obtenir des informations détaillées à partir des données. Stream Analytics facilite la configuration de calculs d’analyse en temps réel sur les données de diffusion à partir d’appareils, de capteurs, de sites web, de médias sociaux, d’applications, de systèmes d’infrastructure et bien plus encore.

En quelques clics dans le portail Azure, vous pouvez créer une tâche Stream Analytics qui spécifie la source d’entrée des données de diffusion, le récepteur de sortie pour les résultats de votre tâche, et une transformation des données exprimée dans un langage de type SQL. Vous pouvez surveiller et ajuster l’échelle/la vitesse de votre tâche dans le portail Azure de quelques kilo-octets à un gigaoctet ou plus d’événements traités par seconde.

Stream Analytics tire parti des années de travail de l’équipe Microsoft Research dans le développement de moteurs de diffusion hautement optimisés pour le traitement urgent, ainsi que dans les intégrations de langages pour de telles spécifications intuitives.

## Dans quels cas puis-je utiliser Stream Analytics ?
De nos jours, d'importantes quantités de données transitent à haute vitesse sur les réseaux câblés. Les organisations qui peuvent traiter et agir sur ces données de diffusion en continu en temps réel peuvent considérablement améliorer leur efficacité et se démarquer de leurs concurrents. Les scénarios d’analyse de diffusion en continu en temps réel concernent tous les secteurs : l’analyse et les alertes personnalisées relatives aux transactions d’actions en temps réel proposées par des sociétés de services financiers ; la détection des fraudes en temps réel ; les services de protection des données et des identités ; la réception et l’analyse fiables des données générées par les capteurs et mécanismes de positionnement incorporés dans des objets physiques (Internet des objets) ; les analyses de flux des clics sur le web ; et les applications de gestion des relations avec les clients (CRM) émettant des alertes lorsque l’expérience client se dégrade pendant un certain temps. Les entreprises recherchent la méthode la plus flexible, fiable et économique pour mener de telles analyses de données de flux d'événements en temps réel pour réussir dans ce monde moderne extrêmement compétitif.

## Avantages et fonctionnalités clés
-   **Simplicité d’utilisation :** Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations. Afin d’optimiser la simplicité d’utilisation, Stream Analytics utilise une variante T-SQL et supprime la nécessité pour les clients de gérer les complexités techniques des systèmes de traitement de flux de données. À l’aide du [langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) dans l’éditeur de requête du navigateur, la fonction de saisie semi-automatique IntelliSense vous aide à implémenter rapidement et facilement des requêtes de série chronologique, comme les jointures temporelles, les agrégats fenêtrés, les filtres temporels, ainsi que d’autres opérations courantes telles que les jointures, les agrégats, les projections et les filtres. En outre, le test de requête dans le navigateur par rapport à un fichier de données d'exemple permet un développement rapide et itératif.

-   **Évolutivité :** Stream Analytics est capable de gérer un débit d’événements élevé allant jusqu’à 1 Go/s. L’intégration à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) permet à la solution de recevoir des millions d’événements par seconde. Ces événements peuvent provenir d’appareils connectés, de flux de clics, de fichiers journaux, etc. Pour ce faire, Stream Analytics exploite la capacité de partitionnement d’Event Hubs, qui peut produire 1 Mo/s par partition. Les utilisateurs sont capables de partitionner le calcul en plusieurs étapes logiques au sein de la définition de requête. Chacune de ces étapes peut elle-même être partitionnée pour accroître l’évolutivité.

-   **Fiabilité, répétition et récupération rapide :** service géré dans le cloud, Stream Analytics aide à empêcher la perte de données et fournit une continuité d’activité en cas de défaillances grâce à des capacités de récupération intégrées. Doté d’une capacité de conservation de l’état en interne, le service peut fournir des résultats reproductibles en assurant la possibilité d’archiver des événements et de recommencer un traitement ultérieurement, tout en obtenant les mêmes résultats. Cette fonctionnalité permet aux clients de revenir en arrière et d’examiner les calculs lors de l’analyse des causes premières, l’analyse de scénarios, etc.

-   **Moindre coût :** le service cloud Stream Analytics est optimisé pour permettre aux utilisateurs d’exécuter et de tenir à jour des solutions d’analyse en temps réel à prix réduit. Le service est conçu pour permettre un paiement à l’utilisation basé sur l’unité de diffusion et la quantité de données traitées par le système. L’utilisation est définie en fonction du volume d’événements traités et de la quantité de puissance de calcul configurée dans le cluster pour gérer les tâches Stream Analytics respectives.

-   **Données de référence :** Stream Analytics permet aux utilisateurs de spécifier et d’utiliser des données de référence. Il peut s’agir de données d’historique ou simplement de données de non-diffusion, qui changent moins souvent au fil du temps. Le système simplifie l'utilisation des données de référence pour les traiter comme n'importe quel autre flux d'événement entrant à joindre avec d'autres flux d'événements reçus en temps réel pour effectuer des transformations.

-   **Fonctions définies par l’utilisateur :** Stream Analytics offre une intégration à Azure Machine Learning pour définir des appels de fonction au sein du service Machine Learning dans le cadre d’une requête Stream Analytics. Cela étend les fonctionnalités de Stream Analytics pour tirer parti des solutions Azure Machine Learning existantes. Pour plus d’informations à ce sujet, consultez le [didacticiel sur l’intégration de Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

-   **Connectivité :** Stream Analytics se connecte directement à Azure Event Hubs et à Azure IoT Hub pour l’ingestion de flux de données, ainsi qu’au service BLOB Azure pour l’ingestion de données d’historique. Les résultats peuvent être écrits de Stream Analytics vers les objets blob ou tables de stockage Azure, le service Base de données SQL Azure, les instances Azure Data Lake Store, DocumentDB, les hubs d’événements Event Hubs, les rubriques ou files d’attente Service Bus Azure et Power BI, où ils peuvent être visualisés, faire l’objet d’un traitement plus poussé par les workflows, être utilisés dans les analyses par lot via [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) ou être traités à nouveau en tant que série d’événements. Lorsque vous utilisez Event Hubs, vous pouvez composer plusieurs Stream Analytics avec d’autres sources de données et moteurs de traitement sans dénaturer la capacité de diffusion des calculs.

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes
Stream Analytics, un service géré d’analyse de diffusion en continu des données à partir de l’Internet des objets vous a été présenté. Pour en savoir plus sur ce service, consultez les rubriques suivantes :

- [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0928_2016-->