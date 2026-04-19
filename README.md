<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fake News Detection with LSTM — README</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #ffffff;
    --bg-secondary: #f7f6f3;
    --bg-tertiary: #f1efe8;
    --text-primary: #1a1a18;
    --text-secondary: #5f5e5a;
    --text-tertiary: #888780;
    --border: rgba(0,0,0,0.12);
    --border-secondary: rgba(0,0,0,0.22);
    --radius-md: 8px;
    --radius-lg: 12px;
    --font-sans: 'DM Sans', system-ui, sans-serif;
    --font-mono: 'JetBrains Mono', 'Fira Mono', monospace;
  }

  @media (prefers-color-scheme: dark) {
    :root {
      --bg: #1c1c1a;
      --bg-secondary: #242422;
      --bg-tertiary: #2c2c2a;
      --text-primary: #f0ede6;
      --text-secondary: #b4b2a9;
      --text-tertiary: #6b6a65;
      --border: rgba(255,255,255,0.1);
      --border-secondary: rgba(255,255,255,0.18);
    }
    .badge-blue { background: #042C53 !important; color: #B5D4F4 !important; }
    .badge-green { background: #173404 !important; color: #C0DD97 !important; }
    .badge-amber { background: #412402 !important; color: #FAC775 !important; }
    .badge-purple { background: #26215C !important; color: #CECBF6 !important; }
    .badge-coral { background: #4A1B0C !important; color: #F5C4B3 !important; }
    .badge-teal { background: #04342C !important; color: #9FE1CB !important; }
    .metric-val.green { color: #9FE1CB !important; }
    .metric-val.blue { color: #B5D4F4 !important; }
    .metric-val.amber { color: #FAC775 !important; }
    .metric-val.coral { color: #F5C4B3 !important; }
    .note { background: #042C53 !important; border-left-color: #378ADD !important; }
    .note p { color: #B5D4F4 !important; }
    .note strong { color: #E6F1FB !important; }
    .arch-card .layer-tag.badge-blue { background: #042C53 !important; color: #B5D4F4 !important; }
    .arch-card .layer-tag.badge-purple { background: #26215C !important; color: #CECBF6 !important; }
    .arch-card .layer-tag.badge-teal { background: #04342C !important; color: #9FE1CB !important; }
    .arch-card .layer-tag.badge-amber { background: #412402 !important; color: #FAC775 !important; }
    .arch-card .layer-tag.badge-coral { background: #4A1B0C !important; color: #F5C4B3 !important; }
    .arch-card .layer-tag.badge-green { background: #173404 !important; color: #C0DD97 !important; }
  }

  body {
    background: var(--bg);
    color: var(--text-primary);
    font-family: var(--font-sans);
    line-height: 1.7;
    padding: 2rem 1rem;
  }

  .readme {
    max-width: 760px;
    margin: 0 auto;
  }

  .hero {
    text-align: center;
    padding: 2.5rem 1rem 2rem;
    border-bottom: 0.5px solid var(--border);
    margin-bottom: 2rem;
  }
  .hero .icon { font-size: 44px; margin-bottom: 0.75rem; }
  .hero h1 { font-size: 28px; font-weight: 600; margin-bottom: 0.5rem; }
  .hero p { font-size: 15px; color: var(--text-secondary); margin-bottom: 1.25rem; max-width: 520px; margin-left: auto; margin-right: auto; }
  .badges { display: flex; flex-wrap: wrap; gap: 8px; justify-content: center; }
  .badge { font-size: 12px; font-weight: 500; padding: 4px 12px; border-radius: 20px; }
  .badge-blue  { background: #E6F1FB; color: #0C447C; }
  .badge-green { background: #EAF3DE; color: #3B6D11; }
  .badge-amber { background: #FAEEDA; color: #854F0B; }
  .badge-purple{ background: #EEEDFE; color: #3C3489; }
  .badge-coral { background: #FAECE7; color: #993C1D; }
  .badge-teal  { background: #E1F5EE; color: #0F6E56; }

  section { margin-bottom: 2.25rem; }
  h2 {
    font-size: 18px; font-weight: 500; margin-bottom: 0.85rem;
    padding-bottom: 8px; border-bottom: 0.5px solid var(--border);
  }
  h3 { font-size: 14px; font-weight: 500; margin: 1.25rem 0 0.5rem; color: var(--text-secondary); text-transform: uppercase; letter-spacing: 0.05em; }
  p { font-size: 14px; color: var(--text-secondary); margin-bottom: 0.5rem; }

  .pipeline {
    display: flex; align-items: center; flex-wrap: wrap; gap: 4px; margin: 1rem 0;
  }
  .step {
    background: var(--bg-secondary); border: 0.5px solid var(--border);
    padding: 7px 14px; font-size: 13px; font-weight: 500;
    border-radius: var(--radius-md); white-space: nowrap;
  }
  .arrow { font-size: 14px; color: var(--text-tertiary); padding: 0 2px; }

  .metrics-grid {
    display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin: 1rem 0;
  }
  .metric-card {
    background: var(--bg-secondary); border-radius: var(--radius-md);
    padding: 0.9rem 1rem; text-align: center;
  }
  .metric-label { font-size: 11px; color: var(--text-tertiary); text-transform: uppercase; letter-spacing: 0.06em; margin-bottom: 4px; }
  .metric-val { font-size: 24px; font-weight: 500; }
  .metric-val.green  { color: #3B6D11; }
  .metric-val.blue   { color: #185FA5; }
  .metric-val.amber  { color: #854F0B; }
  .metric-val.coral  { color: #993C1D; }

  .code-block {
    background: var(--bg-tertiary); border: 0.5px solid var(--border);
    border-radius: var(--radius-md); padding: 1rem 1.25rem;
    font-family: var(--font-mono); font-size: 12.5px; line-height: 1.9;
    overflow-x: auto; margin: 0.75rem 0;
    white-space: pre;
  }
  .kw  { color: #185FA5; }
  .fn  { color: #0F6E56; }
  .str { color: #854F0B; }
  .cm  { color: var(--text-tertiary); font-style: italic; }

  .arch-row {
    display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 1rem 0;
  }
  .arch-card {
    border: 0.5px solid var(--border); border-radius: var(--radius-md); padding: 0.85rem 1rem;
  }
  .arch-card .layer-tag {
    font-size: 11px; font-weight: 500; padding: 3px 10px;
    border-radius: 20px; display: inline-block; margin-bottom: 6px;
  }
  .arch-card .layer-name { font-size: 14px; font-weight: 500; margin-bottom: 3px; }
  .arch-card .layer-detail { font-size: 12px; color: var(--text-secondary); }

  .note {
    background: #E6F1FB; border-left: 3px solid #185FA5;
    border-radius: 0 var(--radius-md) var(--radius-md) 0;
    padding: 0.75rem 1rem; margin: 1rem 0;
  }
  .note p { color: #0C447C; font-size: 13px; margin: 0; }
  .note strong { color: #042C53; }

  .improvement-grid {
    display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin: 1rem 0;
  }
  .imp-item {
    border: 0.5px solid var(--border); border-radius: var(--radius-md); padding: 0.85rem 1rem;
  }
  .imp-item .imp-title { font-size: 13px; font-weight: 500; margin-bottom: 4px; }
  .imp-item .imp-desc  { font-size: 12px; color: var(--text-secondary); line-height: 1.6; }

  .file-tree {
    font-family: var(--font-mono); font-size: 12.5px; line-height: 1.9;
    color: var(--text-secondary); background: var(--bg-tertiary);
    border: 0.5px solid var(--border); border-radius: var(--radius-md); padding: 1rem 1.25rem;
  }
  .file-tree .dir     { color: var(--text-primary); font-weight: 500; }
  .file-tree .comment { color: var(--text-tertiary); }

  @media (max-width: 520px) {
    .metrics-grid { grid-template-columns: repeat(2, 1fr); }
    .arch-row { grid-template-columns: 1fr 1fr; }
    .improvement-grid { grid-template-columns: 1fr; }
  }
</style>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
</head>
<body>
<div class="readme">

  <div class="hero">
    <div class="icon">📰</div>
    <h1>Fake News Detection with LSTM</h1>
    <p>A deep learning approach to classify news articles as real or fake using NLP preprocessing and LSTM neural networks</p>
    <div class="badges">
      <span class="badge badge-blue">Python 3.x</span>
      <span class="badge badge-green">TensorFlow / Keras</span>
      <span class="badge badge-amber">NLTK</span>
      <span class="badge badge-purple">LSTM</span>
      <span class="badge badge-coral">NLP</span>
      <span class="badge badge-teal">Binary Classification</span>
    </div>
  </div>

  <section>
    <h2>Overview</h2>
    <p>This project builds an LSTM-based deep learning model to detect fake news from article titles. It covers the full ML pipeline — from raw CSV data, through text preprocessing and one-hot encoding, to model training and evaluation using TensorFlow/Keras.</p>
    <div class="note"><p><strong>Dataset:</strong> fake_news.csv — 2,084 articles (after cleaning) with title, author, body text, and a binary label (0 = Real, 1 = Fake).</p></div>
  </section>

  <section>
    <h2>Pipeline</h2>
    <div class="pipeline">
      <div class="step">📂 Load CSV</div><div class="arrow">→</div>
      <div class="step">🧹 Clean &amp; Drop NAs</div><div class="arrow">→</div>
      <div class="step">✂️ Stem &amp; Stop Words</div><div class="arrow">→</div>
      <div class="step">🔢 One-Hot Encode</div><div class="arrow">→</div>
      <div class="step">📏 Pad Sequences</div><div class="arrow">→</div>
      <div class="step">🧠 LSTM Model</div><div class="arrow">→</div>
      <div class="step">📊 Evaluate</div>
    </div>
  </section>

  <section>
    <h2>Model architecture</h2>
    <div class="arch-row">
      <div class="arch-card">
        <span class="layer-tag badge-blue">Layer 1</span>
        <div class="layer-name">Embedding</div>
        <div class="layer-detail">vocab=10,000 · dims=40 · seq_len=20</div>
      </div>
      <div class="arch-card">
        <span class="layer-tag badge-purple">Layer 2</span>
        <div class="layer-name">Dropout</div>
        <div class="layer-detail">rate = 0.3 — prevents overfitting</div>
      </div>
      <div class="arch-card">
        <span class="layer-tag badge-teal">Layer 3</span>
        <div class="layer-name">LSTM</div>
        <div class="layer-detail">units = 100 · stateful = False</div>
      </div>
      <div class="arch-card">
        <span class="layer-tag badge-amber">Layer 4</span>
        <div class="layer-name">Dropout</div>
        <div class="layer-detail">rate = 0.3</div>
      </div>
      <div class="arch-card">
        <span class="layer-tag badge-coral">Layer 5</span>
        <div class="layer-name">Dense</div>
        <div class="layer-detail">units = 1 · activation = sigmoid</div>
      </div>
      <div class="arch-card">
        <span class="layer-tag badge-green">Compile</span>
        <div class="layer-name">Optimizer</div>
        <div class="layer-detail">Adam · loss = binary_crossentropy</div>
      </div>
    </div>
  </section>

  <section>
    <h2>Results</h2>
    <div class="metrics-grid">
      <div class="metric-card">
        <div class="metric-label">Train Acc</div>
        <div class="metric-val green">100%</div>
      </div>
      <div class="metric-card">
        <div class="metric-label">Val Acc (peak)</div>
        <div class="metric-val blue">90.0%</div>
      </div>
      <div class="metric-card">
        <div class="metric-label">Test Acc</div>
        <div class="metric-val amber">61.0%</div>
      </div>
      <div class="metric-card">
        <div class="metric-label">Epochs</div>
        <div class="metric-val coral">100</div>
      </div>
    </div>
    <div class="note"><p><strong>Observation:</strong> The model achieves 100% training accuracy but validation accuracy plateaus around epoch 7 (~90%) and degrades — a clear sign of <strong>overfitting</strong>. Early stopping and regularization are recommended.</p></div>

    <h3>Confusion matrix (test set)</h3>
    <div class="code-block">[[344  47]   <span class="cm"># True Negatives | False Positives</span>
 [221  76]]  <span class="cm"># False Negatives | True Positives</span></div>
  </section>

  <section>
    <h2>Quickstart</h2>
    <div class="code-block"><span class="cm"># 1. Install dependencies</span>
pip install tensorflow scikit-learn nltk pandas numpy

<span class="cm"># 2. Download NLTK stopwords</span>
<span class="kw">import</span> nltk
nltk.<span class="fn">download</span>(<span class="str">'stopwords'</span>)

<span class="cm"># 3. Place dataset and run notebook</span>
<span class="cm"># fake_news.csv → same directory as notebook</span>
jupyter notebook fake_news_detection.ipynb</div>
  </section>

  <section>
    <h2>Project structure</h2>
    <div class="file-tree"><span class="dir">fake-news-lstm/</span>
├── fake_news_detection.ipynb   <span class="comment"># main notebook</span>
├── fake_news.csv               <span class="comment"># dataset (not included in repo)</span>
├── requirements.txt
└── README.md</div>
  </section>

  <section>
    <h2>Known issues &amp; improvements</h2>
    <div class="improvement-grid">
      <div class="imp-item">
        <div class="imp-title">Early stopping</div>
        <div class="imp-desc">Add EarlyStopping callback to halt training at peak val accuracy (~epoch 7) instead of running all 100 epochs.</div>
      </div>
      <div class="imp-item">
        <div class="imp-title">Use full article text</div>
        <div class="imp-desc">Currently only article titles are used. Incorporating body text would significantly improve signal quality.</div>
      </div>
      <div class="imp-item">
        <div class="imp-title">Pretrained embeddings</div>
        <div class="imp-desc">Replace one-hot encoding with GloVe or Word2Vec embeddings to capture semantic relationships between words.</div>
      </div>
      <div class="imp-item">
        <div class="imp-title">Bidirectional LSTM</div>
        <div class="imp-desc">Using a Bidirectional LSTM layer can capture context from both directions, improving classification accuracy.</div>
      </div>
      <div class="imp-item">
        <div class="imp-title">TF-IDF baseline</div>
        <div class="imp-desc">Add a simpler Logistic Regression + TF-IDF baseline to compare against the LSTM approach fairly.</div>
      </div>
      <div class="imp-item">
        <div class="imp-title">Class imbalance</div>
        <div class="imp-desc">Inspect label distribution and apply class weights or oversampling if the dataset is skewed.</div>
      </div>
    </div>
  </section>

  <section>
    <h2>Dependencies</h2>
    <div class="code-block">tensorflow>=2.x
scikit-learn
nltk
pandas
numpy</div>
  </section>

</div>
</body>
</html>
