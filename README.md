# Regime-Detection-via-Unsupervised-Learning
Using unsupervised learning, based on real-time order book and volume features

## Objective

The objective of this project is to segment the market into distinct behavioral regimes using real-time order book and volume data. These regimes are categorized along three key axes:

1. Trending vs. Mean-reverting  
2. Volatile vs. Stable  
3. Liquid vs. Illiquid  

Unsupervised machine learning methods were employed to discover these regimes without predefined labels.

---

## Data Description

Two primary data sources were used:

- **Order Book Snapshots (`depth20_1000ms`)**  
  Provided top 20 bid and ask prices and quantities at 1-second intervals.

- **Aggregated Trades (`aggTrade`)**  
  Recorded trade events including time, price, quantity, and market maker side.

Both datasets were parsed from CSV-formatted `.txt` files contained in zipped folders. Timestamps were cleaned and standardized before feature extraction.

---

## Feature Engineering

### Liquidity & Depth Features

- **Spread** = AskPriceL1 - BidPriceL1  
- **Microprice** = Weighted avg of BidPriceL1 & AskPriceL1  
- **Order Book Imbalance** = (BidQtyL1 - AskQtyL1) / (BidQtyL1 + AskQtyL1)  
- **Cumulative Depth** = Sum of BidQtyL1..20 and AskQtyL1..20  

### Price Action & Volatility

- **Mid Price** = (BidPriceL1 + AskPriceL1) / 2  
- **Log Returns** = log(mid_t / mid_{t-1})  
- **Rolling Volatility** = std dev of returns over 10s, 30s  

### Volume Features (from `aggTrade`)

- **Rolling Volume** = Cumulative volume over 10s, 30s  
- **VWAP Shift** = Change in volume-weighted average price  

All features were normalized using z-score standardization. PCA was applied with 10 components.

---

## Clustering Methodology

Two clustering algorithms were compared:

### 1. KMeans
- Fixed number of clusters (n = 4)
- Simple and interpretable

### 2. HDBSCAN
- Adaptive clustering with noise handling
- No pre-specified number of clusters

### Evaluation Metrics

| Method   | Silhouette Score ↑ | DB Index ↓ |
|----------|--------------------|-------------|
| KMeans   | 0.23               | 1.51        |
| HDBSCAN  | 0.38               | 0.81        |

➡**HDBSCAN outperformed KMeans** in both metrics but KMeans offered more interpretable regime structure.

---

##  Regime Insights (from KMeans)

- **Cluster 0:** High spread, high volatility, low liquidity → `Volatile & Illiquid`  
- **Cluster 1:** Low volatility, high liquidity, low spread → `Stable & Liquid`  
- **Cluster 2:** Moderate spread, mean-reverting returns → `Mean Reverting`  
- **Cluster 3:** Trending movement with liquidity → `Trending & Liquid & Stable`  

Regime labels were assigned based on volatility, depth, and price behavior.

---

## Visualizations

- **Regime Evolution Over Time** — cluster labels vs time  
- **Mid Price Overlay** — mid price with regime markers  
- **UMAP Projection** — 2D embedding of PCA features (KMeans clusters)  
- **Regime Transition Matrix** — probability of switching from one regime to another  

---

## Conclusion

This project demonstrated that unsupervised clustering on market microstructure data can uncover valuable regime patterns. HDBSCAN produced the most reliable clusters, while KMeans helped label and interpret them.
---
