---
title: "starterkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["TopicBus", "TopicSubscriber", "TopicSubscription"]
dependencies: []
symbols: ["TopicBus", "TopicSubscriber", "TopicSubscription"]
related: ["api.md", "getting-started.md", "index.md"]
---

# starterkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## TopicSubscriber

`Public Interface TopicSubscriber`

A receiver explicitly subscribed to one or more exact topic names.

### ReceiveTopic

`Sub ReceiveTopic(topic As String, payload As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `topic` | `String` | `ByVal` | Required |
| `payload` | `String` | `ByVal` | Required |

Returns: no value.

## TopicSubscription

`Public Class TopicSubscription`

An owner-bound subscription. The handle never keeps its TopicBus alive.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `IsActive` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(owner As TopicBus, topic As String, receiver As TopicSubscriber)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `owner` | `TopicBus` | `ByVal` | Required |
| `topic` | `String` | `ByVal` | Required |
| `receiver` | `TopicSubscriber` | `ByVal` | Required |

Returns: no value.

### Dispose

`Public Sub Dispose()`

No parameters.

Returns: no value.

### Destructor

`Public Sub Destructor()`

No parameters.

Returns: no value.

## TopicBus

`Public Class TopicBus`

Synchronous, ordered, explicitly owned topic subscriptions. Not thread safe.

### Subscribe

`Public Function Subscribe(topic As String, receiver As TopicSubscriber) As Integer`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `topic` | `String` | `ByVal` | Required |
| `receiver` | `TopicSubscriber` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

### Unsubscribe

`Public Function Unsubscribe(token As Integer) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `token` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### SubscribeScoped

`Public Function SubscribeScoped(topic As String, receiver As TopicSubscriber) As TopicSubscription`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `topic` | `String` | `ByVal` | Required |
| `receiver` | `TopicSubscriber` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `TopicSubscription` |

### IsSubscribed

`Public Function IsSubscribed(token As Integer) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `token` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### Clear

`Public Sub Clear()`

No parameters.

Returns: no value.

### SubscriptionCount

`Public Function SubscriptionCount() As Integer`

No parameters.

#### Returns

| Type |
| --- |
| `Integer` |

### Publish

`Public Sub Publish(topic As String, payload As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `topic` | `String` | `ByVal` | Required |
| `payload` | `String` | `ByVal` | `""` |

Returns: no value.
