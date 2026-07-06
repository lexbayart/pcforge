# 🖥️ PC BUILDER SIMULATOR — MASTER BRIEF
> Версия документа: 1.0 | Дата обсуждения: 14 июня 2026  
> Этот документ — полный контекст для разработки приложения. Прочти его полностью перед тем, как писать первую строку кода.

---

## 🎯 ЧТО МЫ СТРОИМ

**Интерактивный браузерный симулятор сборки ПК** — не просто конфигуратор, а полноценный инструмент для исследования, сравнения и планирования высокопроизводительной сборки под задачи AI/ML и видеопроизводства.

**Кодовое название:** `PCForge` (можно использовать в UI)

**Технический формат:** Один HTML-файл с React (через CDN), работающий полностью офлайн в браузере. Все данные (цены, спецификации, модели AI) хранятся внутри файла в JSON-объектах. При открытии файла в браузере — всё работает без интернета.

---

## 👤 ПРОФИЛЬ ПОЛЬЗОВАТЕЛЯ

- **Текущая система:** MacBook (работает на Mac постоянно)
- **Задачи:**
  1. Съёмка и постпродакшн фильма
  2. AI-видеогенерация (ComfyUI / Draw Things / Wan 2.2 / HunyuanVideo)
  3. Написание кода с помощью AI (coding assistants, LLM inference локально)
  4. Создание AI-приложений (аналогичных этому)
- **Опыт:** Не разбирается в железе, не разбирается в коде, не разбирается в дизайн-документах — всё должно быть понятно визуально
- **VR:** Хочет поддержку VR-очков в сборке. Приоритеты: минимальный вес + максимальное разрешение
- **Бюджет:** Не ограничен на основное железо и VR. На периферию (мышь, клавиатура, монитор) — дешёвые но качественные варианты
- **Стиль восприятия:** Визуал. Лучше видит через схемы, диаграммы, игровые интерфейсы — чем через текст и таблицы

---

## 🎨 ДИЗАЙН И UX — КРИТИЧЕСКИ ВАЖНО

### Общий стиль
- **Пиксель-арт** — весь визуальный стиль UI должен быть в пиксельной эстетике
- Вдохновение: экран апгрейда автомобиля в гоночных играх (Need for Speed, Forza) — где при изменении компонента видно как растут/падают шкалы характеристик
- Не типичный корпоративный Bootstrap/Material Design. Нужно что-то необычное и запоминающееся
- Тёмная тема как основа (dark mode)
- Pixel font для заголовков (например, Press Start 2P из Google Fonts)
- Чистый современный шрифт для мелкого текста (Inter или JetBrains Mono)

### Главные экраны/режимы приложения
1. **BUILD VIEW** — основной конструктор (drag-and-drop компонентов в слоты)
2. **NODE VIEW** — граф-редактор (компоненты как ноды, связи между ними — пропускная способность, PCIe, питание)
3. **AI BENCH** — таблица AI-моделей с фильтрацией и переключением квантования
4. **VR LAB** — сравнение VR-шлемов
5. **COMPARE** — сравнение нескольких сборок бок о бок
6. **PRESETS** — готовые рекомендованные сборки

### Визуальные шкалы характеристик (ОБЯЗАТЕЛЬНО)
При изменении любого компонента в реальном времени обновляются шкалы:
- 🧠 AI Performance (LLM inference speed)
- 🎬 Video Gen Speed (frames/sec для Wan 2.2, HunyuanVideo)
- 💾 VRAM Capacity
- ⚡ Power Draw (Вт)
- 💰 Total Cost ($)
- 🌡️ Thermal Output
- 🔗 Bandwidth (PCIe / NVLink / межкарточная)

Шкалы анимированы (пиксельная анимация заполнения). Если характеристика ухудшилась — шкала мигает красным. Улучшилась — зелёным.

### Система предупреждений
- 🔴 **Красный блок** — критическая несовместимость (разные сокеты, нет нужных PCIe-слотов, PSU не тянет)
- 🟡 **Жёлтый блок** — деградация производительности (PCIe bandwidth bottleneck при multi-GPU без NVLink)
- 🟢 **Зелёная точка** — есть доступный вариант (например, модель не влезает в VRAM, но есть квантованная версия)

### Квантование AI-моделей (интерактивно)
- В таблице AI-моделей НЕ дублировать строки для каждого уровня квантования
- На каждой строке модели — **переключатель квантования** (FP16 → FP8 → GGUF Q8 → GGUF Q4)
- При переключении — моментально обновляются: нужный VRAM, скорость генерации, качество (индикатор)
- Если модель не влезает в текущий GPU — **зелёная точка/бейдж** с текстом "Q4 влезет" или "нужно 2× GPU"

### Node View (отдельный режим)
- Материнская плата — центральный хаб
- Каждый компонент — нода (GPU, CPU, RAM, Storage, PSU)
- Связи между нодами — подписаны (PCIe Gen 5 x16, DDR5-6400, NVMe 7GB/s и т.д.)
- При несовместимости — связь становится красной с пояснением
- Можно создавать новые ноды и подключать к материнке
- Отображается узкое место (bottleneck) — визуально пульсирует

---

## 🏗️ ТЕХНИЧЕСКАЯ АРХИТЕКТУРА

### Стек
```
HTML5 + CSS3 + JavaScript (ES6+)
React 18 (CDN: unpkg.com/react@18/umd/react.production.min.js)
ReactDOM 18 (CDN)
Babel Standalone (для JSX в браузере)
Lucide Icons (CDN)
```

### Структура одного HTML-файла
```javascript
// ============================================================
// PCForge v1.0 | PC Builder Simulator
// Last data update: 2026-06-14
// Author session: [дата обновления]
// ============================================================
//
// ARCHITECTURE:
// 1. DATA_LAYER     — JSON объекты: GPU, CPU, MB, RAM, PSU, Storage, VR, AI_MODELS
// 2. COMPATIBILITY  — движок проверки совместимости + расчёт производительности
// 3. COMPONENTS     — React-компоненты (BuildView, NodeView, AIBench, VRLab, Compare)
// 4. STYLES         — CSS (пиксель-арт стиль, анимации)
// 5. APP            — точка входа, роутинг между экранами
//
// UPDATE CHECKLIST (запускать в каждой новой сессии):
// [ ] Проверить цены GPU на Newegg/Amazon
// [ ] Проверить новые релизы NVIDIA (RTX 50 Super?)
// [ ] Обновить бенчмарки AI-моделей (Wan, HunyuanVideo, LTX)
// [ ] Проверить новые LLM-модели и их VRAM-требования
// [ ] Обновить таблицу VR-шлемов (Steam Frame статус?)
// [ ] Проверить правила совместимости
// [ ] Удалить из ROADMAP завершённые задачи
// [ ] Добавить новые задачи от пользователя
//
// ROADMAP (текущие задачи — обновляется):
// [x] Базовый каталог компонентов
// [ ] Node View режим
// [ ] Drag-and-drop в BUILD VIEW
// [ ] Сравнение сборок (COMPARE)
// [ ] Export / Copy build
// [ ] Интеграция Claude API для динамических советов
// ============================================================
```

