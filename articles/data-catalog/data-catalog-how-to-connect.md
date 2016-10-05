<properties
   pageTitle="Connexion aux sources de données « Big Data » | Microsoft Azure"
   description="Article de procédure relatif à la connexion aux sources de données détectées avec Azure Data Catalog."
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
   ms.date="09/15/2016"
   ms.author="maroche"/>


# Connexion aux sources de données

## Introduction
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui sert de système d'inscription et de détection des sources de données d'entreprise. En d'autres termes, **Microsoft Azure Data Catalog** vise essentiellement à aider les utilisateurs à détecter, comprendre et utiliser des sources de données et permet aux organisations de mieux exploiter leurs données. L’utilisation des données représente un aspect clé de ce scénario. Lorsqu’un utilisateur détecte une source de données et en comprend l’objet, l’étape suivante consiste à se connecter à la source de données pour pouvoir exploiter les données correspondantes.

## Emplacements des sources de données
Pendant l’inscription des sources de données, **Azure Data Catalog** reçoit les métadonnées de la source de données. Ces métadonnées incluent des détails concernant l’emplacement de la source de données. Ceux-ci varient selon les sources de données, mais contiennent toujours les informations requises pour la connexion. Par exemple, l’emplacement d’une table SQL Server inclut le nom du serveur, le nom de la base de données, le nom du schéma et le nom de la table, tandis que l’emplacement d’un rapport SQL Server Reporting Services inclut le nom du serveur et le chemin d’accès au rapport. Pour d’autres types de sources de données, les emplacements reflètent la structure et les fonctionnalités du système source.

## Outils clients intégrés
Le moyen le plus simple de vous connecter à une source de données consiste à utiliser le menu « Ouvrir dans... » du portail **Azure Data Catalog**. Ce menu affiche une liste d’options pour la connexion à la ressource de données sélectionnée. Lorsque vous utilisez l’affichage en mosaïque par défaut, ce menu est disponible sur chaque vignette.

 ![Ouverture d’une table SQL Server dans Excel à partir de la vignette de ressource de données](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Lorsque vous utilisez l’affichage par liste, le menu est disponible dans la barre de recherche, en haut de la fenêtre du portail.

 ![Ouverture d’un rapport SQL Server Reporting Services dans le Gestionnaire de rapports à partir de la barre de recherche](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## Applications clientes prises en charge
Lorsque vous utilisez le menu « Ouvrir dans... » pour les sources de données dans le portail Azure Data Catalog, l’application cliente appropriée doit être installée sur l’ordinateur client.

| Ouvrir dans l’application | Extension de fichier/protocole | Versions d’application prises en charge |
| --- | --- | --- |
| Excel | .odc | Excel 2010 ou version ultérieure |
| Excel (1 000 premiers) | .odc | Excel 2010 ou version ultérieure |
| Power Query | .xlsx | Excel 2016 ou Excel 2010 ou Excel 2013 avec la macro complémentaire Power Query pour Excel installée
| Power BI Desktop | .pbix | Power BI Desktop juillet 2016 ou version ultérieure |
| SQL Server Data Tools | vsweb:// | Visual Studio 2013 Update 4 ou version ultérieure avec les outils SQL Server installés |
| Gestionnaire de rapports | http:// | Voir la [configuration de navigateur requise pour SQL Server Reporting Services](https://technet.microsoft.com/fr-FR/library/ms156511.aspx) |

## Vos données, vos outils
Les options disponibles dans le menu dépendent du type de ressource de données actuellement sélectionné. Bien entendu, le menu « Ouvrir dans... » ne présente pas tous les outils applicables. Cependant, il est relativement simple de se connecter à la source de données à l’aide de n’importe quel outil client. Lorsqu’une ressource de données est sélectionnée dans le portail **Azure Data Catalog**, l’emplacement complet s’affiche dans le volet des propriétés.

 ![Informations de connexion pour une table SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Les informations de connexion diffèrent selon le type de source de données, mais les informations incluses dans le portail vous fourniront tout ce dont vous avez besoin pour vous connecter à la source de données avec n’importe quel outil client. Les utilisateurs peuvent copier les détails de connexion relatifs aux sources de données qu’ils ont détectées à l’aide d’**Azure Data Catalog**. Ils peuvent ainsi utiliser les données avec l’outil de leur choix.

## Connexion et autorisations de sources de données
Même si **Azure Data Catalog** rend les sources de données détectables, l’accès aux données proprement dites reste sous le contrôle du propriétaire ou de l’administrateur des sources de données. La détection d’une source de données dans **Azure Data Catalog** ne donne pas à l’utilisateur l’autorisation d’accéder à la source de données proprement dite.

À des fins de simplicité, les utilisateurs qui détectent une source de données mais ne sont pas autorisés à accéder aux données correspondantes peuvent fournir des informations via la propriété Request Access lors de l’annotation d’une source de données. Les informations fournies ici (notamment les liens vers le processus ou le point de contact pour obtenir l’accès à la source de données) sont présentées avec les informations d’emplacement de source de données, dans le portail.

 ![Informations de connexion avec fourniture des instructions d’accès de requête](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##Résumé
L’inscription d’une source de données avec **Azure Data Catalog** favorise la détection des données. Cette étape consiste à copier les métadonnées descriptives et structurelles à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été inscrite et détectée, les utilisateurs peuvent se connecter à la source de données à partir du menu « Ouvrir dans... » du portail **Azure Data Catalog** ou à l’aide des outils de données de leur choix.

## Voir aussi
- Didacticiel [Prise en main d’Azure Data Catalog](data-catalog-get-started.md) décrivant la procédure pas à pas de connexion à des sources de données.

<!---HONumber=AcomDC_0921_2016-->