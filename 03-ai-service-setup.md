# 🤖 AI Service Setup — Ollama, STT, CUDA & FFmpeg

> Setup guide for the AI components of the **Curia / Najda** project:
> the **LLM triage service** (Ollama + Qwen) and the **Speech-to-Text
> service** (faster-whisper). Tuned for best performance on an
> **NVIDIA RTX 4070 (12 GB VRAM)**.

**Last updated:** May 22, 2026
**Target GPU:** NVIDIA GeForce RTX 4070 (12 GB VRAM, Ada Lovelace)

---

## 📋 What You'll Install

| # | Component | Purpose |
|---|-----------|---------|
| 1 | NVIDIA GPU Driver | Lets Windows + CUDA use the RTX 4070 |
| 2 | CUDA Toolkit 12.x | GPU compute for PyTorch / CTranslate2 |
| 3 | Ollama | Runs the local LLM (Qwen 2.5) |
| 4 | Qwen 2.5 model | The triage reasoning LLM |
| 5 | Python 3.11 + venv | Runtime for both AI services |
| 6 | PyTorch (CUDA build) | Deep-learning backend |
| 7 | faster-whisper | Speech-to-Text engine (Whisper) |
| 8 | FFmpeg | Audio decoding / conversion |

> **Why the RTX 4070 matters:** with 12 GB VRAM you can comfortably run
> Qwen 2.5:7b **fully on GPU**, and even step up to **Qwen 2.5:14b**.
> Whisper `large-v3-turbo` runs in `float16` with plenty of headroom.
> The original project docs assumed a 6 GB GTX 1660 Ti / RTX 2060 — the
> 4070 removes those constraints, so this guide uses faster settings.

---

## 1️⃣ NVIDIA GPU Driver

Install the latest **Game Ready** or **Studio** driver for the RTX 4070.
The Studio driver is slightly more stable for compute/ML work.

- **Download:** https://www.nvidia.com/Download/index.aspx
- Select: GeForce RTX 40 Series → RTX 4070 → Windows 11.

**Verify** (open PowerShell):
```powershell
nvidia-smi
```
This prints the driver version, GPU name, VRAM, and — top right — the
**maximum CUDA version** the driver supports. Make sure that number is
**12.x** (it will be, on any recent driver).

---

## 2️⃣ CUDA Toolkit 12.x

CUDA is what lets PyTorch and CTranslate2 (the engine behind faster-whisper)
run on the GPU.

- **Download:** https://developer.nvidia.com/cuda-downloads
- Choose: Windows → x86_64 → 11 → exe (local).
- Recommended: **CUDA 12.4** (or any 12.x — 12.3 or newer).

> ⚠️ **Important compatibility note for faster-whisper:**
> The latest `ctranslate2` (the runtime inside faster-whisper) requires
> **CUDA 12 and cuDNN 9**. CUDA 12.3 or newer is the safe target. The
> RTX 4070 fully supports CUDA 12.x, so this is not a problem.

**You usually do NOT need to install cuDNN separately.** The modern
PyTorch CUDA wheels and the `nvidia-*` pip packages bundle the cuDNN and
cuBLAS libraries faster-whisper needs. Installing CUDA via pip wheels
(shown in Step 7) is the simplest path on Windows.

**Verify CUDA toolkit:**
```powershell
nvcc --version
```

---

## 3️⃣ Ollama (LLM Runtime)

Ollama runs the local LLM that performs triage reasoning.

- **Download:** https://ollama.com/download
- Install the Windows installer with default options.
- Ollama runs as a background service and exposes an API at
  `http://localhost:11434`.

**Verify:**
```powershell
ollama --version
```

Ollama automatically uses the RTX 4070 — no manual GPU config needed. You
can confirm GPU usage by running `nvidia-smi` while a model is generating.

---

## 4️⃣ Qwen 2.5 Model

The project uses **Qwen 2.5:7b** as the triage LLM. Pull it:

```powershell
ollama pull qwen2.5:7b
```

- Download size: ~4.7 GB.
- On the RTX 4070's 12 GB VRAM, the 7b model runs **fully on GPU** — fast.

**Test it:**
```powershell
ollama run qwen2.5:7b
# type a prompt, then /bye to exit
```

### Optional — step up to Qwen 2.5:14b

Because the 4070 has 12 GB VRAM, you can also run the larger 14b model,
which the project's AI plan lists as a comparison candidate:

```powershell
ollama pull qwen2.5:14b
```

