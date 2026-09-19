# Lab 02 Summary Report — Skin Lesion Classification with Image Filters

## What We Did

The goal of this lab was to check if using image filters before feeding images
into a deep learning model changes how well the model can classify skin
lesions. We took the 3 best models from Lab 01 and trained each one 6 times.
One time with no filter at all, and 5 more times with a different filter
applied to the images first. Then we compared the results.

## Dataset

We used the ISIC skin cancer dataset with 9 classes of skin lesions. The full
dataset had 2357 images. Since some classes had many more images than others,
we made a balanced subset so the model would not be biased toward the bigger
classes. Our balanced subset had 1860 images total, split into:

- Train: 1301 images
- Validation: 279 images
- Test: 280 images

## Models Used

We used the top 3 models from Lab 01 based on accuracy:

1. ResNet101
2. DenseNet121
3. ResNet50

## Filters Used

We tested 5 filters plus the no filter baseline:

1. No Filter (baseline)
2. Average (blur)
3. Gaussian (smooth blur)
4. Median (removes noise)
5. Sharpening (makes edges stand out)
6. Sobel (only shows edges, removes color and texture)

## Results Table

| Model | Filter | Accuracy | Precision | Recall | F1-score | Balanced Accuracy | AUC |
|---|---|---|---|---|---|---|---|
| ResNet101 | No Filter | 0.7071 | 0.6548 | 0.6638 | 0.6575 | 0.6638 | 0.9469 |
| ResNet101 | Average | 0.7429 | 0.6822 | 0.6942 | 0.6839 | 0.6942 | 0.9478 |
| ResNet101 | Gaussian | 0.6929 | 0.6546 | 0.6449 | 0.6383 | 0.6449 | 0.9353 |
| ResNet101 | Median | 0.7107 | 0.6322 | 0.6546 | 0.6379 | 0.6546 | 0.9408 |
| ResNet101 | Sharpening | 0.6750 | 0.6483 | 0.6352 | 0.6331 | 0.6352 | 0.9432 |
| ResNet101 | Sobel | 0.5571 | 0.5041 | 0.5155 | 0.5033 | 0.5155 | 0.9010 |
| DenseNet121 | No Filter | 0.6750 | 0.6490 | 0.6124 | 0.6152 | 0.6124 | 0.9507 |
| DenseNet121 | Average | 0.7321 | 0.6662 | 0.6548 | 0.6526 | 0.6548 | 0.9491 |
| DenseNet121 | Gaussian | 0.7000 | 0.6326 | 0.6275 | 0.6262 | 0.6275 | 0.9506 |
| DenseNet121 | Median | 0.6857 | 0.6092 | 0.6027 | 0.5998 | 0.6027 | 0.9465 |
| DenseNet121 | Sharpening | 0.7357 | 0.6626 | 0.6551 | 0.6535 | 0.6551 | 0.9551 |
| DenseNet121 | Sobel | 0.5536 | 0.4713 | 0.4877 | 0.4759 | 0.4877 | 0.8999 |
| ResNet50 | No Filter | 0.6964 | 0.6427 | 0.6479 | 0.6431 | 0.6479 | 0.9457 |
| ResNet50 | Average | 0.7107 | 0.6652 | 0.6501 | 0.6408 | 0.6501 | 0.9363 |
| ResNet50 | Gaussian | 0.6964 | 0.6356 | 0.6511 | 0.6378 | 0.6511 | 0.9398 |
| ResNet50 | Median | 0.7286 | 0.6560 | 0.6689 | 0.6575 | 0.6689 | 0.9474 |
| ResNet50 | Sharpening | 0.7714 | 0.7027 | 0.7045 | 0.6924 | 0.7045 | 0.9508 |
| ResNet50 | Sobel | 0.5536 | 0.5100 | 0.5014 | 0.4851 | 0.5014 | 0.8752 |

The best single result overall was **ResNet50 with Sharpening**, reaching
77.14 percent accuracy and 0.6924 macro F1 score. This is the highest score
in the whole table.

## Main Findings

### 1. Sobel filter is bad for all 3 models

Sobel gave the worst result every single time. It dropped macro F1 by about
0.14 to 0.16 points for all 3 models, which is a huge drop. This makes sense
because Sobel only keeps the edges of the image. It throws away all the color
and texture information. Skin lesions are told apart a lot by their color and
texture, not just their outline, so removing that information hurts the model
a lot.

### 2. Sharpening and Average filter usually help, but not always

For DenseNet121 and ResNet50, both Sharpening and Average filter improved the
macro F1 score compared to no filter. Sharpening gave the biggest boost for
ResNet50, about plus 0.049 points.

But for ResNet101, Sharpening actually made results worse, dropping macro F1
by 0.024 points. So sharpening does not help every model the same way.

