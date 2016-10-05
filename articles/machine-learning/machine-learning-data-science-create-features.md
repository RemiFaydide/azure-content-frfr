<properties
	pageTitle="Conception de fonctionnalités dans le processus Cortana Analytics | Microsoft Azure" 
	description="Explique les finalités de la conception de fonctionnalités et fournit des exemples de son rôle dans le processus d'amélioration des données de l'apprentissage automatique."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="zhangya;bradsev" />


# Conception de fonctionnalités dans le processus Cortana Analytics 

Cette rubrique explique les finalités de la conception de fonctionnalités et fournit des exemples de son rôle dans le processus d’amélioration des données de l’apprentissage automatique. Les exemples utilisés pour illustrer ce processus sont tirés d’Azure Machine Learning Studio.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Ce **menu** pointe vers des rubriques qui expliquent comment créer des fonctionnalités pour les données dans différents environnements. Cette tâche est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

La conception de fonctionnalités tente d'augmenter la puissance prédictive des algorithmes d'apprentissage en créant des fonctionnalités à partir de données brutes qui facilitent le processus d'apprentissage. La conception et la sélection de fonctionnalités constituent une partie du processus TDSP présenté dans [Qu'est ce que le processus TDSP (Team Data Science Process) ?](data-science-process-overview.md) La conception et la sélection de fonctionnalités sont des parties de l’étape de **développement de fonctionnalités** du processus TDSP.

* La **conception de fonctionnalités** : ce processus tente de créer des fonctionnalités supplémentaires pertinentes à partir de fonctionnalités brutes existantes dans les données et d’augmenter la performance de prédiction de l’algorithme d’apprentissage.

* La **sélection de caractéristiques** : ce processus sélectionne le sous-ensemble clé des caractéristiques de données d'origine afin de réduire la dimensionnalité du problème d'apprentissage.

En général, la **conception de fonctionnalités** s’applique d’abord à la génération de fonctionnalités supplémentaires. L’étape de **sélection de fonctionnalités** est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou fortement corrélées.

Les données d'apprentissage utilisées dans l'apprentissage automatique peuvent souvent être améliorées par l'extraction de fonctionnalités à partir des données brutes collectées. Un exemple de conception de fonctionnalité dans le cadre de l'apprentissage de la classification des images de caractères écrits à la main est la création d'une carte de densité de bits construite à partir des données brutes de distribution de bits. Cette carte peut aider à localiser les bords des caractères plus efficacement que l'utilisation de la distribution brute directement.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Création de caractéristiques à partir de vos données - Conception de caractéristiques

Les données d'apprentissage se constituent d'une matrice composée d'exemples (enregistrements ou observations stockées dans les lignes), chaque exemple disposant d'un ensemble de caractéristiques (variables ou champs stockés dans des colonnes). Les caractéristiques spécifiées dans la conception expérimentale sont supposées caractériser les modèles dans les données. Bien que plusieurs champs de données brutes puissent être directement inclus dans l'ensemble des caractéristiques sélectionnées utilisées pour l'apprentissage d'un modèle, il est fréquent que des caractéristiques supplémentaires (conçues) doivent être construites à partir des caractéristiques dans les données brutes pour générer un jeu de données de formation amélioré.

Quelles sont les caractéristiques qui doivent être créées pour améliorer le jeu de données lors de l'apprentissage d'un modèle ? Les caractéristiques conçues améliorant l'apprentissage offrent des informations qui permettent de mieux différencier les modèles dans les données. Les nouvelles caractéristiques devraient fournir des informations supplémentaires qui ne sont pas clairement capturées ou facilement visibles dans l'ensemble des caractéristiques existantes ou d'origine. Mais ce processus est tout un art. Des décisions réfléchies et productives nécessitent souvent une spécialisation dans le domaine.

En débutant avec Azure Machine Learning, il est plus facile de comprendre correctement le processus avec des exemples fournis dans le Studio. Deux exemples sont présentés ici :

