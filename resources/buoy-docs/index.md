---
title: "StarterKit"
summary: "Publish synchronous application-model topics to explicitly owned subscribers."
category: "Overview"
order: 0
keywords: ["topic", "bus", "subscriber", "notification", "model"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# StarterKit

StarterKit adds an ordered synchronous topic bus for application models. Implement `TopicSubscriber`, subscribe an instance to an exact topic, and retain the integer subscription token. It complements Buoy's inheritance-based single-cast events.

For owner-bound cleanup, retain `SubscribeScoped`'s `TopicSubscription` handle.
Dispose it explicitly or let its last reference end. Its owner reference is weak;
receiver registrations remain strong. See [the scoped API contract](api.md#scoped-handles).

This bus is independent of operating-system notifications and does not use the `notifications` package. There is no native dependency, queue, UI-thread dispatch, wildcard matching, or automatic weak subscription.

## Delivery semantics

Topics match exactly and case-sensitively, including Unicode. Dots and asterisks have no special meaning. Duplicate subscriptions are separate registrations and receive separate calls in registration order.

Publish snapshots registrations. Additions during a callback appear in the next Publish, including a nested call. Removing a pending subscription or clearing the bus prevents that delivery in the current snapshot. A receiver exception stops delivery and propagates to the publisher.

## Explicit ownership

Subscribers are retained strongly until unsubscribe, clear, or bus destruction. If a subscriber also owns the bus, break the ownership cycle with explicit Unsubscribe/Clear and release its bus reference. Use the bus from one thread; it has no synchronization.

Payloads are Strings. Use JSON text or a separate typed domain interface for richer application messages. Nested publication is allowed up to 32 active calls, with depth restored even when a callback throws.

Start with [Getting started](getting-started.md), then read [API reference](api.md) for tokens and mutation rules.
