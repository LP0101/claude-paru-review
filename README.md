# claude-paru-review

Pipes paru's PKGBUILD diffs through an LLM for automated security review before AUR packages are built and installed.

## How it works

When paru shows a diff during `paru -Syu`, it passes the output through a configured pager. This replaces the default pager with a script that displays the diff via `bat` (or `less` as fallback), then appends a security verdict — one of `SAFE`, `SUSPICIOUS`, or `DANGEROUS` — before paru continues.

The review is performed by calling an [Open WebUI](https://github.com/open-webui/open-webui) (OpenAI-compatible) `chat/completions` endpoint.

## Requirements

- [paru](https://github.com/Morganamilo/paru)
- `curl` and `jq`
- Access to an Open WebUI / OpenAI-compatible chat endpoint and an API key
- `bat` (optional, falls back to `less`)

## Configuration

The script is configured via environment variables:

| Variable            | Default                                      | Description                    |
| ------------------- | -------------------------------------------- | ------------------------------ |
| `OPENWEBUI_API_KEY` | _(none — required)_                          | Bearer token for the endpoint. |
| `OPENWEBUI_URL`     | `https://ai.popesco.io/api/chat/completions` | Chat completions endpoint URL. |
| `OPENWEBUI_MODEL`   | `gpt-oss:20b`                                | Model to use for the review.   |

If `OPENWEBUI_API_KEY` is unset (or `curl`/`jq` are missing), the diff is still
displayed and the review step is skipped gracefully.

Because paru runs the pager in your shell environment, export the key somewhere
it will be inherited — e.g. in `~/.profile` or `~/.zshenv`:

```sh
export OPENWEBUI_API_KEY="sk-..."
# optionally override the defaults:
# export OPENWEBUI_URL="https://your-host/api/chat/completions"
# export OPENWEBUI_MODEL="your-model"
```

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
