# Pilot 001 — Local-first adult animation on the existing RDNA1 workstation

**Date:** 2026-11-11  
**Stage:** design / unverified experiment  
**Hardware spending:** €0  
**Objective:** determine whether a small, fully local pipeline can deliver a convincing original 20–30 second scene; then decide whether a 2–5 minute pilot is worth producing.

This document captures **proposed tests**, not test results. The host's Linux distribution, driver versions, storage space and existing software installation are unknown.

## 1. Target output and constraints

A short original adult-oriented animation, independent of a specific existing show's visual style:
- two recognizable, recurring adult fictional characters;
- two camera angles or more, one reusable setting;
- at least one spoken exchange in Italian with audible, synchronized dialogue;
- at least one complex visual/action beat (may be stylized horror or graphic violence);
- basic sound design, titles/credits and one mastered video export;
- original characters and assets; no unauthorized imitations of real people's appearance or voices.

**Deliverable A:** 20–30 s proof of production, 1920×1080 final container (source images can be smaller; do not claim native AI 1080p), with a frame cadence selected for the style (e.g. 12 distinct poses/s with 24 fps delivery).  
**Deliverable B (conditional):** 2–5 minute pilot only once A is practical and repeatable.

**Not in scope at first:** all-generative continuous video, MiniMax H3 locally, real-time generation, model training, automatic full-episode production, or purchasing replacement hardware.

## 2. Available machine

| Item | Known | Unknown / check |
| --- | --- | --- |
| CPU | AMD Ryzen 5 3600, 6 cores / 12 threads | sustained thermal/performance behavior |
| GPU | Radeon RX 5700 XT, RDNA1/gfx1010, 8 GB VRAM | Vulkan/Mesa versions, application support |
| Memory | 32 GB DDR4 | free memory under actual workload |
| OS | Linux | distribution/version, kernel |
| Storage | reused existing disks | free space, filesystem, scratch speed |
| Audio | existing output devices | microphone/recording quality |

**Compatibility policy.** AMD's official ROCm Radeon product matrices do not list RX 5700 XT. TheRock community build tables include gfx1010, but that does **not** establish supported PyTorch/ComfyUI performance or reliability on this machine. We start with the Vulkan backend supported by stable-diffusion.cpp and keep CPU-only fallback. Do not change installed system GPU drivers just to attempt an unverified ROCm stack.

