# Contributing

Issues and pull requests are welcome. This project is experimental and has not yet been verified on Android hardware, so reports that include exact device and build details are especially useful.

## Bug reports

Before opening an issue, check existing reports. Include the Termux source (F-Droid or official GitHub), Termux version, Android version, `uname -m`, package versions, the pinned upstream revision, and the exact command used. For build failures, provide the first compiler error and relevant final output. For pool issues, include the pool software/algorithm and the kind of rejection, but redact hostnames if private and always redact wallet addresses, worker identifiers, passwords, access tokens, private keys, and seed phrases.

Do not claim an Android architecture or device works based only on the Linux sandbox test. If you test on a physical device, report model, SoC/ABI, Android release, power mode, duration, whether the screen was on, and whether accepted shares were observed. Do not include performance numbers without describing the test conditions.

## Changes

Open an issue before a large refactor. Keep changes narrowly scoped and preserve upstream copyright/license notices in source files fetched by the installer. Do not add another algorithm, pool endpoint, payout address, credential, donation redirect, or opaque binary without explicit review and documentation.

The canonical source build is pinned to upstream commit `5710e52b064d2077a53d3db3ae6b1a7febbec45b`. The installer performs source-list pruning and creates link-only stubs because the legacy dispatcher still refers to excluded scanners. Changes to that transformation must be checked against the exact upstream revision.

Before submitting, run at least:

```sh
bash -n termux-minotaurx
```

When a Linux build environment is available, also rerun the documented Autotools build and a short `--benchmark --algo minotaurx` smoke test. An x86-64 Linux result is not evidence of Android compatibility. Do not report an Android or pool test unless you actually performed it, and keep device-specific results separate from benchmark results.

Submit a pull request describing the change, the checks that passed, the checks not run, and any platform/device limitations. Never include generated binaries, private configuration files, logs containing pool credentials, or personal wallet data.
