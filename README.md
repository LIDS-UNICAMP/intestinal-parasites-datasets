# Intestinal Parasites Datasets

Repository containing three microscopy image datasets for the detection and classification of intestinal parasites: helminth eggs, helminth larvae, and protozoan cysts. Each dataset provides segmentation masks and stratified train/validation/test splits, including incremental training subsets for semi-supervised and low-data regime experiments.

---

## Dataset Composition

### Helminth Eggs — 5,112 images

| Class ID | Species | Count | % |
|:---:|---|---:|---:|
| 1 | *Hymenolepis nana* | 348 | 6.8% |
| 2 | *Hymenolepis diminuta* | 80 | 1.6% |
| 3 | *Ancylostoma* | 148 | 2.9% |
| 4 | *Enterobius vermicularis* | 122 | 2.4% |
| 5 | *Ascaris lumbricoides* | 337 | 6.6% |
| 6 | *Trichuris trichiura* | 375 | 7.3% |
| 7 | *Schistosoma mansoni* | 122 | 2.4% |
| 8 | *Taenia* spp | 236 | 4.6% |
| 9 | Impurities | 3,344 | 65.4% |
| | **Total** | **5,112** | |

### Helminth Larvae — 3,514 images

| Class ID | Species | Count | % |
|:---:|---|---:|---:|
| 1 | *Strongyloides stercoralis* | 446 | 12.7% |
| 2 | Impurities | 3,068 | 87.3% |
| | **Total** | **3,514** | |

### Protozoan Cysts — 9,568 images

| Class ID | Species | Count | % |
|:---:|---|---:|---:|
| 1 | *Entamoeba coli* | 719 | 7.5% |
| 2 | *Entamoeba histolytica* | 78 | 0.8% |
| 3 | *Endolimax nana* | 724 | 7.6% |
| 4 | *Giardia intestinalis* | 641 | 6.7% |
| 5 | *Iodamoeba bütschlii* | 1,501 | 15.7% |
| 6 | *Blastocystis hominis* | 189 | 2.0% |
| 7 | Impurities | 5,716 | 59.7% |
| | **Total** | **9,568** | |

---

## Repository Structure

```
intestinal-parasites-datasets/
├── helminth-eggs/
│   ├── images.tar.bz2                  # Compressed images archive
│   ├── masks.tar.bz2                   # Compressed masks archive
│   ├── splits/
│   │   ├── split1.json                 # Full split 1 (train / validation / test)
│   │   ├── split2.json
│   │   └── split3.json
│   └── splits_incremental/
│       ├── split1/
│       │   ├── data_descriptor_perc1.json    # 1% of train, same val/test
│       │   ├── data_descriptor_perc5.json
│       │   ├── data_descriptor_perc10.json
│       │   ├── data_descriptor_perc25.json
│       │   ├── data_descriptor_perc50.json
│       │   ├── data_descriptor_perc75.json
│       │   └── data_descriptor_perc100.json  # Full train (same as split1.json)
│       ├── split1_distribution.png     # Class distribution plot across percentages
│       ├── split2/ ...
│       └── split3/ ...
├── helminth-larvae/   (same structure)
└── protozoan-cysts/   (same structure)
```

---

## File Naming Convention

Images and masks follow the pattern:

```
{class_id:06d}_{image_id:08d}.png
```

For example, `000003_00000042.png` is the 42nd image of class 3. The class ID directly maps to the tables above for each dataset.

---

## Splits

Each dataset has **3 independent splits**, each generated with a fixed random seed for reproducibility. The stratified split ratios are:

| Subset | Ratio | Eggs | Larvae | Cysts |
|---|---:|---:|---:|---:|
| Train | 40% | 2,041 | 1,405 | 3,824 |
| Validation | 10% | 507 | 350 | 953 |
| Test | 50% | 2,564 | 1,759 | 4,791 |

Stratification is performed per class, so the class distribution is preserved across all subsets.

### Incremental Training Subsets

For each split, the training set is further sub-sampled at 7 incremental percentages — `1, 5, 10, 25, 50, 75, 100` — while keeping the validation and test sets fixed. Sub-sampling is also stratified per class, and subsets are **nested** (the 5% subset contains the 1% subset, and so on), enabling consistent low-data regime evaluations.

---

## Loading a Split

```python
import json

with open("helminth-eggs/splits/split1.json") as f:
    split = json.load(f)

train_files      = split["train"]       # list of filenames, e.g. "000001_00000001.png"
validation_files = split["validation"]
test_files       = split["test"]
```

For an incremental subset:

```python
with open("helminth-eggs/splits_incremental/split1/data_descriptor_perc10.json") as f:
    split = json.load(f)
# split["train"] now contains 10% of the original training set
```