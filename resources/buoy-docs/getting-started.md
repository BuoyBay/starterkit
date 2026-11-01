---
title: "StarterKit getting started"
summary: "Connect two model subscribers, publish a topic, and explicitly remove registrations."
category: "Guides"
order: 10
keywords: ["subscribe", "publish", "unsubscribe", "ownership", "ReceiveTopic"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Install `starterkit` with BPM or copy `starterkit-0.1.0.buipkg` into the consumer's `packages/`. Use `Import starterkit`; the bus has no GUI or native dependency.

## Subscribe explicitly

This complete console example sends a document-saved message to two receivers. After unsubscribing the status receiver, only the log receives the second publication. Clear releases the final retained receiver.

```buoy
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

The token identifies one registration on this bus. Keep it with the corresponding owner and bus instance. A token from another bus can accidentally share the same integer and remove a local registration; tokens are not globally unique.

## Choose lifecycle boundaries

Subscribe when a model component begins observing and unsubscribe when it stops. For a component that retains its own bus, explicitly break the resulting ownership cycle. `Clear()` is appropriate when shutting down a bus owner and discarding all registrations.

Publishing invokes callbacks synchronously on the caller's thread. Keep callbacks short or hand work to host-controlled queues. The bus does not marshal to a UI thread. Catch receiver exceptions at an appropriate publisher boundary if the application should recover; an exception stops the remaining receivers for that publication.

## Understand callback mutation

A subscription added during a callback does not join the current snapshot. Removing a later subscriber or clearing the bus prevents its pending call. A nested Publish starts a new snapshot and sees current registrations. Avoid unbounded reentrancy: the thirty-third active publication rejects with `InvalidArgumentException`.

Continue with [API reference](api.md), or return to [StarterKit](index.md).
