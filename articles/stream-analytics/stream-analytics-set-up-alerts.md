<properties 
	pageTitle="Configurer des alertes pour des requêtes dans Stream Analytics | Microsoft Azure" 
	description="Présentation des alertes Stream Analytics" 
	keywords="configurer des alertes"
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


# Configuration d’alertes pour des tâches Azure Stream Analytics

## Introduction : page de surveillance

Vous pouvez configurer des règles pour déclencher une alerte quand une mesure atteint une condition que vous spécifiez.

Par exemple, si les événements de sortie des 15 dernières minutes sont < 100, envoyer une notification par courrier électronique à l’ID de courrier électronique : xyz@company.com.

Les règles peuvent être configurées sur des mesures par le biais du portail ou [par programmation](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sur les données des journaux des opérations.

## Configurer des alertes par le biais du portail Azure Classic

Il existe deux façons de configurer des alertes sur le portail de gestion Azure :

1.	Onglet **Surveillance** de votre tâche Stream Analytics
2.	Le Journal des opérations dans les Services de gestion

## Configurer des alertes par le biais de l’onglet Surveillance de la tâche dans le portail

1.	Sélectionnez la mesure dans l’onglet de surveillance, cliquez sur le bouton **Ajouter une règle** dans la partie inférieure du tableau de bord et configurez vos règles.

    ![Tableau de bord](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Entrez un nom et une description pour l'alerte

    ![Création de la règle](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Entrez les seuils, la fenêtre d'évaluation d'alerte et les actions de l'alerte

    ![Définition des conditions](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Configurer des alertes par le biais des journaux des opérations

1.	Accédez à l’onglet **Alertes** dans les Services de gestion du [portail Azure Classic](https://manage.windowsazure.com).
2.	Cliquez sur **Ajouter une règle**

    ![Critères](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Entrez un nom et une description pour l'alerte. Sélectionnez « Stream Analytics » comme Type de service et le nom de la tâche comme Nom du service.

    ![Définition de l'alerte](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Configurer des alertes dans le portail Azure ##

Dans le portail Azure, accédez à la tâche Stream Analytics pour laquelle vous souhaitez configurer une alerte, puis cliquez sur la section **Surveillance**. Dans le panneau **Métrique** qui s’ouvre, cliquez sur la commande **Ajouter une alerte**.

  ![Configuration du portail Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

Vous pouvez nommer votre règle d'alerte et choisir la description à afficher dans le message électronique de notification.

Quand vous sélectionnez Mesures, vous allez choisir une condition et une valeur de seuil pour la métrique.

  ![Sélection d’une métrique dans le portail Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Pour plus d’informations sur la configuration d’alertes dans le portail Azure, consultez la rubrique [Réception de notifications d’alerte](../azure-portal/insights-receive-alert-notifications.md).

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->