# Chapter 2: Basic Concepts, Terminology and Architectures of Neural Networks


## 1. The Artificial Neuron

A neuron performs three steps:

1. **Multiply** each input by its weight
2. **Add** the results plus a **bias**
3. **Apply an activation function**

```
z = w1·x1 + w2·x2 + ... + wn·xn + b
output = activation(z)
```

**Why a bias?** Without it, a neuron's decision boundary must pass through the origin. The bias lets the neuron shift its threshold, like the intercept `b` in the line `y = mx + b`.

**Worked example.** Inputs `x = [1, 2]`, weights `w = [0.5, -0.3]`, bias `b = 0.4`:

```
z = (0.5 × 1) + (-0.3 × 2) + 0.4 = 0.5 - 0.6 + 0.4 = 0.3
```
- ReLU(Rectified Linear Unit): max(0, 0.3) = **0.3**
- Sigmoid: 1 / (1 + e^-0.3) ≈ **0.574**

**Intuition.** Positive weights mean "this input pushes the neuron to fire"; negative weights mean "this input suppresses it"; larger magnitude means more influence.

## 2. Essential Terminology

| Term | Meaning |
|---|---|
| **Feature** | One input variable (a pixel, a price, a word embedding value) |
| **Input layer** | Receives the raw features. It does no computation |
| **Hidden layer** | Layers between input and output that learn intermediate features |
| **Output layer** | Produces the final prediction |
| **Weight** | Learned strength of a connection |
| **Bias** | Learned offset added before the activation |
| **Activation function** | Non-linear function applied to a neuron's sum (Chapter 4) |
| **Parameters** | All learnable numbers (weights and biases) |
| **Hyperparameters** | Settings chosen by *you*: learning rate, layers, units, batch size |
| **Forward pass** | Computing the output from the input |
| **Loss / cost function** | A number measuring how wrong the prediction is |
| **Backpropagation** | Computes how much each weight contributed to the loss |
| **Gradient** | The slope of the loss with respect to a parameter |
| **Gradient descent** | Moves parameters in the direction that reduces loss |
| **Learning rate** | Size of each update step |
| **Epoch** | One full pass through the training data |
| **Batch / mini-batch** | A subset of the data used for one update |
| **Iteration** | One update step (one batch) |
| **Overfitting** | Memorising training data, performing poorly on new data |
| **Underfitting** | Too simple to capture the pattern |
| **Generalisation** | Performing well on unseen data |
| **Training / validation / test sets** | Fit the weights / tune choices / final honest check |
| **Depth** | Number of layers. **Width** is the units per layer |

**Epochs, batches and iterations.** If you have 1,000 samples and a batch size of 100, one epoch = 10 iterations.

## 3. Layers and Matrix Form

A **dense (fully connected) layer** connects every input to every neuron. For a whole layer at once:

```
h = activation(W · x + b)
```
- `x`: input vector (size n_in)
- `W`: weight matrix (n_out × n_in)
- `b`: bias vector (size n_out)

**Parameter count of a dense layer** = `n_in × n_out + n_out`.
Example: 3 inputs → 4 neurons = 12 + 4 = **16**.
A network `3 → 4 → 4 → 1` has (3×4+4) + (4×4+4) + (4×1+1) = 16 + 20 + 5 = **41** parameters.

## 4. A Complete Worked Example: Forward Pass, Loss and One Update

**Network:** 2 inputs → 2 hidden neurons (ReLU) → 1 output (linear).

Input: `x = [1, 2]`, target `y = 1`.

Hidden layer:
- Neuron 1: weights `[0.5, -0.3]`, bias `0.4`
- Neuron 2: weights `[0.2, 0.4]`, bias `-0.1`

Output neuron: weights `[1.0, -0.5]`, bias `0.2`.

**Step 1: forward pass**
```
h1 = ReLU(0.5·1 + (-0.3)·2 + 0.4) = ReLU(0.3) = 0.3
h2 = ReLU(0.2·1 +   0.4 ·2 - 0.1) = ReLU(0.9) = 0.9
ŷ  = 1.0·0.3 + (-0.5)·0.9 + 0.2   = 0.3 - 0.45 + 0.2 = 0.05
```

