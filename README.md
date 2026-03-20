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

| Package | Version | GPU |
|---------|---------|-----|
| torch | 2.12.0a0+git928cada | ✅ CUDA sm_110 |
| torchvision | 0.26.0a0+6285457 | ✅ CUDA sm_110 |
| torchaudio | 2.11.0a0+11ed357 | ✅ CUDA sm_110 |
| xgboost | 3.3.0-dev | ✅ CUDA sm_110 |
| lightgbm | 4.6.0.99 | ✅ CUDA sm_110 |

## Instalación rápida

```bash
pip install torch-2.12.0a0+git928cada-cp310-cp310-linux_aarch64.whl
pip install xgboost-3.3.0.dev0-py3-none-manylinux_2_39_aarch64.whl
pip install lightgbm-4.6.0.99-py3-none-linux_aarch64.whl
```

### LightGBM — fix obligatorio tras instalar
```bash
cp $(python -c "import lightgbm,os; print(os.path.dirname(lightgbm.__file__))")/lib_lightgbm.so    $(python -c "import lightgbm,os; print(os.path.dirname(lightgbm.__file__))/lib/lib_lightgbm.so")
```

