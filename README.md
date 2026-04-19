# 📰 Fake News Detection with LSTM

> A deep learning approach to classify news articles as real or fake using NLP preprocessing and LSTM neural networks.

![Python](https://img.shields.io/badge/Python-3.x-blue?style=flat-square)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Keras-orange?style=flat-square)
![NLTK](https://img.shields.io/badge/NLP-NLTK-yellow?style=flat-square)
![Model](https://img.shields.io/badge/Model-LSTM-purple?style=flat-square)
![Task](https://img.shields.io/badge/Task-Binary%20Classification-teal?style=flat-square)

---

## Overview

This project builds an LSTM-based deep learning model to detect fake news from article titles. It covers the full ML pipeline — from raw CSV data, through text preprocessing and one-hot encoding, to model training and evaluation using TensorFlow/Keras.

**Dataset:** `fake_news.csv` — 2,084 articles (after cleaning) with title, author, body text, and a binary label:
- `0` = Real
- `1` = Fake

---

## Pipeline

```
Load CSV → Clean & Drop NAs → Stem & Remove Stopwords → One-Hot Encode → Pad Sequences → LSTM Model → Evaluate
```

---

## Model Architecture

| Layer | Type | Details |
|-------|------|---------|
| 1 | Embedding | vocab=10,000 · dims=40 · seq_len=20 |
| 2 | Dropout | rate=0.3 — prevents overfitting |
| 3 | LSTM | units=100 |
| 4 | Dropout | rate=0.3 |
| 5 | Dense | units=1 · activation=sigmoid |

**Compiled with:** Adam optimizer · Binary Crossentropy loss

---

## Results

| Metric | Value |
|--------|-------|
| Training Accuracy | 100% |
| Validation Accuracy (peak) | ~90% (epoch 7) |
| Test Accuracy | 61.0% |
| Epochs | 100 |

> ⚠️ **Overfitting observed:** The model achieves 100% training accuracy but validation accuracy plateaus around epoch 7 (~90%) and degrades beyond that. Early stopping and regularization are strongly recommended.

### Confusion Matrix (test set)

```
[[344  47]   # True Negatives | False Positives
 [221  76]]  # False Negatives | True Positives
```

---

## Quickstart

```bash
# 1. Install dependencies
pip install tensorflow scikit-learn nltk pandas numpy

# 2. Download NLTK stopwords
python -c "import nltk; nltk.download('stopwords')"

# 3. Place dataset and run notebook
# fake_news.csv → same directory as notebook
jupyter notebook fake_news_detection.ipynb
```

---

## Project Structure

```
fake-news-lstm/
├── fake_news_detection.ipynb   # main notebook
├── fake_news.csv               # dataset (not included in repo)
├── requirements.txt
└── README.md
```

---

## Known Issues & Improvements

| Area | Suggestion |
|------|-----------|
| **Early stopping** | Add `EarlyStopping` callback to halt training at peak val accuracy (~epoch 7) |
| **Feature richness** | Currently only article titles are used — incorporating body text would improve accuracy significantly |
| **Embeddings** | Replace one-hot encoding with pretrained GloVe or Word2Vec embeddings |
| **Architecture** | Try a `Bidirectional LSTM` to capture context from both directions |
| **Baseline** | Add a Logistic Regression + TF-IDF baseline for fair comparison |
| **Class imbalance** | Check label distribution and apply class weights or oversampling if skewed |

---

## Dependencies

```
tensorflow>=2.x
scikit-learn
nltk
pandas
numpy
```

---

