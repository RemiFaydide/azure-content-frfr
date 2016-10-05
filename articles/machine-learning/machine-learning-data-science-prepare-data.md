<properties
	pageTitle="Tâches de préparation des données pour l'apprentissage automatique amélioré | Microsoft Azure"
	description="Prétraitez et nettoyez les données afin de les préparer pour l'apprentissage automatique."
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
	ms.author="bradsev" />


# Tâches de préparation des données pour l'apprentissage automatique amélioré

Le prétraitement et le nettoyage des données sont des tâches importantes qui doivent intervenir avant d'utiliser un jeu de données à des fins d'apprentissage automatique. Les données brutes sont souvent bruyantes, peu fiables et incomplètes. Leur utilisation pour la modélisation peut générer des résultats trompeurs. Ces tâches font partie du processus TDSP (Team Data Science Process) et suivent généralement l'exploration initiale d'un jeu de données utilisé pour découvrir et planifier le traitement préliminaire requis. Pour plus d'instructions sur le processus TDSP, consultez les étapes décrites dans le [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Les tâches de traitement préalable et de nettoyage, comme la tâche d'exploration de données, peuvent être exécutées dans une grande variété d'environnements, tels que SQL ou Hive ou Azure Machine Learning Studio et avec différents outils et langages, tels que R ou Python, en fonction de l'emplacement de stockage de vos données et leur mise en forme. Étant donné que le processus TDSP est itératif par nature, ces tâches peuvent avoir lieu à diverses étapes du flux de travail du processus.

Cet article présente différents concepts et tâches de prétraitement des données, à effectuer avant ou après l'intégration de ces données dans Azure Machine Learning.

Pour obtenir un exemple d'exploration de données et de prétraitement effectués dans Azure Machine Learning Studio, consultez la vidéo [Prétraitement des données dans Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## Pourquoi prétraiter et nettoyer les données ?

Dans le monde réel, les données proviennent de plusieurs sources et processus. Elles peuvent contenir des anomalies ou des valeurs incorrectes qui compromettent la qualité du jeu de données. Les problèmes de qualité les plus fréquents sont les suivants :

* **Caractère incomplet :** des valeurs ou des attributs sont manquants.
* **Bruit :** les données contiennent des enregistrements erronés ou des aberrations.
* **Incohérence :** les données contiennent des enregistrements en conflit ou des contradictions.

La qualité des données est essentielle pour obtenir des modèles prédictifs performants. Pour éviter de traiter des données erronées et améliorer la performance du modèle, il faut impérativement analyser les données, détecter les anomalies le plus tôt possible et déterminer les étapes de prétraitement et de nettoyage appropriées.

## Quels sont les critères d’intégrité des données généralement employés ?

Il est possible d’évaluer la qualité globale des données en vérifiant les éléments suivants :

* Le nombre d'**enregistrements**.
* Le nombre d'**attributs** (ou de **fonctionnalités**).
* Le **type de données** des attributs (nominal, ordinal ou continu).
* Le nombre de **valeurs manquantes**.
* La **qualité de formation** des données.
	* Si les données sont au format TSV ou CSV, vérifiez que les séparateurs de colonne et de ligne séparent correctement et systématiquement les colonnes et les lignes.
	* Si les données sont au format HTML ou XML, vérifiez qu'elles sont conformes à leur standard respectif.
	* Une analyse peut également se révéler nécessaire pour obtenir des informations structurées à partir de données non ou semi-structurées.
* **Enregistrements de données incohérents**. Vérifiez la plage des valeurs autorisées. Par exemple, si les données sont des notes moyennes d'étudiant, vérifiez qu'elles sont comprises dans la fourchette correcte.

Lorsque vous détectez des problèmes dans les données, des **étapes de traitement** s'imposent : nettoyage des valeurs manquantes, normalisation des données, discrétisation, traitement de texte pour supprimer et/ou remplacer des caractères susceptibles de perturber l'alignement des données, types de données mixtes dans les champs communs, etc.

**Azure Machine Learning n'exploite que les données tabulaires bien formées**. Si les données sont déjà au format tabulaire, Azure Machine Learning peut les prétraiter directement dans Machine Learning Studio. Si elles ne sont pas au format tabulaire, comme le format XML, une analyse peut être nécessaire pour les convertir.

## Quelles sont les principales opérations effectuées lors du prétraitement des données ?

