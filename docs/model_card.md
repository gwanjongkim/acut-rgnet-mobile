# Model Card — A-CUT RGNet Mobile

## Model Details
- **Model Name**: A-CUT RGNet Mobile (`acut-rgnet-mobile`)
- **Developer**: Gwanjung Kim (A-CUT Mobile Photo Evaluation & Curation Capstone)
- **Architecture**: RGNet-paper-v1 (`scripts/rgnet_paper_v1.py`): DenseNet121 + Cascaded DenseASPP / ASPP + Cosine Region Similarity Graph + 3× Residual Graph Convolution + Mean/LSE Region Score Aggregation. Also includes Practical RGNet (`scripts/rgnet.py`) with EfficientNetV2B0.
- **Deployment Role**: Production Aesthetic Ensemble Component (Weight: 0.45 in submitted Flutter app; 0.41 in offline calibrated ensemble)

## Tensor Input / Output Contract
- **Input Contract**: RGB float32 `[1, 256, 256, 3]`, PIL Bilinear resize, pixel scale `[0, 1]` (`DenseNetV1UnitPreprocess` maps `[0, 1]` to DenseNet ImageNet scale inside the graph)
- **Output Contract**: Scalar aesthetic score in `[0, 1]`
- **Included Model Artifacts**:
  - `rgnet_pil_resize_aadb_fp16.tflite (37.13 MB, SHA256: 1d3b19a93758)`

## Intended Use
- Designed for on-device or offline photo quality and aesthetics evaluation within the A-CUT smartphone photo selection pipeline.
- Not intended as an uncalibrated standalone medical, forensic, or safety-critical classifier.

## Limitations & Audit Qualifications
- **PIL Bilinear Preprocessing Parity**: Aligning training/evaluation resize semantics to PIL Bilinear (`256×256`) and embedding `DenseNetV1UnitPreprocess` eliminated framework interpolation drift.
- **Practical RGNet Fix (2026-07-11 Audit)**: Fixed `scripts/rgnet.py` (`EfficientNetV2B0`) to pass `include_preprocessing=False` and apply explicit unit-to-signed rescaling (`[-1, 1]`), preventing double `1/255` scaling.
- **Full 1,000-Row Predictions Included**: `results/test_predictions.csv` and `results/aadb_mobile_like_predictions.csv` contain all 1,000 held-out AADB test predictions.
