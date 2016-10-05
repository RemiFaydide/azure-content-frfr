<properties
	pageTitle="Analyse du sentiment à l'aide d'Azure Stream Analytics et Azure Machine Learning | Microsoft Azure"
	description="Comment utiliser une fonction définie par l’utilisateur et Machine Learning dans un travail Stream Analytics"
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
	ms.date="08/08/2016" 
	ms.author="jeffstok"
/>

# Analyse du sentiment à l'aide d'Azure Stream Analytics et Azure Machine Learning #

Cet article est conçu pour vous aider à configurer rapidement un travail Azure Stream Analytics simple avec l’intégration d’Azure Machine Learning. Nous mettrons à profit un modèle d’analyse de sentiment Machine Learning de la galerie Cortana Intelligence pour analyser la diffusion des données de texte et déterminer l’évaluation du sentiment en temps réel. Les informations de cet article peuvent vous aider à comprendre les scénarios tels que l’analyse de sentiments en temps réel de la diffusion en continu des données Twitter, l’analyse des enregistrements de conversation client grâce au personnel du support technique, l’évaluation des commentaires sur les forums, blogs et vidéos, sans oublier les nombreux autres scénarios d’évaluation prédictive en temps réel.

Cet article propose un exemple de fichier CSV avec le texte comme entrée dans le stockage d’objets Blob Azure, illustré dans l’image suivante. La tâche applique le modèle d’analyse de sentiments en tant que fonction définie par l’utilisateur (UDF) sur l’exemple de données de texte à partir du magasin d’objets blob. Le résultat final est placé dans le même magasin d’objets blob, dans un autre fichier CSV.

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

