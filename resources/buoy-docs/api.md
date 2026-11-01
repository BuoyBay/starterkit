---
title: "StarterKit API"
summary: "Exact topic subscriptions, ordered synchronous delivery, tokens, and reentrancy limits."
category: "Reference"
order: 20
keywords: ["TopicBus", "TopicSubscriber", "TopicSubscription", "Publish", "SubscribeScoped", "Dispose", "Clear"]
symbols: ["TopicSubscriber", "TopicSubscriber.ReceiveTopic", "TopicBus", "TopicBus.Subscribe", "TopicBus.SubscribeScoped", "TopicBus.IsSubscribed", "TopicSubscription", "TopicSubscription.IsActive", "TopicSubscription.Dispose", "TopicBus.Unsubscribe", "TopicBus.Publish", "TopicBus.Clear", "TopicBus.SubscriptionCount"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

## Subscriber and bus

| Member | Contract |
| --- | --- |
| `TopicSubscriber.ReceiveTopic(topic As String, payload As String)` | Host-implemented synchronous receiver callback |
| `New TopicBus()` | Empty bus owned by one thread |
| `Subscribe(topic, receiver)` | Retain a receiver and return a new Integer registration token |
| `SubscribeScoped(topic, receiver)` | Register and return an owner-bound TopicSubscription handle |
| `IsSubscribed(token)` | Query whether a legacy registration is still active |
| `Unsubscribe(token)` | Remove that registration; return whether it existed |
| `Publish(topic, payload = "")` | Invoke matching current registrations in order |
| `Clear()` | Remove all registrations and release retained receivers |
| `SubscriptionCount()` | Return the current registration count |

Empty topics and Nil receivers throw `InvalidArgumentException`. Names compare case-sensitively and exactly, including Unicode. Dots and asterisks are ordinary characters. Duplicate subscriptions intentionally create multiple callback registrations.

## Tokens and lifetime

Tokens belong to their bus and are not reused after Clear. Passing another bus's token is a caller error: its integer can match a local registration. Token-space exhaustion rejects a new subscription rather than wrapping.

Subscriptions hold strong receiver references. If the receiver owns its bus, call Unsubscribe/Clear and release its bus reference to break cycles. The bus has no weak subscriptions, automatic UI teardown hook, or thread synchronization.

## Scoped handles

Retain the `TopicSubscription` returned by `SubscribeScoped`. Its `IsActive`
property reflects disposal, legacy unsubscription, Clear and owner destruction.
`Dispose()` is idempotent and always targets the handle's own bus. The equivalent
constructor is `New TopicSubscription(bus, topic, receiver)`; no arbitrary token
is accepted by this API.

The handle has a weak owner reference and the bus does not retain its handles.
Dropping the last handle reference unsubscribes through its destructor; aliases
keep the same handle alive. Ignoring a returned handle immediately unsubscribes.
Explicit disposal in Finally is useful when the intended lifetime ends sooner.
Receiver references remain strong: receiver/bus cycles and a receiver owning its
own handle still require explicit disposal/Clear. Use all operations, including
destruction, on the bus's owning thread.

## Publish mutation and errors

Publication snapshots ordered registration objects. A callback's additions appear on the next publication, including a nested one. Removed or cleared pending registrations become inactive and do not receive the current snapshot. A callback exception stops remaining delivery and propagates.

Up to 32 active nested Publish calls are permitted. The next call throws; the depth counter is restored in a Finally path even after receiver failure. Payloads are Strings; richer types require an explicit host encoding or another typed interface.

The package supplies no asynchronous queue, wildcard routing, system notification, Variant payload, or concurrent bus. See [Getting started](getting-started.md) and the [overview](index.md).


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
