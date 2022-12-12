---
title: "Get change notifications for Microsoft Teams virtual event updates"
description: "Use change notifications in Microsoft Graph to enable you to subscribe to various events for Microsoft Teams online meetings."
author: "benlee-msft"
ms.localizationpriority: high
ms.prod: "cloud-communications"
ms.custom: scenarios:getting-started
---

# Get change notifications for Microsoft Teams virtual event updates

## Subscribe to notifications for virtual event updates.

Please review webhook subscriptions for more details about the subscription payload.

### Permissions

| Permission type                       | Permissions (from least to most privileged)              | Supported versions |
|:--------------------------------------|:---------------------------------------------------------|:-------------------|
| Delegated (work or school account)    | Not supported.                                           | Not supported.     |
| Delegated (personal Microsoft account)| Not supported.                                           | Not supported.     |
| Application                           | VirutalEvent.Read.All                                    | beta               |

To get change notifications for virtual events, you may specify the resource as the following: 

### Subscribeable resources

| Resource Type                            | Resource                                                                        | Supported change types    |
|:-----------------------------------------|:--------------------------------------------------------------------------------|:--------------------------|
| Event (Tenant-level)                     | solutions/virtualEvents/events                                                  | created                   |
| Event                                    | solutions/virtualEvents/events/{eventId}                                        | updated, deleted          |
| Sessions (All sessions for an event)     | solutions/virtualEvents/events/{eventId}/sessions                               | created, deleted |
| Session updates                          | communications/onlineMeetings/?$filter=JoinWebUrl eq '{meetingJoinUrl}'         | updated  |
| Registrant (All registants for an event) | solutions/virtualEvents/events/{eventId}/registration/registrants               | created, updated, deleted |
| Attendance report                        | ***solutions/virtualEvents/events/{eventId}/sessions/{sessionId}/attendanceReports*** | TBD                       |

**Note:** Replace values with paranthesis with actual values.

### Subscription to all created events in a tenant

Subscripitions for all created events for a unique app and tenant is accomplished by having `solutions/virtualEvents/events` as the resource in the subscription payload.
Only event created notifications are supported for this subscription.

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to all created events for the following organizer and co-organizers.

Subscriptions to all created events for a tenant where a group of organizers/coorganizers are a part of can be accomplished with the resource specified as 
`"solutions/virtualEvents/events/?$filter=organizerid eq '{userA}' and coorganizerid in ('{userB}', '{userC}')"`. The query params here are used to specify the organizer and coorganizer for created events that are of interest.

***For this case are we saying that***:
 - A.) We want any notifications where the organizer and co-organizers are included? 
   - If it is this case, is there a point in having organizer and coorganizer query params.
 - B.) Are we specifying that we want all notifications that must contain this organizer and co-organizer combination?

***Implementation question, how are we deciding subscription uniqueness for either cases?***

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/?$filter=organizerid eq '4cc2cccd-f9b3-4551-8bb9-f134d0366e1a' and coorganizerid in ('f0e00be8-8bd6-48f6-812b-03e778a7e64c', '1ee69d4c-8aaf-4f6f-9791-a78ca1207fb9')",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to specific event's updated and deleted

To receive updated and deleted notifications for a particular event, a subscription must be created for that unique event id.
The resource `solutions/virtualEvents/events/{eventId}` must contain a valid event id and the changet type must include the notifications desired.

***A subscription can be created for a unique app, tenant, and resource combination for subscriptions to a specific event.***

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "updated, deleted",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId}",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to all sessions for a particular event

Session notifications for a particular event can be subscribed to by specifying the resource as `solutions/virtualEvents/events/{eventId}/sessions`. ***This subscription can only receive notifications for a sessions created and updated events?***

***Only a single session level subscription can exist for a particular event for a unique app and tenant combination.***

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created, deleted",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId}/sessions",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to a specific session's updated events

To subscribe to a specific session's events, please see [Change notifications for online meetings](changenotifications-for-onlinemeetings.md) for more information.

### Subscribe to all registrants for a particular event

Registant notifications for a particular event can be subscribed to by specifying the resource as `solutions/virtualEvents/events/{eventId}/registration/registrants`.

***Only a single registrant level subscription can exist for a particular event for a unique app and tenant combination.***

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created, updated, deleted",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId}/registration/registrants",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to attendance reports for a particular session --> Prediction

To receive notifications on the attendance reports for a particular session, please specify the resource as `solutions/virtualEvents/events/{eventId}/sessions/{sessionId}/attendanceReports`. 

***Only a single attendance report subscription can exist for a particular event, session, app, and tenant combination.***

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "TBD",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}/attendanceReports",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

## Receiving event notifications

Virtual events resources receives notifications for the following changes:
- **Event:** Refers to virtual event changes.
- **Session:** Refers to the online meeting sessions created for virtual event.
- **Registrant:** Refers to participant registration at the event level. While these notifications are at event level, the meta data can contain references to sessions.

Notifications will include the resource url of the changed resource. A separate request to graph will be required to obtain the information of the changed resource.

The below table indicates the supported notification and change types for the virtual events resource.

### Notification types

| Notification type      | Resource id                                                                                 | Change types    |
|:-----------------------|:--------------------------------------------------------------------------------------------|:----------------|
| [Event]()              | solutions/virtualEvents/events/{eventId}                                                    | created, updated, deleted  |
| [Session]()            | solutions/virtualEvents/events/{eventId}/sessions/{sessionId}                         | created, updated, deleted  |
| [Registrant]()         | solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}              | created, updated, deleted  |
| [Attendance reports]() | solutions/virtualEvents/events/{eventId}/sessions/{sessionId}/attendanceReports/{reportId} | TBD |

## Event notification examples
### Event created
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/"
    }
  }]
}
```

### Event updated

```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "updated",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/"
    }
  }]
}
```

### Event deleted

```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "deleted",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/"
    }
  }]
}
```

## Session notification examples
### Session created
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/sessions",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

### Session updated ---> Is this only online meeting events?
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "updated",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/sessions",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

### Session deleted
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "deleted",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/sessions",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

## Registrant notifications examples
### Registrant created
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/registration/registrants",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```

### Registrant created
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "updated",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/registration/registrants",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```

### Registrant deleted
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "deleted",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/registration/registrants",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```
## Attendance report notifications examples --> TBD