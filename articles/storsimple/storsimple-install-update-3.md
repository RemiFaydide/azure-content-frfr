<properties
   pageTitle="Installer Update 3 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple série 8000 Update 3 sur votre appareil StorSimple série 8000."
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

# Installer Update 3 sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment installer Update 3 sur un appareil StorSimple exécutant une version logicielle antérieure par le biais du Portail Azure Classic et à l’aide de la méthode du correctif logiciel. La méthode du correctif logiciel est utilisée quand une passerelle est configurée sur une interface réseau différente de DATA 0 de l’appareil StorSimple et que la mise à jour porte sur une version logicielle antérieure à Update 1.

Update 3 comprend les mises à jour du logiciel de l’appareil, du microprogramme et du pilote LSI et de Storport et Spaceport. En cas de mise à jour à partir d’Update 2 ou d’une version antérieure, vous devrez également appliquer les mises à jour iSCSI, WMI et, dans certains cas, du microprogramme de disque. Les correctifs du logiciel de l’appareil, WMI, iSCSI, du pilote LSI, de Spaceport et de Storport sont des mises à jour non perturbatrices qui peuvent être appliquées par le biais du portail Azure Classic. Les mises à jour du microprogramme de disque sont des mises à jour perturbatrices et peuvent uniquement être appliquées par le biais de l’interface Windows PowerShell de l’appareil.

> [AZURE.IMPORTANT]

> - Un ensemble de vérifications préalables manuelles et automatiques sont effectuées avant l’installation pour déterminer l’intégrité de l’appareil sur le plan de l’état du matériel et de la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure Classic.
> - Nous vous recommandons d’installer les mises à jour du pilote et du logiciel au moyen du portail Azure Classic. Vous devez uniquement accéder à l’interface Windows PowerShell de l’appareil (pour installer les mises à jour) en cas d’échec de la vérification de la passerelle avant la mise à jour dans le portail. Selon la version que vous mettez à jour, l’installation des mises à jour peut prendre entre 1,5 et 2,5 heures. Les mises à jour du mode de maintenance doivent être installées via l’interface Windows PowerShell de l’appareil. Les mises à jour du mode de maintenance étant des mises à jour perturbatrices, elles entraînent un temps d’arrêt pour votre appareil.
> - Si vous exécutez l’outil StorSimple Snapshot Manager facultatif, veillez à le mettre à niveau vers la version Update 2 avant de mettre à jour l’appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installer Update 3 avec le Portail Azure Classic

Suivez la procédure suivante pour mettre à jour votre appareil vers [Update 3](storsimple-update3-release-notes.md).


> [AZURE.NOTE]
Si vous appliquez Update 2 ou version ultérieure (y compris Update 2.1), Microsoft sera en mesure d’extraire les informations de diagnostic supplémentaires à partir de l’appareil. Par conséquent, lorsque notre équipe en charge des opérations identifie des appareils qui ont un problème, nous sommes plus à même de collecter des informations à partir de ces appareils et de diagnostiquer les problèmes. En acceptant d’installer Update 2 ou version ultérieure, vous nous permettez de fournir cette assistance proactive.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre appareil exécute **StorSimple série 8000 Update 3 (6.3.9600.17759)**. Le paramètre **Dernière date de mise à jour** doit également être modifié.

	Si vous effectuez une mise à jour à partir d’une version antérieure à Update 2, vous verrez également que les mises à jour en mode de maintenance sont disponibles (ce message peut continuer à afficher jusqu’à 24 heures après l’installation des mises à jour).

    Les mises à jour en mode maintenance entraînent des temps d’arrêt de l’appareil et ne peuvent être appliquées que par le biais de l’interface Windows PowerShell de votre appareil. Dans certains cas lorsque vous exécutez Update 1.2, le microprogramme de disque peut être déjà à jour, auquel cas vous n’avez pas besoin d’installer les mises à jour du mode de maintenance.

	Si vous effectuez la mise à jour à partir d’Update 2 ou d’une version ultérieure, votre appareil devrait maintenant être à jour. Vous pouvez donc ignorer les étapes restantes.

