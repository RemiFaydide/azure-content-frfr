<properties
	pageTitle="Gérer les serveurs et les coffres Azure Recovery Services | Microsoft Azure"
	description="Ce didacticiel vous apprend à gérer les serveurs et les coffres Azure Recovery Services."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="jimpark; markgal"/>


# Surveiller et gérer les serveurs et les coffres Azure Recovery services pour les ordinateurs Windows

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](backup-azure-manage-windows-server.md)
- [Classique](backup-azure-manage-windows-server-classic.md)

Cet article offre une vue d’ensemble des tâches de gestion des sauvegardes disponibles via le portail de gestion Azure et l’agent Microsoft Azure Backup.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] le modèle de déploiement classique.

## Tâches du portail de gestion

### Accéder à vos coffres Recovery Services

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.

2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste est filtrée. Cliquez sur **Coffres Recovery Services**.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-manage-windows-server/browse-to-rs-vaults.png) <br/>

2. Sélectionnez le nom du coffre que vous souhaitez afficher dans la liste pour ouvrir le panneau du tableau de bord du coffre Recovery Services.

    ![coffre recovery services tableau de bord](./media/backup-azure-manage-windows-server/rs-vault-dashboard.png) <br/>

## Surveiller les travaux et les alertes
Vous pouvez surveiller les travaux et les alertes à partir du tableau de bord du coffre Recovery Services dans lequel s’affichent :

- Les détails des alertes de sauvegarde
- Les fichiers et dossiers, ainsi que les machines virtuelles protégées dans le cloud
- Le stockage total consommé dans Azure
- L’état du travail de sauvegarde

