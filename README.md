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

## Proof of concept: Qwen-Image-2.1 on free Colab T4

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/fendimaharsi0-sketch/sdcpp-cuda-linux/blob/main/Qwen-Image-2.1-sdcpp_colab-t4.ipynb)

[`Qwen-Image-2.1-sdcpp_colab-t4.ipynb`](Qwen-Image-2.1-sdcpp_colab-t4.ipynb)
runs Qwen-Image-2.1 on a free Colab Tesla T4 with this binary — no torch,
pure `sd-cli`. It downloads official weights (Comfy-Org INT8-convrot unet,
Qwen3-VL-8B-Instruct Q4_K_M text encoder, texture-fix bf16 VAE) plus the Viggle 6-step
turbo LoRA, and generates 1024px images in ~1.5 min on a T4. The VAE dropdown
in cell ④ offers a texture-fix decoder variant (default, removes checkerboard
artifacts) or the stock VAE.

> The recipe (flags, turbo sigmas, VRAM fit) was verified on a Colab T4
> with these official weights (txt2img and image editing), using the
> default INT8-convrot configuration. The GGUF Q5_0 alternative has not
> been GPU-tested.

## Rebuild

Actions → "Build sd-cli (CUDA, Linux x86_64)" → Run workflow.
Pinned sd.cpp tag and CUDA settings live at the top of
`.github/workflows/build-cuda-linux.yml`.