- Download size: ~9 GB.
- The 14b model mostly fits in VRAM on a 4070; expect somewhat slower
  generation than 7b but better reasoning. Good for accuracy comparisons.

> Keep the 7b as the default for interactive triage (latency matters);
> use 14b for offline evaluation runs.

### Useful Ollama commands

```powershell
ollama list                 # show downloaded models
ollama ps                   # show running models + GPU/CPU split
ollama rm qwen2.5:14b        # delete a model to free disk space
```

---

## 5️⃣ Python 3.11 + Virtual Environment

The AI service and STT service run on Python. Use **Python 3.11** (best
support for torch / ctranslate2 wheels).

- Download: https://www.python.org/downloads/release/python-31115/
- During install: ✅ "Add python.exe to PATH".

Create an isolated environment for the AI service:
```powershell
cd D:\path\to\ai-service
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
```

> Keep the **triage AI service** and the **STT service** in separate
> virtual environments if they live in separate folders — the project
> deploys them as two independent FastAPI processes.

---

## 6️⃣ PyTorch with CUDA (GPU build)

⚠️ The default `pip install torch` gives you the **CPU-only** build.
For the RTX 4070 you must install the **CUDA build** explicitly:

```powershell
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
```

(`cu124` = CUDA 12.4 build. Use `cu121` if you installed CUDA 12.1.)

**Verify the GPU is detected:**
```powershell
python -c "import torch; print(torch.cuda.is_available()); print(torch.cuda.get_device_name(0))"
```
Expected output:
```
True
NVIDIA GeForce RTX 4070
```
If it prints `False`, you installed the CPU build — reinstall with the
`--index-url` above.

---

## 7️⃣ faster-whisper (Speech-to-Text)

The STT service uses **faster-whisper** with the **`large-v3-turbo`**
model — the project's chosen STT setup.

### Install

```powershell
pip install faster-whisper
```

faster-whisper pulls in `ctranslate2` automatically. On the RTX 4070, the
GPU libraries (cuDNN 9, cuBLAS) it needs are provided by the `nvidia-*`
pip packages that come with the PyTorch CUDA wheels installed in Step 6.

> If you ever get the error
> `"This CTranslate2 package was not compiled with CUDA support"` or a
> cuDNN load failure, install the NVIDIA runtime wheels explicitly:
> ```powershell
> pip install nvidia-cublas-cu12 nvidia-cudnn-cu12
> ```

### Recommended settings for the RTX 4070

| Setting | Value | Why |
|---------|-------|-----|
| `device` | `"cuda"` | Use the GPU |
| `compute_type` | `"float16"` | Best speed/accuracy on the 4070; plenty of VRAM |
| `model_size` | `"large-v3-turbo"` | Project default — fast, accurate |
| `language` | `"ar"` | Algerian Darija → Arabic base |
| `beam_size` | `5` | Good accuracy; the 4070 handles it easily |

Example loading code (for the STT FastAPI service):
```python
from faster_whisper import WhisperModel

# RTX 4070 — full GPU, float16
model = WhisperModel(
    "large-v3-turbo",
    device="cuda",
    compute_type="float16",
)

segments, info = model.transcribe(
    "audio.wav",
    language="ar",          # Darija → Arabic
    beam_size=5,
    vad_filter=True,        # skip silent parts
    initial_prompt="...",   # Darija medical bias prompt
)
for seg in segments:
    print(seg.text)
```

> **CPU fallback** (for a machine without CUDA): use
> `device="cpu", compute_type="int8"`. Much slower — only for development.

The first run downloads the `large-v3-turbo` weights (~1.5 GB) and caches
them under `%USERPROFILE%\.cache\huggingface`.

---

## 8️⃣ FFmpeg (Audio Processing)

The STT pipeline converts and resamples incoming audio. While
faster-whisper itself bundles audio decoding via PyAV, having **FFmpeg**
installed system-wide is needed for the broader audio preprocessing
(format conversion, resampling, normalization) and for any tooling that
shells out to `ffmpeg` / `ffprobe`.

### Install on Windows

**Option A — winget (easiest):**
```powershell
winget install Gyan.FFmpeg
```

**Option B — manual:**
1. Download a build from https://www.gyan.dev/ffmpeg/builds/
   (the "release full" build) or https://ffmpeg.org/download.html
2. Extract to a clean folder, e.g. `C:\ffmpeg`
3. Add `C:\ffmpeg\bin` to your **PATH** environment variable.

**Verify:**
```powershell
ffmpeg -version
ffprobe -version
```

### Environment variables

