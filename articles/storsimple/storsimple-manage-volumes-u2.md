<properties
   pageTitle="Gérer vos volumes StorSimple (U2) | Microsoft Azure"
   description="Explique comment ajouter, modifier, analyser et supprimer des volumes StorSimple et comment les mettre hors connexion si nécessaire."
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
   ms.workload="NA"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# Utilisez le service StorSimple Manager pour gérer les volumes (Mise à jour 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## Vue d'ensemble

Ce didacticiel explique comment utiliser le service StorSimple Manager pour créer et gérer des volumes sur l’appareil StorSimple et l’appareil virtuel StorSimple avec la mise à jour Update 2 installée.

Le service StorSimple Manager est une extension dans le portail Azure Classic qui vous permet de gérer votre solution StorSimple à partir d’une seule interface Web. Outre la gestion des volumes, vous pouvez utiliser le service StorSimple Manager pour créer et gérer les services StorSimple, afficher et gérer les appareils, afficher les alertes, ainsi que pour afficher et gérer les stratégies de sauvegarde et le catalogue de sauvegarde.

## Types de volume

Les volumes StorSimple peuvent être les suivants :

- **Volumes épinglés localement **: les données de ces volumes restent en permanence sur l’appareil StorSimple local.
- **Volumes hiérarchisés **: les données de ces volumes peuvent se disperser dans le cloud.

Un volume d’archivage est un type de volume hiérarchisé. La grande taille des blocs de déduplication utilisée pour les volumes d’archivage permet à l’appareil de transférer des segments de données plus importants vers le cloud.

