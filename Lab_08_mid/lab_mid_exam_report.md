# Lab Mid Exam: HOG vs SIFT on Blood-Cell Images

**Registration number:** 008

| Setting | Value |
|---|---|
| Random seed | 8 |
| HOG cell size | $8 \times 8$ |
| SIFT grid size | $6 \times 6$ (36 keypoints) |

---

## Q1. Data setup

Dataset: BloodMNIST (size 64), classes eosinophil, monocyte, neutrophil, platelet. Images converted to grayscale and resized to $128 \times 128$.

```
train (400, 128, 128) (400,) {'eosinophil': 100, 'monocyte': 100, 'neutrophil': 100, 'platelet': 100}
val (120, 128, 128) (120,) {'eosinophil': 30, 'monocyte': 30, 'neutrophil': 30, 'platelet': 30}
test (120, 128, 128) (120,) {'eosinophil': 30, 'monocyte': 30, 'neutrophil': 30, 'platelet': 30}
```

Figure: grid of 4 example images per class (see notebook).

---

## Q2. HOG + SVM

```
(400, 8100) (120, 8100)
HOG feature length: 8100
```

**HOG feature length (hand calculation)**

- Image = $128 \times 128$, cell = $8 \times 8$, so cells = $128 / 8 = 16$ per side, giving $16 \times 16$ cells.
- Blocks are $2 \times 2$ cells with a stride of 1 cell, so blocks per side = $16 - 2 + 1 = 15$, giving $15 \times 15 = 225$ blocks.
- Each block = $2 \times 2\text{ cells} \times 9\text{ orientations} = 36$ values.
- Total = $225 \times 36 = 8100$, which matches the printed length.

Figure: HOG pictures for one image per class (see notebook).

**Choosing C on the validation set**

| C | Validation accuracy |
|---|---|
| 0.1 | 0.7417 |
| 1 | 0.7250 |
| 10 | 0.7250 |

Best $C = 0.1$

**Validation results (HOG + SVM)**

- Accuracy: 0.7417
- Macro-F1: 0.7399

Confusion matrix (rows = true class, columns = predicted class):

| | eosinophil | monocyte | neutrophil | platelet |
|---|---|---|---|---|
| **eosinophil** | 18 | 7 | 4 | 1 |
| **monocyte** | 1 | 25 | 4 | 0 |
| **neutrophil** | 9 | 3 | 18 | 0 |
| **platelet** | 0 | 0 | 2 | 28 |

---

## Q3. SIFT + SVM

**Normal SIFT keypoints on training images**

| Statistic | Value |
|---|---|
| Average keypoints per image | 72.3925 |
| Minimum | 8 |
| Maximum | 189 |
| Images with zero keypoints | 0 |

Figure: keypoints drawn on one image (see notebook).

**Why a different number of keypoints is a problem for an SVM:** An SVM needs every image to give a feature vector of the same length. Normal SIFT gives a different number of keypoints per image (each with 128 values), so the vectors have different lengths and cannot be fed to the SVM directly.

**Fixed grid SIFT**

```
(400, 4608) (120, 4608)
SIFT feature length: 4608
```

**SIFT feature length (hand calculation)**

- Grid = $6 \times 6 = 36$ grid points, keypoint size 32.
- Each point gives a 128-value descriptor.
- Total = $36 \times 128 = 4608$, which matches the printed length.

**Choosing C on the validation set**

| C | Validation accuracy |
|---|---|
| 0.1 | 0.7167 |
| 1 | 0.7167 |
| 10 | 0.7167 |

All three values tied, so the first one, $C = 0.1$, was kept.

**Validation results (SIFT + SVM)**

- Accuracy: 0.7167
- Macro-F1: 0.7076

Confusion matrix (rows = true class, columns = predicted class):

| | eosinophil | monocyte | neutrophil | platelet |
|---|---|---|---|---|
| **eosinophil** | 15 | 8 | 6 | 1 |
| **monocyte** | 4 | 25 | 1 | 0 |
| **neutrophil** | 6 | 4 | 17 | 3 |
| **platelet** | 0 | 0 | 1 | 29 |

---

## Q4. Robustness

**Prediction (written before running the test)**

- **Rotation:** I expect HOG to lose more accuracy. HOG uses fixed $8 \times 8$ cells and gradient orientation bins, so rotating the image shifts the gradient directions and moves content between cells and bins. The SIFT grid uses larger 32-size patches that overlap, so it changes less.
- **Noise:** I expect HOG to lose more accuracy. HOG works directly on pixel gradients, and noise creates many strong random gradients in small cells. SIFT smooths the image with Gaussian blur before computing gradients, which reduces the noise.

**Results on the test set** (rotation $20^\circ$, Gaussian noise $\sigma = 0.05$)

| Model | Clean accuracy | Rotated accuracy | Noisy accuracy |
|---|---|---|---|
| HOG + SVM | 0.7250 | 0.6083 | 0.5000 |
| SIFT + SVM | 0.7333 | 0.5333 | 0.7333 |

Figure: clean, rotated and noisy example (see notebook).

**Was the prediction correct?**

The prediction was partly correct. For noise it was right, since HOG fell from 0.7250 to 0.5000 while SIFT stayed at 0.7333, but for rotation it was wrong, since HOG dropped from 0.7250 to 0.6083 while SIFT dropped more, from 0.7333 to 0.5333. This is likely because the grid keypoints have their angle fixed at 0, so the SIFT descriptors are not rotation invariant, while SIFT's Gaussian smoothing explains why noise did not affect it.

---

## Q5. Short answers and AI log

**(a) Why does HOG use block normalisation?**
Gradient strength changes with lighting and contrast. Normalising each block makes the features depend on the local shape rather than how bright or contrasty the area is, so the same shape gives similar features under different lighting.

**(b) Why is it wrong to choose C using the test set?**
The test set is meant to give an unbiased estimate of performance on unseen data. If $C$ is tuned on it, the model is effectively fitted to the test data, so the reported accuracy is optimistic and no longer reflects real performance. $C$ should be chosen on the validation set.

**Most confused classes**

```
HOG most confused: neutrophil -> eosinophil count: 9
SIFT most confused: eosinophil -> monocyte count: 8
```

Eosinophil was the most confused class in both models. HOG mostly mixed up neutrophil and eosinophil (9 neutrophils predicted as eosinophil, 4 the other way), and SIFT mostly mixed up eosinophil and monocyte (8 eosinophils predicted as monocyte, 4 the other way). In grayscale, the colour of the cytoplasm and granules, which is a main cue for telling these cells apart, is lost, and only shape and texture remain, which look similar for these classes.

**AI log**

| Prompt (one line) | What I changed | How I checked |
|---|---|---|
| Write HOG + SVM code for BloodMNIST with the exam settings | Set seed to 8 and HOG cell to $8 \times 8$ | Printed the feature length (8100) and matched my hand calculation |
| Create fixed grid SIFT features for an SVM | Set the grid to $6 \times 6$ and keypoint size to 32 | Printed the shape (400, 4608) and matched $36 \times 128$ |
| Write the rotation and noise test code | Used rotation $20^\circ$ and $\sigma = 0.05$, applied before feature extraction | Displayed the clean, rotated and noisy images and checked the table values |