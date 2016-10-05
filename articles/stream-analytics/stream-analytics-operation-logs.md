<properties 
	pageTitle="Débogage à l'aide des journaux des opérations et de service dans Stream Analytics | Microsoft Azure" 
	description="Comment utiliser les journaux des opérations Stream Analytics" 
	keywords="journaux de service"
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
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

# Débogage des travaux Stream Analytics à l’aide des journaux des opérations et de service

Tous les services Azure fournissent des messages de journalisation opérationnelle aux utilisateurs pour enregistrer les détails relatifs aux opérations de gestion. Dans Azure Stream Analytics, ces informations peuvent être utilisées à des fins de débogage, comme les messages sur l'affichage de l'état d'une tâche, la progression de la tâche et l'échec pour suivre la progression d'une tâche au fil du temps, depuis son démarrage, jusqu'à son traitement et sa sortie.

## Rechercher des journaux d’opérations dans le portail de gestion Azure

Les journaux des opérations sont accessibles de deux manières :

- Tableau de bord de la tâche Stream Analytics
- Services de gestion dans le portail Azure Classic

## Tableau de bord de la tâche Stream Analytics

Un lien vers les journaux correspondants d'une tâche Stream Analytics s'affiche dans l'onglet du Tableau de bord de la tâche. Si vous cliquez sur ce lien, il définit les filtres de manière à afficher les derniers journaux pour cette tâche.

  ![Sélection des journaux des services de gestion](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## Services de gestion

Pour accéder manuellement aux journaux des opérations pour Stream Analytics et d’autres services dans le portail Azure Classic :

1.	Cliquez sur **Services de gestion** dans le [portail Azure Classic](https://manage.windowsazure.com).
2.	Sélectionnez **Stream Analytics** pour **Type** et le nom de la tâche pour **Nom du service**.

  ![Sélection de Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## Rechercher des journaux d’audit dans le portail Azure ##

Pour rechercher des journaux d’opérations pour votre tâche Stream Analytics dans le portail Azure, cliquez sur **Parcourir**, puis sélectionnez **Journaux d’audit**.

  ![Sélection Stream Analytics dans le portail Azure](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)

Cette action ouvre un panneau répertoriant les événements survenus au cours des sept derniers jours pour toutes les ressources de votre abonnement. Vous pouvez filtrer ces informations pour afficher les événements d’un type ou d’un intervalle de temps spécifique en cliquant sur la commande **Filtrer**.

  ![Sélection Stream Analytics dans le portail Azure](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)

## Obtenir les détails d’un journal

Vous pouvez filtrer par Période et État pour afficher les journaux pour votre tâche.

Dans le portail de gestion Azure, cliquez sur le bouton **Détails** au bas de la fenêtre pour afficher plus de détails sur un événement sélectionné.

  ![Sélection des détails](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

Dans le portail Azure, cliquez sur une entrée de journal pour afficher le détail des événements.

  ![Sélection des détails dans le portail Azure](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)

À partir de là, vous pouvez ouvrir le panneau **Détail** en cliquant sur l’événement.

  ![Sélection des détails dans le portail Azure](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)

## Débogage d’une tâche ayant échoué

Dans le portail de gestion Azure, cliquez sur l’icône Rechercher et tapez « échoué ». Vous obtenez comme résultat tous les journaux avec des erreurs.

  ![Débogage d'une tâche ayant échoué](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

Dans le portail Azure, vous pouvez filtrer par niveau de message pour afficher les événements **Critiques**.

  ![Débogage du portail Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)

Vous pouvez sélectionner l’une des erreurs et cliquer sur **Détails** pour obtenir plus d’informations sur l’erreur. Certains messages d'erreur fournissent également des informations sur la façon d'atténuer le problème.

  ![Détails de l'opération](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

Si vous devez contacter le [support technique](https://azure.microsoft.com/support/options/) ou fournir des informations à l’équipe par le biais du [forum MSDN](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics), notez les détails de l’opération, en particulier l’**ID de corrélation**.

## Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->