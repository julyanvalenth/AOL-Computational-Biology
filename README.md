# GC Content Analysis: Termofil vs Mesofil Bacteria

## Overview

Analisis komputasional pengaruh GC content terhadap adaptasi termal bakteri. Proyek ini membandingkan profil GC content genom 91 bakteri — 54 Termofil dan 37 Mesofil — menggunakan statistik inferensial dan machine learning untuk memprediksi suhu optimal tumbuh (Topt).

## Dataset

`real_gc_data.csv` — 91 genom bakteri dengan kolom berikut:

| Kolom | Deskripsi |
|-------|-----------|
| `accession` | NCBI accession number |
| `species` | Nama spesies |
| `genus` | Genus |
| `Topt` | Suhu optimal tumbuh (°C) |
| `group` | Kategori termal: `Termofil` / `Mesofil` |
| `label` | Label biner: 1 = Termofil, 0 = Mesofil |
| `GCw` | GC content seluruh genom (%) |
| `GC_tRNA` | GC content gen tRNA (%) |
| `GC_16S` | GC content gen 16S rRNA (%) |
| `GC_23S` | GC content gen 23S rRNA (%) |

Fitur turunan yang dihitung di notebook: `GC_core`, `GC1`, `GC2`, `GC3`.

## Struktur Proyek

```
GC_Analysis_Final/
├── gc_analysis.ipynb        # Notebook utama — seluruh analisis ada di sini
├── real_gc_data.csv         # Dataset genom bakteri
├── fig1_distribusi_gc.png   # Distribusi GC content per kelompok (boxplot)
├── fig2_distribusi_topt.png # Distribusi Topt per kelompok
├── fig3_perbedaan_gc.png    # Selisih rata-rata & effect size (Cohen's d)
├── fig4_scatter.png         # Scatter plot GC vs Topt + garis regresi
├── fig5_heatmap.png         # Heatmap korelasi Pearson
├── fig6_pgls.png            # Perbandingan koefisien OLS vs PGLS
├── fig8_model_comparison.png# Feature importance RF + prediksi vs aktual
├── fig9_roc_cm.png          # ROC curve + confusion matrix klasifikasi
└── fig10_single_auc.png     # AUC per fitur tunggal
```

## Alur Analisis

### 1. Statistik Deskriptif
Ringkasan mean, SD, min, max tiap fitur GC per kelompok.

### 2. Uji Beda (Mann-Whitney U)
Perbandingan non-parametrik GC content antara Termofil dan Mesofil beserta effect size (Cohen's d).

### 3. Analisis Korelasi
Pearson dan Spearman antara tiap fitur GC dengan Topt.

### 4. PGLS (Simulasi)
Approximate Phylogenetic Generalized Least Squares untuk kontrol efek filogeni (λ Pagel = 0.7, bootstrap 200 iterasi).

### 5. Regresi Prediksi Topt
Empat model dibandingkan pada split 70/30:
- **OLS** (Linear Regression)
- **Lasso** (LassoCV, 5-fold)
- **Random Forest** (GridSearchCV)
- **SVR** (RBF kernel, GridSearchCV)

### 6. Klasifikasi Termofil/Mesofil
Random Forest Classifier dievaluasi dengan Accuracy, F1, ROC-AUC, dan Cohen's Kappa.

## Hasil Ringkasan

| Hipotesis | Hasil |
|-----------|-------|
| H1: GC RNA berbeda signifikan antar kelompok | **DITERIMA** (GC_tRNA/16S/23S p < 0.001) |
| H2: GC RNA berkorelasi lebih kuat dari GCw | **DITERIMA** (r GC_16S = 0.795 vs GCw = −0.223) |
| H3: RF RMSE ≤ 12°C | **DITERIMA** (RMSE = 5.52°C, R² = 0.90) |
| H4: AUC klasifikasi ≥ 0.85 | **DITERIMA** (AUC = 1.000, Accuracy = 96.4%) |

## Cara Menjalankan

### Prasyarat

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-learn
```

### Jalankan Notebook

Buka `gc_analysis.ipynb` dengan Jupyter dan jalankan semua cell secara berurutan:

```bash
jupyter notebook gc_analysis.ipynb
```

> Pastikan `real_gc_data.csv` berada di direktori yang sama dengan notebook.

## Dependencies

| Library | Kegunaan |
|---------|----------|
| `numpy` | Komputasi numerik |
| `pandas` | Manipulasi data |
| `matplotlib` / `seaborn` | Visualisasi |
| `scipy` | Uji statistik (Mann-Whitney, Pearson, Spearman) |
| `scikit-learn` | Model ML (Lasso, RF, SVR, GridSearchCV) |
