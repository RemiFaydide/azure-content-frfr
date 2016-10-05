<properties
	pageTitle="Visualisation et dépannage de travaux Stream Analytics | Microsoft Azure"
	description="Découvrez comment visualiser un pipeline de tâches Stream Analytics pour le dépannage en libre-service à l’aide de la fonctionnalité de diagramme de diagnostic."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"
/>


# Visualisation et dépannage de travaux Stream Analytics

Dans Stream Analytics, comme dans d’autres technologies de cloud, la résolution des problèmes implique parfois d’étudier la raison pour laquelle une tâche ne produit pas la sortie attendue (ou même aucune sortie). Dans cet esprit, Stream Analytics permet de visualiser une tâche de streaming. Il est également utile en tant qu’outil de modélisation pour aider les personnes ayant besoin de documenter tout leur travail.

Dans le volet de visualisation, les entrées sont visibles, ainsi que la requête en cours d’exécution et toutes les sorties configurées. Les problèmes liés à la connectivité ou à la configuration peuvent alors être plus évidents, et il peut également être utile d’afficher une représentation visuelle de votre configuration.

## Utilisation de l’outil de diagramme de diagnostic

Pour accéder à ce visualiseur, cliquez simplement sur le bouton « Diagramme de diagnostic » dans le panneau « Paramètres » de la de la tâche Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Chaque entrée et sortie ont un code de couleur pour indiquer l’état actuel de ce composant, comme indiqué ci-dessous.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Lorsque l’utilisateur souhaite consulter les étapes intermédiaires de sa requête pour comprendre les modèles de flux de données au sein d’une tâche, l’outil de visualisation fournit une vue de la répartition des étapes de la requête et de la séquence de flux. Cliquez sur chaque étape de requête pour afficher la section correspondante dans un volet d’édition de requête, comme illustré.

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->