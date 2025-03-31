---
title: Data-driven parametrization of molecular mechanics force fields for expansive
  chemical space coverage
authors:
- Tianze Zheng
- Ailun Wang
- Xu Han
- Yu Xia
- Xingyuan Xu
- admin
- Yu Liu
- Yang Chen
- Zhi Wang
- Xiaojie Wu
- ' others'
date: '2025-01-01'
publishDate: '2025-01-01T21:08:40.653258Z'
abstract: A force field is a critical component in molecular dynamics simulations for computational drug discovery. It must achieve high accuracy within the constraints of molecular mechanics' (MM) limited functional forms, which offers high computational efficiency. With the rapid expansion of synthetically accessible chemical space, traditional look-up table approaches face significant challenges. In this study, we address this issue using a modern data-driven approach, developing ByteFF, an Amber-compatible force field for drug-like molecules. To create ByteFF, we generated an expansive and highly diverse molecular dataset at the B3LYP-D3(BJ)/DZVP level of theory. This dataset includes 2.4 million optimized molecular fragment geometries with analytical Hessian matrices, along with 3.2 million torsion profiles. We then trained an edge-augmented, symmetry-preserving molecular graph neural network (GNN) on this dataset, employing a carefully optimized training strategy. Our model predicts all bonded and non-bonded MM force field parameters for drug-like molecules simultaneously across a broad chemical space. ByteFF demonstrates state-of-the-art performance on various benchmark datasets, excelling in predicting relaxed geometries, torsional energy profiles, and conformational energies and forces. Its exceptional accuracy and expansive chemical space coverage make ByteFF a valuable tool for multiple stages of computational drug discovery.
publication_types:
- article-journal
publication: '*Chemical Science*'
doi: "https://doi.org/10.1039/D4SC06640E"
links:
- name: URL
  url: https://pubs.rsc.org/en/content/articlehtml/2025/sc/d4sc06640e
---
