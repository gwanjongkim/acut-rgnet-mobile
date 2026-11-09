# Benchmark Summary — A-CUT RGNet Mobile

## Overview
- **Repository**: `acut-rgnet-mobile`
- **Role in A-CUT**: Production Aesthetic Ensemble Component (Weight: 0.45 in submitted Flutter app; 0.41 in offline calibrated ensemble)
- **Task**: Composition-Aware Aesthetic Quality Assessment (IAA)

## Evaluated Metrics

| Dataset | Split / Variant | SRCC | PLCC | MAE | RMSE | Status |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| AADB | Test (Keras Best, PIL Bilinear) | **0.6819** | **0.6871** | 0.1194 | 0.1498 | **Production Base** |
| AADB | Test (1,000 rows, TFLite FP16) | **0.6648** | **0.6708** | 0.1219 | 0.1522 | **Production Mobile** |
| AVA | Binary Benchmark | - | - | - | Acc: **0.7697** | Evaluated |

## Key Engineering & Audit Findings

- **PIL Bilinear Preprocessing Parity**: Aligning training/evaluation resize semantics to PIL Bilinear (`256×256`) and embedding `DenseNetV1UnitPreprocess` eliminated framework interpolation drift.
- **Practical RGNet Fix (2026-07-11 Audit)**: Fixed `scripts/rgnet.py` (`EfficientNetV2B0`) to pass `include_preprocessing=False` and apply explicit unit-to-signed rescaling (`[-1, 1]`), preventing double `1/255` scaling.
- **Full 1,000-Row Predictions Included**: `results/test_predictions.csv` and `results/aadb_mobile_like_predictions.csv` contain all 1,000 held-out AADB test predictions.

## Evidence Files in `results/`
All metric tables, per-sample predictions, parity checks, and audit reports backing the numbers above are stored directly in the [`results/`](../results/) directory of this repository.
