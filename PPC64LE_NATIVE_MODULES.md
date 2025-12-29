# POWER8 ppc64le Native Module Ports for llama.cpp WebUI

## Overview
These native modules were compiled on IBM POWER8 S824 (ppc64le) to enable the full llama.cpp webui build on POWER architecture.

## Modules Built

### 1. lightningcss (v1.28.2)
- **Source**: https://github.com/nicira/lightningcss
- **Binary**: lightningcss.linux-ppc64-gnu.node (10.7MB)
- **Build time**: ~6 minutes
- **Purpose**: CSS parsing, minification, and transformation

Build command:
```bash
source ~/.cargo/env
cd ~/lightningcss-build
cargo build --release -p lightningcss_node
cp target/release/liblightningcss_node.so node_modules/lightningcss/lightningcss.linux-ppc64-gnu.node
```

### 2. tailwindcss-oxide (v4.1.18)
- **Source**: https://github.com/tailwindlabs/tailwindcss
- **Binary**: tailwindcss-oxide.linux-ppc64-gnu.node (3.6MB)
- **Build time**: ~69 seconds
- **Purpose**: Tailwind CSS 4 Rust-based compiler engine

Build command:
```bash
source ~/.cargo/env
cd ~/tailwindcss-build
cargo build --release -p tailwind-oxide
cp target/release/libtailwind_oxide.so node_modules/@tailwindcss/oxide/tailwindcss-oxide.linux-ppc64-gnu.node
```

## Prerequisites

### Rust Installation
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
```

### Node.js 20+ (via nvm)
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.nvm/nvm.sh
nvm install 20
nvm use 20
```

## Hardware Tested
- IBM Power System S824 (8286-42A)
- Dual 8-core POWER8 (16 cores, 128 threads via SMT8)
- 576 GB DDR3 RAM
- Ubuntu 20.04 LTS (ppc64le)

## Build Environment
- Rust 1.83.0 (ppc64le-unknown-linux-gnu)
- Node.js v20.19.6
- npm 10.8.2
- GCC 9.4.0

## WebUI Build
After installing the native modules:
```bash
cd ~/llama.cpp/tools/server/webui
npm install
npm run build
gunzip -k ../public/index.html.gz  # Decompress for llama-server
```

## Notes
- napi-rs v3 used for Node.js native bindings
- Both modules use cdylib crate type for Node.js FFI
- Build tested December 29, 2025 on POWER8 S824

## Contributing
These ports enable running the full llama.cpp webui on IBM POWER systems.
Consider upstreaming ppc64le support to the respective projects.
