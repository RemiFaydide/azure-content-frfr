<properties 
	pageTitle="Échantillonner des données dans des conteneurs d'objets blob Azure, SQL Server et des tables Hive | Microsoft Azure" 
	description="Comment explorer les données stockées dans différents environnements Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Échantillonner des données dans des conteneurs d'objets blob Azure, SQL Server et des tables Hive

Ce document contient des liens vers des rubriques qui expliquent comment échantillonner les données stockées dans l’un des trois emplacements Azure :

- Les **données de conteneurs d’objets blob Azure** sont échantillonnées par le biais de leur téléchargement par programmation, puis de leur échantillonnage à l’aide d’un exemple de code Python.
- Les **données SQL Server** sont échantillonnées à l’aide de SQL et du langage de programmation Python.
- Les données des **tables Hive** sont échantillonnées à l’aide de requêtes Hive.

Le **menu** ci-dessous pointe vers les rubriques qui expliquent comment échantillonner des données dans chacun de ces environnements de stockage Azure.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Pourquoi échantillonner des données ?

Si vous prévoyez d'analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. Cette opération facilite la compréhension et l’exploration des données, ainsi que la conception de fonctionnalités. Son rôle dans le processus Cortana Analytics consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles d’apprentissage automatique.

<!---HONumber=AcomDC_0921_2016-->