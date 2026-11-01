---
title: "testkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["AsyncTestAction", "AsyncTestToken", "Check", "ExceptionMatcher", "TestAction", "TestFailure", "TestSkipped", "TestSuite"]
dependencies: []
symbols: ["AsyncTestAction", "AsyncTestToken", "Check", "ExceptionMatcher", "TestAction", "TestFailure", "TestSkipped", "TestSuite"]
related: ["api.md", "getting-started.md", "index.md"]
---

# testkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## TestKit functions

### TestAction

`Public Delegate Sub TestAction()`

A named test or fixture callback. Register explicitly with AddressOf.

No parameters.

Returns: no value.

### ExceptionMatcher

`Public Delegate Function ExceptionMatcher(error As Exception) As Boolean`

Match an exception using typed IsA checks or application-specific fields.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `error` | `Exception` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### AsyncTestAction

`Public Delegate Sub AsyncTestAction(token As AsyncTestToken)`

Starts an asynchronous case; complete the token from the main thread.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `token` | `AsyncTestToken` | `ByVal` | Required |

Returns: no value.

### TestPatternMatches

`Public Function TestPatternMatches(value As String, pattern As String) As Boolean`

Match case-sensitive patterns; only * and ? are special.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `value` | `String` | `ByVal` | Required |
| `pattern` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

## TestFailure

`Public Class TestFailure Inherits Exception`

Catchable assertion failure, separate from the exit-on-failure Testing prelude.

### Constructor

