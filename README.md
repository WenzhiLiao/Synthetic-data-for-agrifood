# Data-Efficient Agricultural Computer Vision with Real and Synthetic Data

This repository contains the experimental notebook and supporting configuration for studying **Synthetic Data for Data-Efficient Agricultural Computer Vision:
Three Datasets and a Multi-Task Benchmark Across Real and Simulated Domains**.

The experiments use **YOLO11n-seg** and investigate how model performance changes when only a small number of real images are available and synthetic images are added to the training set.

## Overview

The main experimental settings implemented in `paper5times.ipynb` are:

1. **Real + synthetic training**
   - Randomly select 10 real training images.
   - Combine the selected real images with synthetic training data.
   - Keep validation and test datasets fixed.
   - Train and evaluate YOLO11n-seg.

2. **Five repeated 10-real-image experiments**
   - Five independent experiments are performed.
   - Each experiment uses a different random selection of 10 real images.
   - Results are summarized using **mean ± standard deviation**.

3. **Synthetic-data ablation study**
   - Keep the same 10 real images for each experiment.
   - Vary the number of synthetic training images:
     - 0
     - 250
     - 500
     - 750
   - The notebook currently contains the corresponding experimental configuration; adjust `SYNTHETIC_SIZES` if additional sizes are required.

4. **Evaluation**
   - Bounding-box metrics:
     - mAP@50
     - Recall
   - Segmentation metrics:
     - mask mAP@50
     - mask Recall
   - Individual experiment results and mean ± standard deviation summaries are saved as CSV/TXT files.

## Repository structure

```text
NormAI/
├── demo.ipynb
├── README.md
├── requirements.txt
├── .gitignore
└── data/                         # NOT included in Git
    ├── apples/
    │   ├── Real/
    │   │   ├── images/
    │   │   │   ├── train/
    │   │   │   ├── val/
    │   │   │   └── test/
    │   │   └── labels/
    │   │       ├── train/
    │   │       ├── val/
    │   │       └── test/
    │   └── CAD2Render/
    │       ├── images/
    │       └── labels/
    │
    └── carrots/
        └── crack_all/
            ├── images/
            │   ├── Real/
            │   └── CAD2Render_full/
            └── labels/
                ├── Real/
                └── CAD2Render_full/

# Generated during experiments
random_experiments/
random_experiments_apple/
runs/
```

> **Important:** The real and synthetic data, as well as their annotations, are publicly available: https://doi.org/10.5281/zenodo.20842567

## Dataset format

The code expects a YOLO segmentation dataset.

For every image:

```text
image_001.jpg
image_001.txt
```

The corresponding `.txt` file contains YOLO segmentation annotations.

A typical YOLO segmentation annotation has the form:

```text
<class_id> <x1> <y1> <x2> <y2> ... <xn> <yn>
```

where polygon coordinates are normalized to `[0, 1]`.

### Dataset splits

The experiments use three splits:

- `train`: training data
- `val`: validation data used during model development
- `test`: independent final evaluation data

The validation and test datasets should remain fixed when comparing experimental conditions.

## Requirements

Recommended environment:

- Python 3.11
- PyTorch
- Ultralytics YOLO

Install the Python dependencies with:

```bash
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### PyTorch / CUDA

PyTorch installation depends on the CUDA version and NVIDIA driver available on the machine.

Install the appropriate PyTorch build for your system before running the notebook. For example, verify the installation with:

```bash
python -c "import torch; print(torch.__version__); print(torch.cuda.is_available())"
```

Then verify Ultralytics:

```bash
python -c "from ultralytics import YOLO; print('Ultralytics OK')"
```


## Five repeated experiments

The purpose of repeating the experiment five times is to reduce the influence of a particular random selection of the 10 real images.

The workflow is:

```text
Real training dataset
        │
        ├── Randomly select 10 real images
        │
        ├── Experiment 1 → YOLO11n-seg → evaluation
        ├── Experiment 2 → YOLO11n-seg → evaluation
        ├── Experiment 3 → YOLO11n-seg → evaluation
        ├── Experiment 4 → YOLO11n-seg → evaluation
        └── Experiment 5 → YOLO11n-seg → evaluation
                                      │
                                      ▼
                              Mean ± STD
```

The same validation/test data are used for the repeated experiments.

## Synthetic-data ablation

The ablation study evaluates the effect of increasing synthetic training data while keeping the real-data contribution fixed.

```text
10 real
10 real + 250 synthetic
10 real + 500 synthetic
10 real + 750 synthetic
10 real + 1000 synthetic
```



### Metrics

The notebook extracts:

| Metric | Description |
|---|---|
| `bbox_mAP50` | Bounding-box mAP at IoU 0.50 |
| `mask_mAP50` | Segmentation-mask mAP at IoU 0.50 |
| `bbox_recall` | Mean bounding-box recall |
| `mask_recall` | Mean segmentation-mask recall |

For repeated experiments, the summary is reported as:

```text
mean ± standard deviation
```

The standard deviation is calculated using the sample standard deviation (`ddof=1`).

## Reproducibility

For reproducible experiments:

1. Use the same dataset version.
2. Keep validation and test sets fixed.
3. Keep the same YOLO model version.
4. Keep the same training parameters.
5. Keep the same random seeds.
6. Record the GPU, CUDA, PyTorch, and Ultralytics versions.

A recommended environment record is:

```bash
python --version
python -c "import torch; print('PyTorch:', torch.__version__); print('CUDA:', torch.version.cuda); print('GPU available:', torch.cuda.is_available())"
python -c "import ultralytics; print('Ultralytics:', ultralytics.__version__)"
```


## Citation

If this code is used in an academic publication, please cite the associated paper.

A BibTeX entry can be added here once the paper has a DOI or final publication information.

```bibtex
@article{Data_efficient_agricultural_cv,
  title   = {Synthetic Data for Data-Efficient Agricultural Computer Vision:
Three Datasets and a Multi-Task Benchmark Across Real and Simulated Domains},
  journal = {AgriEngineering},
  author  = {Steven Moonen, Wouter Jansen, Wenzhi Liao, et al.},
  year    = {2026},

}
```

## Contact

For questions regarding the code or experiments:

**Wenzhi Liao**  
Flanders Make  
Email: wenzhi.liao@flandersmake.be
