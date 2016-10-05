<properties 
   pageTitle="Compilation de configurations dans Azure Automation DSC | Microsoft Azure" 
   description="Présentation des deux manières de compiler les configurations de l’état souhaité (DSC) : dans le portail Azure et avec Windows PowerShell. " 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="01/25/2016"
   ms.author="coreyp"/>
   
#Compilation de configurations dans Azure Automation DSC#

Vous pouvez compiler les configurations de l’état souhaité (DSC) de deux manières avec Azure Automation : dans le portail Azure et avec Windows PowerShell. Le tableau suivant vous aide à déterminer quand utiliser chaque méthode en fonction des caractéristiques de chacune :

###Portail Azure en version préliminaire###
- Méthode la plus simple avec une interface utilisateur interactive
- Formulaire pour fournir les valeurs de paramètres simples
- Suivi aisé de l’état des tâches
- Accès authentifié avec ouverture de session Azure

###Windows PowerShell###
- Appel à partir de la ligne de commande avec les applets de commande Windows PowerShell
- Possibilité d’inclusion dans une solution automatisée à plusieurs étapes
- Fourniture de valeurs de paramètres simples et complexes
- Suivi de l’état des tâches
- Client requis pour prendre en charge les applets de commande PowerShell
- Transmission ConfigurationData
- Compilation de configurations utilisant des informations d’identification

Une fois que vous avez choisi une méthode de compilation, vous pouvez suivre les procédures correspondantes ci-dessous pour lancer la compilation.

##Compilation d’une configuration DSC avec le portail Azure##

1.  À partir de votre compte Automation, cliquez sur **Configurations**.
2.  Cliquez sur une configuration pour ouvrir son panneau.
3.  Cliquez sur **Compiler**.
4.  Si la configuration ne possède aucun paramètre, vous devez confirmer si vous souhaitez la compiler. Si la configuration possède des paramètres, le panneau **Compiler la configuration** s’ouvre afin que vous puissiez fournir les valeurs de paramètre. Pour plus d’informations sur les paramètres, voir la section <a href="#basic-parameters">**Paramètres de base**</a> ci-dessous.
5.  Le panneau **Tâche de compilation** est ouvert de sorte que vous pouvez suivre l’état de la tâche de compilation, ainsi que les configurations de nœud (documents de configuration MOF) qui ont dû être placés sur le serveur collecteur Azure Automation DSC.

##Compilation d’une configuration DSC avec Windows PowerShell##

