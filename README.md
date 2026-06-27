# Surface Crack Detection Using CNNs

> **Pattern Recognition Course — Phase 3: Final Report and Web Application**
> University of Europe for Applied Sciences · M.Sc. Software Engineering · 2026

A deep learning project for automated binary classification of concrete surface images into **crack** and **no-crack** categories using Convolutional Neural Networks, Transfer Learning, and Explainable AI.

---

## Links

| Resource | Link |
|---|---|
| Live Web App | [huggingface.co/spaces/Avishek71/cracksense](https://huggingface.co/spaces/Avishek71/cracksense) |
| Kaggle Notebook | [kaggle.com/code/avishekbhowmik/surface-crack-detection-ss26-pr](https://www.kaggle.com/code/avishekbhowmik/surface-crack-detection-ss26-pr) |
| Dataset | [Kaggle - Surface Crack Detection (arunrk7)](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection) |

---

## Project Overview

This project applies CNN-based image classification to detect surface cracks in concrete structures automatically.
Two models are implemented and compared:

- **Custom CNN** - designed from scratch as a regularised baseline
- **MobileNetV2** - transfer learning from ImageNet weights in feature extraction mode

Explainability is incorporated through **Grad-CAM** and **SHAP** to verify that predictions are based on actual crack regions rather than irrelevant background areas.

| Feature | Details |
|---|---|
| Task | Binary Image Classification |
| Classes | Positive (crack) / Negative (no crack) |
| Input Size | 96 × 96 × 3 (RGB) |
| Framework | TensorFlow 2.19 / Keras |
| Environment | Kaggle Notebook (GPU: Tesla P100) |
| Accuracy Target | Below 90% - regularised to avoid overfitting |

---

## Dataset

**Surface Crack Detection** - published by arunrk7 on Kaggle

| Attribute | Details |
|---|---|
| Total Images | 40,000 |
| Classes | 2 (Positive, Negative) |
| Images per Class | 20,000 each - perfectly balanced |
| Native Resolution | 227 × 227 pixels |
| Resized To | 96 × 96 pixels |
| Image Type | RGB JPEG |
| Train / Val Split | 80% / 20% (seed = 42) |

> The dataset is not included in this repository. See `Datasets/dataset_link.txt` for the download link.

---

## Models

### Model 1 - Custom CNN (Baseline)

```
Input (96×96×3)
    → Conv2D(32, 3×3, ReLU, L2=0.01) → MaxPool(2×2)
    → Conv2D(64, 3×3, ReLU, L2=0.01) → MaxPool(2×2)
    → Conv2D(128, 3×3, ReLU, L2=0.01) → MaxPool(2×2)
    → GlobalAveragePooling2D
    → Dropout(0.5)
    → Dense(1, Sigmoid)
```

- Trainable parameters: ~93,377
- Regularisation: L2 λ = 0.01 + Dropout 0.5

### Model 2 - MobileNetV2 (Transfer Learning)

```
MobileNetV2 base (frozen, ImageNet weights)
    → GlobalAveragePooling2D
    → Dense(128, ReLU, L2=0.01)
    → Dropout(0.7)
    → Dense(1, Sigmoid)
```

- Trainable parameters: ~155,649 (head only)
- Frozen parameters: ~2,257,984 (base)
- Mode: Feature extraction - base layers frozen

---

## Results

| Model | Val Accuracy | AUC-ROC | F1-Score | Params |
|---|---|---|---|---|
| Custom CNN | 84.6% | 0.921 | 0.84 | ~93K |
| MobileNetV2 | 88.9% | 0.954 | 0.89 | ~155K trainable |

> Accuracy is intentionally kept below 90% through L2 regularisation and Dropout.

---

## Repository Structure

```
surface-crack-detection/
│
├── Codes/
│   └── github_link.txt               # GitHub and Kaggle links
│
├── Datasets/
│   └── dataset_link.txt              # Kaggle dataset download link
│
├── pattern-recognition.ipynb         # Main Kaggle notebook
│
├── Custom_CNN_cm.pdf                 # Confusion matrix — Custom CNN
├── Custom_CNN_curves.pdf             # Training curves — Custom CNN
├── MobileNetV2_cm.pdf                # Confusion matrix — MobileNetV2
├── MobileNetV2_curves.pdf            # Training curves — MobileNetV2
├── ROC_curves.pdf                    # ROC curves — both models
├── gradcam_positive.pdf              # Grad-CAM on cracked surface
├── gradcam_negative.pdf              # Grad-CAM on intact surface
├── shap_sample_0.pdf                 # SHAP overlay — sample 0
├── shap_sample_1.pdf                 # SHAP overlay — sample 1
├── shap_sample_2.pdf                 # SHAP overlay — sample 2
├── sample_images.pdf                 # Sample dataset images
├── model_comparison.csv              # Model comparison table
│
└── README.md
```

---

## How to Run

### Option 1 - Kaggle (Recommended)

1. Go to [Kaggle](https://www.kaggle.com) and open the notebook linked above
2. The Surface Crack Detection dataset is already attached
3. Enable GPU: **Settings → Accelerator → GPU P100**
4. Click **Run All**

All outputs (PDFs, CSV, `.keras` model files) are saved to `/kaggle/working/`.

### Option 2 - Local Machine

```bash
git clone https://github.com/avishekbhowmik/surface-crack-detection.git
cd surface-crack-detection
pip install tensorflow numpy pandas matplotlib seaborn scikit-learn shap
jupyter notebook pattern-recognition.ipynb
```

Download the dataset from [Kaggle](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection) and update `BASE_PATH` in the notebook.

---

## Technologies Used

| Library | Version | Purpose |
|---|---|---|
| TensorFlow / Keras | 2.19.0 | Model building and training |
| NumPy | ≥ 1.24 | Numerical operations |
| Pandas | ≥ 2.0 | Results tables and CSV export |
| Matplotlib | ≥ 3.7 | Plotting and figure export |
| Seaborn | ≥ 0.12 | Confusion matrix heatmaps |
| scikit-learn | ≥ 1.3 | Metrics: F1, ROC-AUC, confusion matrix |
| SHAP | ≥ 0.43 | Explainability - GradientExplainer |
| Gradio | 5.33.0 | Web application interface |

---

## Web Application

Deployed on **Hugging Face Spaces** using Gradio.

**Live demo:** [huggingface.co/spaces/Avishek71/cracksense](https://huggingface.co/spaces/Avishek71/cracksense)

Features:
- Upload any concrete surface image
- Both CNN models run simultaneously
- Crack probability score per model
- Grad-CAM heatmap per model
- Reference metrics comparison table

---

## License

This project is submitted as part of a university course assignment.
Dataset credit: [arunrk7 on Kaggle](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection)
