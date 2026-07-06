# PCForge 🔷

> The only PC builder that tells you whether Llama 70B will actually fit on your GPU before you spend $12,000.

**[▶ Open in browser — try it now](https://lexbayart.github.io/pcforge/)**
No install. No account. Works offline.

---

## What makes this different

Every PC builder on the market tells you if a CPU fits a motherboard. That's been solved for 20 years. PCForge does something no other tool does: it calculates VRAM requirements for specific AI models at specific quantization levels and tells you exactly which build can run them.

Select a GPU configuration, pick a model like DeepSeek V3 or HunyuanVideo 1.5, choose your quantization (FP16, FP8, Q8, Q4), and PCForge will show you if it fits — on one card, two cards, or not at all. It accounts for the real bottleneck: PCIe bandwidth between consumer GPUs versus NVLink on workstation cards. A 70B model might technically fit in 64GB across two RTX 5090s, but without NVLink the inference speed collapses. PCForge shows you that.

The visual node diagram draws your entire system topology with live bottleneck indicators — red pulsing warnings when your multi-GPU setup is constrained by PCIe instead of NVLink, or when your PSU is running past safe margins.

---

## Features

Six screens, one file:

- ⚒️ **Build View** — pick GPU (1–4×), CPU, motherboard, RAM, PSU, storage with real-time cost tracking and animated stat bars
- 🤖 **AI Bench** — test video generation models (Wan 2.2, HunyuanVideo, LTX-2.3, CogVideoX, AnimateDiff) and LLMs (Llama, DeepSeek, Qwen, Gemma, Mistral) against your GPU setup at 4 quantization levels
- 🔀 **Node View** — interactive topology diagram showing all component connections, bandwidth labels, and live bottleneck indicators
- 🥽 **VR Lab** — side-by-side comparison of VR headsets (Quest 3, Pimax Crystal Super, Apple Vision Pro, Steam Frame) with weight and resolution prioritized
- 📊 **Compare** — pit two builds against each other across all performance metrics
- 📁 **Presets** — verified builds from MIT HAN Lab, ArsenalPC, B&H Photo, with real community reviews linked to sources

Plus a MacBook Pro M5 Max reference preset so you can see exactly what you'd gain by building a dedicated AI workstation.

---

## Who it's for

PCForge was built for a specific person: a MacBook user who films, does AI video generation (ComfyUI, Wan 2.2, HunyuanVideo), codes with AI assistants, and wants to understand what PC to build — without knowing anything about hardware. The pixel-art aesthetic, racing-game upgrade screens, and visual stat bars are all designed for someone who learns through visuals, not spec sheets.

But it works for anyone trying to answer the question: "what PC do I need to run this AI model?"

---

## Open it

**[▶ lexbayart.github.io/pcforge](https://lexbayart.github.io/pcforge/)**

Or download `index.html` from this repo and open it locally — fully standalone, works without internet after the first load.

---

## Documentation

- [**User Guide**](docs/GUIDE.md) — walkthrough of every screen and feature
- [**Master Design Document**](docs/MASTER_PROMPT.md) — the full project brief: vision, data, roadmap, philosophy

---

## Roadmap

| Phase | Status | What's in it |
|-------|--------|-------------|
| **Phase 1 — MVP** | ✅ Done | Build configurator, component catalogs, cost calculator, stat bars, presets, compatibility checks |
| **Phase 2 — AI Intelligence** | ✅ Done | AI Bench with video + LLM models, quantization toggles, fit indicators, MacBook comparison |
| **Phase 3 — Visual Builder** | 🚧 In progress | Drag-and-drop, Node View topology diagram, animated warnings, pixel art icons |
| **Phase 4 — Compare & Export** | 🔜 Coming | Side-by-side build comparison, export/copy build to text, VR Lab, buy links |
| **Phase 5 — Advanced** | 🔮 Planned | Claude API for live suggestions, localStorage saves, OS recommendations, peripheral catalogs |

This is a living project. As the user who built it put it: *"this isn't a static app, it will be modified more than once."* New hardware, new AI models, new features — it evolves.

---

## Tech

Single HTML file. React 18 loaded from CDN. Zero build step. Zero dependencies. All hardware data, AI model benchmarks, and compatibility logic live inside the file as structured JSON.

For the full architecture, data schemas, and development checklist, see [docs/MASTER_PROMPT.md](docs/MASTER_PROMPT.md).

---

## Feedback

Found a bug or have an idea?

- **Telegram:** [@lexbay](https://t.me/lexbay)
- **GitHub Issues:** [Open an issue](https://github.com/lexbayart/pcforge/issues)

---

## License

© 2025–2026 lexbayart — [CC BY-NC 4.0](LICENSE)

Free to use and share for non-commercial purposes with attribution. Commercial use requires explicit permission from the author.