Vous pouvez utiliser [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) pour démarrer la compilation avec Windows PowerShell. L’exemple de code suivant commence la compilation d’une configuration DSC appelée **SampleConfig**.

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` renvoie un objet de tâche de compilation que vous pouvez utiliser pour suivre son état. Vous pouvez ensuite utiliser cet objet de tâche de compilation avec [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) pour déterminer l’état de la tâche de compilation, et [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) pour afficher ses flux (sortie). L’exemple de code suivant démarre la compilation de la configuration **SampleConfig**, attend qu’elle soit terminée, puis affiche ses flux.
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {
    	$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##Paramètres de base##

La déclaration de paramètres dans les configurations DSC, y compris les types de paramètres et les propriétés, fonctionne de la même manière que pour les runbooks Azure Automation. Consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) pour en savoir plus sur les paramètres de runbooks.

L’exemple suivant utilise deux paramètres appelés **FeatureName** et **IsPresent**, afin de déterminer les valeurs des propriétés dans la configuration du nœud **ParametersExample.sample**, générée pendant la compilation.

    Configuration ParametersExample
    {
    	param(
    		[Parameter(Mandatory=$true)]
    
    		[string] $FeatureName,
    
    		[Parameter(Mandatory=$true)]
    		[boolean] $IsPresent
    	)
    
    	$EnsureString = "Present"
    	if($IsPresent -eq $false)
    	{
    		$EnsureString = "Absent"
    	}
    
    	Node "sample"
    	{
    		WindowsFeature ($FeatureName + "Feature")
    		{
    			Ensure = $EnsureString
    			Name = $FeatureName
    		}
    	}
    }

Vous pouvez compiler les configurations DSC qui utilisent des paramètres de base dans le portail Azure Automation DSC ou avec Azure PowerShell :

###Portail###

Dans le portail, vous pouvez entrer des valeurs de paramètre après avoir cliqué sur **Compiler**.

![texte de remplacement](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell requiert des paramètres dans une [table de hachage](http://technet.microsoft.com/library/hh847780.aspx) où la clé correspond au nom de paramètre et la valeur est égale à la valeur du paramètre.

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

Pour plus d’informations sur la transmission d’informations d’identification PowerShell en tant que paramètres, voir la section <a href="#credential-assets">**Ressources d’informations d’identification**</a> ci-dessous.

##ConfigurationData##

**ConfigurationData** vous permet de séparer la configuration structurelle de toute configuration spécifique de l’environnement lors de l’utilisation de la configuration de l’état souhaité PowerShell. Consultez [Distinguer « objet » et « emplacement » dans la configuration de l’état souhaité PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) pour en savoir plus sur **ConfigurationData**.

>[AZURE.NOTE] Vous pouvez utiliser **ConfigurationData** lors de la compilation dans Azure Automation DSC à l’aide d’Azure PowerShell, mais pas dans le portail Azure.

L’exemple de configuration de l’état souhaité suivant utilise **ConfigurationData** via les mots-clés **$ConfigurationData** et **$AllNodes**. Vous aurez également besoin du module [**xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/) pour cet exemple :

     Configuration ConfigurationDataSample
     {
    	Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    
    	Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 
    
    	Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    	{
    		xWebsite Site
    		{
    			Name = $Node.SiteName
    			PhysicalPath = $Node.SiteContents
    			Ensure   = "Present"
    		}
    	}
    }

Vous pouvez compiler la configuration DSC ci-dessus avec PowerShell. La commande PowerShell ci-dessous ajoute deux configurations de nœud au serveur collecteur Azure Automation DSC : **ConfigurationDataSample.MyVM1** et **ConfigurationDataSample.MyVM3** :

    $ConfigData = @{
    	AllNodes = @(
			@{
    			NodeName = "MyVM1"
    			Role = "WebServer"
    		},
    		@{
    			NodeName = "MyVM2"
    			Role = "SQLServer"
    		},
    		@{
    			NodeName = "MyVM3"
    			Role = "WebServer"
    
    		}
    
    	)
    
    	NonNodeData = @{
    		SomeMessage = "I love Azure Automation DSC!"
    
    	}
    
    } 
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


##Éléments multimédias##

Les références de ressources sont les mêmes dans les configurations Azure Automation DSC et les runbooks. Consultez les liens suivants pour plus d’informations :

- [Certificats](automation-certificates.md)
- [Connexions](automation-connections.md)
- [Informations d'identification](automation-credentials.md)
- [Variables](automation-variables.md)

###Ressources d’informations d’identification###
Bien que les configurations DSC dans Azure Automation puissent référencer des ressources d’informations d’identification en utilisant **Get-AzureRmAutomationCredential**, vous pouvez également transmettre les ressources d’informations d’identification par le biais de paramètres si vous le souhaitez. Si une configuration accepte un paramètre de type **PSCredential**, vous devez transmettre le nom de chaîne d’une ressource d’informations d’identification Azure Automation comme valeur de ce paramètre, plutôt qu’un objet PSCredential. En arrière-plan, la ressource d’informations d’identification Azure Automation portant le même nom est récupérée et transmise à la configuration.

Conserver les informations d’identification en sûreté dans les configurations de nœud (documents de configuration MOF) nécessite le chiffrement des informations d’identification dans le fichier MOF de configuration de nœud. Azure Automation va encore plus loin et chiffre la totalité du fichier MOF. Cependant, actuellement, vous devez indiquer à la configuration de l’état souhaité PowerShell que vous êtes d’accord pour que les informations d’identification soient extraites en texte brut lors de la génération du fichier MOF de configuration de nœud, parce que la configuration de l’état souhaité PowerShell ne sait pas qu’Azure Automation va chiffrer l’intégralité du fichier MOF après sa génération via une tâche de compilation.

Vous pouvez indiquer à la configuration DSC PowerShell que vous êtes d’accord pour que les informations d’identification soient extraites en texte brut dans les fichiers MOF de configuration de nœud générés en utilisant <a href="#configurationdata">**ConfigurationData**</a>. Vous devez transmettre `PSDscAllowPlainTextPassword = $true` par le biais de **ConfigurationData** pour chaque nom de bloc de nœuds qui apparaît dans la configuration DSC et utilise les informations d’identification.

L’exemple suivant montre une configuration de l’état souhaité qui utilise une ressource d’informations d’identification Automation.

    Configuration CredentialSample
    {
       $Cred = Get-AzureRmAutomationCredential -Name "SomeCredentialAsset"
    
    	Node $AllNodes.NodeName
    	{ 
    		File ExampleFile
    		{ 
    			SourcePath = "\\Server\share\path\file.ext" 
    			DestinationPath = "C:\destinationPath" 
    			Credential = $Cred 
       		}
    	}
    }

Vous pouvez compiler la configuration DSC ci-dessus avec PowerShell. La commande PowerShell ci-dessous ajoute deux configurations de nœud au serveur collecteur Azure Automation DSC : **CredentialSample.MyVM1** et **CredentialSample.MyVM2**.


    $ConfigData = @{
    	AllNodes = @(
    		@{
    			NodeName = "*"
    			PSDscAllowPlainTextPassword = $True
    		},
    		@{
    			NodeName = "MyVM1"
    		},
    		@{
    			NodeName = "MyVM2"
    		}
    	)
    }
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData

<!---HONumber=AcomDC_0921_2016-->