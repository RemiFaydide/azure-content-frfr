<properties 
	pageTitle="Surveillance et gestion des travaux Stream Analytics à l’aide de PowerShell | Microsoft Azure" 
	description="Découvrez comment utiliser Azure PowerShell et les applets de commande pour surveiller et gérer des travaux Stream Analytics." 
	keywords="azure powershell, applets de commande azure powershell, commande powershell, scripts powershell"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>


# Surveillance et gestion des travaux Stream Analytics à l’aide des applets de commande Azure PowerShell

Découvrez comment surveiller et gérer les ressources Stream Analytics à l’aide d’applets de commande Azure PowerShell et de scripts PowerShell qui exécutent les tâches Stream Analytics de base.

## Conditions requises pour l'exécution des applets de commande Azure PowerShell pour Stream Analytics

 - Créez un groupe de ressources Azure dans votre abonnement. Voici un exemple de script Azure PowerShell : Pour obtenir des informations sur Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Azure PowerShell 0.9.8 :

 		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
		Select-AzureSubscription -SubscriptionName <subscription name>
 
		# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
		#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0 :

 		# Log in to your Azure account
		Login-AzureRmAccount

		# Select the Azure subscription you want to use to create the resource group.
		Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

		# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
		#Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
		
		# Create an Azure resource group
		New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
		


> [AZURE.NOTE] Par défaut, la surveillance n’est pas activée pour les travaux Stream Analytics créés par programme. Vous pouvez activer manuellement la surveillance dans le portail Azure. Pour cela, accédez à la page de surveillance du travail et cliquez sur le bouton Activer. Vous pouvez également procéder par programme en suivant les étapes décrites dans [Azure Stream Analytics - Surveillance des travaux Stream Analytics par programme](stream-analytics-monitor-jobs.md).

## Applets de commande Azure PowerShell pour Stream Analytics
Vous pouvez utiliser les applets de commande Azure PowerShell suivantes pour surveiller et gérer des travaux Azure Stream Analytics. Notez qu'il existe différentes versions d'Azure PowerShell. **Dans les exemples répertoriés, la première commande s'applique à Azure PowerShell 0.9.8, la deuxième commande s'applique à Azure PowerShell 1.0.** Les commandes Azure PowerShell 1.0 contiennent toujours « AzureRM » dans la commande.

### Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Répertorie tous les travaux Stream Analytics définis dans l’abonnement Azure ou le groupe de ressources spécifié, ou obtient des informations sur un travail spécifique au sein d’un groupe de ressources.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsJob

Cette commande PowerShell retourne des informations sur tous les travaux Stream Analytics dans l’abonnement Azure.

**Exemple 2**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Cette commande PowerShell retourne des informations sur tous les travaux Stream Analytics dans le groupe de ressources StreamAnalytics-Default-Central-US.

**Exemple 3**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Cette commande PowerShell retourne des informations sur le travail Stream Analytics StreamingJob dans le groupe de ressources StreamAnalytics-Default-Central-US.

### Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Répertorie toutes les entrées qui sont définies dans un travail Stream Analytics spécifié ou obtient des informations sur une entrée spécifique.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Cette commande PowerShell retourne des informations sur toutes les entrées définies dans le travail StreamingJob.

**Exemple 2**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Cette commande PowerShell retourne des informations sur l’entrée nommée EntryStream définie dans le travail StreamingJob.

### Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Répertorie toutes les sorties qui sont définies dans un travail Stream Analytics spécifié ou obtient des informations sur une sortie spécifique.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Cette commande PowerShell retourne des informations sur les sorties définies dans le travail StreamingJob.

**Exemple 2**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Cette commande PowerShell retourne des informations sur la sortie nommée Output définie dans le travail StreamingJob.

### Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtient des informations sur le quota des unités de diffusion en continu d'une région spécifiée.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Cette commande PowerShell retourne des informations sur le quota et l’utilisation des unités de diffusion en continu dans la région Centre des États-Unis.

### Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtient des informations sur une transformation spécifique définie dans un travail Stream Analytics.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0 :

	Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Cette commande PowerShell retourne des informations sur la transformation nommée StreamingJob dans le travail StreamingJob.

### New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
Crée une entrée dans un travail Stream Analytics ou met à jour une entrée spécifiée existante.
  
Vous pouvez spécifier le nom de l'entrée dans le fichier .json ou sur la ligne de commande. Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez une entrée qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer l'entrée existante.

Si vous spécifiez le paramètre -Force et un nom d'entrée existant, l'entrée est remplacée sans confirmation.

Pour plus d'informations sur la structure et le contenu du fichier JSON, reportez-vous à la section [Création d'une entrée (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] de la [bibliothèque de référence des API REST de gestion de Stream Analytics][stream.analytics.rest.api.reference].

**Exemple 1**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Cette commande PowerShell crée une entrée à partir du fichier Input.json. Si une entrée existante avec le nom spécifié dans le fichier de définition d'entrée est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Cette commande PowerShell crée une entrée dans le travail nommé EntryStream. Si une entrée existante portant ce nom est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 3**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Cette commande PowerShell remplace la définition de la source d’entrée existante nommée EntryStream par la définition qui se trouve dans le fichier.

