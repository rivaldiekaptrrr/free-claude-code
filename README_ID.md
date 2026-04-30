<div align="center">

# 🤖 Free Claude Code

Gunakan Claude Code CLI, VS Code, JetBrains ACP, atau chat bot melalui proxy Anda sendiri yang kompatibel dengan Anthropic secara gratis (atau dengan provider pilihan Anda).

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)
[![Python 3.14](https://img.shields.io/badge/python-3.14-3776ab.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/downloads/)
[![uv](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json&style=for-the-badge)](https://github.com/astral-sh/uv)
[![Tested with Pytest](https://img.shields.io/badge/testing-Pytest-00c0ff.svg?style=for-the-badge)](https://github.com/Alishahryar1/free-claude-code/actions/workflows/tests.yml)
[![Type checking: Ty](https://img.shields.io/badge/type%20checking-ty-ffcc00.svg?style=for-the-badge)](https://pypi.org/project/ty/)

Free Claude Code meneruskan trafik Anthropic Messages API dari Claude Code ke NVIDIA NIM, OpenRouter, DeepSeek, LM Studio, llama.cpp, atau Ollama. Ini menjaga protokol client-side Claude Code tetap stabil sambil memberi Anda kebebasan memilih model gratis, berbayar, atau lokal.

[Mulai Cepat](#mulai-cepat) · [Provider](#pilih-provider) · [Koneksi](#hubungkan-ke-claude-code) · [Troubleshooting](#pemecahan-masalah) · [Pengembangan](#pengembangan)

</div>

<div align="center">
  <img src="pic.png" alt="Free Claude Code beraksi" width="700">
</div>

## Fitur Utama

- **Proxy Drop-in**: Gantikan endpoint API Anthropic asli dengan proxy ini tanpa mengubah kode klien.
- **Enam Backend Provider**: Mendukung NVIDIA NIM, OpenRouter, DeepSeek, LM Studio, llama.cpp, dan Ollama.
- **Routing Per-Model**: Atur jalur model Opus, Sonnet, Haiku, dan trafik fallback ke provider yang berbeda-beda.
- **Fitur Canggih**: Mendukung streaming, penggunaan tool (tool use), penanganan blok penalaran (thinking blocks), dan optimasi request lokal.
- **Integrasi Bot**: Opsional pembungkus bot Discord atau Telegram untuk sesi coding jarak jauh.
- **Catatan Suara**: Transkripsi voice-note opsional melalui Whisper lokal atau NVIDIA NIM.

---

## Mulai Cepat

### 1. Instalasi Kebutuhan

Instal [Claude Code](https://github.com/anthropics/claude-code), lalu instal `uv` dan Python 3.14.

**macOS/Linux:**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv self update
uv python install 3.14
```

**Windows PowerShell:**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
uv self update
uv python install 3.14
```

### 2. Clone Dan Konfigurasi

```bash
git clone https://github.com/Alishahryar1/free-claude-code.git
cd free-claude-code
cp .env.example .env
```

Edit file `.env` dan pilih satu provider. Contoh untuk NVIDIA NIM (default):

```dotenv
NVIDIA_NIM_API_KEY="nvapi-your-key"
MODEL="nvidia_nim/z-ai/glm4.7"
ANTHROPIC_AUTH_TOKEN="freecc"
```

*Catatan: Gunakan secret apa saja untuk `ANTHROPIC_AUTH_TOKEN`. Claude Code akan mengirimkan nilai yang sama kembali ke proxy ini.*

### 3. Jalankan Proxy

```bash
uv run uvicorn server:app --host 0.0.0.0 --port 8082
```

Atau instal sebagai tool global:

```bash
uv tool install git+https://github.com/Alishahryar1/free-claude-code.git
fcc-init
free-claude-code
```

### 4. Jalankan Claude Code

Arahkan `ANTHROPIC_BASE_URL` ke root proxy. Jangan tambahkan `/v1`.

**Bash:**

```bash
ANTHROPIC_AUTH_TOKEN="freecc" ANTHROPIC_BASE_URL="http://localhost:8082" claude
```

---

## Pilih Provider

Format nilai model: `provider_id/model/name`. `MODEL` digunakan sebagai fallback.

| Provider | Prefiks | Transport | Kunci API | Base URL Default |
| --- | --- | --- | --- | --- |
| NVIDIA NIM | `nvidia_nim/...` | OpenAI chat | `NVIDIA_NIM_API_KEY` | `https://integrate.api.nvidia.com/v1` |
| OpenRouter | `open_router/...` | Anthropic | `OPENROUTER_API_KEY` | `https://openrouter.ai/api/v1` |
| DeepSeek | `deepseek/...` | Anthropic | `DEEPSEEK_API_KEY` | `https://api.deepseek.com/anthropic` |
| LM Studio | `lmstudio/...` | Anthropic | Tidak ada | `http://localhost:1234/v1` |
| Ollama | `ollama/...` | Anthropic | Tidak ada | `http://localhost:11434` |

<details>
<summary><b>Lihat Detail Provider</b></summary>

- **NVIDIA NIM**: Ambil kunci di [build.nvidia.com](https://build.nvidia.com/explore/discover). Contoh: `nvidia_nim/z-ai/glm4.7`.
- **OpenRouter**: Cocok untuk model gratis. Contoh: `open_router/stepfun/step-3.5-flash:free`.
- **DeepSeek**: Menggunakan endpoint kompatibel Anthropic milik DeepSeek.
- **Local (Ollama/LM Studio)**: Jalankan model di mesin Anda sendiri untuk privasi maksimal.

</details>

---

## Hubungkan Ke Claude Code

### VS Code Extension
Cari `claude-code.environmentVariables` di Settings, lalu tambahkan:
```json
"claudeCode.environmentVariables": [
  { "name": "ANTHROPIC_BASE_URL", "value": "http://localhost:8082" },
  { "name": "ANTHROPIC_AUTH_TOKEN", "value": "freecc" }
]
```

### Model Picker (`claude-pick`)
Gunakan `claude-pick` untuk memilih model saat peluncuran:
```bash
alias claude-pick="/path/ke/free-claude-code/claude-pick"
claude-pick
```

---

## Pemecahan Masalah (Troubleshooting)

### Error `undefined ... input_tokens`
- Pastikan `ANTHROPIC_BASE_URL` adalah `http://localhost:8082`, **bukan** `.../v1`.
- Update ke commit terbaru untuk perbaikan metadata streaming.

### HTTP 400 dari Ollama/LM Studio
- Periksa apakah server lokal sudah mendukung `POST /v1/messages`.
- Pastikan ukuran konteks (`--ctx-size` di llama.cpp) cukup besar.

---

## Pengembangan

Struktur proyek:
- `api/`: Endpoint FastAPI dan logika routing.
- `providers/`: Adapter untuk berbagai provider (NIM, Ollama, dll).
- `messaging/`: Integrasi bot Discord/Telegram.
- `core/`: Protokol Anthropic dan utilitas SSE.

Jalankan cek sebelum push:
```bash
uv run ruff format && uv run ruff check && uv run ty check && uv run pytest
```

---

## Lisensi

Lisensi MIT. Lihat [LICENSE](LICENSE) untuk detailnya.
