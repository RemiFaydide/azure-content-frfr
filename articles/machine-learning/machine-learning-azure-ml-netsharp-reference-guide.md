<properties 
	pageTitle="Guide du langage de spécification des réseaux neuronaux Net# | Microsoft Azure" 
	description="Syntaxe pour le langage de spécification des réseaux neuronaux Net#, avec des exemples montrant comment créer un modèle de réseau neuronal personnalisé dans Microsoft Azure ML à l’aide de Net#" 
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
	ms.date="09/12/2016" 
	ms.author="jeannt"/>



# Guide du langage de spécification des réseaux neuronaux Net# pour Azure Machine Learning

## Vue d'ensemble
Net# est un langage développé par Microsoft, qui définit les architectures de réseaux neuronaux des modules correspondants dans Microsoft Azure Machine Learning. Cet article portera sur les éléments suivants :

-	Concepts fondamentaux liés aux réseaux neuronaux
-	Conditions des réseaux neuronaux et définition des principaux composants
-	Syntaxe et mots clés du langage de spécification Net#
-	Exemples de réseaux neuronaux personnalisés créés avec Net#
	
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Principes fondamentaux des réseaux neuronaux
Un réseau neuronal se compose de ***nœuds***, organisés en ***couches***, et de ***connexions pondérées*** (ou ***bords***) reliant les nœuds. Les connexions sont directionnelles ; chaque connexion présente un nœud ***source*** et un nœud de ***destination***.

Chaque ***couche apte à l'apprentissage*** (couche masquée ou de sortie) présente un ou plusieurs ***faisceaux de connexions***. Un faisceau de connexions se compose d'une couche source et d'une définition des connexions de cette couche. Toutes les connexions d'un même faisceau partagent la même ***couche source*** et la même ***couche de destination***. En Net#, on considère qu'un faisceau de connexions appartient à sa couche de destination.
 
Net# prend en charge différents types de faisceau de connexions, ce qui vous permet de personnaliser la façon dont les entrées sont mappées aux couches masquées et aux sorties.

Le faisceau par défaut ou standard est un **faisceau complet**, dans lequel chaque nœud de la couche source est connecté à tous les nœuds de la couche de destination.

En outre, Net# prend en charge les quatre types de faisceaux de connexions avancés suivants :

-	**Faisceaux filtrés**. L’utilisateur peut définir un prédicat à l’aide de l’emplacement du nœud de la couche source et de celui de la couche de destination. Les nœuds sont connectés chaque fois que le prédicat a la valeur True.
-	**Faisceaux convolutionnels**. L'utilisateur peut définir de petits voisinages de nœuds dans la couche source. Chaque nœud de la couche de destination est connecté à un voisinage de nœuds de la couche source.
-	**Faisceaux de regroupement** et **faisceaux de normalisation de réponse**. Ces faisceaux sont similaires aux faisceaux convolutionnels dans le sens où l'utilisateur définit de petits voisinages de nœuds dans la couche source. Il existe une différence : les poids des bords dans ces faisceaux ne sont pas aptes à l’apprentissage. Au lieu de cela, une fonction prédéfinie est appliquée aux valeurs de nœud source, afin de déterminer la valeur du nœud de destination.

L’utilisation de Net# pour définir la structure d’un réseau neuronal permet de spécifier des structures complexes, comme des réseaux neuronaux profonds ou des convolutions de dimensions arbitraires, connues pour améliorer l’apprentissage à partir de données telles que des images, des sons ou des vidéos.

## Personnalisations prises en charge
L’architecture des modèles de réseau neuronal que vous créez dans Microsoft Azure Machine Learning peut bénéficier d’une personnalisation avancée grâce à Net#. Vous pouvez :

-	créer des couches masquées et contrôler le nombre de nœuds dans chaque couche ;
-	spécifier la façon dont les couches doivent être connectées les unes aux autres ;
-	définir des structures de connectivité spéciales, telles que des convolutions et des faisceaux à poids partagé ;
-	spécifier différentes fonctions d'activation.

Pour plus d'informations sur la syntaxe du langage de spécification, consultez la section consacrée à la [définition de la structure](#Structure-specifications).
 