### Данные — обновляемые JSON объекты внутри файла
Все данные должны иметь метку `lastUpdated: "2026-06-14"` для отслеживания актуальности.

---

## 💾 ДАННЫЕ — ЖЕЛЕЗО (актуально на 14.06.2026)

### GPU — Топовый сегмент (ТОЛЬКО топ, никаких бюджетных вариантов)

#### RTX 5090 (потребительский флагман)
```json
{
  "id": "rtx5090",
  "name": "NVIDIA GeForce RTX 5090",
  "tier": "consumer_flagship",
  "arch": "Blackwell",
  "vram_gb": 32,
  "vram_type": "GDDR7",
  "vram_bandwidth_tbs": 1.79,
  "cuda_cores": 21760,
  "tensor_cores_gen": 5,
  "tflops_fp32": 104.8,
  "tdp_w": 575,
  "pcie": "Gen5 x16",
  "nvlink": false,
  "price_usd": 2000,
  "market_price_usd": 2500,
  "buy_url": "https://www.newegg.com/p/pl?d=rtx+5090",
  "release": "2026-01",
  "notes": "Флагман потребительского сегмента. NVLink не поддерживается. При multi-GPU — только PCIe bandwidth (~64 GB/s между картами). Температура памяти под нагрузкой 88-90°C.",
  "lastUpdated": "2026-06-14"
}
```

#### RTX PRO 6000 Blackwell Workstation Edition (профессиональный)
```json
{
  "id": "rtx_pro6000_ws",
  "name": "NVIDIA RTX PRO 6000 Blackwell Workstation",
  "tier": "pro_workstation",
  "arch": "Blackwell",
  "vram_gb": 96,
  "vram_type": "GDDR7 ECC",
  "vram_bandwidth_tbs": 1.79,
  "cuda_cores": 24064,
  "tensor_cores_gen": 5,
  "tdp_w": 600,
  "pcie": "Gen5 x16",
  "nvlink": true,
  "nvlink_bandwidth_gbs": 1800,
  "price_usd": 8565,
  "market_price_usd": 12099,
  "official_msrp_note": "NVIDIA поднял до $13,250 в июне 2026 — ДЕФИЦИТ. Newegg: $12,099.",
  "buy_url": "https://www.newegg.com/p/pl?d=rtx+pro+6000+blackwell",
  "release": "2025-03",
  "notes": "Профессиональная карта. ECC-память. NVLink 5 — 1800 GB/s двунаправленно. Поддерживает vGPU/виртуализацию. Лучший выбор для multi-GPU AI-сервера.",
  "lastUpdated": "2026-06-14"
}
```

#### RTX PRO 6000 Blackwell Max-Q / Server Edition (серверный вариант)
```json
{
  "id": "rtx_pro6000_mq",
  "name": "NVIDIA RTX PRO 6000 Blackwell Max-Q",
  "tier": "pro_server",
  "arch": "Blackwell",
  "vram_gb": 96,
  "vram_type": "GDDR7 ECC",
  "cuda_cores": 24064,
  "tdp_w": 300,
  "cooling": "passive",
  "nvlink": true,
  "nvlink_bandwidth_gbs": 1800,
  "notes": "Та же GPU, но пассивное охлаждение, 300W вместо 600W — примерно 10-15% ниже пиковая производительность. Можно ставить 2-4 штуки в специализированный серверный корпус. Идеально для тихого домашнего AI-сервера.",
  "price_usd": 9000,
  "buy_url": "https://www.bhphotovideo.com/c/search?Ntt=rtx+pro+6000+blackwell+server",
  "lastUpdated": "2026-06-14"
}
```

### Готовые конфигурации GPU (варианты для конструктора)

| Конфиг | GPU | Кол-во | Суммарный VRAM | Inter-GPU BW | Цена GPU |
|--------|-----|--------|----------------|--------------|----------|
| Entry AI | RTX 5090 | 1× | 32 GB | — | ~$2,500 |
| Dual Consumer | RTX 5090 | 2× | 64 GB | PCIe ~64 GB/s ⚠️ | ~$5,000 |
| Pro Single | RTX PRO 6000 WS | 1× | 96 GB | — | ~$12,099 |
| Pro Dual NVLink | RTX PRO 6000 WS | 2× | 192 GB | NVLink 1800 GB/s ✅ | ~$24,000 |
| Mini Server | RTX PRO 6000 Max-Q | 4× | 384 GB | NVLink ✅ | ~$36,000 |

**ПРЕДУПРЕЖДЕНИЕ для 2× RTX 5090:** Показывать в UI: "PCIe Gen 5 bandwidth между картами — ~64 GB/s против 1800 GB/s у NVLink. Для больших моделей (70B+) двухкарточный 5090 даст только 1230 tok/s против 4210 tok/s у одного PRO 6000. Два 5090 суммарно потребляют 1150 Вт — нужен специализированный БП и корпус."

---

## 🤖 ДАННЫЕ — AI МОДЕЛИ ВИДЕОГЕНЕРАЦИИ (актуально 06.2026)

### Система квантований для таблицы

| Уровень | Обозначение | VRAM × | Качество |
|---------|-------------|--------|---------|
| Полная точность | FP16 | 1.0× | ★★★★★ |
| Половинная | FP8 | ~0.5× | ★★★★☆ |
| Сжатый | GGUF Q8 | ~0.45× | ★★★★☆ |
| Сжатый light | GGUF Q4 | ~0.25× | ★★★☆☆ |

### Wan 2.2 (Wan Video 14B)
```json
{
  "id": "wan22",
  "name": "Wan 2.2 14B",
  "type": "T2V + I2V",
  "developer": "Wan Team",
  "params_b": 14,
  "quantizations": {
    "FP16": { "vram_gb": 54, "speed_rtx5090_sec_per_clip": null, "note": "Не влезает на RTX 5090" },
    "FP8":  { "vram_gb": 27, "speed_rtx5090_sec_per_clip": 125, "note": "Рекомендуется с sageattn3" },
    "Q8":   { "vram_gb": 24, "speed_rtx5090_sec_per_clip": 140 },
    "Q4":   { "vram_gb": 6,  "speed_rtx5090_sec_per_clip": 90, "note": "480p, заметная потеря качества" }
  },
  "max_resolution": "720p",
  "max_duration_sec": 10,
  "strengths": ["Стабильность камеры", "Прорисовка рук", "Кинематографичность"],
  "tools": ["ComfyUI", "DrawThings"],
  "huggingface": "https://huggingface.co/Wan-AI/Wan2.2",
  "benchmark_note": "На RTX 5090 FP8 + sageattn3: ~125 сек на 5-секундный клип 720p. 1 сек/кадр при 720×720 I2V.",
  "lastUpdated": "2026-06-14"
}
```

