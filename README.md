
<div align="center">

  # 🌐 English-to-Hindi Neural Machine Translation
  ### *Powered by Luong Multiplicative Attention*

  ![Python](https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python&logoColor=white)
  ![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
  ![Google Colab](https://img.shields.io/badge/Google_Colab-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)
  ![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

  <br />

  <img src="https://user-images.githubusercontent.com/74038190/212284100-561aa473-3905-4a80-b561-0d28506553ee.gif" width="600" alt="Translation Animation Header" />

  <p align="center">
    <b>A Seq2Seq Deep Learning Pipeline converting English text into natural Hindi using Luong Scaled Dot-Product Attention.</b>
  </p>

  ---

</div>

## 📌 Table of Contents
* [✨ Features](#-features)
* [🧠 Model Architecture](#-model-architecture)
* [📊 Dataset & Preprocessing](#-dataset--preprocessing)
* [🚀 Getting Started](#-getting-started)
* [⚡ Code & Model Setup](#-code--model-setup)
* [📈 Training Results](#-training-results)
* [🔮 Inference Pipeline](#-inference-pipeline)
* [🤝 Contributing](#-contributing)

---

## ✨ Features

<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExOHp1bmg3OG4zdmtuNzA5OWtrZm4zMm5rYmtvdmd6c3lsYWlxbHRidCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3o7TKSjRrfIPjeiVyM/giphy.gif" align="right" width="220" alt="Brain GIF"/>

* 🚀 **Luong (Multiplicative) Attention:** Dynamically calculates scaled dot-product attention scores across encoder states.
* 🧹 **Automated Text Cleaning:** Normalizes casing, strips numerical characters, and cleans punctuation.
* 🔤 **Dual Tokenization:** Prepares separate tokenized vocabularies for both English and Hindi text.
* ⚙️ **Autoregressive Inference:** Includes explicit Encoder-Decoder inference models for step-by-step token decoding.
* ⏱️ **Smart Training:** Utilizes `EarlyStopping` callbacks based on validation loss to prevent overfitting.

<br clear="right" />

---

## 🧠 Model Architecture


```

[English Input] ──► [Encoder Embedding] ──► [Encoder LSTM] ──┐
│
(Attention States)
│
[Hindi Target]  ──► [Decoder Embedding] ──► [Decoder LSTM] ──┼──► [Luong Attention Layer]
│                 │
▼                 ▼
[Concat Context & Output Vector]
│
▼
[Dense Softmax Layer]
│
▼
[Predicted Hindi Word]

```

| Component | Layer / Parameter | Config / Dimension |
| :--- | :--- | :--- |
| **Encoder** | Embedding Vector | `latent_dim = 512` |
| | Encoder LSTM | `512 units`, `Dropout = 0.2` |
| **Decoder** | Embedding Vector | `latent_dim = 512` |
| | Decoder LSTM | `512 units`, `Dropout = 0.2` |
| **Attention** | `tf.keras.layers.Attention` | `use_scale=True` *(Multiplicative)* |
| **Output** | Dense Layer | `Softmax` over `hin_vocab_size` |

---

## 📊 Dataset & Preprocessing

The project uses the **Hindi-English Truncated Corpus** (sampled from TED talk transcripts):

* **English Vocabulary Size:** `~3,843`
* **Hindi Vocabulary Size:** `~4,556`
* **Max English Sequence Length:** `20`
* **Max Hindi Sequence Length:** `27`

```python
# Text Cleaning Routine
def clean_text(text):
    text = str(text).lower()
    for char in string.punctuation:
        text = text.replace(char, "")
    for digit in string.digits:
        text = text.replace(digit, "")
    return text.strip()

```

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install numpy pandas tensorflow

```

### Installation & Execution

1. Clone the repository:
```bash
git clone [https://github.com/your-username/english-to-hindi-luong-attention.git](https://github.com/your-username/english-to-hindi-luong-attention.git)
cd english-to-hindi-luong-attention

```


2. Open and run the notebook in **Google Colab**:


---

## ⚡ Code & Model Setup

```python
import tensorflow as tf
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, LSTM, Dense, Concatenate, Embedding, Attention

# Luong / Multiplicative (Dot-Product) Attention Core Construction
latent_dim = 512

# Encoder
encoder_input = Input(shape=(None,), name="encoder_input")
encoder_embedded = Embedding(eng_vocab_size, latent_dim, name="encoder_embedding")(encoder_input)
encoder_outputs, state_h, state_c = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="encoder_lstm"
)(encoder_embedded)
encoder_states = [state_h, state_c]

# Decoder
decoder_input = Input(shape=(None,), name="decoder_input")
decoder_embedded = Embedding(hin_vocab_size, latent_dim, name="decoder_embeddings")(decoder_input)
decoder_outputs, _, _ = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="decoder_lstm"
)(decoder_embedded, initial_state=encoder_states)

# Multiplicative Attention Layer
attention_layer = Attention(use_scale=True, name="luong_attention")
context_vector = attention_layer([decoder_outputs, encoder_outputs])
decoder_combined_context = Concatenate(axis=-1, name="concat_layer")([decoder_outputs, context_vector])

# Output
decoder_dense = Dense(hin_vocab_size, activation="softmax", name="output_layer")
decoder_final_output = decoder_dense(decoder_combined_context)

# Model Compilation
model = Model([encoder_input, decoder_input], decoder_final_output)
model.compile(optimizer="adam", loss="sparse_categorical_crossentropy", metrics=["accuracy"])

```

---

## 📈 Training Results

The model is trained using **Early Stopping** on `val_loss`:

```text
Epoch 1/50 ── loss: 2.8609 ── accuracy: 0.6966 ── val_loss: 2.0314 ── val_accuracy: 0.7286
Epoch 3/50 ── loss: 1.9126 ── accuracy: 0.7221 ── val_loss: 2.0175 ── val_accuracy: 0.7290
Epoch 5/50 ── loss: 1.8200 ── accuracy: 0.7253 ── val_loss: 2.0110 ── val_accuracy: 0.7311
Epoch 8/50 ── loss: 1.6337 ── accuracy: 0.7344 ── val_loss: 2.0289 ── val_accuracy: 0.7359

```

---

## 🔮 Inference Pipeline

The autoregressive decoding loop translates English input sequences into predicted Hindi output token-by-token:

```python
# Sample Execution Output
English:     we still dont know who her parents are who she is
Hindi Pred:  हम अभी तक नहीं जानते हैं कि उसके मातापिता कौन हैं

English:     no keyboard
Hindi Pred:  कोई कुंजीपटल नहीं

```

---

### ⭐ Star this repository if you found it useful!
