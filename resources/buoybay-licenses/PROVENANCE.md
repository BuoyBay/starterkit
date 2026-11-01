# Provenance

Reference: https://github.com/npalardy/KitchenSink

Inspected commit: `62aae83ade1be137f2dd90b8af4268fa22d22955` (retrieved 2026-10-31).
The project is linked by https://www.great-white-software.com/open-source/.

KitchenSink's root LICENSE calls itself “Mostly MIT” and excludes Xojo Inc and
its staff and contractors from the permission grant. This is a substantive
restriction; do not describe it as standard MIT. No original source, comments,
artwork or project resources are included in this package. The implementation
was written independently using Buoy interfaces, arrays and exception handling.
The source inspection informed the capability inventory and topic-bus choice.

Original Notifications exposes Subscribe, Unsubscribe, Notify, MessageIsA and a
Subscriber interface with a Variant payload. Its limited regex subscription
patterns and process-global dictionary are deliberately not replicated.
StarterKit provides an explicitly owned instance, exact names, String payloads,
ordered calls, mutation semantics, explicit strong ownership and bounded nested
publication. It is an alternative primitive, not a source-compatible port of
the entire KitchenSink starter project.