* **Nettoyage des données :** compléter les valeurs manquantes, détecter et supprimer les données bruyantes et les aberrations.
* **Transformation des données :** normaliser les données pour réduire le volume et le bruit.
* **Réduction des données :** échantillonner les enregistrements de données ou les attributs pour faciliter la manipulation des données.
* **Discrétisation des données :** convertir des attributs continus en attributs catégoriels pour simplifier l’exploitation des données dans certains outils d’apprentissage automatique.
* **Nettoyage du texte :** supprimer les caractères intégrés pouvant perturber l’alignement des données (comme les tabulations dans un fichier TSV), les nouvelles lignes qui peuvent interrompre des enregistrements, etc.

Les sections suivantes décrivent certaines de ces étapes de traitement des données.

## Comment gérer les valeurs manquantes ?

Si vous avez des valeurs manquantes, la première chose à faire est d’en identifier l’origine. Les méthodes les plus courantes de traitement des valeurs manquantes sont les suivantes :

* **Suppression :** supprimer les enregistrements ayant des valeurs manquantes.
* **Remplacement par une valeur factice :** remplacer des valeurs manquantes par une valeur factice : par exemple, _inconnu_ pour les valeurs catégorielles ou 0 pour les valeurs numériques.
* **Remplacement par la moyenne :** si les données manquantes sont numériques, remplacez-les par la valeur moyenne.
* **Remplacement par l’élément le plus fréquent :** si les données manquantes sont catégorielles, remplacer les valeurs manquantes par l’élément le plus fréquent.
* **Remplacement par la valeur de régression :** remplacer les valeurs manquantes par des valeurs de régression.

## Comment normaliser les données ?

La normalisation des données restreint les valeurs numériques à une plage spécifiée. Les méthodes de normalisation les plus courantes sont les suivantes :

* **Normalisation min-max** : adapter linéairement les données à une plage comprise, par exemple, entre 0 et 1. La valeur minimale est 0 et la valeur maximale est 1.
* **Normalisation par le test Z** : mettre les données à l’échelle en fonction de la moyenne et de l’écart standard : diviser la différence entre les données et la moyenne par l’écart standard.
* **Mise à l'échelle décimale** : mettre les données à l’échelle en déplaçant le séparateur décimal de la valeur de l’attribut.

## Comment discrétiser les données ?

Pour discrétiser les données, il faut convertir les valeurs continues en attributs ou intervalles nominaux. Plusieurs méthodes permettent d’effectuer cette opération :

* **Compartimentage à largeur identique** : diviser la plage de toutes les valeurs possibles d’un attribut en N groupes de même taille et attribuer aux valeurs le numéro de compartiment qui leur correspond.
* **Compartimentage à hauteur identique :** diviser la plage de toutes les valeurs possibles d’un attribut en N groupes contenant le même nombre d’instances, puis attribuer à aux valeurs le numéro de compartiment qui leur correspond.

## Comment réduire les données ?

Plusieurs méthodes permettent de réduire la taille des données pour en faciliter la manipulation. Selon la taille et le domaine, les méthodes applicables sont les suivantes :

* **Échantillonnage des enregistrements** : échantillonner les enregistrements de données et ne choisir que le sous-ensemble représentatif.
* **Échantillonnage des attributs** : ne sélectionner que les attributs importants dans les données.
* **Agrégation **: diviser les données en groupes et stocker les nombres de chaque groupe. Par exemple, le chiffre d’affaires quotidien d’une chaîne de restaurants sur les 20 dernières années peut être agrégé en un chiffre d’affaires mensuel pour réduire la taille des données.

## Comment nettoyer les données textuelles ?

**Les champs de texte au format tabulaire** peuvent contenir des caractères qui perturbent l’alignement des données et/ou les limites des enregistrements. Par exemple, des tabulations intégrées dans un fichier TSV peuvent décaler les colonnes, et des caractères de nouvelle ligne peuvent déstructurer des enregistrements. Une manipulation inappropriée de l’encodage pendant l’écriture ou la lecture de texte provoque une perte d’informations ou l’ajout intempestif de caractères illisibles, pouvant empêcher une analyse correcte du texte. Soyez prudent lorsque vous analysez et modifiez des champs de texte pour corriger l’alignement et/ou extraire des données structurées de données non ou semi-structurées.

**L’exploration des données** permet d’obtenir une vue sur l’état des données. Plusieurs problèmes peuvent être détectés pendant cette étape et les méthodes de correction correspondantes, appliquées. Il est important de s’interroger sur l’origine du problème et la manière dont il a été introduit dans les données. Cela permet de déterminer les étapes de traitement à effectuer pour résoudre ces problèmes. Les conclusions que l’on peut tirer de l’analyse des données permettent également de hiérarchiser le travail de traitement des données.

## Références

>*Data Mining : Concepts et Techniques*, 3e édition, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber et Jian Pei

<!---HONumber=AcomDC_0921_2016-->