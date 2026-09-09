🔍 H303 IPv4 Scanner
High‑Performance Cross‑Platform Port Scanner
Auto‑calibrating · 32 Countries · Rust‑Powered

<p align="center"> <img src="https://img.shields.io/badge/Python-3.6%2B-blue?style=for-the-badge&logo=python" alt="Python"> <img src="https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey?style=for-the-badge" alt="Platform"> <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License"> <img src="https://img.shields.io/badge/Engine-inertia--scanner-orange?style=for-the-badge" alt="Engine"> <img src="https://img.shields.io/badge/PRs-welcome-brightgreen?style=for-the-badge" alt="PRs"> </p>
📖 Table of Contents
Overview

Key Features

How It Works

System Requirements

Installation

Usage Guide

Configuration & Customization

Architecture & Components

Performance & Resource Management

Output & Logging

Troubleshooting

Contributing

Legal Disclaimer

Author & Channels

License

🌐 Overview
H303 IPv4 Scanner is a modern, cross‑platform network scanner designed for security researchers and system administrators. It targets 32 vulnerable countries using a curated list of IPv4 subnets (aggregated from IPdeny, IPverse, and other reliable sources). The scanner leverages the Rust‑based inertia-scanner engine to achieve extremely fast TCP port scanning, while a Python controller handles intelligent chunking, parallelism, resource monitoring, and fault‑tolerant resume capabilities.

Unlike traditional scanners that either overwhelm your system or are too slow, H303 auto‑calibrates its concurrency, chunk size, and timeout based on your current CPU cores and available RAM (using up to 90% of RAM and 80% of CPU). For low‑end hardware (e.g., <4 cores or <2GB RAM), it automatically switches to Safe Mode to prevent crashes.

Whether you are conducting a penetration test, auditing your own network, or gathering threat intelligence, H303 provides a reliable, high‑throughput solution with minimal manual tuning.

✨ Key Features
Feature	Description
🌍 32 Targeted Countries	US, CN, RU, BR, IN, JP, DE, GB, FR, IT, CA, AU, ES, NL, MX, ID, TR, PL, EG, ZA, AR, VN, TH, MY, SG, HK, PK, NG, BD, UA, KZ, UZ – each with an extended subnet list (far more than typical free databases).
⚙️ Auto‑Calibration	Dynamically calculates optimal concurrency, chunk size, and timeout using your system’s real‑time CPU and RAM stats. No manual tweaking required.
🛡️ Safe Mode	Automatically engages when total RAM < 2GB or CPU cores < 4, reducing concurrency to a stable level (e.g., 50 connections, 100 IPs/chunk) to avoid resource exhaustion.
💾 Resume Support	Saves progress to scan_resume.json every few found servers. If the scan is interrupted (Ctrl+C, power loss, or network drop), simply rerun the same selection to continue from where you left off.
📊 Real‑time Resource Monitor	Displays live CPU usage, memory percentage, available RAM, network speed (MB/s), and packet drops – all updated on the status line. Helps you spot network congestion or system bottlenecks instantly.
🚀 Parallel Workers	Uses a ThreadPoolExecutor with up to 8 workers (depending on concurrency) to distribute scanning load. Each worker handles a sub‑chunk of IPs, dramatically increasing overall throughput.
📁 Auto‑Save to Downloads	All found IP:PORT entries are saved to a timestamped .txt file inside your system’s Downloads folder (or current directory if that fails). No need to manually copy results.
📦 Self‑Installing Dependencies	On first run, the script automatically installs all required Python libraries (psutil, aiohttp, paramiko, rich, colorama, fake-useragent) and even attempts to install inertia-scanner via pip.
🖥️ Cross‑Platform	Works flawlessly on Windows (admin rights required), Linux (sudo), and macOS (sudo). The same codebase runs everywhere.
🔍 Flexible Port Selection	You can enter any comma‑separated list of ports. Defaults are 22,23,2053,3389,80,443 – the most common attack vectors and admin interfaces.
📈 Interactive Progress	Uses rich (if available) to display a beautiful table of results and a summary panel. Falls back to plain text if rich is missing.
🧠 How It Works
The scanning process is orchestrated in a pipeline of several stages:

Subnet Loading
The selected country’s subnet list is retrieved from the built‑in COUNTRY_SUBNETS dictionary. Each subnet is expanded into a network object (ipaddress).

