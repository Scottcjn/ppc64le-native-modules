# Building llama.cpp WebUI on IBM POWER8 (ppc64le)

## Session Summary - December 29, 2025

Successfully built and deployed the full llama.cpp webui on IBM POWER8 S824 with Elyan Labs branding. This required porting two Rust-based native modules that didn't have ppc64le binaries.

## What Was Done

### 1. Native Module Compilation

The llama.cpp webui uses Vite + Svelte + TailwindCSS 4, which requires two Rust-based native Node.js modules:

| Module | Version | Size | Build Time |
|--------|---------|------|------------|
| lightningcss | 1.28.2 | 10.7MB | ~6 min |
| tailwindcss-oxide | 4.1.18 | 3.6MB | ~69 sec |

### 2. Elyan Labs Rebranding

Applied custom branding to the webui:
- Title: "Elyan Labs - Sophia"
- Custom flame favicon (SVG data URL)
- Database name: ElyanLabsDatabase
- LocalStorage prefix: ElyanLabs

### 3. Server Configuration

Running with full PSE (Proto-Sentient Emergence) optimizations:
```bash
numactl --interleave=all ~/llama.cpp/build-pse-collapse/bin/llama-server \
  -m ~/models/gpt-oss-20b-Q4_K_M.gguf \
  --host 0.0.0.0 --port 8080 -t 64 -a Sophia-20B -c 8192 \
  --jinja --chat-template-file ~/sophia_chat_template.jinja \
  --path ~/llama.cpp/tools/server/public
```

## Build Process

### Prerequisites

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env

# Install Node.js 20 via nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.nvm/nvm.sh
nvm install 20
nvm use 20
```

### Step 1: Build lightningcss

```bash
git clone --depth 1 https://github.com/nicira/lightningcss.git ~/lightningcss-build
cd ~/lightningcss-build
cargo build --release -p lightningcss_node
```

### Step 2: Build tailwindcss-oxide

```bash
git clone --depth 1 https://github.com/tailwindlabs/tailwindcss.git ~/tailwindcss-build
cd ~/tailwindcss-build
cargo build --release -p tailwind-oxide
```

### Step 3: Install npm dependencies

```bash
cd ~/llama.cpp/tools/server/webui
npm install
```

### Step 4: Copy native modules

```bash
cp ~/lightningcss-build/target/release/liblightningcss_node.so \
   ~/llama.cpp/tools/server/webui/node_modules/lightningcss/lightningcss.linux-ppc64-gnu.node

cp ~/tailwindcss-build/target/release/libtailwind_oxide.so \
   ~/llama.cpp/tools/server/webui/node_modules/@tailwindcss/oxide/tailwindcss-oxide.linux-ppc64-gnu.node
```

### Step 5: Apply branding (optional)

```bash
# Run the rebrand script
~/rebrand_webui.sh

# Or manually edit:
# - src/lib/constants/localstorage-keys.ts
# - src/lib/services/database.ts
# - svelte.config.js (add title)
# - Create custom favicon
```

### Step 6: Build the webui

```bash
cd ~/llama.cpp/tools/server/webui
npm run build
gunzip -k ../public/index.html.gz  # Decompress for llama-server
```

## File Locations

| File | Location |
|------|----------|
| lightningcss native | `node_modules/lightningcss/lightningcss.linux-ppc64-gnu.node` |
| tailwindcss-oxide native | `node_modules/@tailwindcss/oxide/tailwindcss-oxide.linux-ppc64-gnu.node` |
| Built webui | `~/llama.cpp/tools/server/public/` |
| Rebrand script | `~/rebrand_webui.sh` |

## Performance

Build times on POWER8 S824 (16 cores, 576GB RAM):
- lightningcss Rust compilation: 6 minutes
- tailwindcss-oxide Rust compilation: 69 seconds
- WebUI Vite build: 1 minute 9 seconds

## Accessing the UI

- Local: http://192.168.0.50:8080
- Tailscale: http://100.94.28.32:8080
- PostMath Router: port 8090 for MoE synthesis

## Dependencies

```
Rust 1.83.0 (ppc64le-unknown-linux-gnu)
Node.js v20.19.6
npm 10.8.2
GCC 9.4.0
napi-rs 3.x
```

## Troubleshooting

### "Cannot find native binding" error
Ensure the .node files are copied to the correct locations with the exact filenames:
- `lightningcss.linux-ppc64-gnu.node`
- `tailwindcss-oxide.linux-ppc64-gnu.node`

### 404 on root URL
Decompress the index.html.gz:
```bash
cd ~/llama.cpp/tools/server/public
gunzip -k index.html.gz
```

### Build fails with memory error
POWER8 has 576GB RAM, but ensure you're using appropriate thread count:
```bash
cargo build --release -j 16  # Limit to 16 parallel jobs
```