### HunyuanVideo 1.5 (Tencent) — ЭТО МОДЕЛЬ НА БУКВУ H
```json
{
  "id": "hunyuan15",
  "name": "HunyuanVideo 1.5",
  "type": "T2V + I2V",
  "developer": "Tencent",
  "params_b": 8.3,
  "quantizations": {
    "FP16": { "vram_gb": 26, "speed_rtx5090_sec_per_clip": 90, "note": "Влезает на RTX 5090" },
    "FP8":  { "vram_gb": 15, "speed_rtx5090_sec_per_clip": 60 },
    "Q8":   { "vram_gb": 12, "speed_rtx5090_sec_per_clip": 55 },
    "Q4":   { "vram_gb": 8,  "speed_rtx5090_sec_per_clip": 75, "note": "Distilled: 75 сек на RTX 4090" }
  },
  "max_resolution": "720p",
  "max_duration_sec": 10,
  "strengths": ["Лучшая прорисовка лиц", "Лёгкая", "Доступна на consumer GPU", "Быстрее Wan при той же сложности"],
  "tools": ["ComfyUI", "DrawThings"],
  "huggingface": "https://huggingface.co/tencent/HunyuanVideo-1.5",
  "benchmark_note": "Distilled-модель: 75 сек на RTX 4090. На RTX 5090 — быстрее. FP16 влезает на 5090 без проблем.",
  "lastUpdated": "2026-06-14"
}
```

### LTX-2.3 22B (Lightricks)
```json
{
  "id": "ltx23",
  "name": "LTX-2.3 22B",
  "type": "T2V + I2V + Audio",
  "developer": "Lightricks",
  "params_b": 22,
  "quantizations": {
    "FP16": { "vram_gb": 48, "speed_rtx5090_sec_per_clip": 22, "note": "Не влезает на RTX 5090 полностью" },
    "FP8":  { "vram_gb": 24, "speed_rtx5090_sec_per_clip": 22, "note": "Влезает! Это флагман по скорости" },
    "Q8":   { "vram_gb": 20, "speed_rtx5090_sec_per_clip": 25 },
    "Q4":   { "vram_gb": 14, "speed_rtx5090_sec_per_clip": 30 }
  },
  "max_resolution": "4K",
  "max_fps": 50,
  "max_duration_sec": 20,
  "strengths": ["Самый быстрый (5.7× быстрее Wan 2.2)", "Нативная 4K 50fps", "Синхронный аудио+видео"],
  "weaknesses": ["Нестандартный pipeline в ComfyUI (21 нода)", "Хуже стабильность камеры vs Wan 2.2"],
  "tools": ["ComfyUI (специальный pipeline)"],
  "benchmark_note": "Warmed-up: 22 сек на клип. В 5.7× быстрее Wan 2.2. Единственная open-source модель с нативным 4K@50fps.",
  "lastUpdated": "2026-06-14"
}
```

### CogVideoX (Zhipu AI)
```json
{
  "id": "cogvideox",
  "name": "CogVideoX-5B",
  "type": "T2V",
  "developer": "Zhipu AI",
  "params_b": 5,
  "quantizations": {
    "FP16": { "vram_gb": 12, "speed_rtx5090_sec_per_clip": 30 },
    "FP8":  { "vram_gb": 6, "speed_rtx5090_sec_per_clip": 25 },
    "Q4":   { "vram_gb": 4, "speed_rtx5090_sec_per_clip": 20 }
  },
  "max_resolution": "480p",
  "max_duration_sec": 6,
  "strengths": ["Работает на 12GB VRAM", "Хорошая для тестирования", "Apache 2.0 лицензия"],
  "weaknesses": ["Только 480p", "Только 6 сек", "Качество ниже Wan/Hunyuan"],
  "lastUpdated": "2026-06-14"
}
```

### AnimateDiff (Stability AI ecosystem)
```json
{
  "id": "animatediff",
  "name": "AnimateDiff",
  "type": "I2V (loops)",
  "quantizations": {
    "FP16": { "vram_gb": 8, "speed_rtx5090_sec_per_clip": 15 },
    "Q4":   { "vram_gb": 4, "speed_rtx5090_sec_per_clip": 10 }
  },
  "max_resolution": "512p",
  "strengths": ["Минимальные требования (8GB)", "Хорошо для коротких анимаций и петель"],
  "weaknesses": ["Ограниченная длина", "Только image-to-video"],
  "lastUpdated": "2026-06-14"
}
```

---

## 🧠 ДАННЫЕ — LLM МОДЕЛИ (актуально 06.2026)

### Бенчмарки по скорости (токенов/сек)

| Модель | Квант | VRAM | RTX 5090 (tok/s) | PRO 6000 (tok/s) | GPU нужно (5090) |
|--------|-------|------|------------------|-------------------|-----------------|
| Llama 3.3 70B | Q4_K_M | ~40GB | ~47 | ~90 | 2× 5090 |
| Llama 3.3 70B | Q8 | ~75GB | — | ~50 | 1× PRO 6000 |
| Llama 3 8B | FP16 | ~16GB | ~500 | ~900 | 1× 5090 |
| Llama 3 8B | Q4 | ~5GB | ~1200 | ~2100 | 1× 5090 |
| DeepSeek V3 (MoE) | Q4 | ~35GB | ~60 | ~120 | 2× 5090 |
| Qwen2.5 72B | Q4 | ~40GB | ~45 | ~85 | 2× 5090 |
| Mistral 7B | Q4 | ~5GB | ~1100 | ~2000 | 1× 5090 |
| Gemma 3 27B | Q4 | ~16GB | ~160 | ~300 | 1× 5090 |
| (General small ~7B Q4) | Q4 | ~5GB | 4570 | 8425 | 1× 5090 |

**Источник бенчмарков RTX 5090 vs PRO 6000:** CloudRift.ai, BIZON-tech.com, GamersNexus (сентябрь 2025)

### Важные примечания для UI
- LLaMA 3.1 405B требует ~16× RTX 5090 или ~5× PRO 6000 — показывать предупреждение
- При разбивке модели на 2× 5090 через PCIe — скорость НЕ удваивается (bottleneck bandwidth)
- PRO 6000 с NVLink в 1.8× быстрее 5090 на сопоставимых моделях благодаря bandwidth

---

## 🥽 ДАННЫЕ — VR ШЛЕМЫ (актуально 06.2026)

Приоритеты пользователя: **1) Вес 2) Разрешение** (затем всё остальное)

