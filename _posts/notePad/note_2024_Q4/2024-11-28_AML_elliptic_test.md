---
title: "[Note] 2024-11-28 AML_elliptic_test.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-11-28 21:49:00 +0900
comments: false
---
---

Anti-money laundering in
bitcoin: Experimenting with graph convolutional networks for financial forensics.
arXiv preprint arXiv:1908.02591 (2019).



Machine learning techniques for anti-money laundering (AML) solutions in suspicious transaction detection: a review.
Knowledge and Information Systems 57, 2 (2018), 245–285.



##환경셋팅
conda 설치해놨는데 언제 없어졌지
conda config --add channels conda-forge
conda env create -f environment.yml
conda env list
conda init
conda activate reaml


##echo off

cd C:\Users\User\OneDrive\2024_부산대\바탕 화면\(개연)AML\reaml
call activate reaml
call jupyter lab


!pip install jupyterlab networkx scikit-learn matplotlib seaborn pyod


!python src/experiments/supervised_baseline.py
!python src/experiments/anomaly_detection_benchmark.py
!python src/experiments/umap_data_exploration.py




python -m ipykernel install --user --name reaml --display-name reaml


conda install -c anaconda pywin32


pip install umap-learn==0.5.2


