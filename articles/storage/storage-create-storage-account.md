<properties
	pageTitle="Création, gestion ou suppression d’un compte de stockage dans le portail Azure | Microsoft Azure"
	description="Créez un compte de stockage, gérez vos clés d’accès au compte ou supprimez un compte de stockage dans le portail Azure. En savoir plus sur les comptes de stockage standard et Premium."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/26/2016"
	ms.author="micurd;robinsh"/>


# À propos des comptes de stockage Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## Vue d'ensemble

Un compte de stockage Azure fournit un espace de noms unique pour stocker vos objets de données Azure Storage et y accéder. Tous les objets d’un compte de stockage sont facturés ensemble en tant que groupe. Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## Facturation du compte de stockage

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé automatiquement pour vous sur le lieu de déploiement si vous ne disposez pas déjà de ce type de compte à cet emplacement. Il n’est donc pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques de machines virtuelles. Le nom du compte de stockage dépend du nom de la machine virtuelle. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Points de terminaison d'un compte de stockage

Chaque objet que vous stockez dans Azure Storage possède une adresse URL unique. Le nom du compte de stockage constitue le sous-domaine de cette adresse. La combinaison sous-domaine/nom du domaine, qui est propre à chaque service, forme un *point de terminaison* pour votre compte de stockage.

Par exemple, si le nom de votre compte de stockage est *mystorageaccount*, les points de terminaison par défaut de votre compte de stockage sont les suivants :

- Service BLOB : http://*mystorageaccount*.blob.core.windows.net

- Service de table : http://*mystorageaccount*.table.core.windows.net

- Service de file d'attente : http://*mystorageaccount*.queue.core.windows.net

- Service de fichiers : http://*mystorageaccount*.file.core.windows.net

> [AZURE.NOTE] Un compte de stockage d’objets blob expose uniquement le point de terminaison de service blob.

L’URL permettant d’accéder à un objet dans un compte de stockage est constituée de l’ajout de l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d’objet blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez aussi configurer un nom de domaine personnalisé à utiliser avec votre compte de stockage. Dans le cas de comptes de stockage classiques, consultez la page [Configurer un nom de domaine personnalisé pour un point de terminaison Blob Storage](storage-custom-domain-name.md) pour plus d’informations. Pour les comptes de stockage Resource Manager, cette fonctionnalité n’a pas encore été ajoutée au [Portail Azure](https://portal.azure.com), mais vous pouvez la configurer avec PowerShell. Pour plus d’informations, référez-vous à l’applet de commande [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx).

## Créez un compte de stockage.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu Hub, sélectionnez **Nouveau** -> **Données et stockage** -> **Compte de stockage**.

3. Entrez un nom pour votre compte de stockage. Pour plus d’informations sur l’utilisation du nom de compte de stockage pour adresser les objets que vous stockez dans Azure Storage, consultez [Points de terminaison d’un compte de stockage](#storage-account-endpoints).

	> [AZURE.NOTE] Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
	>  
	> Le nom de votre compte de stockage doit être unique dans Azure. Le portail Azure indique si le nom du compte de stockage sélectionné est déjà utilisé.

4. Sélectionnez le modèle de déploiement à utiliser : **Resource Manager** ou **Classique**. **Resource Manager** est le modèle de déploiement recommandé. Pour plus d’informations, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).

	> [AZURE.NOTE] Les comptes de stockage d’objets blob peuvent être créés uniquement avec le modèle de déploiement Resource Manager.

5. Sélectionnez le type de compte de stockage : **Usage général** ou **Blob Storage**. Le type par défaut est **Usage général**.

	Si **Usage général** est sélectionné, spécifiez le niveau de performances **Standard** ou **Premium**. Le niveau par défaut est **Standard**. Pour plus d’informations sur les comptes de stockage standard et premium, consultez [Introduction à Microsoft Azure Storage](storage-introduction.md) et [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage.md).

	Si **Blob Storage** est sélectionné, spécifiez le niveau d’accès **Chaud** ou **Froid**. Le niveau par défaut est **Chaud**. Pour plus d’informations, voir [Stockage d’objets blob Azure : niveaux froid et chaud](storage-blob-storage-tiers.md).

6. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS**, **RA-GRS** ou **ZRS**. La valeur par défaut est **RA-GRS**. Pour plus d’informations sur les options de réplication d’Azure Storage, consultez [Réplication Azure Storage](storage-redundancy.md).

7. Sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.

8. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md).