| Шлем | Разрешение (на глаз) | Вес | FOV | Тип подключения | Цена | Рейтинг лёгкости |
|------|----------------------|-----|-----|-----------------|------|-----------------|
| Pimax Crystal Super | 3840×3840 | ~820g ⚠️ | 120° | PCIe/USB | ~$1,799 | ★★☆☆☆ |
| Meta Quest 3 | 2064×2208 | 515g ✅ | 110° | Standalone/USB/WiFi | ~$499 | ★★★★★ |
| Meta Quest 3S | 1832×1920 | 514g ✅ | 96° | Standalone/USB/WiFi | ~$299 | ★★★★★ |
| Valve Index | 1440×1600 | 809g ⚠️ | 130° | USB+DisplayPort | ~$999 | ★★☆☆☆ |
| HTC Vive XR Elite | 1920×1920 | 625g | 110° | Standalone/PC | ~$1,099 | ★★★☆☆ |
| Apple Vision Pro | 3660×3142 | 600g+ ⚠️ | ~100° | Standalone (M2) | ~$3,499 | ★★☆☆☆ (battery-pack) |
| Valve Steam Frame | TBD | TBD | TBD | PC | TBD | — |

**Рекомендация по приоритетам пользователя:**
- **Лучший баланс:** Meta Quest 3 (лёгкий, хорошее разрешение, standalone + PC режим)
- **Максимальное разрешение:** Pimax Crystal Super (но тяжёлый)
- **Watch list:** Steam Frame от Valve (ожидается в 2026, похоже на значимый продукт)

**Совместимость VR с GPU:**
- Все варианты работают с RTX 5090 и PRO 6000
- Pimax Crystal Super требует DisplayPort 1.4 или выше
- Apple Vision Pro — НЕ совместим с Windows PC (только Mac/standalone)

---

## 🖥️ ДАННЫЕ — ПРОЧИЕ КОМПОНЕНТЫ (топовые варианты)

### CPU
```json
[
  {
    "id": "ryzen_9800x3d",
    "name": "AMD Ryzen 9 9800X3D",
    "socket": "AM5",
    "cores": 8,
    "tdp_w": 120,
    "price_usd": 479,
    "buy_url": "https://www.newegg.com/p/pl?d=9800x3d",
    "note": "Лучший для AI-workloads + gaming. GamersNexus использует именно его в GPU-тестах RTX PRO 6000."
  },
  {
    "id": "threadripper_7970x",
    "name": "AMD Threadripper 7970X",
    "socket": "TRX50",
    "cores": 32,
    "tdp_w": 350,
    "price_usd": 2499,
    "note": "Для серверных конфигураций с 4× GPU. Поддерживает до 128 PCIe 5.0 lanes."
  },
  {
    "id": "core_ultra9_285k",
    "name": "Intel Core Ultra 9 285K",
    "socket": "LGA1851",
    "cores": 24,
    "tdp_w": 250,
    "price_usd": 589,
    "note": "Альтернатива AMD для однокарточных сборок."
  }
]
```

### Материнские платы (примеры для ключевых конфигов)
```json
[
  {
    "id": "asus_x870e_apex",
    "name": "ASUS ROG Crosshair X870E Apex",
    "socket": "AM5",
    "chipset": "X870E",
    "pcie_x16_slots": 2,
    "pcie_gen": 5,
    "max_ram_gb": 192,
    "price_usd": 699,
    "note": "Для сборки с 1-2 RTX 5090 или 1× PRO 6000"
  },
  {
    "id": "asus_pro_ws_trx50",
    "name": "ASUS Pro WS TRX50-SAGE WIFI",
    "socket": "TRX50",
    "chipset": "TRX50",
    "pcie_x16_slots": 7,
    "pcie_gen": 5,
    "max_ram_gb": 2048,
    "price_usd": 1099,
    "note": "Для сборки мини-сервера с 4× PRO 6000 Max-Q"
  }
]
```

### PSU (только для ориентира)
- 1× RTX 5090 → минимум **1000W** (рекомендуется 1200W)
- 2× RTX 5090 → минимум **1600W** (специализированный)
- 1× RTX PRO 6000 WS → **850W**
- 4× PRO 6000 Max-Q → **серверный блок питания** или **2× 1600W**

---

## 💻 СРАВНИТЕЛЬНЫЙ ПРЕСЕТ — MacBook Pro M5 Max

```json
{
  "id": "macbook_m5max",
  "name": "MacBook Pro M5 Max",
  "type": "reference_preset",
  "chip": "Apple M5 Max",
  "unified_memory_gb": 48,
  "storage_tb": 2,
  "gpu_cores": 40,
  "cpu_cores": 16,
  "neural_engine_tops": 38,
  "price_usd": 3999,
  "os": "macOS",
  "ml_framework": "MPS (Metal Performance Shaders)",
  "cuda_support": false,
  "notes": [
    "Unified memory — GPU и CPU делят 48GB, нет отдельного VRAM",
    "Не поддерживает CUDA — ограниченная совместимость с AI-экосистемой",
    "ComfyUI работает через MPS, но медленнее чем CUDA",
    "DrawThings — оптимизирован под Apple Silicon, работает хорошо",
    "Wan 2.2 в FP16 не влезает (48GB но unified — часть занята ОС и CPU)",
    "HunyuanVideo 1.5 Q4 — запускается (около 8GB GPU memory нужно)"
  ],
  "video_gen_benchmark": {
    "Wan 2.2 FP8": "не запускается (нет CUDA)",
    "HunyuanVideo 1.5 Q4 (MPS)": "~400-600 сек на клип",
    "LTX-2.3 FP8 (MPS)": "~200-300 сек на клип"
  },
  "llm_benchmark": {
    "Llama 3 8B Q4 (MLX)": "~40-60 tok/s",
    "Llama 3.3 70B Q4 (MLX)": "~8-12 tok/s (не влезает, offload)"
  },
  "lastUpdated": "2026-06-14"
}
```

---

## 🔧 ДВИЖОК СОВМЕСТИМОСТИ — ПРАВИЛА

### Матрица совместимости (реализовать как объект-граф)