Some audio libraries (e.g. `pydub`, certain preprocessing scripts) look
for FFmpeg via environment variables. Add these to the AI service's
`.env` file:

```dotenv
FFMPEG_BINARY=ffmpeg
FFPROBE_BINARY=ffprobe
```

These tell the code to call `ffmpeg` / `ffprobe` directly. This works as
long as FFmpeg's `bin` folder is on your system **PATH** (so the bare
command names resolve). If you prefer not to touch PATH, you can instead
give the full paths:

```dotenv
FFMPEG_BINARY=C:\ffmpeg\bin\ffmpeg.exe
FFPROBE_BINARY=C:\ffmpeg\bin\ffprobe.exe
```

> Recommended: put FFmpeg on PATH and keep the simple
> `FFMPEG_BINARY=ffmpeg` form — it stays portable across machines.

---

## ✅ Full Verification

Run each of these — all should succeed:

```powershell
# GPU + driver
nvidia-smi

# CUDA toolkit
nvcc --version

# Ollama + model
ollama --version
ollama list

# Python + GPU-enabled PyTorch
python -c "import torch; print('CUDA:', torch.cuda.is_available(), torch.cuda.get_device_name(0))"

# faster-whisper imports
python -c "from faster_whisper import WhisperModel; print('faster-whisper OK')"

# FFmpeg
ffmpeg -version
ffprobe -version
```

---

## 🚀 Running the AI Services

Both AI components run as separate FastAPI processes.

### LLM Triage Service
```powershell
# Ollama must be running (it runs as a service automatically)
cd D:\path\to\ai-service
.venv\Scripts\activate
uvicorn main:app --reload --port 8000
```

### STT Service
```powershell
cd D:\path\to\stt-service
.venv\Scripts\activate
uvicorn main:app --reload --port 8001
```

> Keep the two services on different ports. The backend calls the triage
> service (default `:8000`) and the STT service separately.

---

## ⚙️ Performance Notes — RTX 4070

The 4070's 12 GB VRAM comfortably holds **both** an LLM and Whisper at the
same time, but watch total VRAM if you run everything at once:

| Component | Approx. VRAM | Notes |
|-----------|--------------|-------|
| Qwen 2.5:7b (Ollama) | ~5–6 GB | Fully on GPU |
| Qwen 2.5:14b (Ollama) | ~9–10 GB | Fits, slower than 7b |
| Whisper large-v3-turbo (float16) | ~2–3 GB | Fast on the 4070 |

**Recommendations:**
- For everyday dev: run **Qwen 2.5:7b + Whisper turbo** together — fits
  easily in 12 GB.
- Avoid running **Qwen 14b + Whisper** simultaneously — that's close to
  the 12 GB ceiling and may spill to system RAM (slow).
- `nvidia-smi` (or `nvidia-smi -l 1` for live updates) shows real-time
  VRAM use — keep an eye on it when tuning.
- Keep models on an **SSD** for fast load times.
- The RTX 4070 handles `compute_type="float16"` for Whisper with no
  accuracy loss — no need for `int8` unless you want to save VRAM.

---

## 🚨 Common Issues

| Problem | Fix |
|---------|-----|
| `torch.cuda.is_available()` is `False` | Installed CPU build — reinstall torch with the `cu124` index URL |
| CTranslate2 "not compiled with CUDA" | Install `nvidia-cublas-cu12` + `nvidia-cudnn-cu12` |
| cuDNN version error in faster-whisper | Need CUDA ≥ 12.3 / cuDNN 9 — update CUDA, or pin `ctranslate2==4.4.0` |
| Ollama runs on CPU only | Update the NVIDIA driver; check `ollama ps` shows GPU |
| `ffmpeg not recognized` | FFmpeg `bin` not on PATH — add it, restart terminal |
| Out of VRAM | Don't run 14b + Whisper together; use 7b, or Whisper `int8` |
| Whisper first run is slow | First run downloads model weights (~1.5 GB) — cached afterwards |

---

## 📚 Reference Links

- NVIDIA drivers: https://www.nvidia.com/Download/index.aspx
- CUDA Toolkit: https://developer.nvidia.com/cuda-downloads
- Ollama: https://ollama.com/download
- Qwen 2.5 on Ollama: https://ollama.com/library/qwen2.5
- PyTorch install selector: https://pytorch.org/get-started/locally/
- faster-whisper: https://github.com/SYSTRAN/faster-whisper
- FFmpeg builds: https://www.gyan.dev/ffmpeg/builds/
