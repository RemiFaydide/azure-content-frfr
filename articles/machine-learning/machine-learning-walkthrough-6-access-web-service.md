<properties
	pageTitle="Étape 6 : accéder au service web Machine Learning | Microsoft Azure"
	description="Étape 6 du guide pas à pas du développement d'une solution prédictive : accès à un service web actif Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# Étape de didacticiel pas à pas 6 : accéder au service web Azure Machine Learning

Voici la dernière étape de la procédure pas à pas [Développement d'une solution d'analyse prédictive dans Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.	[Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Accéder au service web**

----------

Dans l'étape précédente de cette procédure pas à pas, nous avons déployé un service web qui utilise notre modèle prédictif de risque de crédit. Désormais, les utilisateurs doivent pouvoir lui envoyer des données et recevoir des résultats.

Il s'agit d'un service web Azure qui peut recevoir et renvoyer des données à l'aide d'API REST de deux manières :

-	**Demande/Réponse** : l'utilisateur envoie une ou plusieurs lignes de crédit au service en utilisant le protocole HTTP et le service répond avec un ou plusieurs jeux de résultats.
-	**Exécution en lots** : l'utilisateur stocke une ou plusieurs lignes de données de crédit dans le stockage d'objets blob Azure, puis envoie l'emplacement du stockage d'objets blob au service. Le service évalue toutes les lignes de données dans l'objet blob d'entrée, enregistre les résultats dans un autre objet blob et renvoie l'URL de ce conteneur.

Le moyen le plus rapide et le plus simple d'accéder au service web consiste à utiliser les modèles d'application web disponibles dans [Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/). Ces modèles d'applications web peuvent générer une application Web personnalisée qui connaît les données d'entrée et les résultats attendus de votre service Web. Il vous suffit de donner à l'application Web l'accès à votre service Web et aux données associées, et le modèle fait le reste.

Pour plus d'informations sur les modèles d'applications web, consultez [Utilisation d'un service Web Microsoft Azure Machine Learning à l'aide d'un modèle d'application Web](machine-learning-consume-web-service-with-web-app-template.md).

Vous pouvez également développer une application personnalisée pour accéder au service web à l'aide du code de démarrage fourni dans R, C# et les langages de programmation Python. Vous trouverez des détails complets dans [Utilisation d'un service web Microsoft Azure Machine Learning publié à partir d'une expérience Machine Learning](machine-learning-consume-web-services.md).

<!---HONumber=AcomDC_0921_2016-->