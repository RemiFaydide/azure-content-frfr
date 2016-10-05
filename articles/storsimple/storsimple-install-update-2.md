<properties
   pageTitle="Installer Update 2 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 Series Update 2 sur votre appareil de la gamme StorSimple 8000."
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

# Installer Update 2 sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment installer Update 2 sur un appareil StorSimple exécutant une version logicielle antérieure par le biais du portail Azure Classic. Ce didacticiel couvre également les étapes à effectuer pour la mise à jour quand une passerelle est configurée sur une interface réseau différente de DATA 0 de l’appareil StorSimple et que la mise à jour porte sur une version logicielle antérieure à Update 1.

Update 2 comprend des mises à jour logicielles de l’appareil, des mises à jour du pilote LSI et des mises à jour du microprogramme de disque. Les mises à jour logicielles de l’appareil et du pilote LSI sont des mises à jour non perturbatrices qui peuvent être appliquées par le biais du portail Azure Classic. Les mises à jour du microprogramme de disque sont des mises à jour perturbatrices et peuvent uniquement être appliquées par le biais de l’interface Windows PowerShell de l’appareil.

> [AZURE.IMPORTANT]

> -  Il est possible que vous ne voyiez pas immédiatement Update 2, car nous effectuons un déploiement échelonné des mises à jour. Revérifiez les mises à jour dans quelques jours, car celle-ci sera bientôt disponible.
> - Un ensemble de vérifications préalables manuelles et automatiques sont effectuées avant l’installation pour déterminer l’intégrité de l’appareil sur le plan de l’état du matériel et de la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure Classic.
> - Nous vous recommandons d’installer les mises à jour du pilote et du logiciel au moyen du portail Azure Classic. Vous devez uniquement accéder à l’interface Windows PowerShell de l’appareil (pour installer les mises à jour) en cas d’échec de la vérification de la passerelle avant la mise à jour dans le portail. L’installation des mises à jour peut prendre 4 à 7 heures (mises à jour Windows incluses). Les mises à jour du mode de maintenance doivent être installées via l’interface Windows PowerShell de l’appareil. Les mises à jour du mode de maintenance étant des mises à jour perturbatrices, elles entraînent un temps d’arrêt pour votre appareil.
> - Si vous exécutez l’outil StorSimple Snapshot Manager facultatif, veillez à le mettre à niveau vers la version Update 2 avant de mettre à jour l’appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installer Update 2 par le biais du portail Azure Classic

Suivez la procédure suivante pour mettre à jour votre appareil vers [Update 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Update 2 permet à Microsoft d’extraire des informations de diagnostic supplémentaires de l’appareil. Par conséquent, lorsque notre équipe en charge des opérations identifie des appareils qui ont un problème, nous sommes plus à même de collecter des informations à partir de ces appareils et de diagnostiquer les problèmes. En acceptant d’installer Update 2, vous nous permettez de fournir cette assistance proactive.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre appareil exécute **StorSimple 8000 Series Update 2 (6.3.9600.17673)**. Le paramètre **Dernière date de mise à jour** doit également être modifié. Vous verrez également que les mises à jour en mode de maintenance sont disponibles (ce message peut continuer à afficher jusqu’à 24 heures après avoir l’installation des mises à jour).

    Les mises à jour en mode maintenance entraînent des temps d’arrêt de l’appareil et ne peuvent être appliquées que par le biais de l’interface Windows PowerShell de votre appareil. Dans certains cas lorsque vous exécutez Update 1.2, le microprogramme de disque peut être déjà à jour, auquel cas vous n’avez pas besoin d’installer les mises à jour du mode de maintenance.

13. Téléchargez les mises à jour en mode maintenance à l’aide de la procédure indiquée dans [Pour télécharger des correctifs logiciels](#to-download-hotfixes) pour rechercher et télécharger KB3121899, qui installe les mises à jour du microprogramme de disque (les autres mises à jour doivent déjà être installés à ce stade).

13. Suivez les étapes répertoriées dans [Installer et vérifier le correctif logiciel en mode Maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer ces mises à jour en mode maintenance.


## Installer Update 2 en tant que correctif logiciel

Utilisez cette procédure en cas d’échec de la vérification de la passerelle lors de la tentative d’installation des mises à jour par le biais du portail Azure Classic. La vérification échoue, car vous avez une passerelle affectée à une interface réseau différente de DATA 0 et votre appareil exécute une version logicielle antérieure à Update 1.

Les versions logicielles qui peuvent être mises à niveau à l’aide de la méthode du correctif logiciel sont : Update 0.1, Update 0.2, Update 0.3, Update 1, Update 1.1 et Update 1.2. La méthode du correctif logiciel implique les trois étapes suivantes :

- Télécharger les correctifs logiciels à partir du catalogue Microsoft Update
- Installer et vérifier les correctifs logiciels en mode Normal
- Installer et vérifier les correctifs logiciels en mode Maintenance

Pour installer Update 2 comme un correctif, vous devez télécharger et installer les correctifs suivants :

| Ordre | Ko | Description | Type de mise à jour |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | Mise à jour logicielle | Normal |
| 2 | KB3121900 | Pilote LSI | Normal |
| 3 | KB3080728 | Correctif Storport </br> Windows Server 2012 R2 | Normal |
| 4 | KB3090322 | Correctif Spaceport </br> Windows Server 2012 R2 | Normal |
| 5 | KB3121899 | Microprogramme de disque | Maintenance |


> [AZURE.IMPORTANT]
>
> - Si votre appareil exécute la version commerciale (GA), contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide avec cette mise à jour.
> - Cette procédure ne doit être effectuée qu’une seule fois pour appliquer Update 2. Pour appliquer les mises à jour ultérieures, vous pouvez utiliser le portail Azure Classic.
> - Chaque installation d’un correctif logiciel peut prendre environ 20 minutes. Le temps total d’installation avoisine les 2 heures.
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, vérifiez que les deux contrôleurs d’appareil sont en ligne et que tous les composants matériels sont sains.

Exécutez la procédure suivante pour appliquer cette mise à jour en tant que correctif logiciel.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## Étapes suivantes

En savoir plus sur la [version Update 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0921_2016-->