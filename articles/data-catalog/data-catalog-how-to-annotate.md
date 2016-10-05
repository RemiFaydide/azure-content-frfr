<properties
   pageTitle="Annotation de sources de données | Microsoft Azure"
   description="Article de procédure relatif à l’annotation de ressources de données dans Azure Data Catalog et abordant notamment les noms conviviaux, les balises, les descriptions et les experts."
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


# Annotation de sources de données

## Introduction
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui sert de système d'inscription et de détection des sources de données d'entreprise. En d’autres termes, Data Catalog vise essentiellement à aider les utilisateurs à détecter, comprendre et utiliser des sources de données, et à permettre aux organisations de mieux exploiter leurs données existantes. Lorsqu’une source de données est inscrite dans Data Catalog, ses métadonnées sont copiées et indexées par le service. Mais ce n’est pas tout. Data Catalog permet aux utilisateurs de fournir leurs propres métadonnées descriptives (descriptions et balises, par exemple) pour compléter les métadonnées extraites de la source de données et favoriser la compréhension de la source de données pour un plus grand nombre de personnes.

## Annotation et crowdsourcing
Tout le monde possède un avis. Et c’est une bonne chose. Avec Data Catalog, les perspectives de chaque utilisateur à l’égard des sources de données d’entreprise sont prises en compte et valorisées. Examinez le scénario suivant :

* L’administrateur système connaît le contrat de niveau de service associé aux serveurs ou services qui hébergent la source de données.
* L’administrateur de base de données connaît la planification de sauvegarde pour chaque base de données et les fenêtres de traitement ETL autorisées.
* Le propriétaire du système connaît le processus permettant aux utilisateurs de demander l'accès à la source de données.
* Le gestionnaire de données sait de quelle façon les ressources et attributs de la source de données sont mappés au modèle de données d’entreprise.
* L’analyste sait comment les données sont utilisées dans le cadre des processus d’entreprise qu’il prend en charge.

Chacune de ces perspectives est précieuse, et Data Catalog utilise une approche de crowdsourcing à l’égard des métadonnées, qui permet de capturer et d’utiliser chacune d’elles en vue d’établir une image complète des sources de données inscrites. À l’aide du portail Data Catalog, chaque utilisateur peut ajouter et modifier ses propres annotations, tout en ayant la possibilité de consulter les annotations apportées par les autres utilisateurs.

## Différents types d’annotations
Data Catalog prend en charge les types d’annotations suivants :

| Annotation | Remarques |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom convivial | Les noms conviviaux peuvent être fournis au niveau des ressources de données pour en favoriser la compréhension. Les noms conviviaux sont particulièrement utiles lorsque le nom d’objet sous-jacent est peu clair, abrégé ou peu explicite auprès des utilisateurs. |
| Description | Des descriptions peuvent être fournies aux niveaux des sources de données et attributs / colonnes. Les descriptions sont de courtes annotations de texte libre décrivant la perspective de l’utilisateur sur la ressource de données ou son utilisation. |
| Balises (balises utilisateur) | Des balises peuvent être fournies aux niveaux des sources de données et attributs / colonnes. Les balises utilisateur sont des étiquettes définies par l’utilisateur qui permettent de classer les ressources de données ou les attributs. |
| Balises (balises glossaire) | Des balises peuvent être fournies aux niveaux des sources de données et attributs / colonnes. Les balises de glossaire sont des termes de glossaire définis de façon centralisée qui permettent de classer les ressources de données ou les attributs à l’aide d’une taxonomie métier commune. Pour plus d’informations, consultez l’article [Comment configurer le glossaire métier pour un balisage géré](data-catalog-how-to-business-glossary.md). |
| Experts | Des experts peuvent être fournis au niveau des ressources de données. Les experts identifient les utilisateurs ou groupes avec des perspectives d’expert sur les données et peuvent servir de points de contact pour les utilisateurs qui détectent les sources de données inscrites et se posent des questions auxquelles les annotations existantes n’apportent pas de réponse. |
| Demander l'accès | Des informations d’accès de requête peuvent être fournies au niveau des ressources de données. Ces informations sont destinées aux utilisateurs qui détectent une source de données à laquelle ils ne sont pas encore autorisés à accéder. Les utilisateurs peuvent saisir l’adresse de messagerie de l’utilisateur ou du groupe qui accorde l’accès ou l’URL du processus ou de l’outil dont les utilisateurs ont besoin pour obtenir l’accès. Ils peuvent également indiquer le processus lui-même sous forme de texte. |
| Documentation | Une documentation peut être fournie au niveau des ressources de données. La documentation des ressources présente des informations complètes pouvant inclure des liens et des images et pouvant fournir des données non véhiculées par les descriptions et les balises. |


## Annotation de plusieurs ressources
Lorsque vous sélectionnez plusieurs ressources de données dans le portail Data Catalog, les utilisateurs peuvent annoter toutes les ressources sélectionnées en une seule opération. Les annotations s’appliqueront à toutes les ressources sélectionnées, favorisant ainsi la sélection et la fourniture d’une description cohérente et de jeux de balises et d’experts pour les ressources de données associées.

> [AZURE.NOTE] Les balises et les experts peuvent également être fournis lors de l’inscription des ressources de données à l’aide de l’outil d’inscription des sources de données de Data Catalog.

Si vous sélectionnez plusieurs tables et vues, seules les colonnes communes à toutes les ressources de données sélectionnées seront affichées dans le portail Data Catalog. Cela permet aux utilisateurs de fournir des balises et des descriptions pour toutes les colonnes portant le même nom pour l’ensemble des ressources sélectionnées.

## Annotations et détection
Les métadonnées extraites à partir de la source de données lors de l’inscription sont ajoutées à l’index de recherche de Data Catalog. De la même façon, les métadonnées fournies par l’utilisateur sont également indexées. Ainsi, les annotations favorisent non seulement la compréhension des données détectées par les utilisateurs, mais également la détection des ressources de données annotées, grâce à la possibilité d’effectuer des recherches de termes explicites pour les utilisateurs.

## Résumé
L’inscription d’une source de données avec Data Catalog favorise la détection des données en copiant les métadonnées descriptives et structurelles à partir de la source de données dans le service Catalog. Une fois qu’une source de données a été inscrite, les utilisateurs peuvent fournir des annotations pour faciliter la détection et la compréhension des données par le biais du portail Data Catalog.

## Voir aussi
- Didacticiel [Prise en main d’Azure Data Catalog](data-catalog-get-started.md) décrivant la procédure pas à pas d’annotation des sources de données.

<!---HONumber=AcomDC_0921_2016-->