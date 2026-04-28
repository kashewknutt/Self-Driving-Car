# Self-Driving Car in 2D environment, using Deep Q Learning

This is a fun project created to learn Reinforcement Learning, inspired by [CodeBullet's awesome video on YouTube](https://www.youtube.com/watch?v=r428O_CMcpI).

![](figure.png)

## Requirements

- **Python 3.11** — required for the pinned TensorFlow stack that matches Apple Silicon Metal wheels (`tensorflow-metal`). Older/newer interpreters often yield `No matching distribution found for tensorflow`.
- **TensorFlow** — PyPI package **`tensorflow`** (not `tensorflow-macos`; that name is only for TensorFlow ≤ 2.12). Keras is **`tensorflow.keras`** or bundled **`keras`** via TensorFlow — do **not** install standalone `keras` from PyPI unless you intend to diverge from TF’s pinned version.
- **Apple Silicon Metal:** **`tensorflow-metal`** must match **`tensorflow`** at the binary level. This repo pins **`tensorflow==2.18.1`** with **`tensorflow-metal==1.2.0`** (Metal **1.1.0** can trigger `libmetal_plugin.dylib` / `Symbol not found … LogMessage` on import). Listed with a PEP 508 marker in [`requirements.txt`](requirements.txt) so Metal installs only on `darwin` + `arm64`.
- **NumPy** — constrained to stay compatible with TensorFlow 2.18 wheels.
- **Pyglet** 1.5.x — this project uses legacy `Batch.add(...)` APIs removed in Pyglet 2.x.

## Setup (macOS, Apple Silicon — MacBook Air M5 and similar)

Use **Python 3.11** from Xcode CLT, python.org, or Homebrew (`brew install python@3.11`).

```bash
cd /path/to/Self-Driving-Car
# Remove an old venv if it used another Python version:
# rm -rf .venv

python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

**Intel Mac or Linux:** `tensorflow-metal` is skipped by the marker. You still get `tensorflow==2.18.1` for CPU training/inference.

### Verify install

```bash
python --version          # expect 3.11.x
python -c "import platform; print(platform.machine())"   # Apple Silicon: arm64

python -c "import tensorflow as tf; print('TensorFlow', tf.__version__); print(tf.config.list_physical_devices())"
```

### Troubleshooting: `No matching distribution found for tensorflow`

1. Confirm **`python --version`** is **3.11** inside the activated venv (`which python`).
2. Confirm **`platform.machine()`** is **`arm64`** on Apple Silicon (not Rosetta `x86_64`).
3. Upgrade pip: `python -m pip install --upgrade pip`, then retry.

### Troubleshooting: `NotFoundError` / `libmetal_plugin.dylib` / `Symbol not found` (e.g. `tsl::internal::LogMessage`)

The Metal plugin wheel does not match the TensorFlow core wheel. Fix by reinstalling the pinned pair:

```bash
pip uninstall -y tensorflow-metal tensorflow
pip install tensorflow==2.18.1
pip install tensorflow-metal==1.2.0
```

Or **CPU-only** (no GPU): `pip uninstall -y tensorflow-metal` and keep `tensorflow==2.18.1`.

## How to use

```bash
# Train — close the window or press ESC to quit; weights are saved to model.h5 after the app exits.
python train_deep_q.py

# Run the car using weights from model.h5
python main.py
```

Place a trained `model.h5` next to the scripts for `main.py`. If the repo does not ship `model.h5` or `assets/sprites/car.png`, train first or add those assets locally.
