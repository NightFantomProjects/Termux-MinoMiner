# Security policy

This repository is an experimental shell installer and source builder. It has not undergone a formal security audit. Do not treat it as audited or production-secure software.

## Reporting a vulnerability

Please do not publish exploitable vulnerability details in a public issue. If GitHub private vulnerability reporting is enabled for this repository, use **Security → Report a vulnerability**. Otherwise contact the repository owner through the private contact method on the owner's GitHub profile and ask for a secure reporting channel before sending technical details. Do not send passwords, pool credentials, private keys, seed phrases, or wallet recovery material.

Include the affected file and commit, relevant environment information, impact, and safe reproduction steps. Redact pool URLs if they contain credentials and remove wallet/worker IDs from logs. Allow time for maintainers to investigate and prepare a fix before public disclosure.

## User data and local configuration

The launcher accepts pool details at runtime and saves them in `~/.config/termux-minotaurx/config.json`; the saved pool password is plain text protected only by filesystem permissions. The runtime miner config is created with mode `600` and removed when the launcher exits, but this is not encryption. Do not place secrets in issues, pull requests, logs, or screenshots.

The installer downloads source from the pinned upstream Git repository and runs its build/configure scripts. Review the script and upstream source before execution if you do not trust that code. Use only a pool endpoint you independently trust. The launcher does not add a pool by default.
