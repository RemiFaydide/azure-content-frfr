<properties
	pageTitle="Qu’est-ce que le processus TDSP (Team Data Science Process) ? | Microsoft Azure"
	description="Le processus TDSP (Team Data Science Process) est une méthode systématique servant à la création d’applications intelligentes qui exploitent des analyses avancées."
	keywords="processus de science des données, équipes de science des données"
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


# Qu’est-ce que le processus TDSP (Team Data Science Process) ?

Le processus TDSP (Team Data Science Process) fournit une approche systématique pour la création d’applications intelligentes qui permettent aux équipes de chercheurs de données de collaborer dans les meilleures conditions tout au long du cycle de vie des activités nécessaires à la conversion de ces applications en produits.

Plus précisément, le processus TDSP fournit actuellement aux équipes de science des données les informations suivantes :

- **Méthodologie** : décrit une séquence d’étapes qui définissent le cycle de vie de développement offrant des conseils sur la façon de définir le problème, d’analyser les données pertinentes, de créer et d’évaluer des modèles prédictifs, puis de déployer ces modèles dans les applications d’entreprise.
- **Ressources** : outils et technologies, par exemple la machine virtuelle de science de données, permettant de simplifier la configuration des environnements pour les activités de science des données et offrant des conseils pratiques pour l’intégration de nouvelles technologies.

Voici le cycle de vie de développement du processus de sciences des données d’équipe (TDSP) :

![Diagramme  : Procédure de science des données pour les équipes](./media/data-science-process-overview/data-science-process-for-teams-diagram.png)


Le processus est **itératif** : la connaissance des nouveautés, des données existantes, ou des adaptations du modèle évolue et nécessite une reprise des opérations déjà terminées dans la séquence. Les processus de planification et de développement organisationnel du projet sont **facilement adaptables**, et peuvent donc fonctionner avec des suites d’opérations définies par TDSP.