```javascript
const COMPATIBILITY_RULES = [
  // CPU ↔ Motherboard
  { type: "socket", component_a: "cpu", component_b: "motherboard",
    check: (cpu, mb) => cpu.socket === mb.socket,
    error: "⛔ Несовместимый сокет! {cpu.socket} ≠ {mb.socket}" },
  
  // GPU PCIe slots
  { type: "pcie_slots", component_a: "gpu[]", component_b: "motherboard",
    check: (gpus, mb) => gpus.length <= mb.pcie_x16_slots,
    error: "⛔ Недостаточно PCIe x16 слотов. Нужно {gpus.length}, доступно {mb.pcie_x16_slots}" },
  
  // PSU мощность
  { type: "power", components: ["gpu[]", "cpu", "ram[]", "storage[]"],
    check: (totalTdp, psu) => psu.wattage >= totalTdp * 1.2,
    warning: "⚠️ PSU на пределе! Рекомендуется запас 20%. Реальное потребление может превышать TDP.",
    error: "⛔ PSU не хватит! Суммарный TDP {totalTdp}W, PSU {psu.wattage}W" },
  
  // Multi-GPU NVLink warning
  { type: "multi_gpu_bandwidth", component_a: "gpu[]",
    check: (gpus) => gpus.length < 2 || gpus[0].nvlink,
    warning: "⚠️ Multi-GPU без NVLink! Пропускная способность между картами ~64 GB/s (PCIe). Для больших моделей это жёсткий bottleneck. Рассмотри RTX PRO 6000 с NVLink (1800 GB/s)." },
  
  // RAM DDR5 compatibility
  { type: "ram_type", component_a: "ram", component_b: "motherboard",
    check: (ram, mb) => ram.type === mb.ram_type,
    error: "⛔ Несовместимый тип RAM! {ram.type} ≠ {mb.ram_type}" },
  
  // Размер корпуса
  { type: "form_factor", component_a: "motherboard", component_b: "case",
    check: (mb, case_) => case_.supported_form_factors.includes(mb.form_factor),
    error: "⛔ Материнка {mb.form_factor} не влезет в корпус {case_.name}" }
]
```

### Расчёт производительности AI (псевдокод)
```javascript
function calcAIPerformance(build, model, quantization) {
  const gpu = build.gpus[0]; // primary GPU
  const gpuCount = build.gpus.length;
  const totalVram = gpu.vram_gb * gpuCount;
  const modelVram = model.quantizations[quantization].vram_gb;
  
  if (modelVram > gpu.vram_gb && !gpu.nvlink) {
    return { 
      status: "MULTI_GPU_PCIe",
      warning: "Модель разбита по PCIe — ожидайте снижения скорости",
      speed: model.baseSpeed * 0.6 // PCIe overhead
    };
  }
  
  if (modelVram > totalVram) {
    return { status: "DOES_NOT_FIT", speed: 0 };
  }
  
  // Масштабирование по GPU
  const speedMultiplier = gpu.nvlink ? gpuCount * 0.95 : gpuCount * 0.6;
  return {
    status: "OK",
    speed: model.quantizations[quantization].speed_rtx5090_sec_per_clip / speedMultiplier
  };
}
```

---

## 📋 ГОТОВЫЕ ПРЕСЕТЫ СБОРОК

### 🟢 AI Coding Workstation (Стартовая сборка)
```
GPU:      1× RTX 5090 (32GB)
CPU:      AMD Ryzen 9 9800X3D
MB:       ASUS ROG Crosshair X870E Apex
RAM:      64GB DDR5-6400 (2×32GB)
Storage:  2TB Samsung 990 Pro NVMe
PSU:      1200W Corsair HX1200i
Case:     Fractal Design Define 7 XL
OS:       Ubuntu 22.04 LTS / Windows 11 Pro
Цена GPU: ~$2,500 | Полная сборка: ~$5,500
LLM:      до 30B Q4 на одной карте, 70B Q4 на 2 картах
Video:    Wan 2.2 FP8 (125 сек/клип), HunyuanVideo FP16 (90 сек)
```

### 🔵 Video Generation Studio (Одна мощная карта)
```
GPU:      1× RTX PRO 6000 Blackwell WS (96GB)
CPU:      AMD Ryzen 9 9800X3D
MB:       ASUS ROG Crosshair X870E Apex
RAM:      128GB DDR5-6400
Storage:  4TB Samsung 990 Pro NVMe × 2 (RAID)
PSU:      1000W Corsair HX1000i
Case:     Fractal Design Define 7 XL
OS:       Ubuntu 22.04 LTS
Цена GPU: ~$12,099 | Полная сборка: ~$16,000
LLM:      до 120B MoE Q4 на одной карте, 8425 tok/s
Video:    Все модели без квантования, включая Wan 2.2 FP16
```

### 🔴 Mini AI Server (4× GPU, пассивное охлаждение)
```
GPU:      4× RTX PRO 6000 Max-Q (96GB × 4 = 384GB total)
CPU:      AMD Threadripper 7970X (32 cores)
MB:       ASUS Pro WS TRX50-SAGE WIFI
RAM:      256GB DDR5 ECC
Storage:  8TB NVMe × 2
PSU:      2× серверный PSU 2000W
Case:     Специализированный серверный корпус
OS:       Ubuntu 22.04 LTS + Docker + vLLM
Цена GPU: ~$36,000 | Полная сборка: ~$45,000+
LLM:      Llama 405B в FP16, скорость × 4 с NVLink
Video:    Параллельная генерация 4 клипов одновременно
```

### ⚪ Reference: MacBook Pro M5 Max (для сравнения, неизменяемый)
```
(см. секцию СРАВНИТЕЛЬНЫЙ ПРЕСЕТ выше)
```

---

## 🚀 ФАЗЫ РАЗРАБОТКИ (Roadmap)

### Фаза 1 — MVP (первая сессия кодинга)
- [ ] Базовая HTML-структура с React CDN
- [ ] JSON-данные: 5 GPU, 3 CPU, 5 материнок, PSU, RAM, Storage
- [ ] BUILD VIEW: выбор компонентов по слотам (без drag-and-drop пока)
- [ ] Калькулятор стоимости + мощности в реальном времени
- [ ] Базовые шкалы характеристик (пиксель-арт стиль)
- [ ] 3 готовых пресета
- [ ] Базовые предупреждения совместимости

### Фаза 2 — AI Intelligence
- [ ] Таблица видеомоделей с переключателем квантования
- [ ] Таблица LLM с расчётом токенов/сек
- [ ] Индикатор "влезает / не влезает / можно в Q4" с зелёной точкой
- [ ] Расчёт количества GPU для каждой модели
- [ ] Сравнение с MacBook M5 Max

### Фаза 3 — Visual Builder
- [ ] Drag-and-drop компонентов в слоты
- [ ] NODE VIEW (граф-редактор)
- [ ] Анимированные предупреждения (pulse, flash)
- [ ] Пиксельные иконки компонентов

### Фаза 4 — Compare & Export
- [ ] COMPARE режим (сравнение 2-3 сборок бок о бок)
- [ ] Export/Copy: копирование полной сборки в текст (для проверки в других AI)
- [ ] VR LAB: таблица шлемов с фильтрами
- [ ] Ссылки на покупку для каждого компонента

### Фаза 5 — Advanced
- [ ] Claude API интеграция (динамические советы прямо в приложении)
- [ ] Сохранение сборок в localStorage
- [ ] OS recommendation (Linux vs Windows с обоснованием)
- [ ] Расширение данных: мышь, клавиатура, монитор (бюджетные)

---

## 📐 ПРАВИЛА АРХИТЕКТУРЫ КОДА

