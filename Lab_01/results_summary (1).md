# Results Summary

## Table 1: Comparison of Transfer Learning Models

Eight pretrained CNN architectures were fine-tuned on the 5-class skin lesion dataset (actinic keratosis, basal cell carcinoma, melanoma, nevus, vascular lesion). ResNet101 achieved the best overall performance, with the highest accuracy (85.33%), recall (88.72%), and F1-score (84.28%). DenseNet121 was a close second (84.67% accuracy), followed by ResNet50 (83.33%) and EfficientNet-B0 (81.33%). VGG19 was the weakest performer across all metrics (72.67% accuracy, 72.47% F1-score), while AlexNet, despite being the oldest and simplest architecture, still reached a respectable 78% accuracy. All models achieved strong AUC scores (93–97%), indicating good class separability even where raw accuracy was lower. Overall, deeper/more modern architectures (ResNet101, DenseNet121) outperformed the older VGG family on this task.

### Table 1. Performance Metrics Across Pretrained CNN Architectures

| Model | Accuracy (%) | Precision (%) | Recall (%) | F1-Score (%) | AUC (%) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| AlexNet | 78.00 | 75.98 | 83.61 | 77.77 | 94.77 |
| VGG16 | 80.00 | 78.43 | 83.53 | 78.77 | 95.97 |
| VGG19 | 72.67 | 71.32 | 78.13 | 72.47 | 93.42 |
| ResNet18 | 78.67 | 76.32 | 78.57 | 76.76 | 96.92 |
| ResNet50 | 83.33 | 80.37 | 83.21 | 81.33 | 96.84 |
| ResNet101 | 85.33 | 83.00 | 88.72 | 84.28 | 96.73 |
| DenseNet121 | 84.67 | 80.33 | 82.04 | 80.98 | 96.94 |
| EfficientNet-B0 | 81.33 | 78.42 | 84.92 | 80.24 | 96.15 |

---

## Table 2: Comparison of Different Classifiers

Using deep features extracted from the best-performing backbone (ResNet50) as fixed inputs, seven classical machine learning classifiers were compared. RBF-SVM produced the best results (84.67% accuracy, 83.19% F1-score, 86.18% recall), narrowly ahead of XGBoost (84.00% accuracy, 81.45% F1-score). Random Forest and KNN performed similarly in the low-to-mid 80s for accuracy. Simpler models lagged behind: Logistic Regression and Linear SVM both hovered around 80% accuracy with noticeably lower F1-scores (~74–75%), and Decision Tree was the weakest classifier overall (76% accuracy, 72.85% F1-score, and by far the lowest AUC at 83.89%, reflecting poor probability calibration typical of single trees). This suggests that non-linear, ensemble-based classifiers extract more value from the deep features than linear or single-tree models.

### Table 2. Performance Comparison of Classifiers on Deep Features

| Feature Extractor | Classifier | Accuracy (%) | Precision (%) | Recall (%) | F1-Score (%) | AUC (%) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| Deep Features | Logistic Regression | 80.00 | 73.68 | 75.41 | 74.42 | 96.07 |
| Deep Features | Decision Tree | 76.00 | 72.49 | 73.97 | 72.85 | 83.89 |
| Deep Features | Random Forest | 82.67 | 78.90 | 81.57 | 79.81 | 96.60 |
| Deep Features | K-Nearest Neighbors (KNN) | 82.00 | 78.50 | 81.07 | 79.53 | 94.33 |
| Deep Features | Linear SVM | 80.67 | 74.57 | 75.95 | 75.13 | 96.43 |
| Deep Features | RBF-SVM | 84.67 | 82.31 | 86.18 | 83.19 | 96.39 |
| Deep Features | XGBoost | 84.00 | 80.72 | 83.54 | 81.45 | 96.06 |

---

## Table 3: Computational Efficiency Comparison

This table trades off accuracy against computational cost across seven architectures. EfficientNet-B0 is the clear efficiency winner, with only 4.01M parameters, 15.31 MB model size, and 0.83 GFLOPs — an order of magnitude lighter than VGG16/VGG19 — while still achieving 81.33% accuracy. DenseNet121 offers the best accuracy-to-size trade-off among the larger models, hitting 84.67% accuracy with just 6.96M parameters, though its inference time (16.37 ms) was the slowest in this comparison, likely due to its dense skip-connection structure. VGG16 and VGG19 are the most expensive models by far (134–140M parameters, 512–533 MB), yet they don't deliver correspondingly higher accuracy (80% and 72.67% respectively), making them the least efficient choices here. AlexNet and ResNet18 offer the fastest inference times (~2.2–2.7 ms) but with more modest accuracy (78–78.67%). Overall, EfficientNet-B0 is best suited for resource-constrained deployment, while DenseNet121 is preferable when accuracy is prioritized over inference speed.

### Table 3. Resource Requirements and Runtime Efficiency

| Model | Parameters (M) | Model Size (MB) | FLOPs (G) | Inference Time (ms) | Accuracy (%) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| AlexNet | 57.02 | 217.53 | 1.42 | 2.23 | 78.00 |
| VGG16 | 134.28 | 512.24 | 30.93 | 10.54 | 80.00 |
| VGG19 | 139.59 | 532.50 | 39.26 | 11.61 | 72.67 |
| ResNet18 | 11.18 | 42.64 | 3.65 | 2.69 | 78.67 |
| ResNet50 | 23.52 | 89.72 | 8.26 | 6.32 | 83.33 |
| DenseNet121 | 6.96 | 26.55 | 5.79 | 16.37 | 84.67 |
| EfficientNet-B0 | 4.01 | 15.31 | 0.83 | 7.92 | 81.33 |
