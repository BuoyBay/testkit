# TestKit · 0.1.0

Explicit Buoy test cases, catchable assertions, setup/cleanup and versioned JSON
results. Copy `testkit-0.1.0.buipkg` into `packages/`, then `Import testkit`.
The [example](../examples/testkit_demo.bui) shows a complete consumer, and
the [async example](../examples/testkit_async_demo.bui) shows explicit queue
scheduling without taking over a native application's event loop.

Build and run the sealed-package tests:

```sh
./dist/buoy-dev task build-testkit
./dist/buoy-dev test --filter testkit --json dist/testkit.json --junit dist/testkit.xml
```

`TestSuite(name, filter = "")` runs explicit `TestAction` delegates with
`Run(name, action, setup: callback, cleanup: callback)`. Cases execute in call
order. The optional filter is a case-sensitive substring of the case name;
nonmatching cases are reported as skipped. No reflection, source scanning or
custom attributes are needed. Cases must have unique, nonempty names.

`Check.IsTrue`, `Check.IntEqual`, `Check.StringEqual`, `Check.DoubleWithin`, and
`Check.Throws` throw `TestFailure`. String comparison is case sensitive.
`DoubleWithin` uses an absolute, finite, nonnegative tolerance; NaN and infinite
operands fail. `Throws` expects any Buoy exception. `ThrowsType(action,
expectedType)` requires an exact case-sensitive concrete runtime type name and returns the
exception. `ThrowsMatching(action, matcher)` accepts an `ExceptionMatcher`
predicate, allowing typed `IsA` checks including subclasses, and also returns
the captured exception. Matcher exceptions remain errors rather than being
mistaken for the exception under test.

`Check.ArrayEqual` compares integer and string arrays, or double arrays with an
explicit tolerance. `Check.BytesEqual` compares binary buffers including NUL
bytes. Failures identify the first zero-based differing index/offset and both
lengths. `StringEqual` identifies the first character index and one-based line
and column, with escaped context. `Check.Fail(message)` records an explicit
failure. Assertion messages are bounded to 1023 UTF-8 bytes; comparisons do not
require a diff or regex package.

The suite catches assertion failures, `TestSkipped` and other exceptions.
Cleanup runs after the case or setup throws, including after a skip. A cleanup
failure makes the case an error and retains the earlier message. Cleanup is not
called for filtered cases. `FailureCount` counts failed/error cases.

`Report()` returns schema-1 JSON with suite name and cases. `complete` becomes
true only when `Finish()` seals the suite. `Finish()` emits one line prefixed
`BUOY_TESTKIT `, flushes stdout and exits with status 1 if any cases failed.
Successful suites return normally. Empty suites and a second `Finish()` are
errors. Call `Finish()` after all resource-owning scopes have been cleaned up.

Each case includes a `diagnostics` array. Entries contain `phase` (`setup`,
`case` or `cleanup`), the concrete exception `type`, its `message` and integer
`code`. Original and cleanup exceptions are retained separately in occurrence
order, including skips caused by `TestSkipped`. Passed and filtered cases have
empty arrays. These are additive fields in schema 1; existing status/message
fields keep their behavior. Stack traces are unavailable through Buoy's public
exception API and are not included.

Cases additionally include finite, nonnegative `duration` in seconds, measured
with the monotonic clock from setup through cleanup, and `selected`. A filtered
case has zero duration and no fixture callbacks. Synchronous calls on the same
suite cannot be reentered while a case or asynchronous case is active.

The runner can set `BUOY_TESTKIT_SELECTION` to a selection file. Its version-1
envelope contains `suite`, `include`, `exclude` and `exact` fields. The named
runtime suite applies case-sensitive patterns (`*` and `?` only), then excludes,
then an optional literal exact-name list; the constructor filter also applies.
Other suites, including nested helper suites, ignore a well-formed envelope.
Malformed files, unsupported versions, duplicate object keys, NUL escapes and
unpaired surrogate escapes are rejected before fixtures execute. The matching
suite reports a versioned `selection` acknowledgement with selected/discovered
counts and requested exact names that were not discovered. Deselected cases stay
in the report as `skipped`, `selected: false`, message `not selected`. A suite
with discovered cases but no selected cases still produces a complete record;
the external runner decides whether the overall selection was empty. See the
[shared selection contract](https://github.com/BuoyBay/ahoy/blob/main/src/buoydev/README.md#testing-extension-contract)
for CLI flags and runtime/configured suite-name mapping.

`RunAsync(name, action, timeoutSeconds, setup: callback, cleanup: callback)` starts
one `AsyncTestAction(token)` and returns an `AsyncTestToken`. The caller retains
its existing event loop and calls `PollAsync()` until `HasPendingAsync` is false.
There is no internal event dispatch or blocking wait. Start, polling, completion
and token callbacks must run on the main thread; timeout is within `(0, 86400]`
seconds, starts before setup, and uses the monotonic clock. Start another case
or call `Finish()` only after the pending case settles.

Tokens provide `Complete()`, `Fail(message)` and `Skip(message)`. The first
terminal state wins, with the deadline taking precedence over completion after
expiry. A captured exception before finalization overrides optimistic completion
or a skip; an earlier failure remains a failure. Polling inside an active action
or `Invoke` callback defers cleanup until that callback returns.
`token.Invoke(callback)` catches callback assertions, skips and other
exceptions; successful invocation alone does not complete the token. Use it to
wrap assertions inside event callbacks so exceptions do not escape the event
loop. `PollAsync()` checks the deadline, records the outcome and runs cleanup
exactly once. Duplicate or late callbacks cannot mutate the next case; they
increment `late_callbacks` before suite completion and become no-ops afterwards.

This API does not interrupt blocked setup, callbacks or cleanup. The outer
process watchdog remains required. Console code may explicitly drain its callback
queue; desktop code should poll from its native timer. The executed Linux
scheduler probe establishes callback
queue delivery and observes no native timer delivery under console pumping.
The desktop scheduler probe executes
a real native timer under the Linux desktop loop and checks completion/cleanup.
Run `./dist/buoy-dev task probe-testkit-native` to check its six targets and
execute it on the host (Linux uses Xvfb when no display is present).
The 13-case consumer and desktop native timer probe also pass on macOS arm64
and Windows x64; see `dist/xojounit-macos-validation/results.md` and
`dist/xojounit-windows-validation/results.md` for exact tested snapshots.
On the Linux host the desktop
probe runs under Xvfb with runtime libraries from
`/usr/lib/buoy/runtime/x86_64-unknown-linux-gnu` and the existing JPEG8 prerequisite
at `/tmp/buoy-chatui-jpeg8/build` in `LD_LIBRARY_PATH`.

The process runner validates the final record and exit status together. Crashes,
timeouts and missing/truncated records remain infrastructure failures. The
built-in `Assert` class still exits immediately; it cannot be caught by TestKit.
Use `Check` inside cases. A native abort cannot run Buoy cleanup.

Tests and callbacks run sequentially; async lifecycle operations run on the main
thread. Process isolation,
temporary project directories, deadlines, bounded logs, JUnit and compiler
diagnostics are supplied by the [shared runner](https://github.com/BuoyBay/ahoy/blob/main/src/buoydev/README.md).
UI `Perform*` hooks can be called inside a case; they do not establish native
rendering or real input behavior.

The package, test and example are compiler-checked on six targets. Runtime
validation is performed on Linux x64, macOS arm64 and Windows x64. Intel macOS
is build-checked only; ARM64 Linux/Windows execution is unverified.

Test suites and fixtures are maintainer-local and are not part of this public repository.
