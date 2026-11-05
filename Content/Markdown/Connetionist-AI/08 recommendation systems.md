# Chapter 8: Recommendation Systems (Brief)

**Level:** Beginner | **Reading time:** ~25 minutes

## What you will learn
- What recommender systems do and the data they use
- Content-based, collaborative, matrix factorization and neural approaches
- How a large-scale pipeline (retrieval, ranking, re-ranking) is organised
- Main challenges: cold start, bias, feedback loops
- Evaluation metrics with worked numbers

---

## 1. What Is a Recommender System?

A recommender **predicts which items a user will find relevant** and presents a ranked list: products, videos, songs, articles, jobs, friends. It helps people cope with too many choices, and helps businesses with engagement, discovery and sales.

### Data Used
| Type | Examples | Notes |
|---|---|---|
| **Explicit feedback** | Star ratings, likes, thumbs | Clear but scarce |
| **Implicit feedback** | Clicks, views, purchases, watch time, skips | Abundant but noisy (a click isn't always a like) |
| **User features** | Age group, location, device | Often limited by privacy |
| **Item features** | Genre, price, text, image | Help new items |
| **Context** | Time of day, device, location | Matters for sessions |

The core data structure is the **user–item interaction matrix**, which is very **sparse** because each user touches a tiny fraction of items.

## 2. Main Approaches

### 2.1 Content-Based Filtering
Recommend items **similar to those the user liked**, based on item attributes.
- Build an item profile (genre, keywords, text embeddings).
- Build a user profile from items they liked.
- Rank items by similarity to the profile.

✅ Works for new items with known features; explainable; needs no other users.
❌ Limited novelty ("more of the same"); needs good item features.

### 2.2 Collaborative Filtering (CF)
Uses **behaviour patterns of many users**, without item features: "users like you also liked...".

```
           Movie A  Movie B  Movie C
Alice         5        4        ?
Bob           5        4        5
Carol         1        2        1
```
Alice's ratings resemble Bob's, so Bob's high rating of Movie C suggests Alice would like it.

**User-based CF:** find similar users, borrow their opinions.
**Item-based CF:** find items similar to ones the user liked (often more stable and scalable).

**Similarity measure: cosine similarity**
```
cos(A, B) = (A · B) / (|A| · |B|)
```
Example: A = [5, 4, 0], B = [4, 5, 1]
```
A·B = 20 + 20 + 0 = 40
|A| = √41 ≈ 6.403,  |B| = √42 ≈ 6.481
cos ≈ 40 / 41.50 ≈ 0.964   (very similar)
```

✅ Discovers surprising, cross-category tastes.
❌ Cold start; sparsity; popularity bias.

### 2.3 Matrix Factorization (MF), the connectionist bridge
Learn a small vector (an **embedding**) for every user and every item, so that their **dot product** predicts the rating or interaction.

```
user u = [1, 2]
item v = [3, 1]
predicted score = 1·3 + 2·1 = 5
```
Each embedding dimension is a hidden "taste factor" (e.g. "likes action", "prefers old films"), discovered automatically.

**Training by gradient descent.** Suppose the true rating is 4 but we predicted 5 (error = +1), with learning rate 0.1 (constant factors absorbed):
```
u_new = u − lr·error·v = [1 − 0.3, 2 − 0.1] = [0.7, 1.9]
v_new = v − lr·error·u = [3 − 0.1, 1 − 0.2] = [2.9, 0.8]
new prediction = 0.7·2.9 + 1.9·0.8 = 2.03 + 1.52 = 3.55
```
The prediction moved from 5 toward the true value 4. Real training repeats this over millions of interactions, with **L2 regularisation** to prevent overfitting, and **user/item bias terms** (some users rate generously, some items are popular).

```python
import torch, torch.nn as nn

class MF(nn.Module):
    def __init__(self, n_users, n_items, dim=32):
        super().__init__()
        self.U = nn.Embedding(n_users, dim)
        self.V = nn.Embedding(n_items, dim)
    def forward(self, u, i):
        return (self.U(u) * self.V(i)).sum(dim=1)
```

### 2.4 Neural Recommenders
- **Neural Collaborative Filtering (NCF):** concatenate user and item embeddings and feed them to an **MLP**, learning non-linear interactions instead of a plain dot product.
- **Two-tower models:** one network encodes the user (history, features), another encodes the item. Score = dot product of the two output vectors. Item vectors can be **precomputed**, and the best matches found quickly with **approximate nearest-neighbour search** (e.g. FAISS). This is the standard design for large-scale **candidate retrieval**.
- **Autoencoder-based CF:** reconstruct a user's interaction vector; reconstructed gaps become recommendations.
- **Sequence models (RNN / Transformer):** use the **order** of a user's recent actions to predict the next item (session-based recommendation, e.g. SASRec, BERT4Rec).
- **Wide & Deep / DeepFM / DLRM:** combine memorisation (feature crosses) with generalisation (deep layers) for click prediction with many categorical features.
- **Graph neural networks:** treat users and items as a bipartite graph.
- **Hybrid models:** combine collaborative signals with content features, which also helps cold start.

### Training Signals for Implicit Data
- **Negative sampling:** treat observed interactions as positives and randomly sampled unobserved items as negatives.
- **BPR (Bayesian Personalized Ranking):** learn that a clicked item should score higher than a random non-clicked one.
- **Softmax / contrastive losses** over in-batch negatives, common in two-tower training.

## 3. A Typical Large-Scale Pipeline

```
Millions of items
      │  Candidate retrieval (two-tower + nearest-neighbour search, popular items, etc.)
      ▼
Hundreds of candidates
      │  Ranking model (deep network with rich features; predicts click / watch / purchase)
      ▼
Tens of items
      │  Re-ranking (diversity, freshness, business rules, fairness, remove duplicates)
      ▼
List shown to the user
```
Retrieval must be **fast**, ranking can be **more accurate and expensive**, and re-ranking applies **business and responsibility rules**.

## 4. Key Challenges

| Challenge | Description | Typical remedy |
|---|---|---|
| **Cold start** (new user or item) | No history | Content features, popularity, onboarding questions, exploration |
| **Sparsity** | Few interactions per user | Embeddings, side features, implicit signals |
| **Popularity bias** | Popular items dominate | Re-weighting, diversity, debiasing |
| **Filter bubbles** | Only more of the same | Diversity, exploration |
| **Feedback loops** | Recommendations shape the future data used to train | Randomised exploration, off-policy correction |
| **Position bias** | Top slots get clicks regardless of quality | Model position or randomise |
| **Scalability and latency** | Millions of items, milliseconds to respond | Precomputed embeddings, ANN search |
| **Privacy and fairness** | Personal data and unequal exposure | Data minimisation, fairness constraints |

**Exploration vs exploitation:** showing known favourites (exploit) vs testing new items to learn (explore). This links to **bandit algorithms** and reinforcement learning (Chapter 9).

## 5. Evaluation

### Offline Metrics (on held-out interactions)
- **Precision@K:** share of the top K recommendations that are relevant.
  10 shown, 3 relevant → Precision@10 = **0.3**.
- **Recall@K:** share of all relevant items found in the top K.
  User has 6 relevant items; 3 are in the top 10 → Recall@10 = **0.5**.
- **NDCG@K** (Normalised Discounted Cumulative Gain): rewards putting relevant items near the top.

  **Example.** Top 3 results relevance = `[1, 0, 1]`.
  ```
  DCG  = 1/log2(1+1) + 0/log2(2+1) + 1/log2(3+1) = 1 + 0 + 0.5 = 1.5
  IDCG (best order [1,1,0]) = 1 + 1/log2(3) = 1 + 0.631 = 1.631
  NDCG = 1.5 / 1.631 ≈ 0.92
  ```
- **MAP, MRR (mean reciprocal rank):** other ranking metrics.
- **RMSE / MAE:** error on predicted ratings (less used now than ranking metrics).
- **Beyond accuracy:** **coverage** (share of catalogue ever recommended), **diversity**, **novelty**, **serendipity**.

### Online Metrics
Click-through rate, conversion, watch time, retention, revenue, measured with **A/B tests**. Offline gains don't always translate online, so A/B testing is the final judge.

### Splitting Data
Use a **time-based split** (train on the past, test on later interactions), or leave-last-out per user. Random splits leak future behaviour.

## Key Takeaways
- Recommenders rank items per user using explicit and implicit feedback.
- Content-based uses item features; collaborative uses behaviour; matrix factorization and neural models learn **embeddings** and combine both.
- Large systems split into retrieval, ranking and re-ranking.
- Cold start, bias and feedback loops are the main practical issues.
- Judge with ranking metrics offline and A/B tests online.

---

## Practice Questions

1. Content-based vs collaborative filtering?
2. MF score for user `[2, 0, 1]` and item `[1, 3, 4]`?
3. What is the cold-start problem? One remedy?
4. Precision@10 if 3 of 10 are relevant?
5. Why are two-tower models used for large catalogues?
6. One example each of explicit and implicit feedback?
7. What is a filter bubble?
8. Why use a time-based split for evaluation?

---

## Answer Key

1. Content-based uses item features; collaborative uses patterns across users or items.
2. 2 + 0 + 4 = **6**.
3. New users or items lack history. Remedy: content features, popularity, onboarding questions.
4. **0.3**.
5. Item embeddings can be precomputed and searched quickly via nearest-neighbour search.
6. Explicit: a star rating. Implicit: a click or watch time.
7. The user only sees more of what they already like, losing diversity.
8. Random splits leak future behaviour into training and inflate results.
