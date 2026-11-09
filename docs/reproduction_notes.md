# Reproduction & Execution Notes — A-CUT RGNet Mobile

## Environment Setup
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

## Available Scripts (`scripts/`)
```bash
python scripts/evaluate_rgnet_paper_v1_aadb.py --help
python scripts/train_rgnet_paper_v1_aadb.py --help
```

## Preprocessing & Parity Verification
- **Input Specification**: RGB float32 `[1, 256, 256, 3]`, PIL Bilinear resize, pixel scale `[0, 1]` (`DenseNetV1UnitPreprocess` maps `[0, 1]` to DenseNet ImageNet scale inside the graph)
- **Output Specification**: Scalar aesthetic score in `[0, 1]`
- Ensure EXIF orientation transposition (`ImageOps.exif_transpose`) is applied prior to resizing so portrait smartphone photos are not evaluated rotated.
