<properties
	pageTitle="Mise à l’échelle des travaux Stream Analytics pour augmenter le débit | Microsoft Azure"
	description="Découvrez comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée, en réglant la définition de requête et en configurant les unités de diffusion en continu d’un travail."
	keywords="diffusion en continu de données, traitement de données de diffusion en continu, régler l’analyse"
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

# Mettre à l’échelle des tâches Azure Stream Analytics pour augmenter le débit de traitement des données de flux

Découvrez comment régler les tâches d’analyse et calculer des *unités de diffusion en continu* pour Stream Analytics et comment mettre à l’échelle des tâches Stream Analytics en configurant des partitions d’entrée, en réglant la définition des requêtes d’analyse et en définissant les unités de diffusion en continu d’une tâche.

## Quelles sont les parties d’un travail Stream Analytics ?
La définition d’une tâche Stream Analytics se compose d’entrées, d’une requête et d’une sortie. Les entrées correspondent à l’emplacement à partir duquel le travail lit le flux de données, la requête permet de transformer le flux d’entrée de données, et la sortie correspond à l’emplacement où le travail envoie ses résultats.

Un travail nécessite au moins une source d’entrée pour la diffusion de données en continu. La source d'entrée de flux de données peut être stockée sur un hub d'événements Service Bus Azure ou un objet blob de stockage Azure. Pour plus d’informations, consultez [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md) et [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md).

## Configuration des unités de diffusion en continu
Les unités de diffusion en continu représentent les ressources et la puissance pour exécuter une tâche Azure Stream Analytics. Ces unités permettent de décrire la capacité relative de traitement des événements basée sur une mesure mixte du processeur, de la mémoire et des taux de lecture et d’écriture. Chaque unité de diffusion en continu correspond à un débit d'environ 1 Mo/s.

