# VAE-Based Dimensionality Reduction for FSHD Gene Expression Classification

![Python](https://img.shields.io/badge/Python-3.8%2B-blue) ![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange) ![scikit-learn](https://img.shields.io/badge/scikit--learn-latest-green) ![License](https://img.shields.io/badge/License-MIT-yellow)

## Overview

This project investigates the use of **Variational Autoencoders (VAEs)** as a dimensionality reduction technique for high-dimensional gene expression microarray data to classify **Facioscapulohumeral Muscular Dystrophy (FSHD)** versus healthy (Normal) samples.

Gene expression datasets are notoriously high-dimensional (33,000+ features), making direct classification challenging. This study compresses the feature space into a compact latent representation using a VAE, and compares the performance of multiple classifiers **before and after** VAE-based dimensionality reduction.

---

## Dataset

- **Source:** GEO Dataset `GSE36398` (FSHD Series Matrix)
- **Samples:** 50 samples (FSHD patients and Normal controls)
- **Features:** 33,298 gene probes
- **Labels:** Binary — `FSHD` vs `Normal`
- **Sample IDs:** GSM892341 – GSM892390

---

## Methodology

### 1. Data Preprocessing
- Parsed GEO Series Matrix file and transposed to samples × features format
- Merged gene expression data with disease label metadata
- Label encoded target variable (`FSHD=1`, `Normal=0`)
- Applied **StandardScaler** normalization

### 2. Train/Test Split
- 80/20 stratified split → **40 training samples**, **10 test samples**

### 3. Baseline Classification (Raw Features)
Six classifiers were trained directly on the 33,298-dimensional scaled feature space:

| Classifier | Notes |
|---|---|
| Random Forest | Ensemble of decision trees |
| SVM | Support Vector Machine with RBF kernel |
| k-NN | K-Nearest Neighbors |
| Naive Bayes | Gaussian Naive Bayes |
| Logistic Regression | Linear classifier |
| Gradient Boosting | Sequential ensemble method |

Metrics evaluated: **Accuracy, F1 Score, Recall, ROC-AUC**

### 4. VAE-Based Dimensionality Reduction
A custom VAE was built using TensorFlow/Keras to compress 33,298 features into a **500-dimensional latent space**.

**VAE Architecture:**
```
Input (33,298) → Dense(512, ReLU) → z_mean / z_log_var → Sampling Layer → z (500)
                                                                              ↓
Output (33,298) ← Dense(512, ReLU) ← Dense(500) [Decoder]
```

**Training Configuration:**

| Hyperparameter | Value |
|---|---|
| Latent Dimensions | 500 |
| Learning Rate | 5e-5 |
| Batch Size | 16 |
| Epochs | 50 |
| Optimizer | Adam |
| Loss | Reconstruction Loss + KL Divergence |

### 5. Post-VAE Classification
All six classifiers were retrained on the **500-dimensional VAE-encoded features** and evaluated using the same metrics for direct comparison.

---

## Project Structure

```
├── vae-dimensionality-reduction.ipynb   # Main notebook
├── README.md                            # Project documentation
```

---

## Requirements

```bash
pip install tensorflow scikit-learn pandas numpy matplotlib
```

Or install from requirements:

```
tensorflow>=2.x
scikit-learn
pandas
numpy
matplotlib
```

---

## How to Run

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/vae-dimensionality-reduction.git
   cd vae-dimensionality-reduction
   ```

2. **Download the dataset** from [GEO GSE36398](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE36398) and place the Series Matrix file at:
   ```
   /kaggle/input/fshd-dataset/Dataset2_GSE36398_series_matrix (1).txt
   ```
   *(Update the `file_path` variable in Cell 0 if running locally)*

3. **Run the notebook**
   ```bash
   jupyter notebook vae-dimensionality-reduction.ipynb
   ```

---

## Key Results

The notebook compares classifier performance on:
- **Raw features** (33,298 dimensions) — Baseline
- **VAE-encoded features** (500 dimensions) — After dimensionality reduction

ROC curves are generated for each classifier in both settings, providing a visual comparison of classification performance.

---

## Key Concepts

- **VAE (Variational Autoencoder):** A generative model that learns a probabilistic latent space, enabling smooth and meaningful dimensionality reduction.
- **KL Divergence Loss:** Regularizes the latent space to follow a standard normal distribution.
- **Reconstruction Loss:** Ensures the decoder can faithfully reconstruct the original input.
- **FSHD:** A genetic muscular dystrophy caused by derepression of the DUX4 gene, detectable through characteristic gene expression patterns.

---

## Technologies Used

- **Python 3.8+**
- **TensorFlow / Keras** — VAE architecture
- **scikit-learn** — Classifiers, preprocessing, evaluation
- **Pandas / NumPy** — Data manipulation
- **Matplotlib** — ROC curve visualization

---

## Citation

If you use this work, please cite the dataset:

> GEO Accession: [GSE36398](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE36398)

---

## License

This project is licensed under the MIT License.
