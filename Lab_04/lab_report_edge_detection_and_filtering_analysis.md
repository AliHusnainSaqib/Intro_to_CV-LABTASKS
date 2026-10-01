# Image Processing Lab Report: Filtering and Edge Detection Analysis

---

## 1. Experimental Output Tables

### Table 1: Optimal Pipeline Evaluation Across Test Images
*Configuration: Gaussian Filter + Canny Edge Detection ($50\text{--}100$)*

| Index | Image | Best Filter | Edge Method | Area (pixels) | Perimeter (pixels) |
| :---: | :---: | :---: | :---: | :---: | :---: |
| 0 | Image 1 | Gaussian | Canny 50-100 | 15 | 16.0 |
| 1 | Image 2 | Gaussian | Canny 50-100 | 20 | 33.8 |
| 2 | Image 3 | Gaussian | Canny 50-100 | 163 | 144.12 |
| 3 | Image 4 | Gaussian | Canny 50-100 | 3870 | 1243.65 |
| 4 | Image 5 | Gaussian | Canny 50-100 | 240 | 136.07 |

---

### Table 2: Filter and Edge Detector Benchmark (Noise & Contour Sensitivity)

| Index | Method | Edge Pixels (Noise Indicator) | Area | Perimeter |
| :---: | :--- | :---: | :---: | :---: |
| 0 | Original + Sobel | 67932 | 264599 | 2567.36 |
| 1 | Original + Canny | 246 | 87 | 93.98 |
| 2 | Average + Sobel | 32845 | 50754 | 1338.81 |
| 3 | Average + Canny | 0 | 0 | 0.0 |
| 4 | Gaussian + Sobel | 41115 | 69219 | 3769.2 |
| 5 | Gaussian + Canny | 16 | 15 | 16.0 |
| 6 | Median + Sobel | 36122 | 62047 | 3172.02 |
| 7 | Median + Canny | 17 | 18 | 17.66 |

---

## 2. Lab Questions and Answers

### Q1: Which combination of filter and edge detector performed best, and why?
* **Answer:** **Gaussian + Canny (50–100)** is the best overall method (closely followed by Median + Canny). 
  * It recorded the lowest edge pixel count ($16\text{ pixels}$), effectively suppressing spurious noise while preserving true structural boundaries.
  * It extracted a clean, well-defined contour with an $\text{Area} = 15$ and $\text{Perimeter} = 16.0$, avoiding false merges caused by background artifacts.

---

### Q2: Why does the Sobel operator yield a significantly higher edge pixel count compared to Canny?
* **Answer:** 
  1. **Lack of Suppression:** Sobel computes simple first-order spatial gradients ($G_x$ and $G_y$) across all pixels without non-maximum suppression, creating thick, multi-pixel-wide edges.
  2. **Noise Susceptibility:** Sobel does not implement dual-threshold hysteresis. As a result, random high-frequency background variations exceed the gradient threshold and register as false edges ($67{,}932\text{ pixels}$ on the original image). In contrast, Canny thins edges to single-pixel width and discards weak, non-connected gradients.

---

### Q3: Why did the "Average + Canny" combination result in 0 edge pixels, 0 area, and 0 perimeter?
* **Answer:** 
  * The linear box/average filter distributes intensity evenly over its local kernel, causing aggressive over-smoothing and blurring of authentic boundary transitions.
  * Consequently, the gradient magnitude across the object boundary fell completely below Canny's lower hysteresis threshold ($T_{\text{lower}} = 50$), causing all true edges to be rejected as background.

---

### Q4: How does noise impact morphological measurements like Area and Perimeter?
* **Answer:** 
  * **False Inflation:** In noisy, unfiltered outputs (such as `Original + Sobel`), background noise pixels merge with the foreground contour, massively inflating the measured area ($\text{Area} = 264{,}599$) and distorting the perimeter ($\text{Perimeter} = 2567.36$).
  * **Accurate Boundary Isolation:** Applying proper pre-filtering (Gaussian or Median) isolates the genuine object boundary, yielding realistic geometric parameters ($\text{Area} = 15$, $\text{Perimeter} = 16.0$).

---

### Q5: How do the Gaussian and Median filters compare when paired with Canny edge detection?
* **Answer:** 
  * Both filters demonstrated high performance when paired with Canny:
    * **Gaussian + Canny:** $16\text{ edge pixels}$, $\text{Area} = 15$, $\text{Perimeter} = 16.0$.
    * **Median + Canny:** $17\text{ edge pixels}$, $\text{Area} = 18$, $\text{Perimeter} = 17.66$.
  * **Distinction:** Gaussian filtering is optimal for continuous, zero-mean Gaussian noise, whereas Median filtering is non-linear and excels at eliminating salt-and-pepper (impulse) noise while preserving sharp corner transitions.

---

### Q6: Why is total edge pixel count considered an effective indicator of noise in this experiment?
* **Answer:** 
  * For an isolated target object of known modest size, the true boundary consists of a bounded, finite set of single-pixel-wide edge points.
  * Any large inflation in the total count of edge pixels outside the expected contour perimeter indicates spurious gradient detections triggered by background noise rather than genuine object edges.