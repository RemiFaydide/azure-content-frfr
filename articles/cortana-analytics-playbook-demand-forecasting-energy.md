<properties
	pageTitle="Manuel du modèle de solution Microsoft Cortana Intelligence de prévision de la demande d’énergie | Microsoft Azure"
	description="Un modèle de solution avec Microsoft Cortana Intelligence permettant de prévoir la demande en énergie d’une société de service public de distribution d’énergie."
	services="cortana-analytics"
	documentationCenter=""
	authors="ilanr9"
	manager="ilanr9"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="ilanr9;yijichen;garye"/>

# Manuel du modèle de solution Microsoft Cortana Intelligence de prévision de la demande d’énergie  

## Résumé  

Ces dernières années, en fusionnant, l’Internet des objets (IoT), les sources d’énergie alternatives et le Big Data ont généré d’importantes opportunités dans le domaine des services publics et de l’énergie. Dans le même temps, le secteur des services publics et celui de l’énergie ont vu la consommation baisser, et les usagers exiger des solutions qui leur permettraient de mieux contrôler leur consommation. C’est pour cette raison que les sociétés de service public et du secteur des réseaux intelligents doivent absolument innover et se renouveler. En outre, de nombreuses infrastructures servant à la distribution d’électricité et de services publics deviennent obsolètes et sont donc très coûteuses à entretenir et à gérer. Au cours de l’année dernière, l’équipe a travaillé sur un certain nombre de projets dans le domaine de l’énergie. C’est à l’occasion de ces interventions que nous avons rencontré de nombreux cas dans lesquels les fournisseurs de services publics ou de logiciels indépendants (ISV) cherchaient des prévisions relatives à la demande d’énergie future. Ces prévisions jouent un rôle important dans leurs activités actuelles et futures, et sont à la base de différentes études de cas, parmi lesquelles les prévisions de charge à court et à long terme, les échanges et l’équilibrage des charges, l’optimisation des réseaux, etc. Les méthodes Big Data et Advanced Analytics (AA) telles que Machine Learning (ML) sont des activateurs clés servant à générer des prévisions précises et fiables.

Ce manuel regroupe les instructions liées aux analyses et aux activités métier nécessaires à la réussite du développement et du déploiement d’une solution de prévision de demande d’énergie. Ces instructions peuvent aider les services publics, les spécialistes et les ingénieurs de données à mettre en place des solutions de prévision de la demande parfaitement opérationnelles basées sur le cloud. Pour les entreprises qui entament seulement leur évolution vers le Big data et les analyses avancées, une telle solution peut constituer le « fondement » d’une stratégie de réseau intelligent à long terme.

