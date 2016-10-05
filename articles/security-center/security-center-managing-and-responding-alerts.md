<properties
   pageTitle="Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/19/2016"
   ms.author="yurid"/>

# Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à utiliser Azure Security Center afin de gérer et résoudre les alertes de sécurité.

> [AZURE.NOTE] Pour activer la détection avancée, effectuez une mise à niveau vers Azure Security Center Standard. Une version d’évaluation gratuite de 90 jours est disponible. Pour mettre à niveau, sous [Stratégie de sécurité](security-center-policies.md), sélectionnez Niveau tarifaire. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).


## Que sont les alertes de sécurité ?
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier. Azure Security Center regroupe également les alertes correspondant à des modèles de chaînes de destruction en [incidents](security-center-incident.md).

> [AZURE.NOTE] Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, consultez [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).


## Gestion des alertes de sécurité

Vous pouvez connaître vos alertes actuelles en consultant la vignette **Alertes de sécurité**. Accédez au Portail Azure et suivez les étapes ci-après pour obtenir plus d’informations sur chaque alerte :

1. La vignette **Alertes de sécurité** est affichée dans le tableau de bord Centre de sécurité.

    ![Vignette Alertes de sécurité dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2.  Cliquez sur la vignette pour ouvrir le panneau **Alertes de sécurité**, qui fournit des détails supplémentaires sur les alertes, comme indiqué ci-dessous.

    ![Panneau Alertes de sécurité dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Les détails de chaque alerte sont affichés au bas du panneau. Pour les organiser à votre convenance, cliquez sur la colonne que vous voulez trier. La définition de chaque colonne est indiquée ci-dessous :

- **Alerte** : brève explication de l’alerte.
- **Nombre** : liste de toutes les alertes d’un type spécifique qui ont été détectées un jour précis.
- **Détectée par** : service à l’origine du déclenchement de l’alerte.
- **Date** : date à laquelle l’événement s’est produit.
- **État** : état actuel de l’alerte. Il existe deux types d’état :
    - **Active** : l’alerte de sécurité a été détectée.
    - **Ignorée** : l’alerte de sécurité a été ignorée par l’utilisateur. Cet état est généralement attribué aux alertes qui ont été examinées, mais qui ont nécessité une correction ou qui ne correspondent pas à une attaque.

- **Gravité** : niveau de gravité (élevé, moyen ou bas).

### Filtrage des alertes

Vous pouvez filtrer les alertes en fonction de la date, de l’état et du niveau de gravité. Le filtrage des alertes peut être utile quand vous avez besoin de restreindre le nombre d’alertes de sécurité qui s’affichent. Supposons que vous souhaitiez vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.

1. Cliquez sur **Filtrer** dans le panneau **Alertes de sécurité**. Dans le panneau **Filtrer** qui s’ouvre, vous pouvez sélectionner la date, l’état et les niveaux de gravité que vous souhaitez visualiser.

	![Filtrage des alertes dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)

2. 	Pour certaines alertes, vous pouvez déterminer qu’il s’agit d’un faux positif pour votre environnement, ou que le comportement signalé est normal pour une ressource donnée. Si vous déterminez qu’une alerte de sécurité n’est pas applicable, vous pouvez l’ignorer, puis la faire disparaître à l’aide du filtre. Il existe deux façons d’ignorer une alerte de sécurité. Cliquez avec le bouton droit sur une alerte, puis sélectionnez **Ignorer**, ou pointez sur un élément, cliquez sur les trois points qui apparaissent à droite, puis sélectionnez **Ignorer**. Vous pouvez afficher les alertes de sécurité ignorées en cliquant sur **Filtrer**, puis en sélectionnant **Ignorées**.

	![Masquage des alertes dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Répondre à des alertes de sécurité

Sélectionnez une alerte de sécurité pour en savoir plus sur les événements qui l’ont déclenchée et, le cas échéant, les étapes à suivre pour y remédier. Les alertes de sécurité sont regroupées par type et date d’apparition. Le fait de cliquer sur une alerte de sécurité ouvre un volet contenant une liste des alertes groupées.

![Répondre aux alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Dans ce cas, les alertes qui ont été déclenchées concernent une activité suspecte utilisant le protocole RDP. Les première, deuxième, troisième, quatrième et cinquième colonnes affichent respectivement les ressources qui ont été attaquées, le nombre de fois où la ressource a été attaquée, l’heure de l’attaque, l’état de l’alerte et le niveau de gravité de l’attaque. Après avoir examiné ces informations, cliquez sur la ressource qui a été attaquée. Un nouveau panneau s’affiche.

![Suggestions sur la façon de traiter les alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

Vous trouverez plus d’informations sur l’événement dans le champ **Description** de ce panneau. Ces informations permettent d’en savoir plus sur ce qui a déclenché l’alerte de sécurité, sur la ressource cible, sur l’adresse IP source (le cas échéant) et sur la manière de remédier au problème. Dans certains cas, l’adresse IP source sera vide (non disponible), car certains journaux d’événements Windows de la sécurité n’incluent pas l’adresse IP.

La correction suggérée par le Centre de sécurité dépend de l’alerte de sécurité. Dans certains cas, vous pouvez être amené à utiliser d’autres fonctionnalités Azure pour appliquer la correction recommandée. Par exemple, pour cette attaque, la correction consiste à mettre sur liste noire l’adresse IP à l’origine de l’attaque à l’aide d’une règle de [liste de contrôle d’accès (ACL) réseau](../virtual-network/virtual-networks-acl.md) ou de [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

> [AZURE.NOTE] Pour plus d’informations sur les différents types d’alertes, consultez l’article [Security Alerts by Type in Azure Security Center](security-center-alerts-type.md) (Alertes de sécurité par type dans Azure Security Center).

## Voir aussi

Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md)
- [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
- [Guide des opérations et de planification du Centre de sécurité Azure](security-center-planning-and-operations-guide.md)
- [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!-----HONumber=AcomDC_0921_2016-->