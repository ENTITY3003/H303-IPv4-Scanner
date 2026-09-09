# 🔍 H303 IPv4 Scanner

A high-performance, cross-platform port scanner targeting **32 vulnerable countries** using the Rust-based **`inertia-scanner`** engine. Automatically calibrates concurrency based on your system's CPU/RAM to maximize speed without crashing.

![Python](https://img.shields.io/badge/Python-3.6%2B-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey)

## ✨ Features

- **🌍 32 Countries** (US, CN, RU, BR, IN, JP, DE, GB, FR, IT, CA, AU, ES, NL, MX, ID, TR, PL, EG, ZA, AR, VN, TH, MY, SG, HK, PK, NG, BD, UA, KZ, UZ)
- **⚙️ Auto-Calibration**: Uses up to 90% RAM & 80% CPU dynamically to set `concurrency`, `chunk size`, and `timeout`.
- **🛡️ Safe Mode**: Automatically enables low-resource mode for weak hardware/modems.
- **💾 Resume Support**: If interrupted, it resumes from where it left off (`scan_resume.json`).
- **📊 Real-time Monitor**: Shows CPU%, RAM%, Network Speed, and packet drops.
- **📁 Auto-Save**: Saves found `IP:PORT` results directly to your `Downloads` folder.
- **🚀 Parallel Workers**: Uses `ThreadPoolExecutor` to distribute chunks across multiple workers.

## 📦 Requirements

- Python 3.6 or higher
- **`inertia-scanner`** (Rust binary) – The script will attempt to install it automatically via pip (`inertia-scanner`), but ensure you have **Rust** installed if auto-install fails.

> The script auto-installs other Python dependencies (`psutil`, `aiohttp`, `paramiko`, `rich`, `colorama`, `fake-useragent`) on first run.

## 🚀 Quick Start

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/H303-Scanner.git
   cd H303-Scanner# H303-IPv4-Scanner
High-performance cross-platform IPv4 port scanner with auto-calibration and resume support.
