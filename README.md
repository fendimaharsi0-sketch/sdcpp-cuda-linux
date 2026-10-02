# sdcpp-cuda-linux

Portable prebuilt `sd-cli` ([stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp))
for Linux x86_64 with CUDA — built via GitHub Actions, meant to be downloaded
straight into Google Colab (or any Linux box with an NVIDIA GPU).

## Why not AppImage?

AppImage needs FUSE to mount itself, which container environments like Colab
typically don't have. A plain tarball needs nothing: no FUSE, no root.

## What's in the tarball

- `bin/sd-cli` — the built binary
- `lib/` — bundled CUDA **runtime** libs (`libcudart`, `libcublas`, `libcublasLt`)
- `sd-cli.sh` — launcher that points `LD_LIBRARY_PATH` at `lib/` and forwards args

`libcuda.so` is intentionally **not** bundled — it must come from the host
NVIDIA driver. That's fine: builds against CUDA 12.x run on any newer driver
(NVIDIA forward compatibility), and building on ubuntu-22.04 (glibc 2.35)
keeps it working across OS upgrades.

## Colab quickstart

```bash
wget -q https://github.com/fendimaharsi0-sketch/sdcpp-cuda-linux/releases/download/sdcpp-master-929-3f8527a-cuda12.6/sdcpp-sd-cli-cuda12.6-ubuntu22.04-x86_64.tar.gz
tar xzf sdcpp-sd-cli-cuda12.6-ubuntu22.04-x86_64.tar.gz
./sd-cli.sh --help
```

## Rebuild

Actions → "Build sd-cli (CUDA, Linux x86_64)" → Run workflow.
Pinned sd.cpp tag and CUDA settings live at the top of
`.github/workflows/build-cuda-linux.yml`.
