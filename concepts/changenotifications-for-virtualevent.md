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

Please review webhook subscriptions for more details about the subscription payload. Subscriptions to virtual event resources have a max expiration time of 3 days. Subscriptions must either be created again or renewed. Please review [Subscriptions]() for more details.

### Permissions

| Permission type                       | Permissions (from least to most privileged)              | Supported versions |
|:--------------------------------------|:---------------------------------------------------------|:-------------------|
| Delegated (work or school account)    | Not supported.                                           | Not supported.     |
| Delegated (personal Microsoft account)| Not supported.                                           | Not supported.     |
| Application                           | VirutalEvent.Read.All                                    | beta               |

To get change notifications for virtual events, you may specify the resource as the following: 

### Subscribeable resources

| Resource Type                                 | Resource                                                                        | Supported change types    |
|:----------------------------------------------|:--------------------------------------------------------------------------------|:--------------------------|
| Event (Tenant-level)                          | solutions/virtualEvents/events                                                  | created                   |
| Event (Tenant-level, with organizers filters) | solutions/virtualEvents/events/?$filter=organizers in ('{orgId1}', '{orgId2}')  | created                   |
| Event                                         | solutions/virtualEvents/events/{eventId}                                        | updated, deleted          |
| Sessions (All sessions for an event)          | solutions/virtualEvents/events/{eventId}/sessions                               | created, updated, deleted |
| Session meeting call                          | communications/onlineMeetings/?$filter=JoinWebUrl eq '{meetingJoinUrl}'         | updated                   |
| Registrant (All registants for an event) | solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualeventwebinar/registration/registrants               | created, updated, deleted |
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

### Subscribe to all created events with relevant organizer and co-organizers.

Subscriptions to all created events for a tenant where a group of organizers/coorganizers are a part of can be accomplished with the resource specified as 
`"solutions/virtualEvents/events/?$filter=organizers in ('{userA}', '{userB}', '{userC}')"`. Creating a subscription with query params will mean that any notifications created for the tenant will be notified if userA, userB, or userC are either the organizer or co-organizer for created event.

***For this case we are proposing that***:
 - A.) There are no differences for the subscription context between organizers and co-organizers 
 - B.) Any Id found in filter is relevant for notifications to be delivered to.

***Implementation question, how are we deciding subscription uniqueness for either cases?***

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/?$filter=organizers in ('f0e00be8-8bd6-48f6-812b-03e778a7e64c', '1ee69d4c-8aaf-4f6f-9791-a78ca1207fb9', '4cc2cccd-f9b3-4551-8bb9-f134d0366e1a')",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to specific event's updated and deleted

To receive updated and deleted notifications for a particular event, a subscription must be created for that unique event id.
The resource `solutions/virtualEvents/events/{eventId}` must contain a valid event id and the changet type must include the notifications desired.

A subscription can be created for a unique app, tenant, and resource combination for subscriptions to a specific event.

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

### Subscribe to all sessions' changes for a particular event.

Session notifications for a particular event can be subscribed to by specifying the resource as `solutions/virtualEvents/events/{eventId}/sessions`.

Only a single session level subscription can exist for a particular event for a unique app and tenant combination.

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created, updated, deleted",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId}/sessions",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to a specific session's meeting call events

To subscribe to a specific session's meeting call events, please see [Change notifications for online meetings](changenotifications-for-onlinemeetings.md) for more information.

### Subscribe to all registrants for a particular event

Registant notifications for a particular event can be subscribed to by specifying the resource as `solutions/virtualEvents/events/{eventId}/registration/registrants`.

Only a single registrant level subscription can exist for a particular event for a unique app and tenant combination.

```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created, updated, deleted",
  "notificationUrl": "https://webhook.contoso.com/api",
  "lifecycleNotificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventwebinar/registration/registrants",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

### Subscribe to attendance reports for a particular session --> TBD

To receive notifications on the attendance reports for a particular session, please specify the resource as `solutions/virtualEvents/events/{eventId}/sessions/{sessionId}/attendanceReports`. 

Only a single attendance report subscription can exist for a particular event, session, app, and tenant combination.

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

Notifications will include the resource url of the changed resource. A separate request to graph will be required to obtain the information of the changed resource.

The below table indicates the supported notification and change types for the virtual events resource.

### Notification types

| Notification type      | Resource id                                                                                 | Change types    |
|:-----------------------|:--------------------------------------------------------------------------------------------|:----------------|
| [Event]()              | solutions/virtualEvents/events/{eventId}                                                    | created, updated, deleted  |
| [Session]()            | solutions/virtualEvents/events/{eventId}/sessions/{sessionId}                         | created, updated, deleted  |
| [Registrant]()         | solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}              | created, updated, deleted  |
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

### Event created with OData query params
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/?$filter=organizers in ('f0e00be8-8bd6-48f6-812b-03e778a7e64c', '1ee69d4c-8aaf-4f6f-9791-a78ca1207fb9', '4cc2cccd-f9b3-4551-8bb9-f134d0366e1a')",
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
      "@odata.type": "#microsoft.graph.virtualEventSession",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

### Session updated
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
      "@odata.type": "#microsoft.graph.virtualEventSession",
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
      "@odata.type": "#microsoft.graph.virtualEventSession",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

### Session meeting call updated events. 
For more information about the types of notifications received for meeting call updates, please see [Online meeting notification types](changenotifications-for-onlinemeeting.md#event-notifications-types).

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
      "@odata.id": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEventRegistrant",
      "id": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```

### Registrant updated
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
      "@odata.id": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEventRegistrant",
      "id": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}"
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
      "@odata.id": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEventRegistrant",
      "id": "solutions/virtualEvents/events/{eventId}/microsoft.graph.virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```
## Attendance report notifications examples --> TBD