Pour des exemples de définition de réseaux neuronaux pour certaines tâches standard d'apprentissage automatique, simples comme complexes, consultez la section [Exemples](#Examples-of-Net#-usage).

## Conditions générales
-	Il doit y avoir exactement une couche de sortie, au moins une couche d’entrée et aucune ou plusieurs couches masquées.
-	Chaque couche a un nombre fixe de nœuds, organisés de façon conceptuelle dans un tableau rectangulaire aux dimensions arbitraires.
-	Les couches d'entrée n'ont pas de paramètre formé associé et représentent le point où les données d'instance entrent dans le réseau.
-	Les couches aptes à l’apprentissage (couches masquées et de sortie) présentent des paramètres formés associés, appelés poids et biais.
-	Les nœuds source et de destination doivent se trouver sur des couches indépendantes.
-	Les connexions doivent être non cycliques. En d'autres termes, il ne peut pas y avoir une chaîne de connexions ramenant au nœud source initial.
-	La couche de sortie ne peut pas être la couche source d'un faisceau de connexions.

## Spécifications de structures
Une spécification de structure de réseau neuronal se compose de trois sections : la **déclaration de constante**, la **déclaration de couche** et la **déclaration de connexion**. Il existe également une section de **déclaration de partage**, qui est facultative. Ces sections peuvent être définies dans n'importe quel ordre.

## Déclaration de constante 
Ce type de déclaration est facultatif. Elle offre une méthode de définition des valeurs utilisées à d’autres emplacements de la définition du réseau neuronal. L'instruction de déclaration se compose d'un identifiant suivi du signe égal et d'une expression de valeur.

Par exemple, l'instruction suivante définit une constante **x** :


    Const X = 28;  

Pour définir simultanément deux constantes ou plus, mettez les noms d’identificateur et les valeurs associées entre accolades, en les séparant par des points-virgules. Par exemple :

    Const { X = 28; Y = 4; }  

Le côté droit de chaque expression d’affectation peut être un entier, un nombre réel, une valeur booléenne (vrai/faux) ou une expression mathématique. Par exemple :

	Const { X = 17 * 2; Y = true; }  

## Déclaration de couche
La déclaration de couche est requise. Elle définit la taille et la source de la couche, y compris ses attributs et faisceaux de connexions. L’instruction de déclaration commence par le nom de la couche (d’entrée, masquée ou de sortie), suivi de ses dimensions (un tuple d’entiers positifs). Par exemple :

	input Data auto;
	hidden Hidden[5,20] from Data all;
	output Result[2] from Hidden all;  

-	Le produit des dimensions est le nombre de nœuds de la couche. Dans cet exemple, il y a deux dimensions [5,20], ce qui signifie qu'il y a 100 nœuds dans la couche.
-	Les couches peuvent être déclarés dans n'importe quel ordre, à une exception près : si plusieurs couches d'entrée sont définies, l'ordre dans lequel elles sont déclarées doit être le même que celui des fonctions dans les données d'entrée.


Pour spécifier que le nombre de nœuds d'une couche doit être déterminé automatiquement, utilisez le mot clé **auto**. Ce dernier peut avoir différents effets selon le type de couche :

-	Dans une déclaration de couche d'entrée, le nombre de nœuds est le nombre de caractéristiques dans les données d'entrée.
-	Dans une déclaration de couche masquée, le nombre de nœuds correspond au nombre spécifié par la valeur du paramètre définissant le **Nombre de nœuds masqués**.
-	Dans une déclaration de couche de sortie, le nombre de nœuds est de 2 pour les classifications à deux classes, de 1 pour la régression et égal au nombre de nœuds de sortie pour une classification multiclasse.

Par exemple, la définition de réseau ci-après permet de déterminer automatiquement la taille de toutes les couches :

	input Data auto;
	hidden Hidden auto from Data all;
	output Result auto from Hidden all;  


Une déclaration d'une couche apte à l'apprentissage (couche masquée ou de sortie) peut éventuellement inclure la fonction de sortie (également appelée fonction d'activation), configurée par défaut sur **sigmoid** pour des modèles de classification, et **linéaire** pour des modèles de régression. (Même si vous utilisez la valeur par défaut, vous pouvez déclarer explicitement la fonction d'activation, si vous le souhaitez pour plus de clarté.)

Les fonctions de sortie suivantes sont prises en charge :

