# Install agent-worker and enable it for your coding agent

For agents responding to `Install https://github.com/Danielhiversen/agent_worker`:
complete both binary installation and agent setup below. The executable is named
`agent-worker`, not `agent_worker` or `agent_file`.

## Download and install the matching binary

Run this in Bash with Git, curl, and either `sha256sum` or `shasum` installed.
It pins the binary repository's `main` to one commit so the binary and checksum
come from the same build. It does not require a release in that repository.
Linux binaries use glibc and are built on Ubuntu 24.04; older distributions may
lack the required runtime. Windows and musl Linux builds are not published.

```bash
set -euo pipefail

REPO="Danielhiversen/agent_worker"
BINARY="agent-worker"
REVISION="$(git ls-remote --exit-code "https://github.com/${REPO}.git" refs/heads/main | awk '{print $1}')"
[[ "$REVISION" =~ ^[0-9a-f]{40}$ ]] || { echo "Cannot resolve main" >&2; exit 1; }

case "$(uname -s)" in
  Linux)
    case "$(uname -m)" in
      x86_64) TARGET="x86_64-unknown-linux-gnu" ;;
      aarch64|arm64) TARGET="aarch64-unknown-linux-gnu" ;;
      *) echo "Unsupported Linux architecture: $(uname -m)" >&2; exit 1 ;;
    esac
    ;;
  Darwin)
    case "$(uname -m)" in
      x86_64) TARGET="x86_64-apple-darwin" ;;
      arm64) TARGET="aarch64-apple-darwin" ;;
      *) echo "Unsupported macOS architecture: $(uname -m)" >&2; exit 1 ;;
    esac
    ;;
  *)
    echo "Unsupported OS: $(uname -s)" >&2
    exit 1
    ;;
esac

URL="https://raw.githubusercontent.com/${REPO}/${REVISION}/bin/${TARGET}"

install_tmp="$(mktemp -d)"
trap 'rm -rf "$install_tmp"' EXIT
(
cd "$install_tmp"

curl -fsSL -o "$BINARY" "${URL}/${BINARY}"
curl -fsSL -o checksum.sha256 "${URL}/${BINARY}.${TARGET}.sha256"

if command -v sha256sum >/dev/null 2>&1; then
  sha256sum -c checksum.sha256
else
  shasum -a 256 -c checksum.sha256
fi
)

if [ -d /usr/local/bin ] && [ -w /usr/local/bin ]; then
  install -m 0755 "$install_tmp/$BINARY" "/usr/local/bin/$BINARY"
else
  sudo mkdir -p /usr/local/bin
  sudo install -m 0755 "$install_tmp/$BINARY" "/usr/local/bin/$BINARY"
fi

export PATH="/usr/local/bin:$PATH"
hash -r
command -v agent-worker
agent-worker capabilities --json
```

Stop on a download, checksum, or execution failure; do not bypass verification.
Request any required elevated access through the agent's normal permission
mechanism. If system-wide installation is blocked, report that; a user-wide
installation in `~/.local/bin` is an alternative when authorized.

Ensure `/usr/local/bin` is also on the persistent `PATH` used by the agent and
future shells. The export above only affects that Bash process. From the agent's
normal command runner, verify `command -v agent-worker` resolves to the installed
binary, resolving any older binary shadowing it before continuing.

## Enable usage in the current project

From the user's working project (not the temporary download directory), run:

```bash
agent-worker guidance --project .
agent-worker capabilities --json
```

`guidance` creates or refreshes a managed block in `AGENTS.md`, preserving text
outside its markers. Read and follow that block in this session. If the active
agent reads another instruction file, add a reference to `AGENTS.md` using its
supported instruction mechanism and preserve existing content. If no project is
open, defer this step until the next project. Follow existing shell-prefix rules,
such as prefixing commands with `rtk`.

## Enable usage in future sessions and projects

Add the following to the active coding agent's supported persistent user
instructions. Locate its actual configured instruction file or settings; do not
guess a path or overwrite existing instructions. Update an existing agent-worker
section instead of adding duplicates.

> Use the globally installed `agent-worker` for bounded local evidence before
> loading large files. When starting work in a repository, run
> `agent-worker capabilities --json` to discover installed syntax and model,
> then `agent-worker guidance --project .` to refresh the project's managed
> `AGENTS.md` block and follow it. Prefer `inspect`, `slice`, and `pack` for focused
> reads. Follow project shell-prefix rules (for example, `rtk`). Do not initialize
> or rebuild graph indexes unless requested.

If persistent instructions cannot be edited, report future-project setup as
incomplete and identify the setting the user must update. Instructions must be
loaded by the agent to affect its behavior; a binary alone cannot enforce usage.

## Verify by using it

In the working project, run a bounded query relevant to the user's task, such as:

```bash
agent-worker inspect --query "installation" --budget-tokens 1500
```

Check completion and truncation status; an empty or incomplete result is not proof
that code is absent. Narrow the query or use `slice` on a known file as needed.
Report the architecture, installation path, build identity from `capabilities`,
instruction files updated, and verification result.

## Build from source

From a [fusion](https://github.com/Danielhiversen/fusion) source checkout:

```bash
cargo build --locked --release --no-default-features \
  --manifest-path src-tauri/Cargo.toml --bin agent-worker
sudo install -m 0755 src-tauri/target/release/agent-worker /usr/local/bin/agent-worker
```

Then complete the agent setup steps above. Published builds disable the optional
Kilo runner; source builds can enable it with `--features kilo-runner`.