![Tâches du tableau de bord Backup](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Cliquez sur les informations contenues dans chacune de ces mosaïques pour ouvrir le panneau correspondant où vous pourrez gérer les tâches associées.

De haut en bas dans le tableau de bord :

- Panneau Paramètres : vous donne accès aux tâches de sauvegarde disponibles.
- Panneau Sauvegarde : vous permet de sauvegarder de nouveaux fichiers et dossiers (ou machines virtuelles Azure) dans le coffre Recovery Services.
- Panneau Supprimer : si un coffre Recovery Services n’est plus utilisé, vous pouvez le supprimer pour libérer de l’espace de stockage. L’option Supprimer est disponible uniquement une fois que tous les serveurs protégés ont été supprimés du coffre.

![Tâches du tableau de bord Backup](./media/backup-azure-manage-windows-server/dashboard-tasks.png)
## Alertes relatives aux sauvegardes à l’aide de l’agent de sauvegarde Azure :
| Niveau d’alerte | Alertes envoyées |
| ------------- | ------------- |
| Critique | Échec de sauvegarde, échec de récupération |
| Avertissement | Sauvegarde terminée avec des avertissements (lorsque moins de cent fichiers n’ont pas été sauvegardés du fait de problèmes d’altération et que plus d’un million de fichiers ont été correctement sauvegardés) |
| Informations | Aucun |
## Gérer les alertes de sauvegarde
Cliquez sur la mosaïque **Alertes de sauvegarde** pour ouvrir le panneau **Alertes de sauvegarde** et gérer les alertes.

![Alertes de sauvegarde](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

La mosaïque Alertes de sauvegarde indique :

- le nombre d’alertes critiques non résolues dans les 24 dernières heures
- le nombre d’alertes d’avertissement non résolues dans les 24 dernières heures

Cliquez sur chacun de ces liens pour accéder au panneau **Alertes de sauvegarde** affichant une vue filtrée sur ces alertes (critiques ou d’avertissement).

Dans le panneau Alertes de sauvegarde, vous pouvez :

- Choisir les informations appropriées à inclure dans vos alertes

    ![Choisir les colonnes](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)

- Filtrer les alertes selon leur gravité, leur état et les heures de début/fin

    ![Filtrer les alertes](./media/backup-azure-manage-windows-server/filter-alerts.png)

- Configurer les paramètres de gravité, de fréquence et de destination des notifications, mais aussi activer ou désactiver les alertes

    ![Filtrer les alertes](./media/backup-azure-manage-windows-server/configure-notifications.png)

Si la fréquence de **notification** est définie sur **Par alerte**, le système ne procède à aucun regroupement ou réduction des e-mails. Chaque alerte génère une notification. Il s’agit du paramètre par défaut. Un e-mail de résolution accompagne chaque notification.

Si la fréquence de **notification** est définie sur **Synthèse horaire**, un e-mail est envoyé à l’utilisateur pour lui indiquer que de nouvelles alertes non résolues ont été générées au cours de la dernière heure. Un e-mail de résolution est envoyé à la fin de l’heure.

Des alertes peuvent être envoyées pour les niveaux de gravité suivants :

- critique
- avertissement
- information

Pour désactiver l’alerte, cliquez sur le bouton **Inactivate** (Désactiver) dans le panneau contenant les détails du travail. Lorsque vous cliquez sur Inactivate (Désactiver), vous pouvez ajouter des commentaires pour la résolution de l’alerte.

Pour choisir les colonnes que vous souhaitez voir apparaître dans l’alerte, cliquez sur le bouton **Choisir les colonnes**.

>[AZURE.NOTE] Dans le panneau **Paramètres**, vous pouvez gérer les alertes de sauvegarde en sélectionnant **Monitoring and Reports (Surveillance et rapports) > Alertes et événements > Alertes de sauvegarde**, puis en cliquant sur **Filtrer** ou **Configurer les notifications**.

## Gérer les éléments de sauvegarde
Vous pouvez désormais gérer les sauvegardes locales depuis le portail de gestion. Dans la section Sauvegarde du tableau de bord, la mosaïque **Éléments de sauvegarde** affiche le nombre d’éléments de sauvegarde protégés dans le coffre.

Dans la mosaïque Éléments de sauvegarde, cliquez sur **File-Folders** (Dossiers de fichiers).

![Mosaïque Éléments de sauvegarde](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Le panneau Éléments de sauvegarde s’ouvre avec le filtre défini sur File-Folders (Dossiers de fichiers) et affiche chaque élément de sauvegarde spécifique.

![Éléments de sauvegarde](./media/backup-azure-manage-windows-server/backup-item-list.png)

Cliquez sur un élément de sauvegarde spécifique dans la liste pour en afficher les principaux détails.

>[AZURE.NOTE] Dans le panneau **Paramètres**, vous pouvez gérer les fichiers et dossiers en sélectionnant **Éléments protégés > Éléments de sauvegarde**, puis en sélectionnant **File-Folders** (Dossiers de fichiers) dans le menu déroulant.

![Éléments de sauvegarde à partir des paramètres](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## Gérer les travaux de sauvegarde
Les travaux de sauvegarde réalisés en local (lorsque le serveur local sauvegarde vers Azure) et dans Azure s’affichent dans le tableau de bord.

Dans la section Sauvegarde du tableau de bord, la mosaïque Travail de sauvegarde indique le nombre de travaux :

- en cours
- ayant échoué au cours des 24 dernières heures

Pour gérer vos travaux de sauvegarde, cliquez sur la mosaïque **Travaux de sauvegarde** pour ouvrir le panneau Travaux de sauvegarde.

![Éléments de sauvegarde à partir des paramètres](./media/backup-azure-manage-windows-server/backup-jobs.png)

Cliquez sur le bouton **Choisir les colonnes** en haut de la page pour modifier les informations disponibles dans le panneau Travaux de sauvegarde.

Utilisez le bouton **Filtrer** pour sélectionner les fichiers ou les dossiers, ainsi que la sauvegarde de la machine virtuelle Azure.

Si vous ne voyez pas vos fichiers et dossiers sauvegardés, cliquez sur le bouton **Filtrer** en haut de la page et sélectionnez **Fichiers et dossiers** dans le menu Item Type (Type d’élément).

>[AZURE.NOTE] Dans le panneau **Paramètres**, vous pouvez gérer les travaux de sauvegarde en sélectionnant **Monitoring and Reports (Surveillance et rapports) > Travaux > Travaux de sauvegarde**, puis en sélectionnant **File-Folders** (Dossiers de fichiers) dans le menu déroulant.

## Surveiller l’utilisation de la sauvegarde
Dans la section Sauvegarde du tableau de bord, la mosaïque Backup Usage (Utilisation de la sauvegarde) indique le stockage consommé dans Azure. Les données d’utilisation du stockage incluent :
- L’utilisation du stockage cloud LRS associée au coffre
- L’utilisation du stockage cloud GRS associée au coffre

## Serveurs de production
Pour gérer vos serveurs de production, cliquez sur **Paramètres**. Sous Gérer, cliquez sur **Infrastructure de sauvegarde > Serveurs de Production**.

Le panneau Serveurs de Production affiche une liste de tous vos serveurs de production disponibles. Cliquez sur un serveur dans la liste pour afficher les détails correspondants.

![Éléments protégés](./media/backup-azure-manage-windows-server/production-server-list.png)

## Tâches de l’agent Microsoft Azure Backup

## Ouvrir l’agent Azure Backup

Ouvrez **l’agent Microsoft Azure Backup** (vous pouvez le trouver en recherchant *Microsoft Azure Backup* sur votre ordinateur).

![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Dans les **Actions** disponibles à droite de la console de l’agent de sauvegarde, vous pouvez effectuer les tâches de gestion suivantes :

- Inscription du serveur
- Planification d’une sauvegarde
- Sauvegarder maintenant
- Modifier les propriétés

![Actions de la console de l’agent Microsoft Azure Backup.](./media/backup-azure-manage-windows-server/console-actions.png)

>[AZURE.NOTE] Pour **Récupérer des données**, consultez [Restauration de fichiers sur un serveur Windows ou un ordinateur client Windows](backup-azure-restore-windows-server.md).

## Modifier une sauvegarde existante

1. Dans l’agent Microsoft Azure Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)

2. Dans **l’Assistant Planification de sauvegarde**, conservez l’option **Modifier les éléments ou les dates/heures de sauvegarde** sélectionnée, puis cliquez sur **Suivant**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)

3. Si vous souhaitez ajouter ou modifier des éléments, cliquez sur **Ajouter des éléments** dans l’écran **Sélectionner les éléments à sauvegarder**.

    Vous pouvez également définir les **Paramètres d’exclusion** sur cette page de l’Assistant. Si vous souhaitez exclure des fichiers ou des types de fichiers, lisez la procédure permettant d’ajouter des [paramètres d’exclusion](#exclusion-settings).

4. Sélectionnez les fichiers et les dossiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)

5. Spécifiez la **planification de sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Éléments de sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

    >[AZURE.NOTE] La définition de la planification de sauvegarde est décrite en détail dans cet [article](backup-azure-backup-cloud-as-tape.md).

6. Sélectionnez la **Stratégie de rétention** pour la copie de sauvegarde et cliquez sur **Suivant**.

    ![Éléments de sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)

7. Dans l’écran **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.

8. Dès que l’Assistant a terminé la création de la **planification de sauvegarde**, cliquez sur **Fermer**.

    Après la modification de la protection, vous pouvez confirmer le déclenchement correct des sauvegardes en accédant à l’onglet **Travaux** et en vérifiant que les modifications sont répercutées dans les tâches de sauvegarde.

## Activation de la limitation du réseau  
L’agent Azure Backup contient un onglet Limitation qui vous permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.

Pour activer la limitation :

1. Dans **l’agent Backup**, cliquez sur **Modifier les propriétés**.

2. Cochez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-azure-manage-windows-server/throttling-dialog.png)

3. Une fois que vous avez activé la limitation, spécifiez la bande passante autorisée pour le transfert des données de sauvegarde durant les **Heures de travail** et les **Heures chômées**.

    Les valeurs de bande passante, qui démarrent à 512 kilo-octets par seconde, peuvent aller jusqu’à 1 023 mégaoctets par seconde. Vous pouvez également définir le début et la fin des **Heures de travail** et identifier les jours de la semaine considérés comme des jours de travail. Les intervalles de temps situés en dehors des heures de travail sont considérés comme des périodes (heures) chômées.

4. Cliquez sur **OK**.

## Gérer les paramètres d’exclusion

1. Ouvrez **l’agent Microsoft Azure Backup** (vous pouvez le trouver en recherchant *Microsoft Azure Backup* sur votre ordinateur).

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

2. Dans l’agent Microsoft Azure Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)

3. Dans l’Assistant Planification de sauvegarde, conservez l’option **Modifier les éléments ou les dates/heures de sauvegarde** sélectionnée, puis cliquez sur **Suivant**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)

