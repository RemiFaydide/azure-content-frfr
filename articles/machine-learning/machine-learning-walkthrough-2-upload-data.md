<properties
	pageTitle="Étape 2 : télécharger des données dans une expérience Machine Learning | Microsoft Azure"
	description="Étape 2 du guide pas à pas du développement d'une solution prédictive : téléchargement de données publiques stockées dans Azure Machine Learning Studio."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016" 
	ms.author="garye"/>


# Étape de didacticiel pas à pas 2 : téléchargement de données existantes dans une expérience Azure Machine Learning

Voici la deuxième étape de la procédure pas à pas [Développement d'une solution d'analyse prédictive dans Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	**Télécharger des données existantes**
3.	[Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Pour développer un modèle prédictif pour un risque de crédit, nous avons besoin de données que nous pouvons utiliser pour former et ensuite tester le modèle. Pour cette procédure pas à pas, nous allons utiliser le modèle « UCI Statlog (German Credit Data) Data Set » qui se trouve dans le référentiel de UCI Machine Learning. Vous pouvez le trouver ici : <a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Nous allons utiliser le fichier nommé **german.data**. Téléchargez ce fichier sur votre disque dur.

Ce jeu de données contient des lignes de 20 variables pour 1 000 candidats à un crédit. Ces 20 variables représentent le vecteur de fonctionnalité du jeu de données, qui fournit des caractéristiques permettant d'identifier chaque candidat à un crédit. Une colonne supplémentaire pour chaque ligne représente le risque de crédit calculé de chaque candidat : 700 candidats constituent un faible risque de crédit et 300 un risque élevé.

Le site web UCI fournit une description des attributs du vecteur de fonctionnalité incluant des informations financières, un historique de crédit, un statut professionnel et des informations personnelles. Une évaluation binaire a été appliquée à chaque candidat, afin d'indiquer s'il constitue un risque de crédit faible ou élevé.

Nous allons utiliser ces données pour former un modèle d'analyse prédictive. Lorsque nous aurons terminé, notre modèle pourra saisir des informations concernant de nouveaux candidats et prévoir s'ils constituent un risque de crédit faible ou élevé.

Voici une évolution intéressante. La description du jeu de données précise que classer par erreur une personne comme risque de crédit faible alors qu'elle constitue un risque de crédit élevé est 5 fois plus coûteux pour l'institution financière que commettre l'erreur inverse. Pour intégrer facilement cet avertissement dans notre expérience, il suffit de multiplier par 5 la valeur des entrées représentant une personne constituant un risque de crédit élevé. Ensuite, si le modèle classe par erreur une personne dans la catégorie des risques de crédit faibles, cette erreur sera commise 5 fois, une fois pour chaque doublon. Cela augmentera le coût de cette erreur dans les résultats de formation.

##Conversion du format du jeu de données
Le jeu de données d'origine utilise un format séparé par des espaces. Machine Learning Studio fonctionne mieux avec un fichier de valeurs séparées par des virgules (CSV). Nous allons donc convertir le jeu de données en remplaçant les espaces par des virgules.

Il existe de nombreux moyens de convertir ces données. L'une des méthodes consiste à utiliser la commande Windows PowerShell suivante :

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

Vous pouvez aussi utiliser la commande Unix sed :

	sed 's/ /,/g' german.data > german.csv  

Dans les deux cas, nous avons créé une version séparée par des virgules des données dans un fichier nommé **german.csv** que nous utiliserons dans notre expérience.

##Télécharger le jeu de données vers Machine Learning Studio

Une fois les données converties au format CSV, nous devons les télécharger vers Machine Learning Studio. Pour en savoir plus sur la prise en main de Machine Learning Studio, consultez la [page d'accueil de Microsoft Azure Machine Learning Studio](https://studio.azureml.net/).

1.	Ouvrez Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). Lorsque vous êtes invité à vous connecter, utilisez le compte que vous avez spécifié comme propriétaire de l'espace de travail.
1.  Cliquez sur l'onglet **Studio** en haut de la fenêtre.
1.	Cliquez sur **+NOUVEAU** en bas de la fenêtre.
1.	Sélectionnez **JEU DE DONNÉES**.
1.	Sélectionnez **DEPUIS UN FICHIER LOCAL**.
1.	Dans la boîte de dialogue **Charger un nouveau jeu de données**, cliquez sur **Parcourir**, puis recherchez le fichier **german.csv** que vous avez créé.
1.	Entrez le nom du jeu de données. Pour les besoins de cette procédure, nous allons l'appeler « Données de carte de crédit allemande UCI ».
1.	Pour le type de données, sélectionnez **Fichier CSV générique sans en-tête (.nh.csv)**.
1.	Ajoutez la description de votre choix.
1.	Cliquez sur **OK**.

![Télécharger le jeu de données][1]


Les données sont téléchargées dans un module de jeu de données utilisable dans une expérience.

> [AZURE.TIP] Pour gérer les jeux de données que vous avez téléchargés dans Studio, cliquez sur l'onglet **JEUX DE DONNÉES** à gauche de la fenêtre de Studio.

Pour plus d'informations sur l'importation de différents types de données dans une expérience, consultez [Importez vos données d'apprentissage dans Azure Machine Learning Studio](machine-learning-data-science-import-data.md).

**Étape suivante : [créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!---HONumber=AcomDC_0921_2016-->