Si nécessaire, vous pouvez modifier le type de volume local en volume hiérarchisé, et inversement. Pour plus d’informations, consultez [Modification du type de volume](#change-the-volume-type).

### Volumes épinglés localement

Les volumes épinglés localement sont des volumes entièrement configurés qui ne hiérarchisent pas les données dans le cloud, garantissant ainsi que les données principales restent en local, indépendantes de la connexion au cloud. Les données des volumes épinglés localement ne sont pas dédupliquées ni compressées, mais les instantanés des volumes épinglés localement sont dédupliqués.

Les volumes épinglés localement sont totalement configurés. Vous devez donc disposer de suffisamment d’espace sur votre appareil lorsque vous les créez. Vous pouvez configurer des volumes épinglés localement jusqu’à une taille maximale de 8 To sur un appareil StorSimple 8100 et de 20 To sur un appareil 8600. StorSimple réserve l’espace restant local de l’appareil pour les instantanés, les métadonnées et le traitement des données. Vous pouvez augmenter la taille d’un volume épinglé localement pour qu’il occupe l’espace maximal disponible, mais vous ne pouvez pas réduire la taille d’un volume après sa création.

Si vous créez un volume épinglé localement, l’espace disponible pour la création de volumes hiérarchisés est réduit. L’inverse est également vrai : si vous disposez de volumes hiérarchisés, l’espace disponible pour la création des volumes épinglés localement sera inférieur, dans les limites maximales susmentionnées. Pour plus d’informations sur les volumes locaux, reportez-vous au [forum aux questions sur les volumes épinglés localement](storsimple-local-volume-faq.md).

### Volumes hiérarchisés

Les volumes hiérarchisés sont des volumes alloués dynamiquement dans lesquels les données fréquemment sollicitées restent locales sur l’appareil alors que les données moins fréquemment utilisées sont hiérarchisées automatiquement vers le cloud. L’allocation dynamique est une technologie de virtualisation dans laquelle le stockage disponible semble dépasser les ressources physiques. Plutôt que de réserver un espace de stockage suffisant à l’avance, StorSimple utilise l’allocation dynamique pour allouer juste assez d’espace pour répondre aux besoins actuels. La nature évolutive du stockage cloud simplifie cette approche, car StorSimple peut augmenter ou diminuer le stockage cloud pour répondre aux demandes changeantes.

Si vous utilisez le volume hiérarchisé pour les données d’archivage, l’activation de la case à cocher **Utiliser ce volume pour les données d’archivage auxquelles vous accédez moins souvent** définit la taille de segment de déduplication pour votre volume sur 512 Ko. Si cette option n’est pas activée, le volume à plusieurs niveaux correspondant utilise une taille de segment de 64 Ko. Une grande taille de segment de déduplication permet à l’appareil d’accélérer le transfert des données d’archivage volumineuses vers le cloud.

>[AZURE.NOTE] Les volumes d’archivage créés avec une version antérieure à la mise à jour Update 2 de StorSimple sont importés en tant que volumes hiérarchisés si la case d’archivage est cochée.

### Capacité allouée

Reportez-vous au tableau suivant pour connaître la capacité maximale allouée pour chaque type d’appareil et de volume. (Notez que les volumes épinglés localement ne sont pas disponibles sur un appareil virtuel.)

| | Taille maximale de volume hiérarchisé | Taille maximale de volume épinglé localement |
|-------------|----------------------------|------------------------------------|
| **Appareils physiques** | | |
| 8100 | 64 To | 8 To |
| 8600 | 64 To | 20 To |
| **Appareils virtuels** | | |
| 8010 | 30 To | N/A |
| 8020 | 64 To | N/A |

## Page Volumes

La page **Volumes** vous permet de gérer les volumes de stockage alloués sur l’appareil Microsoft Azure StorSimple pour vos initiateurs (serveurs). Elle comprend la liste des volumes de votre appareil StorSimple.

 ![Page volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volume est constitué d’une série d’attributs :

- **Nom du volume** : nom descriptif qui doit être unique et vous aide à identifier le volume. Ce nom est également utilisé dans les rapports d’analyse lorsque vous filtrez sur un volume particulier.

- **État** : peut être en ligne ou hors connexion. Si un volume est hors connexion, il n’est pas visible pour les initiateurs (serveurs) qui sont autorisés à l’utiliser.

- **Capacité** : spécifie le volume total de données qui peut être stocké par l’initiateur (serveur). Les volumes épinglés localement sont totalement configurés et résident sur l’appareil StorSimple. Les volumes hiérarchisés sont alloués dynamiquement et les données dédupliquées. Si les volumes sont alloués dynamiquement, votre appareil ne préalloue pas de capacité de stockage physique localement ou dans le cloud en fonction de la capacité de volume configurée. La capacité du volume est allouée et utilisée à la demande.

- **Type** : indique si le volume est **Hiérarchisé** (par défaut) ou **Épinglé localement**.

- **Sauvegarde** : indique si une stratégie de sauvegarde par défaut existe pour le volume.

- **Accès** : indique les initiateurs (serveurs) autorisés à accéder à ce volume. Les initiateurs qui ne sont pas membres de l’enregistrement de contrôle d’accès (ACR) associé au volume ne voient pas le volume.

- **Analyse** : indique si un volume est ou non en cours d’analyse. Par défaut, l’analyse est activée au moment de la création du volume. Toutefois, elle est désactivée pour un volume cloné. Pour activer l’analyse d’un volume, suivez les instructions indiquées dans [Analyse d’un volume](#monitor-a-volume).

Suivez les instructions de ce didacticiel pour effectuer les tâches suivantes :

- Ajout d’un volume
- Modification d’un volume
- Modification du type de volume
- Suppression d’un volume
- Mise hors connexion d’un volume
- Analyse d’un volume

## Ajout d’un volume

Vous [avez créé un volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) lors du déploiement de votre solution StorSimple. La procédure d’ajout d’un volume est similaire.

#### Pour ajouter un volume

1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.

2. Dans la liste, sélectionnez un conteneur de volumes, puis double-cliquez dessus pour accéder aux volumes associés au conteneur.

3. Cliquez sur **Ajouter** en bas de la page. L’Assistant Ajouter un volume démarre.

     ![Assistant Ajout de volume, paramètres de base](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. Dans l’Assistant Ajouter un volume, sous **Paramètres de base**, procédez comme suit :

  1. Saisissez un **nom** pour le volume.
  2. Dans la liste déroulante, sélectionnez un **type d'utilisation**. Pour les charges de travail qui requièrent que les données soient disponibles localement sur l’appareil en permanence, sélectionnez **Épinglé localement**. Pour tous les autres types de données, sélectionnez **Hiérarchisé**. (**Hiérarchisé** est la valeur par défaut.)
  3. Si vous avez sélectionné **Hiérarchisé** à l’étape 2, vous pouvez cocher la case **Utiliser ce volume pour les données d’archivage auxquelles vous accédez moins souvent** pour configurer un volume d’archivage.
  4. Indiquez la **capacité allouée** du volume en Go ou To. Consultez la section [Capacité allouée](#provisioned-capacity) pour connaître les tailles maximales pour chaque type d’appareil et de volume. Examinez la **capacité disponible** pour déterminer la quantité de stockage réellement disponible sur votre appareil.

5. Cliquez sur l’icône en forme de flèche ![Icône en forme de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Si vous configurez un volume épinglé localement, le message suivant s’affiche.

    ![Message de modification du type de volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Cliquez de nouveau sur l'icône en forme de flèche ![Icône en forme de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) pour accéder à la page **Paramètres supplémentaires**.

    ![Assistant Ajout de volume, paramètres avancés](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Sous **Paramètres supplémentaires**, ajoutez un enregistrement de contrôle d’accès (ACR) :
  
  1. Dans la liste déroulante, sélectionnez un enregistrement de contrôle d’accès (ACR). Vous pouvez également ajouter un nouvel enregistrement de contrôle d’accès. Les enregistrements de contrôle d’accès déterminent quels hôtes peuvent accéder à vos volumes en faisant correspondre le nom qualifié de l’hôte à celui répertorié dans l’enregistrement. Si vous ne spécifiez pas d’ACR, le message suivant s’affiche.

        ![Specify ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Nous vous recommandons de cocher la case **Autoriser une sauvegarde par défaut pour ce volume**.
  3. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) pour créer le volume avec les paramètres spécifiés.

Le nouveau volume est maintenant prêt à l’emploi.

>[AZURE.NOTE] Si vous créez un volume épinglé localement, puis un autre volume du même type immédiatement après, les tâches de création de volume s’exécutent de façon séquentielle. La première tâche de création de volume doit se terminer pour que la deuxième puisse commencer.

## Modification d’un volume

Modifiez un volume lorsque vous avez besoin d’en augmenter la taille ou de modifier les hôtes qui peuvent y accéder.

> [AZURE.IMPORTANT] 
>
> - Si vous modifiez la taille du volume sur l’appareil, vous devez la modifier sur l’hôte également.
> - Les étapes côté hôte décrites ici concernent Windows Server 2012 (2012R2). Les procédures pour Linux ou d’autres systèmes d’exploitation sont différentes. Consultez les instructions du système d’exploitation de l’hôte pour modifier le volume sur un hôte exécutant un autre système d’exploitation.

#### Pour modifier un volume

1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.

2. Dans la liste, sélectionnez un conteneur de volumes, puis double-cliquez dessus pour afficher les volumes associés au conteneur.

3. Sélectionnez un volume, puis cliquez sur **Modifier** en bas de la page. L’Assistant Modification de volume démarre.

4. Dans l’Assistant Modifier un volume, sous **Paramètres de base**, vous pouvez effectuer les opérations suivantes :

  - Modifiez le **nom**.
  - Convertissez le **type d’utilisation** Épinglé localement en Hiérarchisé ou inversement (pour plus d’informations, voir [Modification du type de volume](#change-the-volume-type)).
  - Augmenter la **capacité allouée**. La **capacité allouée** peut uniquement être augmentée. Vous ne pouvez pas réduire la taille d’un volume après sa création.

5. Sous **Paramètres supplémentaires**, vous pouvez modifier l’ACR, sous réserve que le volume soit hors connexion. Si le volume est en ligne, vous devez d’abord le mettre hors connexion. Reportez-vous à la procédure [Mise hors connexion d’un volume](#take-a-volume-offline) avant de modifier l’enregistrement de contrôle d’accès.

    > [AZURE.NOTE] Vous ne pouvez pas modifier l’option **Activer une sauvegarde par défaut** du volume.

6. Enregistrez vos modifications en cliquant sur l’icône en forme de coche ![icône-coche](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Le portail Azure Classic affiche un message de mise à jour de volume. Il affiche un message de réussite quand le volume a été mis à jour avec succès.

7. Si vous développez un volume, procédez comme suit sur votre ordinateur hôte Windows :

   1. Accédez à **Gestion de l’ordinateur** -> **Gestion des disques**.
   2. Cliquez avec le bouton droit sur **Gestion des disques**, puis sélectionnez **Analyser les disques de nouveau**.
   3. Dans la liste des disques, sélectionnez le volume que vous avez mis à jour, cliquez avec le bouton droit, puis sélectionnez **Étendre le volume**. L’Assistant Étendre le volume démarre. Cliquez sur **Next**.
   4. Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Une fois l’Assistant terminé, le volume doit afficher la taille augmentée.

    >[AZURE.NOTE] Si vous développez un volume épinglé localement, puis un autre volume du même type immédiatement après, les tâches d’expansion de volume s’exécutent de façon séquentielle. La première tâche d’expansion de volume doit se terminer pour que la deuxième puisse commencer.

![Vidéo disponible](./media/storsimple-manage-volumes-u2/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo expliquant comment développer un volume, cliquez [ici](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## Modification du type de volume

Vous pouvez modifier le type de volume hiérarchisé en épinglé localement, et inversement. Toutefois, cette conversion ne doit pas être effectuée fréquemment. Raisons motivant la conversion d’un volume hiérarchisé en volume épinglé localement :

- Assurance de la disponibilité et des performances des données locales.
- Élimination des latences du cloud et des problèmes de connexion au cloud.

En général, il s’agit de petits volumes existants auxquels vous souhaitez accéder fréquemment. Un volume épinglé localement est entièrement configuré lors de sa création. Si vous convertissez un volume hiérarchisé en volume épinglé localement, StorSimple vérifie que vous disposez d’un espace suffisant sur l’appareil avant de démarrer la conversion. Si tel n’est pas le cas, vous recevez une erreur et l’opération est annulée.

> [AZURE.NOTE] Avant de commencer la conversion d’un volume hiérarchisé en volume épinglé localement, vérifiez que vous tenez compte de l’espace requis par les autres charges de travail.

Vous souhaiterez peut-être modifier un volume épinglé localement en volume hiérarchisé si vous avez besoin d’espace supplémentaire pour configurer d’autres volumes. Lorsque vous convertissez le volume épinglé localement en volume hiérarchisé, la capacité disponible sur l’appareil augmente de la taille de la capacité libérée. Si des problèmes de connectivité empêchent la conversion d’un volume de type local en volume de type hiérarchisé, le volume local présente les propriétés d’un volume hiérarchisé tant que la conversion n’est pas terminée. Cela tient au fait que certaines données peuvent avoir été dispersées dans le cloud. Ces données dispersées continuent d’occuper l’espace local sur l’appareil qui ne peut pas être libéré tant que l’opération n’est pas redémarrée ni terminée.

>[AZURE.NOTE] La conversion d’un volume peut prendre un certain temps. Vous ne pouvez pas l’annuler une fois qu’elle a commencé. Le volume reste en ligne pendant la conversion, et vous pouvez effectuer des sauvegardes, mais vous ne pouvez pas développer ni restaurer le volume pendant l’exécution de la conversion.

Conversion d’un volume hiérarchisé en un volume épinglé localement peut affecter les performances du périphérique. En outre, les facteurs suivants peuvent augmenter le temps que nécessaire pour terminer la conversion :

- La bande passante est insuffisante.

- Il n’existe aucune sauvegarde actuelle.

Pour réduire les effets de ces facteurs :

- Passez en revue vos stratégies de limitation de bande passante et assurez-vous qu’une bande passante dédiée de 40 Mbits/s est disponible.
- Planifiez la conversion pendant les heures creuses.
- Effectuez un instantané cloud avant de commencer la conversion.

Si vous convertissez plusieurs volumes (prenant en charge différentes charges de travail), vous devez donner la priorité à la conversion de volume afin que les volumes de priorité élevée soient convertis en premier. Par exemple, vous devez convertir des volumes hébergeant des machines virtuelles ou des volumes avec des charges de travail SQL avant de convertir des volumes avec des charges de travail à partage de fichiers.

#### Pour modifier le type de volume

1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.

2. Dans la liste, sélectionnez un conteneur de volumes, puis double-cliquez dessus pour afficher les volumes associés au conteneur.

3. Sélectionnez un volume, puis cliquez sur **Modifier** en bas de la page. L’Assistant Modification de volume démarre.

4. Dans la page **Paramètres de base**, modifiez le type d'utilisation en sélectionnant le nouveau type dans la liste déroulante **Type d'utilisation**.

    - Si vous définissez le type sur **Épinglé localement**, StorSimple vérifie si sa capacité est suffisante.
    - Si vous définissez le type sur **Hiérarchisé** et que ce volume sera utilisé pour les données d’archivage, cochez la case **Utiliser ce volume pour des données d’archivage moins fréquemment sollicitées**.

        ![Case à cocher Archiver](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Cliquez sur l’icône en forme de flèche ![Icône en forme de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) pour accéder à la page **Paramètres supplémentaires**. Si vous configurez un volume épinglé localement, le message suivant s'affiche.

    ![Message de modification du type de volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Cliquez de nouveau sur l’icône en forme de flèche ![Icône en forme de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) pour continuer.

7. Cliquez sur l’icône de coche ![Icône en forme de coche](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) pour démarrer le processus de conversion. Le portail Azure affiche un message de mise à jour de volume. Il affiche un message de réussite quand le volume a été mis à jour avec succès.

## Mise hors connexion d’un volume

Vous devrez peut-être mettre un volume hors connexion si vous envisagez de le modifier ou de le supprimer. Lorsqu’un volume est hors connexion, il n’est pas disponible pour l’accès en lecture-écriture. Vous devrez mettre le volume hors connexion à la fois sur l’ordinateur hôte et sur l’appareil.

#### Pour mettre un volume hors connexion

1. Assurez-vous que le volume en question n’est pas utilisé avant de le mettre hors connexion.

2. Mettez d’abord le volume hors connexion sur l’ordinateur hôte. Cela élimine tout risque d’endommagement des données sur le volume. Pour les instructions spécifiques, reportez-vous aux instructions du système d’exploitation de l’ordinateur hôte.

3. Une fois que l’ordinateur hôte est hors connexion, mettez le volume hors connexion sur l’appareil en procédant comme suit :

  1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**. L’onglet **Conteneurs de volumes** répertorie dans un tableau tous les conteneurs de volumes associés à l’appareil.
  2. Sélectionnez un conteneur de volumes et cliquez dessus pour afficher la liste de tous les volumes qu’il contient.
  3. Sélectionnez un volume, cliquez sur **Mettre hors connexion**.
  4. Cliquez sur **Oui** lorsque vous êtes invité à confirmer l’opération. Le volume doit maintenant être hors connexion.

    Une fois qu’un volume est hors connexion, l’option **Mettre en ligne** devient disponible.

> [AZURE.NOTE] La commande **Mettre hors connexion** permet de demander à l’appareil de mettre le volume hors connexion. Si les ordinateurs hôtes utilisent toujours le volume, les connexions correspondantes sont rompues et la mise hors connexion du volume n’échoue pas.

## Suppression d’un volume

> [AZURE.IMPORTANT] Vous pouvez supprimer un volume uniquement s’il est hors connexion.

Pour supprimer un volume, procédez comme indiqué ci-dessous.

#### Pour supprimer un volume

1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.

2. Sélectionnez le conteneur de volumes qui contient le volume à supprimer. Cliquez sur le conteneur de volumes pour accéder à la page **Volumes**.

3. Tous les volumes associés à ce conteneur sont affichés sous forme de tableau. Vérifiez l’état du volume à supprimer. Si le volume que vous souhaitez supprimer n’est pas hors connexion, mettez-le d’abord hors connexion, en suivant la procédure [Mise hors connexion d’un volume](#take-a-volume-offline).

4. Une fois le volume hors connexion, cliquez sur **Supprimer** en bas de la page.

5. Cliquez sur **Oui** lorsque vous êtes invité à confirmer l’opération. Le volume est ensuite supprimé et la liste des volumes du conteneur affichée dans la page **Volumes** est mise à jour.

    >[AZURE.NOTE] Si vous supprimez un volume épinglé localement, l'espace disponible pour les nouveaux volumes ne sera peut-être pas mis à jour immédiatement. Le service StorSimple Manager met à jour l'espace local disponible régulièrement. Nous vous recommandons de patienter quelques minutes avant d’essayer de créer le volume.<br> En outre, les tâches de suppression de volume s’exécutent de façon séquentielle si vous supprimez un volume épinglé localement, puis un autre volume du même type immédiatement après. La première tâche de suppression de volume doit se terminer pour que la deuxième puisse commencer.
 
## Analyse d’un volume

L’analyse de volume vous permet de collecter des statistiques sur les E/S d’un volume. L’analyse est activée par défaut pour les 32 premiers volumes que vous créez. L’analyse des volumes supplémentaires est désactivée par défaut. L’analyse des volumes clonés est également désactivée par défaut.

Suivez la procédure ci-dessous pour activer ou désactiver l’analyse d’un volume.

#### Pour activer ou désactiver l’analyse de volume

1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.

2. Sélectionnez le conteneur dans lequel réside le volume, puis cliquez sur le conteneur de volumes pour accéder à la page **Volumes**.

3. Tous les volumes associés à ce conteneur sont affichés sous forme de tableau. Cliquez sur le volume ou le volume cloné pour le sélectionner.

4. En bas de la page, cliquez sur **Modifier**.

5. Dans l’Assistant Modifier un volume, sous **Paramètres de base**, sélectionnez **Activer** ou **Désactiver** dans la liste déroulante **Analyse**.

## Étapes suivantes

- Découvrez comment [cloner un volume StorSimple](storsimple-clone-volume.md).

- Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=AcomDC_0921_2016-->