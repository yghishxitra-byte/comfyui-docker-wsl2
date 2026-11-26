# ComfyUI Local GPU Setup (Docker + WSL2)

Production-ready Docker Compose configuration for deploying **ComfyUI** locally with NVIDIA GPU acceleration on Windows (via WSL2) or Linux. Designed for reproducibility, clean volume isolation, and optimized VRAM management.

## 🎯 Project Objectives & Key Features

* **Reproducible Environment:** Containerized deployment using pinned `ai-dock/comfyui` CUDA images, eliminating dependency conflicts ("works on my machine" issues).
* **Hardware-Accelerated Inference:** Full NVIDIA GPU passthrough configured via Docker `deploy.resources` reservations.
* **VRAM Optimization:** Pre-configured runtime flags (`--highvram`, `--reserve-vram`, `--fp16-vae`) tailored for consumer GPUs (e.g., RTX 3060 12GB) to prevent out-of-memory errors and system freezes.
* **Data Isolation & Security:** Strict separation of read-only (`:ro`) model weights from read-write (`:rw`) outputs, custom nodes, and user workflows, plus built-in authentication toggles.
* **12-Factor App Configuration:** Environment variables decoupled into `.env` with a documented `.env.example` template.

---

## 📁 Project Structure

```text
comfyui-docker-wsl2/
├── custom_nodes/          # Custom ComfyUI extensions and nodes (:rw)
├── outputs/               # Generated images and workflow artifacts (:rw)
├── user/                  # Saved user workflows and UI settings (:rw)
├── volumes/               # Isolated storage for model weights (:ro)
│   ├── checkpoints/       # Base diffusion models (SDXL, Flux, SD 1.5)
│   ├── controlnet/        # ControlNet preprocessors and models
│   ├── embeddings/        # Textual inversion embeddings
│   ├── storage/           # Auxiliary model storage
│   ├── upscale_models/    # ESRGAN / upscaling weights
│   └── vae/               # Variational Autoencoders
├── .env.example           # Template for environment variables
├── .gitignore             # Excludes heavy weights and local secrets from Git
├── docker-compose.yml     # Main service orchestration config
└── README.md              # Project documentation
```

---

## 🛠 Prerequisites

1. **OS:** Windows 10/11 with **WSL2** (Ubuntu recommended) or native Linux.
2. **GPU & Drivers:** NVIDIA GPU with up-to-date host drivers.
3. **Container Runtime:**
   * Docker Desktop (with WSL2 backend enabled) or Docker Engine.
   * [NVIDIA Container Toolkit](https://nvidia.com) installed and configured.

---

## 🚀 Quick Start

### 1. Clone the repository
```bash
git clone https://github.com/yghishxitra-byte/comfyui-docker-wsl2.git
cd comfyui-docker-wsl2
```

### 2. Configure environment variables
Copy the example template to create your local `.env` file and adjust parameters if needed:
```bash
cp .env.example .env
```

### 3. Add your models
Place your `.safetensors` or `.ckpt` checkpoint files into `./volumes/checkpoints/` (and LoRAs/VAEs into their respective subdirectories).

### 4. Launch the container
```bash
docker compose up -d
```

Once started, access the ComfyUI web interface at: **`http://localhost:3000`** (or the port specified in your `.env`).

---

## ⚙️ Configuration Reference (`.env`)

| Variable | Default | Description |
| :--- | :--- | :--- |
| `COMFYUI_PORT` | `3000` | Host port mapped to internal container port `8188`. |
| `COMFYUI_ARGS` | `--highvram --reserve-vram 0.5 --fp16-vae` | CLI flags passed to ComfyUI for memory and precision tuning. |
| `WEB_ENABLE_AUTH` | `false` | Set to `true` to enable basic authentication when exposing externally. |

---

## 🧹 Useful Commands

* **View live container logs:**
  ```bash
  docker compose logs -f comfyui
  ```
* **Stop the service:**
  ```bash
  docker compose down
  ```
