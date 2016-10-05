<properties
	pageTitle="Dépannage de la sauvegarde de machines virtuelles Azure | Microsoft Azure"
	description="Dépannage de la sauvegarde et de la restauration de machines virtuelles Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="trinadhk;jimpark;"/>


# Dépannage de la sauvegarde de machine virtuelle Azure

> [AZURE.SELECTOR]
- [Coffre Recovery Services](backup-azure-vms-troubleshoot.md)
- [Archivage de sauvegarde](backup-azure-vms-troubleshoot-classic.md)

Vous pouvez résoudre les erreurs rencontrées pendant l’utilisation d’Azure Backup à l’aide des informations figurant dans le tableau ci-dessous.

## Découverte

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Découverte | Échec de la découverte de nouveaux éléments : Microsoft Azure Backup a rencontré une erreur interne. Patientez quelques minutes et recommencez l’opération. | Recommencez le processus de découverte après 15 minutes.
| Découverte | Échec de la découverte de nouveaux éléments : une autre opération de découverte est déjà en cours. Veuillez patienter jusqu’à la fin de l’opération de découverte en cours. | Aucun |

## S’inscrire
| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| S’inscrire | Le nombre de disques de données attachés à la machine virtuelle a dépassé la limite autorisée : Détachez des disques de données de cette machine virtuelle et recommencez l’opération La sauvegarde Azure prend en charge jusqu’à 16 disques de données rattachés à une machine virtuelle Azure à des fins de sauvegarde. | Aucun |
| S’inscrire | Microsoft Azure Backup a rencontré une erreur interne. Veuillez patienter quelques minutes et réessayez l’opération. Si le problème persiste, contactez le support technique Microsoft. | Vous pouvez obtenir cette erreur en raison d’une des configurations de machine virtuelle non prises en charge sur LRS Premium. <br> Les machines virtuelles de stockage Premium peuvent être sauvegardées à l’aide du coffre Recovery Services. [En savoir plus](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| S’inscrire | Échec de l’inscription avec un délai d’expiration de l’opération Installer l’agent | Vérifiez si la version de système d’exploitation de la machine virtuelle est prise en charge. |
| S’inscrire | Échec de l’exécution de la commande - Une autre opération est en cours sur cet élément Attendez que l’opération précédente aboutisse | Aucun |
| S’inscrire | Les machines virtuelles dotées de disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge par la sauvegarde | Aucun |
| S’inscrire | L’agent de machine virtuelle n’est pas présent sur la machine virtuelle. Veuillez installer les logiciels prérequis, l’agent de machine virtuelle, puis relancez l’opération. | [En savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |

## Sauvegarde

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Sauvegarde | Impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané. La sous-tâche de machine virtuelle de capture instantanée a expiré. Consultez le guide de dépannage pour connaître la procédure de résolution de ce problème. | Cette erreur est générée si un problème existe avec l’agent de machine virtuelle ou si l’accès réseau à l’infrastructure Azure est bloqué. En savoir plus sur le [débogage des problèmes d’instantanés de machines virtuelles](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Si l’agent de machine virtuelle n’est pas à l’origine des problèmes, redémarrez la machine virtuelle. Il arrive que l’état incorrect d’une machine virtuelle provoque des problèmes et le redémarrage de la machine virtuelle réinitialise cet « état incorrect » |
| Sauvegarde | Échec de l’opération de sauvegarde avec une erreur interne - Retentez l’opération dans quelques minutes. Si le problème persiste, contactez le support technique Microsoft. | Veuillez vérifier s’il existe un problème temporaire d’accès au stockage de la machine virtuelle. Veuillez vérifier [l’état Azure](https://azure.microsoft.com/status/) pour voir s’il existe des problèmes en cours relatifs au calcul/stockage/réseau dans la région. Veuillez réessayer pour savoir si le problème post-sauvegarde est résolu. |
| Sauvegarde | Impossible d’effectuer l’opération car la machine virtuelle n’existe plus. | Impossible d’effectuer la sauvegarde car la machine virtuelle configurée pour la sauvegarde a été supprimée. Arrêtez les autres sauvegardes en accédant à la vue Éléments protégés, sélectionnez l’élément protégé, puis cliquez sur Arrêter la protection. Vous pouvez conserver les données en sélectionnant l’option Conserver les données de sauvegarde. Vous pouvez réactiver ultérieurement la protection de cette machine virtuelle en cliquant sur Configurer la protection dans la vue des éléments protégés.|
| Sauvegarde | Échec de l’installation de l’extension Azure Recovery Services sur l’élément sélectionné - L’agent VM est un composant requis pour l’extension Azure Recovery Services. Installez d’abord l’agent Azure VM, puis recommencez l’opération d’inscription. | <ol> <li>Vérifiez si l’agent de machine virtuelle a été installé correctement. <li>Vérifiez que l’indicateur de la configuration de la machine virtuelle est défini correctement.</ol> [En savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |
| Sauvegarde | Échec de l’exécution de la commande - Une autre opération est en cours sur cet élément. Attendez que l’opération précédente aboutisse, puis réessayez. | Une sauvegarde ou un travail de restauration existant pour la machine virtuelle est en cours d’exécution, et aucun nouveau travail ne peut être démarré pendant l’exécution d’un travail existant. |
| Sauvegarde | L’installation de l’extension a échoué en renvoyant l’erreur « COM+ n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator | Cela signifie généralement que le service COM+ ne fonctionne pas. Pour résoudre ce problème, contactez le support Microsoft. |
| Sauvegarde | L’opération d’instantané a échoué, en renvoyant l’erreur d’opération VSS « Ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur à partir du panneau de configuration. | Désactivez BitLocker pour tous les lecteurs sur l’ordinateur virtuel et vérifiez si le problème VSS est résolu |
| Sauvegarde | Les machines virtuelles dotées de disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge par la sauvegarde | Aucun |
| Sauvegarde | Machine virtuelle Azure introuvable | Cela se produit lorsque la machine virtuelle principale est supprimée. Cependant, la stratégie de sauvegarde continue de rechercher une machine virtuelle pour effectuer la sauvegarde. Pour corriger cette erreur : <ol><li>Recréez la machine virtuelle avec le même nom et le même nom de groupe de ressources [nom du service cloud] <br>(OU) <li> Désactivez la protection de cette machine virtuelle pour empêcher le déclenchement des autres sauvegardes. </ol> |
| Sauvegarde | L’agent de machine virtuelle n’est pas présent sur la machine virtuelle. Veuillez installer les logiciels prérequis, l’agent de machine virtuelle, puis relancez l’opération. | [En savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |

## Travaux
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Annuler le travail | L’annulation n’est pas prise en charge pour ce type de tâche. Veuillez patienter jusqu’à ce que le travail soit terminé. | Aucun |
| Annuler le travail | Le travail n’est pas dans un état annulable. Veuillez patienter jusqu’à ce que le travail soit terminé. <br>OU<br> Le travail sélectionné n’est pas dans un état annulable. Veuillez patienter jusqu’à ce que le travail soit terminé.| Selon toute probabilité, le travail est presque terminé ; veuillez patienter jusqu’à ce que le travail soit terminé. |
| Annuler le travail | Impossible d’annuler le travail, car il n’est pas en cours. L’annulation est prise en charge uniquement pour les travaux en cours. Veuillez tenter l’annulation de la tâche en cours d’exécution. | Cet événement est dû à un état provisoire. Attendez une minute et relancez l’opération d’annulation |
| Annuler le travail | Impossible d’annuler le travail. Veuillez patienter jusqu’à ce que le travail soit terminé. | Aucun |


## Restauration
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restauration | Échec de la restauration avec une erreur interne du cloud | <ol><li>Le service cloud sur lequel vous essayez d’effectuer la restauration est configuré avec des paramètres DNS. Vous pouvez vérifier <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si une adresse est configurée, cela signifie que des paramètres DNS sont configurés.<br> <li>Le service cloud vers lequel vous essayez de restaurer est configuré avec une adresse IP réservée et les machines virtuelles existantes dans le service cloud sont à l’état Arrêté.<br>Vous pouvez voir si un service cloud utilise des adresses IP réservées à l’aide des applets de commande Powershell suivantes :<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Vous essayez de restaurer une machine virtuelle avec les configurations réseau spéciales suivantes dans le même service cloud. <br>- Machines virtuelles sous configuration de l’équilibreur de charge (interne et externe)<br>- Machines virtuelles avec plusieurs adresses IP réservées<br>- Machines virtuelles avec plusieurs cartes réseau<br>Sélectionnez un nouveau service cloud dans l’interface utilisateur ou reportez-vous aux [considérations relatives à la restauration](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) pour les machines virtuelles avec des configurations réseau spéciales</ol> |
| Restauration | Le nom DNS sélectionné est déjà utilisé. Veuillez spécifier un autre nom DNS et réessayez. | Le nom DNS fait référence au nom du service cloud (se termine généralement par .cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un autre nom de machine virtuelle pendant la restauration. <br><br> Cette erreur ne s’affiche que pour les utilisateurs du portail Azure. L’opération de restauration via PowerShell se déroule correctement, car elle ne fait que restaurer les disques et ne crée pas de machine virtuelle. L’erreur se rencontre lorsque la machine virtuelle est explicitement créée par vos soins après l’opération de restauration du disque. |
| Restauration | La configuration de réseau virtuel spécifiée n’est pas correcte. Veuillez indiquer une autre configuration de réseau virtuel et réessayez. | Aucun |
| Restauration | Le service cloud spécifié utilise une adresse IP réservée, ce qui ne correspond pas à la configuration de machine virtuelle en cours de restauration. Veuillez spécifier un autre service cloud n’utilisant pas l’adresse IP réservée ou choisir un autre point de restauration. | Aucun |
| Restauration | Le service cloud a atteint la limite du nombre de points de terminaison d’entrée. Recommencez l’opération en spécifiant un autre service cloud ou en utilisant un point de terminaison existant. | Aucun |
| Restauration | Le coffre de stockage et le compte de stockage cible se trouvent dans deux régions différentes. Vérifiez que le compte de stockage spécifié dans l’opération de restauration se trouve dans la même région Azure que le coffre de sauvegarde. | Aucun |
| Restauration | Le compte de stockage spécifié pour l’opération de restauration n’est pas pris en charge. Seuls les comptes de stockage de base/Standard avec des paramètres de réplication redondants au niveau géographique ou local sont pris en charge. Veuillez sélectionner un compte de stockage pris en charge | Aucun |
| Restauration | Le type de compte de stockage spécifié pour l’opération de restauration n’est pas en ligne. Assurez-vous que le compte de stockage spécifié dans l’opération de restauration est en ligne | Cela peut se produire cas d’erreur temporaire dans le stockage Azure ou en cas de panne. Veuillez choisir un autre compte de stockage. |
| Restauration | Le quota de groupe de ressources a été atteint. Veuillez supprimer certains groupes de ressources du portail Azure ou contactez le support Azure pour étendre les limites. | Aucun |
| Restauration | Le sous-réseau sélectionné n’existe pas. Veuillez sélectionner un sous-réseau qui existe | Aucun |


## Stratégie
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Créer une stratégie | Impossible de créer la stratégie. Veuillez réduire les choix en matière de rétention pour poursuivre la configuration de la stratégie. | Aucun |


## Agent VM

### Configuration de l’agent de machine virtuelle
En règle générale, l’agent de machine virtuelle est déjà présent dans les machines virtuelles qui sont créées à partir de la galerie Azure. Cependant, les machines virtuelles qui sont migrées à partir de centres de données locaux n’ont pas d’agent de machine virtuelle. Pour ces machines virtuelles, l’agent de machine virtuelle doit être installé de manière explicite. Pour en savoir plus, consultez la page [Installation de l’agent de machine virtuelle sur une machine virtuelle existante](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Pour les machines virtuelles Windows :

- Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin de privilèges d’administrateur pour terminer l’installation.
- [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.

Pour les machines virtuelles Linux :

- Installez l’[agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de github.
- [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.


### Mise à jour de l’agent de machine virtuelle
Pour les machines virtuelles Windows :

- La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Toutefois, vous devez vous assurer qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle.

Pour les machines virtuelles Linux :

- Suivez les instructions dans la rubrique [Mise à jour de l’agent Linux Azure sur une machine virtuelle vers la dernière version de Github](../virtual-machines/virtual-machines-linux-update-agent.md).


### Validation de l’installation de l’agent de machine virtuelle
Pour vérifier la version de l’agent de machine virtuelle sur les machines virtuelles Windows :

1. Ouvrez une session sur la machine virtuelle Azure et accédez au dossier *C:\\WindowsAzure\\Packages*. Le fichier WaAppAgent.exe doit être présent.
2. Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure

<!---HONumber=AcomDC_0921_2016-->