# YOLOv8 Traffic Sign Detection

[![Python](https://img.shields.io/badge/python-3.10%2B-blue.svg)](#environment)
[![Framework](https://img.shields.io/badge/framework-Ultralytics%20YOLOv8-green.svg)](#model-family)
[![Task](https://img.shields.io/badge/task-traffic%20sign%20detection-orange.svg)](#project-overview)

A GitHub-ready computer vision project for traffic sign detection built on YOLOv8 and extended with custom architectural modules including CBAM, LCFE, IMCMD, and YOLO-TS style feature fusion.

The project is organized as a reproducible CV repository rather than a loose collection of experiments. It includes model variants, dataset configuration, training and evaluation entrypoints, result visualizations, and documentation for extension and presentation.

## Project Overview

### Objective

The goal is to detect traffic signs in road-scene imagery and evaluate whether custom lightweight feature-fusion designs can outperform a standard YOLOv8 baseline, especially under challenging lighting conditions.

### Main research questions

- Can a lightweight custom architecture improve traffic sign detection accuracy?
- Which modules contribute most to the final performance gain?
- How robust are the models under day and night conditions?
- Can the repository be packaged as a clean, portfolio-ready CV project?

## Model Family

This repository includes several model variants built around YOLOv8:

| Variant | Description |
|---|---|
| `baseline` | Standard YOLOv8 training |
| `cbam` | YOLOv8 with CBAM attention |
| `lcfe` | YOLOv8 with lightweight context enhancement |
| `lcfe_v2` | Stability-improved LCFE design |
| `imcmd` | IMCMD with custom feature fusion |
| `imcmd_ts` | IMCMD combined with YOLO-TS style AGRFM fusion |
| `ts` | YOLO-TS style ablation variant |

## Results

The repository includes experiment summary figures and benchmark tables for project presentation.

### Headline performance

On the LISA traffic sign benchmark used in this project, the strongest variant is `IMCMD`, which reaches **42.74% mAP@0.5**, outperforming the YOLOv8 baseline at **39.14% mAP@0.5**.

### Summary table

| Model | mAP@0.5 | Params | Notes |
|---|---:|---:|---|
| IMCMD | 42.74 | 1.98M | Best overall accuracy |
| IMCMD-TS | 40.57 | 2.21M | Strong multi-scale fusion variant |
| YOLOv8s Baseline | 39.14 | 11.14M | Reference model |
| YOLO-TS | 38.10 | 13.71M | Ablation comparison |
| CCA_Light | 29.91 | - | Lightweight context baseline |
| CBAM | 11.73 | - | Attention-only comparison |

Detailed summaries are available in:

- [docs/results.md](docs/results.md)
- [reports/metrics_summary.csv](reports/metrics_summary.csv)
- [reports/day_night_summary.csv](reports/day_night_summary.csv)

### Figures

#### Overall model comparison

![Performance Comparison](charts/chart1_performance_comparison.png)

#### Day vs night robustness

![Day Night Comparison](charts/chart2_day_night_comparison.png)

#### Ablation study

![Ablation Study](charts/chart3_ablation_study.png)

#### Training convergence

![Training Convergence](charts/chart4_training_convergence.png)

## Repository Structure

```text
yolov8-traffic-sign/
|-- .github/                    # CI workflow
|-- assets/
|   `-- sample_predictions/     # Placeholder for qualitative predictions
|-- charts/                     # Project result figures used in the README
|-- configs/
|   |-- datasets/               # Dataset yaml files
|   `-- experiments/            # Experiment presets
|-- data/
|   |-- external/               # Raw datasets (not tracked)
|   |-- interim/                # Intermediate assets
|   `-- processed/              # Processed dataset assets
|-- docs/
|   |-- dataset_card.md
|   |-- github_release_checklist.md
|   |-- project_overview.md
|   |-- results.md
|   `-- roadmap.md
|-- outputs/                    # Prediction outputs
|-- reports/
|   |-- ablation_template.md
|   |-- day_night_summary.csv
|   |-- metrics_summary.csv
|   `-- metrics_summary_template.csv
|-- scripts/
|   |-- train.py
|   |-- evaluate.py
|   |-- predict.py
|   `-- prepare_folders.py
|-- src/
|   `-- yolo_traffic_sign/
|       |-- models/
|       |-- cli.py
|       |-- inference.py
|       |-- legacy.py
|       `-- paths.py
|-- tests/
|-- pyproject.toml
|-- requirements.txt
`-- README.md
```

## Environment

Recommended setup:

```bash
py -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
pip install -e .
```

If you are using GPU training, install the correct CUDA-enabled PyTorch build first, then install the remaining dependencies.

## Quick Start

### Prepare folders

```bash
py scripts/prepare_folders.py
```

### Train a baseline model

```bash
py scripts/train.py train --variant baseline --data configs/datasets/lisa_day.yaml --epochs 100 --batch 16 --name baseline_yolov8s_100epochs
```

### Train IMCMD

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

Prediction outputs are saved under `outputs/predict/`.

## Dataset

This repository does not ship the full dataset. Update the YAML files in `configs/datasets/` or the root-level `lisa_day.yaml` and `lisa_night.yaml` to match your local dataset path before training or evaluation.

Expected class set:

- go
- goForward
- goLeft
- stop
- stopLeft
- warning
- warningLeft

More details are in [docs/dataset_card.md](docs/dataset_card.md).

## Project Features

- Multiple YOLOv8-based architecture variants
- Day and night robustness comparison
- Ablation-style component analysis
- Unified CLI wrappers for training, evaluation, and prediction
- GitHub Actions CI and basic tests
- Structured docs and result summaries for presentation

## Engineering Notes

- Root-level model scripts are preserved for compatibility with the original experiment code.
- Reusable wrapper logic lives in `src/yolo_traffic_sign/`.
- Result summaries in `reports/` are aligned with the figures used in the README.

## Roadmap

Short-term improvements:

- move legacy modules fully into `src/yolo_traffic_sign/models/`
- add automated metrics export after training
- add sample qualitative prediction gallery
- add a stricter evaluation report pipeline

Longer-term improvements:

- add pretrained release artifacts
- add notebook-free experiment reproduction docs
- add richer inference demos and error analysis

## License

MIT License. See [LICENSE](LICENSE).
