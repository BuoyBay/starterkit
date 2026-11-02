# In-process pub/sub

`Import starterkit` adds a synchronous, ordered application-model topic bus.
This is an independent implementation prompted by KitchenSink's Notifications
component. It complements inheritance-based single-cast Events; it does
not deliver system notifications or use the `notifications` package.

Implement `TopicSubscriber.ReceiveTopic(topic As String, payload As String)`.
Create `New TopicBus()`, retain the integer token returned by
`Subscribe(topic, receiver)`, and call `Publish(topic, payload = "")`.
`Unsubscribe(token)` returns whether it removed a subscription; `Clear()` removes
all subscriptions. `SubscriptionCount()` reports the current registrations.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add starterkit --git https://github.com/BuoyBay/starterkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

```basic
Import starterkit

Class StatusPanel Implements TopicSubscriber
    Public Overrides Sub ReceiveTopic(topic As String, payload As String)
        Print "Status panel: " + topic + " -> " + payload
    End Sub
End Class

Class DocumentLog Implements TopicSubscriber
    Public Overrides Sub ReceiveTopic(topic As String, payload As String)
        Print "Document log: " + payload
    End Sub
End Class

Sub Main()
    Dim bus As New TopicBus()
    Dim status As New StatusPanel()
    Dim log As New DocumentLog()
    Dim statusToken As Integer = bus.Subscribe("document.saved", status)
    Dim logToken As Integer = bus.Subscribe("document.saved", log)
    bus.Publish("document.saved", "notes.bui")
    bus.Unsubscribe(statusToken)
    bus.Publish("document.saved", "chapter.bui")
    bus.Clear()
End Sub
```

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer starterkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