Chunk Generation (Round‑Robin)
All subnets are shuffled to avoid scanning adjacent blocks in order (which might trigger IDS alerts). The generator yields chunks of IPs (size determined by auto‑calibration). Each chunk contains IPs from different subnets, interleaved randomly.

Parallel Worker Dispatch
Each chunk is further divided into sub‑chunks, each sent to a separate worker thread (up to 8). Each worker invokes the inertia-scanner Rust binary with the -t file://..., -p <ports>, -c <concurrency>, and --timeout <ms> parameters.

Result Collection
The Rust scanner outputs a JSON file (inertia_output.json). The Python worker parses this file, extracts open IP:PORT pairs, and creates ScanResult objects.

Resource Monitoring
A background thread continuously polls psutil for CPU, memory, and network stats. These values are displayed in the live status bar.

Progress & Resume
After every few found servers, the entire result list and total checked count are written to scan_resume.json. If the script restarts, it loads this file and continues from that point (skipping already‑scanned IPs).

Final Output
Once the target number of servers is found (or all IPs are exhausted), the results are saved to ~/Downloads/scan_results_YYYYMMDD_HHMMSS.txt. A final summary is printed to the console.

📦 System Requirements
Operating System: Windows 7+, Linux (any distribution), macOS 10.12+

Python: 3.6 or higher (3.8+ recommended)

Rust (optional): While inertia-scanner is installed via pip, it may require a Rust toolchain. If auto‑install fails, you can manually install Rust from rustup.rs and then run pip install inertia-scanner.

Network: A stable internet connection (for downloading dependencies and scanning external IPs).

Permissions:

Windows: Administrator rights (right‑click → Run as Administrator).

Linux/macOS: sudo access (sudo python3 scanner.py).

💡 Note: The scanner does not require any firewall modifications or additional libraries beyond those automatically installed.

🚀 Installation
Option 1 – Clone from GitHub (Recommended)
bash
git clone https://github.com/nothing303/H303-Scanner.git
cd H303-Scanner
Option 2 – Direct Download
Download the scanner.py file (or the whole repository as a ZIP) from the GitHub page and place it in a folder of your choice.

Dependencies
The script will automatically install missing Python packages on the first run. However, if you prefer to install them manually:

bash
pip install psutil aiohttp paramiko rich colorama fake-useragent inertia-scanner
🎮 Usage Guide
Run the scanner with administrative privileges:

Platform	Command
Windows	Right‑click scanner.py → Run as Administrator (or open CMD as Admin and type python scanner.py)
Linux	sudo python3 scanner.py
macOS	sudo python3 scanner.py
You will then be guided through an interactive menu:

Select a country – a numbered list of 32 countries appears. Enter the number corresponding to your target.

Enter ports – type a comma‑separated list (e.g., 22,80,443,8080) or just press Enter to use the default set 22,23,2053,3389,80,443.

How many servers to find? – specify the number of live servers you want to discover (e.g., 10 for a quick test, 100 for a deeper scan). The scan will stop as soon as this number is reached or when all IPs are exhausted.

Example session:

text
Select country (number): 1
Selected: United States
Ports: 22,80,443
How many servers to find? (e.g., 10): 5
The scan will start immediately, and you will see a live status line updating every few seconds.

⚙️ Configuration & Customization
All tunable parameters are at the top of the scanner.py file (or inside the SmartScanner class). You can modify:

DEFAULT_PORTS – change the default port list.

SERVICE_NAMES – add or remove port‑to‑service mappings.

COUNTRY_SUBNETS – extend or replace subnet lists for any country.

VULNERABLE_COUNTRIES – add new countries with their names.

To adjust auto‑calibration behaviour, edit the auto_calibrate() function:

Modify the upper bound of concurrency (currently capped at 500).

Change the CPU/RAM contribution factors (cpu_cores * 40 + mem_total_gb * 15).

Tweak the safe_mode threshold (mem < 2GB or cores < 4).

🏗️ Architecture & Components
The project consists of a single Python script that integrates several modular components:

Component	Responsibility
request_admin_early()	Elevates privileges on Windows or ensures root/sudo on Unix.
ensure_libraries()	Checks for and installs required Python packages.
ResourceMonitor	Runs a background thread to poll CPU, RAM, and network metrics.
SubnetChunkGenerator	Shuffles subnets, generates IP chunks in round‑robin fashion, and tracks progress.
SmartScanner	Main orchestrator – manages concurrency, workers, resume logic, and result collection.
ScanResult	Dataclass holding IP, port, service, country, timestamp, etc.
auto_calibrate()	Computes optimal concurrency, chunk size, and timeout based on system resources.
The scanner uses asyncio for handling worker tasks concurrently, but the actual scanning is offloaded to the Rust binary (inertia) via subprocess, ensuring near‑native performance.