-	sigmoid
-	linear
-	softmax
-	rlinear
-	square
-	sqrt
-	srlinear
-	abs
-	tanh
-	brlinear

Par exemple, la déclaration suivante utilise la fonction **softmax** :

	output Result [100] softmax from Hidden all;  

## Déclaration de connexion
Immédiatement après avoir défini la couche entraînable, vous devez déclarer les connexions entre les couches définies. Une déclaration de faisceau de connexions commence par le mot clé **from**, suivi du nom de la couche source du faisceau et du type de faisceau à créer.

À ce jour, cinq types de faisceau de connexions sont pris en charge :

-	Faisceau **complet**, signalé par le mot clé **all**
-	Faisceau **filtré**, signalé par le mot clé **where**, suivi par une expression de prédicat
-	Faisceau **convolutionnel**, signalé par le mot clé **convolve**, suivi des attributs de convolution
-	Faisceau de **regroupement**, signalé par les mots clés **max pool** ou **mean pool**
-	Faisceau de **normalisation de réponse**, signalé par le mot clé **response norm**

## Faisceaux complets  

Les faisceaux de connexions complets incluent une connexion entre chaque nœud de la couche source et chaque nœud de la couche de destination. Il s'agit du type de connexion réseau par défaut.

## Faisceaux filtrés
Une spécification de faisceau de connexion filtré inclut un prédicat, dont la syntaxe est assez similaire à celle d'une expression lambda C#. L'exemple suivant est une définition de deux faisceaux filtrés :

	input Pixels [10, 20];
	hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-	Dans le prédicat de _ByRow_, **s** est un paramètre représentant un index dans le tableau rectangulaire de nœuds de la couche d'entrée, _Pixels_, tandis que **d** est un paramètre représentant un index dans le tableau de nœuds de la couche masquée _ByRow_. Le type de **s** et de **d** est un tuple d'entiers de longueur 2. D'un point de vue conceptuel, **s** couvre toutes les paires d'entiers avec _0 <= s[0] < 10_ et _0 <= s[1] < 20_, tandis que **d** couvre toutes les paires d'entiers avec _0 <= d[0] < 10_ et _0 <= d[1] < 12_.
-	Sur la droite de l'expression de prédicat se trouve une condition. Dans cet exemple, pour chaque valeur de **s** et **d** permettant à la condition d'avoir la valeur True, il existe un bord à partir du nœud de couche source vers le nœud de couche de destination. Ainsi, cette expression de filtre indique que le faisceau inclut une connexion du nœud défini par **s** au nœud défini par **d**, dans tous les cas où s[0] est égal à d[0].

Vous pouvez également spécifier un ensemble de poids pour un faisceau filtré. La valeur de l'attribut **Weights** doit être un tuple de valeurs à virgule flottante dont la longueur est égale au nombre de connexions défini par le faisceau. Par défaut, les poids sont générés de façon aléatoire.

Les valeurs de poids sont regroupées par l'index de nœuds de destination. Ainsi, si le premier nœud de destination est connecté à K nœuds sources, les _K_ premiers éléments du tuple **Weights** correspondent aux poids du premier nœud de destination, selon l’ordre de l’index source. Il en va de même pour les nœuds de destination restants.

Il est possible de spécifier les poids directement comme des valeurs constantes. Par exemple, si vous connaissez déjà les poids, vous pouvez les spécifier en tant que constantes à l'aide de cette syntaxe :

	const Weights_1 = [0.0188045055, 0.130500451, ...]


## Faisceaux convolutionnels
Lorsque les données d’apprentissage présentent une structure homogène, des connexions convolutionnelles sont généralement utilisées pour l’apprentissage des caractéristiques de haut niveau des données. Par exemple, pour les données images, audio ou vidéo, la dimensionnalité spatiale ou temporelle peut être assez uniforme.

Les faisceaux convolutionnels emploient des **noyaux** rectangulaires, glissés à travers les dimensions. En gros, chaque noyau définit un ensemble de poids appliqués dans les voisinages locaux, que l'on appelle **applications de noyau**. Chaque application de noyau correspond à un nœud de la couche source appelé **nœud central**. Les poids d'un noyau sont partagés entre de nombreuses connexions. Dans un faisceau convolutionnel, chaque noyau est rectangulaire et toutes les applications de noyau sont de la même taille.