### Комментарии в коде
Каждый раздел данных должен начинаться с:
```javascript
// ============================================================
// SECTION: GPU_DATA
// Last updated: 2026-06-14
// Source: Newegg, NVIDIA, Tom's Hardware, GamersNexus
// Update trigger: При каждом запуске новой сессии разработки
// ============================================================
```

### Версионирование данных
```javascript
const APP_META = {
  version: "1.0.0",
  dataVersion: "2026-06-14",
  lastGPUPriceCheck: "2026-06-14",
  lastAIModelBenchmark: "2026-06-14",
  lastVRCheck: "2026-06-14",
  updateChecklist: [
    "GPU цены на Newegg/Amazon",
    "Новые NVIDIA релизы (RTX 50 Super rumors?)",
    "AI model benchmarks",
    "VR headset new releases",
    "LLM VRAM requirements",
  ]
};
```

### CSS-переменные пиксель-арт темы
```css
:root {
  --bg-primary: #0a0a0f;
  --bg-secondary: #12121a;
  --bg-card: #1a1a2e;
  --accent-green: #00ff88;
  --accent-blue: #0088ff;
  --accent-red: #ff4444;
  --accent-yellow: #ffcc00;
  --accent-purple: #8844ff;
  --text-primary: #e0e0ff;
  --text-secondary: #8888aa;
  --pixel-border: 2px solid var(--accent-green);
  --font-pixel: 'Press Start 2P', monospace;
  --font-ui: 'Inter', sans-serif;
  --font-mono: 'JetBrains Mono', monospace;
}
```

### Шкалы характеристик — цветовая система
- Выше 80% от максимума: `--accent-green` (зелёный)
- 50-80%: `--accent-yellow` (жёлтый)
- Ниже 50%: `--accent-red` (красный)
- Конфликт: мигание красным 0.5s interval

---

## ⚠️ ИЗВЕСТНЫЕ ТЕХНИЧЕСКИЕ ОГРАНИЧЕНИЯ

1. **Цены** — не real-time. Обновляются вручную при каждой сессии разработки. Всегда показывать дату последнего обновления прямо в UI.

2. **Два RTX 5090 без NVLink** — показывать жёлтое предупреждение всегда. Не запрещать сборку, но честно предупреждать о degradation.

3. **RTX PRO 6000 в дефиците** (июнь 2026) — показывать в UI "⚠️ Дефицит! Цена растёт." с датой последней проверки.

4. **Apple Vision Pro** — в VR-таблице помечать как "несовместим с Windows PC" большим баннером.

5. **Wan 2.2 и HunyuanVideo в FP16** — на RTX 5090 показывать две версии в таблице: "FP16: не влезает" и рядом зелёная точка "FP8: влезает, скорость X сек".

6. **MacBook M5 Max** — ComfyUI работает через MPS, но многие CUDA-оптимизации (sageattn3, flash attention) недоступны. Честно показывать разницу в скорости.

7. **Quantization impact на качество** — добавить визуальный индикатор (звёзды или пиксельная шкала качества) рядом с переключателем квантования.

---

## 🔄 ЧЕКЛИСТ ДЛЯ КАЖДОЙ НОВОЙ СЕССИИ

Перед тем как писать код в новой сессии — выполнить проверку:

```
1. ЖЕЛЕЗО
   [ ] Есть ли новые GPU от NVIDIA? (поиск "NVIDIA RTX 50 Super 2026")
   [ ] Текущая цена RTX 5090 на Newegg?
   [ ] Текущая цена/наличие RTX PRO 6000 Blackwell?
   [ ] Новые материнские платы для AM5/TRX50?

2. AI МОДЕЛИ  
   [ ] Есть ли новые версии Wan? (поиск "Wan 2.3" или "Wan Video 2026")
   [ ] Есть ли обновления HunyuanVideo? (поиск "HunyuanVideo 2.0")
   [ ] Новые конкурентные видеомодели?
   [ ] Новые флагманские LLM и их VRAM?
   
3. VR
   [ ] Статус Steam Frame (Valve)?
   [ ] Новые релизы Pimax, Meta, HTC?
   
4. ПРИЛОЖЕНИЕ
   [ ] Проверить ROADMAP — удалить выполненные задачи
   [ ] Добавить новые задачи от пользователя
   [ ] Проверить ссылки на покупку (Newegg, Amazon, B&H)
   [ ] Обновить APP_META.dataVersion
```

---

## 📝 ИСТОЧНИКИ (для проверки актуальности)

- **GPU цены:** newegg.com, bhphotovideo.com, amazon.com
- **GPU бенчмарки:** gamersnexus.net, runpod.io/articles, bizon-tech.com
- **AI видеомодели:** huggingface.co, comfyui.org, willitrunai.com
- **LLM бенчмарки:** cloudrift.ai, bizon-tech.com, ollama.com
- **VR шлемы:** engadget.com, techradar.com, pcgamer.com
- **Цены PRO 6000:** nvidia.com/marketplace, videocardz.com
- **GitHub референсы:**
  - japanese builder: `jisaku.nunawa.net` (React/TypeScript/SQL — структура данных)
  - AI PC builder: `github.com/topics/pc-building` (ML-prediction approach)

---

## 💬 ЦИТАТЫ ПОЛЬЗОВАТЕЛЯ (сохранить для контекста)

> "Я снимаю фильм и также увлекаюсь кодированием с помощью искусственного интеллекта"

> "Это не статичное приложение, мы сейчас пишем, оно будет модифицироваться не один раз"

> "Я не очень люблю типичный дизайн... лучше находить решения современные"

> "По видеокарте у меня есть подозрение что нужно несколько штук... это такой мини-сервер"

> "Стиль как в гоночных играх — апгрейд и видишь как растут/падают шкалы характеристик"

> "Я больше визуальный человек и визуально воспринимаю лучше"

> "Возможно мне нужно какое-то отдельное окно, в которое можно переходить и видеть связку как ноды"

> "Можешь делать это в стиле пиксель-арта"

> "Цены обновляются при каждом апгрейде, онлайн в реальном времени не нужно"

> "Размер данного файла может быть вообще неограниченным"

---

*Документ создан: 14 июня 2026. В следующей сессии — начинать с Фазы 1.*

---

---

## 🧠 ФИЛОСОФИЯ РАЗРАБОТКИ — ГИБКОСТЬ И ЖИВОЕ МЫШЛЕНИЕ

> **ВАЖНО ДЛЯ CLAUDE В СЛЕДУЮЩЕЙ СЕССИИ: Прочти этот раздел первым.**

Этот документ — **отправная точка**, а не клетка. Данные, перечни компонентов, список AI-моделей и цены — всё это **актуально на дату создания документа (14.06.2026)**, но мир железа и AI меняется каждые 2-3 месяца. Задача Claude в каждой новой сессии — **думать, а не копировать**.

### Что это означает на практике