L’image suivante illustre cette configuration. Pour un scénario plus réaliste, vous pouvez remplacer le stockage d’objets blob par la diffusion de données Twitter en continu à partir d’une entrée Event Hub Azure. Vous pouvez également créer une visualisation [Microsoft Power BI](https://powerbi.microsoft.com/) en temps réel agrégée du sentiment.

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## Composants requis

Voici la configuration requise pour exécuter les tâches présentées dans cet article :

-	Un abonnement Azure actif.
-	Un fichier CSV contenant des données. Vous pouvez télécharger le fichier indiqué dans la Figure 1 à partir de [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv), ou créer votre propre fichier. Pour cet article, nous partons du principe que vous utilisez celui proposé en téléchargement sur GitHub.

À un niveau élevé, pour effectuer les tâches décrites dans cet article, vous devez procédez comme suit :

1.	Téléchargez un fichier d’entrée CSV vers le stockage d’objets blob Azure.
2.	Ajoutez un modèle d’analyse de sentiments à partir de la galerie Cortana Intelligence à votre espace de travail Azure Machine Learning.
3.	Déployez ce modèle en tant que service web dans l’espace de travail Machine Learning.
4.	Créez un travail Stream Analytics qui appelle ce service web en tant que fonction afin de déterminer le sentiment de saisie de texte.
5.	Démarrez le travail Stream Analytics et examen du résultat.

## Téléchargement d’un fichier d’entrée CSV vers le stockage d’objets blob

Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, comme celui disponible sur GitHub. Pour télécharger le fichier, vous pouvez utiliser l[’Explorateur du stockage Azure](http://storageexplorer.com/), Visual Studio ou un code personnalisé. Nous utilisons des exemples basés sur Visual Studio.

1.	Dans Visual Studio, cliquez sur **Azure** > **Stockage** > **Attacher un stockage externe**. Entrez un **nom de compte** et une **clé de compte**.

    ![Stream Analytics Machine Learning, Server Explorer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Développez le stockage que vous venez d’attacher à l’étape 1, cliquez sur **Créer un conteneur d’objets blob**, puis entrez un nom logique. Après avoir créé le conteneur, ouvrez-le pour afficher son contenu. (Il sera vide à ce stade).

    ![Stream Analytics Machine Learning, créer un objet blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Pour charger le fichier CSV, cliquez sur **Charger un objet blob**, puis choisissez un **fichier sur le disque local**.

## Ajout du modèle d’analyse de sentiments de la galerie Cortana Intelligence

1.	Téléchargez le [modèle d’analyse prédictive de sentiment ](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) à partir de la galerie Cortana Intelligence.
2.	Dans Machine Learning Studio, cliquez sur **Ouvrir dans Studio**.

    ![Stream Analytics Machine Learning, ouvrir Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Connectez-vous pour accéder à l’espace de travail. Sélectionnez le site qui convient le mieux à votre site.
4.	Cliquez sur **Exécuter** en bas de la page.
5.	Une fois le processus correctement exécuté, cliquez sur **Déployer le service web**.
6.	Le modèle d’analyse de sentiments est prêt à être utilisé. Pour valider, cliquez sur le bouton **Test** et saisissez un texte d’entrée comme « J’aime Microsoft ». Le test doit retourner un résultat similaire à ce qui suit :

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Stream Analytics Machine Learning, données d’analyse](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Dans la colonne **Apps**, cliquez sur le lien pour le classer **Excel 2010 ou version antérieure** afin d’obtenir votre clé API et l’URL dont vous aurez besoin plus tard pour configurer le travail Stream Analytics. (Cette étape est obligatoire uniquement pour utiliser un modèle Machine Learning à partir d’un autre espace de travail de compte Azure. Cet article suppose que c’est le cas pour les besoins de ce scénario.)

Notez l’URL et la clé d’accès du service web depuis le fichier Excel téléchargé comme indiqué ci-dessous :

![Stream Analytics Machine Learning, aperçu rapide](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Créer une tâche d’analyse Stream Analytics qui utilise le modèle Machine Learning

1.	Accédez au [portail Azure](https://manage.windowsazure.com).
2.	Cliquez sur **Nouveau** > **Data Services** > **Stream Analytics** > **Création rapide**. Entrez le nom de votre travail dans **Nom de la tâche**, entrez la région appropriée pour la tâche dans **Région**, puis sélectionnez le compte dans **Compte de stockage de surveillance régional**.
3.	Une fois le travail créé, dans l’onglet **Entrées** cliquez sur **Ajouter une entrée**.

    ![Stream Analytics Machine Learning, ajouter une saisie Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Sur la première page de l’assistant **Ajouter une entrée**, cliquez sur **Flux de données**, puis sur **Suivant**. Sur la page suivante, sélectionnez **Stockage d’objets blob** comme entrée, puis cliquez sur **Suivant**.
5.	Sur la page **Paramètres du stockage d’objets blob** de l’assistant, fournissez le nom du conteneur d’objets blob du compte de stockage défini précédemment, au moment du téléchargement des données. Cliquez sur **Next**. Pour **Format de sérialisation de l’événement**, cliquez sur **CSV**. Acceptez les valeurs par défaut pour le reste de la page **configuration de la sérialisation**. Cliquez sur **OK**.
6.	Dans l’onglet **Sorties**, cliquez sur **Ajouter une sortie**.

    ![Stream Analytics Machine Learning, ajouter une sortie](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Cliquez sur **Stockage d’objets blob**, puis entrez les mêmes paramètres, sauf pour le conteneur. La valeur **Input** a été configurée pour assurer la lecture à partir du conteneur nommé « test » dans lequel le fichier **CSV** a été téléchargé. Pour **Output**, entrez « testouput ». Les noms de conteneurs doivent être différents. Vérifiez l’existence de ce conteneur.
8.	Cliquez sur **Suivant** pour configurer les **paramètres de sérialisation** de sortie. Comme pour **Entrée**, cliquez sur **CSV** puis sur le bouton **OK**.
9.	Dans l’onglet **Fonctions**, cliquez sur **Ajouter une fonction Machine Learning**.

    ![Stream Analytics Machine Learning, ajouter une fonction Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Sur la page **Paramètres du Service Web Machine Learning**, recherchez l’espace de travail de Machine Learning, le service web et le point de terminaison par défaut. Pour cet article, appliquez les paramètres manuellement pour vous familiariser avec la configuration du service web d’un espace de travail quelconque dont vous connaissez l’URL et avez la clé API. Entrez le point de terminaison **URL** et **Clé API**. Cliquez sur **OK**.

    ![Stream Analytics Machine Learning, service web Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Dans l’onglet **Requête**, modifiez la requête comme suit :

 ```
 	WITH subquery AS (  
 		SELECT text, sentiment(text) as result from input  
  	)  
 
 	Select text, result.[Scored Labels]  
 	Into output  
 	From subquery  
 ```    
12.	Cliquez sur **Enregistrer** pour enregistrer la feuille de calcul.

## Démarrage du travail Stream Analytics et observation du résultat

1.	Cliquez sur **Démarrer** au bas de la page du travail.
2.	Dans la **boîte de dialogue Démarrer la requête**, cliquez sur **Heure personnalisée** puis sélectionnez une heure antérieure au moment où le fichier CSV a été chargé sur le stockage d’objets blob. Cliquez sur **OK**.

    ![Stream Analytics Machine Learning, heure personnalisée](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Accédez au stockage d’objets blob à l’aide de l’outil que vous avez utilisé pour charger le fichier CSV, par exemple Visual Studio.
4.	Quelques minutes après le début du travail, le conteneur de sortie est créé et un fichier CSV téléchargé dans celui-ci.
5.	Ouvrez le fichier dans l’éditeur CSV par défaut. Un écran semblable à celui-ci devrait s’afficher :

    ![Stream Analytics Machine Learning, vue CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusion

Cet article explique comment créer une tâche Stream Analytics qui lit en continu des données texte et applique en temps réel une analyse des sentiments aux données. Vous pouvez réaliser toutes ces opérations sans avoir à vous soucier des subtilités de la création d’un modèle d’analyse des sentiments. Il s’agit de l’un des avantages de la suite Cortana Intelligence.

Vous pouvez également afficher les mesures liées à la fonction Azure Machine Learning. Pour cela, cliquez sur l’onglet **Surveiller**. Trois fonctions liées aux métriques sont affichées.

- **Requêtes de fonction** indique le nombre de requêtes adressées au service web Machine Learning.
- **Événements de fonction** indique le nombre d’événements dans la requête. Par défaut, chaque requête envoyée à un service web Machine Learning contient jusqu'à 1 000 événements.
- **Échec de requêtes de fonction** indique le nombre de requêtes envoyées vers le service web Machine Learning ayant échoué.

    ![Stream Analytics Machine Learning, affichage du moniteur Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0921_2016-->