**Step 2: loss (squared error)**
```
L = (ŷ - y)² = (0.05 - 1)² = 0.9025
```

**Step 3: backward pass (just for the output weight w1 = 1.0)**
Using the chain rule:
```
dL/dŷ  = 2(ŷ - y) = 2(0.05 - 1) = -1.9
dŷ/dw1 = h1 = 0.3
dL/dw1 = -1.9 × 0.3 = -0.57
```

**Step 4: update with learning rate 0.1**
```
w1_new = 1.0 - 0.1 × (-0.57) = 1.057
```
The weight increased, pushing the prediction (0.05) toward the target (1). Repeating this for every weight, over many batches and epochs, is training.

## 5. What Backpropagation Really Is

Backpropagation is **the chain rule of calculus applied efficiently**. The loss depends on the output, which depends on the hidden activations, which depend on earlier weights. We compute gradients **from the output backward**, reusing intermediate results, so one backward pass gives the gradient for every weight at roughly the cost of one forward pass.

You rarely code it by hand. Frameworks like PyTorch and TensorFlow do **automatic differentiation**:

```python
import torch
w = torch.tensor(1.0, requires_grad=True)
h1 = torch.tensor(0.3)
loss = (w*h1 + 0.2 - 1.0) ** 2    # simplified
loss.backward()
print(w.grad)                      # gradient computed automatically
```

## 6. The Learning Loop

1. **Forward pass:** make a prediction
2. **Compute loss:** compare with the truth
3. **Backward pass:** compute gradients
4. **Update:** `w ← w − learning_rate × gradient`
5. Repeat over batches and epochs, monitoring validation loss

## 7. Common Loss Functions

| Task | Output layer | Loss |
|---|---|---|
| Regression | Linear | Mean Squared Error (MSE) or MAE |
| Binary classification | Sigmoid | Binary cross-entropy |
| Multi-class classification | Softmax | Categorical cross-entropy |

**Cross-entropy example.** If the true class gets predicted probability 0.7, the loss is −ln(0.7) ≈ **0.357**. Probability 0.99 gives 0.010; probability 0.1 gives 2.30. Confident wrong answers are punished heavily.

## 8. Architectures