`Public Sub Constructor(message As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |

Returns: no value.

## TestSkipped

`Public Class TestSkipped Inherits Exception`

Throw from a case to record a skipped test. Cleanup still runs.

### Constructor

`Public Sub Constructor(message As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |

Returns: no value.

## Check

`Public Class Check`

Assertions throw TestFailure; they never call the built-in Assert class.

### IsTrue

`Public Shared Sub IsTrue(passed As Boolean, label As String = "condition")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `passed` | `Boolean` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"condition"` |

Returns: no value.

### IntEqual

`Public Shared Sub IntEqual(actual As Integer, expected As Integer, label As String = "integer")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `Integer` | `ByVal` | Required |
| `expected` | `Integer` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"integer"` |

Returns: no value.

### Fail

`Public Shared Sub Fail(message As String = "explicit failure")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | `"explicit failure"` |

Returns: no value.

### StringEqual

`Public Shared Sub StringEqual(actual As String, expected As String, label As String = "string")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `String` | `ByVal` | Required |
| `expected` | `String` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"string"` |

Returns: no value.

### ArrayEqual

`Public Shared Sub ArrayEqual(actual As Array Of Integer, expected As Array Of Integer, label As String = "array")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `Array Of Integer` | `ByVal` | Required |
| `expected` | `Array Of Integer` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"array"` |

Returns: no value.

### ArrayEqual (overload 2)

`Public Shared Sub ArrayEqual(actual As Array Of String, expected As Array Of String, label As String = "array")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `Array Of String` | `ByVal` | Required |
| `expected` | `Array Of String` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"array"` |

Returns: no value.

### ArrayEqual (overload 3)

`Public Shared Sub ArrayEqual(actual As Array Of Double, expected As Array Of Double, tolerance As Double, label As String = "array")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `Array Of Double` | `ByVal` | Required |
| `expected` | `Array Of Double` | `ByVal` | Required |
| `tolerance` | `Double` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"array"` |

Returns: no value.

### BytesEqual

`Public Shared Sub BytesEqual(actual As Bytes, expected As Bytes, label As String = "bytes")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `Bytes` | `ByVal` | Required |
| `expected` | `Bytes` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"bytes"` |

Returns: no value.

### DoubleWithin

`Public Shared Sub DoubleWithin(actual As Double, expected As Double, tolerance As Double, label As String = "double")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actual` | `Double` | `ByVal` | Required |
| `expected` | `Double` | `ByVal` | Required |
| `tolerance` | `Double` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"double"` |

Returns: no value.

### Throws

`Public Shared Sub Throws(action As TestAction, label As String = "exception")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `action` | `TestAction` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"exception"` |

Returns: no value.

### ThrowsType

`Public Shared Function ThrowsType(action As TestAction, expectedType As String, label As String = "exception") As Exception`

Require an exact concrete runtime type name and return the captured exception.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `action` | `TestAction` | `ByVal` | Required |
| `expectedType` | `String` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"exception"` |

#### Returns

| Type |
| --- |
| `Exception` |

### ThrowsMatching

`Public Shared Function ThrowsMatching(action As TestAction, matcher As ExceptionMatcher, label As String = "exception") As Exception`

Require a caller's typed predicate; subclasses can match through IsA.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `action` | `TestAction` | `ByVal` | Required |
| `matcher` | `ExceptionMatcher` | `ByVal` | Required |
| `label` | `String` | `ByVal` | `"exception"` |

#### Returns

| Type |
| --- |
| `Exception` |

## AsyncTestToken

`Public Class AsyncTestToken`

A single asynchronous case completion token. All operations are main-thread only. TestSuite.PollAsync performs deadlines and cleanup; it never pumps an event loop.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `IsInvoking` | `Boolean` | Yes | No | — | — |
| `IsComplete` | `Boolean` | Yes | No | — | — |
| `Status` | `String` | Yes | No | — | — |
| `Error` | `Exception` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(owner As TestSuite, deadline As Integer, selected As Boolean = True)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `owner` | `TestSuite` | `ByVal` | Required |
| `deadline` | `Integer` | `ByVal` | Required |
| `selected` | `Boolean` | `ByVal` | `True` |

Returns: no value.

### Complete

`Public Sub Complete()`

No parameters.

Returns: no value.

### Fail

`Public Sub Fail(message As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |

Returns: no value.

### Skip

`Public Sub Skip(message As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |

Returns: no value.

### Invoke

`Public Sub Invoke(action As TestAction)`

Invoke an event callback with exception capture while this token is pending. The callback calls Complete explicitly; successful invocation alone does not finish.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `action` | `TestAction` | `ByVal` | Required |

Returns: no value.

### Reject

`Public Sub Reject(error As Exception, status As String)`

Used by the suite to retain an actual callback/setup exception.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `error` | `Exception` | `ByVal` | Required |
| `status` | `String` | `ByVal` | Required |

Returns: no value.

### Poll

`Public Sub Poll()`

Checks the monotonic deadline without dispatching platform events.

No parameters.

Returns: no value.

### Invalidate

`Public Sub Invalidate()`

Close after recording; late completion cannot affect a later case.

No parameters.

Returns: no value.

## TestSuite

`Public Class TestSuite`

Sequential cases and explicitly polled asynchronous cases with final JSON results. Process isolation and hard deadlines belong to the external runner.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `FailureCount` | `Integer` | Yes | No | — | — |
| `HasPendingAsync` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(name As String, filter As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `filter` | `String` | `ByVal` | `""` |

Returns: no value.

### RecordLateCallback

`Public Sub RecordLateCallback()`

Diagnostic counter only; completed/finished suites cannot be modified by tokens.

No parameters.

Returns: no value.

### Run

`Public Sub Run(name As String, action As TestAction, setup As TestAction = Nil, cleanup As TestAction = Nil)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `action` | `TestAction` | `ByVal` | Required |
| `setup` | `TestAction` | `ByVal` | `Nil` |
| `cleanup` | `TestAction` | `ByVal` | `Nil` |

Returns: no value.

### RunAsync

`Public Function RunAsync(name As String, action As AsyncTestAction, timeoutSeconds As Double, setup As TestAction = Nil, cleanup As TestAction = Nil) As AsyncTestToken`

Begin one asynchronous case, then return to the caller's event loop. Call PollAsync from that loop/timer until HasPendingAsync becomes False.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `action` | `AsyncTestAction` | `ByVal` | Required |
| `timeoutSeconds` | `Double` | `ByVal` | Required |
| `setup` | `TestAction` | `ByVal` | `Nil` |
| `cleanup` | `TestAction` | `ByVal` | `Nil` |

#### Returns

| Type |
| --- |
| `AsyncTestToken` |

### PollAsync

`Public Function PollAsync() As Boolean`

Advances one pending case without pumping any platform or console run loop. Returns True when no asynchronous case remains.

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### Report

`Public Function Report() As String`

No parameters.

#### Returns

| Type |
| --- |
| `String` |

### Finish

`Public Sub Finish()`

Emit and flush exactly one final record; exit 1 after failed cases.

No parameters.

Returns: no value.
