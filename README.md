# Writing in Air ✍️

A deep learning system that recognizes **English (and Korean) characters written in mid-air** using finger gestures captured as video frame sequences. The model uses a 3D ResNet encoder to extract spatiotemporal features from gesture videos and decodes them into text using CTC loss — no pen, no keyboard, just hand movements.

---

## How It Works

Each input is a short video clip of a finger tracing a letter in the air. The pipeline:

1. **Video frames** (PNG sequences) → normalized frame tensors
2. **3D ResNet encoder** (R3D / MC3 / R(2+1)D / R2D) → spatiotemporal embeddings
3. **Optional sequence decoder** (LSTM / GRU / Transformer) → refined sequence
4. **CTC head** → character predictions
5. **Greedy decode** → recognized text, evaluated with CER / WER

---

## Model Variants

| Encoder | Description |
|---|---|
| `r3d` | Full 3D convolutions (default) |
| `mc3` | Mixed 3D/2D — 3D in early layers |
| `rmc3` | Reversed MC3 — 3D in later layers |
| `twoplusone` | R(2+1)D — spatial + temporal convolutions factorized |
| `r2d` | 2D convolutions only (no temporal modelling) |

Sequence decoders: `none` (FC only) · `lstm` · `gru` · `transformers`

---

## Dataset

The project uses a custom air-writing dataset structured as:

```
dataset/
└── english/
    ├── train/
    │   └── <group>/
    │       ├── gt.txt          # ground-truth labels (one per line)
    │       ├── 0/              # video frames for sample 0
    │       │   ├── 0000.png
    │       │   └── ...
    │       └── 1/
    └── val/
    └── test/
```

The code auto-extracts and splits zipped data (80/10/10 train/val/test) from a directory of `.zip` files.

---

## Setup

### Prerequisites

- Python 3.8+
- CUDA-capable GPU (recommended: CUDA 11.8)
- PyTorch + torchvision (must be version-matched — see below)

### Install dependencies

```bash
pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 \
  --index-url https://download.pytorch.org/whl/cu118

pip install editdistance scikit-image matplotlib pillow numpy
```

> **Troubleshooting:** If you see `RuntimeError: operator torchvision::nms does not exist`, your torch/torchvision versions are mismatched. Run the install command above in a clean conda environment and restart the kernel.

### Environment variables

Copy `.env.example` to `.env` and fill in your local paths:

```bash
cp .env.example .env
```

---

## Configuration

All settings live in the `Config` class inside `writing_in_air.py`. The key ones to set before running:

| Parameter | Default | Description |
|---|---|---|
| `zip_dir` | `I:\SAGAR\dataset` | Folder containing your `.zip` dataset files |
| `dataset_dir` | `I:\SAGAR\dataset_extracted` | Destination for extracted data |
| `save_dir` | `I:\SAGAR\checkpoints` | Where model checkpoints are saved |
| `model_type` | `r3d` | Encoder architecture |
| `recurrent_type` | `none` | Sequence decoder (`gru`, `lstm`, `transformers`, `none`) |
| `data_type` | `english` | Language (`english` or `korean`) |
| `batch_size` | `2` | Reduce to `1` if you hit CUDA OOM |
| `num_epochs` | `175` | Training epochs (use `50` for a quick run) |
| `num_workers` | `0` | Keep `0` on Windows to avoid DataLoader issues |
| `learning_rate` | `1e-3` | Initial learning rate |
| `optimizer_type` | `adam` | `adam`, `adamW`, `sgd`, `rmsprop`, `lamb` |
| `scheduler_type` | `warmup` | `warmup`, `steplr`, `none` |
| `data_augment` | `False` | Enable colour jitter + random rotation |
| `pretrained` | `False` | Load ImageNet-pretrained torchvision weights |
| `load_dir` | `''` | Path to checkpoint folder to resume from |

---

## Running

The project is a single self-contained script converted from a Colab notebook. Run it cell by cell in Jupyter/Colab, or execute the full script:

```bash
python writing_in_air.py
```

**Recommended: run in Colab** (the file was originally authored there):

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1yA21YyjPU6g4mEIyYdMew_HyDsUYls1H)

### Execution order

| Cell | What it does |
|---|---|
| 1 | Verify PyTorch + CUDA compatibility |
| 2 | Install missing Python packages |
| 3 | Load `Config` with your paths |
| 4 | Extract & split zipped dataset |
| 5 | Utility functions (CER, WER, label converter) |
| 6 | Scheduler & LAMB optimizer |
| 7 | Video augmentation transforms |
| 8 | 3D ResNet encoder definitions |
| 9 | `GestureTranslator` full model |
| 10 | `AirTypingDataset` |
| 11 | Collate function & `Trainer` class |
| 12 | Configure training run |
| 13 | Start training |

---

## Project Structure

```
writing-in-air/
└── writing_in_air.py   # Full pipeline: data, model, training, evaluation
```

---

## Metrics

- **CER** (Character Error Rate) — edit distance at character level
- **WER** (Word Error Rate) — edit distance at word level

Both strip `<START>` / `<EOS>` tokens before scoring.

---

## Limitations & Future Work

- [ ] Refactor from single-script notebook into a proper module structure (`model/`, `data/`, `train.py`)
- [ ] Add `requirements.txt` / `environment.yml`
- [ ] Support for real-time webcam inference
- [ ] Pre-trained checkpoint releases
- [ ] Evaluation on held-out test set with full CER/WER report

---

## License

[MIT](https://choosealicense.com/licenses/mit/)
