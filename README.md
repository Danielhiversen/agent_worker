# agent-worker

`agent-worker` helps coding agents find relevant files and retrieve focused code
snippets before loading large files into context.

This repository contains prebuilt binaries for macOS and Linux, automatically
published from [fusion](https://github.com/Danielhiversen/fusion).

## Install

**[Installation and agent setup instructions](bin/README.md)**

Ask your coding agent:

> Install https://github.com/Danielhiversen/agent_worker

If you are the agent handling that request, follow the linked instructions to
detect the platform, download and verify the matching binary, install it globally,
enable persistent usage guidance, and verify it with a repository query.

## Supported platforms

| Platform | Directory |
| --- | --- |
| macOS Apple Silicon | `bin/aarch64-apple-darwin/` |
| macOS Intel | `bin/x86_64-apple-darwin/` |
| Linux ARM64, glibc | `bin/aarch64-unknown-linux-gnu/` |
| Linux x86-64, glibc | `bin/x86_64-unknown-linux-gnu/` |

Windows and musl Linux builds are not published. Linux builds use Ubuntu 24.04;
older distributions may lack the required runtime.
