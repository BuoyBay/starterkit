# StarterKit 0.1.0

`Import starterkit` adds a synchronous, ordered application-model topic bus.
This is an independent implementation prompted by KitchenSink's Notifications
component. It complements Buoy's inheritance-based single-cast Events; it does
not deliver system notifications or use the `notifications` package.

Implement `TopicSubscriber.ReceiveTopic(topic As String, payload As String)`.
Create `New TopicBus()`, retain the integer token returned by
`Subscribe(topic, receiver)`, and call `Publish(topic, payload = "")`.
`Unsubscribe(token)` returns whether it removed a subscription; `Clear()` removes
all subscriptions. `SubscriptionCount()` reports the current registrations.

Topics match exactly and case-sensitively, including Unicode; dots and asterisks
have no special meaning. Empty topics and Nil receivers throw
`InvalidArgumentException`. Duplicate subscriptions are distinct registrations
and receive separate calls in registration order. Tokens belong to their bus;
passing a token from another bus is a caller error and may remove a same-numbered
local subscription. Tokens are not reused after Clear.

Publish snapshots the ordered registrations. Additions during a callback appear
on the next Publish (including a nested Publish). Removing a pending registration
or clearing the bus prevents its delivery in the current snapshot. A callback
exception stops delivery and propagates to the caller. Nested Publish is allowed
up to 32 active calls; the next call throws `InvalidArgumentException`. The depth
counter is restored even when a callback throws.

Removal updates registrations before releasing subscribers, so a subscriber's
Destructor may safely reenter the bus. `Clear()` detaches and deactivates all
current registrations before releasing their receivers; subscriptions added by
those destructors remain registered.

Subscribers are retained strongly until Unsubscribe/Clear or bus destruction.
If a subscriber owns its bus, explicitly Clear/Unsubscribe and release its bus
reference to break ownership cycles. Bus registrations retain their receivers
strongly. This bus has no queue, thread synchronization, UI-thread dispatch,
wildcard subscriptions or Variant
payloads. Access it from one thread. Payloads are Strings; use JSON text or a
separate typed domain interface for richer messages.

## Scoped subscriptions

`SubscribeScoped(topic, receiver) As TopicSubscription` creates a registration and
returns its owner-bound handle. Retain that handle for as long as the subscription
should remain active. `New TopicSubscription(bus, topic, receiver)` is equivalent;
its constructor never accepts an arbitrary numeric token. The legacy integer API
remains unchanged.

- `handle.IsActive` reflects the live owner's registration. It becomes False
  after `Dispose`, `Clear`, legacy `Unsubscribe`, or destruction of the bus.
- `handle.Dispose()` removes only its own registration, through the bus that
  created it. Repeated disposal is harmless. It cannot accidentally target a
  different bus whose numeric tokens happen to match.
- Dropping the **last reference** to a handle runs its destructor and disposes
  the registration. Assigning a handle to another variable shares the handle;
  dropping one alias does not end the registration while another alias remains.
  Discarding the result of `SubscribeScoped` therefore immediately unsubscribes.
- The handle uses a `Weak TopicBus` field, so it does not keep the owner bus alive
  or add a strong handle-to-bus cycle. The bus does not retain the handle.

Explicit `Dispose()` in `Finally` is useful when the intended lifetime ends before
the last reference disappears. Disposal inside a callback follows the original
snapshot rules: a currently executing callback completes, while a pending disposed
registration is skipped. `IsSubscribed(token)` also exposes a read-only query for
legacy integer registrations.

Scoped handles do not make receiver references weak. If a receiver strongly owns
its bus, the existing bus/receiver cycle still requires explicit disposal or
`Clear`. A receiver that owns its own handle remains retained by the bus until
explicit disposal/clear; its handle cannot be destroyed first to break that chain.
Handle methods and destruction must follow the same single-thread access rule as
the bus. No cross-thread dispatch or thread-safe lifetime guarantee is added.
