<properties
	pageTitle="Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning | Microsoft Azure"
	description="Découvrez comment mettre correctement à l’échelle des travaux Stream Analytics (partitionnement, quantité d’unités de diffusion en continu, etc.) lors de l’utilisation de fonctions Azure Machine Learning."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"
/>

# Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning

Il est souvent très facile de configurer un travail Stream Analytics et d’exécuter des exemples de données par son intermédiaire. Que faire si nous avons besoin d’exécuter le même travail avec un volume de données plus important ? Nous devons comprendre comment configurer le travail Stream Analytics pour permettre sa mise à l’échelle. Dans ce document, nous nous concentrerons sur les aspects particuliers de la mise à l’échelle des travaux Stream Analytics avec des fonctions Machine Learning. Pour plus d’informations sur la procédure de mise à l’échelle des travaux Stream Analytics en général, voir l’article [Mise à l’échelle des travaux](stream-analytics-scale-jobs.md).

## Qu’est-ce qu’une fonction Azure Machine Learning dans Stream Analytics ?

Une fonction Machine Learning dans Stream Analytics peut être utilisée comme un appel de fonction normal dans le langage de requête Stream Analytics. Toutefois, en arrière-plan, ces appels de fonction sont en fait des demandes de services web Microsoft Azure Machine Learning. Les services web Machine Learning prennent en charge le « traitement par lot » de plusieurs lignes, appelé mini-lot, dans un même appel d’API de service web pour améliorer le débit global. Pour plus d’informations, voir l’article concernant les [fonctions Azure Machine Learning dans Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) (en anglais) et l’article [Services web Azure Machine Learning](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## Configurer un travail Stream Analytics avec des fonctions Azure Machine Learning

Lorsque vous configurez une fonction Machine Learning pour un travail Stream Analytics, vous devez prendre en compte deux paramètres : la taille de lot des appels de fonction Machine Learning et les unités de diffusion en continu approvisionnées pour le travail Stream Analytics. Pour déterminer les valeurs appropriées pour ces paramètres, commencez par trouver le bon compromis entre latence et débit, autrement dit, entre la latence du travail Stream Analytics et le débit de chaque unité de diffusion en continu. Il est toujours possible d’ajouter des unités de diffusion en continu à un travail pour augmenter le débit d’une requête Stream Analytics correctement partitionnée. Toutefois, cet ajout augmente le coût d’exécution du travail.

Il est donc important de déterminer la *tolérance* de latence pour l’exécution d’un travail Stream Analytics. L’augmentation de la taille de lot entraîne naturellement une latence supplémentaire dans le cadre de l’exécution des demandes de services Azure Machine Learning, et donc une augmentation de la latence du travail Stream Analytics. En revanche, l’accroissement de la taille de lot permet au travail Stream Analytics de traiter davantage d’événements avec le *même nombre* * de demandes de services web Machine Learning. L’augmentation de la latence de service web Machine Learning est souvent consécutive à l’augmentation de la taille de lot ; il est donc important de prendre en compte la taille de lot la plus rentable pour un service web Machine Learning dans n’importe quelle situation donnée. La taille de lot par défaut pour les demandes de service web est de 1 000 et peut être modifiée au moyen de l’[API REST Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "API REST Stream Analytics") ou du [client PowerShell pour Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Client PowerShell pour Stream Analytics").

Une fois qu’une taille de lot a été déterminée, la quantité d’unités de diffusion en continu peut être fixée en fonction du nombre d’événements que la fonction doit traiter par seconde. Pour plus d’informations sur les unités de diffusion en continu, voir l’article décrivant la procédure de [mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md#configuring-streaming-units).

En général, il existe 20 connexions simultanées au service web Machine Learning pour chaque ensemble de 6 unités de diffusion en continu, mais les travaux à 1 unité de diffusion en continu et à 3 unités de diffusion en continu bénéficieront également de 20 connexions simultanées. Par exemple, si le débit de données d’entrée est de 200 000 événements par seconde, et que la taille de lot par défaut de 1 000 est conservée, la latence de service web résultante avec un mini-lot de 1 000 événements sera de 200 ms. Cela signifie que chaque connexion peut envoyer 5 demandes au service web Machine Learning en une seconde. Dans le cas de 20 connexions, le travail Stream Analytics peut traiter 20 000 événements en 200 ms, et donc 100 000 événements en une seconde. Par conséquent, pour traiter 200 000 événements par seconde, le travail Stream Analytics a besoin de 40 connexions simultanées, ce qui correspond à 12 unités de diffusion en continu. Le diagramme ci-dessous illustre les demandes émanant du travail Stream Analytics vers le point de terminaison de service web Machine Learning. Pour chaque ensemble de 6 unités de diffusion en continu, il existe jusqu’à 20 connexions simultanées au service web Machine Learning.

![Mettre à l’échelle Stream Analytics avec des fonctions Machine Learning - Exemple de 2 travaux](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Mettre à l’échelle Stream Analytics avec des fonctions Machine Learning - Exemple de 2 travaux")

Généralement, pour la taille de lot ***B*** et la latence de service web en millisecondes ***L*** pour la taille de lot B, le débit d’un travail Stream Analytics avec ***N*** unités de diffusion en continu est le suivant :

![Mettre à l’échelle Stream Analytics avec des fonctions Machine Learning - Formule](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Mettre à l’échelle Stream Analytics avec des fonctions Machine Learning - Formule")

Un autre aspect éventuel à prendre en compte est le nombre maximal d’appels simultanés du côté du service web Machine Learning ; il est recommandé de définir ce paramètre sur sa valeur maximale (200 à l’heure actuelle).

Pour plus d’informations sur ce paramètre, voir l’[article sur la mise à l’échelle pour les services web Machine Learning](../machine-learning/machine-learning-scaling-webservice.md).

## Exemple – Analyse de sentiments

L’exemple suivant inclut un travail Stream Analytics avec la fonction Machine Learning d’analyse de sentiments, comme décrit dans le [didacticiel d’intégration de Machine Learning à Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

La requête est une requête simple entièrement partitionnée suivie de la fonction **sentiment**, comme indiqué ci-dessous :

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )
    
    Select text, result.[Score]
    Into output
    From subquery

Considérons le scénario suivant ; avec un débit de 10 000 tweets par seconde, un travail Stream Analytics doit être créé pour effectuer une analyse de sentiments relative aux tweets (événements). Avec 1 unité de diffusion en continu, ce travail Stream Analytics serait-il en mesure de gérer le trafic ? L’utilisation de la taille de lot par défaut de 1 000 devrait permettre de prendre en charge le trafic d’entrée. En outre, la fonction Machine Learning ajoutée ne devrait pas générer plus d’une seconde de latence, ce qui constitue la latence générale par défaut du service web Machine Learning d’analyse de sentiments (avec une taille de lot par défaut de 1 000). La latence **totale** ou de bout en bout du travail Stream Analytics serait généralement de l’ordre de quelques secondes. Examinons ce travail Stream Analytics plus en détail, *en particulier* les appels de fonction Machine Learning. La taille de lot étant de 1 000, un débit de 10 000 événements nécessitera l’envoi de 10 demandes environ au service web. Même avec 1 unité de diffusion en continu, le nombre de connexions simultanées est suffisant pour prendre en charge ce trafic d’entrée.

Mais que se passera-t-il si le taux d’événements d’entrée est multiplié par 100 et que le travail Stream Analytics doit alors traiter 1 000 000 de tweets par seconde ? Nous avons deux options :

1.  augmenter la taille de lot ;
2.  partitionner le flux d’entrée pour traiter les événements en parallèle.

Si nous choisissons la première option, la **latence** du travail augmentera.

La seconde option nécessitera l’approvisionnement d’un plus grand nombre d’unités de diffusion en continu, et générera donc davantage de demandes de service web Machine Learning simultanées. Cela se traduira par une augmentation du **coût** du travail.


Supposons que la latence du service web Machine Learning d’analyse de sentiments soit inférieure ou égale à 200 ms pour des lots de 1 000 événements, égale à 250 ms pour des lots de 5 000 événements, égale à 300 ms pour des lots de 10 000 événements ou égale à 500 ms pour des lots de 25 000 événements.

1. Si nous utilisions la première option (**sans** approvisionnement d’un plus grand nombre d’unités de diffusion en continu), la taille de lot pourrait être augmentée à **25 000**. Cela permettrait alors au travail de traiter 1 000 000 d’événements avec 20 connexions simultanées au service web Machine Learning (avec une latence de 500 ms par appel). La latence supplémentaire du travail Stream Analytics découlant des demandes de la fonction sentiment par rapport aux demandes de service web Machine Learning passerait donc de **200 ms** à **500 ms**. Toutefois, notez que la taille de lot **ne peut pas** être augmentée à l’infini, car les services web Machine Learning exigent que la taille utile d’une demande soit de 4 Mo et imposent un délai d’expiration de 100 secondes pour les demandes de service web de taille moindre.
2. Si nous utilisions la seconde option, en conservant la taille de lot de 1000, avec une latence de service web de 200 ms, chaque ensemble de 20 connexions simultanées au service web serait alors en mesure de traiter 1000 * 20 * 5 événements = 100 000 événements par seconde. Pour traiter 1 000 000 d’événements par seconde, le travail aurait donc besoin de 60 unités de diffusion en continu. Par rapport à la première option, le travail Stream Analytics effectuerait un plus grand nombre de demandes de service web par lot, ce qui entraînerait alors une augmentation des coûts.

Le tableau ci-après présente le débit du travail Stream Analytics pour différentes unités de diffusion en continu et tailles de lot (en nombre d’événements par seconde).

| Unités de diffusion en continu | | | | Taille de lot (latence Machine Learning) | |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| | | | | | |
| | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| 1 unité de recherche | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| 3 unités de diffusion en continu | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| 6 unités de diffusion en continu | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| 12 unités de diffusion en continu | 5 000 | 10 000 | 40 000 | 60 000 | 100 000 |
| 18 unités de diffusion en continu | 7 500 | 15 000 | 60 000 | 90 000 | 150 000 |
| 24 unités de diffusion en continu | 10 000 | 20 000 | 80 000 | 120 000 | 200 000 |
| … | … | … | … | … | … |
| 60 unités de diffusion en continu | 25 000 | 50 000 | 200 000 | 300 000 | 500 000 |

À ce stade, vous devriez déjà avoir une bonne compréhension du mode de fonctionnement des fonctions Machine Learning dans Stream Analytics. Vous devez également avoir compris que les travaux Stream Analytics envoient les données de sources de données par le biais d’une transmission de type pull, et que chaque transmission de ce type renvoie un lot d’événements destinés à être traités par le travail Stream Analytics. Comment ce modèle de transmission de type pull affecte-t-il les demandes de service web Machine Learning ?

En règle générale, la taille de lot que nous avons définie pour les fonctions Machine Learning ne sera pas exactement divisible par le nombre d’événements renvoyés par chaque transmission de type pull de travail Stream Analytics. Si cela se produit, le service web Machine Learning sera appelé avec des lots « partiels ». Cette approche est destinée à éviter un surcroît de latence de travail induit par le regroupement des événements issus des différentes transmissions de type pull.

## Nouvelles métriques de surveillance associées aux fonctions

Dans la zone de surveillance d’un travail Stream Analytics, trois nouvelles métriques associées aux fonctions ont été ajoutées. Il s’agit des métriques DEMANDES DE FONCTION, ÉVÉNEMENTS DE FONCTION et ÉCHEC DES DEMANDES DE FONCTION, comme illustré dans le graphique ci-après.

![Mettre à l’échelle Stream Analytics avec des fonctions Machine Learning - Métriques](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Mettre à l’échelle Stream Analytics avec des fonctions Machine Learning - Métriques")

Les définitions de ces métriques sont les suivantes :

**DEMANDES DE FONCTION** : nombre de demandes de fonction.

**ÉVÉNEMENTS DE FONCTION** : nombre d’événements dans les demandes de fonction.

**ÉCHEC DES DEMANDES DE FONCTION** : nombre d’échecs de demande de fonction.

## Points clés  

Pour récapituler les points principaux, la mise à l’échelle d’un travail Stream Analytics avec des fonctions Machine Learning nécessite la prise en compte des éléments suivants :

1.  taux d’événements d’entrée ;
2.  latence tolérée pour le travail Stream Analytics en cours d’exécution (et donc taille de lot des demandes de service web Machine Learning) ;
3.  unités de diffusion en continu Stream Analytics approvisionnées et nombre de demandes de service web Machine Learning (coûts associés aux fonctions supplémentaires).

Notre exemple portait sur une requête Stream Analytics entièrement partitionnée. Si vous avez besoin d’une requête plus complexe, le [Forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics) est une précieuse ressource vous permettant d’obtenir une aide complémentaire auprès de l’équipe Stream Analytics.

## Étapes suivantes

Pour en savoir plus sur Stream Analytics, consultez :

- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->