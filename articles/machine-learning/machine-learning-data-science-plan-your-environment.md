<properties 
	pageTitle="Comment identifier les scénarios et planifier le traitement des données d’analyse avancée | Microsoft Azure" 
	description="Planifiez une analyse avancée en imaginant une série de questions clés." 
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


# Comment identifier les scénarios et planifier le traitement des données d’analyse avancée

Quelles ressources devez-vous inclure lors de la configuration d’un environnement à des fins de traitement des analyses avancées sur un jeu de données ? Cet article propose une série de questions qui permettront d’identifier les tâches et les ressources pertinentes pour votre scénario. L’ordre des étapes principales pour l’analyse prédictive est défini dans [Qu’est-ce que le processus TDSP (Team Data Science Process) ?](data-science-process-overview.md). Chacune de ces étapes nécessite des ressources spécifiques pour les tâches correspondant à votre scénario. Les principales questions pour identifier votre scénario concernent la logistique de données, leurs caractéristiques, la qualité des jeux de données, ainsi que les outils et les langues à utiliser pour effectuer l’analyse.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Questions logistiques : emplacements et déplacement des données
Les questions logistiques concernent l’emplacement de la **source de données**, la **destination cible** dans Azure et la configuration requise pour le déplacement des données, y compris la planification, la quantité et les ressources impliquées. Il peut être nécessaire de déplacer les données plusieurs fois pendant le processus d’analyse. Un scénario courant consiste à déplacer les données locales dans un système de stockage sur Azure, puis dans Machine Learning Studio.

1. **Quelle est votre source de données ?** S’agit-il d’une source locale ou dans le cloud ? Par exemple :
	- Les données sont accessibles au public à une adresse HTTP.
	- Les données résident dans un fichier local ou sur le réseau.
	- Les données sont contenues dans une base de données SQL Server.
	- Les données sont stockées dans un conteneur de stockage Azure.

2. **Qu’est-ce que la destination Azure ?** Où doit-elle se trouver pour le traitement ou la modélisation ? Par exemple :
	- un stockage Azure Blob
	- Bases de données SQL Azure
	- SQL Server dans les machines virtuelles Azure
	- HDInsight (Hadoop sur Azure) ou tables Hive
	- Azure Machine Learning
	- Disques durs virtuels Azure montables.

3. **Comment allez-vous déplacer les données ?** Les procédures et les ressources disponibles pour l’intégration ou le chargement des données dans différents environnements de stockage et de traitement sont décrites dans les rubriques suivantes.

	-  [Charger des données dans des environnements de stockage à des fins d’analyse](machine-learning-data-science-ingest-data.md)
	-  [Importez vos données d’apprentissage Azure Machine Learning Studio depuis différentes sources de données](machine-learning-data-science-import-data,md).

4. **Les données doivent-elles être déplacées à intervalles réguliers ou modifiées pendant la migration ?** Envisagez d’utiliser Azure Data Factory (ADF) lorsque les données doivent être migrées en permanence, notamment si cela implique un scénario hybride qui accède aux ressources locales et cloud, lorsque les données sont traitées ou doivent être modifiées, ou si vous ajoutez une logique métier en cours de migration. Pour plus d’informations, référez-vous à [Déplacement de données à partir d’un serveur SQL local vers SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md)

5. **Quelle est la quantité de données à déplacer vers Azure ?** Si les jeux de données sont très volumineux, ils peuvent dépasser la capacité de stockage de certains environnements. Pour obtenir un exemple, reportez-vous à la rubrique sur les limites de taille pour Machine Learning Studio dans la section suivante. Dans ces cas, un échantillon de données peut servir à l’analyse. Pour plus d’informations sur l’échantillonnage d’un jeu de données dans différents environnements Azure, référez-vous à [Échantillonner des données dans le processus TDSP (Team Data Science Process)](machine-learning-data-science-sample-data.md).


## Questions sur les caractéristiques des données : type, format et taille
Ces questions sont essentielles pour la planification de vos environnements de stockage et de traitement. Chacun d’entre eux sera adapté aux différents types de données, avec des restrictions propres.

