🌐 English-to-Hindi Neural Machine Translation

Powered by Luong Multiplicative Attention (TensorFlow/Keras)

<p align="center">
<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=28&pause=1000&color=36BCF7&center=true&vCenter=true&width=900&lines=English+to+Hindi+Neural+Machine+Translation;Luong+Attention+Seq2Seq+Model;TensorFlow+%7C+Deep+Learning+%7C+NLP;Machine+Translation+Project" />
</p>

<p align="center">
<img src="https://img.shields.io/badge/Python-3.10-blue?style=for-the-badge&logo=python"/>
<img src="https://img.shields.io/badge/TensorFlow-2.x-orange?style=for-the-badge&logo=tensorflow"/>
<img src="https://img.shields.io/badge/NLP-Seq2Seq-success?style=for-the-badge"/>
<img src="https://img.shields.io/badge/Attention-Luong-red?style=for-the-badge"/>
<img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge"/>
</p>

📖 Overview

This project implements an English → Hindi Neural Machine Translation system using a Sequence-to-Sequence LSTM Encoder–Decoder architecture enhanced with Luong Multiplicative Attention.

✨ Features

🚀 Luong Multiplicative Attention

🧹 Automatic preprocessing

🔤 Independent tokenizers

📦 TensorFlow/Keras implementation

📉 Early stopping

⚡ Autoregressive inference

📊 Easy to train and evaluate

🧠 Model Architecture

English Sentence
      │
Embedding
      │
Encoder LSTM
      │
Encoder Outputs ───────────────┐
                               │
                         Luong Attention
                               │
Hindi Input → Embedding → Decoder LSTM
                               │
                     Context Concatenation
                               │
                         Dense Softmax
                               │
                       Hindi Translation

⚙️ Hyperparameters

Parameter

Value

Embedding Dimension

512

Encoder Units

512

Decoder Units

512

Dropout

0.2

Optimizer

Adam

Loss

Sparse Categorical Crossentropy

📂 Dataset

Hindi-English Truncated Corpus (TED subset)

2,500 sentence pairs

80/20 train-validation split

🧹 Preprocessing

Lowercasing

Remove punctuation

Remove digits

Tokenization

Padding

🏗️ Training Pipeline

Clean text

Tokenize

Pad sequences

Build Encoder

Build Decoder

Apply Luong Attention

Train

Validate

Save Model

Perform inference

📈 Results

Metric

Value

Training Accuracy

73%

Validation Accuracy

73%

Loss

~1.63

Example:

English : no keyboard
Hindi   : कोई कुंजीपटल नहीं

English : and this particular balloon
Hindi   : और यह खास गुब्बारा

📁 Project Structure

project/
│
├── dataset/
├── notebooks/
├── models/
├── inference.py
├── train.py
├── requirements.txt
└── README.md

💻 Installation

git clone <repository-url>

cd project

pip install -r requirements.txt

python train.py

📦 Requirements

Python

TensorFlow

NumPy

Pandas

Matplotlib

Scikit-learn

🔮 Future Improvements

Transformer

BERT Encoder

Beam Search

BLEU Evaluation

Larger Dataset

Mixed Precision

Deployment with FastAPI

🤝 Contributing

Pull requests are welcome.

📜 License

MIT License

⭐ Support

If this project helped you, please give it a ⭐ on GitHub.
