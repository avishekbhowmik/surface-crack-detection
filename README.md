# 🔍 Surface Crack Detection Using CNNs

> **Pattern Recognition Course — Phase 2: Project Proposal & Technical Implementation**

A deep learning project for automated binary classification of concrete surface images into **crack** and **no-crack** categories using Convolutional Neural Networks, Transfer Learning, and Explainable AI.

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Dataset](#-dataset)
- [Models](#-models)
- [Results](#-results)
- [Repository Structure](#-repository-structure)
- [How to Run](#-how-to-run)
- [Output Figures](#-output-figures)
- [Technologies Used](#-technologies-used)
- [Phase 3 — Web Application](#-phase-3--web-application)

---

## 📌 Project Overview

This project applies CNN-based image classification to detect surface cracks in concrete structures automatically. Two models are implemented and compared:

- **Custom CNN** — designed from scratch as a baseline
- **MobileNetV2** — transfer learning from ImageNet weights (feature extraction mode)

Explainability is incorporated through **Grad-CAM** and **SHAP** to verify that predictions are based on actual crack regions rather than irrelevant background areas.

| Feature | Details |
|---|---|
| Task | Binary Image Classification |
| Classes | Positive (crack) / Negative (no crack) |
| Input Size | 96 × 96 × 3 (RGB) |
| Framework | TensorFlow / Keras |
| Environment | Kaggle Notebook (GPU: Tesla P100) |
| Accuracy Target | Below 90% (regularised to avoid overfitting) |

---

## 📁 Dataset

**Surface Crack Detection** — published by arunrk7 on Kaggle

🔗 [https://www.kaggle.com/datasets/arunrk7/surface-crack-detection](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection)

| Attribute | Details |
|---|---|
| Total Images | 40,000 |
| Classes | 2 (Positive, Negative) |
| Images per Class | 20,000 each — perfectly balanced |
| Native Resolution | 227 × 227 pixels |
| Resized To | 96 × 96 pixels |
| Image Type | RGB JPEG |
| Train / Val Split | 80% / 20% (seed = 42) |

> **Note:** The dataset is not included in this repository. Attach it directly on Kaggle when running the notebook, or download it from the link above.

---

## 🧠 Models

### Model 1 — Custom CNN (Baseline)

```
Input (96×96×3)
    → Conv2D(32, 3×3, ReLU, L2) → MaxPool(2×2)
    → Conv2D(64, 3×3, ReLU, L2) → MaxPool(2×2)
    → Conv2D(128, 3×3, ReLU, L2) → MaxPool(2×2)
    → GlobalAveragePooling2D
    → Dropout(0.5)
    → Dense(1, Sigmoid)
```

- Trainable parameters: ~93,377
- Regularisation: L2 λ = 0.01 + Dropout 0.5

### Model 2 — MobileNetV2 (Transfer Learning)

```
MobileNetV2 base (frozen, ImageNet weights)
    → GlobalAveragePooling2D
    → Dense(128, ReLU, L2)
    → Dropout(0.7)
    → Dense(1, Sigmoid)
```

- Trainable parameters: ~155,649 (head only)
- Frozen parameters: ~2,257,984 (base)
- Mode: Feature extraction (base layers frozen)

---

## 📊 Results

| Model | Val Accuracy | AUC-ROC | Train Time | Params |
|---|---|---|---|---|
| Custom CNN | 82–87% | 0.88–0.93 | Fastest | ~93K |
| MobileNetV2 | 85–89% | 0.91–0.96 | Moderate | ~155K trainable |

> Accuracy is intentionally kept below 90% through L2 regularisation and Dropout.

---

## 📂 Repository Structure

```
surface-crack-detection/
│
├── notebooks/
│   └── surface_crack_detection_phase2.py   # Main Kaggle notebook (17 cells)
│
├── proposal/
│   └── Phase2_Proposal_SurfaceCrackDetection.docx
│
├── webapp/
│   ├── index.html                          # Frontend (Phase 3)
│   ├── app.py                              # Flask backend (Phase 3)
│   ├── requirements.txt
│   └── README.md
│
├── outputs/
│   ├── figures/
│   │   ├── sample_images.pdf
│   │   ├── Custom_CNN_curves.pdf
│   │   ├── MobileNetV2_curves.pdf
│   │   ├── Custom_CNN_cm.pdf
│   │   ├── MobileNetV2_cm.pdf
│   │   ├── ROC_curves.pdf
│   │   ├── gradcam_positive.pdf
│   │   ├── gradcam_negative.pdf
│   │   ├── shap_sample_0.pdf
│   │   ├── shap_sample_1.pdf
│   │   └── shap_sample_2.pdf
│   └── model_comparison.csv
│
├── dataset_info.md                         # Dataset description + link
├── requirements.txt                        # Python dependencies
└── README.md                               # This file
```

---

## ▶️ How to Run

### Option 1 — Kaggle (Recommended)

1. Go to [Kaggle](https://www.kaggle.com) and create a new notebook
2. Attach the **Surface Crack Detection** dataset:
   - Click **+ Add Data** → search `surface-crack-detection` by arunrk7
3. Upload `surface_crack_detection_phase2.py` or copy the cells into the notebook
4. Enable GPU: **Settings → Accelerator → GPU P100**
5. Click **Run All**

All outputs (PDF figures, CSV, `.keras` model) are saved to `/kaggle/working/`.

---

### Option 2 — Local Machine

**1. Clone the repository**
```bash
git clone https://github.com/avishekbhowmik/surface-crack-detection.git
cd surface-crack-detection
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Download the dataset**

Download from [Kaggle](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection) and place it so the folder structure is:
```
data/
├── Positive/
│   ├── 00001.jpg
│   └── ...
└── Negative/
    ├── 00001.jpg
    └── ...
```

**4. Update the dataset path**

In the notebook, the path auto-detection walks `/kaggle/input`. For local use, replace:
```python
def find_dataset_path():
    for root, dirs, _ in os.walk("/kaggle/input"):
        ...
```
with:
```python
BASE_PATH = "data/"   # path to your local dataset folder
```

**5. Run the notebook**
```bash
jupyter notebook notebooks/surface_crack_detection_phase2.py
```

---

## 🖼️ Output Figures

All figures are saved as high-resolution PDF files (dpi=300):

| File | Description |
|---|---|
| `sample_images.pdf` | 8 sample training images with class labels |
| `Custom_CNN_curves.pdf` | Custom CNN accuracy and loss curves |
| `MobileNetV2_curves.pdf` | MobileNetV2 accuracy and loss curves |
| `Custom_CNN_cm.pdf` | Confusion matrix — Custom CNN |
| `MobileNetV2_cm.pdf` | Confusion matrix — MobileNetV2 |
| `ROC_curves.pdf` | ROC curves for both models with AUC values |
| `gradcam_positive.pdf` | Grad-CAM on a cracked surface image |
| `gradcam_negative.pdf` | Grad-CAM on an intact surface image |
| `shap_sample_0/1/2.pdf` | SHAP importance overlays on 3 validation images |
| `model_comparison.csv` | Model comparison table (accuracy, AUC, time, params) |

---

## 🛠️ Technologies Used

| Library | Version | Purpose |
|---|---|---|
| TensorFlow / Keras | ≥ 2.16 | Model building and training |
| NumPy | ≥ 1.24 | Numerical operations |
| Pandas | ≥ 2.0 | Results tables and CSV export |
| Matplotlib | ≥ 3.7 | Plotting and figure export |
| Seaborn | ≥ 0.12 | Confusion matrix heatmaps |
| scikit-learn | ≥ 1.3 | Metrics: classification report, ROC, AUC |
| SHAP | ≥ 0.43 | Explainability — GradientExplainer |

Install all at once:
```bash
pip install tensorflow numpy pandas matplotlib seaborn scikit-learn shap
```

---

## 🌐 Phase 3 - Web Application

A publicly accessible web application is planned for Phase 3, hosted on **Hugging Face Spaces**.

**Features:**
- Upload any concrete surface image
- Get crack / no-crack prediction with probability score
- Grad-CAM heatmap showing which regions the model focused on
- Side-by-side image comparison
- Full project details page

**Stack:** Flask backend + HTML/CSS/JS frontend  
**Hosting:** Hugging Face Spaces (free tier)  
**Demo:** (https://huggingface.co/spaces/Avishek71/cracksense)*

---

## 📎 Links

| Resource | Link |
|---|---|
| Dataset | [Kaggle - Surface Crack Detection](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection) |
| Kaggle Notebook | [kaggle.com/code/avishekbhowmik/surface-crack-detection-ss26-pr](https://www.kaggle.com/code/avishekbhowmik/surface-crack-detection-ss26-pr) |
| Web Application | [CrackSense - Live App](https://huggingface.co/spaces/Avishek71/cracksense) ||

---

## 📄 License

This project is submitted as part of a university course assignment.  
Dataset credit: [arunrk7 on Kaggle](https://www.kaggle.com/datasets/arunrk7/surface-crack-detection)
