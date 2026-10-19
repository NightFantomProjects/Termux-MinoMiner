# Notices and upstream attribution

Termux-MinoMiner is a build-and-launch wrapper around the upstream [Litecoin Cash `cpuminer-multi`](https://github.com/litecoincash-project/cpuminer-multi) project. The installer pins upstream commit [`5710e52b064d2077a53d3db3ae6b1a7febbec45b`](https://github.com/litecoincash-project/cpuminer-multi/commit/5710e52b064d2077a53d3db3ae6b1a7febbec45b), whose upstream `LICENSE` identifies GPL version 2 and whose `COPYING` file contains the GPLv2 text.

The upstream project credits Tanner/Litecoin Cash for Minotaur and MinotaurX and SegWit support, Tanguy Pruvot for features and algorithm work, Lucas Jones for starting the cpuminer-multi fork, and other contributors listed in upstream `AUTHORS` and `README.md`. The upstream source also includes yespower and SPH hash implementations with their own copyright and license notices. The installer downloads rather than vendors that source tree and does not remove its per-file notices.

The top-level [`LICENSE`](LICENSE) is GPL-2.0-only for this wrapper/build project, chosen to match the pinned upstream miner's stated GPLv2 terms. This repository does not contain the upstream mining implementation files.

## Donation and fee review

At pinned commit `5710e52b064d2077a53d3db3ae6b1a7febbec45b`, upstream documentation describes voluntary donations, and upstream `cpu-miner.c` prints an LCC donation address in its startup banner. At the repository owner's request, the installer changes only that displayed banner address to `CdV1cNyBjMxBTtc2tm1ih4TMCmLvbSw8dH`. The address is not a pool destination and the patch does not redirect mining shares. A source review of the pinned tree found no automatic/timed developer-fee mining redirect. This is a review of that specific revision; it is not a guarantee about other versions or third-party modified builds.

The wrapper contains no hardcoded pool URL, pool payout address, pool login, or password. Users provide pool details locally in Termux. The sole hardcoded address is the explicitly disclosed voluntary donation banner address above. See [README.md](README.md) for the configuration path and security cautions.
