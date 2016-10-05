<properties
	pageTitle="Utilisation des exemples de jeux de données dans Machine Learning Studio | Microsoft Azure"
	description="Descriptions des jeux de données utilisés dans les exemples de modèles inclus dans ML Studio. Vous pouvez utiliser ces exemples de jeux de données pour vos expériences."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# Utilisation des exemples de jeux de données dans Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Quand vous créez un espace de travail dans Azure Machine Learning, vous disposez par défaut d'un certain nombre d'exemples de jeux de données et d'expériences. Plusieurs de ces exemples de jeu de données sont utilisés par les exemples de modèles dans la [galerie Azure Cortana Intelligence](http://gallery.cortanaintelligence.com/), tandis que d’autres illustrent les différents types de données utilisés en général dans l’apprentissage automatique.

Certains de ces jeux de données sont disponibles dans le stockage d'objets blob Azure. Le tableau ci-dessous fournit un lien direct vers ces jeux de données. Vous pouvez utiliser ces jeux de données dans vos expériences à l’aide du module [Importer les données][import-data].

Les autres exemples de jeux de données sont répertoriés sous **Jeux de données enregistrés** dans la palette de modules à gauche du canevas de l'expérience quand vous ouvrez ou créez une expérience dans ML Studio. Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en le faisant glisser sur le canevas de l'expérience.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nom du jeu de données</th>
  <th align=left>Description du jeu de données</th>
</tr>

<tr>
  <td valign=top>Jeu de données Adult Census Income Binary Classification</td>
  <td valign=top>
Sous-ensemble de la base de données Census de 1994, qui recense les adultes de plus de 16&#160;ans en activité avec un index des revenus ajustés supérieur à&#160;100.<p> </p><b>Utilisation&#160;:</b> effectuer un classement des personnes en utilisant des données démographiques pour prédire si une personne gagne plus de 50&#160;000&#160;$ par an.<p> </p><b>Recherche associée&#160;:</b> Kohavi, R, Becker, B., (1996). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Jeu de données des codes de l'aéroport</td>
  <td valign=top>
Codes des aéroports des États-Unis.<p> </p>Ce jeu de données contient une seule ligne par aéroport des États-Unis. Il indique le numéro d'identification de l'aéroport et son nom, ainsi que la ville et l'état dans lesquels il se trouve.
  </td>
</tr>

<tr>
  <td valign=top>Données sur le prix des véhicules automobiles (brutes)</td>
  <td valign=top>
Informations sur les véhicules automobiles par marque et modèle, incluant le prix, des caractéristiques telles que le nombre de cylindres et de litres au&#160;100, et une note de risque d'assurance.<p> </p>La note de risque est initialement associée au prix du véhicule, puis ajustée pour le risque réel au cours d'une procédure communément appelée symbolisation par les actuaires. La valeur&#160;+3 indique que le véhicule est à risque, et la valeur&#160;-3 qu'il est plutôt sûr.<p> </p><b>Utilisation&#160;:</b> prédire la note de risque en fonction des caractéristiques, en utilisant une classification de régression ou multivariable. <p> </p><b>Recherche associée&#160;:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Jeu de données de location de vélos de l'UCI</td>
  <td valign=top>
Le jeu de données de location de vélo de l'UCI est basé sur les données réelles de la société Capital Bikeshare qui assure l'entretien du réseau de location de vélos à Washington&#160;DC.<p> </p>Ce jeu de données comporte une seule ligne pour chaque heure de chaque jour en 2011 et 2012, soit un total de 17&#160;379&#160;lignes. Le nombre de locations de vélos par heure varie entre&#160;1 et&#160;977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Image RVB de Bill Gates</td>
  <td valign=top>
Fichier image accessible au public et converti en données CSV.<p> </p>Le code utilisé pour convertir l’image se trouve dans la page d’informations sur le modèle de <strong>Quantification des couleurs à l’aide de l’algorithme des k-moyennes</strong>.
  </td>
