---
title: 'Obtención del token de acceso mediante la CLI de Azure: Azure API for FHIR'
description: En este artículo se explica cómo obtener un token de acceso para Azure API for FHIR mediante la CLI de Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: b7bdb7f4a22d080f382fea984e710980428067ff
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020302"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Obtención del token de acceso para Azure API for FHIR mediante la CLI de Azure

En este artículo se explica cómo obtener un token de acceso para Azure API for FHIR mediante la CLI de Azure. Cuando se [aprovisiona Azure API for FHIR](fhir-paas-portal-quickstart.md), configura un conjunto de usuarios o entidades de servicio que tienen acceso al servicio. Si el identificador del objeto de usuario está en la lista de identificadores de objeto permitidos, puede acceder al servicio mediante un token obtenido con la CLI de Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Obtención de un token

Azure API for FHIR usa un elemento `resource` o `Audience` con un URI igual al del servidor de FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com`. Puede obtener un token y almacenarlo en una variable (llamada `$token`) con el siguiente comando:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Uso con Azure API for FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido cómo obtener un token de acceso para Azure API for FHIR mediante la CLI de Azure. Para obtener información sobre cómo acceder a la API de FHIR con Postman, continúe con el tutorial de Postman.

>[!div class="nextstepaction"]
>[Acceso a la API de FHIR con Postman](access-fhir-postman-tutorial.md)
