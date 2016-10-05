<properties
   pageTitle="Surveillance des sauvegardes de machines virtuelles déployées via Resource Manager | Microsoft Azure"
   description="Suivre les événements et les alertes des sauvegardes d’une machine virtuelle déployée via Resource Manager. Envoyer un e-mail en fonction des alertes."
   services="backup"
   documentationCenter="dev-center-name"
   authors="markgalioto"
   manager="cfreeman"
   editor=""/>

<tags
ms.service="backup"
ms.workload="storage-backup-recovery"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/25/2016"
ms.author="trinadhk; giridham;"/>

# Suivez les alertes des sauvegardes de machines virtuelles Azure

Les alertes sont des indications du service indiquant qu’un seuil d’événement a été atteint ou dépassé. Le fait de prendre connaissance d’un problème dès qu’il se produit peut être indispensable pour limiter les dépenses. Les alertes ne sont généralement pas planifiées. Il est donc utile de savoir aussi tôt possible lorsque des alertes sont générées. Par exemple, lorsqu’un travail de sauvegarde ou de restauration échoue, une alerte se produit dans les cinq minutes suivant l’échec. Dans le tableau de bord du coffre, la vignette Alertes de sauvegarde affiche des événements de niveaux Critique et Avertissement. Dans les paramètres des alertes de sauvegarde, vous pouvez afficher tous les événements. Mais que faire si une alerte se produit lorsque vous travaillez sur un autre problème ? Si vous ne savez pas quand l’alerte se produit, il peut s’agir d’un désagrément mineur, ou d’un problème entraînant des pertes de données. Pour vous assurer que les bonnes personnes sont prévenues en cas d’alerte, configurez le service de manière à envoyer des notifications d’alerte par e-mail. Pour plus d’informations sur la configuration des notifications par e-mail, consultez [Configurer les notifications](backup-azure-monitor-vms.md#configure-notifications).

## Comment puis-je trouver des informations sur les alertes ?

Pour afficher des informations sur l’événement qui a généré l’alerte, vous devez ouvrir le panneau Alertes de sauvegarde. Il existe deux manières d’ouvrir le panneau Alertes de sauvegarde : soit à partir de la vignette Alertes de sauvegarde dans le tableau de bord du coffre, soit à partir du panneau Alertes et événements.

Pour ouvrir le panneau Alertes de sauvegarde à partir de la vignette Alertes de sauvegarde :

- Sur la vignette **Alertes de sauvegarde** dans le tableau de bord du coffre, cliquez sur **Critique** ou **Avertissement** pour afficher les événements opérationnels pour ce niveau de gravité.

    ![Vignette Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts-tile.png)


Pour ouvrir le panneau Alertes de sauvegarde à partir du panneau Alertes et événements :

1. À partir du tableau de bord du coffre, cliquez sur **Tous les paramètres**. ![Bouton Tous les paramètres](./media/backup-azure-monitor-vms/all-settings-button.png)

2. Dans le panneau **Paramètres**, cliquez sur **Alertes et événements**. ![Bouton Alertes et événements](./media/backup-azure-monitor-vms/alerts-and-events-button.png)

3. Sur le panneau **Alertes et événements**, cliquez sur **Alertes de sauvegarde**. ![Bouton Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts.png)

    Le panneau **Alertes de sauvegarde** s’ouvre et affiche les alertes filtrées.

    ![Vignette Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts-critical.png)

4. Pour afficher des informations détaillées sur une alerte spécifique, cliquez sur l’alerte de votre choix dans la liste des événements pour ouvrir son panneau **Détails**.

    ![Détail sur l’événement](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Pour personnaliser les attributs affichés dans la liste, consultez [Afficher les attributs d’événement supplémentaires](backup-azure-monitor-vms.md#view-additional-event-attributes).

## Configurer les notifications

 Vous pouvez configurer le service de manière à envoyer des notifications par e-mail pour les alertes qui se sont produites au cours de la dernière heure, ou lorsque des événements particuliers se produisent.

Pour configurer des notifications par e-mail pour les alertes

1. Dans le menu Alertes de sauvegarde, cliquez sur **Configurer des notifications**.

    ![Menu Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Le panneau Configurer les notifications s’ouvre.

    ![Panneau Configurer les notifications](./media/backup-azure-monitor-vms/configure-notifications.png)

2. Dans le panneau Configurer les notifications, pour les notifications par e-mail, cliquez sur **Activé**.

    Les boîtes de dialogue Destinataires et Gravité comportent une étoile, car ces informations sont requises. Fournissez au moins une adresse e-mail et sélectionnez au moins un niveau de gravité.

3. Dans la boîte de dialogue **Destinataires (e-mail)**, saisissez les adresses e-mail des personnes devant recevoir les notifications. Utilisez le format : username@domainname.com. Séparez les adresses e-mail par des point-virgule (;).

4. Dans la zone **Notification**, choisissez **Par alerte** pour envoyer une notification lorsque l’alerte indiquée se produit, ou **Synthèse horaire** pour envoyer un résumé de la dernière heure.

5. Dans la boîte de dialogue **Gravité**, sélectionnez un ou plusieurs niveaux pour lesquels vous voulez envoyer des notifications par e-mail.

6. Cliquez sur **Save**.
### Quels sont les types d’alertes disponibles pour la sauvegarde des machines virtuelles Azure IaaS ?
| Niveau d’alerte | Alertes envoyées |
| ------------- | ------------- |
| Critique | Échec de sauvegarde, échec de récupération |
| Avertissement | Aucun |
| Informations | Aucun | 

### Existe-t-il des situations lors desquelles un e-mail n’est pas envoyé même si les notifications sont configurées ?

Il existe des situations lors desquelles une alerte n’est pas envoyée, même si les notifications ont été correctement configurées, afin de réduire le nombre d’alertes. Ces situations sont les suivantes :

- Si les notifications sont configurées sur une base horaire et qu’une alerte est déclenchée et résolue dans l’heure.
- Si le travail est annulé.
- Si un travail de sauvegarde est déclenché et échoue, et si un autre travail de sauvegarde est en cours.
- Si un travail de sauvegarde planifiée pour une machine virtuelle Resource Manager démarre, mais que celle-ci n’existe plus.

## Personnaliser l’affichage des événements

Le paramètre **Journaux d’audit** est fourni avec un ensemble prédéfini de filtres et de colonnes affichant des informations sur les événements opérationnels. Vous pouvez personnaliser l’affichage, afin que lorsque le panneau **Événements** s’ouvre, il affiche les informations souhaitées.

1. Dans le [tableau de bord du coffre](./backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), recherchez **Journaux d’audit** et cliquez dessus pour ouvrir le panneau **Événements**.

    ![Journaux d’audit](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Le panneau **Événements** s’ouvre sur les événements opérationnels filtrés pour le coffre actif.

    ![Filtre des journaux d’audit](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    Le panneau affiche la liste des événements critiques, erreurs, avertissements et informations qui se sont produits au cours de la semaine passée. L’intervalle de temps est une valeur par défaut définie dans **Filtre**. Le panneau **Événements** affiche également un graphique à barres qui retrace le déroulement des événements. Si vous ne souhaitez pas voir ce graphique, accédez au menu **Événements** et cliquez sur **Masquer le graphique** pour désactiver le graphique. L’affichage par défaut des événements affiche les informations opération, niveau, état, ressources et heure. Pour plus d’informations sur l’affichage d’attributs d’événement supplémentaires, consultez la section [Développer les informations sur les événements](backup-azure-monitor-vms.md#view-additional-event-attributes).

2. Pour obtenir plus d’informations sur un événement opérationnel, dans la colonne **Opération**, cliquez sur un événement opérationnel pour ouvrir son panneau. Le panneau contient des informations détaillées sur les événements. Les événements sont regroupés par leur identifiant de corrélation et une liste des événements qui se sont produits dans l’intervalle de temps.

    ![Détails de l'opération](./media/backup-azure-monitor-vms/audit-logs-details-window.png)

3. Pour afficher des informations détaillées sur un événement particulier, cliquez sur l’événement de votre choix dans la liste des événements pour ouvrir le panneau **Détails** correspondant.

    ![Détail sur l’événement](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    Les informations au niveau de l’événement sont répertoriées au fur et à mesure de leur disponibilité. Si vous préférez afficher autant d’informations sur chaque événement et que vous voulez ajouter ce niveau de détail au panneau **Événements**, consultez la section [Développer les informations sur les événements](backup-azure-monitor-vms.md#view-additional-event-attributes).


## Personnaliser le filtre d’événements
Utilisez **Filtre** pour ajuster ou choisir les informations qui apparaissent dans un panneau spécifique. Pour filtrer les informations sur l’événement :

1. Dans le [tableau de bord du coffre](./backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), recherchez **Journaux d’audit** et cliquez dessus pour ouvrir le panneau **Événements**.

    ![Journaux d’audit](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Le panneau **Événements** s’ouvre sur les événements opérationnels filtrés pour le coffre actif.

    ![Filtre des journaux d’audit](./media/backup-azure-monitor-vms/audit-logs-filter.png)

2. Dans le menu **Événements**, cliquez sur **Filtre** pour ouvrir ce panneau.

    ![ouvrir le panneau Filtre](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)

3. Dans le panneau **Filtre**, ajustez les filtres **Niveau**, **Intervalle de temps** et **Appelant**. Les autres filtres ne sont pas disponibles dans la mesure où ils ont été définis pour fournir des informations actuelles concernant le coffre Recovery Services.

    ![Détails de la requête de journaux d’audit](./media/backup-azure-monitor-vms/filter-blade.png)

    Vous pouvez spécifier le **Niveau** de l’événement : critique, erreur, avertissement ou information. Vous pouvez combiner plusieurs niveaux d’événements, mais vous devez sélectionner au moins un niveau. Activez ou désactivez le niveau. Le filtre **Intervalle de temps** vous permet de spécifier la durée de collecte des événements. Si vous utilisez un intervalle de temps personnalisé, vous pouvez définir les heures de début et de fin.

4. Une fois que vous êtes prêt à interroger les journaux d’opérations à l’aide de votre filtre, cliquez sur **Mettre à jour**. Les résultats s’affichent dans le panneau **Événements**.

    ![Détails de l'opération](./media/backup-azure-monitor-vms/edited-list-of-events.png)


### Afficher des attributs d’événements supplémentaires
À l’aide du bouton **Colonnes**, vous pouvez activer des attributs d’événement supplémentaires à afficher dans la liste sur le panneau **Événements**. La liste par défaut des événements affiche les informations opération, niveau, état, ressources et heure. Pour activer des attributs supplémentaires :

1. Sur le panneau **Événements**, cliquez sur **Colonnes**.

    ![Colonnes ouvertes](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    Le panneau **Choisir les colonnes** s’ouvre.

    ![Panneau Colonnes](./media/backup-azure-monitor-vms/columns-blade.png)

2. Pour sélectionner l’attribut, cliquez sur sa case à cocher. La case à cocher se coche et se décoche.

3. Cliquez sur **Réinitialiser** pour réinitialiser la liste des attributs dans le panneau **Événements**. Après avoir ajouté ou supprimé des attributs de la liste, utilisez **Réinitialiser** pour afficher la nouvelle liste des attributs de l’événement.

4. Cliquez sur **Mettre à jour** pour mettre à jour les données des attributs de l’événement. Le tableau suivant fournit des informations sur chaque attribut.

| Nom de la colonne |Description|
| -----------------|-----------|
| Opération|Nom de l’opération|
| Niveau|Le niveau de l’opération, dont les valeurs peuvent être : information, avertissement, erreur ou critique|
|État|État descriptif de l’opération|
|Ressource|URL qui identifie la ressource ; également connu sous le nom d’identifiant de ressource|
|Time|Heure, mesurée à partir de l’heure actuelle, à laquelle l’événement s’est produit|
|Appelant|Élément ou personne ayant déclenché l’événement ; il peut s’agir du système ou d’un utilisateur|
|Timestamp|L’heure à laquelle l’événement a été déclenché|
|Groupe de ressources|Le groupe de ressources associé|
|Type de ressource|Le type de ressource interne utilisé par Resource Manager|
|Identifiant d’abonnement|L’identifiant d’abonnement associé|
|Catégorie|Catégorie de l’événement|
|ID de corrélation :|Identifiant courant des événements connexes|



## Utiliser PowerShell pour personnaliser les alertes
Vous pouvez obtenir des notifications d’alerte personnalisées pour les travaux du portail. Pour cela, définissez des règles d’alerte basées sur PowerShell sur les événements de journaux des opérations. Utilisez *PowerShell version 1.3.0 ou ultérieure*.

Pour définir des notifications personnalisées pour signaler les échecs de sauvegarde, utilisez une commande ressemblant au script suivant :

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId** : vous pouvez obtenir le ResourceId à partir des journaux d’audit. L’élément ResourceId est une URL fournie dans la colonne Ressource des journaux d’opérations.

**OperationName** : OperationName est au format « Microsoft.RecoveryServices/recoveryServicesVault/*EventName* » où *EventName* peut être :<br/>
- Register <br/>
- Unregister <br/>
- ConfigureProtection <br/>
- Backup <br/>
- Restore <br/>
- StopProtection <br/>
- DeleteBackupData <br/>
- CreateProtectionPolicy <br/>
- DeleteProtectionPolicy <br/>
- UpdateProtectionPolicy <br/>

**État** : les valeurs prises en charge sont Démarré, Réussi ou Échec.

**ResourceGroup** : groupe de ressources auquel appartient la ressource. Vous pouvez ajouter la colonne Groupe de ressources aux journaux générés. Le groupe de ressources représente l’un des types d’informations disponibles sur les événements.

**Nom** : nom de la règle d’alerte.

**CustomEmail** : spécifiez l’adresse e-mail personnalisée à laquelle vous voulez envoyer une notification d’alerte

**SendToServiceOwners** : cette option envoie des notifications d’alerte à tous les administrateurs et coadministrateurs de l’abonnement. Elle peut être utilisée dans l’applet de commande **New-AzureRmAlertRuleEmail**.

### Limitations sur les alertes
Les alertes basées sur des événements sont soumises aux limitations suivantes :

1. Des alertes sont déclenchées sur toutes les machines virtuelles du coffre Recovery Services. Vous ne pouvez pas personnaliser l’alerte pour un sous-ensemble de machines virtuelles à l’intérieur d’un coffre Recovery Services.
2. Cette fonctionnalité est en version préliminaire. [En savoir plus](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. Les alertes sont envoyées à partir de l’adresse « alerts-noreply@mail.windowsazure.com ». Actuellement, vous ne pouvez pas modifier l’expéditeur de courrier électronique.


## Étapes suivantes

Les journaux d’événements activent l’assistance post mortem et d’audit des opérations de sauvegarde. Les opérations suivantes sont consignées :

- S’inscrire
- Annuler l’inscription
- Configurer la protection
- Sauvegarde (sauvegarde planifiée et à la demande)
- Restauration
- Arrêter la protection
- Supprimer les données de sauvegarde
- Add policy
- Supprimer la stratégie
- Mettre à jour la stratégie
- Annuler le travail

Pour une explication générale des événements, des opérations et des journaux d’audit dans les services Azure, consultez l’article [Afficher les événements et les journaux d’audit](../azure-portal/insights-debugging-with-events.md).

Pour plus d’informations sur la manière de recréer une machine virtuelle à partir d’un point de récupération, consultez [Restauration de machines virtuelles dans Azure](backup-azure-restore-vms.md). Pour plus d’informations sur la protection de vos machines virtuelles, consultez [Premier aperçu : sauvegarder les machines virtuelles ARM dans un archivage de Recovery Services](backup-azure-vms-first-look-arm.md). Apprenez-en plus sur les tâches de gestion pour les sauvegardes de machines virtuelles dans l’article [Gérer les sauvegardes des machines virtuelles Azure](backup-azure-manage-vms.md).

<!---HONumber=AcomDC_0921_2016-->