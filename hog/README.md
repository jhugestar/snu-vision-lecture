# HOG Dissected — an interactive lecture demo

**[Live Demo](https://jhugestar.github.io/snu-vision-lecture/hog/hog.html)** · [More Demos](https://github.com/jhugestar/snu-vision-lecture)

**[Hanbyul Joo](https://jhugestar.github.io/) / SNU Visual Computing Lab**

Everything is in the single file `hog.html`. Double-click it to open in a browser.
No server, build step or internet connection is needed (web fonts apply only when online).

## Tabs

| Tab | What it shows | How to use it in class |
|---|---|---|
| 01 Pipeline | original → grayscale → Gx/Gy → magnitude & orientation → cell histograms → block normalization | Change cell size, bin count and block size and watch every stage update. Hover over an image to open that cell in the inspector: per-pixel gradients, the orientation votes, and the block vector before/after normalization |
| 02 Descriptor | dimension formula, the full vector, an illumination experiment | Shows *why* block normalization matters, with numbers. Lower the contrast gain: the unnormalized histograms change wholesale while HOG barely moves |
| 03 Detection | a logistic-regression detector trained in the browser on synthetic pedestrians → sliding window → NMS | Highlights: a person's outline emerging in the learned weights, and false positives flooding in as the threshold drops |
| 04 Summary | the six steps, the paper's default parameters, frequently asked questions | A recap to leave on screen instead of the whiteboard |

## Suggested 50-minute session

1. **10 min** — Tab 01: push cell size up to 20px, then down to 4px, to build intuition for spatial resolution vs. noise.
2. **10 min** — Hover over any cell and follow inspector panels A → B → C: vote → concatenate → normalize.
3. **10 min** — Tab 02: drop contrast gain to 0.2 and compare the three bars. Then set normalization to *None* in Tab 01 and watch the invariance disappear.
4. **15 min** — Tab 03: run ①–④ in order. Explain the weights, then the heatmap, then NMS.
5. **5 min** — Tab 04 to wrap up.

## Implementation notes

- Every computation (gradients, histograms, block normalization, training, detection) is written in plain JS with no libraries, so you can walk through the code in class. The "1. HOG core" section of `hog.html` is the whole algorithm.
- Lines in the rose plots are drawn along the **edge direction θ+90°**, not the gradient direction θ, following the usual visualization convention.
- *Flat-block suppression ε* sets the ε in `v / √(‖v‖²+ε²)` as a fraction of the image's mean block norm. The original paper uses ε≈0, but at 0 the 8-bit quantization noise in flat regions (like the sky) gets amplified by normalization and swamps both the picture and the invariance experiment. The slider itself makes a good discussion point.
- The detector uses logistic regression (SGD) instead of an SVM. It is still linear, so the explanation carries over, and it trains in a few seconds in the browser. After the first round it collects high-scoring windows from person-free backgrounds and retrains (**hard negative mining**).
