<properties
	pageTitle="Modification d’un groupe à haute disponibilité pour les machines virtuelles | Microsoft Azure"
	description="Apprenez à modifier un groupe à haute disponibilité pour vos machines virtuelles à l’aide d’Azure PowerShell et du modèle de déploiement Resource Manager."
	keywords=""
	services="virtual-machines-windows"
	documentationCenter=""
	authors="Drewm3"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="drewm"/>



# Modification du groupe à haute disponibilité pour une machine virtuelle Windows

Les étapes suivantes décrivent comment modifier le groupe à haute disponibilité d’une machine virtuelle à l’aide d’Azure PowerShell. Une machine virtuelle ne peut être ajoutée à un groupe à haute disponibilité que lors de sa création. Pour modifier le groupe à haute disponibilité, vous devez supprimer et recréer la machine virtuelle.

## Modification du groupe à haute disponibilité avec PowerShell

1. Capturez les détails clés suivants de la machine virtuelle à modifier.

	Nom de la machine virtuelle
	
	```powershell
	$vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
	$vm.Name
	```
 
	Taille de la machine virtuelle
	
	```powershell
	$vm.HardwareProfile.VmSize
	```

	L’interface réseau principale et les interfaces réseau optionnelles si elles existent sur la machine virtuelle
	
	```powershell
	$vm.NetworkProfile.NetworkInterfaces[0].Id
	```

	Profil de disque de système d’exploitation

	```powershell
	$vm.StorageProfile.OsDisk.OsType
	$vm.StorageProfile.OsDisk.Name
	$vm.StorageProfile.OsDisk.Vhd.Uri
	```

	Profils de disque pour chaque disque de données
	
	```powershell
	$vm.StorageProfile.DataDisks[<index>].Lun
	$vm.StorageProfile.DataDisks[<index>].Vhd.Uri
	```

	Extensions de machine virtuelle installées
	
	```powershell
	$vm.Extensions
	```

2. Supprimez la machine virtuelle sans supprimer les disques ou les interfaces réseau.

	```powershell
	Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
	```

3. Créer le groupe à haute disponibilité s’il n’existe pas encore

	```powershell
	New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
	```

4. Recréer la machine virtuelle à l’aide du nouveau groupe à haute disponibilité

	```powershell
	$vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>

	Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]

	Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 

	New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
	``` 

5. Ajoutez des extensions et des disques de données. Pour plus d’informations, consultez [Joindre un disque de données à une machine virtuelle](virtual-machines-windows-attach-disk-portal.md) et [Exemples de configuration d’extension](virtual-machines-windows-extensions-configuration-samples.md). Des disques de données et extensions peuvent être ajoutés à la machine virtuelle à l’aide de PowerShell ou l’interface de ligne de commande Azure.

## Exemple de script

Le script suivant fournit un exemple de rassemblement des informations requises, de suppression de la machine virtuelle d’origine et de recréation de la machine virtuelle dans un groupe à haute disponibilité.

```powershell
	#set variables
	$rg = "demo-resource-group"
	$vmName = "demo-vm"
	$newAvailSetName = "demo-as"
	$outFile = "C:\temp\outfile.txt"

	#Get VM Details
	$OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

	#Output VM details to file
	"VM Name: " | Out-File -FilePath $outFile 
	$OriginalVM.Name | Out-File -FilePath $outFile -Append

	"Extensions: " | Out-File -FilePath $outFile -Append
	$OriginalVM.Extensions | Out-File -FilePath $outFile -Append

	"VMSize: " | Out-File -FilePath $outFile -Append
	$OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

	"NIC: " | Out-File -FilePath $outFile -Append
	$OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

	"OSType: " | Out-File -FilePath $outFile -Append
	$OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

	"OS Disk: " | Out-File -FilePath $outFile -Append
	$OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

	if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
	}

	#Remove the original VM
	Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

	#Create new availability set if it does not exist
	$availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
	if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
	}

	#Create the basic configuration for the replacement VM
	$newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
	Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

	#Add Data Disks
	foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
	}

	#Add NIC(s)
	foreach ($nic in $OriginalVM.NetworkInterfaceIDs) {
		Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic
	}

	#Create the VM
	New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## Étapes suivantes

Ajout de stockage supplémentaire à votre machine virtuelle en ajoutant un [disque de données](virtual-machines-windows-attach-disk-portal.md) supplémentaire.

<!---HONumber=AcomDC_0921_2016-->