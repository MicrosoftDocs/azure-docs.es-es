---
title: Cambio de un conjunto de disponibilidad de máquinas virtuales
description: Aprenda a cambiar un conjunto de disponibilidad para su máquina virtual mediante Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 65bf2dea6f3bc2e33ec10dc75b1678466401c10b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184481"
---
# <a name="change-the-availability-set-for-a-vm"></a>Cambiar el conjunto de disponibilidad para una máquina virtual
En los pasos siguientes se describe cómo cambiar el conjunto de disponibilidad de una máquina virtual con Azure PowerShell. Una máquina virtual solo puede agregarse a un conjunto de disponibilidad cuando se crea. Para cambiar el conjunto de disponibilidad, debe eliminar la máquina virtual y volver a crearla. 

Este artículo se aplica a las máquinas virtuales Linux y Windows.

Este artículo se probó por última vez el 12/02/2019 mediante [Azure Cloud Shell](https://shell.azure.com/powershell) y la versión 1.2.0 del [módulo de PowerShell de Az](/powershell/azure/install-az-ps).

En este ejemplo no se comprueba si la máquina virtual está conectada a un equilibrador de carga. Si la máquina virtual está conectada a un equilibrador de carga, tendrá que actualizar el script para controlar ese caso. 


## <a name="change-the-availability-set"></a>Cambio del conjunto de disponibilidad 

El script siguiente proporciona un ejemplo de recopilación de la información necesaria: se elimina la máquina virtual original y, luego, se vuelve a crear en un nuevo conjunto de disponibilidad.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Pasos siguientes

Agregue almacenamiento adicional a la máquina virtual mediante la adición de un [disco de datos](attach-managed-disk-portal.md)adicional.