4. Cliquez sur **Paramètres d’exclusion**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)

5. Cliquez sur **Ajouter une exclusion**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)

6. Sélectionnez l’emplacement, puis cliquez sur **OK**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)

7. Ajoutez l’extension de fichier dans le champ **Type de fichier**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Ajout d’une extension .mp3

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Pour ajouter une autre extension, cliquez sur **Ajouter une exclusion** et entrez une autre extension de type de fichier (ajout d’une extension .jpeg).

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)

8. Lorsque vous avez ajouté toutes les extensions, cliquez sur **OK**.

9. Suivez les instructions de l’Assistant Planification de sauvegarde en cliquant sur **Suivant** jusqu’à la **page de Confirmation**, puis cliquez sur **Terminer**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## Forum Aux Questions
**Q1. Le travail de sauvegarde est indiqué comme terminé dans l’agent de sauvegarde Azure, pourquoi cela ne s’affiche pas immédiatement dans le portail ?**

R1. Un délai maximal de 15 minutes est nécessaire pour que l’état du travail de sauvegarde dans l’agent de sauvegarde Azure soit répercuté dans le portail Azure.

**Q2. En cas d’échec d’un travail de sauvegarde, combien de temps faut-il pour déclencher une alerte ?**

R2. Une alerte est générée dans les 5 minutes suivant l’échec de la sauvegarde Azure.

