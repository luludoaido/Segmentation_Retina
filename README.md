# Segmentation_Retina

Automated retinal blood vessel segmentation pipeline using the [DRIVE dataset](https://drive.grand-challenge.org/). Two approaches are implemented and compared: the **Frangi filter** (classical image processing) and a **Random Forest classifier** (machine learning). The project is part of a university course assignment at ZHAW (MSLS / CO4, FS26).

---

## Results

| Method        | Mean Dice | Std   | Mean Jaccard |
|---------------|-----------|-------|--------------|
| Frangi Filter | 0.306     | 0.117 | 0.190        |
| Random Forest | 0.522     | 0.030 | 0.354        |

Evaluated on 10 unseen test images from the DRIVE dataset. The Random Forest outperforms the Frangi filter both in accuracy and consistency.

---

## Repository Structure

```
Segmentation_Retina/
│
├── DRIVE/
│   ├── training/
│   │   ├── 1st_manual/                 # Reference manual segmentations (DRIVE official)
│   │   ├── images/                     # 20 retinal fundus images (.tif)
│   │   ├── mask/                       # Field-of-view masks (.gif)
│   │   ├── segmentation_images/        # Manual ground truth masks (created in Fiji)
│   │   ├── auto_segmentation_frangi/   # Frangi segmentation outputs
│   │   ├── auto_segmentation_rf/       # Random Forest segmentation outputs
│   │   ├── overlap_frangi/             # TP/FN/FP overlap images — Frangi
│   │   ├── overlap_rf/                 # TP/FN/FP overlap images — Random Forest
│   │   ├── evaluation_barchart.png     # Bar chart: mean Dice scores
│   │   ├── evaluation_table.png        # Table: per-image Dice & Jaccard scores
│   │   └── preprocessing_example.png  # Preprocessing step visualization
│   └── test/
│       ├── images/
│       ├── mask/
│       ├── segmentation_images/
│       ├── auto_segmentation_frangi/
│       ├── auto_segmentation_rf/
│       ├── overlap_frangi/
│       ├── overlap_rf/
│       ├── evaluation_barchart.png
│       └── evaluation_table.png
│
├── daSilva_doAido_Retina_Co4.ipynb     # Main notebook (full pipeline)
├── daSilva_doAido_Retina_Co4.pdf       # PDF export of the notebook
├── file_clean.ipynb                    # Cleaned-up version of the notebook
├── first_try.ipynb                     # Initial exploration notebook
├── requirements.txt                    # Python dependencies
└── README.md
```

---

## Pipeline

The full pipeline is documented step by step in `daSilva_doAido_Retina_Co4.ipynb`:

**1. Preprocessing**
- Extract the green channel (highest contrast for blood vessels)
- Apply CLAHE (`clipLimit=2.0`, `tileGridSize=(8,8)`) for local contrast enhancement
- Normalize pixel values to [0, 1]
- Apply retinal field-of-view mask to restrict processing to the eyeball region

**2. Manual Segmentation**
- 10 training images and 10 test images annotated manually in Fiji (ImageJ) — all available images
- Pencil tool used for vessel tracing; binarized via Threshold function
- Saved as binary TIF masks (white = vessel, black = background)

**3. Automated Segmentation**

*Frangi Filter* — classical, unsupervised vessel-enhancement method based on Hessian eigenvalues. Detects tubular structures at multiple scales. Results are binarized at threshold 0.05, small objects removed, and an eroded mask applied to suppress border artifacts.

*Random Forest* — pixel-wise classifier trained on 10 manually annotated training images. Feature vector per pixel:
- Raw intensity
- Gaussian blur at σ = 1, 2, 4
- Sobel edge response
- Frangi vessel probability

**4. Evaluation**
- Dice score (F1) and Jaccard score (IoU) computed per image
- Overlap visualization: Green = True Positive, Red = False Negative, Blue = False Positive

---

## Dataset

The [DRIVE dataset](https://drive.grand-challenge.org/) (Digital Retinal Images for Vessel Extraction) is **included** in this repository under the `DRIVE/` folder.

- 20 color fundus photographs used in total (10 training, 10 test), 565×584 px, TIF format
- Each image accompanied by a binary field-of-view mask
- Originally acquired as part of a diabetic retinopathy screening program in the Netherlands

---

## Getting Started

### Requirements

- Python 3.8+
- See `requirements.txt` for all dependencies

### Installation

```bash
git clone https://github.com/luludoaido/Segmentation_Retina.git
cd Segmentation_Retina
git checkout develope
pip install -r requirements.txt
```

### Run

```bash
jupyter notebook
```

Open `daSilva_doAido_Retina_Co4.ipynb` and run the cells from top to bottom. Make sure the DRIVE dataset is placed in the correct folder before running.

---

## Key Libraries

| Library | Purpose |
|---------|---------|
| `opencv-python` | Image loading, CLAHE, color conversion |
| `scikit-image` | Frangi filter, morphological operations |
| `scikit-learn` | Random Forest classifier, evaluation metrics |
| `Pillow` | TIF/GIF mask loading |
| `numpy` / `matplotlib` | Numerical processing and visualization |

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Author

**Luana da Silva do Aido** — ZHAW, MSLS / CO4, FS26  
[GitHub: luludoaido](https://github.com/luludoaido)