<properties 
	pageTitle="Comment écrire des requêtes dans Stream Analytics | Microsoft Azure" 
	description="Écrire des requêtes dans Stream Analytics et interroger des données | segment du parcours d’apprentissage."
	keywords="comment écrire des requêtes, données de requête, écrire une requête, écriture de requêtes"
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

# Écriture de requêtes dans Stream Analytics

Une requête de logique de traitement de flux écrite dans Azure Stream Analytics est implémentée en tant que « requête active ». Celle-ci est définie avant que la tâche ne démarre et exécutée sur les données au moment où celles-ci atteignent la tâche. La transformation des données est exprimée dans un langage de requête semblable à SQL, qui est principalement un sous-ensemble de T-SQL avec certaines extensions de langage ajoutées, comme [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) qui est utilisé pour exprimer la sémantique temporelle.

## Écriture de requêtes : ##

1. Dans votre tâche Stream Analytics dans le portail de gestion Azure, cliquez sur **Requête**.

    ![Sélection d'une requête](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)

    Dans le portail Azure, cliquez sur **Requête**.

    ![Sélection d’un aperçu de requête](./media/stream-analytics-write-queries/query-preview-portal.png)

2.	Les nouvelles tâches disposent d'un modèle de requête pour vous aider à commencer. Le modèle de requête effectue une requête « pass-through » qui transfère tous les champs provenant d'événements d'entrée vers la sortie.

    - Si vous avez défini au moins une entrée et une sortie pour votre tâche, vous pouvez remplacer les champs « [YourOutputAlias] » et « [YourInputAlias] » d'espace réservé par les alias de l'entrée et de la sortie que vous souhaitez utiliser en premier. En outre, vous pouvez toujours créer et tester votre requête dans le portail Azure Classic sans définir d'entrées et de sorties pour la tâche.
    - Si vous souhaitez effectuer un traitement supérieur à une simple opération « pass-through », vous pouvez modifier la définition de la requête. Pour vous familiariser avec la création de requêtes, examinez les modèles de requête courants illustrés [ici](stream-analytics-stream-analytics-query-patterns.md).
  
    ![Fenêtre Données de requête](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)

## Pour vérifier le bon fonctionnement des données de requête : ##

Vous pouvez vérifier que votre requête se comporte comme prévu en l'exécutant dans le navigateur sur un ou plusieurs fichiers JSON locaux contenant des données de test. Ceci ne démarre pas la tâche et n'a aucune incidence sur la facturation.

> [AZURE.NOTE] Actuellement, le portail Azure ne prend pas en charge le test d’une requête dans le navigateur.

1.	Assurez-vous que la requête ne contient pas d'erreur (sinon, le bouton Test sera désactivé), puis cliquez sur le bouton Test.

    ![Test des données de requête](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)

2.	Vous devrez spécifier des fichiers pour chacune des entrées référencées dans la requête. Dans cet exemple, le modèle de requête est laissé tel quel, donc la boîte de dialogue demande une entrée nommée « yourinputalias ».

    ![Tester les données de requête](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)

3.	Accédez à un fichier de test. Plusieurs exemples de fichiers sont disponibles sur [github] \(données https://github.com/Azure/azure-stream-analytics/tree/master/Sample) et vous pouvez également récupérer des données d’exemple à partir de vos propres entrées de flux de données avec la fonction Exemples de données de l’onglet des entrées.

    ![Entrée de requête](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)

4.	Après avoir fermé la boîte de dialogue, votre requête sera exécutée sur les données de test et les résultats s'afficheront au bas de la page de requête.

    ![Résumé de la requête](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->