**Q3. Est-il possible qu’aucun e-mail ne soit envoyé alors que les notifications sont activées ?**

R3 Voici les cas pour lesquels la notification ne sera pas envoyée afin de réduire le bruit des alertes :

   - Si les notifications sont configurées sur une base horaire et qu’une alerte est déclenchée et résolue dans l’heure.
   - Si le travail est annulé.
   - Si le travail de sauvegarde secondaire a échoué, car un travail de sauvegarde d’origine est en cours.

## Résolution des problèmes de surveillance<br>
#### Problème : les travaux et les alertes de l’agent de sauvegarde Azure n’apparaissent pas sur le portail.
##### Procédure de résolution :
« OBRecoveryServicesManagementAgent » est utilisé pour envoyer les données des travaux et des alertes au service de sauvegarde Azure. Ouvrez le gestionnaire de tâches et vérifiez si le processus « OBRecoveryServicesManagementAgent » est en cours d’exécution. Il est possible que ce processus ait été bloqué ou arrêté. Si le processus n’est pas en cours d’exécution, parcourez la liste des services dans le Panneau de configuration, puis démarrez ou redémarrez « Agent de gestion Microsoft Azure Recovery Services ». Pour plus d’informations, recherchez les journaux dans : « dossier d’installation de l’agent de sauvegarde Azure »\\Agent Microsoft Azure Recovery Services\\Temp\\GatewayProvider*. <b>Par exemple :</b> C:\\Program Files\\Agent Microsoft Azure Recovery Services\\Temp\\GatewayProvider0.errlog

## Étapes suivantes
- [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
- Pour en savoir plus sur Azure Backup, consultez la [vue d’ensemble d’Azure Backup](backup-introduction-to-azure-backup.md).
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0921_2016-->