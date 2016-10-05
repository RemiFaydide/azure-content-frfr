<properties
	pageTitle="Comment configurer le glossaire métier pour un balisage géré | Microsoft Azure"
	description="Article de procédure relatif au glossaire métier d’Azure Data Catalog, qui permet de définir et d’utiliser un vocabulaire métier commun en vue de baliser les ressources de données inscrites."
	services="data-catalog"
	documentationCenter=""
	authors="steelanddata"
	manager="NA"
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="09/21/2016"
	ms.author="maroche"/>

# Comment configurer le glossaire métier pour un balisage géré

## Introduction

Azure Data Catalog intègre des fonctionnalités de découverte de sources de données qui permettent aux utilisateurs de découvrir et comprendre facilement les sources de données dont ils ont besoin pour effectuer des analyses et prendre des décisions. Ces fonctionnalités de découverte se révèlent tout particulièrement utiles quand les utilisateurs peuvent trouver et comprendre le plus large éventail de sources de données disponibles.

L’une des fonctionnalité de Data Catalog qui favorise une meilleure compréhension des données de ressources est le balisage. Le balisage permet aux utilisateurs d’associer des mots clés à une ressource ou à une colonne, qui facilite à son tour la découverte de la ressource via la recherche et la navigation, et permet aux utilisateurs de comprendre plus facilement le contexte et l’intention de la ressource.

Cependant, le balisage peut parfois engendrer des problèmes qui lui sont propres. Voici quelques exemples de problèmes potentiels liés au balisage :

1.	Utilisateurs balisant certaines ressources avec des abréviations et d’autres avec du texte développé. Cette incohérence est un obstacle à la découverte de ressources même si l’intention était de baliser les ressources avec la même balise.
2.	Balises ayant plusieurs significations selon le contexte. Par exemple, l’application de la balise « Chiffre d’affaires » sur un jeu de données clients pourra-t-elle désigner le chiffre d’affaires par client, alors que l’application de la même balise appliquée à un jeu de données de ventes trimestrielles pourra désigner le chiffre d’affaires trimestriel de l’entreprise.

Pour faire face à ce type de difficulté, Data Catalog met à disposition un glossaire métier.

Le glossaire métier de Data Catalog permet aux organisations de rassembler les principaux termes métier et leurs définitions pour créer un vocabulaire métier commun. Cette gouvernance favorise une utilisation cohérente des données à l’échelle de l’organisation. Une fois que des termes sont définis dans le glossaire métier, ils peuvent être affectés à des ressources de données dans le catalogue, selon le même principe que le balisage, ce qui autorise ainsi un _balisage géré_.

> [AZURE.NOTE] La fonctionnalité décrite dans cet article est uniquement disponible dans l’édition Standard d’Azure Data Catalog. L’édition gratuite ne propose pas de fonctionnalités de balisage géré ni de glossaire métier.

## Disponibilité du glossaire et privilèges

/*Le glossaire métier est disponible dans l’Édition Standard d’Azure Data Catalog. L’Édition gratuite de Data Catalog n’intègre pas de glossaire.*/

Le glossaire métier est accessibles via l’option « Glossaire » du menu de navigation du portail Data Catalog.

![Accès au glossaire métier](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Les administrateurs de Data Catalog et les membres du rôle Administrateurs de glossaire peuvent créer, modifier et supprimer des termes dans le glossaire métier. Tous les utilisateurs de Data Catalog peuvent consulter les définitions des termes et baliser les ressources avec ces termes du glossaire.

![Ajout d’un nouveau terme de glossaire](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## Création de termes de glossaire

Les administrateurs de Data Catalog et les administrateurs de glossaire peuvent créer des termes de glossaire en cliquant sur le bouton Nouveau terme. Des termes de glossaire sont alors créés avec les champs suivants :

* définition métier du terme ;
* description qui rend compte de l’usage prévu ou des règles métier de la ressource/colonne ;
* liste des parties prenantes qui connaissent le mieux le terme ;
* terme parent qui définit la hiérarchie dont dépend le terme.


## Hiérarchies de termes de glossaire

Le glossaire métier de Data Catalog permet de décrire le vocabulaire métier sous la forme d’une hiérarchie de termes. Les organisations peuvent ainsi créer une classification de termes qui représente mieux leur taxonomie métier.

La désignation d’un terme doit être unique à un niveau donné de la hiérarchie (les noms en double ne sont pas autorisés). Il n’existe aucune limite quant au nombre de niveaux dont est constituée une hiérarchie, mais celle-ci sera plus facile à cerner si elle n’en comporte pas plus de trois.

L’utilisation de hiérarchies dans le glossaire métier est facultative. Le fait de laisser le champ Terme parent vide pour des termes du glossaire a pour effet de créer une liste de termes plate (non hiérarchisée) dans le glossaire.

## Balisage de ressources avec des termes de glossaire

Dès lors que des termes de glossaire sont définis dans le catalogue, le balisage de ressources est optimisé pour les recherches dans le glossaire à mesure que l’utilisateur tape sa balise. Le portail Data Catalog affiche une liste de termes de glossaire correspondants que l’utilisateur peut choisir. Si l’utilisateur sélectionne un terme de glossaire dans la liste, il est ajouté à la ressource sous forme de balise (c’est ce que l’on appelle une balise de glossaire). L’utilisateur peut aussi choisir de créer une balise en tapant un terme qui ne se trouve pas dans le glossaire (dans ce cas, on parle de balise utilisateur).

![Ressource de données balisée avec une balise utilisateur et deux balises de glossaire](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] La balise utilisateur est le seul type de balise pris en charge dans l’édition gratuite de Data Catalog.

### Comportement du pointage sur les balises
Dans le portail Data Catalog, les deux types de balise se distinguent l’un de l’autre tant sur le plan visuel que sur le plan du comportement du pointage. Quand l’utilisateur pointe sur une balise utilisateur, il peut voir le texte de la balise et l’identité du ou des utilisateurs ayant ajouté la balise. Quand l’utilisateur pointe sur une balise de glossaire, il obtient aussi la définition du terme de glossaire, ainsi qu’un lien permettant d’ouvrir le glossaire métier pour y consulter la définition complète du terme.

### Filtres de recherche pour les balises
Les balises de glossaire comme les balises utilisateur peuvent non seulement faire l’objet de recherches, mais elles peuvent aussi servir de filtres dans une recherche.

## Résumé
Le glossaire métier d’Azure Data Catalog et le balisage géré qu’il autorise permettent d’identifier, de gérer et de découvrir les ressources de données de façon cohérente. Le glossaire métier offre la possibilité aux utilisateurs d’une organisation d’apprendre le vocabulaire métier et contribue à la capture de métadonnées explicites, ce qui fait de la découverte et de l’identification des ressources un jeu d’enfant.

## Voir aussi

- [Documentation de l’API REST pour les opérations de glossaire métier](https://msdn.microsoft.com/library/mt708855.aspx)

<!---HONumber=AcomDC_0921_2016-->