### 8.1 Feedforward Network / Multi-Layer Perceptron (MLP)
- Information flows one way, input → hidden → output.
- Every layer is dense.
- **Use for:** tabular data, simple regression and classification, and as building blocks inside bigger models.
- **Limit:** ignores structure (it doesn't know pixels are neighbours or that words come in order).

### 8.2 Convolutional Neural Network (CNN)
- A small **filter (kernel)** slides over the image, detecting a local pattern wherever it appears.
- **Weight sharing:** the same filter is reused across positions, so far fewer parameters than a dense layer.
- **Pooling** reduces size and adds tolerance to small shifts.
- Early layers detect edges, later layers detect textures, parts and objects.

**Useful formulas**
- Output size = `(n − k + 2p) / s + 1` (input size n, kernel k, padding p, stride s).
  Example: n = 32, k = 3, p = 1, s = 1 → (32 − 3 + 2)/1 + 1 = **32**.
- Parameters of a conv layer = `(k × k × c_in + 1) × c_out`.
  Example: 3×3 kernel, 3 input channels, 16 filters → (27 + 1) × 16 = **448**.

**Use for:** images, video, audio spectrograms, also some text and time series (1D convolution).

### 8.3 Recurrent Networks: RNN, LSTM, GRU
A basic RNN keeps a **hidden state** that carries information through a sequence:

```
h_t = tanh(Wx · x_t + Wh · h_(t-1) + b)
```
- **Problem:** gradients vanish over long sequences, so it forgets distant history.
- **LSTM** adds a **cell state** and three **gates** (forget, input, output) that decide what to erase, store and reveal.
- **GRU** is a lighter two-gate version.
- **Use for:** time series, speech, older text models. Largely replaced by Transformers in language, but still useful for small sequence tasks.

### 8.4 Transformer
- Uses **self-attention**: each element of a sequence computes how relevant every other element is, then mixes information accordingly.

```
Attention(Q, K, V) = softmax(Q·Kᵀ / √d) · V
```
- Processes the whole sequence **in parallel** (fast on GPUs) and captures long-range relationships directly.
- Needs **positional encodings**, since it has no built-in notion of order.
- **Use for:** language, and increasingly vision, audio, time series and multimodal tasks.

### 8.5 Autoencoder
- **Encoder** compresses input into a small **latent code**; **decoder** reconstructs the input from it.
- Trained to minimise reconstruction error, with no labels needed.
- **Use for:** dimensionality reduction, denoising, **anomaly detection** (anomalies reconstruct poorly).
- **Variational autoencoders (VAEs)** learn a smooth latent space that can generate new samples.

### 8.6 Generative Adversarial Network (GAN)
- **Generator** creates fake samples; **discriminator** tries to tell real from fake. They improve by competing.
- **Use for:** realistic image synthesis. Training can be unstable.

### 8.7 Other Types Worth Knowing
- **Graph neural networks (GNNs):** data shaped as nodes and edges (molecules, social networks).
- **Diffusion models:** generate images by learning to remove noise step by step.

### Architecture Comparison

| Architecture | Typical data | Key idea | Main strength | Main limitation |
|---|---|---|---|---|
| MLP | Tabular | Dense layers | Simple, flexible | Ignores structure |
| CNN | Images | Local filters, shared weights | Efficient for spatial data | Limited long-range context |
| RNN/LSTM | Sequences | Hidden state | Handles variable length | Slow, hard to parallelise |
| Transformer | Text, sequences | Attention | Long-range, parallel | Memory heavy for long inputs |
| Autoencoder | Any | Compress and reconstruct | Unsupervised features | Reconstruction ≠ usefulness |
| GAN | Images | Generator vs discriminator | Sharp samples | Unstable training |

## 9. How to Choose an Architecture

1. Look at your **data type** (table, image, sequence, graph).
2. Start with the **simplest sensible model** (often an MLP, or a pre-trained model).
3. Build a **baseline** first, then add complexity only if needed.
4. Prefer **transfer learning** (reuse a pre-trained network) when data is limited.

## 10. Tiny Code Example (PyTorch)

```python
import torch
import torch.nn as nn

model = nn.Sequential(
    nn.Linear(2, 2),   # input 2 -> hidden 2
    nn.ReLU(),
    nn.Linear(2, 1)    # hidden 2 -> output 1
)

x = torch.tensor([[1.0, 2.0]])
y = torch.tensor([[1.0]])

loss_fn = nn.MSELoss()
opt = torch.optim.SGD(model.parameters(), lr=0.1)

for step in range(100):
    opt.zero_grad()
    loss = loss_fn(model(x), y)
    loss.backward()
    opt.step()

print(model(x).item())   # close to 1.0
```

## 11. Common Beginner Mistakes
- Forgetting to **scale inputs**, which makes training slow or unstable.
- Evaluating on training data only.
- Using softmax **and** a loss that already includes softmax (e.g. PyTorch `CrossEntropyLoss` expects raw scores).
- Making the network far bigger than the data justifies.

## Key Takeaways
- A neuron = weighted sum + bias + activation.
- A network is layers of neurons; parameters = weights + biases.
- Training = forward pass, loss, backpropagation, update, repeated.
- Choose the architecture to match the data's structure.

---

## Practice Questions

1. Compute a ReLU neuron with inputs `[2, 1]`, weights `[0.4, 0.6]`, bias `-1`.
2. Parameters in a dense layer with 5 inputs and 3 neurons?
3. 2,000 samples, batch size 250: how many iterations per epoch?
4. Order these: update weights, forward pass, compute loss, backward pass.
5. Which architecture first for (a) photos, (b) spreadsheet, (c) translation?
6. What problem do LSTM gates help solve?
7. What does validation loss rising while training loss falls indicate?
8. Why is a bias term useful?

---

## Answer Key

1. z = 0.8 + 0.6 − 1 = 0.4, ReLU → **0.4**.
2. 5×3 + 3 = **18**.
3. 2000 / 250 = **8**.
4. Forward pass → loss → backward pass → update.
5. (a) CNN, (b) MLP, (c) Transformer.
6. Vanishing gradients and forgetting over long sequences.
7. Overfitting.
8. It shifts the activation threshold so the neuron isn't forced through the origin.
