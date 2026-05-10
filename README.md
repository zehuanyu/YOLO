# YOLOv8 Traffic Sign Detection

[![Python](https://img.shields.io/badge/python-3.10%2B-blue.svg)](#environment)
[![Framework](https://img.shields.io/badge/framework-Ultralytics%20YOLOv8-green.svg)](#model-family)
[![Task](https://img.shields.io/badge/task-traffic%20sign%20detection-orange.svg)](#project-overview)

A computer vision project for traffic sign detection using YOLOv8 and custom lightweight architecture variants.

This project compares a standard YOLOv8 baseline with several attention and feature-fusion extensions, including CBAM, LCFE, IMCMD, and YOLO-TS-style fusion. The goal is to improve small-object traffic sign detection while evaluating robustness across day and night driving conditions.

---

## Overview

Traffic sign detection is an important perception task for autonomous driving and driver-assistance systems. Road signs are often small, visually similar, partially occluded, or affected by lighting changes, which makes detection challenging.

This project investigates:

- Whether lightweight feature-fusion modules can improve YOLOv8 traffic sign detection
- Which custom modules contribute most to detection performance
- How model performance changes between day and night conditions
- Whether higher accuracy can be achieved with fewer parameters

---

## Project Workflow

```text
Traffic sign dataset
        ↓
Dataset configuration
        ↓
YOLOv8 baseline training
        ↓
Custom model variant training
        ↓
Evaluation and comparison
        ↓
Day/night robustness analysis
        ↓
Result visualization
```

---

## Model Variants

| Variant | Description |
|---|---|
| `baseline` | Standard YOLOv8 training baseline |
| `cbam` | YOLOv8 with CBAM attention |
| `lcfe` | YOLOv8 with lightweight context feature enhancement |
| `lcfe_v2` | Stability-improved LCFE variant |
| `imcmd` | Custom IMCMD feature-fusion design |
| `imcmd_ts` | IMCMD with YOLO-TS-style AGRFM fusion |
| `ts` | YOLO-TS-style ablation variant |

---

## Key Features

- YOLOv8-based traffic sign detection pipeline
- Multiple custom architecture variants for comparison
- Lightweight attention and feature-fusion experiments
- Day vs. night robustness analysis
- Ablation-style model comparison
- CLI wrappers for training, evaluation, and prediction
- Benchmark summaries and visualization charts

---

## Dataset

The project is designed around the LISA traffic sign dataset setup used in the experiments.

Expected class set:

- `go`
- `goForward`
- `goLeft`
- `stop`
- `stopLeft`
- `warning`
- `warningLeft`

The full dataset is not included in this repository. Dataset paths should be updated in the YAML files before training or evaluation.

Dataset configuration files:

```text
configs/datasets/
lisa_day.yaml
lisa_night.yaml
```

More details are available in:

```text
docs/dataset_card.md
```

---

## Methodology

### 1. Baseline Training

The project first trains a standard YOLOv8 baseline to establish a reference point for traffic sign detection performance.

The baseline is used to evaluate whether custom modules improve accuracy, robustness, or parameter efficiency.

---

### 2. Custom Architecture Variants

Several model variants are tested around the YOLOv8 framework.

The custom modules focus on:

- Strengthening feature fusion
- Improving small-object representation
- Adding lightweight attention
- Comparing accuracy vs. parameter efficiency
- Testing robustness under lighting changes

---

### 3. Evaluation

Models are evaluated using object detection metrics, with emphasis on:

- mAP@0.5
- Parameter count
- Overall model comparison
- Day vs. night performance
- Ablation-style component contribution

Results are summarized in:

```text
docs/results.md
reports/metrics_summary.csv
reports/day_night_summary.csv
```

---

## Results

The strongest reported variant in this project is `IMCMD`.

| Model | mAP@0.5 | Params | Notes |
|---|---:|---:|---|
| IMCMD | 42.74 | 1.98M | Best overall accuracy |
| IMCMD-TS | 40.57 | 2.21M | Strong multi-scale fusion variant |
| YOLOv8s Baseline | 39.14 | 11.14M | Reference model |
| YOLO-TS | 38.10 | 13.71M | Ablation comparison |
| CCA_Light | 29.91 | - | Lightweight context baseline |
| CBAM | 11.73 | - | Attention-only comparison |

Key observations:

- `IMCMD` achieved the best reported mAP@0.5.
- `IMCMD-TS` remained competitive and showed strong robustness behavior.
- The custom variants outperformed the standard YOLOv8 baseline in this setup.
- IMCMD achieved better accuracy with substantially fewer parameters than the baseline.

---

## Visual Results

The repository includes generated charts for:

- Overall model comparison
- Day vs. night robustness
- Ablation study
- Training convergence

Chart files are stored in:

```text
charts/
```

Detailed result notes are available in:

```text
docs/results.md
```

---

## Repository Structure

```text
YOLOv8-Traffic-Sign-Detection/
├── .github/                    # CI workflow
├── assets/
│   └── sample_predictions/     # Sample prediction assets
├── charts/                     # Result figures used in README
├── configs/
│   ├── datasets/               # Dataset YAML files
│   └── experiments/            # Experiment presets
├── data/
│   ├── external/               # Raw datasets, not tracked
│   ├── interim/                # Intermediate assets
│   └── processed/              # Processed dataset assets
├── docs/
│   ├── dataset_card.md
│   ├── project_overview.md
│   ├── results.md
│   └── roadmap.md
├── outputs/                    # Prediction outputs
├── reports/
│   ├── day_night_summary.csv
│   ├── metrics_summary.csv
│   └── metrics_summary_template.csv
├── scripts/
│   ├── train.py
│   ├── evaluate.py
│   ├── predict.py
│   └── prepare_folders.py
├── src/
│   └── yolo_traffic_sign/
│       ├── models/
│       ├── cli.py
│       ├── inference.py
│       ├── legacy.py
│       └── paths.py
├── tests/
├── pyproject.toml
├── requirements.txt
└── README.md
```

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/zehuanyu/YOLOv8-Traffic-Sign-Detection.git
cd YOLOv8-Traffic-Sign-Detection
```

### 2. Create environment

```bash
py -m venv .venv
.venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
pip install -e .
```

If using GPU training, install the correct CUDA-enabled PyTorch build before installing the remaining dependencies.

---

## Quick Start

### Prepare project folders

```bash
py scripts/prepare_folders.py
```

### Train YOLOv8 baseline

```bash
py scripts/train.py train --variant baseline --data configs/datasets/lisa_day.yaml --epochs 100 --batch 16 --name baseline_yolov8s_100epochs
```

### Train IMCMD variant

```bash
py scripts/train.py train --variant imcmd --model-type small --data configs/datasets/lisa_night.yaml --epochs 100 --batch 16 --name imcmd_small_100epochs
```

### Evaluate a trained model

```bash
py scripts/evaluate.py --weights runs/traffic_sign/imcmd_small_100epochs/weights/best.pt --data configs/datasets/lisa_day.yaml
```

### Export prediction images

```bash
py scripts/predict.py predict --weights runs/traffic_sign/imcmd_small_100epochs/weights/best.pt --source assets/sample_predictions --variant imcmd --name imcmd_demo
```

Prediction outputs are saved under:

```text
outputs/predict/
```

---

## Engineering Notes

- Root-level model scripts are preserved for compatibility with the original experiment code.
- Reusable wrapper logic is organized under `src/yolo_traffic_sign/`.
- Result summaries in `reports/` are aligned with the charts used in the README.
- Dataset YAML paths must be updated locally before training.

---

## Future Improvements

- Move remaining legacy modules into `src/yolo_traffic_sign/models/`
- Add automated metrics export after training
- Add qualitative prediction examples to the README
- Add stricter evaluation report generation
- Release pretrained model artifacts
- Add more detailed error analysis for failure cases

---

## Tech Stack

Python, PyTorch, Ultralytics YOLOv8, OpenCV, Object Detection, Computer Vision, Traffic Sign Detection, Model Evaluation, Data Visualization

---

## Author

Zehuan Yu
