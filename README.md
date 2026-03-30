# Wheels Thor sm_110

Pre-compiled Python wheels for **NVIDIA Thor** (Jetson AGX Thor).

| Hardware | Value |
|----------|-------|
| Chip | NVIDIA Thor (sm_110 / sm_110a) |
| JetPack | 7.1 (L4T R38.4.0) |
| CUDA | 13.0 |
| Python | 3.10 |
| OS | Ubuntu 22.04 aarch64 |

## Wheels disponibles

| Package | Version | GPU | Release |
|---------|---------|-----|---------|
| torch | 2.12.0a0+git928cada | ✅ CUDA sm_110 | v1.0.0 |
| torchvision | 0.26.0a0+6285457 | ✅ CUDA sm_110 | v1.0.0 |
| torchaudio | 2.11.0a0+11ed357 | ✅ CUDA sm_110 | v1.0.0 |
| xgboost | 3.3.0-dev | ✅ CUDA sm_110 | v1.0.0 |
| lightgbm | 4.6.0.99 | ✅ CUDA sm_110 | v1.0.0 |
| tensorflow | 2.21.0 | ✅ CUDA sm_110 | v1.1.0 |
| vllm | 0.18.1.dev0 (compilado vs torch 2.12.0) | ✅ CUDA sm_110 | v2.0.0 |

## Instalación rápida

```bash
# PyTorch
pip install torch-2.12.0a0+git928cada-cp310-cp310-linux_aarch64.whl
pip install torchvision-0.26.0a0+6285457-cp310-cp310-linux_aarch64.whl
pip install torchaudio-2.11.0a0+11ed357-cp310-cp310-linux_aarch64.whl

# XGBoost
pip install xgboost-3.3.0.dev0-py3-none-manylinux_2_39_aarch64.whl

# LightGBM
pip install lightgbm-4.6.0.99-py3-none-linux_aarch64.whl

# TensorFlow
pip install tensorflow-2.21.0-cp310-cp310-linux_aarch64.whl

# vLLM (ver sección de notas antes de instalar)
pip install vllm-0.18.1.dev0+gbcf2be961.d20260330.cu130-cp310-cp310-linux_aarch64.whl
```

### LightGBM — fix obligatorio tras instalar

```bash
LGBM_DIR=$(python -c "import lightgbm,os; print(os.path.dirname(lightgbm.__file__))")
cp $LGBM_DIR/lib_lightgbm.so $LGBM_DIR/lib/lib_lightgbm.so
```

### TensorFlow — symlink obligatorio para cusolver

```bash
ln -sf /usr/local/cuda-13.0/targets/sbsa-linux/lib/libcusolver.so.12 \
   $VIRTUAL_ENV/lib/libcusolver.so.11
```

### LD_LIBRARY_PATH recomendado

```bash
export LD_LIBRARY_PATH=$VIRTUAL_ENV/lib:/usr/lib/aarch64-linux-gnu:/usr/local/cuda/lib64:/usr/local/cuda-13.0/lib64:/usr/local/cuda-13.0/targets/sbsa-linux/lib
```

---

## vLLM — Notas importantes para sm_110a (Thor)

### Por qué NO usar el wheel del índice JetPack
El wheel oficial `vllm-0.18.0` del índice `developer.download.nvidia.com/compute/redist/jp/v71` fue compilado contra `torch 2.10.0` genérico. Es **incompatible** con el torch custom 2.12.0 compilado para sm_110 → `ImportError: undefined symbol: _ZN3c1013MessageLoggerC1EPKciib`.

**Siempre usar el wheel de este repositorio (v2.0.0)**, compilado desde fuente contra torch 2.12.0.

### Fix obligatorio: ptxas-blackwell de Triton no soporta sm_110a
El Thor reporta su arquitectura como `sm_110a` (variante). El binario `ptxas-blackwell` bundled con Triton no reconoce esta variante. El ptxas del sistema CUDA 13.0 sí la soporta.

**Ejecutar tras instalar vLLM (y repetir si se actualiza Triton):**
```bash
TRITON_BIN=$(python -c "import triton, os; print(os.path.dirname(triton.__file__))")/backends/nvidia/bin
cp $TRITON_BIN/ptxas-blackwell $TRITON_BIN/ptxas-blackwell.bak
cp /usr/local/cuda-13.0/bin/ptxas $TRITON_BIN/ptxas-blackwell
```

### Flag obligatorio al lanzar el servidor
```bash
--enforce-eager
```
El compilador JIT de vLLM intenta acceder a `standalone_compile.FakeTensorMode` que en torch 2.12.0 tiene una ruta de API distinta. `--enforce-eager` desactiva la compilación JIT y evita el error. El rendimiento es ligeramente inferior pero el servidor funciona correctamente.

### Comando completo para lanzar vLLM con Qwen2.5-Coder-72B-AWQ
```bash
source ~/proyectos_ia/entorno/proyecto310/bin/activate
python -m vllm.entrypoints.openai.api_server \
  --model ~/modelos/qwen25-coder-72b-awq \
  --dtype auto \
  --quantization awq \
  --kv-cache-dtype fp8 \
  --max-model-len 131072 \
  --gpu-memory-utilization 0.55 \
  --enable-chunked-prefill \
  --max-num-batched-tokens 131072 \
  --enforce-eager \
  --host 0.0.0.0 --port 8000 \
  --served-model-name qwen-coder
```

### Reinstalar torch custom si pip lo sobreescribe
Instalar vLLM (o cualquier paquete que dependa de torch) puede reemplazar el torch custom por uno genérico de PyPI. Verificar siempre tras instalar:
```bash
python -c "import torch; print(torch.__version__, torch.cuda.get_device_capability())"
# Debe mostrar: 2.12.0a0+git928cada (11, 0)
```
Si no, reinstalar:
```bash
pip install \
  "https://github.com/ajarenillasp/wheels-thor-sm110/releases/download/v1.0.0/torch-2.12.0a0%2Bgit928cada-cp310-cp310-linux_aarch64.whl" \
  "https://github.com/ajarenillasp/wheels-thor-sm110/releases/download/v1.0.0/torchaudio-2.11.0a0%2B11ed357-cp310-cp310-linux_aarch64.whl" \
  "https://github.com/ajarenillasp/wheels-thor-sm110/releases/download/v1.0.0/torchvision-0.26.0a0%2B6285457-cp310-cp310-linux_aarch64.whl" \
  --force-reinstall --no-deps
```