Les faisceaux convolutionnels prennent en charge les attributs suivants :

**InputShape** définit la dimensionnalité de la couche source pour ce faisceau convolutionnel. Cette valeur doit être un tuple d’entiers positifs. Le produit de ces entiers doit être égal au nombre de nœuds de la couche source, mais ne doit pas forcément correspondre à la dimensionnalité déclarée pour celle-ci. La longueur de ce tuple devient la valeur **arity** (arité) du faisceau convolutionnel. En général, l’arité fait référence au nombre d’arguments ou d’opérandes qu’une fonction peut accepter.

Pour définir la forme et les emplacements des noyaux, utilisez les attributs **KernelShape**, **Stride**, **Padding**, **LowerPad** et **UpperPad** :

-	**KernelShape** : (obligatoire) définit la dimensionnalité de chaque noyau du faisceau convolutionnel. Cette valeur doit être un tuple d’entiers positifs, dont la longueur est égale à l’arité du faisceau. Chaque composant de ce tuple doit avoir une valeur inférieure ou égale au composant correspondant de l'élément **InputShape**.
-	**Stride** : (facultatif) définit les tailles d'incrément ajustables de la convolution (une par dimension), soit la distance entre les nœuds centraux. Cette valeur doit être un tuple d'entiers positifs, dont la longueur correspond à l'arité du faisceau. Chaque composant de ce tuple doit avoir une valeur inférieure ou égale au composant correspondant de l'élément **KernelShape**. La valeur par défaut est un tuple dont tous les éléments sont égaux à un.
-	**Sharing** : (facultatif) définit le partage des poids pour chaque dimension de la convolution. La valeur peut être une valeur booléenne unique ou un tuple de valeurs booléennes, dont la longueur est égale à l'arité du faisceau. Une valeur booléenne unique est étendue de façon à devenir un tuple de la bonne longueur dont tous les éléments sont égaux à la valeur spécifiée. La valeur par défaut est un tuple composé uniquement de valeurs True.
-	**MapCount** : (facultatif) définit le nombre de signatures pour le faisceau convolutionnel. Cette valeur peut être un entier positif unique ou un tuple d’entiers positifs dont la longueur est égale à l’arité du faisceau. Une valeur d'entier unique est étendue de façon à devenir un tuple de la bonne longueur dont les premiers éléments sont égaux à la valeur spécifiée et dont tous les éléments restants sont égaux à un. La valeur par défaut est 1. Le nombre total de signatures est le produit des éléments du tuple. La factorisation de ce nombre total sur les éléments détermine la façon dont les valeurs de signature sont regroupées dans les nœuds de destination.
-	**Weights** : (facultatif) définit les poids initiaux du faisceau. La valeur doit être un tuple de valeurs à virgule flottante dont la longueur correspond au nombre de noyaux, multiplié par le nombre de poids par noyau, tel que défini plus loin dans cet article. Les poids par défaut sont générés de façon aléatoire.

Il existe deux ensembles de propriétés contrôlant le remplissage, qui s'excluent mutuellement :

