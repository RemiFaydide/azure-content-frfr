<properties
	pageTitle="Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels | Microsoft Azure"
	description="Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor="tysonn"
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;genli;robinsh"/>

# Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels

## Résumé
Il est possible que vous receviez des erreurs quand vous essayez de supprimer le compte de stockage Azure, le conteneur ou le disque dur virtuel dans le [portail Azure](https://portal.azure.com/) ou le [portail Azure Classic](https://manage.windowsazure.com/). Ces problèmes peuvent être causés par les circonstances suivantes :

-	Lorsque vous supprimez une machine virtuelle, le disque et le disque dur virtuel ne sont pas automatiquement supprimés. Cela peut être la cause de l'échec de la suppression du compte de stockage. Nous ne supprimons pas le disque pour que vous puissiez l’utiliser pour monter une autre machine virtuelle.

-	Il existe toujours un bail sur un disque ou sur l’objet blob associé au disque.

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou sur Twitter (@AzureSupport). Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir une assistance** sur le site du [support Azure](https://azure.microsoft.com/support/options/).

## Résolution :
Pour résoudre les problèmes les plus courants, essayez la méthode suivante :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/).
2. Sélectionnez **MACHINE VIRTUELLE** > **DISQUES**.

	![Image de disques sur des machines virtuelles sur le portail Azure Classic.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Recherchez les disques associés au compte de stockage, au conteneur ou au disque dur virtuel à supprimer. En vérifiant l’emplacement du disque, vous trouverez le compte de stockage, le conteneur et le disque dur virtuel associés.

	![Image qui affiche des informations d'emplacement sur les disques sur le portail Azure Classic](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Vérifiez qu’aucune machine virtuelle n’est indiquée dans le champ **Attaché à** des disques, puis supprimez les disques.

 	> [AZURE.NOTE] Si un disque est attaché à une machine virtuelle, vous ne pouvez pas le supprimer. Les disques sont détachés de façon asynchrone d’une machine virtuelle supprimée. L’effacement de ce champ peut prendre quelques minutes après la suppression de la machine virtuelle.

5. Sélectionnez **MACHINE VIRTUELLE** > **IMAGES**, puis supprimez les images associées au compte de stockage, au conteneur ou au disque dur virtuel.

	Ensuite, réessayez de supprimer le compte de stockage, le conteneur ou le disque dur virtuel.

> [AZURE.WARNING] Veillez à sauvegarder tout ce que vous souhaitez conserver avant de supprimer le compte. Il n’est pas possible de restaurer un compte de stockage supprimé ou de récupérer son contenu avant la suppression. Ceci vaut également pour toutes les ressources du compte : dès que vous supprimez un disque dur virtuel, un objet blob, une table, une file d’attente ou un fichier, la suppression est irréversible. Vérifiez que la ressource n’est pas en cours d’utilisation.

## Problèmes courants

La section suivante répertorie les erreurs courantes que vous pourriez recevoir quand vous tentez de supprimer les comptes de stockage Azure, les conteneurs ou les disques durs virtuels.

### Scénario 1 : Impossible de supprimer un compte de stockage

Lorsque vous accédez au compte de stockage dans le [portail Azure](https://portal.azure.com/) ou dans le [portail Azure Classic](https://manage.windowsazure.com/) et que vous sélectionnez **Supprimer**, le message d'erreur suivant peut s’afficher :

**Dans le portail Azure **:

*Échec de suppression du compte de stockage <vm-storage-account-name>. Impossible de supprimer le compte de stockage <vm-storage-account-name> : Storage account <vm-storage-account-name> contient des images et/ou des disques actifs. Supprimez ces images et/ou disques avant de supprimer ce compte de stockage. ».*

**Dans le portail Azure Classic** :

*Le compte de stockage <vm-storage-account-name> contient des images et/ou des disques actifs, par exemple, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Supprimez ces images et/ou disques avant de supprimer ce compte de stockage.*

Il est également possible que vous obteniez cette erreur :

**Dans le portail Azure** :

*Le compte de stockage <vm-storage-account-name> contient 1 ou plusieurs conteneurs avec une image active et/ou des artefacts de disque. Supprimez ces artefacts du dépôt d’images avant de supprimer ce compte de stockage*.

**Dans le portail Azure Classic** :

*Échec d’envoi : le compte de stockage <vm-storage-account-name> contient 1 ou plusieurs conteneurs avec une image active et/ou des artefacts de disque. Supprimez ces artefacts du dépôt d’images avant de supprimer ce compte de stockage. Quand vous essayez de supprimer un compte de stockage auquel sont associés des disques toujours actifs, un message vous informe que des disques actifs doivent être supprimés*.

### Scénario 2 : Impossible de supprimer un conteneur

Quand vous essayez de supprimer le conteneur de stockage, il est possible que vous rencontriez l’erreur suivante :

*Échec de la suppression du conteneur de stockage <container name>. Erreur : « Il existe actuellement un bail sur le conteneur et aucun ID de bail n’a été spécifié dans la demande »*.

### Scénario 3 : Impossible de supprimer un disque dur virtuel

Après avoir supprimé une machine virtuelle, puis tenté de supprimer les objets blob des disques durs virtuels associés, il est possible que vous receviez le message suivant :

*Échec de la suppression de l’objet blob « path/XXXXXX-XXXXXX-os-1447379084699.vhd ». Erreur : « Il existe actuellement un bail sur l’objet blob et aucun ID de bail n’a été spécifié dans la demande »*.

## À propos de l’état Arrêté (Désalloué)

Les machines virtuelles créées dans le modèle de déploiement classique et conservées auront le statut **Arrêté (Désalloué)** soit sur le [portail Azure](https://portal.azure.com/), soit sur le [portail Azure Classic](https://manage.windowsazure.com/).

**Portail Azure Classic** :

![Statut Arrêté (désalloué) pour les machines virtuelles sur le portail Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Portail Azure** :

![Statut Arrêté (désalloué) pour les machines virtuelles sur le portail Azure Classic.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Le statut « Arrêté (désalloué) » libère les ressources de l'ordinateur (processeur, mémoire et réseau). Les disques, cependant, sont toujours conservés de façon que l'utilisateur puisse rapidement recréer la machine virtuelle si nécessaire. Ces disques sont créés sur les disques durs virtuels sauvegardés par Azure Storage. Le compte de stockage contient ces disques durs virtuels et les disques ont des baux sur ces disques durs virtuels.

## Étapes suivantes

- [Suppression d'un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Interruption du bail verrouillé du stockage d’objets blob dans Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0921_2016-->