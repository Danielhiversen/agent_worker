# agent-worker binaries

Platform-specific agent-worker binaries are stored under `bin/<architecture>/`.

## Install

Each architecture folder contains:

- `agent-worker`
- `agent-worker.<architecture>.sha256`

For each architecture, use the corresponding binary and checksum file in that folder:
`bin/<architecture>/agent-worker`.

## Available architectures

### aarch64-apple-darwin

```bash
sudo install -m 0755 "bin/aarch64-apple-darwin/agent-worker" /usr/local/bin/agent-worker
shasum -a 256 -c "bin/aarch64-apple-darwin/agent-worker.aarch64-apple-darwin.sha256"
```

### aarch64-unknown-linux-gnu

```bash
sudo install -m 0755 "bin/aarch64-unknown-linux-gnu/agent-worker" /usr/local/bin/agent-worker
shasum -a 256 -c "bin/aarch64-unknown-linux-gnu/agent-worker.aarch64-unknown-linux-gnu.sha256"
```

### x86_64-apple-darwin

```bash
sudo install -m 0755 "bin/x86_64-apple-darwin/agent-worker" /usr/local/bin/agent-worker
shasum -a 256 -c "bin/x86_64-apple-darwin/agent-worker.x86_64-apple-darwin.sha256"
```

### x86_64-unknown-linux-gnu

```bash
sudo install -m 0755 "bin/x86_64-unknown-linux-gnu/agent-worker" /usr/local/bin/agent-worker
shasum -a 256 -c "bin/x86_64-unknown-linux-gnu/agent-worker.x86_64-unknown-linux-gnu.sha256"
```
