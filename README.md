
<div align="center">

  # 🌐 English-to-Hindi Neural Machine Translation
  ### *Powered by Luong Multiplicative Attention*

  <p align="center">
    <a href="https://colab.research.google.com"><img src="https://img.shields.io/badge/Google_Colab-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white" alt="Colab"/></a>
    <img src="https://img.shields.io/badge/Python-3.8%2B-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
    <img src="https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white" alt="TensorFlow"/>
    <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License"/>
  </p>

  <br />

  <img src="https://user-images.githubusercontent.com/74038190/212284100-561aa473-3905-4a80-b561-0d28506553ee.gif" width="550" alt="AI Header Banner" />

  <p align="center">
    <b>A Seq2Seq Deep Learning Pipeline translating English to Hindi using Luong Scaled Dot-Product Attention in TensorFlow/Keras.</b>
  </p>

</div>

---

## 📌 Table of Contents
* [✨ Project Features](#-project-features)
* [🧠 Model Architecture](#-model-architecture)
* [📊 Dataset & Data Cleaning](#-dataset--data-cleaning)
* [⚙️ Setup & Model Execution](#-setup--model-execution)
* [📈 Training Performance](#-training-performance)
* [🔮 Inference Pipeline](#-inference-pipeline)

---

## ✨ Project Features

<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExOHp1bmg3OG4zdmtuNzA5OWtrZm4zMm5rYmtvdmd6c3lsYWlxbHRidCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3o7TKSjRrfIPjeiVyM/giphy.gif" align="right" width="200" alt="Brain Animation"/>

* 🚀 **Luong Multiplicative Attention:** Dynamically calculates attention context weights using scaled dot-products (`use_scale=True`).
* 🧹 **Automated Text Cleaning:** Strips punctuation, numerical values, and standardizes casing across both language inputs.
* 🔤 **Dual Tokenization & Padding:** Tokenizes English and Hindi corpora independently (`eng_vocab_size = 3843`, `hin_vocab_size = 4556`).
* ⚡ **Autoregressive Inference:** Step-by-step state-passing inference decoding for target sequence output generation.
* 🛡️ **Early Stopping Integration:** Prevents overfitting by tracking `val_loss` with automatic restoration of optimal weights.

<br clear="right" />

---

## 🧠 Model Architecture

<table>
  <tr>
    <td width="50%">

### 📐 Layer Configuration
* **Embedding Latent Dimension:** `512`
* **Encoder LSTM Units:** `512` (`dropout=0.2`)
* **Decoder LSTM Units:** `512` (`dropout=0.2`)
* **Attention mechanism:** `tf.keras.layers.Attention(use_scale=True)`
* **Loss Function:** `sparse_categorical_crossentropy`
* **Optimizer:** `Adam`

    </td>
    <td width="50%">

### 🔄 Data Flow Pipeline
```text
[English Input] ──► [Encoder LSTM] ──┐
                                     ├──► [Luong Attention]
[Hindi Input]   ──► [Decoder LSTM] ──┘         │
                                               ▼
                                  [Concat Context + Hidden]
                                               │
                                               ▼
                                     [Dense Softmax Output]

```

```
</td>

```

---

## 📊 Dataset & Data Cleaning

Data source: Sampled subset from the **Hindi-English Truncated Corpus** (`TED` category).

```python
import string

def clean_text(text):
    text = str(text).lower()
    for char in string.punctuation:
        text = text.replace(char, "")
    for digit in string.digits:
        text = text.replace(digit, "")
    return text.strip()

lines['english_sentence'] = lines['english_sentence'].apply(clean_text)
lines['hindi_sentence']   = lines['hindi_sentence'].apply(clean_text)

```

---

## ⚙️ Setup & Model Execution

```python
import tensorflow as tf
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, LSTM, Dense, Concatenate, Embedding, Attention

latent_dim = 512

# 1. Encoder
encoder_input = Input(shape=(None,), name="encoder_input")
encoder_embedded = Embedding(eng_vocab_size, latent_dim, name="encoder_embedding")(encoder_input)
encoder_outputs, state_h, state_c = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="encoder_lstm"
)(encoder_embedded)
encoder_states = [state_h, state_c]

# 2. Decoder
decoder_input = Input(shape=(None,), name="decoder_input")
decoder_embedded = Embedding(hin_vocab_size, latent_dim, name="deccoder_embeddings")(decoder_input)
decoder_outputs, _, _ = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="decoder_lstm"
)(decoder_embedded, initial_state=encoder_states)

# 3. Luong Attention & Output
attention_layer = Attention(use_scale=True, name="luong_attention")
context_vector = attention_layer([decoder_outputs, encoder_outputs])
decoder_combined_context = Concatenate(axis=-1, name="concat_layer")([decoder_outputs, context_vector])

decoder_dense = Dense(hin_vocab_size, activation="softmax", name="output_layer")
decoder_final_output = decoder_dense(decoder_combined_context)

model = Model([encoder_input, decoder_input], decoder_final_output)
model.compile(optimizer="adam", loss="sparse_categorical_crossentropy", metrics=["accuracy"])

```

---

## 📈 Training Performance

The model was trained on **2,500 sentence pairs** with an 80/20 train-validation split:

```text
Epoch 1/50 ── accuracy: 0.6966 ── loss: 2.8609 ── val_loss: 2.0314 ── val_accuracy: 0.7286
Epoch 3/50 ── accuracy: 0.7221 ── loss: 1.9126 ── val_loss: 2.0175 ── val_accuracy: 0.7290
Epoch 5/50 ── accuracy: 0.7253 ── loss: 1.8200 ── val_loss: 2.0110 ── val_accuracy: 0.7311
Epoch 8/50 ── accuracy: 0.7344 ── loss: 1.6337 ── val_loss: 2.0289 ── val_accuracy: 0.7359

```

---

## 🔮 Inference Pipeline

Output predictions sampled from the evaluation loop:

```text
English:    we still dont know who her parents are who she is
Hindi Pred: हम अभी तक नहीं जानते हैं कि उसके मातापिता कौन हैं
--------------------------------------------------
English:    no keyboard
Hindi Pred: कोई कुंजीपटल नहीं
--------------------------------------------------
English:    and this particular balloon
Hindi Pred: और यह खास गुब्बारा

```

---

### ⭐ Feel free to star this repository if it helped you!
