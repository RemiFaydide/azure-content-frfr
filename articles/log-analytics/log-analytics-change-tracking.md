<properties
	pageTitle="Solution de suivi des modifications dans Log Analytics | Microsoft Azure"
	description="Vous pouvez utiliser la solution de suivi des modifications de configuration dans Log Analytics pour vous aider à identifier facilement les logiciels et les modifications des services Windows qui se produisent dans votre environnement : l’identification de ces modifications de configuration peut vous aider à identifier les problèmes opérationnels."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="banders"/>

# Solution de suivi des modifications dans Log Analytics


Vous pouvez utiliser la solution de suivi des modifications de configuration dans Log Analytics pour vous aider à identifier facilement les logiciels et les modifications des services Windows qui se produisent dans votre environnement : l’identification de ces modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Vous installez la solution pour mettre à jour le type d’agent que vous avez installé. Les modifications apportées à des logiciels installés et aux services Windows sur les serveurs analysés sont lues, puis les données sont envoyées au service cloud Log Analytics pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. Lorsque des modifications sont détectées, les serveurs comprenant des modifications sont affichés dans le tableau de bord de suivi des modifications. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

## Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- La solution de suivi des modifications nécessite Operations Manager.
- Vous devez disposer d’un agent Windows ou Operations Manager sur chaque ordinateur où vous souhaitez analyser les modifications.
- Ajoutez la solution de suivi des modifications à votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.


## Détails de la collecte de données de suivi des modifications

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte en vue du suivi des modifications.


| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Non](./media/log-analytics-change-tracking/oms-bullet-red.png)| ![Non](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)| Toutes les heures|


## Utilisation du suivi des modifications

Une fois la solution installée, vous pouvez afficher le résumé des modifications pour vos serveurs analysés à l’aide de la vignette **Suivi des modifications** dans la page **Vue d’ensemble** d’OMS.

![image de la vignette de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Vous pouvez afficher les modifications apportées à votre infrastructure, puis étudier en détail les informations pour les catégories suivantes :

- Modifications selon le type de configuration pour les logiciels et services Windows
- Modifications logicielles des applications et mises à jour pour chaque serveur
- Nombre total de modifications logicielles pour chaque application
- Modifications de services Windows pour chaque serveur

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking01.png)

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking02.png)

### Affichage des modifications pour tout type de modification

1. Sur la page **Vue d’ensemble**, cliquez sur la vignette **Suivi des modifications**.
2. Sur le tableau de bord **Suivi des modifications**, passez en revue les informations résumées de l’un des panneaux de type de modification, puis cliquez sur l’une d’entre elles pour afficher des informations détaillées dans la page **Recherche de journaux**.
3. Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## Étapes suivantes

- Utilisez les [recherches de journaux dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées du suivi des modifications.

<!---HONumber=AcomDC_0518_2016-->