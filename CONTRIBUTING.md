# Contributing

Thanks for helping maintain ppc64le native module build notes for llama.cpp
WebUI. This repository documents POWER8 builds of Rust-backed Node native
modules, so reproducible build evidence is the most valuable contribution.

## Getting Started

1. Read `PPC64LE_NATIVE_MODULES.md` for the built module versions and install
   paths.
2. Read `POWER8_WEBUI_BUILD_GUIDE.md` before changing build instructions.
3. Work on a focused branch:

   ```bash
   git checkout -b your-change-name
   ```

## Development Workflow

Keep changes scoped to one area:

- Native module build instructions for `lightningcss`.
- Native module build instructions for `tailwindcss-oxide`.
- llama.cpp WebUI integration notes.
- POWER8/ppc64le environment documentation.

Avoid mixing new module claims with unrelated formatting cleanup. If a binary or
version changes, include how it was built and where it was installed.

## Validation

For build instruction changes, include:

- POWER system model and OS.
- Rust, Node.js, npm, GCC, and Cargo versions.
- Exact `cargo build` and `npm run build` commands.
- Build output or failure logs.
- Whether the generated `.node` file loaded in the llama.cpp WebUI.

If you cannot test on ppc64le hardware, state that clearly and include static
validation of commands, paths, and upstream version references.

## Documentation Guidelines

- Keep architecture labels explicit: `ppc64le`, `powerpc64le-unknown-linux-gnu`,
  and POWER8 where applicable.
- Record module versions and source repositories.
- Do not claim upstream support unless the module officially ships ppc64le
  binaries.
- Include exact destination paths for copied `.node` artifacts.

## Pull Request Checklist

Before opening a PR, include:

- Summary of the module or WebUI area affected.
- Commands run and outputs.
- Hardware/software environment.
- Known limitations or untested steps.