>[AZURE.TIP] Pour télécharger un diagramme offrant une vue d’ensemble de l’architecture de ce modèle, consultez [Architecture du modèle de solution Cortana Intelligence pour la prévision de la demande d'énergie](cortana-analytics-architecture-demand-forecasting-energy.md).

## Vue d'ensemble  

Ce document traite de l’utilisation de Cortana Intelligence, et plus particulièrement d’Azure Machine Learning (AML), dans l’implémentation et le déploiement de solutions de prévision d’énergie du point de vue de l’entreprise, des données et de la technique. Le document se compose de trois parties principales :

1. Présentation de l’entreprise
2. Présentation des données
3. Implémentation technique

La partie **Présentation de l’entreprise** décrit les aspects d’entreprise à connaître et à prendre en compte avant de prendre une décision d’investissement. Elle explique comment appréhender la problématique de l’entreprise pour vous assurer que l’analyse prédictive et l’apprentissage automatique sont efficaces et applicables dans les faits. Ce document explique les principes de base de l’apprentissage automatique et la façon de l’utiliser pour résoudre les problèmes de prévision de la demande en énergie. Il décrit les conditions requises et les critères de qualification d’une étude de cas. Certains exemples d’études de cas et scénarios pratiques sont également fournis.

Les données sont le principal ingrédient de toute solution d’apprentissage. La partie **Présentation des données** de ce document aborde certains aspects relatifs aux données importants. Elle décrit le type de données nécessaire à la prévision des besoins en énergie, les exigences en matière de qualité des données et les sources de données qui existent. Nous y expliquons également comment les données brutes sont utilisées pour préparer les fonctionnalités de données qui régissent la partie modélisation.

La troisième partie du document traite de l’aspect **Implémentation technique** d’une solution. Ingénierie et modélisation sont au cœur du traitement scientifique des données et seront par conséquent examinées en détail. Cette partie présente le concept des services web, qui constituent un vecteur important de déploiement des solutions d’analyse prédictive sur le cloud. Nous y décrivons également une architecture classique de solution de bout en bout opérationnelle.

En outre, le document contient des informations de référence que vous pouvez utiliser pour mieux connaître le domaine et la technologie.

Il est important de noter que nous ne voulons pas aborder dans ce document le traitement scientifique approfondi des données et ses aspects mathématiques et techniques. Vous trouverez ces détails dans la [documentation](http://azure.microsoft.com/services/machine-learning/) et les [blogs](http://blogs.microsoft.com/blog/tag/azure-machine-learning/) Azure ML.

### Public cible   
Ce document est destiné aux employés des services commerciaux et techniques d’une entreprise souhaitant acquérir des connaissances et une bonne compréhension des solutions Machine Learning et de la façon dont elles sont utilisées spécifiquement dans le domaine de la prévision des besoins en énergie.

La lecture de ce document procurera aux spécialistes des données une meilleure connaissance du processus de haut niveau régissant le déploiement d’une solution de prévision des besoins en énergie. Dans ce contexte, il peut également être utilisé pour établir une base de référence et un point de démarrage pour une documentation détaillée avancée.

### Tendances du secteur  
Ces dernières années, en fusionnant, l’IoT, les sources d’énergie alternatives et le Big Data, ont généré d’importantes opportunités dans le secteur des services publics et de l’énergie. Dans le même temps, le secteur des services publics et ceux de l’énergie ont vu la consommation baisser, et leurs usagers exiger des solutions qui leur permettraient de mieux contrôler leur consommation.

De nombreux services publics et sociétés « écoénergétiques » ont innové dans le domaine des [réseaux intelligents](https://en.wikipedia.org/wiki/Smart_grid) en déployant un certain nombre de cas d’utilisation utilisant les données générées par le réseau. Plusieurs études de cas s’articulent autour des caractéristiques intrinsèques de la production d’électricité : elle ne peut être ni accumulée, ni stockée. Par conséquent, ce qui est produit doit être utilisé. Les services publics qui souhaitent gagner en efficacité doivent prévoir leur consommation électrique tout simplement parce que cela leur permet de mieux **équilibrer l’offre et la demande** (ce qui évite de gaspiller de l’énergie et **réduit les émissions de gaz à effet de serre**) et maîtriser leurs coûts.

En matière de coûts, un autre aspect est important, celui du prix. De nouvelles possibilités d’échange entre services publics ont débouché sur nouvelle nécessité : celle qui consiste à **prévoir la demande future et le prix futur de l’électricité**. Les sociétés peuvent ainsi déterminer leurs volumes de production.

Lorsque nous utilisons le terme « intelligent », nous faisons en fait référence à un réseau capable de réunir des informations et d’en tirer des prévisions. Il peut anticiper des variations saisonnières de la consommation, **prévoir les situations de surcharge temporaire et faire des ajustements automatiques**. Grâce au contrôle à distance de la consommation (à l’aide de ces compteurs intelligents), il est possible de traiter les situations de surcharge localisées. **En prévoyant d’abord et en agissant ensuite**, le réseau devient plus intelligent au fil du temps.

Dans le reste de ce document, nous allons nous concentrer sur une famille spécifique d’études de cas traitant des prévisions de la demande en énergie à court et à long terme. Nous travaillons dans ces domaines depuis plusieurs mois et avons acquis une bonne connaissance et des compétences, ce qui nous permet de produire des résultats pour ce secteur. D’autres cas d’utilisation vont être également décrits dans un futur proche.

## Présentation de l’entreprise

### Objectifs de l’entreprise
L’objectif d’**Energy Demo** est de faire la démonstration d’une solution classique d’analyse prédictive et d’apprentissage automatique pouvant être déployée dans un laps de temps très court. Nous nous concentrons plus particulièrement sur l’activation de solutions de prévision de la demande d’énergie, et sa valeur peut donc être rapidement réalisée et exploitée par l’entreprise. Les informations contenues dans ce manuel peuvent aider le client à atteindre les objectifs suivants :
-   mise à profit d’une solution d’apprentissage machine très rapidement,
-   possibilité d’étendre un cas d’utilisation pilote à d’autres cas d’utilisation ou à un domaine plus étendu en fonction des besoins de l’entreprise,
-   Maîtrise rapide du produit Cortana Intelligence Suite

Une fois les objectifs définis, ce manuel a pour but de fournir les connaissances commerciales et techniques qui permettront d’atteindre ces résultats.

### Prévision de charge et de demande d’électricité
Dans le secteur de l’énergie, les prévisions de la demande offrent différents moyens pour résoudre certains problèmes. En fait, la prévision de la demande peut constituer la base de nombreuses études de cas de ce secteur. En règle générale, nous envisageons deux types de prévisions de la demande d’énergie : à court terme et à long terme. Chacun d’eux peut répondre à un objectif distinct et donc, utiliser une approche différente. La principale différence entre les deux est l’horizon de prévision, en d’autres termes, la période du futur à laquelle la prévision s’appliquera.

#### Prévisions de charge à court terme
Dans le contexte de la demande énergétique, la prévision de charge à court terme (STLF) est définie comme la charge cumulée prévue nécessaire dans un avenir proche dans différentes parties du réseau (ou dans l’ensemble du réseau). Dans ce contexte, le court terme définit un horizon temporel compris dans une plage de 1 à 24 heures. Dans certains cas, il peut s’agir d’un horizon à 48 heures. Par conséquent, la prévision STLF est très souvent utilisée dans le cas d’un réseau servant à des activités d’exploitation. Voici quelques exemples de cas d’utilisation avec le paramètre STLF :
-   Équilibrage de l’offre et de la demande
-   Prise en charge des échanges énergétiques
-   Établissement du marché (prix de l’électricité)
-   Optimisation opérationnelle du réseau
-   [Réponse à la demande](https://en.wikipedia.org/wiki/Demand_response)
-   Prévisions de pic de demande
-   Gestion côté demande
-   Équilibrage de charge et prévention des surcharges
-   Prévision de charge à long terme
-   Détection des anomalies et des défaillances
-   Limitation/nivellement des pics

Le modèle STLF est principalement basé sur les données de consommation enregistrées dans un passé proche (jour ou semaine venant de s’écouler) et les prévisions de température constituent un facteur de prévision important. De nos jours, l’obtention de prévisions de température précises pour l’heure et les 24 heures à venir est de moins en moins difficile. Ces modèles sont moins sensibles aux schémas saisonniers et aux tendances de consommation à long terme.

Les solutions de type STLF peuvent également générer un volume important d’appels de prévision (demandes de service), car elles sont appelées sur une base horaire et dans certains cas, la fréquence peut être plus élevée. Il est également très courant de voir des implantations dans lesquelles chaque sous-centrale ou transformateur individuel est représenté comme un modèle autonome et donc, le volume de demandes de prévision est encore plus élevé.

#### Prévision de charge à long terme
L’objectif de la prévision de charge à long terme (LTLF) consiste à prévoir la demande d’électricité à un horizon temporel pouvant s’étendre d’une semaine à plusieurs mois (voire, dans certains cas, plusieurs années). Cet horizon temporel est plus souvent applicable à des études de cas destinées à la planification et l’investissement.

Pour les scénarios à long terme, il est important de disposer de données d’excellente qualité, couvrant plusieurs années (minimum 3 ans). Ces modèles extraient généralement des schémas saisonniers à partir de l’historique des données et utilisent des prévisions externes tels que les schémas météorologiques et climatiques.

Il est important de préciser que plus l’horizon de prévision est éloigné, moins les prévisions sont précises. Il est donc important de mettre en place des intervalles de confiance avec la prévision actuelle, ce qui permet aux hommes de factoriser les possibilités de variation dans leur processus de planification.

Comme le scénario de consommation LTLF correspond principalement à de la planification, nous pouvons nous attendre à des volumes de prévisions plus bas (comparé à STLF). En principe, ces prévisions sont présentées dans des outils d’affichage tels qu’Excel ou PowerBI et peuvent être appelées manuellement par l’utilisateur.

### Prévision à court terme contre prévision à long terme
Le tableau suivant compare les prévisions à long terme et les prévisions à court terme concernant les attributs importants :

|Attribut|Prévision de charge à court terme|Prévision de charge à long terme|
|---|---|---|
|Période de prévision|D’1 heure à 48 heures|De 1 à 6 mois ou plus|
|Granularité des données|Toutes les heures|Toutes les heures ou chaque jour|
|Études de cas classiques|<ul><li>Équilibrage offre/demande</li><li>Recueillir des prévisions pour l’heure</li><li>Réponse à la demande</li></ul>|<ul><li>Planification à long terme</li><li>Planification des ressources réseau</li><li>Planification des ressources</li></ul>|
|Prévisions classiques|<ul><li>Jour ou semaine</li><li>Heure de la journée</li><li>Température toutes les heures</li></ul>|<ul><li>Mois de l’année</li><li>Jour du mois</li><li>Température et météo à long terme</li></ul>|
|Plage de données historiques|Deux ou trois années de données|Cinq à 10 ans de données|
|Précision classique|MAPE* de 5 % ou moins|MAPE* de 25 % ou moins|
|Fréquence de prévision|Générée toutes les heures ou toutes les 24 heures|Générée tous les mois, tous les trimestres ou tous les ans|
*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) : erreur moyenne en pourcentage

Comme nous le voyons dans cette table, il est très important de distinguer les scénarios de prévision à court terme des scénarios à long terme, car ils répondent à des besoins professionnels différents et peuvent se présenter avec des modèles de déploiement et d’utilisation différents.

### Exemple d’étude de cas 1 : systèmes eSmart, optimisation de surcharge
Un des rôles importants du [réseau intelligent](https://en.wikipedia.org/wiki/Smart_grid) consiste à optimiser et ajuster de façon dynamique et continue les modèles de consommation en fonction des changements. La consommation d’énergie peut être affectée par les modifications à court terme provoquées principalement par les fluctuations de température (*par exemple*, lorsque davantage d’électricité est utilisée pour l’air conditionné ou le chauffage). Dans le même temps, la consommation d’énergie est également influencée par les tendances à long terme. Ces dernières peuvent inclure les effets de saisonnalité, les jours fériés, la croissance à long terme de la consommation et même des facteurs économiques tels que l’indice des prix, le prix du pétrole et le produit intérieur brut.

Dans ce cas, [eSmart](http://www.esmartsystems.com/) souhaite déployer des solutions cloud permettant de prédire la propension à se trouver en situation de surcharge dans une sous-centrale du réseau. eSmart souhaite en particulier identifier les sous-centrales susceptibles de connaître des surcharges au cours de l’heure à venir, et pouvoir immédiatement intervenir pour éviter ou résoudre le problème.

Une prévision exacte et rapide nécessite la mise en œuvre de trois modèles prédictifs :
-   Un modèle à long terme permettant de prévoir la consommation d’électricité de chaque sous-centrale au cours des quelques semaines ou mois à venir
-   Un modèle à court terme permettant de prévoir les situations de surcharge sur une sous-centrale donnée au cours de l’heure suivante
-   Un modèle de température offrant des prévisions de la température future dans plusieurs scénarios

L’objectif du modèle à long terme consiste à classer les sous-centrales en fonction de leur propension à la surcharge (compte tenu de leur capacité de transmission électrique) au cours de la semaine ou du mois suivant. Il est ainsi possible de créer une sélection de sous-centrales pouvant servir d’informations d’entrée pour la prévision à court terme. La température étant un facteur de prévision important pour le modèle à long terme, il faut en permanence générer plusieurs prévisions de température, selon plusieurs scénarios et les utiliser comme entrée dans le modèle à long terme. La prévision à court terme est ensuite appelée pour déterminer la sous-centrale susceptible de connaître une surcharge au cours de l’heure suivante.

Les modèles à court et à long terme sont déployés individuellement par chaque sous-centrale. L’exécution pratique de ces modèles nécessite une organisation importante. Pour obtenir une meilleure précision des prévisions à court terme, un modèle granulaire est dédié à chaque heure de la journée. Tous ces modèles sont exécutés à chaque heure et prennent seulement quelques minutes, afin de laisser un temps de réaction suffisant pour prendre des mesures préventives si nécessaire. Cette collection de modèle est actualisée par une refonte périodique avec des données plus récentes.

Vous trouverez d’autres informations sur ce cas d’utilisation [ici](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### Critères de qualification pour l’étude de cas : conditions préalables
Le principal atout de Cortana Intelligence réside dans sa capacité à déployer et à mettre à l’échelle des solutions orientées apprentissage automatique. Elle est conçue pour prendre en charge des milliers de prévisions exécutées simultanément. Elle peut être mise à l’échelle automatiquement pour répondre à d’éventuelles modifications du modèle de consommation. Un des aspects principaux de la solution réside dans la précision et dans les performances de calcul. Par exemple, une société de service public peut être intéressée par une prévision de demande d’énergie précise pour l’heure suivante, et pour chaque heure de la journée. D’autre part, nous n’avons plus à nous préoccuper des raisons pour lesquelles la prévision est telle qu’elle est (c’est le modèle qui s’en charge).

Il est néanmoins important de réaliser que tous les cas d’utilisation et problèmes d’entreprise ne peuvent pas être résolus par l’apprentissage automatique.

Cortana Intelligence et Machine Learning peuvent être très efficaces pour résoudre un problème d’entreprise donné lorsque les critères suivants sont satisfaits :
-   Ici, le problème de l’entreprise est **prédictif** par nature. Exemple d’étude de cas prédictive : une compagnie de service public aimerait prévoir la charge électrique sur une sous-centrale pendant l’heure suivante. D’autre part, les éléments d’analyse et de classement de la demande d’historique seraient de nature **descriptive**, et donc, moins pertinents.
-   Il existe une **voie** claire à emprunter une fois la prévision disponible. Par exemple, la prévision d’une surcharge de sous-centrale au cours de l’heure suivante peut déclencher une action proactive destinée à réduire la charge associée à cette sous-centrale et donc, éviter la dite surcharge.
-   L’étude de cas représente un **type de problématique classique** de sorte qu’une fois résolue, elle ouvre la voie à la résolution de cas similaires.
-   Le client peut définir des **objectifs quantitatifs et qualitatifs** pour faire la démonstration de l’implémentation d’une solution réussie. Par exemple, le seuil de précision requis (*par exemple*, erreur autorisée jusqu’à 5 %) peut constituer un bon objectif quantitatif de prévision de demande d’énergie ou, dans le cas d’une prévision de surcharge d’une sous-centrale, la précision (taux de vrais positifs) ou le rappel (étendue des vrais positifs) doit être supérieur à un seuil donné. Ces objectifs doivent être tirés des objectifs d’entreprise du client.
-   Il existe un **scénario d’intégration** clair au workflow de l’entreprise. Par exemple, la prévision de charge d’une sous-centrale peut être intégrée au contrôle du réseau pour permettre les mesures de prévention des surcharges.
-   Le client dispose de **données d’une qualité suffisante** prêtes à l’emploi pour prendre en charge l’étude de cas (voir d’autres informations dans la section suivante de ce document, **Qualité des données**).
-   Le client a accès à une architecture de données sur le cloud ou à l’**apprentissage automatique basé sur le cloud**, notamment à Azure ML et à d’autres composants de Cortana Intelligence Suite.
-   Le client désire établir **un flux de données de bout en bout** qui permet la distribution des données dans le cloud de manière continue, et souhaite **mettre en œuvre** la solution.
-   Le client est prêt à **dédier des ressources** qui seront engagées de manière active pendant l’implémentation pilote initiale afin que la base de connaissances et la responsabilité de la solution puissent être transférées au client en cas de réussite.
-   La ressource client doit être un **professionnel des données expérimenté**, de préférence, un spécialiste des données.

La qualification d’une étude de cas basée sur les critères qui précèdent peut contribuer de façon significative à améliorer le taux de réussite d’un cas d’utilisation et établir une bonne tête de pont pour l’implémentation ultérieure d’études de cas.

### Solutions cloud
Cortana Intelligence Suite sur Azure est un environnement intégré hébergé dans le cloud. Le déploiement d’une solution d’analyse avancée dans un environnement cloud présente des avantages substantiels pour les entreprises et, dans le même temps, peut représenter un changement important pour les entreprises qui utilisent toujours des solutions informatiques sur site. Dans le secteur de l’énergie, il existe une tendance claire à la migration progressive des opérations vers le cloud. Cette tendance accompagne le développement d’un réseau intelligent, comme indiqué ci-dessus, à la section **Tendances du secteur**. Comme ce manuel se concentre sur une solution cloud dans le domaine de l’énergie, il est important d’expliquer les avantages et d’autres considérations sur le déploiement d’une solution basée sur le cloud.

Le principal avantage d’une solution du cloud est peut-être le coût. Lorsqu’une solution utilise des composants déployés dans le cloud, il n’y a pas de frais initiaux ou de frais liés au coût des marchandises vendues. Cela signifie qu’il est inutile d’investir dans du matériel, des logiciels et la maintenance informatique, et par conséquent, les risques pour l’entreprise sont sensiblement réduits.

Un autre avantage important est la structure tarifaire de paiement à l’utilisation des solutions basées sur le cloud. Des serveurs basés sur le cloud servant au calcul ou au stockage peuvent être déployés et mis à l’échelle en fonction des besoins Cela présente l’avantage de la rentabilité d’une solution basée sur le cloud.

Enfin, il est inutile d’investir dans la maintenance informatique ou le développement futur de l’infrastructure, car cela fait partie de l’offre présente sur le cloud. À cet égard, la suite Cortana Intelligence inclut les meilleurs services de leur catégorie, et sa feuille de route continue d’évoluer. De nouvelles fonctionnalités, composants et capacités sont constamment présentés et ils évoluent.

Pour une société qui commence à peine sa transition vers le cloud, nous recommandons d’adopter une approche progressive, en mettant en œuvre une feuille de route pour la migration vers le cloud. Nous pensons que pour les services publics et les sociétés intervenant dans le domaine de l’énergie, les études de cas évoquées dans ce manuel représentent une excellente opportunité de pilotage des solutions d’analyse prédictive dans le cloud.

#### Considérations relatives à la justification des études de cas
Dans de nombreux cas, le client peut être intéressé par la justification professionnelle d’une étude de cas dans laquelle une solution cloud et Machine Learning sont des composants importants. Contrairement à une solution sur site, dans le cas d’une solution basée sur le cloud, le coût initial est minime, et la plupart des éléments de coûts est associée à l’utilisation réelle. Lorsqu’il s’agit de déployer une solution de prévision de demande énergétique sur Cortana Intelligence Suite, plusieurs services peuvent être intégrés à une structure de coûts commune unique. Les bases de données (*par exemple*, SQL Azure) peuvent être utilisées pour stocker les données brutes, puis les prévisions réelles, et Azure ML peut servir à héberger les services de prévisions. Dans cet exemple, la structure des coûts peut inclure des composants de stockage et transactionnels.

D’autre part, une personne doit avoir une bonne connaissance de la valeur pour l’entreprise de l’utilisation de la prévision de la demande d’énergie (à court ou à long terme). En fait, il est important de comprendre la valeur commerciale de chaque opération de prévision. Par exemple, une prévision de charge électrique précise pour les 24 heures à venir peut éviter la surproduction ou peut aider à empêcher les surcharges du réseau, ce qui peut se traduire par des économies au quotidien.

Voici à quoi pourrait ressembler une formule de base pour le calcul de la solution de prévision de la demande : ![Formule de base pour le calcul des avantages financiers de la solution de prévision de la demande](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Cortana Intelligence Suite fournit un modèle de tarification par répartition, et il est donc inutile d’engager un composant de coût fixe pour cette formule. Cette formule peut être calculée sur une base quotidienne, mensuelle ou annuelle.

Vous trouverez les tarifs de Cortana Intelligence Suite et Azure ML [ici](http://azure.microsoft.com/pricing/details/machine-learning/).

### Processus de développement de solution
Le cycle de développement d’une solution de prévision de demande de l’énergie implique généralement 4 phases, dans l’ensemble desquelles nous utilisons les technologies et le service du cloud dans le cadre de Cortana Intelligence Suite.

C’est ce qu’indique le diagramme suivant :

![Cycle de réseau intelligent](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Le paragraphe suivant décrit ce processus en 4 étapes :

1.  **Collecte des données** : toute solution d’analyse avancée repose sur les données (voir **Présentation des données**). Plus précisément, lorsqu’il s’agit d’analyse prédictive et de prévisions, nous nous basons sur flux de données continu et dynamique. Dans le cas de prévisions de demande d’énergie, ces données peuvent être issues directement de capteurs intelligents, ou être déjà agrégées sur une base de données en local. Nous utilisons également d’autres sources de données externes, notamment pour la météo et la température. Ce flux de données continu doit être orchestré, planifié et stocké. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) est le principal moteur pour accomplir cette tâche.
2.  **Modélisation** : pour obtenir des prévisions d’énergie précises et fiables, il convient de développer (former) et de gérer un excellent modèle utilisant les données d’historique et extraire les schémas de données significatifs et prédictifs. Le domaine de Machine Learning (ML) a rapidement augmenté grâce à des algorithmes plus avancés qui sont développés constamment. Azure ML Studio offre une expérience utilisateur satisfaisante, qui permet d’utiliser les algorithmes ML plus avancés dans un workflow complet. Ce workflow figure dans un diagramme de flux intuitif et inclut la préparation des données, l’extraction des fonctionnalités, la modélisation et l’évaluation du modèle. L’utilisateur peut extraire les centaines de modèles différents inclus dans cet environnement. À la fin de cette phase, un spécialiste des données dispose d’un modèle opérationnel parfaitement évalué et prêt au déploiement.

	Le diagramme qui suit représente un workflow classique :

	![Workflow de modélisation](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)

3.  **Déploiement** : une fois le modèle opérationnel disponible, l’opération suivante est le déploiement. Ici, le modèle est converti en un service web qui expose une API RESTful pouvant être appelée simultanément sur Internet à partir de différents clients. Azure ML offre un moyen simple de déployer un modèle directement à partir d’Azure ML Studio, sur simple clic sur un bouton. L’ensemble du processus de déploiement se déroule en coulisses. Cette solution peut automatiquement être mise à l’échelle pour répondre à la consommation requise.

4.  **Consommation** : lors de cette phase, nous utilisons le modèle de prévision pour générer des prédictions. La consommation peut être pilotée à partir d’une application utilisateur (*par exemple*, tableau de bord) ou directement à partir d’un système opérationnel, notamment le système d’équilibrage demande/offre ou une solution d’optimisation de réseau. Plusieurs études de cas peuvent être pilotées depuis un modèle unique.

## Présentation des données
Après avoir examiné la solution de prévision de demande d’énergie (voir **Présentation d’entreprise**) du point de vue de l’entreprise, nous pouvons aborder l’aspect Données. La solution d’analyse prédictive se fonde sur des données fiables. Pour la prévision de demande d’énergie, nous nous reposons sur les données d’historique avec plusieurs niveaux de granularité. Ces données d’historique servent de matière première. Elles subissent une analyse minutieuse, pendant laquelle des spécialistes de données identifient des facteurs de prévision (également appelés fonctionnalités) qui peuvent être placés dans un modèle qui pourra générer les prévisions requises.

Dans le reste de cette section, nous allons décrire les différentes étapes et considérations pour comprendre ces données et la façon de les amener dans un format utilisable.

### Le cycle de développement de modèle
La création de modèles de prévisions adéquats nécessite une préparation et une planification minutieuses. La division en plusieurs étapes du processus de modélisation et la concentration sur une étape à la fois peuvent améliorer de façon significative l’ensemble du processus.

Le diagramme suivant illustre la façon dont le processus peut être décomposé en plusieurs étapes :

![Cycle de développement de modèle](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Comme on peut le voir, le cycle comporte six étapes :
-   Formulation du problème
-   Ingestion des données et exploration des données
-   Préparation des données et ingénierie des fonctionnalités
-   Modélisation
-   Évaluation de modèle
-   Développement

Dans le reste de cette section, nous allons décrire les opérations et éléments individuels à prendre en compte à chaque étape.

### Formulation du problème
Nous pouvons considérer la formulation du problème comme l’opération la plus critique à exécuter avant d’implémenter une solution d’analyse prédictive. Nous allons transformer le problème de l’entreprise et le décomposer en éléments spécifiques pouvant être résolus à l’aide de données et de techniques de modélisation. La formulation du problème comme un ensemble de questions auxquelles nous voulons répondre est une bonne pratique. Voici quelques questions possibles qui peuvent s’appliquer dans le cadre de la prévision de la demande d’énergie :
-   Quelle est la charge prévue dans une sous-centrale dans le prochain jour ou la prochaine heure ?
-   À quelle heure de la journée le pic de demande sur le réseau aura-t-il lieu ?
-   Quelle est la probabilité que mon réseau supporte la charge maximale attendue ?
-   Quelle est la quantité d’énergie que la centrale électrique doit générer à chaque heure de la journée ?

La formulation de ces questions nous permet de nous concentrer sur l’obtention des données adéquates et l’implémentation d’une solution alignée sur la problématique de l’entreprise. En outre, nous pouvons alors définir des métriques clés qui nous permettent d’évaluer les performances du modèle. Par exemple, quel est le degré de précision de la prévision et quelle est la plage d’erreur acceptable par l’entreprise ?

### Sources de données
Le réseau intelligent moderne recueille des données des différentes parties et composants du réseau. Ces données représentent différents aspects de l’opération et de l’utilisation du réseau d’alimentation. Dans le cadre de la prévision de la demande d’énergie, nous limitons la discussion aux sources de données qui reflètent la consommation de la demande réelle. Les capteurs intelligents sont une source importante de consommation d’énergie. Les services publics autour du monde déploient rapidement des capteurs intelligents pour leurs clients. Ces derniers enregistrent la consommation électrique mondiale et relaient ces données jusqu’à la société de service public. Les données sont collectées et renvoyées à intervalle fixe compris entre 5 minutes et 1 heure. Des compteurs plus avancés peuvent être programmés à distance pour contrôler et équilibrer la consommation réelle au sein d’un foyer. Les données de compteurs intelligents sont relativement fiables et incluent un horodatage. Cela en fait un ingrédient important pour la prévision de la demande. Les données de compteur peuvent être agrégées (totalisées) à différents niveaux au sein de la topologie du réseau : transformateur, sous-centrale, région, *etc.*. Nous pouvons ensuite choisir le niveau d’agrégation requis pour créer un modèle de prévision. Par exemple, si la société de service public veut prévoir une charge future sur chacune des sous-centrales du réseau, alors, les données de compteurs peuvent être agrégées pour chacune des sous-centrales et être utilisées en tant qu’entrée dans le modèle de prévision. Nous nous référons à des capteurs en tant que source de données interne.

Une prévision de la demande d’énergie fiable dépend également d’autres sources de données externes. Un facteur important qui affecte la consommation électrique est la météo, ou plus précisément, la température. Les données historiques montrent un lien étroit entre la température externe et la consommation d’électricité. Pendant les chaudes journées d’été, les usagers utilisent les climatiseurs et pendant l’hiver, ils ont recours à des dispositifs de chauffage. Il est donc primordial de disposer d’un historique de données de températures fiables sur le site du réseau. De plus, nous nous reposons aussi sur des prévisions précises de température comme élément de prévision de la consommation électrique.

Il existe d’autres sources de données permettant de créer des modèles de prévision de la demande d’énergie. Parmi elles se trouvent les modifications climatiques à long terme et certains indices économiques (*par exemple*, produit intérieur brut) entre autres. Dans ce document, nous n’incluons pas les autres sources de données.

### Structure des données
Après avoir identifié les sources de données requises, nous souhaitons nous assurer que les données brutes collectées incluent les fonctionnalités de données correctes. Pour créer un modèle de prévision de la demande fiable, vous devez vous assurer que les données recueillies incluent des éléments de données pouvant aider à prédire la demande future. Voici certaines exigences fondamentales concernant la structure (schéma) des données brutes.

Les données brutes se composent de lignes et de colonnes. Chaque mesure est représentée comme une seule ligne de données. Chaque ligne de données inclut plusieurs colonnes (également appelées fonctionnalités ou champs).

1.  **Horodatage** : le champ Horodatage représente l’heure réelle à laquelle les mesures ont été enregistrées. Il doit respecter l’un des formats de date/heure communs. Les parties Date et Heure doivent être incluses. Dans la plupart des cas, il est inutile d’enregistrer les heures avant le deuxième niveau de granularité. Il est important de spécifier le fuseau horaire dans lequel les données sont enregistrées.
2.  **ID compteur** : ce champ identifie le compteur ou l’appareil de mesure. Il s’agit d’une variable catégorique, qui peut se présenter sous forme de combinaison de chiffres et de caractères.
3.  **Valeur de consommation** : il s’agit de la consommation réelle à une date/heure donnée. La consommation peut être exprimée en kWh (kilowatt-heure) ou une autre unité préférée. Il est important de noter que l’unité de mesure doit rester la même dans toutes les mesures des données. Dans certains cas, la consommation peut être fournie sur 3 phases de l’alimentation. Dans ce cas, nous devons collecter toutes les phases de consommation indépendantes.
4.  **Température** : la température est généralement recueillie à partir d’une source indépendante. Toutefois, elle doit être compatible avec les données de consommation. Elle doit inclure un horodatage tel qu’il est décrit ci-dessus, qui lui permettra de se synchroniser avec les données correspondant à la consommation réelle. La valeur de température peut être spécifiée en degrés Celsius ou Fahrenheit, mais doit être la même pour toutes les mesures.
5.  **Emplacement** le champ d’emplacement est généralement associé à l’endroit où les données de température ont été collectées. Il peut être représenté sous forme de code postal ou au format latitude/longitude (lat/long).

Les tables suivantes montrent des exemples de format de données de consommation et de température corrects :

|**Date**|**Time**|**ID du compteur**|**Phase 1**|**Phase 2**|**Phase 3**|
|--------|--------|------------|-----------|-----------|-----------|
|7/1/2015|10:00:00|ABC1234 |7\.0 |2\.1 |5\.3 |
|7/1/2015|10:00:01|ABC1234 |7\.1 |2\.2 |4\.3 |
|7/1/2015|10:00:02|ABC1234 |6\.0 |2\.1 |4\.0 |

|**Date**|**Time**|**Emplacement**|**Température**|
|--------|--------|-------------|---------------|
|7/1/2015|10:00:00|11242 |24,4 |
|7/1/2015|10:00:01|11242 |24,4 |
|7/1/2015|10:00:02|11242 |24,5 |

Comme illustré ci-dessus, cet exemple inclut 3 valeurs de la consommation associées à 3 phases d’alimentation différentes. Notez également que les champs de date et d’heure sont séparés. Ils peuvent néanmoins être combinés en une seule colonne. Dans ce cas, la colonne correspondant à l’emplacement est représentée au format de code postal à 5 chiffres et la température est exprimée en degrés Celsius.

### Format de données
Cortana Intelligence Suite prend en charge les formats de données les plus courants tels que CSV, TSV, JSON, *etc.*. Il est important que le format de données reste le même pour l’ensemble du cycle de vie du projet.

### Ingestion de données
Étant donné que la prévision de la demande d’énergie est constamment et fréquemment calculée, nous devons nous assurer que les données brutes sont transmises au moyen d’un processus d’ingestion de données performant et fiable. Ce processus d’ingestion doit garantir que les données sont disponibles pour le processus de prévision au moment requis. Cela implique que la fréquence d’ingestion de données doit être supérieure à la fréquence de prévision.

Par exemple : si notre solution de prévision de la demande génère une nouvelle prévision à 8 h 00 chaque jour, nous devons nous assurer que toutes les données recueillies au cours des dernières 24 heures ont été entièrement reçues jusque-là et elle doit même inclure la dernière heure de données.

Pour ce faire, Cortana Intelligence Suite offre différents moyens de prendre en charge un processus d’ingestion de données. Cette méthode sera expliquée plus loin dans la section **Déploiement** de ce document.

### Qualité des données
La source de données brutes requise pour exécuter des prévisions fiables et précises doit satisfaire aux critères de qualité des données de base. Bien que des méthodes statistiques avancées puissent être utilisées pour compenser certains problèmes de qualité des données, nous devons nous assurer que nous recoupons certains seuils de qualité de données de base lors de l’ingestion de nouvelles données. Voici quelques considérations au sujet de qualité de données brutes :
-   **Valeur manquante** : cela désigne le cas dans lequel une mesure spécifique n’a pas été collectée. Le principe de base est que le taux de valeur manquant ne doit pas être supérieur à 10 % pour une période donnée. Dans les cas où une valeur manque, cela doit être indiqué à l’aide d’une valeur prédéfinie (par exemple : « 9999 ») et non pas « 0 », qui peut être une mesure valide.
-   **Précision de mesure** : la valeur réelle de la consommation ou de la température doit être enregistrée correctement. Des mesures incorrectes génèrent des résultats imprécis. En règle générale, l’erreur de mesure est inférieure de 1 % par rapport à la valeur true.
-   **Heure de mesure** : il est nécessaire que l’horodatage réel des données recueillies ne s’écarte pas de plus de 10 secondes par rapport à l’heure réelle de la mesure.
-   **Synchronisation** : lorsque plusieurs sources de données sont utilisées (*par exemple*, la consommation et la température) nous devons nous assurer qu’il n’y a pas de problème de synchronisation entre elles. Cela signifie que la différence horaire qui existe entre l’horodatage recueilli à partir de deux sources de données différentes ne doit pas être supérieure à 10 secondes.
-   **Latence** : comme indiqué ci-dessus, à la section **Ingestion des données**, nous dépendons d’un flux de données fiable et du processus d’ingestion. Pour contrôler cela, nous devons nous assurer que nous contrôlons la latence des données. Il s’agit de la différence de temps qui existe entre l’heure du relevé de la mesure actuelle et l’heure à laquelle il a été chargé dans la mémoire Cortana Intelligence Suite et est prêt à l’emploi. Pour les prévisions de charge à court terme, la latence totale ne doit pas être supérieure à 30 minutes. Pour les prévisions de charge à long terme, la latence totale ne doit pas être supérieure à 1 jour.

### Préparation des données et ingénierie des fonctionnalités
Une fois les données brutes réceptionnées (voir **Ingestion des données**) et stockées en toute sécurité, elles sont prêtes au traitement. La phase de préparation des données prend à la base des données brutes et les transforme pour la phase de modélisation. Cela peut inclure des opérations simples telles que l’utilisation de la colonne des données brutes telle qu’elle se présente avec la valeur mesurée réelle, les valeurs normalisées, les opérations plus complexes telles que le [décalage horaire](https://en.wikipedia.org/wiki/Lag_operator), entre autres. Les colonnes de données nouvellement créées sont appelées fonctionnalités de données et le processus de génération de ces données est appelé ingénierie des fonctionnalités. D’ici à la fin de ce processus, nous allons avoir un nouvel ensemble de données dérivé des données brutes et pouvant être utilisé pour la modélisation. En outre, la phase de préparation des données doit prendre en charge les valeurs manquantes (voir **Qualité des données**) et les compenser. Dans certains cas, il devra également normaliser les données pour s’assurer que toutes les valeurs sont représentées à la même échelle.

Dans cette section, nous répertorions certaines des données communes incluses dans les modèles de demande d’énergie.

**Fonctions pilotées par heure :** ces fonctionnalités sont dérivées des données de date/horodatage. Elles sont extraites et converties en fonctionnalités de catégorie telles que :
-   Heure de la journée : il s’agit de l’heure de la journée, et les valeurs sont comprises entre 0 et 23.
-   Jour de la semaine : il s’agit du jour de la semaine et il se voit attribuer des valeurs comprises entre 1 (dimanche) et 7 (samedi)
-   Jour du mois : représente la date effective et peut avoir des valeurs comprises entre 1 et 31
-   Mois de l’année correspond au mois et contient des valeurs comprises entre 1 (janvier) et 12 (décembre)
-   Fin de semaine : il s’agit d’une fonctionnalité de valeur binaire qui prend la valeur 0 pour les jours de la semaine ou 1 pour le week-end
-   Vacances : il s’agit d’une fonctionnalité de valeur binaire qui prend la valeur 0 pour un jour de semaine ou 1 pour les vacances
-   Termes de Fourier : les termes de Fourier sont des pondérations dérivées de l’horodatage et sont utilisés pour retenir le caractère saisonnier (cycles) des données. Comme plusieurs saisons peuvent figurer parmi les données, il se peut que nous ayons besoin de plusieurs termes de Fourier. Par exemple, les valeurs de demande peuvent comporter des saisons/cycles annuels, hebdomadaires et quotidiens, ce qui se traduira par 3 termes de Fourier.

**Fonctionnalités de mesure indépendante :** les fonctionnalités indépendantes incluent tous les éléments de données que nous souhaitons utiliser comme éléments de prédiction dans notre modèle. Ici, nous allons exclure la fonctionnalité dépendante que nous devons prévoir.
-   Fonction de décalage : ce sont les valeurs horaires corrigées de la demande réelle. Par exemple, la fonctionnalité décalage 1 contiendra la valeur de demande dans l’heure précédente (pour les données horaires) par rapport à l’horodatage réel. De même, nous pouvons ajouter décalage 2, décalage 3, *etc.*. La combinaison réelle des fonctionnalités de décalage utilisée est déterminée pendant la phase de modélisation par l’évaluation des résultats du modèle.
-   Tendance à long terme : cette caractéristique représente le développement linéaire de la demande d’une année à l’autre.

**Fonctionnalité dépendante :** la fonctionnalité dépendante est la colonne de données que nous souhaitons que notre modèle prévoie. Avec l’[apprentissage automatique supervisé](https://en.wikipedia.org/wiki/Supervised_learning), nous devons d’abord former le modèle avec les fonctionnalités dépendantes (qui portent également le nom d’étiquettes). Cela permet au modèle d’apprendre à reconnaître les schémas de données associés à une fonction dépendante. Pour la prévision de la demande d’énergie, en général, nous devons prédire la demande réelle et donc, l’utiliser comme une fonction dépendante.

**Gestion des valeurs manquantes :** pendant la phase de préparation des données, nous devons déterminer la meilleure stratégie pour gérer les valeurs manquantes. Généralement, cela se fait grâce aux nombreuses statistiques [méthodes d’imputation de données](https://en.wikipedia.org/wiki/Imputation_(statistics)). Dans le cas de la prévision de la demande d’énergie, nous imputons généralement les valeurs manquantes en utilisant la moyenne mobile des points de données disponibles précédents.

**Normalisation des données :** la normalisation des données est un autre type de transformation utilisé pour mettre à l’échelle toutes les données numériques, notamment prévisions de demande à la même échelle. Cela permet généralement d’améliorer l’exactitude du modèle et sa précision. Pour ce faire, en général, nous divisons la valeur réelle par la plage de données. Cela permet de ramener la valeur d’origine à une plage plus petite, comprise en général entre -1 et 1.

## Modélisation
La phase de modélisation est celle pendant laquelle la conversion des données a lieu. Au cœur de ce processus, il peut y avoir des algorithmes qui analysent les données d’historique (données d’apprentissage), extraire des modèles et générer un modèle. Ce modèle peut servir par la suite à prédire ces nouvelles données qui n’ont pas été utilisées pour générer le modèle.

Une fois que nous avons un modèle de travail fiable, nous pouvons l’utiliser pour noter les nouvelles données structurées, de manière à y inclure les fonctionnalités requises (X). Le processus d’évaluation utilisera un modèle persistant (objet issu de la phase de formation) et prévoira la variable cible désignée par Ŷ.

### Techniques de modélisation de prévision des demandes
En cas de prévision de demande, nous utilisons les données d’historique commandées par heure. Nous faisons généralement référence à des données qui incluent la dimension temps en tant que [série chronologique](https://en.wikipedia.org/wiki/Time_series). L’objectif de la série chronologique est de dégager des tendances associées à l’heure, à la saisonnalité, la corrélation automatique (corrélation sur la durée) et de les formuler dans un modèle.

Au cours des récentes années, des algorithmes avancés ont été développés pour organiser la prévision des séries chronologiques et pour améliorer la précision des prévisions. Nous en présentons brièvement quelques-unes ici.

> [AZURE.NOTE] Cette section n’est pas destinée à être utilisée en tant qu’apprentissage unique et présentation de la prévision, mais plutôt comme un court résumé des techniques de modélisation qui sont en général utilisées pour la prévision de la demande. Pour plus d’informations, et pour obtenir une documentation matérielle sur les prévisions de série chronologique, nous recommandons particulièrement le manuel en ligne [Forecasting: principles and practice](https://www.otexts.org/book/fpp) (Prévision : principes et pratique).

#### [**MA (Moyenne mobile)**](https://www.otexts.org/fpp/6/2)
Moyenne mobile a été la première technique d’analyse utilisée pour la prévision des séries chronologiques et elle reste l’une des plus utilisées à ce jour. Il s’agit également de l’élément de base servant à des techniques de prévision avancées. Avec Moyenne mobile, nous prévoyons le point de données suivant en faisant la moyenne des K points les plus récents, K désignant l’ordre de la moyenne mobile.

La technique de moyenne mobile a pour effet de lisser la prévision et par conséquent, ne peut pas gérer une grande volatilité des données.

#### [**ETS (Lissage exponentiel)**](https://www.otexts.org/fpp/7/5)
Le lissage exponentiel (ETS) est une famille de méthodes qui utilisent la moyenne pondérée des points de données récents afin de prévoir le prochain point de données. L’idée consiste à affecter des pondérations plus élevées pour les valeurs les plus récentes et à diminuer progressivement cette pondération sur les valeurs mesurées. Il existe un certain nombre de méthodes différentes chez cette famille, dont certaines gèrent la saisonnalité dans les données, notamment, la [Holt-Winters Seasonal Method](https://www.otexts.org/fpp/7/5) (Méthode saisonnière Holt Winters).

Certaines de ces méthodes se factorisent dans la saisonnalité des données.

#### [**ARIMA (Moyenne mobile intégrée de régression automatique)**](https://www.otexts.org/fpp/8)
La moyenne mobile intégrée de régression automatique (ARIMA) est une autre famille de méthodes couramment utilisée pour la prévision de séries chronologiques. Elle combine de façon pratique les méthodes de régression automatique avec la moyenne mobile. Les méthodes de régression automatique utilisent des modèles en prenant des valeurs de série chronologique précédente afin de calculer le prochain point de date. Les méthodes ARIMA appliquent également des méthodes de différenciation incluant le calcul de la différence entre les points de données et l’utilisation de ces derniers à la place de la valeur de mesure d’origine. Enfin, ARIMA utilise également les techniques de moyenne mobile décrites plus haut. Les diverses combinaisons de toutes ces méthodes constituent la famille des méthodes ARIMA.

Aujourd’hui, ETS et ARIMA sont largement utilisés dans le cadre de la prévision de la demande d’énergie et de nombreuses autres problématiques de prévision. Dans de nombreux cas, ils se combinent pour fournir des résultats très précis.

**Régression générale multiple** les modèles de régression peuvent représenter l’approche de modélisation la plus importante dans le modèle d’apprentissage automatique et des statistiques. Dans le contexte d’une série chronologique, nous utilisons la régression pour prévoir les valeurs futures (*par exemple*, de la demande). En régression, nous prenons une combinaison linéaire des éléments de prévision et connaissons la pondération (également appelées coefficients) de ces facteurs lors du processus d’apprentissage. L’objectif consiste à produire une ligne de régression qui prévoira la valeur prédite. Les méthodes de régression conviennent lorsque la variable cible est de type numérique et donc, convient aux prévisions chronologiques. Il existe un large éventail de méthodes de régression, et notamment les modèles de régression très simples notamment la [Linear regression](https://en.wikipedia.org/wiki/Linear_regression) (Régression linéaire) et certains plus sophistiqués, tels que les arbres de décision, [Random Forests](https://en.wikipedia.org/wiki/Random_forest) (Forêts aléatoires), [Neural Networks](https://en.wikipedia.org/wiki/Artificial_neural_network) (Réseaux neuronaux), et Boosted Decision Trees (Arbres de décision mis en avant).

La création de prévisions de demande d’énergie en tant que problème de régression nous offre une grande souplesse dans la sélection des fonctions de données pouvant être combinées à partir de données de série chronologique et des facteurs externes tels que la température. Vous trouverez des informations sur les fonctionnalités sélectionnées à la section Ingénierie de fonctionnalités (consultez la section **Exploration des données et ingénierie des fonctionnalités**) de ce manuel.

À partir de notre expérience de l’implémentation et du déploiement du pilote de prévisions de demande en énergie, nous avons découvert que les modèles de régression avancés disponibles dans Azure ML ont tendance à produire les meilleurs résultats, et nous les utilisons.

## Évaluation de modèle
L’évaluation du modèle joue un rôle essentiel au sein du **Cycle de développement de modèle**. À ce stade, nous cherchons à valider le modèle et ses performances en utilisant les données réelles. Lors de l’étape de modélisation, nous utilisons une partie des données disponibles pour l’apprentissage du modèle. Pendant la phase d’évaluation, nous prenons le reste des données pour tester le modèle. Dans la pratique, cela signifie que nous alimentons en nouvelles données le modèle qui a été restructuré et qu’il contient les mêmes fonctionnalités que le jeu de données d’apprentissage. Toutefois, pendant le processus de validation, nous utilisons le modèle pour réduire la variable cible plutôt que de fournir la variable cible disponible. Nous faisons souvent référence à ce processus en tant qu’évaluation de modèle. Nous utilisons ensuite les valeurs cible réelles et les comparons aux valeurs prévues. L’objectif ici consiste à mesurer et à réduire l’erreur de prévision, en d’autres termes, la différence entre prévision et valeur réelle. La quantification de la mesure d’erreur est primordiale, car nous souhaitons adapter le modèle et vérifier que l’erreur diminue. L’ajustement du modèle peut être effectué par la modification des paramètres de modèle qui contrôlent le processus d’apprentissage ou en ajoutant ou en supprimant les fonctionnalités de données (appelées [balayage de paramètres](https://channel9.msdn.com/Blogs/Windows-Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Dans la pratique, cela signifie qu’il convient d’intervenir entre les phases d’ingénierie des fonctionnalités, de modélisation et d’évaluation de modèle plusieurs fois, jusqu’à être capable de réduire l’erreur jusqu’au niveau requis.

Il convient de souligner que l’erreur de prédiction ne sera jamais à zéro, car il n’existe aucun modèle qui prédit parfaitement chaque résultat. Cependant, il existe une certaine amplitude d’erreur acceptable par l’entreprise. Pendant le processus de validation, nous aimerions nous assurer que notre erreur de prédiction de modèle est au niveau de tolérance d’entreprise, voire inférieure. Il est donc important de définir le niveau d’erreur tolérable au début du cycle pendant la phase de **Formulation de problème**.

### Techniques d’évaluation classiques
Il existe différentes façons de mesurer et quantifier les erreurs de prévision. Dans cette section, nous allons étudier les techniques d’évaluation adaptées aux séries chronologiques et plus particulièrement, en vue de prévoir les demandes en énergie.

#### [**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE signifie Erreur de pourcentage absolue moyenne. Avec MAPE, nous calculons la différence entre chaque point prévu et la valeur réelle de ce point. Nous quantifions ainsi l’erreur par point, en calculant la proportion de la différence par rapport à la valeur réelle. À la dernière étape, nous faisons la moyenne de ces valeurs. La formule mathématique utilisée pour MAPE est la suivante :

![Formule MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png) *Où A<sub>t</sub> est la valeur réelle, F<sub>t</sub> est la valeur prévue et n est l’horizon de prévision.*

## Déploiement
Une fois que nous avons affiné la phase de modélisation et validé le fonctionnement du modèle, il convient de passer à la phase de déploiement. Dans ce contexte, le déploiement revient à permettre au client d’utiliser le modèle en exécutant de vraies prévisions à grande échelle. Le concept de déploiement est primordial dans Azure ML, dans la mesure où notre objectif principal consiste à réaliser en permanence des prévisions par rapport à la simple obtention de l’analyse des données. La phase de déploiement est celle qui permet au modèle d’être utilisé à grande échelle.

Dans le contexte de la prévision de la demande énergétique, notre objectif est d’appeler des prévisions périodiques en continu, en nous assurant que les données actualisées sont disponibles pour le modèle et que les données prévues sont envoyées au client usager.

### Déploiement de services Web
Le principal bloc de construction pouvant être déployé dans Azure ML est le service web. Il s’agit du moyen le plus efficace d’activer l’utilisation d’un modèle prédictif dans le cloud. Le service Web encapsule le modèle et l’inclut dans une API (Interface de programmation d’applications) [RESTful](http://www.restapitutorial.com/). L’API peut être utilisée dans le cadre d’un code client, comme illustré dans le diagramme ci-dessous.

![Nous assurons le déploiement du service et la consommation](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Comme vous pouvez le constater, le service web est déployé dans le nuage Cortana Intelligence Suite et peut être appelé via le point de terminaison API REST exposé. Différents types de clients résidant dans plusieurs domaines peuvent appeler le service via l’API Web simultanément. Le service web peut également évoluer pour prendre en charge des milliers d’appels simultanés.

### Une architecture de solution classique
Lors du déploiement d’une solution de prévision de la demande d’énergie, nous nous intéressons au déploiement d’une solution de bout et bout qui va au-delà du service web de prédiction et facilite l’ensemble du flux de données. Au moment d’appeler une nouvelle prévision, nous devons nous assurer que le modèle contient bien des fonctions de données à jour. Cela implique que les données brutes venant d’être recueillies sont constamment assimilées, traitées et transformées dans la fonction requise définie sur laquelle le modèle a été construit. Dans le même temps, nous aimerions mettre les données prévues à la disposition des clients finaux. À titre d’exemple, un cycle de flux de données (ou pipeline de données) figure dans le diagramme ci-dessous :

![Flux de données de bout en bout pour la prévision de la demande d’énergie](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Voici les opérations qui ont lieu dans le cadre du cycle de prévision de la demande énergétique :
1.  Des millions de compteurs de données déployés génèrent en permanence et en temps réel des données de consommation énergétique.
2.  Ces données sont recueillies et téléchargées dans un référentiel cloud (*par exemple*, un objet Blob Azure).
3.  Avant leur traitement, les données brutes sont agrégées dans une sous-centrale ou au niveau régional, comme indiqué par l’entreprise.
4.  Le traitement de la fonctionnalité (voir **Préparation des données et traitement de fonctionnalité**) lieu et génère les données requises pour la constitution ou l’évaluation du modèle, le jeu de données de fonction est stocké dans une base de données (*par exemple*, SQL Azure).
5.  Le service de formation est appelé pour recalculer le modèle de prévision : cette version mise à jour du modèle est rendue persistante afin de pouvoir être utilisée par le service web d’évaluation.
6.  Le service web d’évaluation est appelé en fonction d’une planification qui correspond à la fréquence de prévision requise.
7.  Les données prévues sont stockées dans une base de données accessible par le client final.
8.  Le client de la consommation récupère les prévisions, les applique au réseau et les utilise conformément à l’étude de cas requise.

Il est important de noter que l’ensemble de cycle est totalement automatisé et exécute un plan. L’intégralité de l’orchestration de ce cycle de données peut être effectuée à l’aide d’outils tels qu’[Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### Architecture de déploiement de bout en bout
Pour déployer une solution de prévision de demande d’énergie sur Cortana Intelligence, nous devons vous assurer que les composants requis sont définis et configurés correctement.

Le diagramme suivant représente une architecture Cortana Intelligence classique qui implémente et gère le cycle de flux de données décrit ci-dessus :

![Architecture de déploiement de bout en bout](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Pour plus d’informations sur chacun des composants et de l’architecture d’ensemble, reportez-vous au modèle Energy Solution.

<!---HONumber=AcomDC_0824_2016-->