# A-CUT RGNet Mobile

> **A-CUT Composition-Aware Aesthetic Assessment — Region Graph Network with PIL-Bilinear Resize (TFLite FP16)**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)
[![Project: A-CUT](https://img.shields.io/badge/Project-A--CUT%20Capstone-success.svg)](https://github.com/gwanjongkim/photo_score_project)

## 1. Overview

- **Role in A-CUT**: Production Aesthetic Ensemble Component (Weight: 0.45 in submitted Flutter app; 0.41 in offline calibrated ensemble)
- **Task**: Composition-Aware Aesthetic Quality Assessment (IAA)
- **Architecture**: RGNet-paper-v1 (`scripts/rgnet_paper_v1.py`): DenseNet121 + Cascaded DenseASPP / ASPP + Cosine Region Similarity Graph + 3× Residual Graph Convolution + Mean/LSE Region Score Aggregation. Also includes Practical RGNet (`scripts/rgnet.py`) with EfficientNetV2B0.

## 2. Input / Output Contract & Model Artifacts

- **Input Contract**: RGB float32 `[1, 256, 256, 3]`, PIL Bilinear resize, pixel scale `[0, 1]` (`DenseNetV1UnitPreprocess` maps `[0, 1]` to DenseNet ImageNet scale inside the graph)
- **Output Contract**: Scalar aesthetic score in `[0, 1]`
- **Model Files (`models/model_files/` & Releases)**:
  - `rgnet_pil_resize_aadb_fp16.tflite (37.13 MB, SHA256: 1d3b19a93758)`

## 3. Benchmark Results

| Dataset | Split / Variant | SRCC | PLCC | MAE | RMSE | Status |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| AADB | Test (Keras Best, PIL Bilinear) | **0.6819** | **0.6871** | 0.1194 | 0.1498 | **Production Base** |
| AADB | Test (1,000 rows, TFLite FP16) | **0.6648** | **0.6708** | 0.1219 | 0.1522 | **Production Mobile** |
| AVA | Binary Benchmark | - | - | - | Acc: **0.7697** | Evaluated |

## 4. Key Engineering Notes & 2026 Audit Findings

- **PIL Bilinear Preprocessing Parity**: Aligning training/evaluation resize semantics to PIL Bilinear (`256×256`) and embedding `DenseNetV1UnitPreprocess` eliminated framework interpolation drift.
- **Practical RGNet Fix (2026-07-11 Audit)**: Fixed `scripts/rgnet.py` (`EfficientNetV2B0`) to pass `include_preprocessing=False` and apply explicit unit-to-signed rescaling (`[-1, 1]`), preventing double `1/255` scaling.
- **Full 1,000-Row Predictions Included**: `results/test_predictions.csv` and `results/aadb_mobile_like_predictions.csv` contain all 1,000 held-out AADB test predictions.

## 5. Quick Start

```bash
pip install -r requirements.txt
python scripts/evaluate_rgnet_paper_v1_aadb.py --help
python scripts/train_rgnet_paper_v1_aadb.py --help
```

For full documentation, see:
- [`docs/model_card.md`](./docs/model_card.md) — Architecture, tensor contracts, and limitations
- [`docs/benchmark_summary.md`](./docs/benchmark_summary.md) — Detailed metrics and dataset evaluations
- [`docs/reproduction_notes.md`](./docs/reproduction_notes.md) — Environment and reproduction guide
- [`models/download.md`](./models/download.md) — Model binary hashes and download notes

## 6. A-CUT Ecosystem Repositories

| Repository | Dimension | Role / Status | Key Metric |
| :--- | :---: | :--- | :--- |
| [acut-nima-mobile](https://github.com/gwanjongkim/acut-nima-mobile) | Aesthetic (IAA) | **Production Ensemble (0.40)** | AVA SRCC `0.5894`, AUC `0.8018` |
| [acut-rgnet-mobile](https://github.com/gwanjongkim/acut-rgnet-mobile) | Aesthetic (IAA) | **Production Ensemble (0.45)** | AADB SRCC `0.6819` (FP16 `0.6648`) |
| [acut-mobile-alamp](https://github.com/gwanjongkim/acut-mobile-alamp) | Aesthetic (IAA) | **Production Ensemble (0.15)** | AVA Acc `0.7633`, AUC `0.7877` |
| [acut-icaa-color-aesthetics](https://github.com/gwanjongkim/acut-icaa-color-aesthetics) | Color Aesthetics | **Verified TF-Native Port** | ICAA17K Color SRCC `0.8791`, MOS `0.9058` |
| [acut-koniq-flive-technical-iqa](https://github.com/gwanjongkim/acut-koniq-flive-technical-iqa) | Technical (IQA) | **Production Baseline (4/7 + 3/7)** | Official SRCC `0.7168` (`0.7958` recon), `0` Severe FP |
| [acut-technical-iqa](https://github.com/gwanjongkim/acut-technical-iqa) | Technical (IQA) | **Umbrella Benchmark** | 4,967-Image Official Protocol Suite |
| [acut-topiq-mixed112](https://github.com/gwanjongkim/acut-topiq-mixed112) | Technical (IQA) | **Evaluated Candidate** | SPAQ SRCC `0.8990`, KonIQ `0.8630` |
| [acut-topiq-flive-hybrid](https://github.com/gwanjongkim/acut-topiq-flive-hybrid) | Technical (IQA) | **Best Log-Only Shadow (60/40)** | Official SRCC `0.8069`, MAE `5.04` |
| [acut-c6-topiq-koniq](https://github.com/gwanjongkim/acut-c6-topiq-koniq) | Technical (IQA) | **Negative Result (Rejected)** | Official SRCC `0.7314`, MAE `15.39` |
| [acut-mdiqa-benchmark](https://github.com/gwanjongkim/acut-mdiqa-benchmark) | Technical (IQA) | **Teacher Ceiling Reference** | KonIQ SRCC `0.9464`, SPAQ `0.9136` |
| [acut-musiq-probe](https://github.com/gwanjongkim/acut-musiq-probe) | Technical (IQA) | **Reference & Probe Study** | Official SPAQ `0.9460`, KonIQ `0.9275` |
| [acut-student-iqa](https://github.com/gwanjongkim/acut-student-iqa) | Technical (IQA) | **Distillation Study (No-Go)** | 14k Student SRCC `0.7436`, SPAQ `0.8984` |
| [photo_score_project](https://github.com/gwanjongkim/photo_score_project) | Full Monorepo | **Core Research Workspace** | Complete Training, Audit & Pipeline Code |

## 7. References & Citation

- Liu, D., Puri, R., Kamath, N., & Bhattacharya, S. (2020). *Composition-Aware Image Aesthetics Assessment*. IEEE/CVF Winter Conference on Applications of Computer Vision (WACV), 3569–3578.

```bibtex
@misc{acut_rgnet_mobile_2026,
  title        = {A-CUT RGNet Mobile: A-CUT Composition-Aware Aesthetic Assessment — Region Graph Network with PIL-Bilinear Resize (TFLite FP16)},
  author       = {Kim, Gwanjung},
  year         = {2026},
  howpublished = {\url{https://github.com/gwanjongkim/acut-rgnet-mobile}}
}
```