### 3. Gaussian and Median filters gave mixed results

These smoothing filters sometimes helped a tiny bit and sometimes hurt a tiny
bit, but the change was always small, usually less than 0.02 points either
way. Compared to Sobel, this is a very small effect.

### 4. The effect of a filter is not the same for every model

Looking at the same filter across all 3 models shows the effect is not
consistent:

- Average helped ResNet101 (+0.026) and DenseNet121 (+0.037), but very
  slightly hurt ResNet50 (-0.002).
- Sharpening helped DenseNet121 (+0.038) and ResNet50 (+0.049) a lot, but hurt
  ResNet101 (-0.024).
- Median helped ResNet50 (+0.014) but hurt ResNet101 (-0.020) and
  DenseNet121 (-0.015).

So there is no single filter that is best for every model. Each model reacts
a bit differently to the same filter.

### 5. Which classes are affected most

Looking at the per class results, the rare classes like seborrheic keratosis,
dermatofibroma, and vascular lesion (the ones with fewer training images) had
the biggest swings in F1 score between filters. The common classes like nevus
and melanoma stayed more stable across filters. This makes sense because
models trained on fewer images are less sure about those classes, so any
change to the input, like a filter, pushes their predictions around more
easily.

## Answers to Key Questions

**Q1: Which three models performed best in Lab 1?**
ResNet101, DenseNet121, and ResNet50, based on their accuracy scores from
Lab 01.

**Q2: How does filtering affect each model?**
Filtering can help or hurt depending on the model and the filter. Average and
Sharpening filters helped DenseNet121 and ResNet50 the most. ResNet101 did
best with Average but got worse with Sharpening. All models got much worse
with Sobel.

**Q3: Which filter causes the biggest change from baseline?**
Sobel, by a wide margin. It dropped performance for all 3 models by around 14
to 16 percentage points in macro F1, way more than any other filter.

**Q4: Is the filter effect the same across all models?**
No. As shown above, Sharpening and Average and Median all had different
effects (sometimes positive, sometimes negative) depending on which model
they were used with. Only Sobel was consistently bad for all 3 models.

**Q5: Does filtering improve or decrease macro F1 and balanced accuracy?**
It depends on the filter. Sobel always decreases both scores by a lot.
Average and Sharpening usually increase both scores, except for a couple of
small exceptions noted above. Gaussian and Median give small, mixed changes.

**Q6: Which lesion classes are most affected by filtering?**
The rare classes with fewer training images (like seborrheic keratosis,
dermatofibroma, and vascular lesion) show the biggest swings when the filter
changes. Common classes like nevus and melanoma stay more stable.

**Q7: Why might smoothing remove useful information?**
Filters like Average, Gaussian, and Median work by blurring the image. This
removes small details like the sharp edges of a lesion, fine textures, and
tiny color changes. These small details are often exactly what doctors use to
tell one type of lesion from another, so blurring can throw away useful
clues.

**Q8: Why might sharpening or edge detection help or hurt?**
Sharpening makes edges and borders stand out more, which can help the model
notice the shape of a lesion better. But sharpening can also make noise and
tiny artifacts (like hair or skin marks) stand out too, which can confuse the
model. Sobel only keeps edges and throws away all color and texture, which is
too much information loss for this task, so it always hurt results badly.

**Q9: What is the difference between convolution and correlation?**
Convolution flips the filter kernel both left-right and top-bottom before
sliding it across the image. Correlation just slides the kernel across the
image without flipping it. If the kernel is symmetric, like a Gaussian
kernel, both methods give the same result. If the kernel is not symmetric,
like a Sobel kernel, they give different results. Most deep learning
libraries, including the ones used to build these models, actually do
correlation but still call it convolution, since the network learns its own
kernel values anyway so the flip does not matter for learning.

**Q10: What is the relationship between classical filtering and deep
learning feature extraction?**
Classical filters like the ones we tested are fixed and hand made, they do
the exact same operation on every image no matter what. Deep learning models
like CNNs learn their own filters from the data, adjusting them during
training to pick out whatever features actually help with classification.
Our results show that adding a classical filter before the CNN can help or
hurt, depending on whether that filter removes noise the CNN did not need, or
removes signal the CNN was actually using. This shows classical filtering and
deep learning are not competitors, they can work together, but only if the
classical filter is chosen carefully for the task.

## Conclusion

Out of all 18 combinations tested (3 models times 6 filter settings), the
best result was **ResNet50 with the Sharpening filter**, reaching 77.14
percent accuracy. Sobel was clearly the worst choice for every model, since
it throws away color and texture that the models rely on. Average and
Sharpening filters were generally helpful, but not for every model, showing
that filter choice should be tested per model rather than assumed to work the
same way everywhere.
