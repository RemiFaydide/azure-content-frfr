<properties
   pageTitle="Utilisation du tableau de bord du service StorSimple Manager | Microsoft Azure"
   description="Décrit le tableau de bord du service StorSimple Manager et explique comment l'utiliser pour afficher les mesures de stockage et les initiateurs connectés, et rechercher le numéro de série et l’IQN."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# Utilisation du tableau de bord d’appareil StorSimple Manager

## Vue d'ensemble

Le tableau de bord d’appareil StorSimple Manager offre une vue d’ensemble des informations relatives à un appareil StorSimple spécifique, à la différence du tableau de bord de service, qui regroupe les informations concernant tous les appareils inclus dans votre solution Microsoft Azure StorSimple.

![Page du tableau de bord d’un appareil](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Le tableau de bord contient les informations suivantes :

- **Zone de graphique** : vous pouvez examiner les métriques de stockage qui vous intéressent dans la zone de graphique située dans la partie supérieure du tableau de bord. Ce graphique vous permet d’examiner les métriques relatives au stockage principal total (quantité de données écrites par les hôtes sur votre appareil), ainsi que le stockage cloud total consommé par votre appareil au cours d’une période donnée.

     Dans ce contexte, le *stockage principal* fait référence à la quantité totale de données écrites par l’hôte, et peut être détaillé par type de volume : le *stockage hiérarchisé principal* inclut à la fois les données stockées localement et les données stockées dans le cloud ; le *stockage principal attaché localement* inclut uniquement les données stockées localement. Le *stockage cloud*, quant à lui, est une mesure de la quantité totale de données stockées dans le cloud. Cela inclut les données hiérarchisées et les sauvegardes. Notez que les données stockées dans le cloud sont dédupliquées et compressées, tandis que le stockage principal indique la quantité de stockage utilisée avant que les données ne soient dédupliquées et compressées. Vous pouvez comparer ces deux nombres pour avoir une idée du taux de compression. Qu’il s’agisse du stockage principal ou du stockage cloud, les quantités indiquées dépendent de la fréquence de suivi que vous configurez. Par exemple, si vous optez pour une fréquence hebdomadaire, le graphique affiche des données pour chaque jour de la semaine précédente.

	 Vous pouvez configurer le graphique comme suit :

	 - Pour afficher la quantité de stockage cloud consommée au fil du temps, sélectionnez l’option **STOCKAGE CLOUD UTILISÉ**. Pour afficher le stockage total des données écrites par l’hôte, sélectionnez les options **STOCKAGE HIÉRARCHISÉ PRINCIPAL UTILISÉ** et **STOCKAGE PRINCIPAL ÉPINGLÉ LOCALEMENT UTILISÉ**. Dans l'illustration, les deux options sont sélectionnées. Par conséquent, le graphique affiche les quantités pour le stockage cloud et le stockage principal. Sachez que tout espace de stockage principal utilisé avant l’installation de la mise à jour 2 est indiqué dans la ligne **STOCKAGE HIÉRARCHISÉ PRINCIPAL UTILISÉ**.
	 - Le menu déroulant situé en haut à droite du graphique permet de spécifier une période : 1 semaine, 1 mois, 3 mois ou 1 an. À noter que le graphique de niveau supérieur n’est actualisé qu'une fois par jour. Autrement dit, il reflète les totaux du jour précédent.

     Pour plus d’informations, consultez [Utilisation du service StorSimple Manager pour surveiller votre appareil StorSimple](storsimple-monitor-device.md).

- **Vue d’ensemble de l’utilisation** : la zone de **vue d’ensemble de l’utilisation** affiche la quantité de stockage principal utilisée, la quantité de stockage approvisionnée et la quantité de stockage maximale de votre appareil. En comparant ces chiffres d'utilisation à la quantité maximale de stockage disponible, vous pouvez voir en un coup de œil si vous avez besoin d’obtenir du stockage supplémentaire. À noter que cette vue d’ensemble est mise à jour toutes les 15 minutes et qu'en raison de la différence de fréquence de mise à jour, elle peut indiquer des chiffres différents de ceux que montre la zone de graphique de dessus, qui est mise à jour quotidiennement. Pour plus d’informations, consultez [Utilisation du service StorSimple Manager pour surveiller votre appareil StorSimple](storsimple-monitor-device.md).


- **Alertes** : la zone **alertes** contient une vue d'ensemble des alertes relatives à votre appareil. Les alertes sont regroupées par niveau de gravité et le nombre d'alertes est indiqué pour chaque niveau. Le fait de cliquer sur le niveau de gravité d'une alerte ouvre une vue délimitée de l'onglet alertes, qui n’affiche que les alertes de ce niveau de gravité pour l'appareil concerné.

- **Tâches** : la zone **tâches** indique le résultat d’une activité de tâche récente. Vous savez ainsi si le système fonctionne comme prévu ou si vous devez prendre une mesure corrective. Pour plus d'informations sur les tâches effectuées récemment, cliquez sur **Travaux réussis dans les dernières 24 heures**.

- La zone **aperçu rapide** à droite du tableau de bord fournit des informations utiles, telles que le modèle, le numéro de série, l'état, la description et le nombre de volumes de l'appareil.

Vous pouvez aussi configurer le basculement et afficher les initiateurs connectés depuis le tableau de bord de l'appareil.

Les tâches courantes qu’il est possible d’effectuer dans cette page sont les suivantes :

- Affichage des initiateurs connectés

- Recherche du numéro de série de l’appareil

- Recherche de l’IQN cible de l’appareil

## Affichage des initiateurs connectés

Vous pouvez afficher les initiateurs iSCSI connectés à votre appareil en cliquant sur le lien **Afficher les initiateurs connectés** fourni dans la zone **aperçu rapide** du tableau de bord de votre appareil. Cette page propose un tableau qui répertorie les initiateurs correctement connectés à votre appareil. Pour chaque initiateur, vous pouvez voir :

- le nom complet iSCSI (IQN) de l'initiateur connecté ;

- le nom de l'enregistrement de contrôle d'accès (ACR) qui autorise cet initiateur connecté ;

- l'adresse IP de l'initiateur connecté ;

- les interfaces réseau auxquelles l'initiateur est connecté sur votre appareil de stockage, qui peuvent aller de DATA 0 à DATA 5 ;

- tous les volumes auxquels l'initiateur connecté est autorisé à accéder en fonction de la configuration ACR actuelle.

Si vous constatez la présence d’initiateurs inattendus dans cette liste ou l’absence d’initiateurs qui auraient dû apparaître, vérifiez votre configuration ACR. Le nombre d’initiateurs pouvant se connecter à votre appareil est limité à 512.

## Recherche du numéro de série de l’appareil

Vous aurez peut-être besoin du numéro de série de l’appareil au moment de configurer MPIO (Microsoft Multipath I/O) sur l’appareil. Pour rechercher le numéro de série de l’appareil, procédez comme suit.

#### Pour rechercher le numéro de série de l’appareil

1. Accédez à **Appareils** > **Tableau de bord**.

2. Dans le volet droit du tableau de bord, repérez la zone **aperçu rapide**.

3. Faites défiler l’écran vers le bas et repérez le numéro de série.

## Recherche de l’IQN cible de l’appareil

Vous aurez peut-être besoin de l'IQN cible au moment de configurer le protocole CHAP (Challenge Handshake Authentication Protocol) sur votre appareil StorSimple. Pour rechercher l’IQN cible de l’appareil, procédez comme suit.

### Pour rechercher l’IQN cible de l’appareil

1. Accédez à **Appareils** > **Tableau de bord**.

1. Dans le volet droit du tableau de bord, repérez la zone **aperçu rapide**.

1. Faites défiler l’écran vers le bas et repérez l’IQN cible.

## Étapes suivantes

- En savoir plus sur le [tableau de bord du service StorSimple Manager](storsimple-service-dashboard.md).
- En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0921_2016-->