**По железу:**
- Если вышла новая NVIDIA GPU (например, RTX 5090 Ti, RTX 6090 или что-то ещё) — добавь её в каталог, проведи сравнение с тем, что уже есть. Не жди команды пользователя.
- Если появился новый конкурент AMD/Intel с интересным соотношением VRAM/цена для AI — добавь в таблицу альтернатив с честным комментарием (но не в основной каталог: пользователь ориентирован на NVIDIA для AI из-за CUDA-экосистемы).
- Если цена RTX PRO 6000 Blackwell изменилась (сейчас дефицит и рост), — обнови и добавь комментарий в UI: "Цены нестабильны, проверяй перед покупкой".

**По AI-моделям:**
- Если вышла Wan 3.0, HunyuanVideo 2.0, LTX-3 или любая другая крупная видеомодель — добавь её в таблицу по той же схеме (параметры, квантизации, скорости). Не спрашивай разрешения.
- Если появилась новая архитектура (не диффузия, а например flow matching или что-то новое) — объясни пользователю в UI-подсказке, что изменилось.
- Таблица LLM должна отражать актуальных лидеров: Llama, Qwen, DeepSeek, Mistral, Gemma. Если какая-то модель устарела (снята с обслуживания, вышла значительно лучшая версия) — помечай её в таблице как "[устарела]", не удаляя сразу.

**По приложению:**
- Если есть лучший способ что-то реализовать (новая UI-библиотека, более эффективный алгоритм совместимости) — предложи и реализуй, объяснив пользователю что и зачем изменил.
- Если пользователь сказал что-то расплывчатое — интерпретируй в духе проекта, реализуй, а потом покажи и объясни.
- Код должен быть написан так, чтобы следующая версия Claude, получив этот файл, сразу поняла логику и могла продолжить.

### Принцип работы с данными в приложении

```javascript
// ❌ НЕ ТАК: жёсткий список без обновления
const GPU_LIST = ["RTX 5090", "RTX PRO 6000"]; // написал и забыл

// ✅ ТАК: живые данные с метаданными
const GPU_CATALOG = {
  meta: {
    lastUpdated: "2026-06-14",
    updateNote: "Проверь новые релизы NVIDIA при следующей сессии",
    sources: ["newegg.com", "gamersnexus.net", "videocardz.com"]
  },
  gpus: [
    { id: "rtx5090", /* ... полные данные ... */ }
    // При следующей сессии: добавить новые карты СЮДА
  ]
}
```

---

## 👥 РЕАЛЬНЫЕ ОТЗЫВЫ И СООБЩЕСТВО — FEATURE В ПРИЛОЖЕНИИ

### Зачем это нужно

Пользователь хочет видеть в приложении **реальные отзывы** от людей, которые собрали похожие конфигурации. Это решает две задачи:
1. Подтверждает, что данные в приложении основаны на реальном опыте, а не выдуманы
2. Даёт практические инсайты, которых нет в официальных спецификациях (нагрев, шум, проблемы с драйверами, реальная скорость в ComfyUI и т.д.)

### Как это отображается в UI

