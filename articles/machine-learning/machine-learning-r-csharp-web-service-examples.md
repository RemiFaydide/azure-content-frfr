<properties 
	pageTitle="Exemples de services web Machine Learning développés avec R | Microsoft Azure" 
	description="Recherchez un ensemble pratique d’exemples de services web créés avec du code R et Machine Learning et publié sur Azure Marketplace." 
	keywords="csharp,code r,exemples de services web"
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2016" 
	ms.author="jaymathe"/>


#Des exemples de services web utilisant du code R sur Azure Machine Learning sont publiés sur Microsoft Azure Marketplace

Cet article contient des exemples de services web créés à l’aide d’Azure Machine Learning et publiés sur Azure Marketplace. Chaque exemple de service web est associé à un document complet intégrant des exemples de jeux de données pour tester les services et expliquant comment l’utilisateur peut lui-même créer un service similaire.

Dans Azure Machine Learning Studio, les utilisateurs peuvent écrire du code R et le publier en quelques clics sous la forme d’un service web afin que d’autres applications et périphériques l’utilisent dans le monde entier.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


De la création de simples calculatrices qui fournissent des fonctionnalités de statistiques à la création d’un facteur personnalisé de prédiction d’analyse de sentiment dans l’exploration de textes, les utilisateurs R nouveaux et expérimentés peuvent bénéficier de la facilité avec laquelle les utilisateurs d’Azure Machine Learning exploitent le code R. Bien que ces services web puissent être utilisés par les utilisateurs, via une application mobile ou un site web, l’objectif de ces exemples de services web est également de vous indiquer comment Machine Learning peut exploiter les scripts R à des fins analytiques et peut être utilisé pour créer des services web sur le code R.

Chaque exemple inclut un exemple C# de consommation du service web.


![Diagramme du code R dans Azure Machine Learning : solutions R pour un usage propriétaire ou publiées sur Azure Marketplace.][1]

Examinez les scénarios suivants.

##Scénario 1 : modèle générique 
Un utilisateur travaille avec un modèle générique qui peut être appliqué aux données d'un nouvel utilisateur, par exemple une prévision de données de série temporelles ou une méthode R personnalisée avec des analyses avancées. Cet utilisateur publie le modèle sous forme de service web afin que d'autres utilisent ses données.



* [Classifieur binaire](machine-learning-r-csharp-binary-classifier.md)
* [Modèle de cluster](machine-learning-r-csharp-cluster-model.md)
* [Régression linéaire multivariable](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Prévisions : lissage exponentiel](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [Prévisions ETS + STL](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Prévisions - Modèle ARIMA (Autoregressive Integrated Moving Average, moyenne mobile intégrée auto-régressive)](machine-learning-r-csharp-arima.md)
* [Analyse de survie](machine-learning-r-csharp-survival-analysis.md)


##Scénario 2 : modèle formé : données spécifiques 
Un utilisateur dispose de données qui fournissent des prédictions utiles dans le code R. Par exemple : un échantillon volumineux de questionnaires de personnalité regroupés via un algorithme à k moyennes pour prédire le type de personnalité de l’utilisateur ou des données d’enquête de santé qui peuvent être utilisées pour prédire le risque qu’un individu contracte un cancer du poumon, à l’aide d’un package R d’analyse de survie. L'utilisateur publie les données via un service web qui prédit les résultats d'un nouvel utilisateur.

##Scénario 3 : modèle formé : données génériques 
Un utilisateur possède des données génériques (par exemple, un corpus de texte) qui permettent à un service web d’être développé et appliqué de manière générique dans différents types de scénarios et cas d’usage.

* [Analyse de sentiments basée sur un lexique](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##Scénario 4 : calculatrice avancée 
Un utilisateur fournit des calculs complexes ou des simulations ne nécessitant aucun modèle formé ni ajustement d’un modèle pour les données de l’utilisateur.

* [Test de différence des proportions](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Normal Distribution Suite](machine-learning-r-csharp-normal-distribution.md)
* [Suite de distribution binomiale](machine-learning-r-csharp-binomial-distribution.md)

##Forum Aux Questions
Pour les Questions fréquemment posées relatives à l’utilisation du service web ou à la publication sur Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 

<!---HONumber=AcomDC_0921_2016-->