# Twip Skia Dawn

Prebuilt [skia-bindings](https://crates.io/crates/skia-bindings) for `wasm32-unknown-emscripten`, with Skia's Graphite
running on WebGPU through Dawn's emdawnwebgpu Emscripten port (and Ganesh on WebGL 2). skia-safe publishes no such
build itself.

- `skia-emdawnwebgpu.patch`: the Skia changes (see its header).
- `.github/workflows/build.yml`: builds skia-bindings from crates.io against the patched Skia and publishes a release
  `<crate version>-<commit>` with `skia-binaries.tar.gz`. The pinned versions and features are at the top.

## Using it

1. Keep `skia-safe` from crates.io at the workflow's `CRATE_VERSION`, with the features in its `FEATURES`.
2. Download and unpack `skia-binaries.tar.gz` from a release.
3. Build with `SKIA_BINARIES_URL=file://<path to skia-binaries.tar.gz>`. Linking needs emsdk at `EMSDK_VERSION` and
   `--use-port=<emdawnwebgpu_pkg>/emdawnwebgpu.port.py` from Dawn's `DAWN_TAG` release.

## Windows

`.github/workflows/build-windows.yml` builds skia-bindings for `x86_64-pc-windows-msvc` with Graphite on native Dawn
(D3D12, D3D11 and Vulkan backends) next to Graphite on Vulkan, and publishes `<crate version>-windows-<commit>`. Its
`skia-binaries.tar.gz` adds `dawn_combined.lib`, Dawn's headers (`dawn/include`) and Skia's to the binary-cache
layout. Use it the same way with the workflow's `FEATURES`; the consumer links `dawn_combined.lib` itself and calls
`dawnProcSetProcs` before using the WebGPU API.

## macOS

`.github/workflows/build-macos.yml` builds skia-bindings for `aarch64-apple-darwin` with Graphite on native Dawn (Metal
backend) next to Graphite on Vulkan, and publishes `<crate version>-macos-<commit>`. Its `skia-binaries.tar.gz` adds
`libdawn_combined.a`, Dawn's headers (`dawn/include`) and Skia's. The consumer links `libdawn_combined.a` and the
Foundation, IOSurface, IOKit, Metal, QuartzCore and Cocoa frameworks itself, and calls `dawnProcSetProcs` before using
the WebGPU API.

## Linux (musl)

`.github/workflows/build-musl.yml` builds skia-bindings for `x86_64-unknown-linux-musl` and
`aarch64-unknown-linux-musl` in Alpine 3.24 with Graphite on native Dawn (Vulkan backend) next to Graphite on Vulkan,
and publishes `<crate version>-musl-<arch>-<commit>`. Its `skia-binaries.tar.gz` adds `libdawn_combined.a`, Dawn's
headers (`dawn/include`) and Skia's. Build the consumer on Alpine with `RUSTFLAGS="-C target-feature=-crt-static"`
(skia-bindings' bindgen dlopens libclang); it links `libdawn_combined.a` itself and calls `dawnProcSetProcs` before
using the WebGPU API.