* un exemple de régression [Prédiction du nombre de locations de vélo](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) dans une expérience supervisée, où les valeurs cibles sont connues
* un exemple de classification d'exploration de texte utilisant le [hachage de caractéristiques](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### Exemple 1 : ajout de caractéristiques temporelles pour le modèle de régression ###

Nous allons utiliser l'exemple de « prévision de la demande de vélos » dans Azure Machine Learning Studio afin de démontrer comment concevoir des caractéristiques pour une tâche de régression. L'objectif de cette expérience est de prédire la demande de vélos, autrement dit le nombre de locations de vélo pour un mois/un jour/une heure spécifique. Le « jeu de données de location de vélo UCI »est utilisé en tant que données brutes d'entrée. Le jeu de données se base sur des données réelles de la société Capital Bikeshare qui gère un réseau de location de vélos à Washington DC aux États-Unis. Ce jeu de données représente le nombre de locations de vélo pour une heure spécifique d'un jour en 2011 et en 2012 et contient 17 379 lignes et 17 colonnes. L'ensemble des caractéristiques brutes contient des conditions météorologiques (température/humidité/vitesse du vent) et le type de jour (vacances/jour de semaine). Le champ à prédire est « cnt », un nombre qui représente les locations de vélo pour une heure spécifique et qui est compris entre 1 et 977.

Afin de construire des caractéristiques efficaces dans les données d'apprentissage, quatre modèles de régression sont générés à l'aide du même algorithme, mais avec quatre jeux de données d'apprentissage différents. Les quatre jeux de données représentent les mêmes données d'entrée brutes, mais avec un nombre croissant de jeux de caractéristiques. Ces caractéristiques sont regroupées en quatre catégories :

1. A = caractéristiques météo + vacances + jour de semaine + week-end pour le jour prévu
2. B = nombre de vélos loués au cours de chacune des 12 dernières heures
3. C = nombre de vélos loués au cours de chacun des 12 derniers jours à la même heure
4. D = nombre de vélos loués au cours de chacune des 12 dernières semaines le même jour à la même heure

Excepté l'ensemble de caractéristiques A, qui existe déjà dans les données brutes d'origine, les trois autres ensembles de caractéristiques sont créés via le processus de conception des caractéristiques. L'ensemble de caractéristiques B capture les toutes dernières demandes de vélos. L'ensemble de caractéristiques C capture la demande de vélos pour une heure en particulier. L'ensemble de caractéristiques D capture la demande de vélos pour une heure particulière et un jour de la semaine particulier. Chacun des quatre jeux de données d'apprentissage inclut respectivement un ensemble de caractéristiques A, A + B, A + B + C et A + B + C + D.

Dans l'expérience d'Azure Machine Learning, ces quatre jeux de données d'apprentissage sont constitués via quatre branches à partir du jeu de données d'entrée traité au préalable. À l'exception de la branche la plus à gauche, chacune de ces branches contient un module [Exécuter le Script R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/), dans lequel un ensemble de caractéristiques dérivées (ensemble de caractéristiques B, C et D) sont respectivement construites et ajoutées au jeu de données importé. La figure suivante montre le script R utilisé pour créer un ensemble de caractéristiques B dans la deuxième branche de gauche.

