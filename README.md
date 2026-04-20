# Data Dominators - Brain Tumor Segmentation Project

Welcome to the **Data Dominators** End-Semester Project repository. This project tackles the challenging task of 3D Brain Tumor Segmentation from multi-parametric MRI scans using a highly optimized, voxel-wise Machine Learning approach.

Instead of relying on deep learning architectures like U-Net, this project demonstrates a highly explainable and computationally efficient pipeline revolving around **XGBoost** and **Markov Random Field (MRF)** post-processing.

---

## 🚀 Pipeline Architecture

Our end-to-end segmentation pipeline includes the following stages:

1. **Preprocessing & Normalization** 
   - Modalities used: T1c, T1n, T2f, and T2w.
   - Computes a global brain mask to restrict computation to valid regions.
   - Voxel-wise Z-score normalization for intensity standardization across patients.

2. **Extensive Feature Extraction**
   - Extracts **109 handcrafted features** per voxel to capture intricate tissue textures and boundaries.
   - **Local Textures & Gradients:** Multi-scale uniform filters, fast local standard deviations, gradient magnitudes (Sobel), and Laplacian.
   - **Advanced Filters:** Multi-scale Gaussians, Difference of Gaussians (DoG), and Gabor kernels across 4 orientations.
   - **Spatial & Semantic Context:** Asymmetry features, entropy proxies, distance to the brain edge, and radial distance to the brain center.
   - **Cross-Modal Relationships:** Inter-modality ratios (e.g., T1c/T2f) and differences that highlight blood-brain-barrier breakdown or infiltrative edema.

3. **Data Sampling & Training (XGBoost)**
   - **Handling Extreme Imbalance:** Addressed the massive background-to-tumor imbalance by enforcing a `1:49` tumor-to-background sampling ratio.
   - **Custom Sample Weights:** Pushed the model to focus on critical and rarer sub-regions (NCR, Edema, ET) using targeted weights (e.g., NCR: 8.0, ET: 6.0).
   - **Model:** `XGBClassifier` optimized using Hist gradient boosting for memory efficiency and speed on millions of samples.

4. **MRF Post-Processing (Spatial Smoothing)**
   - Uses Mean-Field Variational Inference on a Potts MRF to smooth the raw probabilistic outputs from XGBoost.
   - Penalizes biologically implausible tumor region adjacencies and removes isolated artifacts/blobs (< 200 voxels).
   - Enhances final boundary contiguity.

---

## 📊 Evaluation & Results

The pipeline was thoroughly evaluated on a held-out test set of **136 patients**. 
We report the **Dice Similarity Coefficient (F1-Score)** and **Intersection over Union (IoU / Jaccard)** across the three standard clinical sub-regions:
- **WT (Whole Tumor):** Includes ET, NCR, and Edema.
- **TC (Tumor Core):** Includes ET and NCR.
- **ET (Enhancing Tumor):** Only the enhancing region.

### Quantitative Results (136 Unseen Patients)

#### Dice
| Region | Mean | Std | Median | Min | Max |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **WT** | 0.8279 | 0.1420 | 0.8612 | 0.0000 | 0.9707 |
| **TC** | 0.6849 | 0.2579 | 0.7986 | 0.0000 | 0.9628 |
| **ET** | 0.6788 | 0.2641 | 0.7893 | 0.0000 | 0.9628 |
| **Edema** | 0.7754 | 0.1549 | 0.8243 | 0.0000 | 0.9537 |
| **Necrosis** | 0.1115 | 0.2263 | 0.0000 | 0.0000 | 0.8703 |

#### IoU
| Region | Mean | Std | Median | Min | Max |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **WT** | 0.7249 | 0.1574 | 0.7563 | 0.0000 | 0.9431 |
| **TC** | 0.5684 | 0.2476 | 0.6647 | 0.0000 | 0.9283 |
| **ET** | 0.5629 | 0.2511 | 0.6519 | 0.0000 | 0.9283 |
| **Edema** | 0.6545 | 0.1715 | 0.7011 | 0.0000 | 0.9114 |
| **Necrosis** | 0.0791 | 0.1700 | 0.0000 | 0.0000 | 0.7703 |

*Testing notes: The WT threshold was tuned to 0.52 using a validation subset. Results indicate strong prediction reliability, especially considering the constraints of voxel-based classical machine learning methods.*

---

## 📁 Repository Structure

* `Code/`: Contains the Jupyter notebooks encompassing the Pre-Mid Sem and Post-Mid Sem code, along with standalone visualization scripts.
* `End_Sem_Report/`: Contains the official PDF compilation of our End-Semester report.
* `Results/`: Standalone predictions, figures, and plots characterizing the model's region-wise distributions.

---

**Team Name:** Data Dominators