Primary references:
- [AMD ROCm official system requirements](https://github.com/ROCm/rocm-install-on-linux/blob/develop/docs/reference/system-requirements.rst)
- [TheRock community GPU support table](https://github.com/ROCm/TheRock/blob/main/SUPPORTED_GPUS.md)
- [stable-diffusion.cpp build instructions](https://github.com/leejet/stable-diffusion.cpp/blob/master/docs/build.md)
- [stable-diffusion.cpp CLI options](https://github.com/leejet/stable-diffusion.cpp/blob/master/examples/cli/README.md)

## 3. Phase 0 — Inventory and environment (read-only)

Run on the **target Linux workstation**, not a pod or production machine:

```bash
cat /etc/os-release
uname -r
lscpu | sed -n '1,24p'
free -h
df -h
lspci -nnk | grep -A4 -Ei 'VGA|Display|3D'
command -v vulkaninfo && vulkaninfo --summary || true
command -v ffmpeg && ffmpeg -version | head -n 1 || true
```

Record OS/kernel, Mesa/Vulkan driver, free disk and GPU visibility. Do not paste hostnames, internal IPs, private keys or unrelated confidential configuration into public GitHub logs.

**Gate 0:** the desktop works normally, GPU driver is loaded, and Vulkan can enumerate the RX 5700 XT. If Vulkan is absent, address the package/driver prerequisite before any model tests.

## 4. Phase 1 — First local image via Vulkan

Example for an **APT-based Linux distribution**; adapt package names to actual distro. No commands below have been run on the host.

```bash
sudo apt update
sudo apt install -y git cmake ninja-build build-essential \
  libvulkan-dev mesa-vulkan-drivers vulkan-tools glslc spirv-headers

mkdir -p ~/kleo-studio-local/{models,renders,scratch}
cd ~/kleo-studio-local
git clone --recursive https://github.com/leejet/stable-diffusion.cpp.git
cmake -S stable-diffusion.cpp -B stable-diffusion.cpp/build-vulkan \
  -DSD_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
cmake --build stable-diffusion.cpp/build-vulkan -j 6
./stable-diffusion.cpp/build-vulkan/bin/sd-cli --help
```

Obtain a **legally suitable** SD 1.5-compatible checkpoint in `.safetensors` format and save it **outside the Git repository**, e.g. `~/kleo-studio-local/models/pilot.safetensors`. Start with a 512×512 image to isolate basic compatibility. The license must be checked against the intended content (including graphic violence) and later commercial distribution before relying on it.

```bash
cd ~/kleo-studio-local
./stable-diffusion.cpp/build-vulkan/bin/sd-cli \
  -m ./models/pilot.safetensors \
  -p "original stylized adult cartoon character, full body, plain background, clean shapes" \
  -W 512 -H 512 --steps 20 --seed 42 \
  -o ./renders/first-character.png
```

If the CLI rejects an option, use `--help` and the upstream CLI documentation: the project changes frequently. Verify in the log that **Vulkan actually ran on the GPU**, not just that an output image exists. Save timing, VRAM use, warnings and exact git commit hash.

**Gate 1:** at least one correct image, repeatable with fixed seed, with measured elapsed time and no GPU hang. If VRAM exhaustion occurs, try a smaller/checkpoint-compatible model, smaller image or lower-memory options **only after checking actual CLI support**. CPU fallback is acceptable to establish a functional baseline, not an assumption about usable throughput.

## 5. Phase 2 — Tiny production pipeline

| Step | First experiment | Exit criterion |
| --- | --- | --- |
| Concept | one-page scene outline | clear beats and dialogue |
| Visual identity | 2 character sheets + 1 environment | recognizable characters across viewpoints |
| Asset separation | layered heads/bodies/mouths/effects in Krita | reusable assets without re-generation |
| Spoken dialogue | record original Italian voices; optional local Piper | clean editable WAVs with permissions |
| Mouth synchronization | Rhubarb Lip Sync on WAV | timing cues inspected and corrected manually |
| Animation | Blender 2D or OpenToonz: keyframes/cutout/compositing | coherent motion in 2+ shots |
| Audio/edit | effects + Kdenlive/FFmpeg | one final 20–30 s MP4 |

For Rhubarb, a CLI example (after installing a Linux release from upstream):

```bash
rhubarb -o mouth-cues.json -f json scene-dialogue.wav
```

Prefer recording original voices for the pilot; TTS is optional. Piper supports Italian voices, but the **engine and individual voice models may have different licenses**. Validate them before release. Do not clone an identifiable voice without permission.

The deliberately difficult effects/action beat should be built first from layered assets and compositing. Short AI-generated video tests are optional experiments **after** a working scene exists. No claim that Wan/H3/AnimateDiff is practical on RX 5700 XT.

## 6. Measure quality and economics

For each experiment capture:

| Field | Notes |
| --- | --- |
| Test ID / date | unique identifier |
| Host OS/kernel/Mesa/Vulkan | exact versions |
| Engine/model/revision | with source and license |
| Seed/prompt/workflow | reproducible inputs |
| Resolution, steps, settings | as executed |
| Total elapsed time | wall clock |
| Peak RAM / VRAM | if measurable |
| GPU power/energy | only if measurable |
| Output quality | coherence, anatomy, artifacts, lip sync |
| Manual rework | minutes of operator editing |
| Pass/fail and next action | explicit decision |

**Go/no-go after Deliverable A**:
- coherent 20–30 second finished scene rather than a slideshow;
- recognizably recurring characters, intelligible audio and acceptable lip-sync;
- repeatable asset and rendering workflow with recorded timings;
- no reliance on restricted/unlicensed weights or unconsented real-person likenesses;
- an estimate of **operator hours per finished minute**, render hours per finished minute and disk use.

Only then budget a longer pilot, alternate models or GPU upgrade.

## 7. Repository hygiene and next decisions

- Keep model weights, datasets, media, caches, exports and other large generated files **local**; Git tracks docs, small config and reusable code only.
- Once source files exist, document a stable local directory layout and backup plan. Do not synchronize private media or voice datasets to a public repo by accident.
- Record distro, Vulkan status and exact SD checkpoint license before choosing a full workflow.
- Update this pilot document with test results instead of creating multiple overlapping planning issues.

## Source projects

- [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp)
- [Krita](https://krita.org/)
- [Blender](https://www.blender.org/)
- [OpenToonz](https://github.com/opentoonz/opentoonz)
- [Piper](https://github.com/OHF-Voice/piper1-gpl)
- [Rhubarb Lip Sync](https://github.com/DanielSWolf/rhubarb-lip-sync)
- [Kdenlive](https://kdenlive.org/)
- [FFmpeg](https://ffmpeg.org/)
