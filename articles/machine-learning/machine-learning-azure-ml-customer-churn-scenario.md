<properties
	pageTitle="Analyse de l’attrition des clients à l’aide de Microsoft Azure Machine Learning | Microsoft Azure"
	description="Étude de cas de développement d’un modèle intégré pour l’analyse et la notation de l’attrition des clients."
	services="machine-learning"
	documentationCenter=""
	authors="jeannt"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016" 
	ms.author="jeannt"/>

# Analyse de l’attrition des clients à l’aide de Microsoft Azure Machine Learning

##Vue d'ensemble
Cette rubrique présente une implémentation de référence d’un projet d’analyse de l’attrition des clients, créé à l’aide de Microsoft Azure Machine Learning Studio. Elle aborde différents modèles génériques associés afin d’apporter une résolution holistique au problème de l’attrition des clients. Nous avons également mesuré la précision des modèles générés à l’aide de Machine Learning (ML), en déterminant des directions à suivre pour la suite du développement.

### Remerciements

Cette expérience a été développée et testée par Serge Berger, spécialiste des données chez Microsoft, et Roger Barga, anciennement chef de produits pour Microsoft Azure Machine Learning. L’équipe de documentation Azure leur sait gré de leur expertise et les remercie pour ce livre blanc.

>[AZURE.NOTE] Les données utilisées pour cette expérience ne sont pas disponibles publiquement. Pour obtenir un exemple montrant comment créer un modèle Machine Learning pour l’analyse de l’attrition, consultez : [Modèle d’attrition Telco](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) dans la [galerie Cortana Intelligence](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Le problème de l’attrition des clients
Les entreprises du secteur de la consommation et de tous les secteurs en général sont confrontées au problème de l'attrition. Parfois, ce dernier est excessif et influence les décisions relatives aux politiques de l'entreprise. La solution classique consiste à prédire quels clients présenteront une forte probabilité d’attrition et à répondre à leurs besoins par le biais d’un service de conciergerie ou de campagnes marketing, ou encore en appliquant des dérogations. Les approches adoptées peuvent varier d’un secteur à l’autre, voire d’un groupe de clients à l’autre, au sein d’un même secteur (exemple : les télécommunications).

Le facteur commun est le suivant : les entreprises doivent réduire au minimum ces efforts particuliers de rétention des clients. Ainsi, la méthodologie logique consisterait à évaluer chaque client en fonction de sa probabilité d’attrition et à s’occuper des N clients les plus susceptibles de franchir le pas. Ces derniers sont peut-être les plus rentables. Ainsi, dans un scénario plus complexe, une fonction de rentabilité est utilisée lors de la sélection des candidats susceptibles de bénéficier d’une dérogation. Cependant, ces considérations ne représentent qu’une partie de la stratégie globale visant à résoudre le problème de l’attrition. Les entreprises doivent également prendre en compte les risques (et la tolérance à l'égard des risques associée), le niveau et le coût de l'intervention, ainsi que la segmentation possible de la clientèle.

##Perspectives et approches du secteur
La gestion sophistiquée de l'attrition témoigne de la maturité d'un secteur. Le secteur des télécommunications en est un exemple type. En effet, les abonnés sont connus pour changer fréquemment d’opérateur. Cette attrition volontaire est l’un des principaux problèmes du secteur. De plus, les opérateurs ont accumulé de nombreuses connaissances sur les *causes d'attrition*. Il s'agit des facteurs qui incitent les clients à changer d'opérateur.

Le choix du téléphone, par exemple, est une cause d’attrition bien connue dans le secteur de la téléphonie. De ce fait, une pratique courante consiste à réduire le prix d’un téléphone pour les nouveaux abonnés, tout en faisant payer le prix fort aux abonnés existants qui veulent changer d’appareil. Traditionnellement, cette règle incite les clients à passer d’un opérateur à un autre pour obtenir une nouvelle remise. En conséquence, les opérateurs ont été amenés à revoir leurs stratégies.

La volatilité élevée des offres de téléphone est un facteur qui invalide très rapidement les modèles d’attrition basés sur les modèles de téléphone actuels. D’autre part, les téléphones mobiles ne sont pas uniquement des outils de communication ; ce sont également des accessoires de mode (iPhone, par exemple). Ces facteurs de prédiction sociaux ne sont pas pris en compte par les jeux de données standard relatifs aux télécommunications.

Résultat : vous ne pouvez pas établir de politique sûre en éliminant simplement les motifs connus d’attrition. En fait, il est **obligatoire** de recourir à une stratégie de modélisation continue, comprenant des modèles classiques qui quantifient les variables catégoriques (ex. : arbres de décision).

En utilisant des jeux de données volumineux sur leurs clients, les entreprises exécutent une analyse des données volumineuses (en particulier, une détection de l’attrition en fonction de ces données), afin de proposer une solution efficace à ce problème. Pour en savoir plus sur l’approche de résolution du problème d’attribution sur la base des données volumineuses, reportez-vous à la section Recommandations concernant l’ETL.

##Méthodologie de modélisation de l’attrition des clients
Une méthode standard de résolution de l’attrition est décrite dans les figures 1 à 3 :

1.	Le modèle de risque vous permet d'identifier la façon dont les actions affectent la probabilité et le risque.
2.	Le modèle d’intervention vous permet d’envisager la façon dont le niveau d’intervention pourrait influer sur la probabilité d’attrition et la quantité de valeur vie client.
3.	Cela permet de bénéficier d’une analyse qualitative transformée en campagne marketing proactive, qui cible des segments de clients afin de proposer la meilleure offre.

![][1]

Cette approche innovante est la meilleure façon de traiter l’attrition, mais elle s’accompagne d’une certaine complexité : nous devons développer un archétype portant sur plusieurs modèles et une trace entre les modèles. L'interaction entre les modèles peut être encapsulée, comme illustré dans le schéma suivant :

![][2]

*Figure 4 : archétype multi-modèles unifié*

Les interactions entre les différents modèles sont primordiales si nous voulons proposer une méthode holistique permettant de fidéliser la clientèle. Chaque modèle se dégrade forcément au fil du temps. De ce fait, l'architecture est une boucle implicite (similaire à l'archétype défini par le standard d'exploration de données CRISP-DM, [***3***]).

Le cycle global risque-décision-segmentation marketing/décomposition reste une structure généralisée, applicable à de nombreux problèmes d'entreprise. L’analyse de l’attrition n’est qu’une représentation parlante de ce groupe de problèmes, car elle affiche toutes les caractéristiques d’un problème commercial complexe qui ne permet pas l’utilisation d’une solution de prédiction simplifiée. Les aspects sociaux de l’approche moderne portant sur le problème de l’attrition ne sont pas particulièrement mis en lumière dans cette méthode, mais encapsulés dans l’archétype de modélisation, comme dans n’importe quel autre modèle.

L'analyse des données volumineuses est un ajout intéressant. Actuellement, les entreprises de télécommunications et de vente au détail récupèrent des données exhaustives sur leurs clients. De ce fait, nous pouvons prédire que le besoin d’une connectivité multi-modèles deviendra une tendance courante. En effet, les tendances émergentes, telles que l’IoT et les appareils polyvalents, permettent aux entreprises d’utiliser des solutions intelligentes à plusieurs niveaux.

 
##Implémentation de l’archétype de modélisation de ML Studio
Étant donné le problème que nous venons de décrire, quelle est la meilleure façon d'implémenter une méthode de modélisation et de notation intégrée ? Dans cette section, nous verrons comment nous y sommes parvenus à l’aide de Microsoft Azure Machine Learning Studio.

L’approche multi-modèles est incontournable pour concevoir un archétype global d’attrition. Même la partie évaluative (prédictive) de la méthode doit être multi-modèles.

Le schéma suivant illustre le fonctionnement du prototype que nous avons créé, qui utilise quatre algorithmes de notation différents dans ML Studio afin de prévoir le taux d’attrition. L'utilisation d'une méthode multi-modèles n'est pas seulement utile pour créer un classifieur d'ensemble afin d'améliorer l'exactitude, mais également pour se protéger du surajustement et pour améliorer la sélection des caractéristiques prédictives.

![][3]

*Figure 5 : prototype d’une méthode de modélisation de l’attrition*

Les sections suivantes fournissent plus d’informations sur le modèle de notation du prototype que nous avons implémenté à l’aide de ML Studio.

###Sélection et préparation des données
Les données utilisées pour établir les modèles et évaluer les clients ont été obtenues à partir d'une solution verticale de CRM. Elles ont été masquées pour protéger la confidentialité des clients. Ces données portent sur 8 000 abonnements aux États-Unis et proviennent de trois sources : données de préparation (métadonnées des abonnements), données d’activité (utilisation du système) et données de service clientèle. Elles n’incluent pas d’informations professionnelles sur les clients de l’entreprise. Par exemple, elles ne contiennent pas de métadonnées sur la fidélité ni de scores de crédit.

Pour simplifier, les processus de nettoyage des données et ETL ne sont pas utilisés, car nous estimons que la préparation des données a déjà été effectuée à un autre niveau.

La sélection des caractéristiques de modélisation se base sur une notation préliminaire de l’importance de l’ensemble des facteurs de prédiction, incluse dans le processus à l’aide du module de forêt aléatoire. Pour l’implémentation dans ML Studio, nous avons calculé les valeurs moyennes et médianes, ainsi que les plages de caractéristiques représentatives. Nous avons notamment ajouté des agrégats pour les données qualitatives, telles que des valeurs minimum et maximum pour l’activité de l’utilisateur.

Nous avons également collecté des informations temporelles portant sur les 6 derniers mois. Nous avons analysé les données pendant un an. Nous avons pu déterminer que, même s’il existe des tendances significatives en termes de statistiques, l’effet sur l’attrition est fortement réduit après six mois.

Ce qui importe le plus, c’est que le processus dans son ensemble (y compris ETL, la sélection de caractéristiques et la modélisation) a été implémenté dans ML Studio, en utilisant des sources de données dans Microsoft Azure.

Les schémas suivants illustrent les données utilisées :

![][4]

*Figure 6 : extrait d’une source de données (masquée)*

![][5]


*Figure 7 : caractéristiques extraites de la source de données*  
> Notez que ces données sont privées et que, par conséquent, le modèle et les données ne peuvent pas être partagés. Toutefois, pour un modèle similaire utilisant des données disponibles publiquement, consultez cet exemple d’expérience dans la [Galerie Cortana Intelligence](http://gallery.cortanaintelligence.com/) : [attrition des clients Telco](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Pour plus d’informations sur la façon dont vous pouvez implémenter un modèle d’analyse de l’attrition à l’aide de Cortana Intelligence Suite, nous vous recommandons également de regarder [cette vidéo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) proposée par le responsable de programme principal Wee Hyong Tok.
> 

###Algorithmes utilisés dans le prototype

Nous avons utilisé les quatre algorithmes d'apprentissage automatique suivants pour établir le prototype (sans personnalisation) :

1.	Régression logique (LR)
2.	Arbre de décision optimisé(BT)
3.	Perceptron moyenné (AP)
4.	Machines à vecteurs de support (SVM)


Le schéma suivant illustre une partie de la surface de conception de l’expérience, qui indique l’ordre dans lequel les modèles ont été créés :

![][6]


*Figure 8 : création de modèles dans Microsoft Azure ML Studio*

###Méthodes de notation
Nous avons effectué la notation des quatre modèles à l’aide d’un jeu de données d’apprentissage étiqueté.

Nous avons également envoyé le jeu de données de notation à un modèle comparable, créé via l’édition Desktop de SAS Enterprise Miner 12. Nous avons mesuré l’exactitude du modèle SAP, ainsi que des quatre modèles ML Studio.

##Résultats
Dans cette section, nous présentons nos découvertes sur l’exactitude des modèles en fonction du jeu de données d’évaluation.

###Exactitude et précision de la notation
En général, l'implémentation dans Azure ML est derrière SAP en termes d'exactitude et ce, d'environ 10 à 15 % (aire sous la courbe, ASC).

Toutefois, la mesure la plus importante en termes d’attrition est le taux de classification incorrecte : parmi les clients les plus enclins à l’attrition, quels sont ceux qui n’ont **pas** quitté un fournisseur, mais ont bénéficié malgré tout d’un traitement spécial ? Le schéma suivant compare ces différents taux de classification incorrecte pour tous les modèles :

![][7]


*Figure 9 : aire sous la courbe du prototype Passau*

###Utilisation de l’aire sous la courbe (ASC) pour comparer des résultats
L'aire sous la courbe (ASC) est une mesure qui représente la mesure globale de *séparabilité* entre les distributions d'évaluations pour des populations positives et négatives. Elle est similaire à la courbe ROC (Receiver Operating Characteristic, caractéristique de fonctionnement du récepteur). Cependant, contrairement à cette dernière, elle ne vous demande pas de choisir une valeur seuil. Au lieu de cela, elle résume les résultats pour **tous** les choix possibles. En comparaison, la courbe ROC traditionnelle indique le taux de positifs sur l’axe vertical et le taux de faux positifs sur l’axe horizontal. De plus, son seuil de classification varie.

La mesure ASC est généralement utilisée pour évaluer la valeur de différents algorithmes (ou systèmes), car elle permet de comparer des modèles à l’aide de leurs valeurs ASC. Il s’agit d’une approche populaire dans des secteurs comme la météorologie et les biosciences. Ainsi, l’ASC est un outil répandu pour évaluer les performances d’un classifieur.

###Comparaison des taux de classification incorrecte
Nous avons comparé les taux de classification incorrecte du jeu de données concerné à l’aide des données CRM d’environ 8 000 abonnements.

-	Le taux d’erreur de classification de SAP se situait entre 10 et 15 %.
-	Le taux de classification incorrecte de ML Studio était de 15 à 20 % pour les 200-300 clients les plus susceptibles de changer de fournisseur.

Dans le secteur des télécommunications, il est important de s’adresser uniquement aux clients les plus susceptibles d’attrition en leur offrant un service de conciergerie ou tout autre traitement spécial. Sur ce plan, l’implémentation de ML Studio obtient des résultats identiques à ceux du modèle SAP.

De même, l’exactitude est plus importante que la précision, car nous tenons avant tout à classer correctement les personnes susceptibles de changer de fournisseur.

Le schéma suivant, provenant de Wikipedia, décrit cette relation sous forme de graphique dynamique facile à comprendre :

![][8]

*Figure 10 : équilibre entre exactitude et précision*

###Résultats liés à l’exactitude et à la précision du modèle d’arbre de décision optimisé  

Le graphique suivant présente les résultats bruts d’une notation réalisée à l’aide du prototype Machine Learning pour le modèle d’arbre de décision optimisé. Il s’agit du modèle présentant la plus grande exactitude parmi les quatre proposés :

![][9]

*Figure 11 : caractéristiques du modèle d’arbre de décision optimisé*

##Comparaison entre les performances
Nous avons comparé la vitesse à laquelle les données étaient notées à l’aide de modèles ML Studio et d’un modèle comparable créé à l’aide de l’édition Desktop de SAS Enterprise Miner 12.1.

Le tableau suivant récapitule les performances des algorithmes :

*Tableau 1. Performances générales (précision) des algorithmes*

| LR|BT|AP|SVM|
|---|---|---|---|
|Modèle moyen|Meilleur modèle|Mauvaises performances|Modèle moyen|

Les modèles hébergés dans ML Studio ont supplanté le modèle SAP de 15 à 25 % grâce à sa vitesse d’exécution, mais leur exactitude était largement équivalente.

##Considérations et recommandations
Dans le secteur des télécommunications, plusieurs pratiques ont émergé pour l’analyse de l’attrition :

-	Mesures dérivées pour quatre catégories principales :
	-	**Entité (par exemple, un abonnement)**. Informations de base sur l’abonnement ou le client qui risque de se désabonner.
	-	**Activité**. Obtient toutes les informations d’utilisation possibles qui sont associées à l’entité, par exemple le nombre de connexions.
	-	**Service client**. Récupère des informations dans les journaux du service client pour indiquer si l’abonné a rencontré des problèmes ou est entré en contact avec le service client.
	-	**Données d'entreprise et concurrentielles**. Obtient toutes les informations possibles sur le client (par exemple, les données indisponibles ou difficiles à récupérer).
-	Utilisez l'importance pour la sélection des caractéristiques. Cela implique que le modèle d’arbre de décision optimisé est toujours une approche prometteuse.

L’utilisation de ces quatre catégories donne l’impression qu’une simple approche *déterministe*, basée sur des indices formés sur des facteurs raisonnables par catégorie, doit suffire à identifier les clients risquant de se désabonner. Malheureusement, même si cette notion reste plausible, c'est une fausse idée. En effet, l'attrition est un effet temporel et les facteurs y contribuant sont habituellement dans des états temporaires. Un facteur qui incite un client à se désabonner aujourd’hui peut être différent demain, et le sera certainement dans six mois. De ce fait, un modèle *probabiliste* est nécessaire.

Cette observation importante est souvent ignorée par les entreprises, qui préfèrent généralement une approche d’informatique décisionnelle, car elle est plus vendeuse et permet une automatisation directe.

Cependant, la promesse d’une analyse autonome à l’aide de ML Studio permet d’envisager que les quatre catégories d’informations, évaluées par division ou service, deviendront des sources utiles pour l’apprentissage automatique relatif à l’attrition.

Une autre fonctionnalité passionnante proposée par Microsoft Azure ML est la possibilité d’ajouter un module personnalisé dans le référentiel des modules prédéfinis qui sont déjà disponibles. Cette capacité permet surtout de sélectionner des bibliothèques et de créer des modèles pour les marchés verticaux. C’est un facteur de différenciation primordial de Microsoft Azure ML sur le marché.

Nous espérons pouvoir à nouveau évoquer ce sujet, notamment en ce qui concerne l’analyse des données volumineuses.  
##Conclusion
Ce document détaille une approche rationnelle pour la gestion d’un problème commun, l’attrition, à l’aide d’une structure générique. Nous avons envisagé un prototype de modèle de notation, que nous avons implémenté à l’aide de Microsoft Azure ML. Enfin, nous avons évalué l'exactitude et les performances de la solution prototype par rapport aux algorithmes comparables dans SAP.

**Pour plus d'informations :**

Ce document vous a-t-il été utile ? Donnez-nous votre avis. Dites-nous, sur une échelle de 1 (mauvais) à 5 (excellent), comment vous évalueriez ce document et pourquoi vous lui donneriez cette note ? Par exemple :

-	Le notez-vous bien du fait des bons exemples, des excellentes captures d'écran, de l'écriture claire ou pour toute autre raison ?
-	Le notez-vous mal du fait de mauvais exemples, de captures d'écran floues ou d'une écriture peu claire ?

Ces commentaires nous aideront à améliorer la qualité des livres blancs que nous publions.

[Envoyer des commentaires](mailto:sqlfback@microsoft.com).  
##Références
[1] Predictive Analytics: Beyond the Predictions, W. McKnight, Information Management, juillet/août 2011, p.18-20.

[2] Article Wikipédia : [Accuracy and precision](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Step-by-Step Data Mining Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)

[4] [Big Data Marketing: Engage Your Customers More Effectively and Drive Value](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Modèle d’attrition Telco](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) dans la [galerie Cortana Intelligence](http://gallery.cortanaintelligence.com/)  
##Annexe

![][10]

*Figure 12 : capture d’écran d’une présentation d’un prototype pour l’attrition*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png

<!---HONumber=AcomDC_0921_2016-->