1. **Quels sont les types de données ?** Par exemple :
	- Numérique
	- Par catégorie
	- Chaînes
	- Fichier binaire

2. **Quel est le format de vos données ?** Par exemple :
    - Fichiers plats séparés par des virgules (CSV) ou séparées par une tabulation (TSV)
    - Compressés ou non
	- Objets blob Azure
	- Tables Hadoop Hive
	- Tables SQL Server

2. **Quel volume vos données représentent-elles ?**
    - Petit : moins de 2 Go
    - Moyen : entre 2 Go et 10 Go
	- Grand : supérieur à 10 Go

Prenons par exemple l’environnement Azure Machine Learning Studio :

- Pour obtenir la liste des formats et des types de données pris en charge par Azure Machine Learning Studio, référez-vous à la section [Formats et types de données pris en charge](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported).
- Pour plus d’informations sur les limitations en matière de données pour Azure Machine Learning Studio, référez-vous à la section **Quelle est la taille maximale du jeu de données de mes modules ?** de [Importation et exportation de données pour Machine Learning](machine-learning-faq.md#machine-learning-studio-questions).

Pour plus d’informations sur les limitations des autres services Azure utilisés dans le processus d’analyse, référez-vous à [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## Questions sur la qualité des données : exploration et prétraitement

1. **Que savez-vous sur vos données ?** Explorez les données lorsque vous avez besoin d’obtenir et de comprendre leurs caractéristiques de base. Par exemple les modèles ou les tendances qu’elles dévoilent, les aberrations qu’elles contiennent ou le nombre de valeurs manquantes. Cette étape est importante en vue de déterminer l’étendue du prétraitement nécessaire, de formuler des hypothèses visant à suggérer les fonctionnalités ou le type d’analyse les plus appropriés, et de planifier la collecte de données supplémentaires. Le calcul des statistiques descriptives et le tracé des visualisations sont des techniques utiles pour l’inspection des données. Pour plus d’informations sur l’exploration d’un jeu de données dans différents environnements Azure, référez-vous à [Explorer les données du processus TDSP (Team Data Science Process)](machine-learning-data-science-explore-data.md).

2. **Les données nécessitent-elles un prétraitement ou un nettoyage ?** Le prétraitement et le nettoyage des données sont des tâches importantes qui doivent intervenir avant d'utiliser un jeu de données à des fins d'apprentissage automatique. Les données brutes sont souvent bruyantes, peu fiables et incomplètes. Leur utilisation pour la modélisation peut générer des résultats trompeurs. Pour obtenir une description, référez-vous à [Tâches de préparation des données pour l’apprentissage automatique amélioré](machine-learning-data-science-prepare-data.md).

## Questions sur les outils et les langues
Il existe un grand nombre de possibilités en fonction des langues, des environnements de développement et des outils dont vous avez besoin ou avec lesquels vous êtes le plus à l’aise.
 
1. **Quelles langues préférez-vous utiliser pour l’analyse ?**
	- R
	- Python
	- SQL

2. **Quels outils devez-vous utiliser pour l’analyse des données ?**
	- [Microsoft Azure Powershell](powershell-install-configure.md) - langage de script utilisé pour administrer vos ressources Azure dans un langage de script.
	- [Azure Machine Learning Studio](machine-learning-what-is-ml-studio/)
	- [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
	- [RStudio](http://www.rstudio.com)
	- [Python Tools pour Visual Studio](http://microsoft.github.io/PTVS/)
	- [Anaconda](https://www.continuum.io/why-anaconda)
	- [Notebooks Jupiter](http://jupyter.org/)
	- [Microsoft Power BI](http://powerbi.microsoft.com)


## Identification de votre scénario d’analyse avancée
Une fois que vous avez répondu aux questions de la section précédente, vous êtes prêt à déterminer le scénario qui vous convient le mieux. Les exemples de scénarios sont présentés dans la rubrique [Scénarios d’analyses avancées dans Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md).







 

<!---HONumber=AcomDC_0921_2016-->