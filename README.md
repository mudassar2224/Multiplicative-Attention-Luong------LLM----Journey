
<div align="center">

# 🌐 English → Hindi Neural Machine Translation
### *Powered by Luong Multiplicative Attention*

<br>

[![Google Colab](https://img.shields.io/badge/Google_Colab-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)](https://colab.research.google.com)
[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)](https://www.tensorflow.org/)
[![Keras](https://img.shields.io/badge/Keras-D00000?style=for-the-badge&logo=keras&logoColor=white)](https://keras.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-22c55e?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/yourusername/english-hindi-nmt?style=for-the-badge&color=yellow)](https://github.com/yourusername/english-hindi-nmt)

<br>

<img src="https://user-images.githubusercontent.com/74038190/212284100-561aa473-3905-4a80-b561-0d28506553ee.gif" width="600" alt="AI Neural Network Banner"/>

<br>

**A production-ready Seq2Seq pipeline that translates English → Hindi using Luong Scaled Dot-Product Attention, built entirely in TensorFlow / Keras.**

</div>

---

## 📌 Table of Contents

- [✨ Project Highlights](#-project-highlights)
- [🧠 Model Architecture](#-model-architecture)
- [📊 Dataset & Preprocessing](#-dataset--preprocessing)
- [⚙️ Installation & Setup](#️-installation--setup)
- [🚀 Training the Model](#-training-the-model)
- [📈 Training Performance](#-training-performance)
- [🔮 Inference Pipeline](#-inference-pipeline)
- [📁 Project Structure](#-project-structure)
- [🛠️ Tech Stack](#️-tech-stack)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

## ✨ Project Highlights

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExOHp1bmg3OG4zdmtuNzA5OWtrZm4zMm5rYmtvdmd6c3lsYWlxbHRidCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3o7TKSjRrfIPjeiVyM/giphy.gif" width="220" align="right" alt="Neural Brain"/>
</div>

| Feature | Description |
|:--------|:------------|
| 🚀 **Luong Attention** | Scaled Dot-Product Attention (`use_scale=True`) for dynamic context weighting |
| 🧹 **Smart Cleaning** | Automatic removal of punctuation, digits + lower-casing for both languages |
| 🔤 **Dual Tokenization** | Independent English (`vocab ≈ 3.8k`) & Hindi (`vocab ≈ 4.5k`) tokenizers |
| ⚡ **Autoregressive Decoding** | Step-by-step state-passing inference for high-quality generation |
| 🛡️ **Early Stopping** | Monitors `val_loss` and restores best weights automatically |
| 📦 **Fully Reproducible** | Clean, modular, Colab-ready codebase |

<br clear="right"/>

---

## 🧠 Model Architecture

<div align="center">

### Layer Configuration

| Component              | Specification                          |
|------------------------|----------------------------------------|
| Embedding Dimension    | `512`                                  |
| Encoder LSTM           | `512 units` + `dropout=0.2`            |
| Decoder LSTM           | `512 units` + `dropout=0.2`            |
| Attention              | `tf.keras.layers.Attention(use_scale=True)` |
| Loss                   | `sparse_categorical_crossentropy`      |
| Optimizer              | `Adam`                                 |

</div>

<br>

### 🔄 Data Flow

```text
┌─────────────────────┐
│   English Input     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Embedding (512)    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐     ┌──────────────────────┐
│  Encoder LSTM       │────►│  Encoder States      │
│  (return_sequences) │     │  (h, c)              │
└──────────┬──────────┘     └──────────┬───────────┘
           │                           │
           │                           │
           ▼                           ▼
┌─────────────────────┐     ┌──────────────────────┐
│  Encoder Outputs    │     │  Decoder LSTM        │
└──────────┬──────────┘     │  (initial_state)     │
           │                └──────────┬───────────┘
           │                           │
           └───────────┬───────────────┘
                       ▼
            ┌─────────────────────┐
            │  Luong Attention    │
            │  (Scaled Dot-Prod)  │
            └──────────┬──────────┘
                       │
                       ▼
            ┌─────────────────────┐
            │ Concat(Context + H) │
            └──────────┬──────────┘
                       │
                       ▼
            ┌─────────────────────┐
            │ Dense + Softmax     │
            │ (Hindi Vocab Size)  │
            └─────────────────────┘
```

---

## 📊 Dataset & Preprocessing

**Source:** Sampled subset from the *Hindi-English Truncated Corpus* (TED talks category)

### Cleaning Pipeline

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

- Vocabulary sizes after processing:  
  - English → **≈ 3,843** tokens  
  - Hindi   → **≈ 4,556** tokens

---

## ⚙️ Installation & Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/english-hindi-nmt.git
cd english-hindi-nmt

# Create virtual environment (recommended)
python -m venv venv
source venv/bin/activate          # Linux / macOS
# venv\Scripts\activate           # Windows

# Install dependencies
pip install -r requirements.txt
```

**requirements.txt**
```txt
tensorflow>=2.10
numpy
pandas
scikit-learn
matplotlib
seaborn
tqdm
```

---

## 🚀 Training the Model

```python
import tensorflow as tf
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, LSTM, Dense, Concatenate, Embedding, Attention

latent_dim = 512

# 1. Encoder
encoder_input     = Input(shape=(None,), name="encoder_input")
encoder_embedded  = Embedding(eng_vocab_size, latent_dim, name="encoder_embedding")(encoder_input)
encoder_outputs, state_h, state_c = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="encoder_lstm"
)(encoder_embedded)
encoder_states = [state_h, state_c]

# 2. Decoder
decoder_input     = Input(shape=(None,), name="decoder_input")
decoder_embedded  = Embedding(hin_vocab_size, latent_dim, name="decoder_embedding")(decoder_input)
decoder_outputs, _, _ = LSTM(
    latent_dim, return_sequences=True, return_state=True, dropout=0.2, name="decoder_lstm"
)(decoder_embedded, initial_state=encoder_states)

# 3. Luong Attention + Output
attention_layer   = Attention(use_scale=True, name="luong_attention")
context_vector    = attention_layer([decoder_outputs, encoder_outputs])
decoder_combined  = Concatenate(axis=-1, name="concat_layer")([decoder_outputs, context_vector])
decoder_dense     = Dense(hin_vocab_size, activation="softmax", name="output_layer")
decoder_final_out = decoder_dense(decoder_combined)

# Full Model
model = Model([encoder_input, decoder_input], decoder_final_out)
model.compile(
    optimizer="adam",
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)

model.summary()
```

---

## 📈 Training Performance

Trained on **2,500 sentence pairs** with an **80 / 20** train-validation split.

```text
Epoch  1/50  •  accuracy: 0.6966  •  loss: 2.8609  •  val_loss: 2.0314  •  val_acc: 0.7286
Epoch  3/50  •  accuracy: 0.7221  •  loss: 1.9126  •  val_loss: 2.0175  •  val_acc: 0.7290
Epoch  5/50  •  accuracy: 0.7253  •  loss: 1.8200  •  val_loss: 2.0110  •  val_acc: 0.7311
Epoch  8/50  •  accuracy: 0.7344  •  loss: 1.6337  •  val_loss: 2.0289  •  val_acc: 0.7359
```

> Early Stopping automatically restores the best weights based on `val_loss`.

---

## 🔮 Inference Pipeline

Sample predictions from the evaluation loop:

```text
English   : we still dont know who her parents are who she is
Hindi Pred: हम अभी तक नहीं जानते हैं कि उसके मातापिता कौन हैं
--------------------------------------------------------------
English   : no keyboard
Hindi Pred: कोई कुंजीपटल नहीं
--------------------------------------------------------------
English   : and this particular balloon
Hindi Pred: और यह खास गुब्बारा
```

---

## 📁 Project Structure

```text
english-hindi-nmt/
├── data/
│   └── hindi_english_truncated.csv
├── notebooks/
│   └── English_Hindi_NMT.ipynb
├── src/
│   ├── data_cleaning.py
│   ├── model.py
│   ├── train.py
│   └── inference.py
├── saved_models/
├── requirements.txt
├── README.md
└── LICENSE
```

---

## 🛠️ Tech Stack

<div align="center">

| Category          | Tools                                      |
|-------------------|--------------------------------------------|
| **Deep Learning** | TensorFlow 2.x • Keras                     |
| **Language**      | Python 3.8+                                |
| **Data**          | Pandas • NumPy                             |
| **Visualization** | Matplotlib • Seaborn                       |
| **Environment**   | Google Colab / Local Jupyter               |

</div>

---

## 🤝 Contributing

Contributions, issues and feature requests are welcome!

1. Fork the repository  
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)  
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)  
4. Push to the branch (`git push origin feature/AmazingFeature`)  
5. Open a Pull Request  

---

## 📄 License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.

---

<div align="center">

### ⭐ If this project helped you, please give it a star!

<br>

<img src="https://user-images.githubusercontent.com/74038190/212284087-bbe7e430-757e-4901-8949-a6e1b3c8d5a1.gif" width="400" alt="Thank You"/>

**Made with ❤️ and TensorFlow**

</div>
```
