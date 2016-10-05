<properties 
	pageTitle="Présentation de la surveillance des tâches Stream Analytics | Microsoft Azure" 
	description="Présentation de la surveillance des tâches Stream Analytics" 
	keywords="surveillance des requêtes"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/19/2016" 
	ms.author="jeffstok"/>

# Présentation de la surveillance des tâches Stream Analytics et des requêtes

## Introduction : page de surveillance

Le portail de gestion Azure et le portail Azure affichent les mesures de performances clés qui peuvent servir à surveiller et résoudre les problèmes affectant les performances de vos requêtes et de vos tâches.

Dans le portail de gestion Azure, cliquez sur l’onglet **Surveiller** d’une tâche Stream Analytics en cours d’exécution pour afficher ces mesures. Il existe un délai de 1 minute maximum dans les mesures de performances s’affichant sur la page de surveillance.

  ![Tableau de bord de surveillance des tâches](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)

Dans le portail Azure, accédez à la tâche Stream Analytics dont vous souhaitez consulter les mesures et affichez la section **Surveillance**.

  ![Tableau de bord de surveillance des tâches dans le portail Azure](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)

Lors de la première création d’une tâche Stream Analytics dans une région, vous devez configurer les Diagnostics pour cette région. Pour ce faire, cliquez n’importe où dans la section **Surveillance** et le panneau **Diagnostics** s’affiche. Ici, vous pouvez activer les diagnostics et spécifier un compte de stockage pour les données d’analyse.

  ![Configurer les diagnostics des requêtes dans le portail Azure](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)

## Mesures disponibles pour Stream Analytics


| Mesure | Définition |
|--------|-------------|
| Utilisation de % d’unités de diffusion | Utilisation des unités de diffusion affectées à une tâche à partir de l’onglet Mettre à l’échelle de la tâche. Si cet indicateur atteint 80 % ou plus, il est fort probable que le traitement des événements soit retardé ou arrêté. |
| Événements d’entrée | Quantité de données reçues par le travail Stream Analytics, en nombre d’événements. Cela permet de valider que les événements sont envoyés à la source d’entrée. |
| Événements de sortie | Quantité de données envoyées par le travail Stream Analytics à la cible de sortie, en nombre d’événements. |
| Événements non ordonnés | Nombre d’événements reçus dans le désordre qui ont été supprimés ou dont l’horodatage a été réglé, en fonction de la stratégie de classement des événements. Cela peut être affecté par la configuration du paramètre de la plage de tolérance pour les événements en désordre. |
| Erreurs de conversion de données | Nombre d’erreurs de conversion de données générées par une tâche Stream Analytics. |
| Erreurs d’exécution | Nombre d’erreurs qui se produisent pendant l’exécution d’une tâche Stream Analytics. |
| Événements d’entrée tardifs | Nombre d’événements qui arrivent en retard de la source qui ont été supprimés ou dont l’horodatage a été réglé, en fonction de la configuration de la stratégie de classement des événements du paramètre de la plage de tolérance d’arrivée tardive. |

## Personnalisation de la surveillance dans le portail de gestion Azure ##

6 mesures maximum peuvent être affichées sur un graphique

Pour passer de l'affichage des valeurs relatives (valeur finale uniquement pour chaque mesure) à l'affichage des valeurs absolues (Axe Y affiché), sélectionnez Relative ou Absolute en haut du graphique.

  ![Valeurs relatives ou absolues lors de la surveillance des requêtes](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)

Les mesures peuvent être affichées dans le graphique de surveillance en agrégations de 1 heure, 12 heures, 24 heures ou 7 jours.

Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez 1 heure, 24 heures ou 7 jours en haut du graphique.

  ![Échelle de temps de surveillance des requêtes](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)

Vous pouvez définir des règles qui vous informent par e-mail lorsque la tâche dépasse un seuil défini.

## Personnalisation de la surveillance dans le portail Azure ##

Vous pouvez régler le type de graphique, les mesures affichées et la période dans les paramètres Modifier le graphique. Pour plus d’informations, consultez [Personnalisation de la surveillance](../azure-portal/insights-how-to-customize-monitoring.md).

  ![Échelle de temps de surveillance des requêtes dans le portail Azure](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)

## Statut de tâche

Le statut des tâches Stream Analytics peut être affiché dans le portail Azure Classic, où une liste des tâches est disponible. Vous pouvez voir la liste des tâches en cliquant sur l’icône Stream Analytics dans le portail Azure Classic.

| État | Définition |
|--------|------------|
| Date de création | Une tâche a été créée sans être démarrée. |
| Starting | Un utilisateur a cliqué sur Démarrer la tâche et celle-ci est en cours de démarrage |
| Exécution | La tâche est allouée, l’entrée est en cours de traitement ou en attente. Si le travail est bloqué à l’état En cours d’exécution sans produire de résultat, soit la fenêtre temporelle de traitement de données est grande, soit la logique de la requête est compliquée. Une autre raison peut être qu’il n’y a pas de données envoyées actuellement au travail. |
| En cours d’arrêt | Un utilisateur a cliqué sur Arrêter la tâche et celle-ci est en cours d’arrêt. |
| Arrêté | La tâche a été arrêtée. |
| Détérioré | Cet état indique qu’une tâche Stream Analytics rencontre des erreurs passagères (p. ex. erreurs d’entrée/sortie, erreurs de traitement, erreurs de conversion, etc.). La tâche est encore en cours d’exécution, cependant, de nombreuses erreurs sont générées. Cette tâche requiert l’attention du client et ce dernier peut consulter les journaux des opérations pour connaître les erreurs. |
| Échec | Cela indique que la tâche a échoué en raison d’erreurs et que le traitement s’est arrêté. Le client doit consulter les journaux des opérations pour déboguer les erreurs. |
| En cours de suppression | Cela indique que la tâche est en cours de suppression. |

## Diagnostic

Dans le portail de gestion Azure, le tableau de bord de la tâche fournit des informations sur les emplacements à consulter pour le diagnostic, c’est-à-dire les entrées, les sorties et/ou le journal des opérations. Vous pouvez cliquer sur le lien pour accéder à l’emplacement approprié afin de consulter le diagnostic.

  ![Erreur de surveillance des requêtes](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)

Le fait de cliquer sur la ressource d’entrée ou de sortie fournit des informations de diagnostic détaillées. Celles-ci sont actualisées avec les dernières informations de diagnostic pendant que la tâche est en cours d’exécution.

  ![Diagnostics des requêtes](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->