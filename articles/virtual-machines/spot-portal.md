---
title: Uso del portal para implementar máquinas virtuales de acceso puntual de Azure
description: Uso de Azure PowerShell para implementar máquinas virtuales de acceso puntual de Azure para ahorrar costos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098577"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Implementación de máquinas virtuales de acceso puntual de Azure con Azure Portal

El uso de [máquinas virtuales de acceso puntual de Azure](spot-vms.md) permite aprovechar las ventajas de nuestra capacidad no utilizada con un importante ahorro en los costos. Siempre que Azure necesite recuperar la capacidad, su infraestructura expulsará las máquinas virtuales de acceso puntual de Azure. Por lo tanto, las máquinas virtuales de acceso puntual de Azure son excelentes para cargas de trabajo que puedan soportar interrupciones, como los trabajos de procesamiento por lotes, los entornos de desarrollo/pruebas, las grandes cargas de trabajo de proceso, etc.

Los precios de las máquinas virtuales de acceso puntual de Azure son variables, en función de la región y la SKU. Para más información, consulte precios de las máquinas virtuales para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) y [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obtener más información sobre cómo se establece el precio máximo, consulte [Máquinas virtuales de acceso puntual de Azure: Precios](spot-vms.md#pricing).

Puede establecer el precio máximo por hora que esté dispuesto por la máquina virtual. El precio máximo de una máquina virtual de acceso puntual de Azure se puede establecer en dólares estadounidenses (USD), con un máximo de 5 decimales. Por ejemplo, el valor `0.05701` correspondería a un precio máximo de 0,05701 USD por hora. Si establece el precio máximo en `-1`, la máquina virtual no se expulsará por precio. El precio de la máquina virtual será el actual de Spot o el de una máquina virtual estándar, el menor de los dos, siempre que haya capacidad y cuota disponibles.

Cuando se expulsa la VM, tiene la opción de eliminar la VM y el disco subyacente, o bien desasignar la VM para que se pueda reiniciar más tarde.


## <a name="create-the-vm"></a>Creación de la máquina virtual

Al implementar una máquina virtual, puede elegir usar una instancia de Azure Spot.


En la pestaña **Básico**, en la sección **Detalles de instancia**, el valor predeterminado para el uso de una instancia de Azure Spot es **No**.

![Captura de pantalla para elegir No, no usar una instancia de Azure Spot](./media/spot-portal/no.png)

Si selecciona **Sí**, la sección se expande y podrá elegir [el tipo y la directiva de expulsión](spot-vms.md#eviction-policy). 

![Captura de pantalla para elegir Sí, usar una instancia de Azure Spot](./media/spot-portal/yes.png)

También puede comparar los precios y las tasas de expulsión con otras regiones similares; para ello, seleccione **View pricing history and compare prices in nearby regions** (Ver el historial de precios y comparar precios en regiones cercanas).

En este ejemplo, la región Centro de Canadá es más económica y tiene una tasa de expulsión inferior a la región Este de EE. UU.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Captura de pantalla de las opciones de región con la diferencia de precios y tasas de expulsión.":::

Para cambiar la región, puede seleccionar la opción que mejor le convenga y seleccionar **Aceptar**.

## <a name="simulate-an-eviction"></a>Simulación de una expulsión

Puede [simular una expulsión](/rest/api/compute/virtualmachines/simulateeviction) de una máquina virtual de acceso puntual de Azure para probar de qué manera la aplicación responderá a una expulsión repentina. 

Reemplazar lo siguiente por su propia información: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` significa que la expulsión simulada se ha realizado correctamente. 

## <a name="next-steps"></a>Pasos siguientes

También puede crear máquinas virtuales de acceso puntual de Azure con [PowerShell](./windows/spot-powershell.md), la [CLI](./linux/spot-cli.md) o una [plantilla](./linux/spot-template.md).
