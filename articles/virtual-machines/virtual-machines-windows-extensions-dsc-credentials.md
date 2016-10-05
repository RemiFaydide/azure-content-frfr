<properties
   pageTitle="Transfert des informations d’identification à Azure à l’aide de DSC | Microsoft Azure"
   description="Vue d’ensemble du transfert sécurisé des informations d’identification aux machines virtuelles Azure à l’aide de la Configuration de l’état souhaité PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# Transfert des informations d’identification au gestionnaire de l’extension de Configuration de l’état souhaité (DSC) Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cet article traite de l’extension de Configuration de l’état souhaité pour Azure. Vous trouverez une vue d’ensemble du gestionnaire de l’extension DSC dans [Présentation du gestionnaire de l’extension de Configuration de l’état souhaité Azure](virtual-machines-windows-extensions-dsc-overview.md).


## Passage d’informations d’identification
Dans le cadre du processus de configuration, vous devrez peut-être configurer des comptes d’utilisateur, accéder aux services ou installer un programme dans un contexte utilisateur. Pour effectuer ces opérations, vous devez fournir les informations d’identification.

DSC permet d’effectuer des configurations paramétrables dans lesquelles les informations d’identification sont transmises à la configuration et stockées en toute sécurité dans les fichiers MOF. Le Gestionnaire d’extensions Azure simplifie la gestion des informations d’identification en fournissant une gestion automatique des certificats.

Examinez le script de configuration DSC suivant qui crée un compte d’utilisateur local avec le mot de passe spécifié :

*user\_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Il est important d’inclure l’*hôte local du nœud* dans le cadre de la configuration. Si cette instruction est manquante, les étapes suivantes ne fonctionnent pas, car le gestionnaire d’extensions recherche spécifiquement l’instruction relative à l’hôte local du nœud. Il est également important d’inclure le caractère *[PsCredential]*, car ce type spécifique déclenche l’extension pour chiffrer les informations d’identification.

Publiez ce script sur le stockage d’objets blob :

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Définissez l’extension DSC Azure et fournissez les informations d’identification :

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## Sécurisation des informations d’identification
L’exécution de ce code invite à entrer les informations d’identification. Une fois celles-ci fournies, elles sont stockées en mémoire brièvement. Lorsqu’elles sont publiées avec l’applet de commande `Set-AzureVmDscExtension`, elles sont transmises via le protocole HTTPS à la machine virtuelle, où elles sont stockées de manière chiffrée sur le disque par Azure, à l’aide du certificat local de la machine virtuelle. Elles sont ensuite déchiffrées brièvement en mémoire, puis rechiffrées pour leur transfert à DSC.

Ce comportement diffère de l’[utilisation de configurations sécurisées sans le gestionnaire d’extensions](https://msdn.microsoft.com/powershell/dsc/securemof). L’environnement Microsoft Azure permet de transmettre des données de configuration en toute sécurité via des certificats. Lors de l’utilisation du gestionnaire d’extensions DSC, il est inutile de fournir une entrée $CertificatePath ou $CertificateID/$Thumbprint dans ConfigurationData.


## Étapes suivantes ##

Pour plus d’informations sur le gestionnaire d’extensions DSC Azure, voir [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](virtual-machines-windows-extensions-dsc-overview.md).

Examinez le [modèle Azure Resource Manager pour l’extension DSC](virtual-machines-windows-extensions-dsc-template.md).

Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Pour accéder aux fonctionnalités supplémentaires que vous pouvez gérer avec DSC PowerShell, [parcourez PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) pour voir des ressources DSC supplémentaires.

<!---HONumber=AcomDC_0921_2016-->