13. Téléchargez les mises à jour en mode maintenance à l’aide de la procédure indiquée dans [Pour télécharger des correctifs logiciels](#to-download-hotfixes) pour rechercher et télécharger KB3121899, qui installe les mises à jour du microprogramme de disque (les autres mises à jour doivent déjà être installées à ce stade).

13. Suivez les étapes répertoriées dans [Installer et vérifier le correctif logiciel en mode Maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer ces mises à jour en mode maintenance.

  

## Installer Update 3 en tant que correctif logiciel

Utilisez cette procédure en cas d’échec de la vérification de la passerelle lors de la tentative d’installation des mises à jour par le biais du portail Azure Classic. La vérification échoue, car vous avez une passerelle affectée à une interface réseau différente de DATA 0 et votre appareil exécute une version logicielle antérieure à Update 1.

Les versions logicielles qui peuvent être mises à niveau à l’aide de la méthode du correctif logiciel sont les suivantes :

- Update 0.1, 0.2, 0.3
- Update 1, 1.1, 1.2
- Update 2, 2.1, 2.2

> [AZURE.IMPORTANT]
>
> - Si votre appareil exécute la version commerciale (GA), contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide avec cette mise à jour.

La méthode du correctif logiciel implique les trois étapes suivantes :

1.  Télécharger les correctifs logiciels à partir du catalogue Microsoft Update

2.  Installer et vérifier les correctifs logiciels en mode Normal

3.  Installer et vérifier le correctif en mode de maintenance (uniquement dans le cas d’une mise à jour à partir d’une version du logiciel antérieure à Update 2).


#### Télécharger des mises à jour pour votre appareil

**Si votre appareil exécute Update 2.1 ou 2.2**, vous devez télécharger et installer les correctifs suivants dans l’ordre indiqué :

| Ordre | Ko | Description | Type de mise à jour | Durée d’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 1\. | KB3186843 | Mise à jour logicielle &#42; | Standard <br></br>Sans interruption de service | ~ 45 minutes |
| 2\. | KB3186859 | Pilote LSI et microprogramme | Standard <br></br>Sans interruption de service | ~ 20 minutes |
| 3\. | KB3121261 | Correctif Storport et Spaceport </br> Windows Server 2012 R2 | Standard <br></br>Sans interruption de service | ~ 20 minutes |

&#42; *Remarque : la mise à jour logicielle se compose de deux fichiers binaires : la mise à jour logicielle de l’appareil, précédée de `all-hcsmdssoftwareupdate`, et les agents CIS et MDS, précédés de `all-cismdsagentupdatebundle`. La mise à jour du logiciel de l’appareil doit être installée avant les agents CIS et MDS. Vous devez également redémarrer le contrôleur actif par le biais de l’applet de commande `Restart-HcsController` après avoir exécuté la mise à jour des agents CIS et MDS (et avant d’appliquer les autres mises à jour).*


**Si votre appareil exécute Update 0.1, 0.2, 0.3, 1.0, 1.1, 1.2 ou 2.0**, vous devez télécharger et installer les correctifs logiciels suivants en plus des mises à jour du logiciel, du microprogramme et du pilote LSI (indiquées dans le tableau précédent), dans l’ordre :

| Ordre | Ko | Description | Type de mise à jour | Durée d’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 4\. | KB3146621 | Package iSCSI | Standard <br></br>Sans interruption de service | ~ 20 minutes |
| 5\. | KB3103616 | Package WMI | Standard <br></br>Sans interruption de service | ~ 12 minutes |


<br></br>

**Si votre appareil exécute la version 0.2, 0.3, 1.0, 1.1 ou 1.2**, vous devrez peut-être également installer les mises à jour du microprogramme de disque en plus de toutes les mises à jour indiquées dans les tableaux précédents. Pour vérifier si vous avez besoin des mises à jour du microprogramme de disque, vous pouvez exécuter l’applet de commande `Get-HcsFirmwareVersion`. Si vous utilisez la version `XMGG`, `XGEG`, `KZ50`, `F6C2` ou `VR08` du microprogramme, vous n’avez pas besoin d’installer ces mises à jour.


| Ordre | Ko | Description | Type de mise à jour | Durée d’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 6\. | KB3121899 | Microprogramme de disque | Maintenance <br></br>Interruption de service | ~ 30 minutes |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Cette procédure ne doit être effectuée qu’une seule fois pour appliquer Update 3. Pour appliquer les mises à jour ultérieures, vous pouvez utiliser le portail Azure Classic.
> - Si vous effectuez la mise à jour à partir d’Update 2.2, le temps total d’installation sera d’environ 1,1 heure.
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, vérifiez que les deux contrôleurs d’appareil sont en ligne et que tous les composants matériels sont sains.

Procédez comme suit pour télécharger et importer les correctifs logiciels.

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## Étapes suivantes

Découvrez plus en détails la [version Update 3](storsimple-update3-release-notes.md).

<!---HONumber=AcomDC_0921_2016-->