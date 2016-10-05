<properties
	pageTitle="Tableau de bord d’alimentation sur Stream Analytics | Microsoft Azure"
	description="Utilisez un tableau de bord Power BI de diffusion en temps réel pour collecter des informations d’analyse décisionnelle et analyser les données volumineuses à partir d’un travail Stream Analytics."
	keywords="tableau de bord d’analyse, tableau de bord en temps réel"
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

#  Stream Analytics et Power BI : tableau de bord d'analyse en temps réel pour les données de diffusion en continu

Azure Stream Analytics vous permet de tirer parti de Microsoft Power BI, l’un des principaux outils d’analyse décisionnelle. Découvrez comment utiliser Azure Stream Analytics pour analyser les volumes élevés de données de diffusion en continu et obtenir les résultats dans un tableau de bord d’analyse Power BI en temps réel.

Utilisez [Microsoft Power BI](https://powerbi.com/) pour générer rapidement un tableau de bord dynamique. [Visionnez une vidéo illustrant le scénario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Dans cet article, découvrez comment créer vos propres outils d’analyse décisionnelle en utilisant Power BI comme sortie pour vos tâches Azure Stream Analytics comment utiliser un tableau de bord en temps réel.

## Composants requis

* Compte Microsoft Azure.
* Une entrée pour alimenter un travail Stream Analytics en flux de données. Stream Analytics accepte les entrées d'Azure Event Hubs ou d'Azure Blob storage.
* Compte professionnel ou scolaire pour Power BI

## Créer une tâche Azure Stream Analytics

Dans le [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Nouveau, Data Services, Stream Analytics, Création rapide**.

Spécifiez les valeurs suivantes, puis cliquez sur **Créer un travail Stream Analytics** :

* **Nom du travail** - entrez un nom pour le travail. Par exemple, **DeviceTemperatures**.
* **Région** - sélectionnez la région où vous souhaitez exécuter le travail. Envisagez de placer le travail et le hub d’événements dans la même région afin d’améliorer les performances et d’éviter le coût d’un transfert de données entre différentes régions.
* **Compte de stockage** - choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.

Dans le volet gauche, cliquez sur **Stream Analytics** pour afficher une liste des travaux Stream Analytics.

![graphic1][graphic1]

> [AZURE.TIP] Le nouveau travail est affiché avec l’état **Non démarré**. Notez que le bouton **Démarrer** situé en bas de la page est désactivé. Cela est dû au fait qu’avant de pouvoir démarrer le travail, vous devez configurer son entrée, sa sortie, sa requête, etc.

## Spécification d'une entrée de travail

Pour ce didacticiel, nous partons du principe que vous utilisez Event Hub comme entrée avec la sérialisation JSON et l’encodage UTF-8.

* Cliquez sur le nom du travail.
* En haut de la page, cliquez sur **Entrées**, puis sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera le long d’une procédure de configuration de votre entrée.
*	Sélectionnez **Flux de données**, puis cliquez avec le bouton droit.
*	Sélectionnez **Hub d’événements**, puis cliquez avec le bouton droit.
*	Saisissez ou sélectionnez les valeurs suivantes sur la troisième page :
  *	**Alias d’entrée** - entrez un nom convivial pour cette entrée de travail. Notez que vous utiliserez ce nom dans la requête par la suite.
  * **Event Hub** : si l’Event Hub que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, sélectionnez l’espace de noms dans lequel est situé l’Event Hub.
*	Si votre hub d’événements est situé dans un autre abonnement, sélectionnez **Utiliser le hub d’événements à partir d’un autre abonnement** et entrez manuellement l’**espace de noms Service Bus**, le **nom du hub d’événements**, le **nom de la stratégie du hub d’événements**, la **clé de stratégie du hub d’événements** et le **nombre de partitions du hub d’événements**.

> [AZURE.NOTE]	Cet exemple utilise le nombre de partitions par défaut : 16.

* **Nom du hub d’événements** – sélectionnez le nom du hub d’événements Azure que vous avez.
* **Nom de la stratégie du hub d’événements** – sélectionnez la stratégie pour le hub d’événements que vous utilisez. Assurez-vous que cette stratégie a des autorisations de gestion.
*	**Groupe de consommateurs du hub d’événements** – vous pouvez laisser ce champ vide ou spécifier un groupe de consommateurs présent sur votre hub d’événements. Notez que chaque groupe de consommateurs d’un hub d’événements peut avoir seulement 5 lecteurs à la fois. Décidez en conséquence du groupe de consommateurs approprié pour votre travail. Si vous laissez le champ vide, le groupe de consommateurs par défaut est utilisé.

*	Cliquez avec le bouton droit.
*	Spécifiez les valeurs suivantes :
  *	**Format du sérialiseur d’événements** - JSON
  *	**Encodage** - UTF8
*	Cliquez sur le bouton de vérification pour ajouter cette source et vérifier que Stream Analytics peut se connecter au hub d'événements.

## Ajouter une sortie Power BI

1.  En haut de la page, cliquez sur **Sortie**, puis sur**Ajouter une sortie**. Power BI est répertorié comme option de sortie.

    ![graphic2][graphic2]

2.  Sélectionnez **Power BI**, puis cliquez avec le bouton droit.
3.  Un écran de ce type s’affiche :

    ![graphic3][graphic3]

4.  Dans cette étape, vous devez fournir un compte professionnel ou scolaire pour la sortie de la tâche Stream Analytics. Si vous avez déjà un compte Power BI, sélectionnez **Autoriser maintenant**. Dans le cas contraire, choisissez **S’inscrire maintenant**. [Voici un bon blog décrivant les détails de l’inscription à Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]

5.  Ensuite, un écran de ce type s’affiche :

    ![graphic4][graphic4]

Fournissez les valeurs suivantes :

* **Alias de sortie** - Vous pouvez saisir n’importe quel alias de sortie auquel il vous sera facile de vous référer. Cet alias de sortie est particulièrement utile si vous décidez d’avoir des sorties multiples pour votre travail. Dans ce cas, vous devez faire référence à cette sortie dans votre requête. Par exemple, utilisons la valeur d’alias de sortie = « OutPbi ».
* **Nom du jeu de données** - Fournissez un nom de jeu de données que vous souhaitez donner à votre sortie de Power BI. Par exemple, utilisons « pbidemo ».
*	**Nom de la table** - Fournissez un nom de table dans le jeu de données de la sortie de Power BI. Appelons-la par exemple « pbidemo ». Actuellement, la sortie Power BI des travaux Stream Analytics peut avoir seulement une table dans un jeu de données.
*	**Espace de travail** : sélectionnez un espace de travail dans votre client Power BI sous lequel le jeu de données sera créé.

>	[AZURE.NOTE] You should not explicitly create this dataset and table in your Power BI account. They will be automatically created when you start your Stream Analytics job and the job starts pumping output into Power BI. If your job query doesn’t return any results, the dataset and table will not be created.

*	Cliquez sur **OK**, puis **Tester la connexion** . Votre configuration de sortie est terminée.

>	[AZURE.WARNING] Also be aware that if Power BI already had a dataset and table with the same name as the one you provided in this Stream Analytics job, the existing data will be overwritten.


## Écrire une requête

Accédez à l’onglet **Requête** de votre travail. Écrivez la requête dont vous souhaitez obtenir la sortie dans votre Power BI. Par exemple, il peut s’agir de la requête SQL suivante :

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl



Démarrez votre travail. Assurez-vous que votre hub d’événements reçoit des événements et que votre requête génère les résultats attendus. Si votre requête renvoie 0 ligne, le jeu de données et les tables de Power BI ne sont pas créés automatiquement.

## Créer le tableau de bord dans Power BI

Accédez à [Powerbi.com](https://powerbi.com), puis connectez-vous à l’aide de votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, vous voyez que votre jeu de données est déjà créé :

![graphic5][graphic5]

Pour créer le tableau de bord, cliquez sur l’option Tableaux de bord et créez un tableau de bord.

![graphic6][graphic6]

Dans cet exemple, nous l’appellerons « Tableau de bord de démonstration ».

Cliquez maintenant sur le jeu de données créé par votre travail Stream Analytics (pbidemo dans notre exemple). Vous accédez alors à une page pour créer un graphique sur ce jeu de données. Voici un des exemples de rapports que vous pouvez créer :

Sélectionnez les champs Σ temp et heure. Ils vont automatiquement dans Valeur et Axe sur le graphique :

![graphic7][graphic7]

Avec cela, vous obtenez automatiquement un graphique comme celui-ci :

![graphic8][graphic8]

Dans la section valeur, cliquez sur la liste déroulante pour temp et sélectionnez **moyenne** pour la température. Dans le graphique, cliquez sur **visualisation** et choisissez **graphique en courbes** :

![graphic9][graphic9]

Vous obtenez un graphique en courbes de la moyenne dans le temps. En utilisant l’option d’épinglage comme montré ci-dessous, vous pouvez épingler ce rapport au tableau de bord que vous avez créé précédemment :

![graphic10][graphic10]

Lorsque vous affichez le tableau de bord avec ce rapport épinglé, vous constatez que celui-ci est mis à jour en temps réel. Essayez de modifier les données de vos événements (par exemple, le pic de température). L’effet apparaît alors en temps réel sur votre tableau de bord.

Notez que ce didacticiel ne vous a montré la création que d’un seul type de graphique pour un jeu de données. Power BI peut vous aider à créer d’autres outils d’analyse décisionnelle clients pour votre organisation. Pour un autre exemple de tableau de bord Power BI, visionnez la vidéo de [prise en main de Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s).

Pour plus d'informations sur la configuration d'une sortie Power BI et pour utiliser des groupes Power BI, passez en revue la [section Power BI](stream-analytics-define-outputs.md#power-bi) dans [Présentation des sorties Stream Analytics](stream-analytics-define-outputs.md "Présentation des sorties Stream Analytics"). Vous pouvez également consulter la page [Tableaux de bord dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) pour en savoir plus sur la création des tableaux de bord avec Power BI.

## Limites et meilleures pratiques

Power BI utilise des contraintes d'accès concurrentiel et de débit comme indiqué ici : [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Tarification de Power BI")

C’est pour cette raison que Power BI s’applique naturellement dans les cas où l’analyse de flux de données Microsoft Azure débouche sur une réduction significative de la charge des données. Nous vous recommandons d’utiliser TumblingWindow ou HoppingWindow pour garantir que le push de données est au plus de 1 push/seconde et que votre requête respecte les exigences en matière de débit. Vous pouvez utiliser l’équation suivante pour calculer la valeur à donner à votre fenêtre en secondes :

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)

Par exemple, si 1 000 appareils envoient des données chaque seconde, il s’agit de la référence Pro Power BI qui prend en charge 1 000 000 lignes/heure et si vous souhaitez obtenir la moyenne des données par appareil sur Power BI, vous pouvez exécuter un push toutes les 4 secondes par appareil (comme indiqué ci-dessous) :

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)

Cela signifie que nous pouvons modifier la requête d’origine :

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
    	OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,4),
    	dspl

### Actualiser la vue Power BI

Voici une question qui revient fréquemment : « Pourquoi le tableau de bord ne se met pas à jour automatiquement dans Power BI ? ».

Pour y arriver, utilisez Questions et réponses dans Power BI pour poser une question du type : « Valeur maximale par température où l’horodatage est aujourd’hui ». Ensuite, épinglez cette vignette au tableau de bord.

### Renouveler une autorisation

Vous devrez authentifier de nouveau votre compte Power BI si son mot de passe a été modifié depuis la création ou la dernière authentification de votre tâche. Si Multi-Factor Authentication (MFA) est configuré sur votre client Azure Active Directory (AAD), vous devrez également renouveler l’autorisation Power BI toutes les 2 semaines. Un symptôme de ce problème est l’absence de sortie de la tâche et une « erreur d’authentification de l’utilisateur » dans les journaux des opérations :

![graphic12][graphic12]

De même, si une tâche tente de démarrer alors que le jeton a expiré, une erreur se produit et le démarrage de la tâche échoue. L’erreur ressemble à ce qui suit :

![Erreur de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png)
 

Pour résoudre ce problème, arrêtez votre tâche en cours d'exécution et accédez à votre sortie Power BI. Cliquez sur le lien « Renouveler l’autorisation », puis redémarrez votre tâche depuis l’heure du dernier arrêt pour éviter de perdre des données.

![Renouvellement de la validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png)

Une fois que l’autorisation est actualisée avec Power BI, vous voyez une alerte verte dans la zone d’autorisation :

![Renouvellement de la validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png)

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png

<!---HONumber=AcomDC_0921_2016-->