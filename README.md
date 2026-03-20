# Wheels Thor sm_110

Pre-compiled Python wheels for **NVIDIA Thor** (Jetson AGX Thor).

| Hardware | Value |
|----------|-------|
| Chip | NVIDIA Thor (sm_110) |
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
