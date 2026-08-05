<div align="center">

<img src="banner.svg" width="100%" alt="English to Hindi NMT Banner"/>

<br/>

![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![Keras](https://img.shields.io/badge/Keras-Seq2Seq-D00000?style=for-the-badge&logo=keras&logoColor=white)
![Colab](https://img.shields.io/badge/Google_Colab-Ready-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-22c55e?style=for-the-badge)

<br/>

> **A Seq2Seq deep learning pipeline translating English → Hindi using Luong Scaled Dot-Product Attention in TensorFlow/Keras.**

</div>

---

## 📌 Table of Contents

- [✨ Features](#-features)
- [🧠 Architecture](#-architecture)
- [📊 Dataset & Cleaning](#-dataset--cleaning)
- [⚙️ Model Code](#-model-code)
- [📈 Training Results](#-training-results)
- [🔮 Sample Translations](#-sample-translations)

---

## ✨ Features

| Feature | Description |
|---|---|
| 🎯 **Luong Attention** | Scaled dot-product attention (`use_scale=True`) — focuses decoder on relevant encoder states |
| 🧹 **Text Cleaning** | Strips punctuation, digits, normalises casing on both corpora |
| 🔤 **Dual Tokenization** | English vocab: `3,843` · Hindi vocab: `4,556` — tokenized independently |
| ⚡ **Autoregressive Decoding** | Step-by-step state-passing inference generates Hindi token by token |
| 🛡️ **Early Stopping** | Monitors `val_loss`, restores best weights automatically |

---

## 🧠 Architecture

```
[English Tokens] → [Encoder Embedding 512d] → [Encoder LSTM 512]
                                                       ↓ encoder_outputs
[Hindi Tokens]  → [Decoder Embedding 512d] → [Decoder LSTM 512] → [Luong Attention]
                                                                          ↓ context
                                               [Concat: decoder_out ‖ context_vector]
                                                                          ↓
                                                        [Dense Softmax → Hindi Token]
```

### ⚙️ Parameters

| Parameter | Value |
|---|---|
| Embedding / Latent Dim | `512` |
| Encoder LSTM Units | `512` (dropout `0.2`) |
| Decoder LSTM Units | `512` (dropout `0.2`) |
| Attention | `tf.keras.layers.Attention(use_scale=True)` |
| Loss | `sparse_categorical_crossentropy` |
| Optimizer | `Adam` |
| English Vocab Size | `3,843` |
| Hindi Vocab Size | `4,556` |
| Training Pairs | `2,000` (80%) |
| Validation Pairs | `500` (20%) |

---

## 📊 Dataset & Cleaning

**Source:** Hindi-English Truncated Corpus (`TED` category)

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

## ⚙️ Model Code

```python
import tensorflow as tf
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, LSTM, Dense, Concatenate, Embedding, Attention

latent_dim = 512

# 1. Encoder
encoder_input    = Input(shape=(None,), name="encoder_input")
encoder_embedded = Embedding(eng_vocab_size, latent_dim, name="encoder_embedding")(encoder_input)
encoder_outputs, state_h, state_c = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="encoder_lstm"
)(encoder_embedded)
encoder_states = [state_h, state_c]

# 2. Decoder
decoder_input    = Input(shape=(None,), name="decoder_input")
decoder_embedded = Embedding(hin_vocab_size, latent_dim, name="decoder_embedding")(decoder_input)
decoder_outputs, _, _ = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="decoder_lstm"
)(decoder_embedded, initial_state=encoder_states)

# 3. Luong Attention
attention_layer = Attention(use_scale=True, name="luong_attention")
context_vector  = attention_layer([decoder_outputs, encoder_outputs])
combined        = Concatenate(axis=-1, name="concat_layer")([decoder_outputs, context_vector])

# 4. Output
decoder_dense        = Dense(hin_vocab_size, activation="softmax", name="output_layer")
decoder_final_output = decoder_dense(combined)

model = Model([encoder_input, decoder_input], decoder_final_output)
model.compile(optimizer="adam", loss="sparse_categorical_crossentropy", metrics=["accuracy"])
```

---

## 📈 Training Results

Trained on **2,500 sentence pairs** · 80/20 split · up to 50 epochs with early stopping:

| Epoch | Train Acc | Train Loss | Val Loss | Val Acc |
|:---:|:---:|:---:|:---:|:---:|
| 1 | 69.66% | 2.8609 | 2.0314 | 72.86% |
| 3 | 72.21% | 1.9126 | 2.0175 | 72.90% |
| 5 | 72.53% | 1.8200 | 2.0110 | 73.11% |
| 8 | 73.44% | 1.6337 | 2.0289 | **73.59%** |

---

## 🔮 Sample Translations

```
English  :  we still dont know who her parents are who she is
Hindi    :  हम अभी तक नहीं जानते हैं कि उसके मातापिता कौन हैं
──────────────────────────────────────────────────────────────
English  :  no keyboard
Hindi    :  कोई कुंजीपटल नहीं
──────────────────────────────────────────────────────────────
English  :  and this particular balloon
Hindi    :  और यह खास गुब्बारा
```

---

<div align="center">

⭐ **Star this repo if it helped you!**

Made with ❤️ using **TensorFlow** · **Luong Attention** · **Python 3.8+**

</div>
