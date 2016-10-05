<properties
	pageTitle="Guide de démarrage rapide : API Machine Learning Recommendations | Microsoft Azure"
	description="Azure Machine Learning Recommendations - Guide de démarrage rapide"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="luisca"/>

#  Types de build et qualité du modèle #

<a name="TypeofBuilds"></a>
## Types de build pris en charge ##

L’API Recommandations prend actuellement en charge deux types de build : *recommandation* et *FBT*. Les deux types de build sont créés à l’aide de différents algorithmes et offrent des fonctionnalités bien distinctes. Ce document décrit chacune de ces builds, ainsi que les techniques permettant de comparer la qualité des modèles générés.

Si ce n’est déjà fait, nous vous recommandons de suivre le [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### Build de type recommandation ###

La build de type recommandation utilise la factorisation de matrice pour fournir des recommandations. Elle génère des vecteurs de [fonctionnalité latente](https://en.wikipedia.org/wiki/Latent_variable) en fonction de vos transactions afin de décrire chaque élément, et utilise ensuite ces vecteurs latents pour comparer les éléments similaires.

Si vous formez le modèle en fonction des achats effectués dans votre boutique électronique et que vous fournissez un téléphone Lumia 650 en tant qu’entrée pour le modèle, le modèle retournera un ensemble d’articles pouvant potentiellement intéresser les personnes susceptibles d’acheter un téléphone Lumia 650. Les articles peuvent ne pas être complémentaires. Dans cet exemple, il est possible que le modèle retourne d’autres téléphones puisque les personnes qui apprécient le Lumia 650 peuvent aussi aimer d’autres téléphones.

La build de recommandation possède deux fonctionnalités intéressantes :

**La build de recommandation prend en charge le positionnement d’ *articles* froids**

Les articles qui ne sont pas utilisés de manière intensive sont appelés articles froids. Par exemple, si vous recevez un téléphone que n’avez jamais vendu auparavant, le système ne peut pas déduire les recommandations pour ce produit uniquement sur la base des transactions. Autrement dit, le système doit se renseigner à partir des informations concernant le produit lui-même.

Si vous souhaitez utiliser le positionnement d’articles froids, vous devez fournir des informations sur les fonctionnalités de chaque article du catalogue. Voici ce à quoi peuvent ressembler les premières lignes de votre catalogue (notez le format clé-valeur pour les fonctionnalités).

>6CX-00001,Surface Pro2, Surface, Type=Matériel, Stockage=128 Go, Mémoire=4G, Fabricant=Microsoft

>73H-00013,Wake Xbox 360,Gaming, Type=Logiciel, Langue=Anglais, Classification=Mature

>WAH-0F05,Minecraft Xbox 360,Gaming, * Type=Logiciel, Langue=Espagnol, Classification=Récent

Vous devez également définir les paramètres de build suivants :

| Paramètre de build | Remarques
|------------------     |-----------
|*useFeaturesInModel* | Défini sur **true**. Indique si des caractéristiques peuvent être utilisées pour améliorer le modèle de recommandation.
|*allowColdItemPlacement* | Défini sur **true**. Indique si la recommandation doit également placer les éléments froids selon la similarité des caractéristiques.
| *modelingFeatureList* | Liste de noms de caractéristiques séparés par des virgules à utiliser dans la build de recommandation pour améliorer les recommandations. Par exemple, « Langue, Stockage » dans l’exemple précédent.

**La build de recommandation prend en charge les recommandations de l’utilisateur**

Une build de recommandation prend en charge les [recommandations de l’utilisateur](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Cela signifie qu’elle peut fournir des recommandations personnalisées pour les utilisateurs en fonction de leurs historiques de transactions. Pour les recommandations de l’utilisateur, vous pouvez fournir l’ID d’utilisateur ou l’historique de transactions récent de cet utilisateur.

D’une manière générale, il est judicieux d’appliquer les recommandations de l’utilisateur au moment de la connexion sur la page d’accueil. Vous pouvez alors promouvoir le contenu qui s’applique à cet utilisateur spécifique.

Vous pouvez également appliquer une build de type recommandations lorsque l’utilisateur est sur le point de procéder au paiement. À ce stade, vous avez accès à la liste des articles que l’utilisateur est sur le point d’acheter, et vous pouvez lui fournir des recommandations compte tenu du panier en cours.

#### Paramètres de génération de recommandation

| Nom | 	Description |	 Type, <br> valeurs valides <br> (valeur par défaut)
|-------|-------------------|------------------
| *NumberOfModelIterations* |	Le nombre d'itérations effectuées par le modèle est déterminé par le temps de calcul total et la précision du modèle. Plus il y a d’itérations, plus le modèle est précis, mais le calcul prend plus de temps. |	 Entier, <br> 10 à 50 <br>(40)
| *NumberOfModelDimensions* |	Le nombre de dimensions correspond au nombre de caractéristiques que le modèle tente de trouver dans vos données. Le fait d'augmenter le nombre de dimensions permet de mieux ajuster les résultats en clusters plus petits. Toutefois, si le nombre de dimensions est trop important, le modèle ne pourra pas trouver de corrélations entre les éléments. |	Entier, <br> 10 à 40 <br>(20) |
| *ItemCutOffLowerBound* |	Définit le nombre minimal de points d’utilisation où un élément doit se trouver pour qu’il soit considéré comme faisant partie du modèle. |		Entier, <br> 2 ou plus <br> (2) |
| *ItemCutOffUpperBound* | 	Définit le nombre maximal de points d’utilisation où un élément doit se trouver pour qu’il soit considéré comme faisant partie du modèle. | Entier, <br>2 ou plus<br> (2147483647) |
|*UserCutOffLowerBound* |	Définit le nombre minimal de transactions qu’un utilisateur doit avoir effectuées pour être pris en compte dans le modèle. |	Entier, <br> 2 ou plus <br> (2)
| *UserCutOffUpperBound* |	Définit le nombre maximal de transactions qu’un utilisateur doit avoir effectuées pour être pris en compte dans le modèle. |	Entier, <br>2 ou plus <br> (2147483647)|
| *UseFeaturesInModel* |	Indique si des caractéristiques peuvent être utilisées pour améliorer le modèle de recommandation. | 	 Booléen<br> Par défaut : True
|*ModelingFeatureList* |	Liste de noms de caractéristiques séparés par des virgules à utiliser dans la build de recommandation pour améliorer les recommandations. Elle dépend des fonctionnalités importantes. |	Chaîne, 512 caractères maximum
| *AllowColdItemPlacement* |	Indique si la recommandation doit également placer les éléments froids selon la similarité des caractéristiques. | Booléen <br> Par défaut : False
| *EnableFeatureCorrelation* | Indique si des caractéristiques peuvent être utilisées dans le raisonnement. |	Booléen <br> Par défaut : False
| *ReasoningFeatureList* |	Liste de noms de caractéristiques séparés par des virgules à utiliser pour générer des phrases de raisonnement, par exemple pour expliquer les recommandations. Elle dépend des fonctionnalités importantes pour les clients. | Chaîne, 512 caractères maximum
| *EnableU2I* |	Active les recommandations personnalisées, également appelées recommandations d’articles selon un utilisateur (user to item - U2I). | Booléen <br>Par défaut : True
|*EnableModelingInsights* |	Définit si l’évaluation hors connexion doit être effectuée pour collecter des informations sur la modélisation (c’est-à-dire, les mesures de précision et de diversité). Si cette valeur est définie sur True, un sous-ensemble des données ne sera pas utilisé pour l’apprentissage, car il sera réservé pour le test du modèle. En savoir plus sur les [évaluations hors connexion](#OfflineEvaluation). | Booléen <br> Par défaut : False
| *SplitterStrategy* | Si la valeur enable modeling insights (activer les informations de modélisation) est définie sur *true*, il s’agit de la méthode selon laquelle les données doivent être fractionnées à des fins d’évaluation. | Chaîne, *RandomSplitter* ou *LastEventSplitter* <br>Par défaut : RandomSplitter


<a name="FBTBuild"></a>
### Build de type FBT ###

La build de type FBT (Frequently Bought Together - Fréquemment achetés ensemble) effectue une analyse qui compte le nombre de fois où deux ou trois produits différents ont été achetés ensemble. Elle trie ensuite les jeux selon une fonction de similarité (**co-occurrences**, **Jaccard**, **courbes d’élévation**).

Les fonction **Jaccard** et **courbes d’évaluation** sont des techniques de normalisation des co-occurrences. Autrement dit, les articles sont renvoyés uniquement s’ils sont effectivement achetés avec l’élément de départ.

Dans notre exemple du téléphone Lumia 650, le téléphone X est retourné si et seulement s’il a été acheté au cours de la même session que le téléphone Lumia 650. Étant donné que cette situation est peu probable, il est préférable de retourner des articles complémentaires du Lumia 650, par exemple un protecteur d’écran ou un adaptateur compatible.

On suppose actuellement que deux articles sont achetés au cours de la même session si l’achat est effectué dans une transaction associée au même ID utilisateur et au même horodatage.

Les builds FBT ne prennent pas en charge les articles froids puisque, par définition, deux articles doivent être effectivement achetés au cours de la même transaction. Bien que les builds FBT puissent retourner des jeux d’articles (triplets), elles ne prennent pas en charge les recommandations personnalisées puisqu’elles acceptent un seul article de départ comme donnée d’entrée.


#### Paramètres de build FBT

| Nom | 	Description |		Type, <br> valeurs valides, <br> (valeur par défaut)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Niveau de conservatisme du modèle. Nombre de co-occurrences d'éléments à prendre en compte pour la modélisation. | Entier, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Limite le nombre d'éléments dans un ensemble fréquent.| Entier <br> 2-3 <br> (2)
| *FbtMinimalScore* | Score minimal d’un jeu fréquent pour que celui-ci soit inclus dans les résultats retournés. Plus le score est élevé, mieux c'est. | Double <br> 0 et supérieur <br> (0)
| *FbtSimilarityFunction* | Définit la fonction de similarité à utiliser par le build. La fonction **courbes d’élévation** favorise la sérendipité, **co-occurrences** favorise la prévisibilité, et **Jaccard** est un compromis entre les deux. | Chaîne, <br> <i>co-occurrences, courbes d’élévation, jaccard</i><br> Par défaut : <i>jaccard</i>

<a name="SelectBuild"></a>
## Sélection et évaluation des builds ##

Ces instructions peuvent vous aider à déterminer si vous devez utiliser une build de recommandations ou une build FBT, mais n’offrent aucune réponse définitive pour les situations où les deux versions peuvent convenir. Même si vous savez que vous souhaitez utiliser une build de type FBT, vous pouvez tout de même choisir **Jaccard** ou **courbes d’élévation** comme fonction de similarité.

La meilleure approche consiste à tester les deux builds en environnement réel (version d’évaluation en ligne) et à suivre un taux de conversion pour les différentes builds. Le taux de conversion peut être mesuré en fonction du nombre de clics de recommandation, du nombre d’achats réels effectués à partir des recommandations affichées, ou même des montants réels des ventes lorsque les différentes recommandations sont affichées. Vous pouvez sélectionner votre mesure du taux de conversion en fonction de vos objectifs métier.

Dans certains cas, vous souhaiterez peut-être évaluer le modèle en mode hors connexion avant de le mettre en production. Bien qu’une évaluation hors connexion ne puisse pas remplacer une évaluation en ligne, elle peut servir de mesure.

<a name="OfflineEvaluation"></a>
## Évaluation hors connexion  ##

L’objectif d’une évaluation en mode hors connexion est de prédire la précision (le nombre d’utilisateurs qui achèteront l’un des articles recommandés) et la diversité des recommandations (le nombre d’articles recommandés). Dans le cadre de l’évaluation des mesures de précision et de diversité, le système identifie un groupe d’utilisateurs échantillon, puis répartit en deux groupes les transactions de ces utilisateurs : le jeu de données d’apprentissage et le jeu de données de test.

> [AZURE.NOTE] Pour utiliser des mesures en mode hors connexion, vos données d’utilisation doivent contenir des horodatages. Les données temporelles sont indispensables pour fractionner correctement l’utilisation entre les jeux de données d’apprentissage et de test.

> En outre, il est possible que l’évaluation hors connexion n’entraîne aucun résultat pour les fichiers peu utilisés. Pour que l’évaluation soit complète, le jeu de données de test doit contenir un minimum de 1 000 points d’utilisation.

<a name="Precision"></a>
### Précision à k ###
Le tableau suivant représente la sortie de l’évaluation de la précision à k en mode hors connexion.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Pourcentage |	13,75 |	18,04 | 21 |	24,31 |	26,61
|Nombre d’utilisateurs testés |	10 000 |	10 000 |	10 000 |	10 000 |	10 000
|Nombre d’utilisateurs considérés |	10 000 |	10 000 |	10 000 |	10 000 |	10 000
|Nombre d’utilisateurs non considérés |	0 |	0 |	0 |	0 |	0

#### K
Dans le tableau précédent, *k* représente le nombre de recommandations présentées au client. Vous devez donc interpréter le tableau de la manière suivante : « Si, pendant le test, une seule recommandation était présentée aux clients, seuls 13,75 % des utilisateurs achèteraient l’article recommandé. » Ce constat suppose que le modèle a été formé avec les données d’achat. On peut aussi simplement dire que la précision à 1 est de 13,75.

Vous remarquerez que la probabilité qu’un client achète un article recommandé augmente est proportionnelle au nombre d’articles présentés au client. Dans l’expérience précédente, la probabilité passe à 26,61 % lorsque 5 articles sont recommandés.

#### Pourcentage
Pourcentage d’utilisateurs qui ont réagi à au moins une des *k* recommandations indiquées. Le pourcentage est calculé en divisant le nombre d’utilisateurs ayant réagi à au moins une recommandation par le nombre total d’utilisateurs considérés. Consultez la section Nombre d’utilisateurs considérés pour plus d’informations.

#### Nombre d’utilisateurs testés
Les données de cette ligne représentent le nombre total d’utilisateurs dans le jeu de données de test.

#### Nombre d’utilisateurs considérés
Un utilisateur est pris en compte uniquement si le système a recommandé au moins *k* éléments à partir du modèle généré à l’aide du jeu de données d’apprentissage.

#### Nombre d’utilisateurs non considérés
Les données de cette ligne représentent tous les utilisateurs non considérés, c’est-à-dire les utilisateurs n’ayant pas reçu au moins *k* articles recommandés.

Utilisateurs non considérés = utilisateurs testés – utilisateurs considérés

<a name="Diversity"></a>
### Diversité ###
Les mesures de diversité permettent d’évaluer le type d’éléments recommandés. Le tableau suivant représente la sortie de l’évaluation de la diversité en mode hors connexion.

|Plages de percentiles |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Pourcentage | 34,258 | 55,127| 10,615


Nombre total d’articles recommandés : 100 000

Nombre d’articles uniques recommandés : 954

#### Plages de percentiles
Chaque plage de percentiles est représentée par une étendue (valeurs minimales et maximales comprises entre 0 et 100). Les articles proche de 100 représentent les articles les plus populaires et les articles proches de 0 représentent les articles les moins populaires. Par exemple, si la valeur de pourcentage correspondant à la plage de percentiles 99-100 est de 10,6, cela signifie que 10,6 % des recommandations ont retourné uniquement 1 % des articles les plus populaires. La valeur minimale de la plage de percentiles est incluse, et la valeur maximale est exclusive à l’exception de 100.
#### Nombre d’articles uniques recommandés
La mesure du nombre d’articles uniques recommandés indique le nombre d’articles distincts qui ont été retournés pour évaluation.
#### Nombre total d’articles recommandés
La mesure du nombre total d’articles recommandés indique le nombre d’éléments recommandés. Certains articles peuvent être des doublons.

<a name="ImplementingEvaluation"></a>
### Mesures d’évaluation hors connexion ###
Les mesures de précision et de diversité en mode hors connexion peuvent êtes utiles lorsque vous choisissez la build que vous devez utiliser. Au moment de la build, dans le cadre des paramètres de build FBT ou de recommandation :

-	Définissez le paramètre de build *enableModelingInsights* sur **true**.
-	Vous pouvez également sélectionner *splitterStrategy* (*RandomSplitter* ou *LastEventSplitter*). *RandomSplitter* fractionne les données d’utilisation dans les jeux de données d’apprentissage et de test à partir des pourcentages de test et des valeurs de départ aléatoires *randomSplitterParameters*. *LastEventSplitter* fractionne les données d’utilisation dans les jeux de données d’apprentissage et de test en fonction de la dernière transaction de chaque utilisateur.

Ceci déclenche une build qui utilise uniquement un sous-ensemble des données pour l’apprentissage et utilise les autres données pour calculer les mesures d’évaluation. Une fois la build terminée, vous devez appeler [l’API Get build metrics](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f) en transmettant les valeurs *modelId* et *buildId* correspondantes pour obtenir le résultat de l’évaluation.

 Voici le résultat JSON obtenu pour l’exemple d’évaluation.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0921_2016-->