Для каждого пресета сборки или компонента — раздел **"Что говорят люди"** с:
- Коротким цитатой (1-2 предложения, выжимка сути)
- Именем/никнеймом источника (реальный)
- Иконкой платформы (Reddit, HuggingFace, Tom's Hardware, B&H, YouTube)
- Рейтингом отзыва (звёзды)
- **Прямой ссылкой на оригинальный пост/отзыв** — чтобы пользователь мог проверить

### Пример структуры данных отзыва

```javascript
const COMMUNITY_REVIEWS = {
  "rtx5090_ai_build": [
    {
      id: "mithanlab_2025",
      author: "MIT HAN Lab (официальный блог)",
      platform: "blog",
      platform_icon: "🎓",
      excerpt: "RTX 5090 + Threadripper 7960X + 192GB DDR5-ECC: реальная сборка для AI-воркстанции. Проблема: Blackwell SM120 работает только в torch nightly. Motherboard: ASUS TRX50-SAGE Pro WS.",
      sentiment: "positive",
      rating: 5,
      source_url: "https://hanlab.mit.edu/blog/rtx5090",
      date: "2025-Q1",
      tags: ["ai-workstation", "rtx5090", "threadripper", "dual-gpu"],
      verified: true
    },
    {
      id: "huggingface_discuss_2025",
      author: "localprompt.ai (HuggingFace Discuss)",
      platform: "huggingface",
      platform_icon: "🤗",
      excerpt: "Собрал local AI workstation на RTX 5090 + Debian 12. DeepSeek-Coder-V2-Lite Q8_0 — лучший выбор для offline coding. Важно: sm_120 требует torch nightly + CUDA 13.0.1.",
      sentiment: "positive",
      rating: 4,
      source_url: "https://discuss.huggingface.co/t/local-llm-and-ml-platform-with-rtx-5090-gpu/166394",
      date: "2025",
      tags: ["rtx5090", "llm-inference", "local-ai", "coding"],
      verified: true
    },
    {
      id: "arsenalpc_dual5090",
      author: "ArsenalPC Lab (Newegg Community)",
      platform: "newegg",
      platform_icon: "🛒",
      excerpt: "Dual RTX 5090 (64GB суммарно): 6704 AI TOPS. Макс. температура под нагрузкой — 82°C на одной, 77°C на другой. Превышает единственный RTX PRO 6000 по AI TOPS (4000 TOPS) более чем в 1.5×.",
      sentiment: "positive",
      rating: 5,
      source_url: "https://www.newegg.com/gamer/community/t/our-new-ai-workstation-is-complete-and-its-alive/5423",
      date: "2025-2026",
      tags: ["dual-gpu", "rtx5090", "ai-workstation", "thermals"],
      verified: true
    },
    {
      id: "bhphoto_pro6000_review",
      author: "Doug (B&H Photo Video, verified purchase)",
      platform: "bhphotovideo",
      platform_icon: "📷",
      excerpt: "RTX PRO 6000 Blackwell 96GB: работает с почти всеми крупными моделями в Ollama. Одна карта обошла конфигурацию из ДВУХ RTX 6000 Ada в DaVinci Resolve. Время рендера 5.5ч → 1ч.",
      sentiment: "positive",
      rating: 5,
      source_url: "https://www.bhphotovideo.com/c/product/1895402-REG/nvidia_900_5g144_2200_000_rtx_pro_6000_blackwell.html",
      date: "2025-09",
      tags: ["rtx-pro-6000", "ollama", "davinci-resolve", "professional"],
      verified: true
    },
    {
      id: "techradar_puget_5090",
      author: "Puget Systems / TechRadar",
      platform: "techradar",
      platform_icon: "📰",
      excerpt: "RTX 5090 в Adobe Premiere Pro: +9% vs 4090. В DaVinci Resolve: +17% vs 4090, +35% vs 3090 Ti. В After Effects: +35% vs 4090 в 3D-рендеринге. Реальные бенчмарки от профессиональной лаборатории.",
      sentiment: "positive",
      rating: 4,
      source_url: "https://www.techradar.com/pro/usd2000-nvidia-geforce-rtx-5090-gets-tested-on-creative-software-and-ai-and-obliterates-absolutely-everything-in-its-path",
      date: "2025-01",
      tags: ["premiere-pro", "davinci-resolve", "after-effects", "creative"],
      verified: true
    }
  ],
  "macbook_m5max_comparison": [
    // Отзывы по MacBook M5 Max для секции сравнения
    // Найти при следующей сессии разработки
  ]
};
```

### Инструкция для Claude: как искать реальные отзывы при каждой сессии

При запуске новой сессии разработки — **обязательно искать свежие отзывы** по следующим запросам:

```
// Запросы для поиска (выполнять перед обновлением данных):

1. "RTX 5090 local AI build experience {current_year}" 
   → Reddit r/LocalLLaMA, r/Amd, r/MachineLearning, HuggingFace Discuss

2. "RTX PRO 6000 Blackwell review AI inference {current_year}"
   → B&H Photo reviews, GamersNexus, Tom's Hardware forums

3. "Wan 2.2 RTX 5090 ComfyUI speed benchmark {current_year}"
   → YouTube, GitHub Issues, Reddit r/StableDiffusion

4. "HunyuanVideo ComfyUI experience GPU {current_year}"
   → Reddit, HuggingFace community, GitHub

5. "[любой новый AI-видео-пресет] RTX 5090 benchmark"
   → Ищи по модели + карте

// ПРАВИЛА:
// ✅ Принимай: Reddit, Tom's Hardware, HuggingFace Discuss, B&H Photo reviews, MIT/Stanford labs, GamersNexus, Puget Systems
// ✅ Принимай: YouTube-каналы с реальными тестами (GamersNexus, LTT, но всегда давай ссылку на конкретное видео)
// ❌ НЕ выдумывай отзывы — только реальные находки с реальными URL
// ❌ Если не нашёл свежих — показывай старые с пометкой даты
// ⚠️ Всегда ставь дату отзыва и verified: true только если URL рабочий
```

### Правила отображения отзывов в UI

```javascript
// В компоненте ReviewCard:
// - Показывать не более 3 отзывов на компонент/пресет
// - Кнопка "Ещё отзывы" → открывает расширенный список
// - Каждый отзыв: аватар-иконка платформы, автор, дата, цитата, ⭐ рейтинг, [Источник ↗]
// - Цвет рамки: зелёный = positive, красный = negative, жёлтый = mixed
// - Если verified: true — показывать зелёную галочку рядом с именем
// - НЕ показывать отзывы без source_url — только верифицированные
// - Если отзывов для компонента нет: показывать "Отзывов пока нет. Будут добавлены в следующей версии."
```

---

## 🔍 ИНСТРУКЦИЯ: КАК ПРОВОДИТЬ ПОИСК В ИНТЕРНЕТЕ ПРИ КАЖДОЙ СЕССИИ

Это не просто "обновить цены". Это активное исследование состояния рынка.

### Алгоритм для Claude при запуске новой сессии

```
ШАГ 1 — ЖЕЛЕЗО (5-7 поисковых запросов)
────────────────────────────────────────
web_search: "NVIDIA GPU release 2026 new model"
web_search: "RTX 5090 price Newegg {current_month} {current_year}"
web_search: "RTX PRO 6000 Blackwell availability {current_year}"
web_search: "best CPU AI workstation AMD Intel {current_year}"

→ Результат: обновить цены в JSON, добавить новые карты если есть

ШАГ 2 — AI МОДЕЛИ (3-5 запросов)
────────────────────────────────────────
web_search: "best open source video generation model {current_year}"
web_search: "Wan 3 OR HunyuanVideo 2.0 OR LTX-3 release"
web_search: "best local LLM 2026 VRAM comparison"

→ Результат: добавить новые модели, обновить бенчмарки

ШАГ 3 — РЕАЛЬНЫЕ ОТЗЫВЫ (2-3 запроса)
────────────────────────────────────────
web_search: "RTX 5090 AI build review {current_year} reddit OR forum"
web_search: "local AI workstation ComfyUI HunyuanVideo experience {current_year}"

→ Результат: добавить/обновить реальные отзывы в COMMUNITY_REVIEWS

ШАГ 4 — VR (1-2 запроса)
────────────────────────────────────────
web_search: "best VR headset {current_year} PC lightweight high resolution"
web_search: "Valve Steam Frame release date specs {current_year}"

→ Результат: обновить VR-таблицу

После всех поисков:
- Обновить APP_META.dataVersion на текущую дату
- Убрать из ROADMAP выполненные пункты
- Добавить новые задачи от пользователя
- Сообщить пользователю: "Данные обновлены. Найдены следующие изменения: ..."
```

---

## ✅ РЕАЛЬНЫЕ СБОРКИ ИЗ СООБЩЕСТВА — ПРИМЕРЫ ДЛЯ ПРЕСЕТОВ

### Пресет "MIT HAN Lab AI Workstation" (верифицированный)
**Источник:** https://hanlab.mit.edu/blog/rtx5090

```
GPU:      NVIDIA GeForce RTX 5090 × 2 (dual-GPU)
CPU:      AMD Ryzen Threadripper 7960X (24-Core 4.2GHz) — $1,299
MB:       ASUS Pro WS TRX50-SAGE WIFI — $899
RAM:      G.Skill 192GB DDR5-6400 ECC Registered (4×48GB) — $1,349
Storage:  WD Black SN850P 8TB NVMe × 2 — $699 × 2
PSU:      Corsair AX1600i 1600W Titanium — $609
CPU Cool: Arctic Cooling Freezer 4U-M Rev 2 — $64
Итого без GPU: ~$5,620
+ 2× RTX 5090: ~$5,000-6,000
= Полная сборка: ~$10,600-11,600
```
Реальные выводы: Blackwell SM120 требует torch nightly + CUDA 13.0.1. Рекомендуют OS: Debian 12 с pinned драйвером.

### Пресет "ArsenalPC Dual 5090 AI" (верифицированный)
**Источник:** https://www.newegg.com/gamer/community/t/our-new-ai-workstation-is-complete-and-its-alive/5423

```
GPU:      NVIDIA RTX 5090 × 2 (64GB GDDR7, 6704 AI TOPS total)
CPU Cool: Arctic Liquid Freezer III
Case:     Fractal Design (extreme airflow)
Thermals: под нагрузкой 82°C / 77°C (два GPU)
AI TOPS:  6704 total (vs 4000 у одного RTX PRO 6000)
```
Реальные выводы: двойной 5090 бьёт одиночный PRO 6000 по AI TOPS, но PCIe bandwidth — узкое место для больших моделей.