### New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Crée un travail Stream Analytics dans Microsoft Azure ou met à jour la définition d'un travail existant spécifié.

Vous pouvez spécifier le nom du travail dans le fichier .json ou sur la ligne de commande. Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez un nom de travail qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer le travail existant.

Si vous spécifiez le paramètre -Force et un nom de travail existant, la définition de travail est remplacée sans confirmation.

Pour plus d'informations sur la structure et le contenu du fichier JSON, reportez-vous à la section [Création d'un travail Stream Analytics][msdn-rest-api-create-stream-analytics-job] de la [bibliothèque de référence des API REST de gestion de Stream Analytics][stream.analytics.rest.api.reference].

**Exemple 1**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Cette commande PowerShell crée un travail à partir de la définition qui se trouve dans JobDefinition.json. Si un travail existant avec le nom spécifié dans le fichier de définition de travail est déjà défini, l'applet de commande vous demande s'il faut le remplacer.

**Exemple 2**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Cette commande PowerShell remplace la définition du travail StreamingJob.

### New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
Crée une sortie dans un travail Stream Analytics ou met à jour une sortie existante.

Vous pouvez spécifier le nom de la sortie dans le fichier .json ou sur la ligne de commande. Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez une sortie qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer la sortie existante.

Si vous spécifiez le paramètre -Force et un nom de sortie existant, la sortie est remplacée sans confirmation.

Pour plus d'informations sur la structure et le contenu du fichier JSON, reportez-vous à la section [Création d'une sortie (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] de la [bibliothèque de référence des API REST de gestion de Stream Analytics][stream.analytics.rest.api.reference].

**Exemple 1**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Cette commande PowerShell crée une sortie nommée « output » dans le travail StreamingJob. Si une sortie existante portant ce nom est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Cette commande PowerShell remplace la définition de la sortie « output » dans le travail StreamingJob.

### New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
Crée une transformation dans un travail Stream Analytics ou met à jour la transformation existante.
  
Vous pouvez spécifier le nom de la transformation dans le fichier .json ou sur la ligne de commande. Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez une transformation qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer la transformation existante.

Si vous spécifiez le paramètre -Force et un nom de transformation existant, la transformation est remplacée sans confirmation.

Pour plus d'informations sur la structure et le contenu du fichier JSON, reportez-vous à la section [Création d'une transformation (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] de la [bibliothèque de référence des API REST de gestion de Stream Analytics][stream.analytics.rest.api.reference].

**Exemple 1**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Cette commande PowerShell crée une transformation nommée StreamingJobTransform dans le travail StreamingJob. Si une transformation existante est déjà définie avec ce nom, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**

Azure PowerShell 0.9.8 :

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0 :

	New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Cette commande PowerShell remplace la définition de StreamingJobTransform dans le travail StreamingJob.

### Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Supprime de manière asynchrone une entrée spécifique d'un travail Stream Analytics dans Microsoft Azure. Si vous spécifiez le paramètre -Force, l'entrée est supprimée sans confirmation.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0 :

	Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Cette commande PowerShell supprime l’entrée EventStream dans le travail StreamingJob.

### Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Supprime de manière asynchrone un travail Stream Analytics spécifique dans Microsoft Azure. Si vous spécifiez le paramètre -Force, le travail est supprimé sans confirmation.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0 :

	Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Cette commande PowerShell supprime le travail StreamingJob.

### Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Supprime de manière asynchrone une sortie spécifique d'un travail Stream Analytics dans Microsoft Azure. Si vous spécifiez le paramètre -Force, la sortie est supprimée sans confirmation.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0 :

	Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Cette commande PowerShell supprime la sortie Output dans le travail StreamingJob.

### Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Déploie et démarre un travail Stream Analytics dans Microsoft Azure de façon asynchrone.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0 :

	Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Cette commande PowerShell démarre le travail StreamingJob avec une heure de début de sortie personnalisée définie sur le 12 décembre 2012, 12:12:12 UTC.


### Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Arrête l'exécution d'un travail Stream Analytics dans Microsoft Azure de façon asynchrone et libère les ressources qui étaient utilisées. La définition du travail et les métadonnées restent disponibles dans votre abonnement par le biais du Portail Azure et des API de gestion ; ainsi, le travail peut être modifié et redémarré. Un travail à l'état Arrêté ne vous sera pas facturé.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0 :

	Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Cette commande PowerShell arrête le travail StreamingJob.

### Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Teste la capacité de Stream Analytics à se connecter à une entrée spécifiée.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0 :

	Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Cette commande PowerShell teste l’état de la connexion de l’entrée EntryStream dans StreamingJob.

###Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Teste la capacité de Stream Analytics à se connecter à une sortie spécifiée.

**Exemple 1**

Azure PowerShell 0.9.8 :

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0 :

	Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Cette commande PowerShell teste l’état de la connexion de la sortie Output dans StreamingJob.

## Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)


## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0921_2016-->