-	**Padding** : (facultatif) détermine si l'entrée doit être remplie selon un **schéma de remplissage par défaut**. La valeur peut être une valeur booléenne unique ou un tuple de valeurs booléennes, dont la longueur est égale à l’arité du faisceau. Une valeur booléenne unique est étendue de façon à devenir un tuple de la bonne longueur dont tous les éléments sont égaux à la valeur spécifiée. Si la valeur d'une dimension correspond à True, la source est remplie de façon logique dans cette dimension par des cellules de valeur zéro afin de prendre en charge d'autres applications de noyau, de façon que les nœuds centraux des premier et dernier noyaux de cette dimension soient les premier et dernier nœuds de cette dimension dans la couche source. Ainsi, le nombre de nœuds « factices » de chaque dimension est déterminé automatiquement, afin de correspondre exactement à _(InputShape[d] - 1) / Stride[d] + 1_ noyaux dans la couche source remplie. Si la valeur d’une dimension correspond à False, les noyaux sont définis de façon que le nombre de nœuds omis soit le même de chaque côté (une différence de 1 au maximum est tolérée). La valeur par défaut de cet attribut est un tuple dont tous les éléments ont la valeur False.
-	**UpperPad** et **LowerPad** : (facultatifs) permettent de contrôler la quantité de remplissage à utiliser. **Important :** ces attributs peuvent être définis si et seulement si la propriété **Padding** ci-dessus n'est ***pas*** définie. Les valeurs doivent être des tuples d’entiers dont la longueur est égale à l’arité du faisceau. Lorsque ces attributs sont spécifiés, des nœuds « factices » sont ajoutés aux extrémités inférieure et supérieure de chaque dimension de la couche d’entrée. Le nombre de nœuds ajoutés aux extrémités inférieure et supérieure de la dimension est déterminé respectivement par **LowerPad**[i] et **UpperPad**[i]. Afin de veiller à ce que les noyaux correspondent à des nœuds « réels » et non « factices », les conditions suivantes doivent être respectées :
	-	Chaque élément de **LowerPad** doit être strictement inférieur à KernelShape[d]/2.
	-	Chaque élément de **UpperPad** doit être inférieur à KernelShape[d]/2.
	-	La valeur par défaut de ces attributs est un tuple dont tous les éléments sont égaux à 0.

Le paramètre **Padding** = true permet d’obtenir le remplissage requis pour maintenir le « centre » du noyau à l’intérieur de l’entrée « réelle ». Cela modifie un peu le calcul de la taille de sortie. En règle générale, la taille de sortie _D_ est calculée comme suit : _D = (I - K) / S + 1_, où _I_ est la taille d'entrée, _K_ est la taille du noyau, _S_ est le stride, et _/_ est la division d'entier (arrondi vers zéro). Si vous définissez UpperPad = [1, 1], la taille d'entrée _I_ est effectivement 29 et, par conséquent, _D = (29-5) / 2 + 1 = 13_. Toutefois, lorsque **Padding** = true, _I_ est essentiellement augmenté de _K - 1_ ; donc _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. En spécifiant des valeurs pour **UpperPad** et **LowerPad**, vous obtenez un meilleur contrôle sur le remplissage que si vous définissez simplement **Padding** = true.

Pour plus d’informations sur les réseaux convolutionnels et leurs applications, consultez les articles suivants :

