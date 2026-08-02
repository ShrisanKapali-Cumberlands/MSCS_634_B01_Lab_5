# Lab 5: Clustering Techniques Using DBSCAN and Hierarchical Clustering

**Student:** Shrisan kapali  
**Professor:** Satish Penmatsa  
**Course:** Advanced Big Data and Data Mining (MSCS 634 B01)  
**Date:** 08/02/2026

## Overview

This project explores two unsupervised clustering techniques - Agglomerative Hierarchical Clustering and DBSCAN - applied to the Wine dataset from `sklearn.datasets`. The dataset contains 178 wine samples with 13 standardized chemical measurements, drawn from three known cultivars. The goal of the analysis is to compare a distance-based clustering method against a density-based clustering method, evaluate how sensitive each is to its parameters, and assess how well each recovers the known cultivar structure using silhouette, homogeneity, and completeness scores.

## Techniques Implemented

- **Data Preparation:** Loaded the Wine dataset, examined its structure with `.head()`, `.info()`, and `.describe()`, and standardized all 13 features with `StandardScaler` so that no single feature (e.g., proline) dominated the distance calculations.
- **Agglomerative Hierarchical Clustering:** Applied Ward-linkage clustering and tested `n_clusters` values of 2, 3, 4, and 5, visualizing each result on a PCA-reduced scatter plot and generating a full dendrogram to inspect the nested merge structure.
- **DBSCAN:** Ran a grid search over `eps` (1.0-2.5) and `min_samples` (3-10) to observe their effect on cluster count and noise, then visualized three representative configurations with noise points explicitly highlighted.

## Comprehensive Model Evaluation Results

| Model                                      | Silhouette Score              | Homogeneity Score | Completeness Score |
| :----------------------------------------- | :---------------------------- | :---------------- | :----------------- |
| **Hierarchical Clustering (n_clusters=3)** | **Highest**                   | **Highest**       | **Highest**        |
| **DBSCAN (eps=1.5, min_samples=5)**        | Lower (non-noise points only) | Lower             | Lower              |

## Key Findings and Insights

- **Best Performing Model:** Hierarchical clustering with `n_clusters=3` achieved the best overall alignment with the true cultivar labels, producing the highest silhouette, homogeneity, and completeness scores of any configuration tested. Setting `n_clusters=3` matched both the dendrogram's natural high-level branching and the known number of cultivars in the dataset.
- **Effect of Cluster Count:** Increasing `n_clusters` beyond 3 (to 4 or 5) fragmented the natural cultivar groupings into smaller, less interpretable sub-clusters, which was visible in the scatter plots as a decline in cluster quality and silhouette score.
- **DBSCAN Parameter Sensitivity:** `eps` and `min_samples` worked together to control cluster density. A small `eps` paired with a high `min_samples` classified nearly every point as noise, since the standardized 13-dimensional feature space doesn't pack many points within a small radius. A large `eps` merged distinct cultivars into a single cluster, erasing the underlying structure. The mid-range setting of `eps=1.5, min_samples=5` balanced these extremes but still underperformed hierarchical clustering overall.
- **Why Hierarchical Outperformed DBSCAN Here:** The Wine dataset's three cultivars form clusters that overlap somewhat in density rather than being separated by clear empty regions, which favors Ward-linkage hierarchical clustering over a purely density-based approach like DBSCAN.
- **Value of Noise Detection:** Despite its lower alignment with the true labels, DBSCAN's main practical strength in this lab was flagging borderline/outlier wine samples at the sparse edges of the PCA projection - something hierarchical clustering cannot do, since it forces every point into a cluster.

### Strengths and Weaknesses

**Hierarchical Clustering:**

- _Strengths:_ Produces a clear dendrogram, facilitating an understanding of the nested structure of clusters. There is no need to estimate density parameters, and performance improves markedly when the correct number of clusters is identified.
- _Weaknesses:_ Determining the number of clusters beforehand, or identifying the appropriate cut height in the dendrogram, poses a challenge. The method assumes that clusters are reasonably compact and that every point must belong to a cluster, which limits its ability to highlight outliers. Additionally, hierarchical clustering can be computationally intensive with larger datasets.

**DBSCAN:**

- _Strengths:_ One significant advantage of DBSCAN is that it does not necessitate the upfront specification of the number of clusters, which proves convenient. It also automatically identifies noise and outliers, an essential feature when dealing with messy real-world data. Furthermore, DBSCAN can effectively capture non-convex shapes within clusters, whereas methods such as hierarchical clustering cannot.
- _Weaknesses:_ DBSCAN is sensitive to the choices of `eps` and `min_samples`, necessitating considerable manual testing to identify suitable values. The method struggles with clusters of varying densities, leading to lower homogeneity and completeness scores than hierarchical clustering. Additionally, achieving reproducible results with DBSCAN can be challenging due to variations in feature scaling or dimensional choices.

## Challenges

- The main challenge in this lab was finding and correctly applying the right commands for generating the dendrogram - specifically working out how `scipy.cluster.hierarchy.linkage` and `scipy.cluster.hierarchy.dendrogram` fit together (computing the linkage matrix first with the correct method, then passing it into the dendrogram function with the right parameters) took some trial and error before producing a readable plot.
