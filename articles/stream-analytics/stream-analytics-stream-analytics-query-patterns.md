<properties
	pageTitle="Exemples de requête pour les modes d’utilisation courants dans Stream Analytics | Microsoft Azure"
	description="Modèles courants de requêtes Azure Stream Analytics "
	keywords="exemples de requête"
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
	ms.workload="big-data"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>


# Exemples de requête pour les modes d’utilisation courants dans Stream Analytics

## Introduction

Les requêtes Azure Stream Analytics sont exprimées dans un langage de requête de type SQL présenté dans le guide [Référence du langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx). Cet article décrit les solutions à plusieurs modèles de requête habituels, inspirés de scénarios réels. Il est en cours et sera mis à jour avec de nouveaux modèles de manière continue.

## Exemple de requête : conversions de types de données
**Description** : définir les types des propriétés sur le flux d'entrée. Par exemple, le poids de la voiture arrive sur le flux d'entrée sous forme de chaînes et doit être converti en INT pour effectuer la synthèse SUM.

**Entrée** :

| Make | Temps | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Sortie** :

| Assurez-vous | Poids |
| --- | --- |
| Honda | 3000 |

**Solution** :

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**Explication** : utilisation d’une instruction CAST sur le champ Weight pour spécifier son type (voir la liste des types de données pris en charge [ici](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## Exemple de requête : utilisation de Like/Not like pour déterminer la correspondance de modèle
**Description** : vérification qu'une valeur de champ sur l'événement correspond à un certain modèle, par exemple renvoyer les immatriculations commençant par A et se terminant par 9

**Entrée** :

| Make | LicensePlate | Temps |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Sortie** :

| Make | LicensePlate | Temps |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Solution** :

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**Explication** : utilisation de l'instruction LIKE pour vérifier que la valeur du champ LicensePlate commence par A, suivi d’une chaîne de zéro caractère ou plus et se termine par 9.

## Exemple de requête : spécification de la logique pour différentes casses/valeurs (instructions CASE)
**Description** : fourniture de calculs différents pour un champ en fonction de certains critères. Par exemple, fourniture d’une description de chaîne pour le nombre de voitures de la même marque, avec une casse spéciale pour 1.

**Entrée** :

| Make | Temps |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Sortie** :

| CarsPassed | Temps |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyota | 2015-01-01T00:00:10.0000000Z |

**Solution** :

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**Explication** : la clause CASE permet de fournir un calcul différent en fonction de certains critères (dans ce cas, le nombre de voitures dans la fenêtre d'agrégation).

## Exemple de requête : envoi de données vers plusieurs sorties
**Description** : envoi de données vers plusieurs cibles de sortie depuis un projet unique. Par exemple, une analyse des données pour une alerte de seuil et l’archivage de tous les événements pour le stockage d'objets blob

**Entrée** :

| Make | Temps |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1** :

| Make | Temps |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2** :

| Make | Temps | Nombre |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solution** :

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**Explication** : la clause INTO indique à Stream Analytics la sortie sur laquelle écrire les données à partir de cette instruction. La première requête est un transfert des données que nous avons reçues vers une sortie nommée ArchiveOutput. La deuxième requête effectue une agrégation et un filtrage simples et envoie les résultats vers un système d'alerte en aval. *Remarque* : vous pouvez également réutiliser des résultats d’expressions de table communes (par exemple, avec des instructions WITH) dans plusieurs instructions de sortie : cela présente l’avantage supplémentaire d’ouvrir moins de lecteurs à la source d’entrée par exemple.

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## Exemple de requête : comptage des valeurs uniques
**Description** : compter les valeurs de champ uniques qui apparaissent dans le flux de données dans une fenêtre de temps. Par exemple, combien de voitures de marques différentes sont passées au péage pendant une fenêtre de 2 secondes ?

**Entrée** :

| Make | Temps |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output:**

| Nombre | Temps |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solution :**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**Explication :** une agrégation initiale est effectuée pour obtenir les marques uniques avec leur nombre dans la fenêtre. Ensuite, nous effectuons une agrégation du nombre de marques obtenues. Étant donné que toutes les valeurs uniques dans une fenêtre obtiennent le même horodatage, la deuxième fenêtre d'agrégation doit être minime afin de ne pas agréger 2 fenêtres de la première étape.

## Exemple de requête : déterminer si une valeur a changé#
**Description** : examiner une valeur précédente pour déterminer si elle est différente de la valeur actuelle. Par exemple, la voiture actuellement sur la voie de péage est-elle de la même marque que la voiture précédente ?

**Entrée** :

| Make | Temps |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Sortie** :

| Make | Temps |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solution** :

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explication** : utilisez LAG pour lire le flux d’entrée de l’événement précédent et obtenir la valeur de la marque. Elle est ensuite comparée à la marque de l’événement en cours, puis l'événement émet une sortie si elles sont différentes.

## Exemple de requête : recherche du premier événement dans une fenêtre
**Description** : trouver la première voiture de chaque intervalle de 10 minutes.

**Entrée** :

| LicensePlate | Make | Temps |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Sortie** :

| LicensePlate | Make | Temps |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solution** :

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

À présent, nous allons modifier le problème et trouver la première voiture d’une marque donnée dans chaque intervalle de 10 minutes.

| LicensePlate | Make | Temps |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution** :

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## Exemple de requête : recherche du dernier événement dans une fenêtre
**Description** : trouver la dernière voiture de chaque intervalle de 10 minutes.

**Entrée** :

| LicensePlate | Make | Temps |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Sortie** :

| LicensePlate | Make | Temps |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution** :

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**Explication** : la requête comprend deux étapes, la première servant à rechercher l'horodatage le plus récent dans une plage de 10 minutes. La deuxième étape joint les résultats de la première requête avec des flux de données d'origine pour rechercher les événements qui correspondent aux derniers horodatages dans chaque fenêtre.

## Exemple de requête : détection de l’absence d’événements
**Description** : vérifier qu’un flux de données n’a aucune valeur correspondant à un critère donné. Par exemple, 2 voitures consécutives de la même marque sont-elles entrées sur la voie de péage en 90 secondes ?

**Entrée** :

| Make | LicensePlate | Temps |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**Sortie** :

| Make | Temps | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solution** :

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explication** : utilisez LAG pour lire le flux d’entrée de l’événement précédent et obtenir la valeur de la marque. Comparez-la ensuite à la valeur de Make de l’événement actuel, créez une sortie si elles sont identiques, et utilisez LAG pour obtenir des données sur la voiture précédente.

## Exemple de requête : détection de la durée entre des événements
**Description** : rechercher la durée d’un événement donné. Par exemple, en fonction d’un parcours web, déterminer le temps passé sur une fonctionnalité.

**Entrée** :
  
| Utilisateur | Fonctionnalité | Événement | Temps |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Démarrer | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Terminer | 2015-01-01T00:00:08.0000000Z |
  
**Sortie** :
  
| Utilisateur | Fonctionnalité | Durée |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solution**

````
    SELECT
    	[user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
    	Event = 'end'
````

**Explication** : utilisez la fonction LAST pour récupérer la dernière valeur d’heure quand le type d’événement est « Démarrer ». Notez que la fonction LAST utilise PARTITION BY [user] pour indiquer que le résultat doit être calculé par utilisateur unique. La requête a un seuil maximal de 1 heure pour la différence de temps entre les événements « Démarrer » et « Terminer » (LIMIT DURATION(hour, 1)), mais ce seuil est configurable en fonction des besoins.

## Exemple de requête : détection de la durée d’une condition
**Description** : déterminer sur quelle période un problème s’est produit. Par exemple, supposons qu’un bogue entraînant un poids incorrect pour toutes les voitures (d’un poids supérieur à 20 000 livres) se produit et que nous voulons calculer la durée du bogue.

**Entrée** :

| Make | Temps | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Sortie** :

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solution** :

````
	WITH SelectPreviousEvent AS
	(
	SELECT
	*,
		LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
		LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
	FROM input TIMESTAMP BY [time]
	)

	SELECT 
    	LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
    	previousTime [EndFault]
	FROM SelectPreviousEvent
	WHERE
    	[weight] < 20000
	    AND previousWeight > 20000
````

**Explication** : utiliser LAG pour afficher le flux d’entrée sur 24 heures et rechercher les instances dans lesquelles StartFault et StopFault sont couvertes par la condition « weight < 20000 » (poids inférieur à 20 000 livres).

## Exemple de requête : remplir les valeurs manquantes
**Description** : pour le flux des événements qui ont des valeurs manquantes, produire un flux d’événements à intervalles réguliers. Par exemple, générer, toutes les 5 secondes, un événement qui indique le point de données le plus récemment constaté.

**Entrée** :

| t | value |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**Sortie (10 premières lignes)** :

| windowend | lastevent.t | lastevent.value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solution** :

    SELECT
    	System.Timestamp AS windowEnd,
    	TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
    	input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explication** : cette requête génère des événements toutes les 5 secondes et indique le dernier événement précédemment reçu. La durée [Fenêtre récurrente](https://msdn.microsoft.com/library/dn835041.aspx "Fenêtre récurrente - Azure Stream Analytics") détermine la période que la requête remonte pour rechercher le dernier événement (300 secondes, dans cet exemple).


## Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0921_2016-->