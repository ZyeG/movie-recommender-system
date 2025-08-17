# Movie-Lens-Style Recommender System

> Predict user ratings and generate top-N movie recommendations using Alternating Least Squares (ALS).  
> Author: Z.G.
---

## 1 · Project Snapshot
| Item | Details |
|------|---------|
| **Goal** | Build and benchmark an ALS-based recommender on implicit ratings; compare data splits, error metrics, and hyper-parameter choices. |
| **Dataset** | 99 826 ratings  •  columns = `userId`, `movieId`, `rating` |
| **Best Config** | 70 / 30 split, `rank = 20`, `regParam = 0.1` |
| **Key Metric** | **RMSE = 1.0172** (↓ from 1.0663 on 80 / 20 split) |
| **Top-K Quality** | Precision@5 ≈ 0.055 • Recall@5 ≈ 0.159  |

---

## 2 · Exploratory Insights
- **Top-10 movies** by average rating and **top-10 users** by rating count reveal potential popularity bias.  
- Ratings are **heavily skewed toward “1”**, requiring robust evaluation beyond RMSE.  
- Most users supply **44–57 ratings**, so the system is not dominated by a few power-raters.  

---

## 3 · Data Preparation
1. **Remove sparsity outliers** to focus on informative interactions.  
2. **Train-test splits**: 70 / 30 vs 80 / 20; the former yields lower RMSE.  
3. **Error metrics** evaluated: RMSE, MSE, MAE, Precision@k, Recall@k, F1@k.  
   - Ranking metrics highlight cases where RMSE alone is misleading.  

---

## 4 · Modeling
### 4.1 Base ALS  
| Param | Value |
|-------|-------|
| `rank` | 10 |
| `regParam` | 0.1 |
| `maxIter` | 10 |

### 4.2 Hyper-parameter Search  
- Grid over `rank ∈ {10, 20, 30}`, `regParam ∈ {0.05, 0.1, 0.2}` using **5-fold CV**.  
- **Best model**: `rank = 20`, `regParam = 0.1` — *RMSE = 1.0472 on held-out test*.  
- Training time grows roughly linearly with `rank`, so values ≥ 20 offer diminishing returns.  

---

## 5 · Results
| Split | Model | RMSE | Precision@5 | Recall@5 |
|-------|-------|------|-------------|-----------|
| 70 / 30 | ALS (rank = 10) | **1.0172** | 0.0545 | 0.1591 |
| 70 / 30 | ALS (rank = 20) | 1.0472 | 0.0455 | 0.1061 |
| 80 / 20 | ALS (rank = 10) | 1.0663 | 0.0588 | 0.1961 |

**Take-away:** lower RMSE does not always translate to higher top-K quality; trade-offs depend on business goals.

---

## 6 · Personalised Recommendations
Generated top-5 movie lists for *User 11* and *User 21*, excluding items already rated. Recommendations derive from the dot-product of latent user/movie vectors learned by ALS. 

---

## 7 · Limitations & Next Steps
- **Cold-start**: lack of metadata hurts new users/items.  
- **Sparse likes**: few ratings ≥ 4 depress Recall@k.  
- **Future work**: incorporate user demographics, movie tags, and hybrid content-based signals; experiment with neural collaborative filtering for sparse settings. 
