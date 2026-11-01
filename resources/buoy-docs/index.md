---
title: "TestKit"
summary: "Run explicit Buoy test cases with catchable assertions and structured results."
category: "Overview"
order: 0
keywords: ["test", "assertion", "suite", "fixture", "JSON", "cleanup"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# TestKit

TestKit supplies explicit sequential tests, catchable assertions, setup/cleanup callbacks, and versioned JSON results. Register delegates with `AddressOf`; no reflection, source scanning, or custom attributes are required. Cases run on the calling thread.

Use `Check` assertions inside cases. They throw `TestFailure`, which the suite records. The built-in `Assert` exits immediately and cannot be caught by this suite. A native crash or abort cannot run Buoy cleanup either.

## Result lifecycle

`Report()` returns schema-1 JSON and remains incomplete until `Finish()` seals the suite. Finish emits one flushed line beginning with `BUOY_TESTKIT ` and exits with status 1 when any case failed or errored. Successful suites return normally. Call Finish only after resource-owning scopes have been cleaned up.

Process isolation, deadlines, bounded logs, temporary projects, compiler diagnostics, and JUnit conversion belong to the external runner. Calling a UI Perform hook inside a test does not establish real rendering or native input behavior.

## Read next

- [Getting started](getting-started.md)
- [API reference](api.md)