</tr>

<tr>
  <td valign=top>Données sur le don de sang</td>
  <td valign=top>
Sous-ensemble de la base de données des donneurs de sang du centre de transfusion sanguine de Hsin-Chu City à Taiwan.<p> </p>Les données associées aux donneurs incluent le nombre de mois depuis le dernier don et la fréquence, ou le nombre total de dons, la durée écoulée depuis le dernier don et la quantité de sang donnée.<p> </p><b>Utilisation&#160;:</b> l’objectif est de prédire par la classification si le donneur a donné du sang en mars&#160;2007, 1 indiquant un donneur pendant la période ciblée et 0 une personne n’ayant pas fait de don. <p> </p><b>Recherche associée&#160;:</b> Yeh, I.C., (2008). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science <p> </p>Yeh, I-Cheng, Yang, King-Jang et Ting, Tao-Ming, «&#160;Knowledge discovery on RFM model using Bernoulli sequence&#160;», Expert Systems with Applications, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Critiques de livres d'Amazon</td>
  <td valign=top>
Critiques de livres sur Amazon, extraites du site web amazon.com par les chercheurs de l’Université de Pennsylvanie (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">sentiment</a>). Consultez l’article scientifique «&#160;Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification&#160;» de John Blitzer, Mark Dredze et Fernando Pereira&#160;; Association of Computational Linguistics (ACL), 2007.<p> </p>Le jeu de données original comporte 975&#160;000&#160;critiques dont les notes varient entre 1, 2, 3, 4 ou 5. Les critiques ont été rédigées en anglais et datent de la période 1997-2007. Ce jeu de données a été échantillonné pour être réduit à 10&#160;000&#160;critiques.
  </td>
</tr>

<tr>
  <td valign=top>Données sur le cancer du sein</td>
  <td valign=top>
Un des trois jeux de données sur le cancer fournis par l’Oncology Institute, fréquemment cité dans la littérature sur l’apprentissage automatique. Combine des informations de diagnostic et des caractéristiques d'analyse de laboratoire sur environ 300&#160;échantillons de tissu.<p> </p><b>Utilisation&#160;:</b> classifier le type de cancer en se basant sur 9&#160;attributs, certains étant linéaires, d’autres de catégorie. <p> </p><b>Recherche associée&#160;:</b> Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Caractéristiques du cancer du sein
  <td valign=top>
Le jeu de données contient des informations sur 102&#160;000 régions suspectes (candidats) d’images radio, chacune décrite par 117&#160;caractéristiques. Ces caractéristiques sont propriétaires et leur signification n’est pas révélée par les créateurs du jeu de données (Siemens Healthcare). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informations sur le cancer du sein</td>
  <td valign=top>
Le jeu de données contient des informations complémentaires sur chaque région suspecte dans l’image radio. Chaque exemple fournit des informations (par exemple, intitulé, ID patient, coordonnées du correctif par rapport à l’image entière) sur le numéro de ligne correspondante dans le jeu de données Caractéristiques cancer du sein. Chaque patient présente un certain nombre d’exemples. Pour les patients atteints de cancer, certains exemples sont positifs et d’autres négatifs. Pour les patients non atteints de cancer, tous les exemples sont négatifs. Le jeu de données contient 102&#160;000 exemples. Le jeu de données est biaisé, avec 0,6&#160;% des points positifs, le reste étant négatif. Ce jeu de données a été fourni par Siemens Healthcare.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Étiquettes de l'appétence CRM partagées</td>
  <td valign=top>
Étiquettes provenant du KDD Cup&#160;2009 Customer Relationship Prediction Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Étiquettes de l'attrition CRM partagées</td>
  <td valign=top>
Étiquettes provenant du KDD Cup&#160;2009 Customer Relationship Prediction Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Jeu de données CRM partagé</td>
  <td valign=top>
Ces données proviennent du KDD&#160;Cup&#160;2009 Customer Relationship Prediction Challenge (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>Le jeu de données contient 50&#160;000&#160;clients de la société de télécoms française Orange. Chaque client possède 230&#160;caractéristiques rendues anonymes, dont 190 sont numériques et 40 sont catégorielles. Elles sont très fragmentées.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Étiquettes de vente incitative CRM partagées</td>
  <td valign=top>
Étiquettes provenant du KDD Cup&#160;2009 Customer Relationship Prediction Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Données sur la régression de l'efficacité énergétique</td>
  <td valign=top>
Collection de profils d'énergie simulés, basée sur 12&#160;formes différentes de bâtiments. Les bâtiments diffèrent sur 8&#160;caractéristiques, comme la zone de vitrage, la distribution des zones de vitrage et l'orientation.<p> </p><b>Utilisation&#160;:</b> utiliser la régression ou la classification pour prédire la notation de l’efficacité énergétique sous la forme de l’une des deux réponses en valeur réelle. Pour la classification multiclasse, arrondit la variable de la réponse à l’entier le plus proche. <p> </p><b>Recherche associée&#160;:</b> Xifara, A. &amp; Tsanas, A. (2012). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Données relatives aux vols retardés</td>
  <td valign=top>
Données de ponctualité des vols passagers provenant de la collection de données TranStats du ministère Ministère des transports des États-Unis (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">à l’heure</a>).<p> </p>Le jeu de données couvre la période d'avril à octobre&#160;2013. Avant son téléchargement dans Azure ML Studio, le jeu de données a été traité comme suit&#160;:<ul><li>Le jeu de données a été filtré afin de prendre uniquement en compte les 70&#160;aéroports les plus fréquentés aux États-Unis.</li><li>Les vols annulés ont été considérés comme ayant été retardés de plus de 15&#160;minutes.</li><li>Les vols déviés ont été supprimés.</li><li>Les colonnes suivantes avaient été sélectionnées : Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelled</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Performance concernant les vols à l'heure (brutes)</td>
  <td valign=top>
Enregistrements des arrivées et départs de vols aux États-Unis à compter d'octobre&#160;2011.<p> </p><b>Utilisation&#160;:</b> prédire les retards des vols. <p> </p><b>Recherche associée&#160;:</b> ministère du transport des États-Unis <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Données sur les feux de forêt</td>
  <td valign=top>
Contient des données sur la météo, comme les indices de température et d'humidité et la vitesse du vent, pour une zone du nord-est du Portugal, combinées aux enregistrements des feux de forêt.<p> </p><b>Utilisation&#160;:</b> il s’agit d’une tâche difficile de régression dont l’objectif est de prédire la zone brûlée par des feux de forêt. <p> </p><b>Recherche associée&#160;:</b> Cortez, P., &amp; Morais, A. (2008). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science <p> </p>[Cortez et Morais, 2007] P. Cortez et A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. Aux éditions J. Neves, M. F. Santos and J. Machado, «&#160;New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence&#160;», December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponible à l’adresse <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Jeu de données d'UCI pour une carte de crédit allemande</td>
  <td valign=top>
Jeu de données UCI Statlog (carte de crédit allemande) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), utilisant le fichier german.data.<p> </p>Le jeu de données classe les gens, décrits par un ensemble d'attributs, par risque de crédit (faible ou élevé). Chaque exemple représente une personne. Il y a 20&#160;caractéristiques, numériques et catégoriques, ainsi qu’une étiquette binaire (la valeur du risque de crédit). Les entrées avec un risque de crédit élevé portent une étiquette = 2, tandis que les entrées avec un risque de crédit faible portent une étiquette = 1. Le coût d’une erreur de classification d’un risque de crédit faible en risque élevé est de 1 tandis qu’il est de 5 dans le cas inverse (classification d’un risque de crédit élevé en risque faible).
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Titres de films IMDB</td>
  <td valign=top>
Ce jeu de données contient des informations sur les films évalués dans des tweets sur Twitter&#160;: ID IMDB, titre du film et genre, année de production. Ce jeu de données contient 17&#160;000 films. Il provient du document «&#160;S. Dooms, T. De Pessemier et L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013.&#160;»
  </td>
</tr>

<tr>
  <td valign=top>Données sur deux classes d'iris</td>
  <td valign=top>
Il s’agit sans doute de l’une des bases de données les plus réputées dans la littérature de la reconnaissance des formes. Le jeu de données est relativement petit, avec 50&#160;exemples de mesures de pétales de trois variétés d'iris.<p> </p><b>Utilisation&#160;:</b> prédire le type d’iris à partir des mesures.  <p> </p><b>Recherche associée&#160;:</b> Fisher, R.A. (1988). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de films</td>
  <td valign=top>
Ce jeu de données est une version étendue du jeu de données Movie Tweetings. Il contient 170&#160;000 évaluations de film, tirées de tweets structurés publiés sur Twitter. Chaque instance correspond à un tweet et constitue un tuple&#160;: ID utilisateur, ID IMDB, évaluation, horodatage, nombre de favoris pour ce tweet et nombre de retweets pour ce tweet. Ce jeu de données a été fourni par A.&#160;Said, S.&#160;Dooms, B.&#160;Loni et D.&#160;Tikk dans le cadre du Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td valign=top>Données sur la quantité de litres au&#160;100 pour différents véhicules automobiles</td>
  <td valign=top>
Ce jeu de données diffère légèrement de la version fournie par la bibliothèque StatLib de l’université de Carnegie Mellon. Le jeu de données a été utilisé lors de l'exposition de l'American Statistical Association de 1983.<p> </p>Les données répertorient la consommation d'essence de différents véhicules automobiles en miles par gallon, et des informations comme le nombre de cylindres, la cylindrée du moteur, le cheval-puissance, le poids total et l'accélération.<p> </p><b>Utilisation&#160;:</b> prédire l’économie de carburant en se basant sur 3&#160;attributs discrets multivalués et 5&#160;attributs continus. <p> </p><b>Recherches connexes&#160;:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr>
  <td valign=top>Jeu de données sur la classification binaire du diabète chez les indiens Pima</td>
  <td valign=top>
Sous-ensemble de la base de données du National Institute of Diabetes and Digestive and Kidney Diseases. Le jeu de données a été filtré pour s’intéresser aux patientes féminines des descendants des indiens Pima. Les données incluent des données médicales comme les taux de glucose et d'insuline, et des facteurs liés au style de vie.<p> </p><b>Utilisation&#160;:</b> prédire si le sujet a du diabète (classification binaire). <p> </p><b>Recherche associée&#160;:</b> Sigillito, V. (1990). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml"</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science
  </td>
</tr>

<tr>
  <td valign=top>Données sur les clients de restaurant</td>
  <td valign=top>
Jeu de données sur les clients, comprenant des données démographiques et des préférences.<p> </p><b>Utilisation&#160;:</b> utiliser ce jeu de données en combinaison avec les deux autres jeux de données sur les restaurants, pour former et tester un système de recommandation. <p> </p><b>Recherche associée&#160;:</b> Bache, K et Lichman, M. (2013). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td valign=top>Données sur les caractéristiques de restaurants</td>
  <td valign=top>
Jeu de métadonnées sur des restaurants et leurs caractéristiques, comme le type de gastronomie, le style de lieu et l'emplacement.<p> </p><b>Utilisation&#160;:</b> utiliser ce jeu de données en combinaison avec les deux autres jeux de données sur les restaurants, pour former et tester un système de recommandation. <p> </p><b>Recherche associée&#160;:</b> Bache, K et Lichman, M. (2013). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td valign=top>Notations de restaurants</td>
  <td valign=top>
Contient les notes attribuées par les consommateurs à des restaurants sur une échelle de 0 à&#160;2.<p> </p><b>Utilisation&#160;:</b> utiliser ce jeu de données en combinaison avec les deux autres jeux de données sur les restaurants, pour former et tester un système de recommandation. <p> </p><b>Recherche associée&#160;:</b> Bache, K et Lichman, M. (2013). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td valign=top>Jeu de données multiclasse de recuit d'acier</td>
  <td valign=top>
Ce jeu de données contient un ensemble d'enregistrements d'essais de recuits d'acier avec les attributs physiques (largeur, épaisseur, type – rouleau, feuille, etc.) des résultats des types d'acier.<p> </p><b>Utilisation&#160;:</b> prédire un des deux attributs de classe numérique&#160;: robustesse ou force. Vous pouvez également analyser les corrélations entre attributs.<p> </p>Les qualités d'acier répondent à un standard défini par la SAE et d'autres organisations. Vous recherchez une qualité d’acier spécifique (variable de classe) et vous cherchez à comprendre les valeurs nécessaires. <p> </p><b>Recherche associée&#160;:</b> Sterling, D. &amp; Buntine, W., (NA). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: Université de Californie, School of Information and Computer Science <p> </p>Retrouvez un guide sur la qualité de l’acier à la page <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Données sur les télescopes</td>
  <td valign=top>
Enregistrements sur les sursauts de particules gamma de haute énergie et le bruit de fond, à partir de simulations Monte Carlo.<p> </p>L'objectif de la simulation était d'améliorer la précision des télescopes gamma à effet Cherenkov atmosphérique basés sur sol en utilisant des méthodes statistiques pour discriminer le signal souhaité (douches de radiation Cherenkov) du bruit de fond (douches hadroniques dues à des rayons cosmiques dans la haute atmosphère).<p> </p>Les données ont été prétraitées pour créer un cluster allongé avec l'axe long orienté vers le centre de la caméra. Les caractéristiques de cette ellipse (souvent appelée paramètres de Hillas) font partie des paramètres d'image qui peuvent servir pour la discrimination.<p> </p><b>Utilisation&#160;:</b> prédire si l’image d’une douche représente le signal ou le bruit de fond.<p> </p><b>Remarque&#160;:</b> la simple précision de la classification n’est pas significative pour ces données, car classifier un événement de bruit de fond comme événement de signal est pire que classifier un événement de signal comme événement de bruit de fond. Pour comparer différents classifieurs, il faut utiliser le graphe ROC. La probabilité d’accepter un événement de bruit de fond comme signal doit être sous l’un des seuils suivants&#160;: 0,01, 0,02, 0,05, 0,1 ou 0,2.<p> </p>Il faut également noter que le nombre d'événements de bruits de fond («&#160;h&#160;» pour douches hadroniques) est sous-estimé, car dans les mesures réelles la classe&#160;h ou de bruits représente la majorité des événements. <p> </p><b>Recherche associée&#160;:</b> Bock, R.K. (1995). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie&#160;: University of California, School of Information
  </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Jeu de données météorologiques</td>
  <td valign=top>
Observations météorologiques terrestres effectuées toutes les heures par la NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">données fusionnées, de 201304 à 201310</a>).<p> </p>Les données météorologiques couvrent les observations effectuées depuis les stations météo des aéroports, entre les mois d'avril et octobre&#160;2013. Avant son téléchargement dans Azure ML Studio, le jeu de données a été traité comme suit&#160;:<ul><li>Les identifiants des stations météo ont été alignés sur les identifiants des aéroports correspondants.</li><li>Les stations météo non associées à un des 70&#160;aéroports sélectionnés ont été supprimées.</li><li>La colonne Date a été fractionnée en plusieurs colonnes distinctes indiquant l'année, le mois et le jour.</li><li>Les colonnes suivantes avaient été sélectionnées : AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Jeu de données Wikipedia concernant le SP&#160;500</td>
  <td valign=top>
Les données sont extraites de Wikipédia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>), notamment d’articles sur chaque société S&amp;P&#160;500, et sont stockées sous forme de données XML.<p> </p>Avant son téléchargement dans Azure ML Studio, le jeu de données a été traité comme suit&#160;:<ul><li>Extraction du contenu textuel de chaque société particulière</li><li>Suppression de la mise en forme wiki</li><li>Suppression des caractères non alphanumériques</li><li>Conversion de tout le texte en minuscule</li><li>Ajout d'autres catégories de sociétés connues</li></ul><p> </p>Notez que pour certaines sociétés, aucun article n'a pu être trouvé, donc le nombre d'enregistrements est inférieur à 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
Le jeu de données contient les informations client et les indications relatives à leurs réponses à une campagne de publipostage direct. Chaque ligne représente un client. Le jeu de données contient neuf caractéristiques sur les données démographiques de l’utilisateur et le comportement passé, ainsi que trois colonnes d’étiquette (visite, conversion et dépense). La colonne binaire Visite indique qu’un client a effectué une visite après la campagne marketing&#160;; la colonne Conversion indique que le client a fait un achat&#160;; et la colonne Dépense indique le montant dépensé. Le jeu de données a été fourni par Kevin Hillstrom dans le cadre du MineThatData E-Mail Analytics And Data Mining Challenge.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Caractéristiques des exemples de test du jeu de données de presse RCV1-V2 Reuters. Ce jeu de données contient 781&#160;000 articles de presse, accompagnés de leur ID (première colonne du jeu de données). Chaque article est tokénisé, traité et les mots vides définis. Ce jeu de données est fourni par David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Caractéristiques des exemples d’apprentissage du jeu de données de presse RCV1-V2 Reuters. Ce jeu de données contient 23&#160;000 articles de presse, accompagnés de leur ID (première colonne du jeu de données). Chaque article est tokénisé, traité et les mots vides définis. Ce jeu de données est fourni par David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Jeu de données provenant du KDD Cup&#160;1999 Knowledge Discovery and Data Mining Tools Competition (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>Le jeu de données a été téléchargé et stocké dans le stockage d’objets blob Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) et il inclut à la fois les jeux de données d’apprentissage et de test. Le jeu de données d’apprentissage comporte environ 126&#160;000&#160;lignes et 43&#160;colonnes, étiquettes comprises&#160;; 3&#160;colonnes font partie des informations d’étiquette et 40&#160;colonnes, constituées de caractéristiques numériques et catégorielles/de chaîne, sont disponibles pour s’entraîner avec le modèle. Les données de test contiennent environ 22&#160;500&#160;exemples avec les mêmes 43&#160;colonnes que les données d’apprentissage.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top>
Affectation de rubriques aux articles de presse contenus dans le jeu de données de presse Reuters RCV1-V2. Un article de presse peut être affecté à plusieurs rubriques. Le format de chaque ligne est «&#160;<topic name> <document id> 1&#160;». Le jeu de données contient 2,6&#160;millions d’affectations de rubrique. Ce jeu de données est fourni par David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Ces données proviennent du KDD Cup&#160;2010 Student Performance Evaluation Challenge (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">student performance evaluation</a>). Les données utilisées correspondent au jeu d’apprentissage Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., &amp; Koedinger, K.R. (2010). Algebra I&#160;2008-2009. Jeu de données issu du KDD Cup&#160;2010 Educational Data Mining Challenge. Pour le trouver, accédez à <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> ou <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>Le jeu de données a été téléchargé et stocké dans le stockage d’objets blob Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) et il contient les fichiers journaux d’un système de tutorat des étudiants. Les caractéristiques fournies incluent l’identifiant du problème avec une description sommaire, l’identifiant de l’étudiant, un horodatage et le nombre de tentatives de résolution du problème avant la réussite. Le jeu de données original comporte 8,9&#160;millions d’enregistrements. Ce jeu de données a été échantillonné pour être réduit aux premières 100&#160;000&#160;lignes. Le jeu de données comporte 23 colonnes séparées par des tabulations de divers types&#160;: numérique, catégoriel et horodatage.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->