Le choix du nombre d’unités de diffusion en continu requises pour un travail particulier dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Vous pouvez sélectionner des unités de diffusion en continu (jusqu’aux limites de votre quota) pour une tâche à l’aide du portail Azure Classic. Par défaut, chaque abonnement Azure peut avoir jusqu’à 50 unités de diffusion en continu pour tous les travaux Stream Analytics d’une région spécifique. Pour augmenter les unités de diffusion en continu de vos abonnements, contactez le [support technique de Microsoft](http://support.microsoft.com).

Le nombre d'unités de diffusion en continu qu'un travail peut utiliser dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Notez également qu’une valeur valide pour les unités de diffusion en continu doit être utilisée. Les valeurs valides commencent à 1, 3, 6, puis vers le haut par incréments de 6, comme indiqué ci-dessous.

![Mise à l’échelle des unités de diffusion en continu Azure Stream Analytics][img.stream.analytics.streaming.units.scale]

Cet article vous montre comment calculer et régler la requête pour augmenter le débit des travaux Stream Analytics.

## Tâche massivement parallèle
La tâche massivement parallèle est le scénario le plus évolutif d’Azure Stream Analytics. Elle permet de connecter une partition de l’entrée à une instance de la requête, puis de connecter celle-ci à une partition de la sortie. L’obtention de ce parallélisme nécessite plusieurs choses :

1.  Si votre logique de requête dépend de la clé qui est actuellement traitée par la même instance de requête, vous devez vous assurer que les événements atteignent la même partition de votre entrée. Dans le cas de hubs d’événements, les données d’événement doivent être définies sur **PartitionKey**. Vous pouvez aussi utiliser des expéditeurs partitionnés. Pour les objets blob, cela signifie que les événements sont envoyés vers le même dossier de partition. Si votre logique de requête n’a pas besoin de la clé qui est traitée par la même instance de la requête, vous pouvez ignorer cette condition. Un exemple serait une requête simple du type select/project/filter.
2.	Une fois les données disposées dans l’entrée, vous devez vérifier que votre requête est partitionnée. Vous devez utiliser **Partition By** dans toutes les étapes. Les étapes multiples sont autorisées, mais elles doivent être partitionnées à l’aide de la même clé. Actuellement, pour qu’un travail soit entièrement parallèle, la clé de partitionnement doit être définie sur **PartitionId**.
3.	Pour le moment, seuls les hubs d’événements et les objets blob prennent en charge les sorties partitionnées. Pour la sortie des hubs d’événements, vous devez définir le champ **PartitionKey** sur **PartitionId**. Pour les objet blob, aucune action n’est nécessaire.
4.	En outre, le nombre de partitions d’entrée doit être égal à celui des partitions de sortie. Actuellement, la sortie des objets blob ne prend pas en charge les partitions. Toutefois, cela ne pose pas de problème, car elle hérite du schéma de partitionnement de la requête en amont. Voici des exemples de valeurs de partition qui permettent la création d’une tâche entièrement parallèle :
	1.	8 partitions d’entrée de hubs d’événements et 8 partitions de sortie de hubs d’événements
	2.	8 partitions d’entrée de hubs d’événements et une sortie d’objet blob
	3.	8 partitions d’entrée d’objets blob et une sortie d’objet blob
	4.	8 partitions d’entrée d’objets blob et 8 partitions de sortie de hubs d’événements

Voici quelques exemples de parallélisme massif.

### Requête simple
Entrée – Hubs d’événements avec 8 partitions de sortie – Hub d’événements avec 8 partitions

**Requête :**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Il s’agit d’un filtre simple, il n’est donc pas nécessaire de partitionner l’entrée que vous envoyez aux hubs d’événements. Dans la requête, **Partition By** est défini sur **PartitionId**. Nous répondons donc à la deuxième condition citée plus haut. Pour la sortie, nous devons configurer la sortie des hubs d’événements de la tâche afin que le champ **PartitionKey** soit défini sur **PartitionId**. Enfin, nous devons vérifier que partitions d’entrée == partitions de sortie. Cette topologie est massivement parallèle.

### Requête avec clé de regroupement
Entrée – Hubs d’événements avec 8 partitions de sortie – Objet blob

**Requête :**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête a une clé de regroupement. Il est donc nécessaire que la même clé soit traitée par la même instance de requête. Nous devons donc envoyer nos événements aux hubs d’événements de manière partitionnée. Quelle clé nous intéresse ? **PartitionId** est un concept de logique de tâche. La clé qui nous intéresse est **TollBoothId**. Cela signifie que nous devons définir le champ **PartitionKey** des données d’événement que nous envoyons aux hubs d’événements sur le **TollBoothId** de l’événement. Dans la requête, **Partition By** est défini sur **PartitionId**, donc tout va bien. Pour la sortie, puisqu’il s’agit d’un objet blob, il n’est pas nécessaire de configurer **PartitionKey**. Pour la quatrième condition, il s’agit également d’un objet blob, nous n’avons donc rien à faire. Cette topologie est massivement parallèle.

### Requête à plusieurs étapes avec clé de regroupement ###
Entrée – Hub d’événements avec 8 partitions de sortie – Hub d’événements avec 8 partitions

**Requête :**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête a une clé de regroupement. Il est donc nécessaire que la même clé soit traitée par la même instance de requête. Nous pouvons utiliser la même stratégie que dans la requête précédente. La requête comporte plusieurs étapes. Chaque étape a-t-elle un mot clé **Partition By** égal à **PartitionId** ? Oui, c’est bon. Pour la sortie, nous devons définir **PartitionKey** sur **PartitionId** comme nous l’avons vu plus haut. Nous pouvons également voir qu’elle a le même nombre de partitions que l’entrée. Cette topologie est massivement parallèle.


## Exemples de scénarios qui n’impliquent pas un parallélisme massif

### Nombre de partitions d’entrée et nombre de partitions de sortie qui ne correspondent pas ###
Entrée – Hubs d’événements avec 8 partitions de sortie – Hub d’événements avec 32 partitions

Dans ce cas, peu importe la requête, car le nombre de partitions d’entrée != nombre de partitions de sortie.

### Sortie ne correspondant pas à un hub d’événements ou à un objet blob
Entrée – Hubs d’événements avec 8 partitions de sortie – PowerBI

Actuellement, la sortie PowerBI ne prend pas en charge le partitionnement.

### Requête à plusieurs étapes avec des valeurs Partition By différentes
Entrée – Hub d’événements avec 8 partitions de sortie – Hub d’événements avec 8 partitions

**Requête :**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Comme vous pouvez le voir, la deuxième étape utilise **TollBoothId** comme clé de partitionnement. Ce n’est pas la même chose que pour la première étape. Nous devrons donc apporter quelques modifications.

Voici quelques exemples et contre-exemples de tâches Stream Analytics qui permettent un parallélisme massif et potentiellement, une mise à l’échelle maximale. Pour les tâches qui ne correspondent pas à ces profils, de futures mises à jour expliqueront comment obtenir une mise à l’échelle maximale pour d’autres scénarios Stream Analytics canoniques.

Pour l’instant, utilisez les instructions générales ci-dessous :

## Calcul du nombre maximum d'unités de diffusion en continu pour un travail
Le nombre total d'unités de diffusion en continu qui peut être utilisé par un travail Stream Analytics varie selon le nombre d'étapes de la requête définie pour le travail et le nombre de partitions pour chaque étape.

### Étapes dans une requête
Une requête peut avoir une ou plusieurs étapes. Chaque étape est une sous-requête définie à l’aide du mot clé **WITH**. La seule requête qui se trouve en dehors du mot clé **WITH** est également comptabilisée comme une étape, par exemple, l’instruction **SELECT** de la requête suivante :

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId

La requête précédente a deux étapes.

> [AZURE.NOTE] Cet exemple de requête est détaillé plus loin dans cet article.

### Partitionnement d'une étape

Les conditions suivantes doivent être respectées pour procéder au partitionnement d'une étape :

- La source d'entrée doit être partitionnée. Pour plus d’informations, consultez le [Guide de programmation de concentrateurs d’événements](../event-hubs/event-hubs-programming-guide.md).
- L’instruction **SELECT** de la requête doit lire à partir d’une source d’entrée partitionnée.
- La requête de l'étape doit contenir le mot clé **Partition By**

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts et les événements de sorties sont générés pour chacun des groupes. Si vous devez procéder à un agrégat combiné, vous devez créer une deuxième étape non partitionnée à agréger.

### Calcul des unités de diffusion en continu maximum pour un travail

Toutes les étapes non partitionnées ensemble peuvent être mises à l'échelle jusqu'à atteindre six unités de diffusion en continu par travail Stream Analytics. Pour ajouter d'autres unités de diffusion en continu, vous devez partitionner une étape. Chaque partition peut avoir six unités de diffusion en continu.

<table border="1">
<tr><th>Requête d'un travail</th><th>Nombre maximum d'unités de diffusion en continu pour le travail</th></td>

<tr><td>
<ul>
<li>La requête contient une étape.</li>
<li>L'étape n'est pas partitionnée.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par&#160;3.</li>
<li>La requête contient une étape.</li>
<li>L'étape est partitionnée.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La requête contient 2&#160;étapes.</li>
<li>Aucune des étapes n'est partitionnée.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par&#160;3.</li>
<li>La requête contient 2&#160;étapes. L'étape d'entrée est partitionnée et la deuxième étape ne l'est pas.</li>
<li>L'instruction SELECT lit à partir de l'entrée partitionnée.</li>
</ul>
</td>
<td>24&#160;(18&#160;pour les étapes partitionnées +&#160;6&#160;pour les étapes non partitionnées)</td></tr>
</table>

### Exemples de mise à l’échelle
La requête suivante calcule le nombre de voitures traversant une gare de péage avec trois péages dans un intervalle de temps de trois minutes. Cette requête peut être mise à l'échelle jusqu'à six unités de diffusion en continu.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pour utiliser plusieurs unités de diffusion en continu pour la requête, le flux de données d'entrée et la requête doivent être partitionnés. Étant donné que la partition de flux de données est définie sur 3, la requête modifiée suivante peut être mise à l'échelle jusqu'à compter 18 unités de diffusion en continu :

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts. Les événements de sortie sont également générés pour chacun des groupes. Le partitionnement peut provoquer des résultats inattendus lorsque le champ **Group-by** n'est pas la clé de partition dans l'entrée du flux de données. Par exemple, dans l’exemple de requête précédent, le champ **TollBoothId** n’est pas la clé de partition d’Input1. Les données du péage « TollBooth #1 » peuvent être réparties dans plusieurs partitions.

Chacune des partitions Input1 sera traitée séparément par Stream Analytics et plusieurs enregistrements du nombre de voitures qui traversent (« car-pass-through ») seront créés pour le même péage au cours du même intervalle de temps. Au cas où il serait impossible de modifier la clé de partition d'entrée, ce problème peut être résolu en ajoutant une étape non partitionnée supplémentaire, par exemple :

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être mise à l'échelle jusqu'à comporter 24 unités de diffusion en continu.

>[AZURE.NOTE] Si vous joignez deux flux de données, assurez-vous qu’ils sont partitionnés par la clé de partition de la colonne de jointure, et que chaque flux a le même nombre de partitions.


## Configuration d'une partition de travail Stream Analytics

**Réglage de l'unité de diffusion en continu d'un travail**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Stream Analytics** sur la gauche.
3. Cliquez sur le travail Stream Analytics que vous souhaitez mettre à l'échelle.
4. Cliquez sur **METTRE À L'ÉCHELLE** en haut de la page.

![Mise à l’échelle des unités de diffusion en continu Azure Stream Analytics][img.stream.analytics.streaming.units.scale]

Dans le portail Azure, les paramètres de mise à l'échelle sont accessibles sous Paramètres :

![Configuration d’une tâche Stream Analytics sur le portail Azure][img.stream.analytics.preview.portal.settings.scale]

## Surveillance des performances du travail

À l'aide du portail de gestion, vous pouvez suivre le débit d'un travail dans Événements par seconde :

![Surveillance des travaux Azure Stream Analytics][img.stream.analytics.monitor.job]

Calculez le débit prévu pour la charge de travail en événements par seconde. Au cas où le débit est plus faible que prévu, réglez la partition d'entrée ainsi que la requête, puis ajoutez d'autres unités de diffusion en continu à votre travail.

## Débit Stream Analytics à l’échelle - Scénario Raspberry Pi


Pour mieux comprendre comment les tâches Stream Analytics sont mises à l’échelle dans un scénario classique au niveau du débit de traitement sur plusieurs unités de diffusion en continu, voici une expérience qui envoie des données de capteur (clients) dans le hub d’événements, traite ces données, puis envoie une alerte ou des statistiques sous forme de sortie à un autre hub d’événements.

Le client envoie des données de capteur synthétisées aux hubs d’événements au format JSON vers Stream Analytics. La sortie des données est également au format JSON. Voici à quoi ressemblerait l'exemple de données :

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Requête : « Envoyer une alerte lorsque la lumière est éteinte »

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Mesure de débit : dans ce contexte, le débit correspond à la quantité de données d’entrée traitées par Stream Analytics au cours d’une durée fixe (10 minutes). Pour obtenir un meilleur débit de traitement pour les données d'entrée, le flux de données d'entrée et la requête doivent être partitionnés. En outre, **COUNT()** est inclus dans la requête pour mesurer combien d’événements d’entrée ont été traités. Pour vous assurer que la tâche n’attend pas simplement les événements d’entrée, chaque partition du hub d’événements d’entrée a été préchargée avec suffisamment de données d’entrée (environ 300 Mo).

Voici les résultats avec l'augmentation du nombre d'unités de diffusion en continu et le nombre de partitions correspondant dans les hubs d'événements.

<table border="1">
<tr><th>Partitions d'entrée</th><th>Partitions de sortie</th><th>Unités de diffusion en continu</th><th>Débit soutenu
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 Mo/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06&#160;Mo/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32&#160;Mo/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67&#160;Mo/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27&#160;Mo/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69&#160;Mo/s</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)


## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0921_2016-->