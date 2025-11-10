# TOFD Defects Dataset

## 1. General Information
This dataset is intended for training and validating neural networks for defect segmentation from TOFD (Time‑of‑Flight Diffraction) data.

Includes:
- **Synthetic data** — echo signals simulated in the CIVA software package using the beam-tube method.
- **Real data** — measurements performed on samples with purposely introduced defects.

Data are split into training and validation sets.

---

## 2. Data Structure

```
DATASTORES/
│
├── actualdatastore/
│   ├── data_real+civa/          # images (train)
│   ├── data_real+civa_val/      # images (validation)
│   ├── mask_real+civa/          # masks (train)
│   ├── mask_real+civa_val/      # masks (validation)
│
├── OriginalData/
│   ├── data_base/               # original images from real measurements
│   ├── mask_base/               # masks for real-data images
│   ├── civa_base/               # images generated with CIVA
│   ├── mask_civa/               # masks for CIVA images
│   ├── Data_mix/                # merged dataset (Real data + CIVA)
│   ├── Mask_mix/                # masks for the merged dataset (Real data + CIVA)
```

Loading into MATLAB:

```matlab
% Image datastores
ds = imageDatastore('.../data_real+civa','FileExtensions','.png');
dsv = imageDatastore('.../data_real+civa_val','FileExtensions','.png');

% Define class names and their label IDs
classNames = ["E","LSUP","LSDN","LBIN","VI","VC","HW","BS"];
labelIDs   = [0,50,100,125,150,200,220,250];

% Pixel label datastores (masks)
pxds  = pixelLabelDatastore('.../mask_real+civa', classNames, labelIDs);
pxdsv = pixelLabelDatastore('.../mask_real+civa_val', classNames, labelIDs);

% Combine images and pixel masks into pixelLabelImageDatastore
pximds  = pixelLabelImageDatastore(ds, pxds);
pximdsv = pixelLabelImageDatastore(dsv, pxdsv);
```

---

## 3. Classes and Labels

| Label (ID) | Class  | Defect type                          | Example real defects                      | CIVA simulation                    |
|------------|--------|---------------------------------------|-------------------------------------------|------------------------------------|
| 0          | E      | No discontinuity                      | —                                         | —                                  |
| 50         | LSUP   | Extended near-surface                 | Under-cut, lack of fusion at an edge      | Groove, lateral cylindrical hole (outer surface) |
| 100        | LSDN   | Extended near‑root / bottom           | Lack of root fusion, shrinkage, excessive penetration | Groove, lateral cylindrical hole (bottom), simulation of shrinkage |
| 125        | LBIN   | Extended internal                     | Lack of fusion at a border, lack of fusion between beads | Internal groove, lateral cylindrical hole |
| 150        | VI     | Single volumetric (point-like) defect | Slag, pore, inclusion                     | Sphere, vertical hole              |
| 200        | VC     | Cluster of volumetric defects         | Clustered pores, slag inclusions, multilayer defect | Cluster of spheres (simulation only) |
| 220        | HW     | Head wave                             | —                                         | —                                  |
| 250        | BS     | Bottom signal                         | —                                         | —                                  |

---

## 4. Synthetic Data (CIVA)

Numerical experiments used echo models computed in **CIVA** based on beam‑tube theory.
The following defect types were modeled:

- Bottom (root) cracks
- Internal cracks
- Near-surface cracks
- Spherical defects (SDH)
- Lateral cylindrical holes (simulating lack of fusion)

**Ranges of defect parameters:**
- height: 0.5–20 mm
- width: 2.5–25 mm

Noise was modeled as point reflectors randomly distributed with a density of **2 reflectors/mm³**.

---

## 5. Real Data (Samples with Defects)

Two metal samples were manufactured for validation and testing:

- **Material**: Steel 20
- **Thickness**: 25 mm
- **Each sample**: contains 8 artificially created defects
- **Types of defects**:
  - 6 bottom cracks (grooves)
  - 6 near-surface cracks (grooves)
  - 4 internal cracks (side slots)

**Measurement setup:**
- Transducers: 5 MHz, Ø6 mm
- Incident angle: 60°
- Mounting: measurements from both sides of each sample
- Scanning step:
  - along X axis: 0.5 mm
  - along Y axis: 0.2 mm
- Total along Y: 170 sets of echo signals

---

## 6. Example Augmentation (MATLAB)

```matlab
augmenter = imageDataAugmenter( ...
    'RandXTranslation', [-10, 10], ...
    'RandYTranslation', [-10, 10], ...
    'RandScale', [0.85, 1.15]);

pximds = pixelLabelImageDatastore(ds, pxds, ...
    'DataAugmentation', augmenter);
```

---

## 7. Usage
The dataset can be used for:
- training segmentation models (U‑Net, U‑Net++, Attention U‑Net, etc.),
- testing robustness on synthetic vs. real data,
- developing signal filtering and post-processing methods.
  
## License

This dataset is licensed under the **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** license.  
You are free to use and modify the data for research and educational purposes, but commercial use is not permitted.  
See the [LICENSE](./LICENSE) file for details.
