# Termux-MinoMiner

A source-building Termux launcher for **Litecoin Cash (LCC) CPU mining with MinotaurX only**. The installer pins and builds the Litecoin Cash project's `cpuminer-multi` revision, then keeps the MinotaurX scanner and its required hash dependencies while excluding unrelated mining scanner implementations.

This project is an experimental community tool, not an official Litecoin Cash or Termux release. It does not mine with a GPU. Mining on phones can produce substantial heat, battery wear, and power consumption; profitability is not implied.

## Project status and test scope

The reduced source build was configured, compiled, and linked in the Manus Linux sandbox from upstream commit `5710e52b064d2077a53d3db3ae6b1a7febbec45b`. The resulting x86-64 Linux binary ran a short MinotaurX benchmark and emitted a nonzero hash-rate report. The installer also passes `bash -n` validation.

**No Android phone or tablet has been tested.** No Termux installation, Android ABI, pool connection, accepted share, long-duration run, thermal behavior, background survival, or battery behavior has been verified. The Linux benchmark is only a basic smoke test and says nothing about Android performance or profitability. See [the first-release notes](FIRST_RELEASE_NOTES.md) for the exact test matrix.

## Android and CPU architecture expectations

The official Termux app documentation says app and package support is available for Android 7 or newer. That is a Termux platform requirement, **not a claim that this miner has been tested or confirmed on every Android 7+ device**. Install Termux from one official source, such as [F-Droid](https://f-droid.org/en/packages/com.termux/) or the [official Termux GitHub releases](https://github.com/termux/termux-app), and do not mix Termux app/plugin packages signed by different sources.

Termux package build documentation lists `aarch64`, `arm`, `i686`, and `x86_64` architectures. This miner has only been built in the sandbox on x86-64 Linux. Its compatibility with all four Android/Termux architectures is **unverified**; report the output of `uname -m` when filing a build issue. The installer disables optional assembly optimizations, but that alone does not establish portability across Android devices.

## What the launcher does

On first run, `termux-minotaurx` installs build dependencies, clones the pinned upstream source, applies the MinotaurX-only source-list transformation, builds with one job to limit build-time load, and installs the binary under `$PREFIX/bin/cpuminer-minotaurx`. It then prompts for the pool endpoint, LCC payout login, pool password, thread count, and duty-cycle settings. This draft increments the build marker to version 2 so an existing installation created by the earlier launcher rebuilds once and receives the updated startup banner.

The launcher stores configuration at `~/.config/termux-minotaurx/config.json` with directory/file permissions restricted to the current Termux user where supported. It creates a temporary mode-`600` runtime config and removes that temporary file on exit. The saved pool password is still stored as plain text in the private config file. Never enter a seed phrase, private key, exchange password, or API key.

The miner itself reports hash rate, mining/pool status, and share messages. The launcher prints duty-cycle pauses and an OS load-average snapshot when it pauses. It **does not read CPU temperature, thermal headroom, memory use, or Android thermal status** and cannot guarantee a safe temperature.

The duty percentage is the launcher's own process pause schedule. For example, `20` means roughly 12 seconds active and 48 seconds paused in each 60-second cycle. Setting `duty_percent` to `100` disables those scheduled pauses. It does not override Android thermal throttling, power management, or process-killing policies.

The upstream command-line help may still list other algorithms because the upstream dispatcher is retained. Their scanner implementations are not included in this build; generated link-only stubs return failure. The supported mining mode of this project is MinotaurX. Do not select another algorithm.

## Install in Termux

1. Install Termux from an official source. Android 7 or newer is the documented Termux app-and-package baseline. Keep Termux and its add-ons from the same signing source.
2. In Termux, install Git if it is not already available:

   ```sh
   pkg update -y
   pkg install -y git
   ```

3. Clone this repository and run the launcher with Bash:

   ```sh
   git clone https://github.com/NightFantomProjects/Termux-MinoMiner.git
   cd Termux-MinoMiner
   bash ./termux-minotaurx
   ```

   The launcher installs the remaining compiler and library dependencies on its first run. It does not require root access.

If you downloaded the single script through Android's shared `Downloads` folder, copy it into Termux's private home and invoke Bash rather than executing it directly from shared storage:

```sh
termux-setup-storage
cp ~/storage/downloads/termux-minotaurx ~/termux-minotaurx
bash ~/termux-minotaurx
```

This avoids common `Permission denied` errors caused by Android shared storage mount permissions. You do not need to `chmod` the file when launching it as `bash path/to/file`.

## Configuration

On first launch, answer the prompts. Use a pool that explicitly supports **Litecoin Cash MinotaurX**. The miner does not choose a pool or wallet for you. Pool details, ports, login formats, and passwords are pool-specific.

Edit the saved settings later with:

```sh
nano ~/.config/termux-minotaurx/config.json
```

Example only—replace every placeholder with details from a pool you independently trust:

```json
{
  "algo": "minotaurx",
  "url": "stratum+tcp://POOL_HOST:POOL_PORT",
  "user": "YOUR_LCC_WALLET.WORKER_NAME",
  "pass": "x",
  "threads": 2,
  "duty_percent": 50,
  "cycle_seconds": 60
}
```

The installer defaults are 8 threads, 20% duty, and a 60-second cycle. A device with fewer than the configured number of online CPUs will be rejected by the launcher; reduce `threads` if needed. For a continuous launcher schedule, use `"duty_percent": 100`. Restart the launcher after editing the file. Keep the configuration file private; its pool password is not encrypted.

To stop the launcher, press **Ctrl+C** in its Termux session.

## Mining command examples

The installed upstream-compatible binary accepts the conventional cpuminer flags. This direct example uses placeholders and is for syntax reference; it bypasses the launcher's duty-cycle wrapper, and the pool password may be visible in process listings. Prefer the launcher's private config for routine use.

```sh
cpuminer-minotaurx \
  --algo minotaurx \
  --url stratum+tcp://POOL_HOST:POOL_PORT \
  --user YOUR_LCC_WALLET.WORKER_NAME \
  --pass x \
  --threads 2
```

Short options are also supported by upstream cpuminer: `-a`, `-o`, `-u`, `-p`, and `-t` respectively. The worker name is commonly appended to the payout login as `.WORKER_NAME`, but follow the selected pool's instructions. This project has **not** tested a live pool or confirmed any accepted shares.

A local benchmark does not need a pool or wallet:

```sh
cpuminer-minotaurx --algo minotaurx --benchmark --threads 1 --time-limit 10
```

Benchmark results vary by processor, compiler, thermal state, and power policy. They are not a prediction of mining income.

## Build from source

The installer pins the upstream [Litecoin Cash `cpuminer-multi`](https://github.com/litecoincash-project/cpuminer-multi) source at commit `5710e52b064d2077a53d3db3ae6b1a7febbec45b` (2021-05-24). The source tree's `LICENSE` identifies GPL version 2 and `COPYING` contains the full license text. The tested build uses the upstream source at that exact revision.

The automated installer adds these Termux packages:

```sh
pkg install -y git clang make autoconf automake libtool pkg-config openssl libcurl zlib python
```

It then edits the upstream `Makefile.am` source list to keep the pool/miner core, MinotaurX's required SPH hash primitives, `algo/minotaur.c`, and yespower sources. It generates link-only scanner stubs for the legacy dispatcher and replaces the optional all-algorithm hash-test routine, which otherwise creates references to excluded hash implementations. It also replaces only the printed voluntary donation address in the upstream startup banner; see [`NOTICE.md`](NOTICE.md). It does not copy upstream algorithm files into this repository; the installer fetches them at build time and retains their upstream copyright/license headers in the local source checkout.

After the source preparation performed by the launcher, the build commands are:

```sh
cd "$HOME/.local/share/termux-minotaurx/source-5710e52b064d"
./autogen.sh
CFLAGS="-O2" ./configure --disable-assembly --with-crypto --with-curl
make -j1
```

The installer uses `make -j1` on Android to avoid a multi-job compilation spike. A manual build must use the launcher's source transformation exactly; compiling the unmodified upstream source will build other algorithms and is not this MinotaurX-only target. The upstream checkout has no tag for the tested state in this project; use the pinned commit rather than an unpinned branch tip.

## Android temperature, battery, and background behavior

Sustained CPU mining can heat the phone, trigger system thermal throttling, consume battery rapidly, and accelerate battery wear. Android's thermal behavior varies by model. The Android Thermal API documentation describes thermal headroom/status monitoring, but this shell launcher does not call that API. Device temperature may not be exposed to Termux, so do not assume a missing temperature read means the phone is cool.

Start with a low thread count and a conservative duty percentage, keep the phone out of direct sun, and avoid charging while running if that causes excessive heat. Stop the miner if the device becomes hot, behaves erratically, or throttles heavily. Do not disable Android thermal protections. Mobile CPU mining may be uneconomical; no profitability is promised.

Android vendors may suspend or kill background processes, especially under battery saver or aggressive app-management modes. Keep Termux in the foreground during testing. A wake lock or exempting Termux from battery optimization may affect sleep behavior, but is device-dependent and can increase battery drain; this launcher does not configure either setting and cannot guarantee uninterrupted background mining.

## Troubleshooting

- **`Permission denied` while running the script:** copy it from shared storage into `~/` and use `bash ~/termux-minotaurx` as shown above.
- **`pkg` or package installation errors:** update Termux packages and confirm that the installed Termux source/signing channel is supported. Do not mix F-Droid and GitHub Termux app/plugin builds.
- **Build stops with an error:** capture the first compiler error and the final 40 build-log lines. Confirm that the checkout is at the pinned upstream revision. Do not substitute another algorithm source or patch arbitrary files; report the device's `uname -m`, Android version, Termux source, and package versions.
- **Pool connection or rejected shares:** confirm the pool's MinotaurX/LCC support, exact host and port, payout login format, password, and any worker-name rules. No pool has been tested by this project.
- **The launcher still pauses:** inspect `~/.config/termux-minotaurx/config.json`; `duty_percent` must be the number `100` (not a quoted string) to disable scheduled pauses. The startup message shows the value read by the launcher.
- **The phone slows or stops mining with 100% duty:** the launcher is no longer scheduling pauses, but Android may thermally throttle the CPU or stop the process. Reduce threads or stop the miner; 100% duty is not a thermal bypass.
- **The default thread count is rejected:** lower `threads` to no more than the count returned by `getconf _NPROCESSORS_ONLN`.

## Fees, donations, and destinations

The repository does not hardcode a pool endpoint, pool payout wallet, pool login, or pool password. Those are supplied by the user in Termux. The build patch changes only the upstream startup-banner voluntary donation address to `CdV1cNyBjMxBTtc2tm1ih4TMCmLvbSw8dH`, as requested by the repository owner. This printed address does not alter pool configuration or redirect mining shares. The pinned upstream revision documents voluntary donations; a source review found no automatic timed developer-fee mining redirect in that revision. See [`NOTICE.md`](NOTICE.md) for attribution and scope.

## Licensing and attribution

This project is prepared under **GPL-2.0-only** to match the pinned upstream miner's GPLv2 license. See [`LICENSE`](LICENSE). The source builder downloads the upstream repository at installation time and keeps that source tree's own copyright and license notices. See [`NOTICE.md`](NOTICE.md) for attribution and donation/fee notes. No upstream implementation source is vendored in this repository.

## Reporting issues

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for bug-report details and [`SECURITY.md`](SECURITY.md) for private vulnerability reporting.
