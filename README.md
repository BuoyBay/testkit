# Define test cases, assertions, cleanup and structured results

Explicit Buoy test cases, catchable assertions, setup/cleanup and versioned JSON
results. Copy `testkit-0.1.0.buipkg` into `packages/`, then `Import testkit`.
The [example](examples/testkit_demo.bui) shows a complete consumer, and
the [async example](examples/testkit_async_demo.bui) shows explicit queue
scheduling without taking over a native application's event loop.

Build and run the sealed-package tests:

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add testkit --git https://github.com/BuoyBay/testkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer testkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```

The generated agent files and editor settings stay local and are ignored by Git.

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
