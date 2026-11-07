# Chapter 10: Model Training and Optimisation

## Contents

- [Learning Objectives](#learning-objectives)
- [1. The Goal of Training](#1-the-goal-of-training)
- [2. Loss Functions](#2-loss-functions)
- [3. Gradient Descent](#3-gradient-descent)
- [4. Optimisers](#4-optimisers)
- [5. The Learning Rate](#5-the-learning-rate)
- [6. Batch Size](#6-batch-size)
- [7. Overfitting and Underfitting](#7-overfitting-and-underfitting)
- [8. Regularisation Toolbox](#8-regularisation-toolbox)
- [9. Normalisation, Initialisation and Stability](#9-normalisation-initialisation-and-stability)
- [10. Hyperparameter Tuning](#10-hyperparameter-tuning)
- [11. A Complete Training Loop (PyTorch)](#11-a-complete-training-loop-pytorch)
- [12. Debugging Checklist](#12-debugging-checklist)
- [13. Efficiency and Practical Tricks](#13-efficiency-and-practical-tricks)
- [14. Common Mistakes](#14-common-mistakes)
- [Key Takeaways](#key-takeaways)
- [Practice Questions](#practice-questions)
- [Answer Key](#answer-key)
- [15. 🎓 Course Wrap-Up](#15--course-wrap-up)

---

## 1. The Goal of Training

Find parameters (weights and biases) that **minimise a loss function** on training data while **generalising** to new data. Training too well on the training set is not success. Performance on unseen data is.

### 1.1 Data Splits

| Set | Purpose | Typical share |
|---|---|---|
| **Training** | Fit the weights | 60–80% |
| **Validation** | Tune hyperparameters, choose when to stop, compare models | 10–20% |
| **Test** | One final, honest estimate of performance | 10–20% |

Rules:
- **Never tune on the test set.** Otherwise it stops being an unbiased estimate.
- **Time-ordered data** (Chapter 5): split chronologically.
- **Grouped data** (several rows from the same customer): keep each group in one split.
- **Imbalanced classes:** use **stratified** splits.
- **Cross-validation** (k-fold) gives more reliable estimates on small datasets.
- Prevent **data leakage**: fit scalers and encoders on training data only.

![Training fits weights, validation chooses settings, and the test set evaluates the chosen model.](../../../Assets/Connectionist%20AI/Static/dataset-split.svg)

## 2. Loss Functions

| Task | Loss | Idea |
|---|---|---|
| Regression | **MSE** | mean((y − ŷ)²). Punishes big errors, sensitive to outliers |
| Regression | **MAE** | mean(\|y − ŷ\|). Robust to outliers |
| Regression | **Huber** | MSE for small errors, MAE for big ones |
| Binary classification | **Binary cross-entropy** | −[y·ln ŷ + (1−y)·ln(1−ŷ)] |
| Multi-class | **Categorical cross-entropy** | −Σ y_i·ln ŷ_i |
| Probabilistic forecast | **NLL / pinball** | Chapter 6 |
| Ranking | **BPR / contrastive** | Chapter 8 |
| Imbalanced classification | **Weighted CE / focal loss** | Emphasise rare or hard cases |

**Cross-entropy values:** correct-class probability 0.99 → loss 0.010; 0.7 → 0.357; 0.1 → 2.303. Confident wrong predictions cost the most.

**Sanity check:** with `C` balanced classes and random initial weights, the starting cross-entropy should be about `ln(C)`. For 10 classes, ln(10) ≈ **2.30**. If you see much more, something is wrong.

**Loss vs metric:** you *train* on a differentiable loss but *evaluate* with a metric that matters for the task (accuracy, F1, WAPE, NDCG). They can disagree, so monitor both.

![Cross-entropy for a correct class equals minus log of its assigned probability. The chapter examples are 0.99 to 0.010, 0.7 to 0.357, and 0.1 to 2.303.](../../../Assets/Connectionist%20AI/Static/train-cross-entropy.svg)

## 3. Gradient Descent

The **gradient** points in the direction of steepest increase of the loss. We step the opposite way:

```
w ← w − η · ∂L/∂w            (η = learning rate)
```

**Worked example.** w = 2, gradient = 4, η = 0.1 → w = 2 − 0.4 = **1.6**.
If the gradient were −2 with w = 5, η = 0.5 → w = 5 + 1 = **6**.

**Landscape intuition.** Imagine walking downhill in fog. The gradient is the slope under your feet; the learning rate is your step size. Too big a step overshoots the valley; too small takes forever.

![Successive gradient descent steps approach the minimum of a toy squared loss.](../../../Assets/Connectionist%20AI/Animated/descent-steps.svg)

### 3.1 Backpropagation
Computes the gradient for **every** weight efficiently using the chain rule, working backwards from the loss. Frameworks do this automatically (`loss.backward()`).

![Backward gradients follow the forward dependencies in reverse.](../../../Assets/Connectionist%20AI/Animated/chain-rule-path.svg)

### 3.2 Variants

| Variant | Data per update | Notes |
|---|---|---|
| **Batch GD** | All data | Stable, but slow and memory heavy |
| **Stochastic GD (SGD)** | 1 sample | Very noisy, fast updates |
| **Mini-batch GD** | e.g. 32–512 samples | **The standard.** Good speed, noise and hardware use |

### 3.3 Challenges in the Loss Landscape
- **Local minima:** in high dimensions, most points where the gradient is zero are **saddle points** or flat regions rather than bad minima.
- **Plateaus:** gradients near zero.
- **Ravines:** steep in one direction, shallow in another, so SGD zigzags (momentum helps).
- **Noise from mini-batches** is not entirely bad. It helps escape sharp minima and can improve generalisation.

## 4. Optimisers

### 4.1 SGD with Momentum
Accumulates a **velocity** of past gradients, which smooths zigzags and speeds movement along consistent directions.
```
v ← β·v + g
w ← w − η·v         (β ≈ 0.9)
```
**Example** (constant gradient g = 1, η = 0.1, β = 0.9):
- Step 1: v = 1 → move 0.1
- Step 2: v = 1.9 → move 0.19
- Step 3: v = 2.71 → move 0.271

Steps grow while the direction stays consistent.
**Nesterov momentum** looks ahead before computing the gradient.

![With constant gradient one, beta 0.9 and learning rate 0.1, the three update sizes are 0.1,0.19,0.271. They grow as velocity accumulates.](../../../Assets/Connectionist%20AI/Static/train-momentum.svg)

### 4.2 AdaGrad, RMSprop
Give each parameter its **own step size**, shrinking it where gradients have been large.
RMSprop keeps a moving average of squared gradients (good for RNNs).

### 4.3 Adam
Combines **momentum** (average of gradients, `m`) with **adaptive scaling** (average of squared gradients, `v`):
```
m ← β1·m + (1−β1)·g
v ← β2·v + (1−β2)·g²
m̂ = m/(1−β1^t),  v̂ = v/(1−β2^t)        (bias correction)
w ← w − η · m̂ / (√v̂ + ε)
```
Defaults: **η = 1e-3, β1 = 0.9, β2 = 0.999, ε = 1e-8**.
Fun fact: on the very first step the update size is about **η** regardless of the gradient's scale (since m̂/√v̂ ≈ ±1).

![The same gradient contributes to a first-moment average and a second-moment average of squared gradients. Bias-corrected estimates combine to scale the update.](../../../Assets/Connectionist%20AI/Static/train-adam.svg)

### 4.4 AdamW
Adam with **decoupled weight decay**, the usual choice for Transformers and many modern models.

| Optimiser | Strengths | Watch out for |
|---|---|---|
| **SGD + momentum** | Often best final generalisation (vision) | Needs careful learning rate and schedule |
| **Adam** | Fast, robust default | Sometimes slightly worse generalisation |
| **AdamW** | Proper weight decay | — |
| **RMSprop** | Good for RNNs, RL | — |
| **LAMB / LARS** | Very large batch training | Specialised |

> **Rule of thumb:** start with **AdamW, lr = 1e-3** (or 3e-4 for Transformers) and tune from there.

## 5. The Learning Rate

The most important hyperparameter.

| Learning rate | Typical symptom |
|---|---|
| Too low | Loss barely moves; very slow |
| Good | Steady decrease |
| Too high | Loss oscillates, spikes, or becomes **NaN** |

**Finding it:** the **learning-rate range test** increases the rate exponentially over a short run; pick a value somewhat below where the loss starts to rise sharply. Often tried: 1e-1, 1e-2, 1e-3, 1e-4.

![On this toy quadratic, rate 0.02 converges slowly, 0.2 converges quickly, and 1.005 diverges. All losses are divided by the starting loss.](../../../Assets/Connectionist%20AI/Static/train-learning-rate.svg)

### 5.1 Schedules

| Schedule | Idea |
|---|---|
| **Constant** | Simple baseline |
| **Step decay** | Multiply by e.g. 0.1 every N epochs |
| **Exponential decay** | Smooth shrink each epoch |
| **Cosine annealing** | Smoothly follows a cosine curve to near zero |
| **Warm-up** | Start tiny, ramp up over the first steps (stabilises Transformers and Adam) |
| **ReduceLROnPlateau** | Cut the rate when validation loss stalls |
| **One-cycle** | Ramp up then down in one run; often very fast training |

```python
opt = torch.optim.AdamW(model.parameters(), lr=1e-3, weight_decay=1e-2)
sched = torch.optim.lr_scheduler.CosineAnnealingLR(opt, T_max=50)
# call sched.step() once per epoch
```

![Illustrative schedules compare step decay, cosine annealing, and linear warm-up followed by cosine decay. The vertical axis is normalized to each schedule's peak.](../../../Assets/Connectionist%20AI/Static/train-lr-schedules.svg)

## 6. Batch Size

| Small batch (8–64) | Large batch (512+) |
|---|---|
| Noisier gradients (can regularise) | Smoother, accurate gradients |
| More updates per epoch | Faster per epoch on GPUs |
| Less memory | More memory |
| Often generalises a bit better | May need larger learning rate and warm-up |

A common heuristic is to scale the learning rate with batch size. **Gradient accumulation** simulates a large batch by summing gradients over several small batches before one update.

![Four micro-batches produce four gradient contributions. These are averaged before one optimizer update, giving an effective batch of sixty-four for four micro-batches of sixteen.](../../../Assets/Connectionist%20AI/Static/train-accumulation.svg)

## 7. Overfitting and Underfitting

| | Training error | Validation error | What to do |
|---|---|---|---|
| **Underfitting (high bias)** | High | High | Bigger model, train longer, better features, lower regularisation |
| **Good fit** | Low | Low and close | — |
| **Overfitting (high variance)** | Very low | High | More data, regularise, simpler model, early stopping |

**Learning curves:** plot training and validation loss against epochs (or data size). A widening gap signals overfitting; both stuck high signals underfitting.

**Bias–variance trade-off:** simple models are stable but may miss patterns (bias); complex models fit anything but are sensitive to the training sample (variance). Modern deep networks with enough data and regularisation often sit well in the "very large but regularised" regime.

![Illustrative training loss continues falling while validation loss reaches its lowest point and then rises. The lowest-validation checkpoint is marked.](../../../Assets/Connectionist%20AI/Static/train-overfitting.svg)

## 8. Regularisation Toolbox

| Technique | How it helps |
|---|---|
| **More data** | Often the most effective regulariser |
| **Data augmentation** | Create varied examples: flips, crops, noise, time-warp, mixup |
| **L2 / weight decay** | Penalises large weights → smoother functions |
| **L1** | Pushes weights to exactly zero (sparse models) |
| **Dropout** | Randomly zeroes a fraction (e.g. 20–50%) of units during training, preventing co-dependence. Disabled at inference (`model.eval()`) |
| **Early stopping** | Stop when validation loss stops improving; keep the best checkpoint |
| **Smaller / simpler model** | Fewer parameters to memorise with |
| **Label smoothing** | Soften targets (e.g. 0.9 / 0.1) to reduce overconfidence |
| **Ensembling** | Average several models |
| **Noise injection** | Add noise to inputs or weights |
| **Transfer learning** | Start from a pre-trained network |

**L2 regularised loss:** `L_total = L_data + λ · Σ w²`.
**Dropout detail:** with rate p, surviving activations are scaled by 1/(1−p) during training ("inverted dropout") so inference needs no change.

![The training mask alternates between two example subsets of zeroed units. The evaluation layer uses all units. Surviving units are scaled appropriately during inverted dropout training.](../../../Assets/Connectionist%20AI/Animated/train-dropout.svg)

## 9. Normalisation, Initialisation and Stability

### 9.1 Input Scaling
Standardise features (mean 0, std 1), or scale to [0, 1]. It speeds training and avoids features with large ranges dominating. Use **training-set statistics only**.

### 9.2 Normalisation Layers
| Layer | Normalises over | Typical use |
|---|---|---|
| **Batch Norm** | The batch, per feature/channel | CNNs |
| **Layer Norm** | Features within one sample | Transformers, RNNs |
| **Group / Instance Norm** | Groups of channels | Small batches, style tasks |

Benefits: faster, more stable training; allows higher learning rates; mild regularisation.
Remember: Batch Norm behaves differently in `train()` and `eval()` mode.

![In the left sample-by-feature matrix, batch normalization uses values down one feature column. In the right matrix, layer normalization uses the features across one sample row.](../../../Assets/Connectionist%20AI/Static/train-normalisation-axes.svg)

### 9.3 Weight Initialisation
- **Never all zeros** (symmetry: every neuron learns the same thing).
- **Xavier/Glorot** for tanh/sigmoid; **He** for ReLU-family.
- Frameworks default to sensible choices, but custom layers need care.

![Identical incoming weights can preserve identical behavior when updates are the same.](../../../Assets/Connectionist%20AI/Static/initialisation-symmetry.svg)

### 9.4 Gradient Clipping
Cap the gradient norm (e.g. 1.0) to stop exploding gradients:
```python
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
```

![Norm clipping scales a gradient vector while keeping its direction.](../../../Assets/Connectionist%20AI/Static/gradient-clipping.svg)

### 9.5 Residual Connections
`output = layer(x) + x` lets gradients flow through very deep networks (Chapter 4).

![The input passes through a transformation and also reaches the output by an identity path.](../../../Assets/Connectionist%20AI/Animated/residual-path.svg)

## 10. Hyperparameter Tuning

Typical hyperparameters: learning rate, batch size, depth and width, dropout rate, weight decay, optimiser, schedule, augmentation strength.

| Method | Notes |
|---|---|
| **Manual** | Start here; use intuition, change one thing at a time |
| **Grid search** | Try all combinations. Costly, wasteful for many parameters |
| **Random search** | Often **more efficient** than grid: tries more distinct values of the important parameters |
| **Bayesian optimisation** (Optuna, Ray Tune) | Uses past trials to choose promising settings |
| **Successive halving / Hyperband** | Stop poor runs early |
| **Population-based training** | Evolve hyperparameters during training |

![Grid search tests a regular set of combinations. Random search spreads a similar number of trials over distinct settings, with learning rate and decay represented on log scales.](../../../Assets/Connectionist%20AI/Static/train-hyperparameter-search.svg)

**Tips**
- Tune on a **log scale** for learning rate and weight decay (e.g. 1e-5 to 1e-1).
- Tune the **learning rate first**, then batch size, then regularisation.
- Use a **small, fast version** of the problem for initial sweeps.
- Fix random seeds when comparing, and repeat with several seeds for important decisions.

## 11. A Complete Training Loop (PyTorch)

```python
import torch, torch.nn as nn

model = nn.Sequential(nn.Linear(10, 32), nn.ReLU(),
                      nn.Dropout(0.2), nn.Linear(32, 1))
loss_fn = nn.MSELoss()
opt = torch.optim.AdamW(model.parameters(), lr=1e-3, weight_decay=1e-2)
sched = torch.optim.lr_scheduler.ReduceLROnPlateau(opt, factor=0.5, patience=3)

best_val, patience, wait = float("inf"), 8, 0
for epoch in range(200):
    # ---- training ----
    model.train()
    for X, y in train_loader:
        opt.zero_grad()
        loss = loss_fn(model(X), y)
        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
        opt.step()

    # ---- validation ----
    model.eval()
    with torch.no_grad():
        val_loss = sum(loss_fn(model(X), y).item() for X, y in val_loader) / len(val_loader)
    sched.step(val_loss)

    # ---- early stopping + checkpoint ----
    if val_loss < best_val:
        best_val, wait = val_loss, 0
        torch.save(model.state_dict(), "best.pt")
    else:
        wait += 1
        if wait >= patience:
            break

model.load_state_dict(torch.load("best.pt"))
```

![Each batch clears old gradients, runs a forward pass, computes gradients, and updates parameters. After each epoch, validation decides whether to save a checkpoint or stop.](../../../Assets/Connectionist%20AI/Animated/train-loop.svg)

## 12. Debugging Checklist

1. **Overfit one tiny batch** (e.g. 10 samples). A healthy model reaches near-zero loss. If not, there is a bug.
2. **Check the data pipeline:** shapes, label alignment, scaling, no leakage. Look at a few real samples.
3. **Check initial loss** against theory (e.g. ln(C) for classification).
4. **Plot** training and validation loss (and metrics) every epoch.
5. **Loss is NaN or inf:** lower the learning rate, clip gradients, check for division by zero or log(0), inspect the data for NaNs.
6. **Loss not decreasing:** wrong learning rate, forgot `zero_grad()`, frozen layers, bad labels, dead ReLUs, or too much regularisation.
7. **Training good, validation bad:** overfitting, or train/validation distribution mismatch.
8. **Both bad:** underfitting, weak features, or too short training.
9. **Validation better than training?** Dropout on during training, or leakage.
10. **Change one thing at a time** and log every run.
11. **Compare to a simple baseline** (Chapter 5) to know if the model is adding value.

![If a model cannot overfit a tiny training batch, inspect the implementation and data. If it can, compare full training and validation behavior to investigate underfitting, overfitting, or data mismatch.](../../../Assets/Connectionist%20AI/Static/train-debug-flow.svg)

## 13. Efficiency and Practical Tricks

- **GPU / TPU** training; keep data loading from becoming the bottleneck (`num_workers`, prefetching).
- **Mixed precision** (float16 / bfloat16): faster and lighter on memory.
- **Gradient accumulation** for large effective batches.
- **Gradient checkpointing:** trade compute for memory in very deep models.
- **Transfer learning / fine-tuning:** start from a pre-trained model, often the biggest win with limited data. Freeze early layers first, then optionally unfreeze with a small learning rate.
- **Parameter-efficient fine-tuning (e.g. LoRA):** train a few extra parameters instead of the whole model.
- **Checkpointing:** save regularly so you can resume.
- **Reproducibility:** set seeds, record library versions, config and data version.
- **Experiment tracking:** TensorBoard, Weights & Biases, MLflow.
- **Model compression for deployment:** pruning, quantisation, distillation.

![A pretrained feature extractor can be frozen while a new task head is trained. A later fine-tuning phase may unfreeze selected layers at a smaller learning rate.](../../../Assets/Connectionist%20AI/Static/train-transfer-freeze.svg)

## 14. Common Mistakes
- Tuning on the test set
- Leakage from scaling or splitting incorrectly
- Forgetting `model.eval()` at validation or inference
- Comparing models using a single random seed
- Changing many things at once
- Training far longer than validation justifies
- Not saving the best checkpoint

## Key Takeaways
- Training minimises a loss with gradient descent, using backpropagation for gradients.
- **AdamW** is a strong default; the **learning rate** matters most, and schedules help.
- Combat overfitting with data, augmentation, weight decay, dropout, early stopping.
- Normalisation, good initialisation, clipping and residual connections keep training stable.
- Debug methodically: tiny-batch overfit, loss curves, baselines, one change at a time.

---

## Practice Questions

1. One gradient step: w = 5, gradient = −2, η = 0.5?
2. Batch vs stochastic vs mini-batch GD?
3. Training loss 0.02, validation loss 0.45. Diagnosis and three fixes?
4. What happens if the learning rate is too high? Too low?
5. Validation set vs test set?
6. How does dropout reduce overfitting?
7. Why not initialise all weights to zero?
8. Expected starting cross-entropy for 10 balanced classes?
9. What is early stopping?
10. Name two benefits of transfer learning.
11. Typical Adam default learning rate?
12. Momentum: v = 0, β = 0.9, g = 1. What are v after steps 1 and 2?

---

## Answer Key

1. 5 − 0.5×(−2) = **6**.
2. Batch uses all data per update; stochastic uses 1 sample; mini-batch uses a small group (most common).
3. Overfitting. Fixes: more data/augmentation, dropout, weight decay, smaller model, early stopping.
4. Too high: oscillation or divergence (NaN). Too low: very slow or stalled training.
5. Validation tunes and selects; test gives a final unbiased estimate.
6. It randomly disables units so the network can't rely on specific ones, learning more robust features.
7. All neurons would be identical and learn the same thing (symmetry).
8. About ln(10) ≈ **2.3**.
9. Stop when validation loss stops improving, keeping the best checkpoint.
10. Needs less data and training time, and often reaches higher accuracy.
11. **1e-3**.
12. Step 1: v = 1. Step 2: v = 0.9×1 + 1 = **1.9**.

---

## 15. 🎓 Course Wrap-Up

You have now covered the connectionist AI journey: what it is (Ch 1), how networks work (Ch 2), where they're used (Ch 3), depth and activations (Ch 4), forecasting (Ch 5–6), learning from streams (Ch 7), recommendations (Ch 8), reinforcement learning (Ch 9), and how to train well (Ch 10).

**Suggested next steps**
1. Train a small MLP on a tabular dataset and compare it with gradient boosting.
2. Forecast a public time series, beating a seasonal naive baseline and checking interval coverage.
3. Implement the corridor Q-learning, then try a Gymnasium environment with PPO.
4. Build a matrix-factorisation recommender on a small ratings dataset.

Practising on real data is the fastest way to make these ideas stick.

# Reference
00, C., & Snyder, R. M. (n.d.).  
1996 ASCUE Proceedings Neural Networks for the Beginner.

98352720375fb469dd015e007e1aef55. (n.d.).

Adhikari, R., & Agrawal, R. K. (n.d.). An Introductory Study on Time Series Modeling and Forecasting.

Adorf, H.-M. (n.d.). 3 Connectionism and Neural Networks.

Benidis, K., Rangapuram, S. S., Flunkert, V., Wang, Y., Maddix, D., Turkmen, C., Gasthaus, J., Bohlke-Schneider, M., Salinas, D., Stella, L., Aubet, F. X., Callot, L., & Januschowski, T. (2023). Deep Learning for Time Series Forecasting: Tutorial and Literature Survey. ACM Computing Surveys, 55(6).  
https://doi.org/10.1145/3533382

Bhattacherjee, S., Chavan, A., Huang, S., Deshpande, A., & Parameswaran, A. (2015). Principles of dataset versioning: Exploring the recreation/storage tradeoff. In Proceedings of the VLDB Endowment (Vol. 8, Number 12 12, pp. 1346–1357). Association for Computing Machinery.  
https://doi.org/10.14778/2824032.2824035

Bird, R. (1969). Kennedy, K. Variable subsumption with constant folding. SETL York, Feb. 1974. 10. Kennedy, K. Use-definition chains with applications. In Int. J. Comptr. Math., Sect. A (Vol. 5, Number 2). ACM.

Brodie, M., Ma, S., Rachevsky, L., & Champlin, J. (2005). Automated problem determination using call-stack matching. Journal of Network and Systems Management, 13(2), 219–237.  
https://doi.org/10.1007/s10922-005-4443-8

Chen, Y., Kang, Y., Chen, Y., & Wang, Z. (2020). Probabilistic Forecasting with Temporal Convolutional Neural Network.  
http://arxiv.org/abs/1906.04397

Chivers, I., & Sleightholme, J. (2018). An Introduction to Algorithms and the Big O Notation. In Introduction to Programming with Fortran (pp. 391–396). Springer International Publishing.  
https://doi.org/10.1007/978-3-319-75502-1_23

COSY : computer conferencing system : user’s guide, version 3.1. (1985). Institute of Computer Science, University of Guelph.

Curtis, S. A. (2003). The classification of greedy algorithms. Science of Computer Programming, 49(1–3), 125–157.  
https://doi.org/10.1016/j.scico.2003.09.001

Design of an AI-Based Multi-Sensor Self-Diagnostic Chip for Industrial Sensor Health Monitoring. (n.d.).

Eddy, S. R. (n.d.). PRIMER What is (dynamic programming?

Elsayed, S. M., Sarker, R. A., & Essam, D. L. (2014). A new genetic algorithm for solving optimization problems. Engineering Applications of Artificial Intelligence, 27, 57–69.  
https://doi.org/10.1016/j.engappai.2013.09.013

Everitt, T., & Hutter, M. (2015). Analytical Results on the BFS vs. DFS Algorithm Selection Problem. Part II: Graph Search *.  
http://link.springer.com

Felzenszwalb, P. F., & Zabih, R. (2011). Dynamic programming and graph algorithms in computer vision. IEEE Transactions on Pattern Analysis and Machine Intelligence, 33(4), 721–740.  
https://doi.org/10.1109/TPAMI.2010.135

François-Lavet, V., Henderson, P., Islam, R., Bellemare, M. G., & Pineau, J. (2018). An introduction to deep reinforcement learning. Foundations and Trends in Machine Learning, 11(3–4), 219–354.  
https://doi.org/10.1561/2200000071

Friedman, H. (1971). Algorithmic procedures, generalized turing algorithms, and elementary recursion theory. Studies in Logic and the Foundations of Mathematics, 61(C), 361–389.  
https://doi.org/10.1016/S0049-237X(08)71238-2

Gallego, G., Katircioglu, K., & Ramachandran, B. (2007). Inventory management under highly uncertain demand. Operations Research Letters, 35(3), 281–289.  
https://doi.org/10.1016/j.orl.2006.03.012

García, A. (2025). Greedy algorithms: a review and open problems. In Journal of Inequalities and Applications (Vol. 2025, Number 1). Springer Nature.  
https://doi.org/10.1186/s13660-025-03254-1

Gneiting, T., & Katzfuss, M. (2014). Probabilistic forecasting. Annual Review of Statistics and Its Application, 1, 125–151.  
https://doi.org/10.1146/annurev-statistics-062713-085831

Guest, O., & Martin, A. E. (2026). A metatheory of classical and modern connectionism. Psychological Review, 133(3), 719–736.  
https://doi.org/10.1037/rev0000591

Haupt, S. E., Garcia Casado, M., Davidson, M., Dobschinski, J., Du, P., Lange, M., Miller, T., Mohrlen, C., Motley, A., Pestana, R., & Zack, J. (2019). The use of probabilistic forecasts: Applying them in theory and practice. IEEE Power and Energy Magazine, 17(6), 46–57.  
https://doi.org/10.1109/MPE.2019.2932639

Hill, R. K. (2016). What an Algorithm Is. Philosophy and Technology, 29(1), 35–59.  
https://doi.org/10.1007/s13347-014-0184-5

Ingle, C., Bakliwal, D., Jain, J., Singh, P., Kale, P., & Chhajed, V. (2021). Demand Forecasting: Literature Review On Various Methodologies. 2021 12th International Conference on Computing Communication and Networking Technologies, ICCCNT 2021.  
https://doi.org/10.1109/ICCCNT51525.2021.9580139

Introduction to Connectionist AI. (n.d.).

Isinkaye, F. O., Folajimi, Y. O., & Ojokoh, B. A. (2015). Recommendation systems: Principles, methods and evaluation. In Egyptian Informatics Journal (Vol. 16, Number 3, pp. 261–273). Elsevier B.V.  
https://doi.org/10.1016/j.eij.2015.06.005

Iyanda, J. (n.d.). Title : A Comparative Analysis of Breadth First Search (BFS) and Depth First Search (DFS) Algorithms.

Khuller, S., & Raghavachari, B. (n.d.). 7 Basic Graph Algorithms (Vol. 1).

Klump, J., Wyborn, L., Wu, M., Martin, J., Downs, R. R., & Asmi, A. (2021). Versioning data is about more than revisions: A conceptual framework and proposed principles. Data Science Journal, 20(1).  
https://doi.org/10.5334/dsj-2021-012

Klump, J., Wyborn, L., Wu, M., Strecker, D., Pampel, H., & Rothfritz, L. (2026). What Do We Mean by ‘Version’? Actionable Guidelines for the Versioning of Data. Data Science Journal, 25(1).  
https://doi.org/10.5334/dsj-2026-037

Kosasih, E. E., & Brintrup, A. (2025). Towards trustworthy AI for link prediction in supply chain knowledge graph: a neurosymbolic reasoning approach. International Journal of Production Research, 63(6), 2268–2290.  
https://doi.org/10.1080/00207543.2024.2399713

Mala, F. A., & Ali, R. (2022). The Big-O of Mathematics and Computer Science. Journal of Applied Mathematics and Computation, 6(1), 1–3.  
https://doi.org/10.26855/jamc.2022.03.001

Malik, A., Sharma, A., & Saroha, V. (2013). Greedy Algorithm. International Journal of Scientific and Research Publications, 3(8).  
www.ijsrp.org

Masini, R. P., Medeiros, M. C., & Mendes, E. F. (2021). Machine Learning Advances for Time Series Forecasting.  
http://arxiv.org/abs/2012.12802

Mcmaster, S., & Memon, A. M. (n.d.). Call Stack Coverage for Test Suite Reduction.

Morozov, A. V., Levkivskyi, V. L., & Plechystyy, D. D. (2024). ACTIVATION FUNCTIONS IN NEURAL NETWORKS: OVERVIEW AND COMPARISON. Scientific Notes of Taurida National V.I. Vernadsky University. Series: Technical Sciences, 1(3), 144–151.  
https://doi.org/10.32782/2663-5941/2024.3.1/22

Moschovakis, Y. N. (n.d.). Abstract Recursion as a Foundation for the Theory of Algorithms.

Pérez-Sánchez, B., Fontenla-Romero, O., & Guijarro-Berdiñas, B. (2018). A review of adaptive online learning for artificial neural networks. Artificial Intelligence Review, 49(2), 281–299.  
https://doi.org/10.1007/s10462-016-9526-2

Shaffer, C. A. (2010). A Practical Introduction to Data Structures and Algorithm Analysis Third Edition (Java Version).  
http://people.cs.vt.edu/

Sharma, S., Sharma, S., & Athaiya, A. (2020). ACTIVATION FUNCTIONS IN NEURAL NETWORKS. In International Journal of Engineering Applied Sciences and Technology (Vol. 4).  
http://www.ijeast.com

Surti, C., & Celani, A. (2019). Newsvendor Game: A Behavioral Exercise in Decision Making under Risk. Decision Sciences Journal of Innovative Education, 17(4), 324–343.  
https://doi.org/10.1111/dsji.12194

Thuy, T., & Nguyen, H. (2023). Applications of Artificial Intelligence for Demand Forecasting. OPERATIONS AND SUPPLY CHAIN MANAGEMENT, 16(4), 424–434.

Yue, D., & You, F. (2013). Planning and scheduling of flexible process networks under uncertainty with stochastic inventory: MINLP models and algorithm. AIChE Journal, 59(5), 1511–1532.  
https://doi.org/10.1002/aic.13924

Zhang, Jingqiao., & Sanderson, A. C. . (2009). Adaptive differential evolution : a robust approach to multimodal problem optimization. Springer.

Ziukov, S. (2015). A literature review on models of inventory management under uncertainty. Business Systems & Economics, 5(1), 26.  
https://doi.org/10.13165/vse-15-5-1-03
