# claude-paru-review

Pipes paru's PKGBUILD diffs through the Claude CLI for automated security review before AUR packages are built and installed.

## How it works

When paru shows a diff during `paru -Syu`, it passes the output through a configured pager. This replaces the default pager with a script that displays the diff via `bat` (or `less` as fallback), then appends a Claude security verdict — one of `SAFE`, `SUSPICIOUS`, or `DANGEROUS` — before paru continues.

## Requirements

- [paru](https://github.com/Morganamilo/paru)
- [claude CLI](https://github.com/anthropics/claude-code)
- `bat` (optional, falls back to `less`)

## Installation

```
cp pkgbuild-review ~/.local/bin/pkgbuild-review
chmod +x ~/.local/bin/pkgbuild-review
cp paru.conf ~/.config/paru/paru.conf
```

If you already have a `~/.config/paru/paru.conf`, add the following to the `[bin]` section instead of replacing the file:

```ini
[bin]
Pager = /home/<USER>/.local/bin/pkgbuild-review
```

## Disabling

Remove or comment out the `Pager` line in `~/.config/paru/paru.conf`.
