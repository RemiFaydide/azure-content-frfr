<properties
	pageTitle="Sélection de fonctionnalités dans le processus TDSP (Team Data Science Process) | Microsoft Azure" 
	description="Explique la finalité de la sélection de fonctionnalités et fournit des exemples de son rôle dans le processus d’amélioration des données de l’apprentissage automatique."
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


# Sélection de fonctionnalités dans le processus TDSP (Team Data Science Process)

Cet article explique les finalités de la sélection de fonctionnalités et fournit des exemples de son rôle dans le processus d’amélioration des données de l’apprentissage automatique. Ces exemples sont tirés d’Azure Machine Learning Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cette rubrique explique la finalité de la sélection de fonctionnalités et fournit des exemples de son rôle dans le processus d’amélioration des données de l’apprentissage automatique. Ces exemples sont tirés d’Azure Machine Learning Studio.

La conception et la sélection de fonctionnalités constituent une partie du processus TDSP présenté dans [Qu'est ce que le processus TDSP (Team Data Science Process) ?](data-science-process-overview.md). La conception et la sélection de fonctionnalités sont des parties de l’étape de **développement de fonctionnalités** du processus TDSP.

* La **conception de caractéristiques** : ce processus tente de créer des caractéristiques supplémentaires pertinentes à partir de caractéristiques brutes existantes dans les données et d'augmenter la performance de prédiction de l'algorithme d'apprentissage.

* La **sélection de caractéristiques** : ce processus sélectionne le sous-ensemble clé des caractéristiques de données d'origine afin de réduire la dimensionnalité du problème d'apprentissage.

En général, la **conception de fonctionnalités** s’applique d’abord à la génération de fonctionnalités supplémentaires. L’étape de **sélection de fonctionnalités** est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou fortement corrélées.


## Filtrage des caractéristiques à partir de vos données - Sélection de caractéristiques 

La sélection de caractéristiques est un processus qui s'applique en général à la construction de jeux de données d'apprentissage pour les tâches de modélisation prédictives telles que les tâches de classification ou de régression. L'objectif est de sélectionner un sous-ensemble des caractéristiques du jeu de données d'origine qui réduit ses dimensions à l'aide d'un ensemble minimal de caractéristiques pour représenter l'écart de quantité maximum dans les données. Les caractéristiques de ce sous-ensemble sont les seules caractéristiques à inclure à l'apprentissage du modèle. La sélection de caractéristiques a deux principaux objectifs.

* Tout d'abord, la sélection des caractéristiques augmente souvent la précision de classification en éliminant les caractéristiques non pertinentes, redondantes ou fortement liées.
* De plus, il réduit le nombre de caractéristiques qui rendent le processus d'apprentissage du modèle plus efficace. Cela est particulièrement important pour les apprenants dont l'apprentissage est coûteux tels que les machines à vecteurs de support.

Bien que la sélection des caractéristiques ait pour objet de réduire le nombre de caractéristiques dans le jeu de données utilisé pour l'apprentissage du modèle, celle-ci ne correspond généralement pas au terme de « réduction de la dimensionnalité ». Les méthodes de sélection de caractéristiques extraient un sous-ensemble des caractéristiques d'origine dans les données sans les modifier. Les méthodes de réduction de la dimensionnalité utilisent l'ingénierie des caractéristiques qui peuvent transformer les caractéristiques d'origine et donc les modifier. Parmi les exemples de méthodes de réduction de la dimensionnalité, on peut noter l'analyse du composant principal, l'analyse canonique des corrélations et la décomposition en valeurs uniques.

Notamment, l'une des méthodes de sélection de caractéristiques de catégorie largement appliquée dans un contexte supervisé est appelée « sélection de caractéristiques basée sur les filtres ». En évaluant la corrélation entre chaque caractéristique et l'attribut cible, ces méthodes appliquent une mesure statistique pour attribuer un score à chaque caractéristique. Les caractéristiques sont ensuite classées suivant le score qui peut être utilisé pour aider à définir le seuil de conservation ou d'élimination d'une caractéristique spécifique. Parmi les exemples de mesures statistiques utilisées dans ces méthodes, on peut noter la corrélation de Pearson, des informations mutuelles et le test de la loi du Khi-deux.

Dans Azure Machine Learning Studio, des modules sont fournis pour la sélection des caractéristiques. Comme indiqué dans la figure suivante, ces modules comprennent une [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection] et une [analyse discriminante linéaire de Fisher][fisher-linear-discriminant-analysis].

![Exemple de sélection de caractéristiques](./media/machine-learning-data-science-select-features/feature-Selection.png)


Prenons l'exemple de l'utilisation du module de [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection]. Pour plus de commodité, nous continuons d'utiliser l'exemple d'exploration de texte présenté ci-dessus. Supposons que nous voulons créer un modèle de régression après avoir créé un ensemble de 256 caractéristiques via le module de [hachage de caractéristiques][feature-hashing] et que la variable de réponse est le « Col1 » et représente une critique de livre notée de 1 à 5. La « Méthode de notation des caractéristiques » doit être définie en tant que « corrélation de Pearson », la « Colonne cible » en tant que « Col1 » et le « nombre de caractéristiques souhaitées » à 50. Le module de [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection] produira ensuite un jeu de données contenant 50 caractéristiques avec l'attribut cible « Col1 ». La figure suivante montre le flux de cette expérience et les paramètres d'entrée que nous venons de décrire.

![Exemple de sélection de caractéristiques](./media/machine-learning-data-science-select-features/feature-Selection1.png)

La figure suivante montre les jeux de données qui en résultent. Chaque caractéristique obtient un score basé sur la corrélation de Pearson entre elle et l'attribut cible « Col1 ». Les caractéristiques avec les scores les plus élevés sont conservées.

![Exemple de sélection de caractéristiques](./media/machine-learning-data-science-select-features/feature-Selection2.png)

Les scores correspondants aux caractéristiques sélectionnées sont indiqués dans la figure suivante.

![Exemple de sélection de caractéristiques](./media/machine-learning-data-science-select-features/feature-Selection3.png)

En appliquant ce module de [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection], 50 des 256 caractéristiques sont sélectionnées, car elles présentent la corrélation la plus importante avec la variable cible « Col1 » selon la méthode de notation « corrélation de Pearson ».

## Conclusion
La conception et la sélection de fonctionnalités augmentent toutes deux l’efficacité du processus d’apprentissage qui tend à extraire les informations essentielles contenues dans les données. Ces processus améliorent également les performances de ces modèles pour classifier les données d'entrée avec précision et prédire les résultats pertinents de façon plus consistante. L'ingénierie et la sélection de caractéristiques peuvent également être combinées afin de rendre l'apprentissage plus souple d'un point de vue informatique. Cela se fait grâce à l'amélioration puis à la réduction du nombre de caractéristiques nécessaires à l'étalonnage ou l'apprentissage d'un modèle. D'un point de vue mathématique, les caractéristiques sélectionnées pour effectuer l'apprentissage du modèle sont un ensemble minimum de variables indépendantes qui expliquent les modèles des données puis prédisent correctement les résultats.

Notez qu'il n'est pas toujours nécessaire d'effectuer l'ingénierie de caractéristiques ou la sélection des caractéristiques. Que cela soit nécessaire ou non dépend des données que l'on a à disposition ou qui sont collectées, de l'algorithme choisi et des objectifs de l'expérience.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 

<!---HONumber=AcomDC_0921_2016-->