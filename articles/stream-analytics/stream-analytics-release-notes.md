<properties 
	pageTitle="Notes de publication de Stream Analytics | Microsoft Azure" 
	description="Notes de publication de Stream Analytics" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

#Notes de publication de Stream Analytics

## Notes pour la version 04/15/2016 de Stream Analytics ##

Cette version contient la mise à jour suivante.

Intitulé | Description
---|---
Disponibilité générale pour les sorties Power BI | Les [sorties Power BI](stream-analytics-power-bi-dashboard.md) sont désormais mises à la disposition générale. L'expiration de l'autorisation de 90 jours pour Power BI a été supprimée. Pour plus d'informations sur les scénarios dans lesquels l'autorisation doit être renouvelée, consultez la section [Renouveler une autorisation](stream-analytics-power-bi-dashboard.md#Renew-authorization) de Création d’un tableau de bord Power BI.

## Notes relatives à la version du 03/03/2016 de Stream Analytics ##

Cette version contient la mise à jour suivante.

Intitulé | Description
---|---
Nouveaux éléments du langage de requête Stream Analytics | SAQL a désormais [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "Page MSDN sur GetType"), [TRY\_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "Page MSDN sur TRY_CAST") et [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "Page MSDN sur REGEXMATCH").

## Notes relatives à la version du 10/12/2015 de Stream Analytics ##

Cette version contient la mise à jour suivante.

Intitulé | Description
---|---
Mise à jour de la version de l’API REST | L’API REST a été mise à jour vers la version suivante : 01-10-2015. Vous trouverez plus d’informations sur MSDN à l’adresse [références d’API REST de gestion Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx) et [intégration de Machine Learning dans Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Intégration d’Azure Machine Learning | Cette version s’accompagne de la prise en charge des fonctions Azure Machine Learning définies par l’utilisateur. Consultez le [didacticiel](stream-analytics-machine-learning-integration-tutorial.md) pour plus d'informations, ainsi que les [annonces générales sur le blog](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## Notes relatives à la version du 12/11/2015 de Stream Analytics ##

Cette version contient la mise à jour suivante.

Intitulé | Description
---|---
Nouveau comportement de SELECT | Dans Stream Analytics, SELECT a été étendu pour autoriser * comme accesseur de propriété d’un enregistrement imbriqué. Pour plus d’informations, voir [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Types de données complexes").

## Notes relatives à la version du 22/10/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.

Intitulé | Description
---|---
Fonctionnalités de langage de requête supplémentaires | Stream Analytics a étendu le langage de requête en incluant les fonctionnalités suivantes : [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) et [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Limitations d’agrégation supprimées | Cette version supprime la limitation à 15 agrégats dans une requête. Il n’existe désormais aucune limite du nombre d’agrégats par requête.
Fonctionnalité GROUP BY System.Timestamp ajoutée | La fonction [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) autorise désormais window\_type ou [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
Fonctionnalité OFFSET ajoutée pour les fenêtres bascule et récurrentes | Par défaut, la [fenêtre bascule](https://msdn.microsoft.com/library/azure/dn835055.aspx) et la [ fenêtre récurrente](https://msdn.microsoft.com/library/azure/dn835041.aspx) sont alignées par rapport à l’heure zéro (1/1/0001 12:00:00 AM UTC). Le nouveau paramètre (facultatif) « offsetsize » permet de spécifier un décalage (ou alignement) personnalisé.


## Notes relatives à la version du 29/09/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.

Intitulé | Description
---|---
Version préliminaire publique d’IoT Azure Suite | Stream Analytics est inclus dans la version préliminaire publique d’IoT Azure Suite.
Intégration du portail Azure | En plus de continuer à être présent dans le portail de gestion Azure, Stream Analytics est désormais intégré au [portail Azure](https://azure.microsoft.com/overview/preview-portal/). Notez que la fonctionnalité Stream Analytics du portail en version préliminaire correspond actuellement à un sous-ensemble des fonctionnalités fournies dans le Portail de gestion Azure, sans prise en charge du test de requête dans un navigateur, de la configuration Power BI et de la possibilité d’accès aux ressources d’entrée et de sortie ou de création de ces ressources dans vos abonnements.
Prise en charge de la sortie DocumentDB | Les tâches Stream Analytics peuvent désormais générer une sortie vers [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Prise en charge de l’entrée IoT Hub | Les tâches Stream Analytics peuvent à présent recevoir des données à partir d’IoT Hub.
TIMESTAMP BY pour les événements hétérogènes | Quand un flux de données unique contient plusieurs types d’événements ayant des horodatages dans différents champs, vous pouvez désormais utiliser [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) avec des expressions pour spécifier différents champs d’horodatage pour chaque cas.

## Notes relatives à la version du 10/09/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.

Intitulé|Description
---|---
Prise en charge des groupes PowerBI|Pour permettre le partage de données avec d’autres utilisateurs de Power BI, les tâches Stream Analytics peuvent désormais écrire dans des [groupes PowerBI](stream-analytics-define-outputs.md#power-bi) appartenant à votre compte Power BI.

## Notes relatives à la version du 20/08/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.

Intitulé|Description
---|---
Ajout de la fonction LAST |La fonction [LAST](http://msdn.microsoft.com/library/mt421186.aspx) est désormais disponible dans les tâches Stream Analytics, ce qui vous permet de récupérer l’événement le plus récent d’un flux d’événements dans une période donnée.
Nouvelles fonctions de tableau|Les fonctions de tableau [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) et [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) sont désormais disponibles.
Nouvelles fonctions d’enregistrement|Les fonctions d’enregistrement [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) et [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sont désormais disponibles.

## Notes relatives à la version du 30/07/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.

Intitulé|Description
---|---
ID d’organisation Power BI dissocié de l’ID Azure|Cette fonctionnalité permet une [sortie Power BI](stream-analytics-power-bi-dashboard.md) pour les tâches ASA dans tout type de compte Azure (Live ID ou ID d’organisation). En outre, vous pouvez avoir un ID d’organisation pour votre compte Azure et en utiliser un autre pour autoriser la sortie de Power BI.
Prise en charge de la sortie de files d’attente de Service Bus|Les sorties de [files d’attente Service Bus](stream-analytics-connect-data-event-outputs.md#service-bus-queues) sont désormais disponibles dans les tâches Stream Analytics.
Prise en charge de la sortie de rubriques de Service Bus|Les sorties de [rubriques Service Bus](stream-analytics-connect-data-event-outputs.md#service-bus-topics) sont maintenant disponibles dans les travaux Stream Analytics.

## Notes relatives à la version du 09/07/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.


Intitulé|Description
---|---
Partitionnement personnalisé de la sortie des objets blob|Les sorties d’objets blob de stockage autorisent désormais une option pour spécifier la fréquence à laquelle les objets blob de sortie sont écrits ainsi que la structure et le format de la structure des dossiers du chemin d’accès des données de sortie. 

## Notes relatives à la version du 03/05/2015 de Stream Analytics ##

Cette version contient les mises à jour suivantes.


Intitulé|Description
---|---
Augmentation de la valeur maximale de la plage de tolérance pour les événements en désordre|La taille maximale de la plage de tolérance pour les événements en désordre est désormais définie sur 59:59 (mm:ss).
Format de sortie JSON : Séparé par une ligne ou tableau|Il existe désormais une option lors de la sortie vers le stockage d’objets blob ou le hub d’événements sous la forme d’un tableau d’objets JSON ou en séparant les objets JSON par une nouvelle ligne. 

## Notes relatives à la version du 16/04/2015 de Stream Analytics ##


Intitulé|Description
---|---
Retard dans la configuration du compte Azure Storage|Si vous créez un travail Stream Analytics dans une région pour la première fois, vous devez créer un compte de stockage ou indiquer un compte existant pour la surveillance des travaux Stream Analytics de cette région. En raison d’une latence dans la configuration de la surveillance, la création d’autres travaux Stream Analytics dans la même région dans les 30 minutes entraîne une invitation à indiquer un second compte de stockage au lieu d’afficher celui que vous venez de configurer dans la liste déroulante Surveillance de compte de stockage. Pour éviter la création d'un compte de stockage inutile, attendez 30 minutes après votre première création de travail dans une région, puis procédez à la configuration des travaux supplémentaires dans cette région.
Mise à niveau d’un travail|Pour le moment, Stream Analytics ne prend pas en charge les modifications de définition ou de configuration en direct d’un travail en cours d’exécution. Pour modifier l’entrée, la sortie, la requête, la mise à l’échelle ou la configuration d’un travail en cours d’exécution, vous devez tout d’abord l’arrêter.
Types de données déduits de la source d’entrée|Si une instruction CRÉER UNE TABLE n’est pas utilisée, le type d’entrée est dérivé du format d’entrée, par exemple tous les champs du CSV sont des chaînes. Les champs doivent être explicitement convertis au type correct à l’aide de la fonction CAST pour éviter les erreurs d’incompatibilité des types.
Les champs manquants sont renvoyés comme valeurs nulles.|Faire référence à un champ qui n’est pas présent dans la source d’entrée génère des valeurs nulles dans l’événement de sortie.
Les instructions AVEC doivent précéder les instructions SÉLECTIONNER|Dans votre requête, les instructions SÉLECTIONNER doivent suivre les sous-requêtes définies dans les instructions AVEC.
Problème de mémoire insuffisante|Les travaux Streaming Analytics ayant une tolérance importante pour les événements arrivant en désordre et/ou les requêtes complexes tenant à jour un grand nombre d’états, cela peut entraîner un manque de mémoire et provoquer leur redémarrage. Les opérations de démarrage et d’arrêt sont visibles dans les journaux des opérations du travail. Pour éviter ce comportement, procédez à une mise à l’échelle sur plusieurs partitions. Dans une version ultérieure, cette limitation sera traitée par la dégradation des performances des travaux affectés plutôt que par leur redémarrage.
Les entrées importantes d’objets blob sans horodatage de la charge utile peuvent provoquer un problème d’insuffisance de mémoire|L’absorption de fichiers volumineux depuis le stockage d’objets blob peut entraîner le blocage de Stream Analytics si un champ d’horodatage n’est pas spécifié via HORODATAGE PAR. Pour éviter ce problème, faites en sorte que la taille de chaque objet blob ne dépasse pas 10 Mo.
Limitation du volume d’événements de base de données SQL|Lorsque vous utilisez une base de données SQL comme cible de sortie, des volumes de données de sortie particulièrement importants peuvent provoquer une expiration du délai du travail Stream Analytics. Pour résoudre ce problème, réduisez le volume de sortie à l’aide d’opérateurs d’agrégation ou de filtrage, ou choisissez le service de stockage d’objets blob Azure ou Event Hubs comme cible de sortie.
Les jeux de données Power BI ne peuvent contenir qu’une seule table|Power BI ne peut pas prendre en charge plus d’une table dans un jeu de données.

## Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0921_2016-->