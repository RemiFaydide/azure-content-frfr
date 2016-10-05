<properties 
	pageTitle="Vue d’ensemble d’Enterprise Integration | Microsoft Azure App Service | Microsoft Azure" 
	description="Utilisez les fonctionnalités d’Enterprise Integration pour activer les processus métier et les scénarios d’intégration à l’aide d'applications logiques" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="deonhe"/>

# Vue d'ensemble d'Enterprise Integration Pack

## Qu'est-ce que Enterprise Integration Pack ?
Enterprise Integration Pack est la solution basée sur le cloud de Microsoft permettant d'établir de façon transparente des communications Business-to-Business (B2B). Ce pack utilise des protocoles standard, notamment [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md) et [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) pour échanger des messages entre des partenaires commerciaux. Les messages peuvent être sécurisés à l’aide du chiffrement et de signatures numériques.

Le pack permet aux organisations qui utilisent différents protocoles et formats d'échanger des messages électroniques en convertissant les différents formats dans un format que les systèmes de deux entreprises peuvent interpréter et exploiter.

Si vous êtes familiarisé avec BizTalk Server ou Microsoft Azure BizTalk Services, vous saurez facilement utiliser les fonctionnalités Enterprise Integration car la plupart des concepts sont similaires. La principale différence réside dans le fait qu'Enterprise Integration utilise les comptes d’intégration pour simplifier le stockage et la gestion des artefacts utilisés dans les communications B2B.

D'un point de vue architectural, Enterprise Integration Pack repose sur des **comptes d’intégration** qui stockent tous les artefacts pouvant être utilisés pour concevoir, déployer et gérer vos applications B2B. Un compte d’intégration est essentiellement un conteneur dans le cloud, dans lequel vous stockez des artefacts tels que des schémas, des partenaires, des certificats, des mappages et des contrats. Ces artefacts peuvent ensuite être utilisés dans des applications logiques pour créer des flux de travail B2B. Avant de pouvoir utiliser les artefacts dans une application logique, vous devez lier votre compte d’intégration à votre application logique. Une fois ces deux éléments liés, votre application logique pourra accéder aux artefacts du compte d’intégration.

## Pourquoi utiliser l'intégration d’entreprise ?
- Grâce à l’intégration d’entreprise, vous pouvez stocker tous vos artefacts dans un même endroit : votre compte d’intégration.
- Vous pouvez tirer parti du moteur de vos applications logique et de tous ses connecteurs pour créer des flux de travail B2B et les intégrer dans des applications SaaS tierces, des applications locales et des applications personnalisées
- Vous pouvez également exploiter les fonctions Azure

## Comment prendre en main Enterprise Integration Pack ?
Vous pouvez créer et gérer des applications B2B à l’aide d'Enterprise Integration Pack via le concepteur d’applications logiques du **portail Azure**.

Vous pouvez également utiliser [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Rubriques PowerShell sur les applications logiques") pour vos applications logiques.

Voici une vue d’ensemble des étapes requises pour créer des applications dans le portail Azure : ![image de présentation](./media/app-service-logic-enterprise-integration-overview/overview-0.png)

## Quels sont les scénarios courants ?

Enterprise Integration prend en charge les normes suivantes :

- EDI - Electronic Data Interchange
- EAI - Enterprise Application Integration

## Voici ce dont vous avez besoin pour commencer
- Un abonnement Azure avec un compte d’intégration
- Visual Studio 2015 pour créer des mappages et des schémas
- [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)

## Essayer
[Essayez-le maintenant](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) pour déployer votre propre exemple d’application d’envoi et de réception AS2 entièrement fonctionnelle utilisant les fonctionnalités B2B de Logic Apps.

## Pour en savoir plus :
- [Accords](./app-service-logic-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")
- [Scénarios Business to Business (B2B)](./app-service-logic-enterprise-integration-b2b.md "Apprenez à créer des applications logiques avec fonctionnalités B2B")
- [Certificats](./app-service-logic-enterprise-integration-certificates.md "Découvrez les certificats d’intégration d’entreprise")
- [Codage/décodage de fichier plat](./app-service-logic-enterprise-integration-flatfile.md "Découvrez comment encoder et décoder le contenu d'un fichier plat")
- [Comptes d’intégration](./app-service-logic-enterprise-integration-accounts.md "En savoir plus sur les comptes d’intégration")
- [Mappages](./app-service-logic-enterprise-integration-maps.md "Découvrez les mappages d’intégration d’entreprise")
- [Partenaires](./app-service-logic-enterprise-integration-partners.md "Découvrez les partenaires d’intégration d’entreprise")
- [Schémas](./app-service-logic-enterprise-integration-schemas.md "Découvrez les schémas d’intégration d’entreprise")
- [Validation de message XML](./app-service-logic-enterprise-integration-xml.md "Découvrez comment valider des messages XML avec vos applications logiques")
- [Transformation XML](./app-service-logic-enterprise-integration-transform.md "Découvrez les mappages d’intégration d’entreprise")
- [Connecteurs d'intégration d’entreprise](../connectors/apis-list.md "En savoir plus sur les connecteurs Enterprise Integration Pack")

<!---HONumber=AcomDC_0921_2016-->