<properties
	pageTitle="FAQ Azure Machine Learning Studio | Microsoft Azure"
	description="Présentation d'Azure Machine Learning : FAQ portant sur la facturation, les fonctionnalités et les limitations d'un service cloud pour la modélisation prédictive rationalisée."
	keywords="introduction à l’apprentissage automatique, modélisation prédictive, présentation de l’apprentissage automatique"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="garye"/>

# Forum Aux Questions (FAQ) Azure Machine Learning : facturation, fonctionnalités, limitations et support

Ce Forum Aux Questions contient des questions et des réponses sur Azure Machine Learning, un service cloud pour le développement de modèles prédictifs et l’opérationnalisation des solutions via les services web. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## Questions générales

**Qu'est-ce que Microsoft Azure Machine Learning ?**

Microsoft Azure Machine Learning est un service entièrement géré qui permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans le cloud. À l’aide d’un simple navigateur, vous pouvez vous inscrire, télécharger des données et commencer immédiatement des expériences d’apprentissage automatique. Grâce à la modélisation prédictive par glisser-déplacer, la vaste palette de modules et la bibliothèque de modèles de départ, les tâches d'apprentissage automatique les plus courantes sont simples et rapides. Pour plus d’informations, consultez la [Vue d’ensemble du service Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Pour une présentation de l'apprentissage automatique couvrant la terminologie et les concepts principaux, consultez [Présentation d'Azure Machine Learning](machine-learning-what-is-machine-learning.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Qu'est-ce que Machine Learning Studio ?**

Machine Learning Studio est un environnement de banc d’essai accessible via un navigateur Web. Machine Learning Studio héberge une palette de modules avec une interface de composition visuelle vous permettant de créer un flux de travail de science des données de bout en bout sous la forme d’une expérience.

Pour plus d’informations sur Machine Learning Studio, consultez [Présentation de Machine Learning Studio](machine-learning-what-is-ml-studio.md).

**Qu'est-ce que le service API Machine Learning ?**

Le service d’API Machine Learning vous permet de déployer des modèles prédictifs tels que ceux intégrés dans Machine Learning Studio sous forme de services web évolutifs tolérants aux erreurs. Les services Web créés par le service API Machine Learning sont des API REST qui fournissent une interface pour les communications entre les applications externes et vos modèles analytiques prédictifs.

Pour plus d’informations, voir [Connexion à un service Web Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Où sont répertoriés mes services web classiques ? Où sont répertoriés mes nouveaux services web basés sur Azure Resource Manager ?**

Les services web classiques sont répertoriés dans [Machine Learning Studio](http://studio.azureml.net) dans l’onglet des services web. Les nouveaux services web basés sur Azure Resource Manager sont répertoriés sur le portail des [services web Microsoft Azure Machine Learning](https://services.azureml.net/). Il n’existe aucune liste croisée.

## Questions sur le service web Microsoft Azure Machine Learning

**À quoi servent les services web Azure ML Web ?**

Grâce au service web Microsoft Azure Machine Learning, une application externe peut communiquer avec le modèle de notation de workflow Machine Learning et ce, en temps réel. Un appel du service web Machine Learning renvoie les résultats d’une prédiction à une application externe. Pour créer cet appel, vous transmettez la clé API précédemment créée lors du déploiement du service web. Le service web Machine Learning est basé sur l’architecture REST, souvent choisie pour les projets de programmation web.

Microsoft Azure Machine Learning propose deux types de service :

* Service de requête-réponse (Request-Response Service, RRS) : service hautement évolutif, à faible latence, qui constitue une interface pour les modèles sans état créés et déployés à partir de Machine Learning Studio.
* Service d’exécution de lots (Batch Execution Service, BES) : service asynchrone qui effectue la notation d’un lot pour les enregistrements de données.

Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service web. Vous pouvez par exemple écrire une application en C#, R ou Python à l’aide de l’exemple de code généré lors du déploiement du service Web.

L’exemple de code est disponible sur : la page d’utilisation du service web dans le portail des services web Azure Machine Learning, la page d’aide de l’API dans le tableau de bord du service web dans Machine Learning Studio.

Vous pouvez aussi utiliser l’exemple de classeur Microsoft Excel créé pour vous (également disponible dans le tableau de bord du service Web dans Studio).

**Quelles sont les principales mises à jour liées aux nouveaux services web Azure ML ?**

Pour plus d’informations sur les nouveaux services web Azure Machine Learning, reportez-vous à la [documentation connexe](machine-learning-whats-new.md).

## Questions sur Machine Learning Studio

### Création d’une expérience

**Existe-t-il un contrôle de version ou une intégration Git pour les graphiques d’expérience ?**

Non, cependant Machine Learning Studio conserve chaque itération d’une expérience, qui ne peut pas être modifiée par d’autres utilisateurs. Pour plus d’informations, voir [Gestion des itérations d’expériences dans Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).


### Déploiement d’une expérience

**Puis-je déployer une expérience prédictive en tant que nouveau service web (basé sur Azure Resource Manager) si je l’ai déjà déployée en tant que service web classique ?**

Non, vous ne pouvez pas déployer une expérience qui a déjà été déployée auparavant en tant que service web classique. Vous devez créer une nouvelle expérience prédictive et la déployer.


### Importation et exportation de données pour Machine Learning

**Quelles sources de données sont prises en charge par Machine Learning ?**

Les données peuvent être chargées dans l’expérience Machine Learning Studio de l’une des trois manières suivantes : en chargeant un fichier local sous forme de jeu de données, en utilisant un module pour importer des données provenant de services de données cloud, ou en important un jeu de données enregistré depuis une autre expérience. Pour en savoir plus sur les formats de fichiers pris en charge, voir [Importation de vos données d’apprentissage Azure Machine Learning Studio depuis différentes sources de données](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Quelle est la taille maximale du jeu de données de mes modules ?

Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module accepte plusieurs entrées, le total de toutes les tailles d’entrée est de 10 Go. L'échantillonnage de jeux de données plus importants par le biais de requêtes Hive ou de requêtes de base de données SQL ou via un prétraitement Learning by Counts avant l'ingestion est également pris en charge.

Les types de données suivants peuvent être développés en jeux de données plus importants lors de la normalisation des fonctionnalités et sont limités à moins de 10 Go :

- Partiellement alloué
- Par catégorie
- Chaînes
- Données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

- modules de recommandation
- module SMOTE
- modules de script : R, Python, SQL
- modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing.
- la validation croisée, le réglage des hyperparamètres de modèle, la régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé.

Pour les tailles de jeux de données supérieures à quelques gigaoctets, la méthode conseillée consiste à télécharger les données sur Azure Storage ou Base de données SQL (Azure), ou à utiliser HDInsight, au lieu d'effectuer un téléchargement directement à partir d'un fichier local.


####<a id="UploadLimit"></a>Quelles sont les limites de téléchargement de données ?
Pour les tailles de jeux de données supérieures à quelques gigaoctets, téléchargez les données sur Azure Storage ou Base de données SQL Azure, ou utilisez HDInsight, au lieu d'effectuer un téléchargement directement à partir d'un fichier local.

**Puis-je lire les données à partir d’Amazon S3 ?**

Si vous avez une petite quantité de données et que vous souhaitez l’exposer via une URL http, vous pouvez utiliser le module [Reader](reader). Transférez les grandes quantités de données vers Azure Storage, puis utilisez le module [Reader](reader) pour les importer dans votre expérience.
<!--
<SEE CLOUD DS PROCESS>
-->

**Existe-t-il une capacité d’entrée d’image intégrée ?**

Découvrez la capacité d’entrée d’image dans la référence [Importer des images][image-reader].

### Modules

**L’algorithme, la source de données, le format de données ou l’opération de transformation des données que je recherche ne sont pas disponibles dans Azure Machine Learning Studio. Quelles sont mes options ?**

Vous pouvez consulter le [forum des commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231) (en anglais) pour visualiser les demandes de fonctionnalités dont nous effectuons le suivi. Ajoutez votre vote à une requête si une fonctionnalité que vous recherchez a déjà fait l’objet d’une requête. Si la fonctionnalité que vous recherchez n’existe pas, créez une nouvelle requête. Vous pouvez également afficher le statut de votre demande dans ce forum. Nous suivons cette liste de près et mettons à jour régulièrement le statut de disponibilité des fonctionnalités. Par ailleurs, la prise en charge intégrée pour R et Python permet de créer des transformations personnalisées en fonction des besoins.


**Puis-je importer mon code existant dans Machine Learning Studio ?**

Oui. Vous pouvez importer votre code R ou Python existant dans Machine Learning Studio, l’exécuter au sein de la même expérience avec Azure Machine Learning, puis déployer la solution en tant que service web via Azure Machine Learning. Pour plus d’informations, voir [Prolongez votre expérience avec R](machine-learning-extend-your-experiment-with-r.md) et [Exécution des scripts d’apprentissage automatique Python dans Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**Est-il possible d’utiliser [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language), par exemple, pour définir un modèle ?**

Non, cela n’est pas pris en charge, mais du code R ou Python personnalisé peut être utilisé pour définir un module.

**Combien de modules puis-je exécuter en parallèle dans mon expérience ?**

Vous pouvez exécuter jusqu’à quatre modules en parallèle dans une expérience.


### Traitement des données

**Existe-t-il une possibilité de visualiser les données (autre que les visualisations R) de manière interactive au sein de l’expérience ?**

En cliquant sur la sortie d’un module, vous pouvez visualiser les données et obtenir des statistiques.

**Lors de l’aperçu des résultats ou des données dans le navigateur, le nombre de lignes et de colonnes est limité. Pourquoi ?**

Dans la mesure où les données sont transmises au navigateur et peuvent être volumineuses, la taille des données est limitée afin d’éviter de ralentir Machine Learning Studio. Pour visualiser les données/résultats, il est préférable de télécharger les données et d’utiliser Excel ou un autre outil.

### Algorithmes

**Quels sont les algorithmes ML existants pris en charge dans Machine Learning Studio ?**

Machine Learning Studio fournit des algorithmes de pointe tels que les arbres de décision optimisés évolutifs, les systèmes de recommandation bayésiens, les réseaux neuronaux profonds et les jungles de décision développés chez Microsoft Research. Des modules d’apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. Machine Learning Studio prend en charge les algorithmes d’apprentissage automatique pour la classification, la régression et le clustering multiclasses et binaires. Consultez la liste complète des [Modules de Machine Learning][machine-learning-modules].

**Suggérez-vous automatiquement le bon algorithme Machine Learning à utiliser pour mes données ?**

Non. Toutefois, Machine Learning Studio propose plusieurs façons de comparer les résultats de chaque algorithme afin de déterminer le plus approprié dans votre cas.

**Y a-t-il des instructions sur le choix d’un algorithme plutôt qu’un autre pour les algorithmes fournis ?** Voir [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

**Les algorithmes fournis sont-ils écrits en Python ou en R ?**

Non. Ces algorithmes sont principalement écrits dans des langages compilés pour obtenir de meilleures performances.

**Des détails relatifs aux algorithmes sont-ils disponibles ?**

La documentation présente des informations sur les algorithmes, et les paramètres fournis pour le paramétrage sont décrits afin d’optimiser l’algorithme pour votre usage.

**Existe-t-il une prise en charge pour l’apprentissage en ligne ?**

Non. Actuellement, seul le recyclage par programme est pris en charge.

**Est-il possible de visualiser les couches d’un modèle de réseau neuronal à l’aide du module intégré ?**

Non.

**Puis-je créer mes propres modules en C# ou un autre langage ?**

Actuellement, les modules personnalisés peuvent uniquement être créés en R.

### Module R

**Quels packages R sont disponibles dans Machine Learning Studio ?**

Machine Learning Studio prend actuellement en charge plus de 400 packages CRAN R. Voici la [liste actuelle](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de tous les packages inclus. Pour découvrir comment récupérer vous-même cette liste, voir [Prolongez votre expérience avec R](machine-learning-extend-your-experiment-with-r.md). Si le package souhaité ne figure pas dans cette liste, indiquez son nom sur le [forum de commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231).

**Est-il possible de créer un module R personnalisé ?**

Oui. Consultez [Créer des modules R personnalisés dans Microsoft Azure Machine Learning](machine-learning-custom-r-modules.md) pour plus d’informations.

**Existe-t-il un environnement REPL pour R ?**

Non. Il n’existe aucun environnement REPL pour R dans Studio.

### Module Python

**Est-il possible de créer un module Python personnalisé ?**

Pas pour l’instant, mais vous pouvez utiliser un ou plusieurs modules [Exécuter le script Python][python] pour obtenir le même résultat.

**Existe-t-il un environnement REPL pour Python ?**

Vous pouvez utiliser les Notebooks Jupyter dans Machine Learning Studio. Pour plus d’informations, voir [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) (Présentation des notebooks Jupyter dans Azure Machine Learning Studio).

## Service Web

###Reformation de modèles par programme

**Comment reformer les modèles Azure Machine Learning par programme ?**

Utilisez les API de reformation. Pour plus d’informations, voir [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md). Un exemple de code est également disponible sur la page [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Démonstration de reformation Microsoft Azure Machine Learning).

### Créer

**Puis-je déployer le modèle localement ou dans une application sans connexion Internet ?**

Non.


**Existe-t-il une latence de référence attendue pour tous les services Web ?**

Consultez les [Limites d’abonnement Azure](../azure-subscription-service-limits.md)

### Utilisation

**Quand ai-je avantage à exécuter mon modèle prédictif en tant que service d’exécution de lots plutôt qu’un service Web requête-réponse ?**

Le service de requête-réponse (RRS, Request-Response Service) est un service web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et déployés à partir de l’environnement Expérimentation. Le service d’exécution de lots (BES, Batch Execution Service) est un service de notation asynchrone des enregistrements de données. L’entrée de données pour le service BES est similaire à celle utilisée dans le service RRS. La différence principale réside dans le fait que le service BES lit un bloc d'enregistrements à partir de diverses sources telles que le service BLOB et de tables dans Azure, Base de données SQL Azure, HDInsight (requêtes Hive) et les sources HTTP. Pour plus d’informations, consultez [Utilisation des service Web Microsoft Azure Machine Learning](machine-learning-consume-web-services.md).

**Comment puis-je mettre à jour le modèle pour le service Web déployé ?**

Pour mettre à jour un modèle prédictif pour un service déjà déployé, il suffit de modifier et de réexécuter l’expérience utilisée pour créer et enregistrer le modèle formé. Une fois qu’une nouvelle version du modèle formé est disponible, Machine Learning Studio vous demande si vous voulez mettre à jour votre service web. Pour plus d’informations sur la mise à jour d’un service web déployé, voir [Déploiement d’un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Vous pouvez également utiliser les API de recyclage. Pour plus d'informations, consultez la page [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md). Un exemple de code est également disponible sur la page [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Démonstration de reformation Microsoft Azure Machine Learning).

**Comment puis-je surveiller mon service web déployé en production ?**

Une fois qu’un modèle prédictif a été déployé, vous pouvez le surveiller à partir du portail Azure Classic. Chaque service déployé dispose de son propre tableau de bord, à partir duquel vous pouvez consulter les informations de surveillance correspondant à ce service. Pour plus d’informations sur la gestion de vos services web déployés, voir [Gestion d’un espace de travail Azure Machine Learning](machine-learning-manage-workspace.md).

**Existe-t-il un endroit où je peux voir le résultat de mon service RRS/BES ?**

Pour les enregistrements de ressources, c'est généralement dans la réponse du service web que vous voyez le résultat. Vous pouvez également l'écrire dans Azure Blob Storage. Pour les environnements d'initialisation, la sortie est écrite dans un objet blob par défaut. Vous pouvez également écrire la sortie dans une base de données ou une table à l’aide du module [Exporter les données][export-data].

**Puis-je créer des services web uniquement à partir de modèles créés dans Machine Learning Studio ?**

Non. Vous pouvez également créer des services web directement à partir de Notebooks Jupyter et RStudio.

**Où puis-je trouver plus d'informations sur les codes d'erreur ?**

Pour obtenir la liste des codes d’erreur et leur description, voir [Machine Learning Module Error Codes](https://msdn.microsoft.com/library/azure/dn905910.aspx) (Codes d’erreur des modules Machine Learning).

## Extensibilité

**Quelle est l’évolutivité du service Web ?**

Actuellement, le point de terminaison par défaut est doté de 20 demandes RR simultanées par point de terminaison. Vous pouvez l’étendre à 200 demandes simultanées par point de terminaison, et vous pouvez étendre chaque service web à 10 000 points de terminaison par service web, comme indiqué dans l’article [Mise à l’échelle des points de terminaison des API](machine-learning-scaling-endpoints.md). Pour BES, chaque point de terminaison permet de traiter 40 demandes simultanées. Au-delà de 40 demandes, les demandes supplémentaires sont mises en file d’attente. Ces requêtes en file d’attente sont exécutées automatiquement au fur et à mesure que la file diminue.


**Les travaux R sont-ils répartis entre les nœuds ?**

Non.


**Quelle quantité de données puis-je utiliser pour l'apprentissage ?**

Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module prend plus d’une entrée, la taille totale de toutes les entrées est de 10 Go. Vous pouvez également échantillonner des jeux de données plus volumineux par le biais de requêtes Hive ou Base de données Azure SQL, ou par l’intermédiaire d’un prétraitement avec des modules [Apprentissage à l’aide de compteurs][counts] avant l’ingestion.

Les types de données suivants peuvent être développés en jeux de données plus importants lors de la normalisation des fonctionnalités et sont limités à moins de 10 Go :

- partiellement alloué
- par catégorie
- chaînes
- données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

- modules de recommandation
- module SMOTE
- modules de script : R, Python, SQL
- modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing.
- La validation croisée, le réglage des hyperparamètres de modèle, la régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé.

Pour les jeux de données supérieurs à quelques gigaoctets, la méthode conseillée consiste à télécharger les données sur Azure Storage ou la base de données SQL Azure, ou à utiliser HDInsight au lieu d'effectuer un téléchargement directement à partir d'un fichier local.


**Existe-t-il des limitations de taille de vecteurs ?**

Les lignes et colonnes sont limitées selon la limitation Max Int de .NET : 2 147 483 647.

**La taille de la machine virtuelle utilisée pour exécuter le service web peut-elle être ajustée ?**

Non.

## Sécurité et disponibilité

**Qui a accès au point de terminaison http pour le service web par défaut ? Comment puis-je limiter l’accès au point de terminaison ?**

Après le déploiement d’un service web, un point de terminaison par défaut est créé pour ce service. Le point de terminaison par défaut peut être appelée à l'aide de sa clé d'API. Il est possible d'ajouter des points de terminaison supplémentaires avec leurs propres clés à partir du portail Azure Classic ou par le biais d'un programme à l'aide des API de gestion des services web. Des clés d’accès sont nécessaires pour effectuer des appels au service web. Pour plus d’informations, voir [Connexion à un service Web Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Que se passe-t-il si mon compte de stockage Azure est introuvable ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires lors de l’exécution du flux de travail. Ce compte de stockage est fourni à Machine Learning Studio lors de la création d’un espace de travail. Une fois l’espace de travail créé, si le compte de stockage est supprimé et ne peut plus être trouvé, l’espace de travail cesse de fonctionner et toutes les expériences de cet espace de travail échouent.

Si vous avez supprimé par inadvertance le compte de stockage, vous devez recréer ce compte de stockage avec exactement le même nom, dans la même région que celui que vous avez supprimé. Après cela, effectuez une nouvelle synchronisation de la clé d’accès.


**Que se passe-t-il si ma clé d’accès au compte de stockage n’est pas synchronisée ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires lors de l’exécution du flux de travail. Ce compte de stockage est fourni à Machine Learning Studio lors de la création d’un espace de travail et les clés d’accès sont associées à cet espace de travail. Si les clés d’accès sont modifiées après la création de l’espace de travail, celui-ci ne pourra plus accéder au compte de stockage. Il cessera de fonctionner, et toutes les expériences dans cet espace de travail échoueront.

Si vous avez modifié les clés d’accès du compte de stockage, resynchronisez-les dans l’espace de travail à l’aide du portail Azure Classic.


## Azure Marketplace

Voir [Publication et utilisation d’applications Machine Learning sur Azure Marketplace : forum aux questions](machine-learning-marketplace-faq.md).

## Support et formations

**Où puis-je obtenir des formations pour Azure Machine Learning ?**

Le [Centre de documentation Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) héberge des didacticiels vidéo ainsi que des procédures. Ces derniers présentent les services et passent en revue le cycle de vie de science des données lié à l’importation et au nettoyage des données, à la conception de modèles prédictifs et à leur déploiement en production avec Azure Machine Learning.

Nous ajoutons en permanence de nouveaux documents au Centre Machine Learning. Pour obtenir des documents supplémentaires sur le Centre Machine Learning, vous pouvez envoyer des demandes à partir du [forum des commentaires des utilisateurs](https://windowsazure.uservoice.com/forums/257792-machine-learning) (en anglais).

Vous pouvez également rechercher des formations sur [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Comment puis-je bénéficier d’un support pour Azure Machine Learning ?**

Pour obtenir un support technique pour Azure Machine Learning, accédez au [Support Azure](/support/options/) et sélectionnez **Machine Learning**.

Azure Machine Learning dispose également d'un forum communautaire sur MSDN, où vous pouvez poser des questions en rapport avec Azure Machine Learning. Le forum est géré par l'équipe d'Azure Machine Learning. Visitez le [Forum Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## Questions sur la facturation

**Comment fonctionne la facturation dans Machine Learning ?**

Le service Azure Machine Learning comporte deux composantes : Machine Learning Studio et les services web Machine Learning.

Lorsque vous évaluez Machine Learning Studio, vous pouvez utiliser le niveau de facturation gratuit. Le niveau Gratuit vous permet également de déployer un service web classique avec une capacité limitée.

Une fois que vous estimez qu’Azure Machine Learning répond à vos besoins, vous pouvez vous inscrire au niveau Standard. Pour vous inscrire, vous devez disposer d’un abonnement à Microsoft Azure.

Dans le niveau Standard, vous êtes facturé mensuellement pour chaque espace de travail que vous définissez dans Machine Learning Studio. Lorsque vous exécutez une expérience dans le studio, vous êtes facturé selon les ressources de calcul nécessaires à son exécution. Lorsque vous déployez un service web classique, les transactions et les heures de calcul sont facturées sur la base d’un paiement à l’utilisation.

Les nouveaux services web Machine Learning incluent des profils de facturation offrant une plus grande prévisibilité des coûts. La tarification par niveau offre des taux avec remise aux clients ayant besoin d’une capacité importante.

Lorsque vous créez un profil, vous acceptez un coût fixe associé à une quantité incluse d’heures de calcul et de transactions d’API. Si vous avez besoin de davantage de quantités incluses, vous pouvez ajouter des instances supplémentaires à votre plan. Si vous avez besoin de beaucoup plus de quantités incluses, vous pouvez choisir un plan de niveau supérieur qui en offre davantage à un meilleur tarif.

Une fois les quantités incluses dans les instances existantes épuisées, toute utilisation supplémentaire est facturée au tarif de dépassement associé au niveau de profil de facturation.

Remarque : les quantités incluses sont réallouées tous les 30 jours. Les quantités incluses non utilisées ne sont pas reportées sur la période suivante.

Pour plus d’informations sur la facturation et la tarification, consultez [Tarification Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**Une version d’évaluation de Machine Learning est-elle disponible ?**

 Il existe une option d’abonnement gratuit pour Azure Machine Learning (pour plus d’informations, voir [Tarification de Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/)), ainsi qu’une version d’évaluation rapide de 8 heures pour Machine Learning Studio (connectez-vous à [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2) pour accéder à cette version d’évaluation).
 
 En outre, lorsque vous vous inscrivez pour une version d’évaluation gratuite d’Azure, vous pouvez essayer n’importe quel service Azure pendant un mois. Pour plus d’informations sur la version d’évaluation gratuite d’Azure, voir [Forum Aux Questions sur la version d’évaluation gratuite d’Azure](/pricing/free-trial-faq/).

**Qu’est-ce qu’une transaction ?**

Une transaction correspond à un appel d’API pris en charge par Azure Machine Learning. Les transactions provenant d’appels de service de requête-réponse (RRS) et de service d’exécution de lots (BES) sont regroupées et facturées selon votre profil de facturation.

**Puis-je utiliser les quantités de transactions incluses dans un plan à la fois pour les transactions de services RSS et BES ?**

Oui, les transactions de vos RRS et BES sont regroupées et facturées selon votre profil de facturation.

**Qu’est-ce qu’une heure de calcul API ?**

Une heure de calcul API est l’unité de facturation correspondant au temps nécessaire à l’exécution des appels d’API à l’aide des ressources de calcul ML. Tous vos appels sont regroupés pour la facturation.

**Combien de temps prend un appel d’API de production type ?**

Les délais d’appel des API de production peuvent varier de façon importante. Ils sont généralement compris entre plusieurs centaines de millisecondes et quelques secondes. Ils peuvent toutefois nécessiter plusieurs minutes selon la complexité du traitement des données et du modèle d’apprentissage automatique. La meilleure façon d’évaluer les délais d’appel des API de production consiste à définir un modèle sur le service Machine Learning.

**Qu’est-ce qu’une heure de calcul Studio ?**

Une heure de calcul Studio est l’unité de facturation correspondant à la durée cumulée d’utilisation des ressources de calcul dans Studio par vos expériences.

**Dans les nouveaux services web, à quoi sert le niveau de développement/test ?**

Les nouveaux services web Azure ML proposent différents niveaux que vous pouvez utiliser pour configurer votre profil de facturation. Le niveau de développement/test fournit des quantités incluses limitées qui vous permettent de tester votre expérience en tant que nouveau service web à titre gratuit. Vous avez la possibilité de tester le produit pour voir comment il fonctionne.

**Existe-t-il des frais distincts pour le stockage ?**

Le niveau Gratuit de Machine Learning n’autorise pas de stockage distinct. Le niveau Standard de Machine Learning nécessite que les utilisateurs aient un compte de stockage Azure. Le stockage Azure est [facturé séparément](https://azure.microsoft.com/pricing/details/storage/).

**Comment Machine Learning prend-il en charge le travail à disponibilité élevée ?**

Les délais d’appel des API de production peuvent varier de façon importante. Ils sont généralement compris entre plusieurs centaines de millisecondes et quelques secondes. Ils peuvent toutefois nécessiter plusieurs minutes selon la complexité du traitement des données et du modèle d’apprentissage automatique. La meilleure façon d’évaluer les délais d’appel des API de production consiste à définir un modèle sur le service Machine Learning.

**Sur quel type de ressources de calcul mes appels d’API de production seront-ils exécutés ?**

Le service Machine Learning est multilocataire. Les ressources de calcul réelles utilisées sur le service principal varient et sont optimisées pour les performances et la prévisibilité.

### Gestion des nouveaux services web 

**Que se passe-t-il si je supprime mon profil ?**

Le profil est supprimé de votre abonnement et vous êtes facturé au prorata de votre utilisation.

Remarque : vous ne pouvez pas supprimer un profil qui est utilisé par un service web. Pour supprimer le profil, vous devez en attribuer un nouveau au service web ou supprimer ce dernier.

**Qu’est-ce qu’une instance de plan ?**

Une instance de plan est une unité de quantités incluses que vous pouvez ajouter à votre profil de facturation. Lorsque vous sélectionnez un niveau de facturation pour votre profil de facturation, il contient une seule instance. Si vous avez besoin de davantage de quantités incluses, vous pouvez ajouter des instances du niveau de facturation sélectionné à votre profil.

**Combien d’instances de plan puis-je ajouter ?**

Vous ne pouvez avoir qu’une seule instance de niveau développement/test par abonnement.

Pour les niveaux S1, S2 et S3, vous pouvez en ajouter autant que nécessaire.

Remarque : selon l’utilisation que vous prévoyez, il peut s’avérer plus rentable de passer à un niveau plus élevé de quantités incluses plutôt que d’ajouter des instances au niveau actuel.

**Que se passe-t-il lorsque je change de niveau de profil (vers le niveau supérieur/inférieur) ?**

L’ancien profil est supprimé et l’utilisation en cours est facturée au prorata. Un nouveau profil comprenant la totalité des quantités incluses correspondant au niveau supérieur/inférieur est créé pour le reste de la période.

Remarque : les quantités incluses sont allouées par période. Les quantités non utilisées ne sont pas reportées.

**Que se passe-t-il lorsque j’augmente les instances d’un profil ?**

Les quantités sont ajoutées au prorata. L’opération peut prendre 24 heures.

**Que se passe-t-il lorsque je supprime une instance d’un profil ?**

L’instance est supprimée de votre abonnement et vous êtes facturé au prorata de votre utilisation.


### Inscription aux nouveaux plans de services web

**Comment s’inscrire à un plan ?**

Il existe deux manières de créer des profils de facturation.

Lorsque vous déployez un nouveau service web pour la première fois, vous pouvez choisir un profil existant ou en créer un.

Les profils créés de cette façon se situent dans votre région par défaut et votre service web sera déployé dans celle-ci.

Si vous souhaitez déployer des services dans des régions autres que votre région par défaut, nous vous recommandons de définir vos profils de facturation avant de déployer votre service.

Dans ce cas, connectez-vous au portail de services web Azure Machine Learning et accédez à la page dédiée aux profils. Vous pourrez alors ajouter et supprimer des profils, mais aussi modifier des profils existants.

**Quel profil devrais-je choisir pour commencer ?**

Nous vous recommandons de commencer par le niveau Standard S1 et de surveiller l’utilisation de votre service. Si vous estimez que vous utilisez vos quantités incluses rapidement, ajoutez des instances ou optez pour un niveau supérieur afin d’obtenir un meilleur tarif. Vous pouvez ajuster votre profil de facturation en fonction de vos besoins tout au long de votre cycle de facturation.

**Dans quelles régions les nouveaux profils sont-ils disponibles ?**

Les nouveaux profils de facturation sont disponibles dans les trois régions de production dans lesquelles nous prenons en charge les nouveaux services web :

* Centre-Sud des États-Unis
* Europe de l'Ouest
* Asie du Sud-Est

**J’ai des services web dans plusieurs régions. Dois-je avoir un profil pour chaque région ?**

Oui. La tarification des profils varie selon les régions. Si vous déployez un service web dans une autre région, vous devrez lui affecter un profil spécifique pour cette région.

### Nouveaux services web : dépassements

**Comment vérifier si mon utilisation du service web est en dépassement ?**

Vous pouvez consulter l’utilisation de tous vos profils sur la page qui leur est dédiée dans le portail de services web Azure Machine Learning. Connectez-vous au portail et cliquez sur l’option de menu Abonnements.

Dans les colonnes Transactions et Calcul du tableau, vous pouvez consulter les quantités incluses dans le plan et le pourcentage utilisé.

**Que se passe-t-il lorsque j’ai épuisé les quantités incluses dans le niveau de développement/test ?**

Les services affectés au niveau de développement/test sont arrêtés jusqu'à la période suivante ou vous devez les déplacer vers l’un des niveaux payants.

**Pour les services web classiques et les dépassements des nouveaux services web, comment sont calculés les tarifs pour les charges de travail de demande-réponse (RRS) et d’exécution de lots (BES) ?**

Pour une charge de travail RRS, vous êtes facturé pour chaque appel de transaction API, ainsi que pour le temps de calcul associé à ces demandes. Les coûts relatifs aux transactions API de production RRS sont calculés comme suit : nombre total d’appels d’API effectués, multiplié par le tarif par milliers de transactions (au prorata par transaction individuelle). Les coûts relatifs aux heures de calcul API de production RRS sont calculés comme suit : durée d’exécution de chaque appel d’API, multiplié par le nombre total de transactions API, multiplié par le tarif par heure de calcul API de production.

Par exemple, avec le niveau de dépassement Standard S1, le coût de 1 000 000 transactions API d'une durée de 0,72 seconde chacune est calculée comme suit : (1 000 000 * 0,50 $/1 000 transactions API), soit un tarif pour les transactions API de production de 500 $ et (1 000 000 * 0,72 s * 2 $/heure), soit un tarif pour les heures de calcul API de production de 400 $, pour un total de 900 $.

Pour une charge de travail BES, vous êtes facturé de la même manière. Toutefois, les coûts de transaction API représentent le nombre de travaux de traitement par lots que vous soumettez, et les coûts de calcul représentent le temps de calcul associé à ces travaux de traitement par lots. Les coûts relatifs aux transactions API de production BES sont calculés comme suit : nombre total de travaux soumis, multiplié par le tarif par milliers de transactions (au prorata par transaction individuelle). Les coûts relatifs aux heures de calcul API de production BES sont calculés comme suit : durée d’exécution de chaque ligne de votre travail, multiplié par le nombre total de lignes de votre travail, multiplié par le nombre total de travaux, multiplié par le tarif par heure de calcul API de production. Lorsque vous utilisez la calculatrice Machine Learning, le compteur de transactions représente le nombre de travaux que vous planifiez de soumettre, et la durée par transaction représente la durée combinée nécessaire à l'exécution de toutes les lignes de chaque travail.

Par exemple, avec le niveau de dépassement Standard S1, si vous soumettez 100 travaux par jour, chacun étant constitué de 500 lignes prenant 0,72 secondes chacune, le coût mensuel des dépassements est le suivant : (100 travaux par jour = 3 100 travaux/mois * 0,50 $/1 000 transactions API), soit des coûts de transaction API de production de 1,55 $ et (500 lignes * 0,72 s * 3 100 travaux * 2 $/heure) des heures de calcul API de production de 620 $, soit un total de 621,55 $.

### Services web Azure ML classiques

**Le paiement à l’utilisation est-il toujours disponible ?** Oui, les services web classiques sont toujours disponibles dans Azure Machine Learning.

### Niveaux Gratuit et Standard d’Azure Machine Learning

**Qu’est-ce qui est inclus dans le niveau Gratuit d’Azure Machine Learning ?**

Le niveau Gratuit d’Azure Machine Learning offre une introduction complète à Azure Machine Learning Studio. Il vous suffit d'un compte Microsoft pour vous inscrire. Le niveau Gratuit inclut un accès gratuit à un espace de travail Azure Machine Learning Studio par [compte Microsoft](https://www.microsoft.com/account/default.aspx). Il permet d’utiliser jusqu’à 10 Go de stockage et de configurer les modèles en tant qu’API intermédiaires. Les charges de travail du niveau Gratuit ne sont couvertes par aucun contrat de niveau de service et sont uniquement destinées au développement et à une utilisation personnelle. Les charges de travail du niveau Gratuit ne peuvent pas accéder aux données en se connectant à un serveur SQL Server local.

**Qu’est-ce qui est inclus dans le niveau et les plans Standard d'Azure Machine Learning ?**

Le niveau Standard d’Azure Machine Learning est une version de production payante d’Azure Machine Learning Studio. Les frais mensuels du service Azure ML Studio sont facturés par espace de travail et par mois et au prorata pour les mois incomplets. Les heures d'expérimentation Azure ML Studio sont facturées par heure de calcul pour l'expérimentation active. La facturation est ajustée pour les heures incomplètes.

Le service API d’Azure ML est facturé selon le type de service web : classique ou nouveau.

Les frais suivants sont regroupés par espace de travail dans votre abonnement.

* Abonnement à l’espace de travail Machine Learning : l’abonnement à l’espace de travail Machine Learning est un montant mensuel qui permet d’accéder à un espace de travail ML Studio. Il est nécessaire pour pouvoir exécuter des expériences dans le studio et pour pouvoir utiliser les API de production.
* Heures d'expérience Studio : ce compteur regroupe tous les frais de calcul cumulés suite à l'exécution d'expériences dans ML Studio et d'appels d'API de production dans l'environnement intermédiaire.
* Accédez aux données en vous connectant à un serveur SQL Server local dans vos modèles pour votre formation et votre évaluation.
* Pour les services web classiques :
	* Heures calcul des API de production : ce compteur inclut les frais de calcul cumulés par les services web exécutés en production.
	* Transactions d'API de production (par milliers) : ce compteur inclut les frais cumulés par appel à votre service web de production.

Mis à part les frais ci-dessus, dans le cas des nouveaux services web, les frais sont regroupés dans le profil sélectionné :

* Profil d’API Standard S1/S2/S3 (unités) : ce compteur représente le type d’instance sélectionné pour les nouveaux services web.
* Heures de calcul d’API de dépassement Standard S1/S2/S3 : ce compteur inclut les frais de calcul générés par les nouveaux services web en cours d’exécution en production après épuisement des quantités incluses dans les instances existantes. L’utilisation supplémentaire est facturée au tarif de dépassement associé au niveau du plan S1/S2/S3.
* Transactions d’API de dépassement Standard S1/S2/S3 (par milliers) : ce compteur inclut les frais générés par appel à votre nouveau service web de production après épuisement des quantités incluses dans les instances existantes. L’utilisation supplémentaire est facturée au tarif de dépassement associé au niveau du plan S1/S2/S3.
* Heures de calcul des API Quantité incluse : avec les nouveaux services web, ce compteur représente la quantité d’heures de calcul d’API incluse.
* Transactions des API Quantité incluse (en milliers) : avec les nouveaux services web, ce compteur représente la quantité incluse de transactions d’API.


**Comment s’inscrire au niveau Gratuit d’Azure ML ?**

Vous avez besoin uniquement d'un compte Microsoft. Accédez à la [page d’accueil Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) et cliquez sur le bouton **Démarrer maintenant**. Connectez-vous à votre compte Microsoft. Un espace de travail du niveau Gratuit est alors créé pour vous. Vous pouvez immédiatement commencer à explorer et à créer des expériences Machine Learning.

**Comment s’inscrire au niveau Standard d’Azure ML ?**

Vous devez d’abord avoir accès à un abonnement Azure pour créer un espace de travail ML Standard. Vous pouvez souscrire un abonnement Azure gratuit de 30 jours, puis migrer vers un abonnement Azure payant ou acheter directement ce dernier. Vous pouvez ensuite créer un espace de travail Machine Learning à partir du portail de gestion Microsoft Azure Classic après avoir obtenu l’accès à l’abonnement. Veuillez consulter [les instructions pas à pas](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

En outre, vous pouvez être invité par un propriétaire à accéder à son espace de travail Standard ML.

**Puis-je spécifier mon propre compte de stockage d’objets blob Azure à utiliser avec le niveau Gratuit ?**

Non, le niveau Standard est équivalent à la version du service Machine Learning qui était disponible avant l’introduction des niveaux.

**Puis-je déployer mes modèles d'apprentissage automatique en tant qu'API dans le niveau Gratuit ?**

Oui. Avec le niveau Gratuit, vous pouvez faire fonctionner les modèles d’apprentissage automatique dans les services API intermédiaires. Pour que le service API intermédiaire soit mis en production, et pour obtenir un point de terminaison de production pour le service fonctionnel, vous devez utiliser le niveau Standard.

**Quelle est la différence entre l’essai gratuit d’Azure et le niveau Gratuit d’Azure Machine Learning ?**

[L’essai gratuit de Microsoft Azure](https://azure.microsoft.com/free/) offre des crédits qui peuvent être appliqués à n’importe quel service Azure pendant un mois. Le niveau Gratuit d’Azure Machine Learning offre spécifiquement un accès continu au service Azure Machine Learning pour les charges de travail autres que de production.

**Comment migrer l'expérience du niveau Gratuit au niveau Standard ?**

Pour copier vos expériences du niveau Gratuit vers le niveau Standard :

1.	Connectez-vous à Azure Machine Learning Studio. Assurez-vous que les espaces de travail Gratuit et Standard figurent dans le sélecteur d’espaces de travail dans la barre de navigation supérieure.
2.	Passez à l’espace de travail Gratuit si vous êtes dans l’espace de travail Standard.
3.	Dans la liste des expériences, sélectionnez une expérience à copier, puis cliquez sur le bouton de commande Copier.
4.	Sélectionnez l’espace de travail Standard dans la boîte de dialogue contextuelle, puis cliquez sur le bouton Copier. Tous les jeux de données associés, le modèle formé et les autres éléments sont copiés en même temps que l’expérience dans l’espace de travail Standard.
6.	Vous devez réexécuter l’expérience et republier votre site web dans l’espace de travail Standard.

### Espace de travail Studio

**Y a-t-il différentes factures pour différents espaces de travail ?**

Les frais liés à l’espace de travail sont décomposés pour chaque compteur applicable sur une seule facture.

**Sur quel type de ressources de calcul mes expériences seront-elles exécutées ?**

Le service Machine Learning est multilocataire. Les ressources de calcul réelles utilisées sur le service principal varient et sont optimisées pour les performances et la prévisibilité.

### Accès invité

**Qu’est-ce qu’un accès invité à Azure Machine Learning Studio ?**

L'accès invité est une expérience d'essai limitée qui vous permet de créer et d'exécuter des expériences dans Azure Machine Learning Studio, gratuitement et sans authentification. Les sessions en tant qu’invité ne sont pas persistantes (ne peuvent pas être enregistrées) et sont limitées à 8 heures. D’autres limitations incluent l’absence de prise en charge des langages R et Python, l’absence d’API intermédiaires, ainsi qu’une taille de jeu de données et une capacité de stockage limitées. En comparaison, les utilisateurs qui choisissent de se connecter à un compte Microsoft bénéficient d’un accès complet au niveau Gratuit de Machine Learning Studio décrit ci-dessus, qui inclut un espace de travail persistant et davantage de fonctionnalités complètes. Pour choisir l’expérience Machine Learning gratuite, cliquez sur le bouton **Commencer** sur la page [https://studio.azureml.net](https://studio.azureml.net), puis sélectionnez Accès invité ou Se connecter avec un compte Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

<!---HONumber=AcomDC_0928_2016-->