# PCForge — Complete User Guide

> Step-by-step walkthrough for building AI workstations and checking if your models will actually run.

---

## 🖱️ Getting Started

Open PCForge in your browser. You'll land on the **BUILD** tab by default.

The interface has two columns: the main area on the left with component selectors, and a sidebar on the right showing live stats (cost, AI performance, VRAM, power draw, thermal load).

Start by picking a GPU — everything else depends on it. The sidebar will update instantly.

---

## ⚒️ BUILD Tab — Component Configurator

This is the core of PCForge. You configure a complete workstation and see real-time diagnostics.

### Presets bar

At the top, four preset buttons load verified builds:
- **🟢 AI CODER** — Single RTX 5090, Ryzen 9 9800X3D. Entry AI workstation.
- **🔵 VIDEO STUDIO** — Single RTX PRO 6000 (96GB VRAM). Run any video model unquantized.
- **🟡 DUAL 5090** — Two RTX 5090s on Threadripper. 64GB total VRAM, PCIe-connected.
- **🔴 MINI SERVER** — Four RTX PRO 6000 Max-Q passive. 384GB VRAM, full NVLink.

Click a preset to load it. Click **✕ СБРОС** to clear everything.

### Component slots

Each component has a dropdown selector:

1. **GPU** — Choose from RTX 5090, RTX PRO 6000 WS, or RTX PRO 6000 Max-Q. Use the +/− buttons to set quantity (1–4). NVLink badge appears automatically when applicable.
2. **CPU** — Ryzen 9 9800X3D, Threadripper 7960X/7970X, or Core Ultra 9 285K. Socket must match your motherboard.
3. **Motherboard** — Must match your CPU socket. PCIe slot count matters for multi-GPU.
4. **RAM** — DDR5 or DDR5 ECC. Type must match what the motherboard supports.
5. **PSU** — Wattage and efficiency rating. PCForge checks if it can handle your build's power draw.
6. **Storage** — NVMe SSD options from 2TB to 8TB.

### Compatibility diagnostics

Below the components, a diagnostic panel shows:
- ✅ **OK** — configuration is compatible
- ⛔ **Error** — hard incompatibility (wrong socket, not enough PCIe slots, PSU too weak)
- ⚠️ **Warning** — will work but with limitations (PCIe bottleneck for multi-GPU, PSU at limit, RAM type mismatch)

### Sidebar stats

- **AI Performance** — estimated AI TOPS (accounts for NVLink vs PCIe multiplier)
- **Video Gen** — video generation score
- **VRAM Total** — combined VRAM across all GPUs
- **Power Draw** — total TDP of GPU + CPU
- **Thermal** — estimated thermal load percentage
- **GPU Bandwidth** — NVLink or PCIe bandwidth between GPUs (only shown for multi-GPU)

---

## 🤖 AI BENCH Tab — Model Compatibility Checker

This is PCForge's killer feature. It tells you whether specific AI models will fit on your GPU setup.

### How to use it

1. **Select your GPU** from the dropdown at the top (RTX 5090, PRO 6000 WS, or PRO 6000 Max-Q)
2. **Set quantity** with +/− buttons (1–4 cards)
3. **Toggle mode** between 🎬 Video Generation and 🧠 LLM Models
4. **For each model**, use the quantization buttons (FP16 / FP8 / Q8 / Q4) to switch precision levels
5. **Read the FIT column** — it tells you if the model fits on your setup at that quantization

### FIT badges explained

- ✅ **ВЛЕЗЕТ** — fits on a single GPU
- ⚠️ **Нужно 2×** — needs multiple GPUs
- 🔀 **Нужно N×** — needs N GPUs with NVLink
- ❌ **НЕ ВЛЕЗЕТ** — doesn't fit even on 4× PRO 6000

### Video models covered

