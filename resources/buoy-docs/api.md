---
title: "TestKit API"
summary: "Public types and operations supplied by TestKit."
category: "Reference"
order: 20
keywords: ["test", "assertion", "suite", "fixture", "JSON", "cleanup"]
symbols: ["TestAction", "AsyncTestAction", "AsyncTestToken", "TestFailure", "TestSkipped", "Check", "Check.IsTrue", "Check.IntEqual", "Check.StringEqual", "Check.DoubleWithin", "Check.Throws", "Check.ThrowsType", "Check.ThrowsMatching", "ExceptionMatcher", "Check.ArrayEqual", "Check.BytesEqual", "Check.Fail", "TestPatternMatches", "TestSuite", "TestSuite.Run", "TestSuite.RunAsync", "TestSuite.PollAsync", "TestSuite.HasPendingAsync", "TestSuite.Report", "TestSuite.Finish", "TestSuite.FailureCount"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

## Cases and assertions

| Symbol | Contract |
| --- | --- |
| `TestAction` | Parameterless Sub delegate for a case or fixture. |
| `TestFailure(message)` | Catchable assertion exception. |
| `TestSkipped(message)` | Throw to record a skipped case; cleanup still runs. |
| `Check.IsTrue(passed, label = "condition")` | Require True. |
| `Check.IntEqual(actual, expected, label = "integer")` | Exact integer equality. |
| `Check.StringEqual(actual, expected, label = "string")` | Case-sensitive string equality. |
| `Check.DoubleWithin(actual, expected, tolerance, label = "double")` | Absolute finite nonnegative tolerance; NaN/infinite operands fail. |
| `Check.Throws(action, label = "exception")` | Require any catchable Buoy exception. |
| `Check.ThrowsType(action, expectedType, label = "exception")` | Require the exact case-sensitive concrete runtime type name and return the captured exception. |
| `Check.ThrowsMatching(action, matcher, label = "exception")` | Require an ExceptionMatcher predicate, including typed IsA checks, and return the captured exception. |
| `Check.ArrayEqual(actual, expected, label = "array")` | Integer/string array equality with the first differing zero-based index and both lengths. |
| `Check.ArrayEqual(actual, expected, tolerance, label = "array")` | Double array equality with finite nonnegative absolute tolerance. |
| `Check.BytesEqual(actual, expected, label = "bytes")` | Byte-buffer equality including NUL; first mismatch offset and lengths. |
| `Check.Fail(message = "explicit failure")` | Throw an explicit bounded TestFailure. |

## Suite

`TestSuite(name, filter = "")` requires a nonempty name. `Run(name, action, setup = Nil, cleanup = Nil)` invokes explicit callbacks in call order, records passed/failed/skipped/error outcomes, and rejects duplicate names or calls after Finish.

`FailureCount` counts failed and errored cases. `Report()` returns JSON text with schema 1, suite/case data, and the completion flag. `Finish()` seals once, prints the final prefixed record, flushes stdout, and exits nonzero on failures. It rejects empty suites and repeated completion.

The package is compiler-checked for macOS, Windows, and Linux; actual runtime validation is platform-specific. Test callbacks are sequential, and process-level isolation is deliberately outside this API.

Each case has a `diagnostics` array of exception records with `phase` (`setup`,
`case`, `cleanup`), concrete runtime `type`, `message` and integer `code`.
Records preserve both the original exception and any cleanup exception in order.
`TestSkipped` exceptions are recorded too; passed and filtered cases have empty
arrays. Existing schema-1 fields retain their behavior. Stack traces are not
available through the public exception API.

Cases also contain monotonic `duration` in seconds covering setup through
cleanup, and a `selected` boolean. Filtered cases have zero duration and no
fixtures. String equality reports the differing character index, line and column
with escaped snippets; TestFailure messages are bounded to 1023 UTF-8 bytes.

## Runner selection

`BUOY_TESTKIT_SELECTION` names a version-1 selection file with `suite`, `include`,
`exclude` and `exact` fields. Only the matching runtime suite applies it; nested
helper suites retain their own filters. Patterns are case sensitive with `*` and
`?` wildcards. Exact names are literal. Matching suites acknowledge the selection
with version/applied, selected/discovered counts and unmatched exact names.
Deselected cases stay skipped with `selected: false` and `not selected` messages.
Malformed or unsupported selection files fail before fixtures run.

## Explicit asynchronous lifecycle

`AsyncTestAction(token As AsyncTestToken)` starts a case through
`TestSuite.RunAsync(name, action, timeoutSeconds, setup = Nil, cleanup = Nil)`.
The method returns a token and does not own or pump an event loop. The caller
calls `PollAsync()` until `HasPendingAsync` is false. Main-thread operation is
required; the finite timeout is `(0, 86400]` seconds and includes setup.

`AsyncTestToken.Complete()`, `Fail(message)` and `Skip(message)` request a terminal
result. `Invoke(TestAction)` captures exceptions in dispatched callbacks, without
implicitly completing a successful callback. `PollAsync` checks deadline expiry,
settles the result and runs cleanup once. Polling inside an action or `Invoke`
callback defers finalization until that callback returns. A captured exception
overrides optimistic completion or a skip before finalization; an earlier failure
remains a failure. Late or duplicate calls cannot alter later cases. The suite's `late_callbacks` counter
records them until `Finish()`; calls after finishing have no effect.

Do not call another case or `Finish()` with an active asynchronous case. The
caller must arrange continued polling, and the external process deadline remains
necessary for a blocked callback or cleanup. A native timer and console callback
queue are distinct scheduling facilities; this API does not claim equivalence.

[Getting started](getting-started.md) · [Package overview](index.md)


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