Les étapes du processus sont représentées et rassemblées dans le [parcours d'apprentissage TDSP](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) et décrites ci-dessous.


## Étapes de planification et de préparation

## P1. Planification de l’entreprise et de la technologie

Lancer un projet d’analyse en définissant des objectifs d’entreprise et une problématique. Ces éléments sont appelés **Exigences pour l'entreprise**. L’un des objectifs centraux de cette opération consiste à identifier les variables clés de l’entreprise (prévision de ventes ou probabilité qu’une commande en cours soit de nature frauduleuse, par exemple) que l’analyse doit prévoir pour satisfaire ces impératifs. Une planification supplémentaire est alors essentielle pour acquérir une bonne connaissance des **sources de données** nécessaires pour atteindre les objectifs fixés pour le projet d'un point de vue analytique. Il n’est pas rare, par exemple, de constater que les systèmes existants doivent collecter et consigner d’autres types de données pour résoudre le problème et atteindre les objectifs du projet. Pour obtenir des instructions, consultez [Planifier votre environnement pour le processus TDSP (Team Data Science Process)](machine-learning-data-science-plan-your-environment.md) et [Scénarios d'analyses avancées dans Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md).


## P2. Planification et préparation de l’infrastructure

Un environnement d’analyse pour le processus TDSP (Team Data Science Process) fait appel à plusieurs composants :

- **des espaces de travail de données** dans lesquels les données sont conservées pour analyse et modélisation ;
- une **infrastructure de traitement** pour le prétraitement, l'exploration et la modélisation des données ;
- une **infrastructure d'exécution** qui sert à faire fonctionner les modèles analytiques et à exécuter les applications clientes intelligentes qui consomment les modèles.

L’infrastructure d’analyse qui doit être configurée fait souvent partie d’un environnement distinct des systèmes opérationnels principaux. Cependant, elle utilise généralement des données issues de plusieurs systèmes fonctionnant au sein de l’entreprise ainsi que de sources externes. L’infrastructure d’analyse peut se trouver entièrement sur le cloud, être installée en local, ou présenter un mélange des deux. Pour connaître les options, référez-vous à [Configuration d’environnements de science des données à utiliser dans le processus TDSP (Team Data Science Process)](machine-learning-data-science-environment-setup.md).


## Opérations d’analyse :  

## 1\. Ingestion des données dans la plateforme de données

La première étape consiste à recueillir les données pertinentes à partir de diverses sources, internes ou extérieures à l’entreprise, dans un environnement d'analyse dans lequel les données peuvent être traitées. Le **format** des données source peut différer du format requis par la destination. Il se peut donc qu'une conversion des données doive être également réalisée par les outils d'ingestion. Pour connaître les options, référez-vous à [Charger des données dans des environnements de stockage à des fins d'analyse](machine-learning-data-science-ingest-data.md)

Outre l’acquisition initiale des données, de nombreuses applications intelligentes sont nécessaires pour actualiser les données régulièrement dans le cadre d’un processus d’apprentissage en cours d’exécution. Cette opération est possible grâce à la configuration d'un **pipeline de données** ou d'un flux de travail. Cela fait partie de la partie itérative du processus qui inclut la reconstruction et la réévaluation des modèles analytiques utilisés par l’application intelligente qui déploie la solution. Référez-vous par exemple à [Déplacement de données à partir d'un serveur SQL local vers SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md)


## 2\. Exploration et visualisation des données

L’étape suivante a pour objet d’offrir une meilleure connaissance des données en étudiant les **des statistiques de synthèse**, les relations et en utilisant des techniques de **visualisation**. C'est à ce niveau que les problèmes de **qualité des données** et d'intégrité, telles que les valeurs manquantes, les incompatibilités de type de données et les relations incohérentes entre les données sont gérés. Les conversions pré-traitement sont utilisées pour nettoyer les données brutes avant que les analyses et la modélisation puissent avoir lieu. Pour obtenir une description, référez-vous à T[âches de préparation des données pour l'apprentissage automatique amélioré](machine-learning-data-science-prepare-data.md).


## 3\. Création et sélection des fonctionnalités

Les analystes des données, en collaboration avec des experts du domaine, doivent identifier les fonctionnalités qui capturent les propriétés dominantes du jeu de données pouvant être utilisées pour prédire les données d’entreprise clés identifiées pendant la planification. Ces nouvelles fonctionnalités peuvent être dérivées de données existantes ou nécessiter la collecte de données supplémentaires. Ce processus est appelé **Conception de fonctionnalités** et est l'une des principales étapes de la création d'un système efficace d'analyse prédictive. Cette étape nécessite une combinaison inédite d’expertise dans le secteur et d’informations obtenues à partir de l’étape d’exploration de données. Pour plus d'informations, voir [Conception de fonctionnalités dans le processus TDSP (Team Data Science Process)](machine-learning-data-science-create-features.md).


## 4\. Créer et former des modèles d’apprentissage automatique

Les scientifiques de données construisent des modèles permettant de prévoir des variables clés identifiées dans les impératifs d’entreprise définis lors de l’étape grâce aux données nettoyées et analysées. Les systèmes d'apprentissage informatiques prennent en charge plusieurs **algorithmes de modélisation** applicables à toutes sortes de cas. Pour plus d'informations, voir [Comment choisir les algorithmes dans Azure Machine Learning](machine-learning-algorithm-choice.md).

Les scientifiques de données doivent choisir le modèle le mieux adapté à leur tâche de prévision, et il n’est pas rare qu’il faille associer des résultats de différents modèles pour obtenir les meilleurs résultats. Les données en entrée destinées à la modélisation sont généralement divisées aléatoirement en trois parties :

- un jeu de données de formation
- un jeu de données de validation
- un jeu de données de test

Les modèles sont construits avec le **jeu de données d'apprentissage**. La combinaison optimale de modèles (avec des paramètres réglés) est sélectionnée en exécutant des modèles et en mesurant les erreurs de prévision du **jeu de données de validation**. Enfin, le **jeu de données de test** sert à évaluer les performances du modèle choisi pour des données indépendantes n'ayant pas été utilisées pour la formation ou la validation du modèle. Pour connaître les procédures, référez-vous à [Évaluation des performances d'un modèle dans Azure Machine Learning](machine-learning-evaluate-model-performance.md).


## 5\. Déploiement et utilisation des modèles dans le produit

Une fois que notre ensemble de modèles fonctionne correctement, il peut être **mis en œuvre** et utilisé par d'autres applications. En fonction des exigences pour l'entreprise, les prévisions sont faites **en temps réel** ou **par lot**. Pour être mis en œuvre, les modèles doivent être exposés avec une **interface d'API ouverte** facilement utilisables à partir de diverses applications, par exemple, un site web en ligne, des feuilles de calcul, des tableaux de bord ou des applications métier et de serveur principal. Voir [Déploiement d'un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).


## Résumé et étapes suivantes

Le [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) est construit comme une suite d'étapes itérées qui **fournissent des conseils** sur les tâches indispensables à l'utilisation d'analyses avancées permettant de construire des applications intelligentes. Chaque étape fournit également des détails sur l’utilisation de diverses technologies Microsoft pour effectuer les tâches décrites.

TDSP n'impose pas de types d'artefacts de **documentation** spécifiques. Cependant, il est recommandé de documenter les résultats de l'examen, de la modélisation et de l'évaluation des données et d'enregistrer le code pertinent afin que l'analyse puisse être itérée le cas échéant. Ceci permet également de réutiliser le travail d’analyse lorsque vous travaillez sur d’autres applications impliquant des données et tâches de prévision similaires.

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. Elles sont répertoriées avec des descriptions de miniatures dans la rubrique [Team Data Science Process walkthroughs](data-science-process-walkthroughs.md) (Procédures pas à pas du processus TDSP (Team Data Science Process)).

<!---HONumber=AcomDC_0921_2016-->