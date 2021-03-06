<properties
	pageTitle="Vue d’ensemble de l’analyse dans Microsoft Azure | Microsoft Azure"
	description="Vue d’ensemble de haut niveau de l’analyse et des diagnostics dans Microsoft Azure, y compris les alertes, les webhooks, l’échelle automatique et autres."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="robb"/>

# Vue d’ensemble de l’analyse dans Microsoft Azure

Cet article fournit une vue d’ensemble conceptuelle de l’analyse des ressources Azure ainsi que des informations sur des types de ressources spécifiques. Pour obtenir des informations générales sur l’analyse de votre application depuis une autre plate-forme qu’Azure, consultez [Guide de surveillance et de diagnostic](../best-practices-monitoring.md).

Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus. En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces informations peuvent vous aider à améliorer ses performances ou sa facilité de gestion, ou à automatiser certaines actions qui exigeraient normalement une intervention manuelle.

Le diagramme suivant présente une vue conceptuelle de l’analyse Azure, notamment le type de journaux que vous pouvez collecter et ce que vous pouvez faire avec ces données.

![Modèle logique pour l’analyse et le diagnostic pour les ressources non liées au calcul](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

Figure 1 : modèle conceptuel pour l’analyse et le diagnostic pour les ressources non liées au calcul

<br/>

![Modèle logique pour l’analyse et le diagnostic pour les ressources de calcul](./media/monitoring-overview/monitoring-azure-resources-compute.png)

Figure 2 : modèle conceptuel pour l’analyse et le diagnostic pour les ressources liées au calcul


## Sources d’analyse
### Journaux d’activité d’infrastructure
Vous pouvez rechercher le journal d’audit (parfois appelé journal opérationnel ou d’activité) pour obtenir des informations sur vos ressources telles qu’elles sont analysées par l’infrastructure Azure. Ce journal contient des informations telles que les dates et heures de création ou de destruction de ressources.

### Ordinateur virtuel hôte
**Calcul uniquement**

Bien que vous ne puissiez actuellement pas collecter de mesures sur l’ordinateur virtuel hôte, celui-ci est répertorié ici par souci d’exhaustivité.

Certaines ressources de calcul, telles que les services cloud, Virtual Machines et Service Fabric disposent d’un ordinateur virtuel hôte dédié avec lequel elles interagissent. L’ordinateur virtuel hôte est l’équivalent de la racine virtuelle dans le modèle d’hyperviseur Hyper-V.

Le routage 1:1 entre votre ressource et un ordinateur virtuel spécifique n’est pas appliqué dans tous les services Azure.


### Ressources - Métriques et journaux de diagnostics
Le type de métriques que vous pouvez générer varie selon le type de ressources. Par exemple, les machines virtuelles fournissent des statistiques sur l’E/S disque et le pourcentage UC. Mais ces statistiques n’existent pas pour une file d’attente Service Bus, qui fournit à la place des métriques telles que la taille de la file d’attente et le débit des messages.

Pour les ressources de calcul, vous pouvez obtenir les métriques sur le SE invité, ainsi que des modules de diagnostic tels que Azure Diagnostics. Azure Diagnostics aide à collecter et à router des données groupées de diagnostic vers d’autres emplacements, notamment dans le stockage Azure.

### Application - Journaux de diagnostic, journaux d’Application et métriques
**Calcul uniquement**

Les applications peuvent fonctionner parallèlement au SE invité dans le modèle de calcul. Elles émettent leur propre ensemble de journaux et de métriques.

Les types de métriques fournis sont les suivants :

- Compteurs de performances
- Journaux d’application
- Journaux des événements Windows
- .NET EventSource
- Journaux IIS
- ETW basé sur les manifestes
- Vidages sur incident
- Journaux d'erreurs client


## Utilisations de l’analyse de données

### Visualisation
En affichant les données analysées sous forme de graphiques et de diagrammes, vous êtes en mesure d’identifier des tendances beaucoup plus rapidement qu’en lisant simplement les données brutes vous-même.

Il existe quelques méthodes de visualisation, qui sont les suivantes :

- Utilisation du portail Azure
- Routage de données vers Azure Application Insights
- Routage de données vers Microsoft PowerBI
- Routage de données vers un outil de visualisation tiers par streaming en direct ou par lecture de l’outil à partir d’une archive située dans le stockage Azure

### Archivage
De manière générale, les données analysées sont écrites dans le stockage Azure, où elles sont conservées jusqu’à ce que vous les supprimiez.

Quelques façons d’utiliser ces données :

- Une fois les données écrites, vous pouvez les lire et les traiter à l’aide d’outils internes ou externes à Azure.
- Vous devez télécharger les données localement pour les archiver localement ou modifier votre stratégie de rétention dans le cloud pour les conserver sur une période prolongée.
- Vous laissez indéfiniment les données dans le stockage Azure. Notez que vous devez payer pour le stockage Azure en fonction de la quantité de données que vous conservez.

### Interroger
Vous pouvez utiliser l’API REST Insights, les commandes de l’Interface de langage commun (Common Language Interface, CLI), les applets de commande PowerShell ou le Kit de développement logiciel .NET pour accéder aux données présentes dans le système ou dans le stockage Azure

Voici quelques exemples :

-  Obtention de données pour une application d’analyse personnalisée que vous avez écrite
-  Création de requêtes personnalisées et envoi de ces données à une application tierce.

### Routage
Vous pouvez diffuser des données d’analyse à d’autres emplacements en temps réel.

Voici quelques exemples :

- Envoi vers Application Insights afin d’en utiliser les outils de visualisation.
- Envoi vers Event Hubs afin de router les outils tiers pour l’exécution d’une analyse en temps réel.

### Automatisation
Vous pouvez utiliser les données d’analyse pour déclencher des événements ou encore des processus complets. En voici quelques exemples :

- Utilisation des données pour la mise à l’échelle automatique des instances de calcul, vers le haut ou vers le bas selon le chargement de l’application.
- Envoi de messages électroniques lorsqu’une mesure dépasse un seuil prédéfini.
- Appel d’une URL Web (webhook) afin d’exécuter une action dans un système hors Azure
- Démarrage d’un runbook dans Azure Automation pour l’exécution de tout ensemble de tâches

## Méthodes d’utilisation
En général, vous pouvez manipuler le suivi, le routage et la récupération des données à l’aide de l’une des méthodes suivantes. Toutes les méthodes ne sont pas disponibles pour toutes les actions ou tous les types de données.

- [Portail Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)
- [Interface de ligne de commande interplateforme (CLI)](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## Offres d’analyse Azure
Azure propose des offres pour l’analyse de vos services, depuis les infrastructures de systèmes nus jusqu’à la télémétrie applicative. La meilleure stratégie d’analyse combine l’utilisation des trois pour obtenir une analyse complète et détaillée de l’intégrité de vos services.

- [Analyse Azure Insights (plate-forme)](http://aka.ms/azmondocs) – offre des services de visualisation, requête, routage, alertes, mise à l’échelle et automatisation pour les données de l’infrastructure Azure (journal d’activité) et pour chaque ressource Azure (journaux de diagnostic). Cet article fait partie de la documentation Azure Insights.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – fournit une détection et des diagnostics approfondis et bien intégrés par-dessus les données d’Azure Monitoring pour les problèmes survenant au niveau de la couche d’application de votre service.
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), composant d’[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – fournit une solution de gestion informatique globale aussi bien pour les infrastructures sur site que pour celles basées sur le cloud, qui comprend la recherche de journaux et des analyses à partir de vos ressources Azure.


## Étapes suivantes
En savoir plus sur

- [Azure Insights](http://aka.ms/azmondocs) en explorant les liens de la table des matières à gauche et les vidéos sur ce lien.
- [Azure Diagnostic](../azure-diagnostics.md) si vous tentez de diagnostiquer des problèmes dans votre application Cloud Service, Virtual Machine ou Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
- [Résolution des problèmes du stockage Azure](../storage/storage-e2e-troubleshooting.md) lorsque vous utilisez le stockage d’objets blob, de tables ou de files d’attente
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) et [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0907_2016-->