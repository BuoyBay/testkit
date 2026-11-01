---
title: "TestKit getting started"
summary: "Install TestKit and try a complete usage example."
category: "Guides"
order: 10
keywords: ["test", "assertion", "suite", "fixture", "JSON", "cleanup"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Copy `testkit-0.1.0.buipkg` into `packages/` and import `testkit`.

## Register and finish a suite

Each case must have a unique, nonempty name. The following complete test runs one callback and emits the final structured record consumed by a process runner.

```buoy
Import testkit

Private Sub Arithmetic()
    Check.IntEqual(6 * 7, 42, "multiplication")
    Check.DoubleWithin(0.1 + 0.2, 0.3, 0.000001)
End Sub

Sub Main()
    Dim suite As New TestSuite("arithmetic example")
    suite.Run("multiplication and rounding", AddressOf Arithmetic)
    suite.Finish()
End Sub
```

## Fixtures and failures

`Run` accepts optional setup and cleanup delegates. Cleanup runs after a case or setup exception, including `TestSkipped`; it does not run for a filtered-out case. If cleanup fails, the case becomes an error and retains the earlier message.

An optional suite filter is a case-sensitive substring of the case name.
Nonmatching cases are recorded as skipped. `Check.Throws` proves that some Buoy
exception occurred. `ThrowsType` checks an exact runtime type name, while
`ThrowsMatching` takes a typed predicate for subclass-aware checks. Both return
the captured exception for message and error-code assertions.

Do not call Finish twice or finish an empty suite; both are invalid. A runner should validate the final record together with process exit status, treating crashes, timeouts, and missing/truncated records as infrastructure failures.

## Timing, selection and asynchronous callbacks

Case reports include duration in seconds covering setup through cleanup. The
shared runner supports case include/exclude patterns and literal selections;
TestKit validates and acknowledges the runner's selection file so old packages
cannot silently run the wrong cases. Selection is scoped to the runtime suite
name and leaves nested helper suites unaffected.

For asynchronous work, `RunAsync` returns a token immediately. Your existing
main-thread loop calls `PollAsync` until `HasPendingAsync` is false. Complete,
fail or skip the token from the event callback; wrap assertions in
`token.Invoke(callback)` to capture exceptions. Polling checks the monotonic
deadline and runs cleanup once. TestKit does not pump or take ownership of the
native or console event loop. See the API reference for timeout and late-callback
behavior and the repository's `examples/testkit_async_demo.bui` for a complete
console consumer.

[Package overview](index.md) · [API reference](api.md)
