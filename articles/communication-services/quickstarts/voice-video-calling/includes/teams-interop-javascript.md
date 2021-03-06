---
title: 'Inicio rápido: unirse a una reunión de Teams'
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: fa7f279e13e5dbb59af7eb3a7939be1edf0f7d5d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552512"
---
## <a name="prerequisites"></a>Requisitos previos

- Una [aplicación de llamadas de Communication Services](../getting-started-with-calling.md) operativa.
- Una [implementación de Teams](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Incorporación de los controles de la interfaz de usuario de Teams

Reemplace el código de index.html por el siguiente fragmento de código.
El cuadro de texto se utilizará para especificar el contexto de reunión de Teams y el botón se usará para la unión a la reunión especificada:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Habilitación de los controles de la interfaz de usuario de Teams

Reemplace el contenido del archivo client.js por el siguiente fragmento de código.

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Obtención del vínculo de la reunión de Teams

El vínculo de la reunión de Teams se puede recuperar mediante las Graph API. Esto se detalla en la [documentación de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
El SDK de llamada de Communication Services acepta un vínculo a toda la reunión de Teams. Este vínculo se devuelve como parte del recurso `onlineMeeting`, al que se accede bajo la propiedad [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta). También puede obtener la información necesaria de la reunión de la dirección URL **Unirse a la reunión** de la propia invitación a la reunión de Teams.

## <a name="run-the-code"></a>Ejecución del código

Los usuarios de WebPack pueden usar `webpack-dev-server` para compilar y ejecutar la aplicación. Ejecute el siguiente comando para agrupar el host de aplicación en un servidor web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra el explorador web y vaya a http://localhost:8080/. Verá lo siguiente:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Captura de pantalla de la aplicación JavaScript completada.":::

Inserte el contexto de Teams en el cuadro de texto y presione *Join Teams Meeting* (Unirse a la reunión de Teams) para unirse a la reunión de Teams desde dentro de la aplicación de Communication Services.
