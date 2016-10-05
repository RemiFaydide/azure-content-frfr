<properties
   pageTitle="Présentation de l’intégration des journaux Microsoft Azure | Microsoft Azure"
   description="Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# Présentation de l’intégration des journaux Microsoft Azure (Version préliminaire)

Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement.

## Vue d'ensemble

La plateforme en tant que Service (PaaS) et l’infrastructure en tant que Service (IaaS) hébergées dans Azure génèrent une grande quantité de données dans des journaux de sécurité. Ces journaux contiennent des informations critiques qui peuvent fournir des informations essentielles sur les violations de stratégie, les menaces internes et externes, les problèmes de conformité et anomalies du réseau, de l’hôte et de l’activité des utilisateurs.

L’intégration des journaux Azure permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux. L’intégration des journaux Azure collecte des diagnostics Azure à partir de vos machines virtuelles Windows *(WAD)*, ainsi que des diagnostics à partir de solutions partenaires telles qu’un pare-feu d’applications web (WAF). Cette intégration offre un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.

![Intégration des journaux Azure][1]

## Quels journaux puis-je intégrer ?

Azure génère une journalisation complète pour chaque service Azure. Ces journaux sont classés en deux types principaux :

- **Journaux de contrôle/gestion**, qui vous offrent une visibilité sur les opérations CREATE, UPDATE et DELETE d’Azure Resource Manager. Les journaux d’audit Azure sont un exemple de ce type de journal.
- **Journaux des plans de données**, qui vous offrent une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource Azure. Il s’agit par exemple des journaux du système d’événements, de la sécurité et des applications Windows dans une machine virtuelle.

L’intégration des journaux Azure prend actuellement en charge l’intégration des journaux d’Audit d’Azure, des journaux de machine virtuelle et des alertes de l’Azure Security Center.

Si vous avez des questions sur l’intégration des journaux Azure, envoyez un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## Étapes suivantes

Ce document vous a présenté une vue d’ensemble de l’intégration des journaux Azure. Pour plus d’informations sur l’intégration des journaux Azure et les types de journaux pris en charge, consultez les rubriques suivantes :

- [Intégration de journaux Microsoft Azure pour les journaux Azure (Version préliminaire)](https://www.microsoft.com/download/details.aspx?id=53324) : centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation sur l’intégration de journaux Azure.
- [Prise en main de l’intégration des journaux Azure](security-azure-log-integration-get-started.md) : ce didacticiel vous guide dans la procédure d’installation de l’intégration des journaux Azure et d’intégration des journaux d’audit Azure et des alertes du Security Center.
- [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar.
- [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
- [Intégration des alertes du Security Center avec les journaux Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) : ce document montre comment synchroniser les alertes du Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par Azure Diagnostics et les journaux d’audit Azure dans leur solution SIEM ou Log Analytics.
- [Nouvelles fonctionnalités des diagnostics Azure et des journaux d’Audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : ce billet de blog présente les journaux d’Audit Azure et autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

<!---HONumber=AcomDC_0921_2016-->