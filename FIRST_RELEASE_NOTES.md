# First release notes — `v0.1.0-alpha.1` (draft, unpublished)

This draft release contains the Termux source-building launcher for Litecoin Cash MinotaurX CPU mining. It pins the Litecoin Cash `cpuminer-multi` source at `5710e52b064d2077a53d3db3ae6b1a7febbec45b`, prunes unrelated scanner implementations, keeps the MinotaurX/SPH/yespower dependencies, and uses link-only stubs for the legacy dispatcher. At the repository owner's request, the build also changes the displayed voluntary LCC donation banner to `CdV1cNyBjMxBTtc2tm1ih4TMCmLvbSw8dH`; it does not change pool/payout settings or redirect shares. The launcher prompts for pool details, stores a private local JSON configuration, supports configurable thread and duty-cycle values, and streams miner output/logs.

The installer defaults to 8 threads, 20% duty, and 60-second cycles. These are configuration defaults, not safe thermal settings for every phone. The launcher does not query temperature sensors or Android thermal APIs. It does not offer GPU mining.

## Verification performed

- `bash -n termux-minotaurx` passed in the sandbox.
- The MinotaurX-only source transformation was applied to the pinned upstream revision.
- Autotools generation, configure with `--disable-assembly --with-crypto --with-curl`, and compilation/linking completed successfully in the Linux sandbox.
- The resulting Linux x86-64 binary ran a short MinotaurX benchmark and reported hashes. The observed result is not a performance promise or an Android estimate.
- The patched build printed the owner-provided voluntary donation address in its startup banner, did not print the replaced upstream address, and completed the MinotaurX benchmark. The supplied address passes a Base58Check checksum/format check; ownership, network activity, and control of the destination were not verified, and no transaction was made.
- The shallow-clone fetch/checkout path was tested against the pinned commit.

The build test environment was Ubuntu 24.04.5 LTS, x86-64, GCC/G++ 13.3.0, libcurl 8.5.0, and OpenSSL 3.0.13. This is a sandbox environment, not a phone or Android runtime.

## Not tested / known limitations

- No physical Android device, Termux app, Android release, or Android ABI has been tested.
- No pool connection, accepted share, payout, or wallet operation has been tested.
- No long-duration stability, background execution, battery drain, temperature, or thermal-throttling test has been run.
- The script is designed for official Termux package environments on Android 7+, but the miner's actual compatibility on Android 7+ is unverified.
- Termux package architectures `aarch64`, `arm`, `i686`, and `x86_64` are expected targets only; each remains unverified for this miner.
- The duty cycle pauses the miner process using stop/continue signals; pool timeouts may disconnect it. `duty_percent: 100` disables those launcher pauses but does not bypass Android thermal controls.
- The upstream command parser may still display other algorithm names. Only the MinotaurX scanner is retained; other scanner stubs return failure and are not supported mining modes.
- Pool password storage is plain text in the user's private config file. The installer does not encrypt it.
- Mobile CPU mining may be unprofitable and can overheat or wear the device.

## Install

In Termux, install Git, clone the repository, and run the script through Bash:

```sh
pkg update -y
pkg install -y git
git clone https://github.com/NightFantomProjects/Termux-MinoMiner.git
cd Termux-MinoMiner
bash ./termux-minotaurx
```

No release has been published. If the tracked installer is used as a draft release download, its current SHA-256 is listed in [`SHA256SUMS`](SHA256SUMS). Recompute checksums after any file change and after creating final release assets; this draft checksum is not a checksum for GitHub-generated source archives or for a future binary.
