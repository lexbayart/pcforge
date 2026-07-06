# PCForge 🔷 [Beta]

> The only PC builder that tells you whether Llama 70B will actually fit on your GPU before you spend $12,000.

**[▶ Open in browser — try it now](https://lexbayart.github.io/pcforge/)**
No install. No account. Works offline.

---

## 🔷 What makes this different

Every PC builder on the market tells you if a CPU fits a motherboard. That's been solved for 20 years. PCForge does something no other tool does: it calculates VRAM requirements for specific AI models at specific quantization levels and tells you exactly which build can run them.

Select a GPU configuration, pick a model like DeepSeek V3 or HunyuanVideo 1.5, choose your quantization (FP16, FP8, Q8, Q4), and PCForge will show you if it fits — on one card, two cards, or not at all. It accounts for the real bottleneck: PCIe bandwidth between consumer GPUs versus NVLink on workstation cards. A 70B model might technically fit in 64GB across two RTX 5090s, but without NVLink the inference speed collapses. PCForge shows you that.

The visual node diagram draws your entire system topology with live bottleneck indicators — red pulsing warnings when your multi-GPU setup is constrained by PCIe instead of NVLink, or when your PSU is running past safe margins.

---

## ✨ Features

- ⚒️ **Build configurator** — pick GPU (1–4×), CPU, motherboard, RAM, PSU, storage with real-time cost tracking
- 🤖 **AI benchmarking** — test 4 video generation models and 6 LLMs against your GPU setup at 4 quantization levels
- 🔀 **Node view** — interactive SVG topology diagram showing all component connections and bottlenecks
- 🥽 **VR Lab** — side-by-side comparison of 5 VR headsets for creative work (weight, resolution, FOV, features)
- 📊 **Compare mode** — pit two verified presets against each other across all performance metrics
- 📁 **Presets gallery** — 4 verified builds sourced from MIT HAN Lab, ArsenalPC, B&H Photo, HuggingFace
- ⚡ **Compatibility diagnostics** — socket matching, PCIe slot count, PSU headroom, RAM type, NVLink support
- 💬 **Verified reviews** — real quotes from MIT researchers, Puget Systems, B&H customers linked to sources

---

## 🚀 Open it

**[▶ lexbayart.github.io/pcforge](https://lexbayart.github.io/pcforge/)**

Or download `index.html` from this repo and open it locally —
fully standalone, works without internet after first load.

---

## 📖 Documentation

- [**Complete User Guide**](docs/GUIDE.md) — tools, hotkeys, all features explained

---

## 🛠️ Tech

React 18 (CDN) · HTML5 · CSS custom properties · SVG diagrams · Vanilla JS
Single HTML file · Zero dependencies · Zero build step

---

## 💬 Feedback & Contact

This project is in active development.
Found a bug or have an idea? Reach out:

- **Telegram:** [@lexbay](https://t.me/lexbay)
- **GitHub Issues:** [Open an issue](https://github.com/lexbayart/pcforge/issues)

---

## 🧪 Dev Notes

`docs/` contains the complete user guide (GUIDE.md).

---

## 📄 License

© 2025 lexbayart — [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)

Free to use and share for non-commercial purposes with credit.
Commercial use requires explicit permission from the author.