- **Wan 2.2 14B** — text/image to video, 720p 10s clips
- **HunyuanVideo 1.5** — Tencent's model, fits on single RTX 5090 at FP16
- **LTX-2.3 22B** — fastest video gen, 4K 50fps, audio+video
- **CogVideoX-5B** — runs on 12GB, Apache 2.0 licensed

### LLM models covered

- **Llama 3.3 70B** — needs 2× PRO 6000 at FP16, fits on 1× PRO 6000 at Q4
- **Llama 3 8B** — runs on anything, 2100 tok/s at Q4 on RTX 5090
- **DeepSeek V3 (MoE)** — 671B parameters, best for coding. Needs 4× PRO 6000 at FP8.
- **Qwen 2.5 72B** — best math/code, 128K context window
- **Gemma 3 27B** — multimodal, fits on single RTX 5090 at FP8
- **Mistral 7B** — fastest small model, 2000 tok/s at Q4

### Important note for LLMs

2× RTX 5090 connected via PCIe does NOT double throughput (bottleneck at ~64 GB/s). RTX PRO 6000 with NVLink is ~1.8× faster in multi-GPU configs.

---

## 🔀 NODE VIEW Tab — System Topology

Visual SVG diagram of your BUILD configuration showing all component connections.

### What to look for

- **Solid lines** — active connections (component is selected)
- **Dashed lines** — inactive connections (component not selected)
- **Red pulsing ⚠** — bottleneck detected (e.g., multi-GPU without NVLink)
- **Connection labels** — show bus type and bandwidth (NVLink 1800GB/s, PCIe 64GB/s, DDR5-6400, etc.)
- **Red connection lines** — incompatibility (socket mismatch, overloaded PSU)

Go to BUILD first, select your components, then switch to NODE VIEW to see the diagram.

---

## 🥽 VR LAB Tab — Headset Comparison

Comparison of 5 VR headsets for creative/AI workflows:

- **Bigscreen Beyond 2** — recommended for long sessions (127g, lightest)
- **Meta Quest 3** — best price/quality balance, standalone + PCVR
- **Pimax Crystal Super** — highest resolution (3552×3840), but 860g
- **Apple Vision Pro** — Micro-OLED, but macOS only, no Steam
- **Valve Steam Frame** — expected 2026, wireless PCVR (specs TBD)

Each card shows resolution, weight, FOV, refresh rate, connectivity, and pros/cons.

---

## 📊 COMPARE Tab — Preset vs Preset

Pick two presets from dropdowns and compare them side by side across all metrics: AI TOPS, video score, VRAM, power draw, GPU bandwidth, and cost.

Click **⚒️ ЗАГРУЗИТЬ В BUILD** on any preset to load it into the configurator.

---

## 📁 PRESETS Tab — Verified Builds Gallery

Expanded view of all four presets with specs, stats, and verified reviews from real sources (MIT HAN Lab, B&H Photo, HuggingFace, Puget Systems, TechRadar).

Click **▼ ЕЩЁ** to expand additional reviews for a preset.

---

## ⌨️ Keyboard Shortcuts

PCForge does not use keyboard shortcuts. All interaction is through mouse/touch.

---

## ⚠️ Known Limitations

- **Hardware database is curated, not exhaustive** — only 3 GPUs, 4 CPUs, 3 motherboards, 4 RAM kits, 4 PSUs, 3 SSDs. Covers the AI workstation segment specifically.
- **Model list is current as of June 2026** — new models released after this date won't be included.
- **Performance numbers are estimates** — based on published benchmarks and architectural analysis, not direct testing of every combination.
- **Requires internet for React CDN** — the app loads React from cdnjs.cloudflare.com. If offline on first load, it won't work. After page loads, no further internet needed.
- **No save/export** — builds are not persisted between sessions. Take a screenshot of your config if you need to remember it.
- **Russian UI** — the interface is in Russian. This guide is in English.

---

*This guide covers PCForge v3.0.0. The app is in active development.*
