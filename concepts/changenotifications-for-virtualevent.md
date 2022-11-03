---
title: "Get change notifications for Microsoft Teams virtual event updates"
description: "Use change notifications in Microsoft Graph to enable you to subscribe to various events for Microsoft Teams online meetings."
author: "benlee-msft"
ms.localizationpriority: high
ms.prod: "cloud-communications"
ms.custom: scenarios:getting-started
---

# Get change notifications for Microsoft Teams virtual event updates

## Subscribe to notifications for virtual event messages.

To get change notifications for virtual events, you may specify the resource as the following: 
- `/communication/virtualEvents` to receive notifications for all virtual events in tenant. 
- `/communication/virtualEvents/{Virtual-event-id}` to receive notificatiions for specific virtual event.
  - **Note:** Please replace {Virtual-event-id} with the actual virtual event id. For more on the virtual event id, see [VIRTUALEVENT API/RESOURCE PAGE](SOMELINK).

### Permissions

| Permission type                       | Permissions (from least to most privileged)              | Supported versions |
|:--------------------------------------|:---------------------------------------------------------|:-------------------|
| Delegated (work or school account)    | Not supported.                                           | Not supported.     |
| Delegated (personal Microsoft account)| Not supported.                                           | Not supported.     |
| Application                           | OnlineMeetings.Read.All, OnlineMeetings.ReadWrite.All    | beta               |

### Example

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "updated",
  "notificationUrl": "https://webhook.azurewebsites.net/api/resourceNotifications",
  "resource": "/communications/virtualEvents",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "{secretClientState}"
}
```



### Event notifications

An event can be described as.....


### Session notifications (Unavailable)

Sessions in virtual events are ....

### Registrant notifications (Unavailable)

Registrants notifications correspond to ....


### Attendance report notifications (Unavailable)

Attendance report notifications are available to....

### Notification types

| Notification type                       | Resource id              | Change types |
|:--------------------------------------|:---------------------------------------------------------|:-------------------|
| Delegated (work or school account)    | Not supported.                                           | Not supported.     |
| Delegated (personal Microsoft account)| Not supported.                                           | Not supported.     |
| Application                           | OnlineMeetings.Read.All, OnlineMeetings.ReadWrite.All    | beta               |