9. Sélectionnez l’emplacement géographique de votre compte de stockage. Pour plus d’informations sur les services disponibles dans chaque région, voir [Régions Azure](https://azure.microsoft.com/regions/#services).

10. Cliquez sur **Créer** pour créer le compte de stockage.

## Gérer votre compte de stockage

### Modifier la configuration de votre compte

Après avoir créé votre compte de stockage, vous pouvez modifier sa configuration, notamment modifier l’option de réplication utilisée pour le compte ou modifier le niveau d’accès pour un compte de stockage d’objets blob. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage, cliquez sur **Tous les paramètres**, puis sur **Configuration** pour afficher et/ou modifier la configuration du compte.

> [AZURE.NOTE] Selon le niveau de performances que vous avez choisi lors de la création du compte de stockage, certaines options de réplication peuvent ne pas être disponibles.

La modification de l’option de réplication entraîne une modification de vos tarifs. Pour plus d’informations, voir [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Pour les comptes de stockage d’objets blob, changer le niveau d’accès peut entraîner des frais, outre la modification des prix appliqués. Pour plus d’informations, voir [Stockage des objets blob Azure - section Tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing).

### Gérer vos clés d’accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

> [AZURE.NOTE] Nous vous recommandons d’éviter de partager vos clés d’accès de stockage avec qui que ce soit. Pour autoriser l'accès aux ressources de stockage sans donner vos clés d'accès, vous pouvez utiliser une *signature d'accès partagé*. Celle-ci donne accès à votre compte à une ressource pendant une durée que vous déterminez et avec les autorisations que vous spécifiez. Pour plus d’informations, consultez [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).

#### Afficher et copier les clés d’accès de stockage

Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, cliquez sur **Tous les paramètres**, puis cliquez sur **Clés d’accès** pour visualiser, copier et régénérer les clés d’accès de votre compte. Le panneau **Clés d’accès** inclut également des chaînes de connexion préconfigurées utilisant vos clés principales et secondaires, que vous pouvez copier pour utilisation dans vos applications.

#### Régénération des clés d'accès de stockage

Nous vous recommandons de modifier périodiquement les clés d’accès à votre compte de stockage pour garantir la sécurité des connexions de stockage. Vous bénéficiez de deux clés d’accès, ce qui vous permet de conserver des connexions au compte de stockage à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

> [AZURE.WARNING] La régénération des clés d’accès peut affecter les services dans Azure, ainsi que vos applications qui dépendent du compte de stockage. Tous les clients qui utilisent la clé d'accès pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Media Services** : si des services de médias sont liés à votre compte de stockage, vous devez resynchroniser les clés d’accès avec ces services après avoir régénéré les clés.

**Applications** : si des applications web ou des services cloud utilisent le compte de stockage, vous perdez les connexions en régénérant les clés, à moins de les substituer.

**Explorateurs de stockage** : si vous utilisez des [applications d’explorateur de stockage](storage-explorers.md), vous devrez probablement mettre à jour la clé de stockage utilisée par ces applications.

Voici le processus de rotation de vos clés d’accès de stockage :

1. Mettez à jour les chaînes de connexion dans votre code d'application pour désigner la clé d'accès secondaire du compte de stockage.

2. Régénérez la clé d'accès principale de votre compte de stockage. Dans le panneau **Clés d’accès**, cliquez sur **Régénérer Key1**, puis sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d’accès principale.

4. Régénérez la clé d’accès secondaire de la même manière.

## Suppression d'un compte de stockage

Pour supprimer un compte de stockage que vous n’utilisez plus, accédez au compte de stockage dans le [portail Azure](https://portal.azure.com), puis cliquez sur **Supprimer**. La suppression d’un compte de stockage supprime l’intégralité du compte, y compris toutes les données qu’il contient.

> [AZURE.WARNING] Il n’est pas possible de restaurer un compte de stockage supprimé ou son contenu avant la suppression. Veillez à sauvegarder tout ce que vous souhaitez conserver avant de supprimer le compte. Ceci vaut également pour toutes les ressources du compte : dès que vous supprimez un objet blob, une table, une file d’attente ou un fichier, la suppression est irréversible.

Pour supprimer un compte de stockage qui est associé à une machine virtuelle Azure, vous devez tout d’abord vous assurer que tous les disques des machines virtuelles ont été supprimés. Si vous ne commencez pas par supprimer les disques des machines virtuelles, vous voyez un message d’erreur semblable au suivant lorsque vous essayez de supprimer votre compte de stockage :

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si le compte de stockage utilise le modèle de déploiement Classic, vous pouvez supprimer le disque de machine virtuelle en procédant comme suit dans le [portail Azure](https://manage.windowsazure.com) :

1. Accédez au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez à l’onglet Virtual Machines.
3. Cliquez sur l’onglet Disques.
4. Sélectionnez votre disque de données, puis cliquez sur Supprimer le disque.
5. Pour supprimer des images de disque, accédez à l’onglet Images et supprimez les images qui sont stockées dans le compte.

Pour plus d’informations, voir la [documentation sur les machines virtuelles Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Étapes suivantes

- [Stockage d’objets blob Azure : niveaux froid et chaud](storage-blob-storage-tiers.md)
- [Réplication Azure Storage](storage-redundancy.md)
- [Configuration des chaînes de connexion Azure Storage](storage-configure-connection-string.md)
- [Transfert de données avec l'utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- Consultez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

<!---HONumber=AcomDC_0928_2016-->