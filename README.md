# Kleo Studio

Local-first experimental animation studio powered by open-weight AI models and conventional animation tools.

Kleo Studio explores the creation of **original adult-oriented animated stories**, including dark comedy, horror, satire and stylized graphic violence. The aim is to build a repeatable production workflow and eventually a short pilot, **without purchasing hardware or depending on hosted generative APIs**.

## Pilot 001 — existing Linux workstation

| Component | Available hardware |
| --- | --- |
| OS | Linux (distribution/version to be recorded) |
| CPU | AMD Ryzen 5 3600 — 6C/12T |
| GPU | AMD Radeon RX 5700 XT — 8 GB VRAM (RDNA1 / gfx1010) |
| RAM | 32 GB DDR4 |
| Storage | Capacity / filesystem to be measured |
| New hardware budget | **€0** |

**First deliverable:** one original, coherent **20–30 second animated scene** with two recurring characters, dialogue, synchronized mouths, sound effects and one visually demanding action/effects beat. Extend to a **2–5 minute pilot** only after the short scene passes review.

This is a **hybrid animation pipeline**, not a promise that a modern text-to-video model will run at usable speed on this GPU.

## Working principles

1. **Reuse before buying.** No GPU upgrade, paid cloud workflow or large cluster until actual bottlenecks and output quality are measured.
2. **Vulkan first.** RX 5700 XT is not in AMD's standard officially supported Radeon ROCm product list. Test `stable-diffusion.cpp` with its Vulkan backend before experimental ROCm/PyTorch paths. Community TheRock support does not guarantee application compatibility.
3. **Short-scene first.** Images/character sheets → layered assets → rig/keyframes/lip-sync → audio → editing/export. Optional AI video clips only after a reliable baseline exists.
4. **Original IP.** Use original characters and properly licensed assets/models/voices. Track source, license and usage terms, including restrictions relevant to violent material.
5. **Measure.** Record generation time, memory use, failures, consistency, manual minutes per usable shot and storage consumption; don't claim benchmarks before testing.
6. **Local/private by default.** Model weights, datasets, renders and large binaries remain off Git.

## Planned toolchain

| Stage | First-choice tool | Status |
| --- | --- | --- |
| Still-image inference | [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp), Vulkan + a legally compatible SD 1.5 checkpoint | **First experiment; not yet validated on host** |
| Character/asset editing | [Krita](https://krita.org/) | Candidate |
| Animation/compositing | [Blender](https://www.blender.org/) or [OpenToonz](https://github.com/opentoonz/opentoonz) | Candidate |
| Local Italian dialogue | Human recording first; [Piper](https://github.com/OHF-Voice/piper1-gpl) optional (verify *voice-model* license) | Candidate |
| Mouth timing | [Rhubarb Lip Sync](https://github.com/DanielSWolf/rhubarb-lip-sync) | Candidate |
| Video edit/export | [Kdenlive](https://kdenlive.org/) + [FFmpeg](https://ffmpeg.org/) | Candidate |

*MiniMax H3, Wan, ComfyUI/ROCm and fully AI-generated video are research items, **not baseline dependencies** for this workstation.* Some model licenses have regional, commercial, or content restrictions; open weights do not imply unrestricted use.

## Documentation

- [Pilot 001: setup, experiment plan, acceptance criteria](docs/pilot-001.md)

## Status

**Planning only (2026-11-11).** No software installation, GPU inference, benchmark or finished animation has been performed or verified on the target machine.

License of this repository's **code and original studio assets** is not yet selected; licenses of external tools/models remain with their respective owners.
