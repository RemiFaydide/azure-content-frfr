<properties
	pageTitle="Utilisation de PowerShell pour sauvegarder et restaurer des applications App Service"
	description="Découvrez comment utiliser PowerShell pour sauvegarder et restaurer une application dans Azure App Service"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="nicking"/>
# Utilisation de PowerShell pour sauvegarder et restaurer des applications App Service

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API REST](../app-service-web/websites-csm-backup.md)

Apprenez à utiliser PowerShell pour sauvegarder et restaurer des [applications App Service](https://azure.microsoft.com/services/app-service/web/). Pour plus d’informations sur les sauvegardes d’applications web, et notamment en connaître les exigences et restrictions, consultez [Sauvegarder une application web dans Azure App Service](../app-service-web/web-sites-backup.md).

## Composants requis
Pour pouvoir utiliser PowerShell afin de gérer les sauvegardes de votre application, vous avez besoin des éléments suivants :

- **Une URL SAP** autorisant un accès en lecture et en écriture à un conteneur de stockage Azure. Pour une explication sur les URL SAP, voir [Présentation du modèle SAP](../storage/storage-dotnet-shared-access-signature-part-1.md). Consultez la page [Utilisation d’Azure PowerShell avec Azure Storage](../storage/storage-powershell-guide-full.md) pour obtenir des exemples de gestion d'Azure Storage à l’aide de PowerShell.
- **Une chaîne de connexion de base de données** si vous souhaitez sauvegarder une base de données avec votre application web.

### Comment générer une URL SAP à utiliser avec les applets de commande de sauvegarde d’application web
Une URL SAP peut être générée avec PowerShell. Voici un exemple montrant comment générer une URL qui peut être utilisée avec les applets de commande décrites dans cet article.

		$storageAccountName = "<your storage account's name>"
		$storageAccountRg = "<your storage account's resource group>"

		# This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

		$blobContainerName = "<name of blob container for app backups>"
		$sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## Installer Azure PowerShell 1.3.2 ou versions ultérieures

Pour obtenir des instructions sur l’installation et l’utilisation d’Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## Création d'une sauvegarde

Utilisez l’applet de commande New-AzureRmWebAppBackup pour créer une sauvegarde d’une application web.

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Vous obtenez une sauvegarde avec un nom généré automatiquement. Si vous souhaitez attribuer vous-même un nom à votre sauvegarde, utilisez le paramètre facultatif BackupName.

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Pour inclure une base de données dans votre sauvegarde, commencez par créer un paramètre de sauvegarde de base de données à l’aide de l’applet de commande New-AzureRmWebAppDatabaseBackupSetting, puis renseignez ce paramètre dans le paramètre Databases de l’applet de commande New-AzureRmWebAppBackup. Le paramètre Databases accepte un tableau de paramètres de base de données, ce qui vous permet de sauvegarder plusieurs bases de données.

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## Obtenir des sauvegardes

L’applet de commande Get-AzureRmWebAppBackupList renvoie un tableau de toutes les sauvegardes d’une application web. Vous devez renseigner le nom de l’application web et de son groupe de ressources.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Pour obtenir une sauvegarde spécifique, utilisez l’applet de commande Get-AzureRmWebAppBackup.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Pour des raisons pratiques, vous pouvez également diriger un objet d’application web dans n’importe quelle applet de commande de gestion des sauvegardes.

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## Planification de sauvegardes automatiques

Vous pouvez planifier l’exécution automatique des sauvegardes à un intervalle spécifié. Pour configurer une planification de sauvegarde, utilisez l’applet de commande Edit-AzureRmWebAppBackupConfiguration. Cette applet de commande accepte plusieurs paramètres :

- **Name** : nom de l’application web.
- **ResourceGroupName** : nom du groupe de ressources contenant l’application web.
- **Slot** : facultatif. Nom de l’emplacement de l’application web.
- **StorageAccountUrl** : URL SAP du conteneur de stockage Azure utilisé pour le stockage des sauvegardes.
- **FrequencyInterval** : valeur numérique indiquant la fréquence à laquelle les sauvegardes doivent être effectuées. Cette valeur doit être un entier positif.
- **FrequencyUnit** : unité de temps indiquant la fréquence à laquelle les sauvegardes doivent être effectuées. Peut être exprimée en heures et en jours.
- **RetentionPeriodInDays** : nombre de jours pendant lesquels les sauvegardes automatiques doivent être enregistrées avant d’être automatiquement supprimées.
- **StartTime** : facultatif. Heure de début des sauvegardes automatiques. Les sauvegardes débutent immédiatement si la valeur est null. Doit être une valeur DateTime.
- **Databases** : facultatif. Tableau de paramètres DatabaseBackupSettings pour les bases de données à sauvegarder.
- **KeepAtLeastOneBackup** - paramètre à bascule facultatif. Renseignez ce paramètre si vous souhaitez toujours conserver une sauvegarde dans le compte de stockage, quel que soit son âge.

L’exemple suivant montre comment utiliser cette applet de commande.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Pour obtenir la dernière planification de sauvegarde, utilisez l’applet de commande Get-AzureRmWebAppBackupConfiguration. Cela peut être utile pour modifier une planification qui a déjà été configurée.

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## Restauration d’une application web à partir d’une sauvegarde

Pour restaurer une application web à partir d’une sauvegarde, utilisez l’applet de commande Restore-AzureRmWebAppBackup. Le moyen le plus simple d’utiliser cette applet de commande consiste à transmettre un objet de sauvegarde récupéré à partir de l’applet de commande Get-AzureRmWebAppBackup ou de l’applet de commande Get-AzureRmWebAppBackupList.

Une fois que vous obtenez un objet de sauvegarde, vous pouvez le diriger dans l’applet de commande Restore-AzureRmWebAppBackup. Spécifiez le paramètre à bascule Overwrite pour indiquer que vous souhaitez remplacer le contenu de votre application web par le contenu de la sauvegarde. Si la sauvegarde contient des bases de données, ces bases de données sont également restaurées.

		$backup | Restore-AzureRmWebAppBackup -Overwrite

Voici un exemple d’utilisation de l’applet de commande Restore-AzureRmWebAppBackup dans laquelle tous les paramètres sont spécifiés.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## Supprimer une sauvegarde

Pour supprimer une sauvegarde, utilisez l’applet de commande Remove-AzureRmWebAppBackup. La sauvegarde est alors supprimée de votre compte de stockage. Spécifiez le nom de votre application, son groupe de ressources et l’ID de la sauvegarde que vous souhaitez supprimer.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Vous pouvez également diriger un objet de sauvegarde dans l’applet de commande Remove-AzureRmWebAppBackup pour le supprimer.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0921_2016-->