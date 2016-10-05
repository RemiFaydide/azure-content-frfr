<properties 
	pageTitle="Comment démarrer la diffusion en continu de tâches dans Stream Analytics | Microsoft Azure" 
	description="Comment exécuter une tâche de diffusion en continu dans Azure Stream Analytics | segment du parcours d’apprentissage."
    keywords="diffusion en continu de tâches"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

# Comment exécuter une tâche de diffusion en continu dans Azure Stream Analytics

Lorsque l'entrée, la requête et la sortie d'une tâche ont été spécifiées, vous pouvez démarrer la tâche Stream Analytics.

Pour démarrer la tâche :

1.	Dans le portail Azure Classic, dans le tableau de bord de la tâche, cliquez sur **Démarrer** au bas de la page.

    ![Bouton Démarrer la tâche](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    Dans le portail Azure, cliquez sur **Démarrer** en haut de la page de votre tâche.

    ![Bouton Démarrer la tâche du portail Azure](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	Spécifiez une valeur **Démarrer la sortie** pour déterminer quand cette tâche commence à générer une sortie. Le paramètre par défaut pour les tâches qui n’ont jamais été démarrées est **Heure de démarrage de la tâche**, ce qui signifie que la tâche commence immédiatement à traiter les données. Vous pouvez également spécifier une heure **personnalisée** dans le passé (pour l’utilisation des données historiques) ou dans le futur (pour retarder le traitement jusqu’à une date ultérieure). Dans les cas où une tâche a déjà été démarrée, puis arrêtée, l’option **Heure du dernier arrêt** est disponible pour reprendre la tâche à partir de la dernière heure de sortie et éviter toute perte de données.

    ![Heure de démarrage de la tâche de diffusion en continu](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Heure de démarrage de la tâche de diffusion en continu dans le portail Azure](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	Confirmez votre sélection. L’état de la tâche passe à *Démarrage*, puis à *En cours d’exécution* une fois la tâche démarrée. Vous pouvez surveiller la progression de l’opération de **démarrage** dans le **Hub de notification** :

    ![progression de la tâche de diffusion en continu](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Progression de la tâche de diffusion en continu dans le portail Azure](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->