-	[http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
-	[http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf)
-	[http://people.csail.mit.edu/jvb/papers/cnn\_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)

## Faisceaux de regroupement
Un **faisceau de regroupement** applique une géométrie similaire à la connectivité convolutionnelle, mais utilise des fonctions prédéfinies pour dériver les valeurs du nœud source vers la valeur du nœud de destination. De ce fait, les faisceaux de regroupement n’ont pas d’état entraînable (poids ou biais). Ils prennent en charge tous les attributs convolutionnels, hormis **Sharing**, **MapCount** et **Weights**.

En général, les noyaux résumés par des unités de regroupement adjacentes ne se chevauchent pas. Si Stride[d] est égal à KernelShape[d] dans chaque dimension, la couche obtenue est la couche de regroupement locale traditionnelle, qui est généralement employée dans les réseaux neuronaux convolutionnels. Chaque nœud de destination calcule le maximum ou la moyenne des activités de son noyau dans la couche source.

L'exemple suivant illustre un faisceau de regroupement :

	hidden P1 [5, 12, 12]
	  from C1 max pool {
	    InputShape  = [ 5, 24, 24];
	    KernelShape = [ 1,  2,  2];
	    Stride      = [ 1,  2,  2];
	  }  

-	L'arité du faisceau est de 3 (longueur des tuples **InputShape**, **KernelShape** et **Stride**).
-	Le nombre de nœuds de la couche source est égal à _5 * 24 * 24 = 2 880_.
-	Il s'agit d'une couche de regroupement locale traditionnelle, car les valeurs **KernelShape** et **Stride** sont égales.
-	Le nombre de nœuds de la couche de destination est égal à _5 * 12 * 12 = 1 440_.
	
Pour plus d’informations sur les couches de regroupement, consultez les articles suivants :

-	[http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Section 3.4)
-	[http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
-	[http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
	
## Faisceaux de normalisation de réponse
La **normalisation de réponse** est un schéma de normalisation local proposé par Geoffrey Hinton (et al.) dans un article intitulé [ImageNet Classiﬁcation with Deep Convolutional Neural Networks](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Classification ImageNet avec les réseaux neuronaux profonds). La normalisation de réponse permet de contribuer à la généralisation dans les réseaux neuronaux. Lorsqu’un neurone s’active à un niveau très élevé, la couche de normalisation de réponse locale supprime le niveau d’activation des neurones alentour. Pour cela, on utilise trois paramètres (***α***, ***β*** et ***k***), ainsi qu'une structure convolutionnelle (ou forme de voisinage). Chaque neurone ***y*** de la couche de destination correspond à un neurone ***x*** de la couche source. Le niveau de l'activation de l'élément ***y*** est calculé via la formule suivante, où ***f*** correspond au niveau de l'activation d'un neurone de la valeur ***Nx***, au noyau (ou à l'ensemble qui contient les neurones dans le voisinage de l'élément ***x***), comme défini par la structure convolutionnelle suivante :

![][1]

Les faisceaux de normalisation de réponse prennent en charge tous les attributs convolutionnels, hormis **Sharing**, **MapCount** et **Weights**.
 
-	Si le noyau contient des neurones de la même signature que ***x***, le schéma de normalisation est appelé **normalisation de même signature**. Pour définir une normalisation de ce type, la première coordonnée de **InputShape** doit avoir la valeur 1.
-	Si le noyau contient des neurones dans la même position spatiale que ***x***, mais situés dans d'autres signatures, le schéma de normalisation est appelé **normalisation intersignature**. Ce type de normalisation de réponse implémente une forme d'inhibition latérale inspirée par le type trouvé dans les vrais neurones et qui crée une compétition pour des niveaux d'activation élevés entre les sorties neuronales calculées sur différentes signatures. Pour définir une normalisation intersignature, la première coordonnée doit être un entier supérieur à 1 et inférieur ou égal au nombre de signatures. Les coordonnées restantes doivent avoir la valeur 1.

Les faisceaux de normalisation de réponse appliquant une fonction prédéfinie aux valeurs de nœud source pour déterminer la valeur du nœud de destination, ils n’ont pas d’état entraînable (poids ou biais).

**Alerte** : les nœuds de la couche de destination correspondent aux neurones centraux des noyaux. Par exemple, si KernelShape[d] est impair, alors _KernelShape[d]/2_ correspond au nœud de noyau central. Si la valeur _KernelShape[d]_ est paire, le nœud central a la valeur _KernelShape[d]/2 - 1_. Par conséquent, si le paramètre **Padding**[d] a la valeur False, les premier et dernier nœuds _KernelShape[d]/2_ ne sont associés à aucun nœud correspondant dans la couche de destination. Pour éviter cette situation, définissez **Padding** sur [true, true, …, true].

Outre les quatre attributs décrits précédemment, les faisceaux de normalisation de réponse prennent également en charge les attributs suivants :

-	**Alpha** : (obligatoire) spécifie une valeur à virgule flottante qui correspond à ***α*** dans la formule précédente.
-	**Beta** : (obligatoire) spécifie une valeur à virgule flottante qui correspond à ***β*** dans la formule précédente.
-	**Offset** : (facultatif) spécifie une valeur à virgule flottante correspondant à l'élément ***k*** dans la formule précédente. Par défaut, elle est de 1.

L'exemple suivant définit un faisceau de normalisation de réponse utilisant ces attributs :

	hidden RN1 [5, 10, 10]
	  from P1 response norm {
	    InputShape  = [ 5, 12, 12];
	    KernelShape = [ 1,  3,  3];
	    Alpha = 0.001;
	    Beta = 0.75;
	  }  

-	La couche source inclut cinq signatures, chacune présentant des dimensions 12x12, pour un total de 1 440 nœuds.
-	La valeur de **KernelShape** indique qu'il s'agit d'une couche de normalisation de même signature, où le voisinage est un rectangle 3x3.
-	La valeur par défaut de **Padding** est False. La couche de destination n'a donc que 10 nœuds par dimension. Pour inclure un seul nœud dans la couche de destination correspondant à tous les nœuds de la couche source, ajoutez la chaîne Padding = [true, true, true] et remplacez la taille de l'élément RN1 par [5, 12, 12].

## Déclaration de partage 
Net# peut prendre en charge la définition de plusieurs faisceaux avec des poids partagés. Les poids de deux faisceaux quelconques peuvent être partagés tant qu’ils ont la même structure. La syntaxe suivante définit des faisceaux avec des poids partagés :

	share-declaration:
	    share    {    layer-list    }
	    share    {    bundle-list    }
	   share    {    bias-list    }
	
	layer-list:
	    layer-name    ,    layer-name
	    layer-list    ,    layer-name
	
	bundle-list:
	   bundle-spec    ,    bundle-spec
	    bundle-list    ,    bundle-spec
	
	bundle-spec:
	   layer-name    =>     layer-name
	
	bias-list:
	    bias-spec    ,    bias-spec
	    bias-list    ,    bias-spec
	
	bias-spec:
	    1    =>    layer-name
	
	layer-name:
	    identifier  

Par exemple, la déclaration de partage spécifie les noms de couches, indiquant que les poids et biais doivent être partagés :

	Const {
	  InputSize = 37;
	  HiddenSize = 50;
	}
	input {
	  Data1 [InputSize];
	  Data2 [InputSize];
	}
	hidden {
	  H1 [HiddenSize] from Data1 all;
	  H2 [HiddenSize] from Data2 all;
	}
	output Result [2] {
	  from H1 all;
	  from H2 all;
	}
	share { H1, H2 } // share both weights and biases  

-	Les caractéristiques d'entrée sont partitionnées en deux couches d'entrée de même taille.
-	Les couches masquées calculent alors des caractéristiques de niveau supérieur sur les deux couches d'entrée.
-	La déclaration de partage spécifie que _H1_ et _H2_ doivent être calculés de la même façon à partir de leurs entrées respectives.
 
Il est également possible de spécifier cet élément avec deux déclarations de partage indépendantes, comme suit :

	share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

	share { 1 => H1, 1 => H2 } // share biases  

Vous ne pouvez utiliser la forme abrégée que si les couches contiennent un seul faisceau. En général, le partage n’est possible que lorsque les structures concernées sont identiques, c’est-à-dire si elles ont la même taille, la même géométrie convolutionnelle, etc.

## Exemples d’utilisation de Net#
Cette section fournit des exemples d’utilisation de Net# pour ajouter des couches masquées, définir la façon dont elles interagissent avec les autres couches et construire des réseaux convolutionnels.

### Définition d'un réseau neuronal personnalisé simple : exemple « Hello World »
Cet exemple simple montre comment créer un modèle de réseau neuronal contenant une seule couche masquée.

	input Data auto;
	hidden H [200] from Data all;
	output Out [10] sigmoid from H all;  

Cet exemple illustre certaines commandes de base, comme suit :

-	La première ligne définit la couche d'entrée (nommé _Données_). Lorsque vous utilisez le mot-clé **Automatique**, le réseau neuronal inclut automatiquement toutes les colonnes de fonctionnalité dans les exemples d'entrée.
-	La deuxième ligne crée la couche masquée. Le nom _H_ est affecté à la couche masquée, qui contient 200 nœuds. Cette couche est entièrement connectée à la couche d’entrée.
-	La troisième ligne définit la couche de sortie (appelée _O_), qui contient 10 nœuds de sortie. Si le réseau neuronal est utilisé pour une classification, il y a un nœud de sortie par classe. Le mot clé **sigmoid** indique que la fonction de sortie est appliquée à la couche de sortie.

### Définir plusieurs couches masquées : exemple de vision
L’exemple suivant montre comment définir un réseau neuronal un peu plus complexe, avec plusieurs couches masquées personnalisées.

	// Define the input layers 
	input Pixels [10, 20];
	input MetaData [7];
	
	// Define the first two hidden layers, using data only from the Pixels input
	hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
	
	// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
	hidden Gather [100] 
	{
	  from ByRow all;
	  from ByCol all;
	}
	
	// Define the output layer and its sources
	output Result [10]  
	{
	  from Gather all;
	  from MetaData all;
	}  

Cet exemple illustre plusieurs caractéristiques du langage de spécification des réseaux neuronaux :

-	La structure a deux couches d'entrée, _Pixels_ et _MetaData_.
-	La couche _Pixels_ est une couche source pour deux ensembles de connexion, avec les couches de destination, _ByRow_ et _ByCol_.
-	Les couches _Gather_ et _Result_ sont des couches de destination dans plusieurs ensembles de connexion.
-	La couche de sortie, _Result_, est une couche de destination dans deux ensembles de connexion, le premier indiquant la deuxième couche masquée (Gather) en tant que couche de destination et le deuxième (MetaData) en tant que couche de destination.
-	Les couches masquées _ByRow_ et _ByCol_ spécifient une connectivité filtrée en utilisant des expressions de prédicat. Plus précisément, le nœud dans _ByRow_ à [x, y] est connecté aux nœuds dans l'élément _Pixels_, car la première coordonnée d'index est égale à la première coordonnée du nœud, x. De même, le nœud de l'élément \_ByCol à [x, y] est connecté à ces nœuds dans _Pixels_, dont la deuxième coordonnée d'index se trouve dans la deuxième coordonnée du nœud, y.

### Définir un réseau convolutionnel pour la classification multiclasse : exemple de reconnaissance de chiffre
La définition du réseau ci-après, conçu pour reconnaître les chiffres, illustre certaines techniques avancées de personnalisation d’un réseau neuronal.

	input Image [29, 29];
	hidden Conv1 [5, 13, 13] from Image convolve 
	{
	   InputShape  = [29, 29];
	   KernelShape = [ 5,  5];
	   Stride      = [ 2,  2];
	   MapCount    = 5;
	}
	hidden Conv2 [50, 5, 5]
	from Conv1 convolve 
	{
	   InputShape  = [ 5, 13, 13];
	   KernelShape = [ 1,  5,  5];
	   Stride      = [ 1,  2,  2];
	   Sharing     = [false, true, true];
	   MapCount    = 10;
	}
	hidden Hid3 [100] from Conv2 all;
	output Digit [10] from Hid3 all;  


-	La structure a une seule couche d'entrée, _Image_.
-	Le mot clé **convolve** indique que les couches nommées _Conv1_ et _Conv2_ sont des couches convolutionnelles. Chacune de ces déclarations de couche est suivie d'une liste des attributs de convolution.
-	Le réseau contient une troisième couche masquée, _Hid3_, entièrement connectée à la deuxième couche masquée, _Conv2_.
-	La couche de sortie, _Digit_, n'est connectée qu'à la troisième couche masquée, _Hid3_. Le mot clé **all** indique que la couche de sortie est entièrement connectée à _Hid3_.
-	L'arité de la convolution est de 3 (longueur des tuples **InputShape**, **KernelShape**, **Stride** et **Sharing**).
-	Le nombre de poids par noyau est de _1 + **KernelShape**\[0] * **KernelShape**\[1] * **KernelShape**\[2] = 1 + 1 * 5 * 5 = 26. Or 26 * 50 = 1300_.
-	Vous pouvez calculer les nœuds de chaque couche masquée comme suit :
	-	**NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
	-	**NodeCount**\[1] = (13 - 5) / 2 + 1 = 5.
	-	**NodeCount**\[2] = (13 - 5) / 2 + 1 = 5.
-	Vous pouvez calculer le nombre total de nœuds en utilisant la dimensionnalité déclarée de la couche, [50, 5, 5], comme suit : _**MapCount** * **NodeCount**\[0] * **NodeCount**\[1] * **NodeCount**\[2] = 10 * 5 * 5 * 5_
-	Puisque **Sharing**[d] est faux uniquement pour _d == 0_, le nombre de noyaux est _**MapCount** * **NodeCount**\[0] * **NodeCount**\[1] * **NodeCount**\[2] = 10 * 5 * 5 * 5_


## Remerciements

Le langage Net # pour la personnalisation de l'architecture des réseaux neuronaux a été développée chez Microsoft par Shon Katzenberger (architecte, Machine Learning) et Alexey Kamenev (ingénieur logiciel, Microsoft Research). Il est utilisé en interne pour l'apprentissage de projets et d'applications allant de la détection d'images à l'analyse de texte. Pour plus d'informations, consultez [Réseaux neuronaux dans Azure ML - présentation de Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx) (en Anglais)


[1]: ./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 

<!---HONumber=AcomDC_0914_2016-->