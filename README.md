| ADDING THIS                   | PROS |
|-------------------------------|------|
| Adaptive Multi-Model Predictor|Faster|
| Weighted Polynomial Regression|+flick|
| Holt-Winters Triple Smoothing |+math |
# CleannAI Premium – AI-Powered Overlay

High-performance AI aimbot with TensorRT inference, DXGI screen capture, and Dear ImGui overlay.

## Prerequisites

| Dependency | Version | Notes |
|------------|---------|-------|
| **Visual Studio 2022** | 17.x | C++20 with Desktop Development workload |
| **CUDA Toolkit** | 12.x | [Download](https://developer.nvidia.com/cuda-downloads) |
| **TensorRT** | 10.x | [Download](https://developer.nvidia.com/tensorrt) – set `TENSORRT_ROOT` env var |
| **CMake** | 3.24+ | Included with VS2022 |

## Setup

### 1. Clone Dear ImGui (docking branch)

```powershell
mkdir thirdparty
cd thirdparty
git clone --branch docking https://github.com/ocornut/imgui.git
```

### 2. Get nlohmann-json (header-only)

```powershell
cd thirdparty
git clone https://github.com/nlohmann/json.git
```

### 3. Set environment variable

```powershell
$env:TENSORRT_ROOT = "C:\TensorRT"   # adjust to your install path
```

### 4. Build

```powershell
cmake -B build -G "Visual Studio 17 2022"
cmake --build build --config Release
```

The executable will be at `build/Release/Cleann.exe`.

## Usage

1. Place a YOLO `.onnx` model file in the `models/` folder
2. Run `Cleann.exe`
3. In the UI panel, enter the model path and click **Load Model**
4. The ONNX model will auto-convert to a TensorRT engine on first load (takes ~30 seconds)
5. Press **F1** to toggle aiming on/off
6. Hold your chosen activation key to aim

## Controls

| Key | Action |
|-----|--------|
| **F1** | Toggle aim on/off |
| **Activation Key** | Hold to aim (configurable in UI) |

## Configuration

All settings are saved/loaded as JSON via the UI buttons. Settings include:
- FOV radius, AI speed, smooth factor
- Target bone (Head/Neck/Chest)
- Anti-overshoot toggle
- GPU device selection
- Input resolution (320×320 / 640×640)
- Visual settings (glow, rainbow FOV, box colours)

## Architecture

```
main.cpp         → Win32 overlay + D3D11 + ImGui + main loop
capture.cpp      → DXGI Desktop Duplication (async thread)
inference.cpp    → TensorRT engine (async thread)
aimbot.cpp       → Target tracking + aim smoothing
input.cpp        → SendInput mouse movement
render.cpp       → EMA-smoothed bounding boxes + glow
ui.cpp           → Dear ImGui control panel
```

Three async threads: **Capture → Inference → Render** with a lock-free triple buffer between capture and inference.