![création de caractéristiques](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

La comparaison des résultats de performance des quatre modèles est résumée dans la table suivante. Les caractéristiques A + B + C affichent les meilleurs résultats. Notez que le taux d'erreur diminue lorsqu'un ensemble de caractéristiques supplémentaires est inclus dans les données d'apprentissage. Cela confirme l'idée présupposée que les ensembles de caractéristiques B et C fournissent des informations supplémentaires pertinentes pour la tâche de régression. Mais l'ajout de la caractéristique D semble ne pas fournir une réduction du taux d'erreur supplémentaire.

![comparaison des résultats](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a> Exemple 2 : création de caractéristiques dans l'exploration de texte  

L'ingénierie de caractéristiques s'applique largement aux tâches liées à l'exploration de texte, telles que la classification de document et l'analyse de sentiments. Par exemple, lorsque vous souhaitez classer des documents en plusieurs catégories, l'hypothèse typique est que les mots ou expressions inclus dans une catégorie de document sont moins susceptibles de se produire dans une autre catégorie de document. Autrement dit, la fréquence de la distribution de mots ou d'expressions est capable d'identifier les différentes catégories de document. Dans les applications d'exploration de texte, étant donné que chaque élément des contenus de textes est généralement utilisé en tant que données d'entrée, le processus de conception de caractéristiques est nécessaire pour créer les caractéristiques impliquant des fréquences de mot ou d'expression.

Pour effectuer cette tâche, une technique appelée **hachage de caractéristiques** est appliquée pour transformer efficacement les caractéristiques de texte arbitraires en index. Au lieu d'associer chaque caractéristique de texte (mots ou d'expressions) à un index particulier, cette méthode fonctionne en appliquant une caractéristique de hachage aux caractéristiques et en utilisant directement leurs valeurs de hachage en tant qu'index.

Dans Azure Machine Learning, il existe un module de [hachage de caractéristiques](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) qui crée en toute facilité ces caractéristiques de mot ou expression. La figure suivante montre un exemple d'utilisation de ce module. Le jeu de données d'entrée contient deux colonnes : l'évaluation du livre allant de 1 à 5 et le contenu même de la critique. L'objectif de ce module de [hachage de caractéristiques](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) est de récupérer un ensemble de nouvelles caractéristiques qui montrent la fréquence d'occurrence des mots ou expressions correspondants dans cette critique de livre en particulier. Pour utiliser ce module, il est nécessaire d'effectuer les étapes suivantes :

* Tout d'abord, sélectionnez la colonne qui contient le texte d'entrée (« Col2 » pour cet exemple).
* Ensuite, définissez le « nombre de bits de hachage » sur 8, ce qui signifie que 2 ^ 8 = 256 caractéristiques seront créées. Le mot ou l'expression sera haché en 256 index dans tout le texte. Le paramètre « hachage du nombre de bits » est compris entre 1 et 31. Les mots ou les phrases sont moins susceptibles d'être hachés dans le même index s'ils sont définis sur un nombre plus grand.
* Enfin, définissez le paramètre « N-grammes » sur 2. Celui-ci permet d'obtenir la fréquence d'occurrence d'unigrammes (une caractéristique pour chaque mot) et de bigrammes (une caractéristique pour chaque paire de mots juxtaposés) à partir du texte d'entrée. Le paramètre « N-grammes » est compris entre 0 et 10, ce qui indique le nombre maximum de mots séquentiels à inclure dans une caractéristique.

![Module « hachage de caractéristiques »](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figure suivante montre à quoi ressemblent ces nouvelles caractéristiques.

![Exemple « hachage de caractéristiques »](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## Conclusion

L'ingénierie et la sélection de caractéristiques augmentent l'efficacité du processus d'apprentissage qui tend à extraire les informations essentielles contenues dans les données. Ces processus améliorent également les performances de ces modèles pour classifier les données d'entrée avec précision et prédire les résultats pertinents de façon plus consistante. L'ingénierie et la sélection de caractéristiques peuvent également être combinées afin de rendre l'apprentissage plus souple d'un point de vue informatique. Cela se fait grâce à l'amélioration puis à la réduction du nombre de caractéristiques nécessaires à l'étalonnage ou l'apprentissage d'un modèle. D'un point de vue mathématique, les caractéristiques sélectionnées pour effectuer l'apprentissage du modèle sont un ensemble minimum de variables indépendantes qui expliquent les modèles des données puis prédisent correctement les résultats.

Notez qu'il n'est pas toujours nécessaire d'effectuer l'ingénierie de caractéristiques ou la sélection des caractéristiques. Que cela soit nécessaire ou non dépend des données que l'on a à disposition ou qui sont collectées, de l'algorithme choisi et des objectifs de l'expérience.
 

<!---HONumber=AcomDC_0921_2016-->