⚡ Performance & Resource Management
Concurrency: Determines how many simultaneous TCP connections inertia-scanner opens per worker. Auto‑calibration ensures it never exceeds your system’s capacity.

Chunk Size: The number of IPs sent to each worker. Larger chunks reduce overhead but may cause timeouts; smaller chunks increase parallelism. The default formula concurrency * 2.0 gives a good balance.

Timeout: The total time (in milliseconds) allowed for inertia-scanner to finish a chunk. It is dynamically set to at least 60 seconds and scales with chunk size (5 extra seconds per 100 IPs).

Safe Mode: For weak devices, concurrency drops to 50, chunk size to 100, and timeout to 60 seconds – ensuring the system remains responsive.

Resource Monitor: The live status bar shows CPU%, RAM%, network speed (MB/s), and packet drops. If packet drops exceed zero, it indicates network congestion or firewall throttling – you may consider lowering concurrency manually.

📁 Output & Logging
All successful scans produce two types of output:

Final Results File
Saved as scan_results_YYYYMMDD_HHMMSS.txt inside your Downloads folder.
Each line contains one found server in the format IP:PORT.
Example:

text
192.168.1.10:22
192.168.1.10:80
10.0.0.5:443
Resume File (scan_resume.json)
Stored in the working directory. Contains the full list of already‑found results, total checked count, and metadata. If you run the scanner again with the same country and ports, it will automatically resume from where it stopped.

Console Output
Live updates show the number of checked IPs, found servers, speed, elapsed time, and resource usage. At the end, a summary table (using rich) displays all found servers grouped by port.

❓ Troubleshooting
Issue	Solution
inertia command not found	The script attempts to install inertia-scanner automatically. If it fails, install Rust (https://rustup.rs/) and run pip install inertia-scanner.
Permission denied (Linux/macOS)	You must run with sudo.
Admin prompt doesn’t appear (Windows)	Right‑click scanner.py and select “Run as Administrator”.
Scan hangs or takes too long	Check your network connection. You may also reduce the target number or use a smaller port list. The resume feature allows you to restart without losing progress.
ModuleNotFoundError for psutil etc.	The auto‑installer should handle this. If not, manually run pip install psutil aiohttp paramiko rich colorama fake-useragent.
Results file not saved in Downloads	If the Downloads folder is inaccessible (e.g., read‑only), the file is saved in the current working directory.
High packet drops	This indicates network congestion. Reduce concurrency by manually editing concurrency in the auto_calibrate() function, or select a different country with smaller subnets.
🤝 Contributing
Contributions are welcome! To contribute:

Fork the repository.

Create a new branch (git checkout -b feature/your-feature).

Make your changes and commit (git commit -am 'Add some feature').

Push to the branch (git push origin feature/your-feature).

Open a Pull Request.

Please ensure your code follows PEP 8 guidelines and includes docstrings for new functions. You can also open issues for bug reports or feature requests.

⚖️ Legal Disclaimer
IMPORTANT NOTICE
This software is provided solely for educational purposes, authorised security testing, and network defence research.
The author (nothing303) does not condone, encourage, or support any unauthorised access to computer systems, networks, or data.

By using this tool, you agree that:

You have explicit written permission from the owner of the target network(s) before performing any scan.

You are solely responsible for complying with all applicable local, national, and international laws regarding network intrusion, data privacy, and computer misuse.

The author and contributors shall not be held liable for any damages, legal actions, or other consequences arising from the misuse of this software.

If you are unsure about the legality of scanning a particular target, do not use this tool and consult a legal professional.

This tool is not intended for use in any malicious, destructive, or criminal activities. The author reserves the right to deny support to anyone using this software for unethical purposes.

👤 Author & Channels
GitHub: nothing303

Telegram Channel: @iazmonman – for updates, news, and support.

Email: (if you wish to provide one – optional)

Feel free to join the Telegram channel for announcements, tips, and community discussions. For direct inquiries, you can open an issue on GitHub or reach out via Telegram.

📄 License
This project is licensed under the MIT License – see the LICENSE file for details.

text
MIT License

Copyright (c) 2026 H303

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
<p align="center">Made with ❤️ by <strong>nothing303</strong> &nbsp;|&nbsp; Stay ethical, stay secure.</p>
