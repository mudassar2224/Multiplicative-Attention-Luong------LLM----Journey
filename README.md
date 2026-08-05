<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/> 
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>English-to-Hindi NMT | Luong Attention</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet"/>
<style>
  *{margin:0;padding:0;box-sizing:border-box;}
  :root{
    --bg:#0a0a0f;
    --bg2:#0f0f1a;
    --bg3:#14142b;
    --card:#16182e;
    --card2:#1c1f3a;
    --border:#2a2d52;
    --accent:#7c6af7;
    --accent2:#5eead4;
    --accent3:#f97316;
    --text:#e8e8f0;
    --muted:#8888aa;
    --code-bg:#0d0f1e;
  }
  body{background:var(--bg);color:var(--text);font-family:'Inter',sans-serif;line-height:1.7;overflow-x:hidden;}

  /* HERO */
  .hero{position:relative;text-align:center;padding:90px 20px 70px;overflow:hidden;}
  .hero-bg{position:absolute;inset:0;background:radial-gradient(ellipse 80% 60% at 50% 0%,#1a1040 0%,transparent 70%),radial-gradient(ellipse 50% 40% at 80% 80%,#0d2a2a 0%,transparent 60%);}
  .particles{position:absolute;inset:0;pointer-events:none;}
  .particle{position:absolute;border-radius:50%;animation:float linear infinite;opacity:.5;}
  @keyframes float{0%{transform:translateY(100vh) scale(0);}100%{transform:translateY(-20px) scale(1);opacity:0;}}
  .badge-row{display:flex;flex-wrap:wrap;justify-content:center;gap:10px;margin-bottom:28px;position:relative;z-index:2;}
  .badge{display:inline-flex;align-items:center;gap:6px;padding:6px 14px;border-radius:20px;font-size:12px;font-weight:600;letter-spacing:.5px;border:1px solid;animation:fadeUp .6s ease both;}
  .badge-purple{background:#1a1040;border-color:#6d5bf0;color:#a89bff;}
  .badge-teal{background:#0a1f1f;border-color:#14b8a6;color:#5eead4;}
  .badge-orange{background:#1a0e00;border-color:#f97316;color:#fdba74;}
  .badge-green{background:#0a1a0a;border-color:#22c55e;color:#86efac;}
  @keyframes fadeUp{from{opacity:0;transform:translateY(20px);}to{opacity:1;transform:translateY(0);}}

  h1.hero-title{font-size:clamp(2rem,5vw,3.8rem);font-weight:900;line-height:1.15;margin-bottom:16px;position:relative;z-index:2;
    background:linear-gradient(135deg,#a78bfa,#5eead4,#f97316);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;animation:fadeUp .8s .2s ease both;}
  .hero-sub{font-size:1.1rem;color:var(--muted);max-width:640px;margin:0 auto 36px;position:relative;z-index:2;animation:fadeUp .8s .4s ease both;}
  .lang-pill{display:inline-flex;align-items:center;gap:10px;background:var(--card);border:1px solid var(--border);border-radius:40px;padding:10px 24px;font-size:1rem;font-weight:500;position:relative;z-index:2;animation:fadeUp .8s .5s ease both;}
  .lang-sep{font-size:1.4rem;color:var(--accent);}

  /* NAV */
  .nav-bar{position:sticky;top:0;z-index:100;background:rgba(10,10,15,.85);backdrop-filter:blur(16px);border-bottom:1px solid var(--border);padding:12px 0;}
  .nav-inner{max-width:1000px;margin:0 auto;display:flex;gap:4px;flex-wrap:wrap;justify-content:center;}
  .nav-link{padding:6px 14px;border-radius:8px;font-size:13px;color:var(--muted);text-decoration:none;transition:all .2s;font-weight:500;}
  .nav-link:hover{background:var(--card2);color:var(--text);}

  /* SECTIONS */
  .section{max-width:1000px;margin:0 auto;padding:60px 20px;}
  .section-label{display:inline-flex;align-items:center;gap:8px;background:var(--card2);border:1px solid var(--border);border-radius:8px;padding:5px 14px;font-size:12px;font-weight:700;letter-spacing:1.2px;text-transform:uppercase;color:var(--accent);margin-bottom:18px;}
  h2.sec-title{font-size:clamp(1.5rem,3vw,2.2rem);font-weight:800;margin-bottom:14px;}
  .sec-desc{color:var(--muted);max-width:640px;margin-bottom:36px;font-size:1rem;}

  /* FEATURES GRID */
  .feat-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:16px;}
  .feat-card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:24px;transition:all .3s;position:relative;overflow:hidden;}
  .feat-card::before{content:'';position:absolute;inset:0;background:linear-gradient(135deg,var(--glow-color,#7c6af720),transparent 60%);opacity:0;transition:opacity .3s;}
  .feat-card:hover{border-color:var(--glow-color,var(--accent));transform:translateY(-4px);}
  .feat-card:hover::before{opacity:1;}
  .feat-icon{width:44px;height:44px;border-radius:12px;display:flex;align-items:center;justify-content:center;font-size:22px;margin-bottom:14px;background:var(--icon-bg,#1a1040);}
  .feat-title{font-weight:700;font-size:.95rem;margin-bottom:6px;}
  .feat-desc{color:var(--muted);font-size:.875rem;line-height:1.6;}

  /* ARCH */
  .arch-grid{display:grid;grid-template-columns:1fr 1fr;gap:20px;}
  @media(max-width:640px){.arch-grid{grid-template-columns:1fr;}}
  .arch-card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:24px;}
  .arch-card h3{font-size:1rem;font-weight:700;margin-bottom:16px;color:var(--accent2);}
  .param-row{display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border);}
  .param-row:last-child{border-bottom:none;}
  .param-key{font-size:.875rem;color:var(--muted);}
  .param-val{font-size:.875rem;font-weight:600;font-family:'JetBrains Mono',monospace;color:var(--accent);}

  /* FLOW DIAGRAM */
  .flow-diagram{background:var(--code-bg);border:1px solid var(--border);border-radius:16px;padding:30px;text-align:center;overflow-x:auto;}
  .flow-row{display:flex;align-items:center;justify-content:center;gap:8px;flex-wrap:wrap;margin:8px 0;}
  .flow-box{padding:10px 18px;border-radius:10px;font-size:.825rem;font-weight:600;font-family:'JetBrains Mono',monospace;border:1px solid;white-space:nowrap;}
  .flow-enc{background:#1a1040;border-color:#6d5bf0;color:#c4b5fd;}
  .flow-dec{background:#0a1f1f;border-color:#14b8a6;color:#5eead4;}
  .flow-att{background:#1a0d00;border-color:#f97316;color:#fdba74;}
  .flow-out{background:#0f1a0f;border-color:#22c55e;color:#86efac;}
  .flow-arr{color:var(--muted);font-size:1.1rem;}

  /* CODE */
  pre{background:var(--code-bg);border:1px solid var(--border);border-radius:12px;padding:24px;overflow-x:auto;font-family:'JetBrains Mono',monospace;font-size:.82rem;line-height:1.75;position:relative;}
  .code-header{display:flex;align-items:center;justify-content:space-between;background:#0d0f1e;border:1px solid var(--border);border-bottom:none;border-radius:12px 12px 0 0;padding:10px 20px;}
  .code-dots{display:flex;gap:6px;}
  .dot{width:12px;height:12px;border-radius:50%;}
  .dot-r{background:#ff5f57;}.dot-y{background:#ffbd2e;}.dot-g{background:#28ca41;}
  .code-lang{font-size:11px;font-weight:700;color:var(--muted);letter-spacing:1px;font-family:'JetBrains Mono',monospace;}
  .code-header+pre{border-radius:0 0 12px 12px;}
  .k{color:#c084fc;}.s{color:#5eead4;}.c{color:#64748b;font-style:italic;}.f{color:#60a5fa;}.n{color:#e8e8f0;}.num{color:#f97316;}

  /* TRAINING */
  .epoch-list{display:flex;flex-direction:column;gap:12px;}
  .epoch-row{background:var(--card);border:1px solid var(--border);border-radius:12px;padding:16px 20px;display:grid;grid-template-columns:80px 1fr 1fr 1fr 1fr;gap:12px;align-items:center;transition:border-color .2s;}
  .epoch-row:hover{border-color:var(--accent);}
  .ep-label{font-size:.8rem;font-weight:700;color:var(--accent);font-family:'JetBrains Mono',monospace;}
  .ep-metric{text-align:center;}
  .ep-metric-label{font-size:.7rem;color:var(--muted);margin-bottom:2px;}
  .ep-metric-val{font-size:.9rem;font-weight:700;font-family:'JetBrains Mono',monospace;}
  .ep-bar-wrap{grid-column:1/-1;height:3px;background:var(--bg2);border-radius:3px;margin-top:6px;}
  .ep-bar{height:3px;border-radius:3px;background:linear-gradient(90deg,var(--accent),var(--accent2));transition:width .8s ease;}

  /* INFERENCE */
  .infer-card{background:var(--card);border:1px solid var(--border);border-radius:16px;overflow:hidden;margin-bottom:14px;transition:all .3s;}
  .infer-card:hover{border-color:#5eead4;transform:translateX(4px);}
  .infer-en{padding:14px 20px;background:var(--card2);border-bottom:1px solid var(--border);}
  .infer-hi{padding:14px 20px;}
  .infer-lang-tag{font-size:10px;font-weight:700;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:5px;}
  .en-tag{color:#60a5fa;}.hi-tag{color:#f97316;}
  .infer-text-en{font-family:'JetBrains Mono',monospace;font-size:.875rem;}
  .infer-text-hi{font-size:1rem;font-weight:500;}

  /* STATS BAR */
  .stats-bar{background:var(--card2);border:1px solid var(--border);border-radius:16px;padding:28px;display:grid;grid-template-columns:repeat(auto-fit,minmax(120px,1fr));gap:20px;margin-bottom:48px;}
  .stat-item{text-align:center;}
  .stat-num{font-size:2rem;font-weight:900;background:linear-gradient(135deg,#a78bfa,#5eead4);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;}
  .stat-label{font-size:.8rem;color:var(--muted);margin-top:2px;font-weight:500;}

  /* FOOTER */
  .footer{text-align:center;padding:48px 20px;border-top:1px solid var(--border);color:var(--muted);font-size:.875rem;}
  .star-btn{display:inline-flex;align-items:center;gap:8px;background:linear-gradient(135deg,#7c6af7,#14b8a6);color:#fff;padding:12px 28px;border-radius:12px;text-decoration:none;font-weight:700;font-size:.95rem;margin-bottom:20px;transition:opacity .2s;border:none;cursor:pointer;}
  .star-btn:hover{opacity:.85;}

  /* ANIMATIONS */
  .reveal{opacity:0;transform:translateY(30px);transition:opacity .6s ease,transform .6s ease;}
  .reveal.visible{opacity:1;transform:translateY(0);}
  .glow-line{height:2px;background:linear-gradient(90deg,transparent,var(--accent),var(--accent2),transparent);margin:60px auto;max-width:600px;border-radius:2px;animation:glow-pulse 3s ease-in-out infinite;}
  @keyframes glow-pulse{0%,100%{opacity:.4;}50%{opacity:1;}}
</style>
</head>
<body>

<!-- PARTICLES -->
<div class="particles" id="particles"></div>

<!-- HERO -->
<div class="hero">
  <div class="hero-bg"></div>
  <div class="badge-row">
    <span class="badge badge-purple" style="animation-delay:.1s">🧠 Deep Learning</span>
    <span class="badge badge-teal" style="animation-delay:.2s">🔄 Seq2Seq</span>
    <span class="badge badge-orange" style="animation-delay:.3s">⚡ TensorFlow 2.x</span>
    <span class="badge badge-green" style="animation-delay:.4s">✅ MIT License</span>
  </div>
  <h1 class="hero-title">English → Hindi<br/>Neural Machine Translation</h1>
  <p class="hero-sub">A Seq2Seq deep learning pipeline powered by <strong style="color:#a78bfa;">Luong Multiplicative Attention</strong> — built with TensorFlow/Keras.</p>
  <div class="lang-pill">
    <span>🇬🇧 English</span>
    <span class="lang-sep">→</span>
    <span>🇮🇳 हिंदी</span>
  </div>
</div>

<!-- NAV -->
<nav class="nav-bar">
  <div class="nav-inner">
    <a class="nav-link" href="#features">Features</a>
    <a class="nav-link" href="#architecture">Architecture</a>
    <a class="nav-link" href="#dataset">Dataset</a>
    <a class="nav-link" href="#setup">Setup</a>
    <a class="nav-link" href="#training">Training</a>
    <a class="nav-link" href="#inference">Inference</a>
  </div>
</nav>

<!-- STATS BAR -->
<div class="section" style="padding-bottom:0">
  <div class="stats-bar reveal">
    <div class="stat-item"><div class="stat-num">512</div><div class="stat-label">Latent Dim</div></div>
    <div class="stat-item"><div class="stat-num">3,843</div><div class="stat-label">English Vocab</div></div>
    <div class="stat-item"><div class="stat-num">4,556</div><div class="stat-label">Hindi Vocab</div></div>
    <div class="stat-item"><div class="stat-num">2,500</div><div class="stat-label">Sentence Pairs</div></div>
    <div class="stat-item"><div class="stat-num">80%</div><div class="stat-label">Train Split</div></div>
    <div class="stat-item"><div class="stat-num">73.5%</div><div class="stat-label">Val Accuracy</div></div>
  </div>
</div>

<div class="glow-line"></div>

<!-- FEATURES -->
<section class="section" id="features">
  <div class="reveal">
    <div class="section-label">✨ Features</div>
    <h2 class="sec-title">What makes this model tick</h2>
    <p class="sec-desc">A complete end-to-end NMT pipeline with modern architectural choices and robust preprocessing.</p>
  </div>
  <div class="feat-grid reveal">
    <div class="feat-card" style="--glow-color:#7c6af7;--icon-bg:#1a1040;">
      <div class="feat-icon">🎯</div>
      <div class="feat-title">Luong Multiplicative Attention</div>
      <div class="feat-desc">Dynamically computes alignment weights using scaled dot-products (<code>use_scale=True</code>), letting the decoder focus on relevant encoder states at each step.</div>
    </div>
    <div class="feat-card" style="--glow-color:#14b8a6;--icon-bg:#0a1f1f;">
      <div class="feat-icon">🧹</div>
      <div class="feat-title">Automated Text Cleaning</div>
      <div class="feat-desc">Strips punctuation, digits, and normalises casing across both language corpora before tokenization — ensuring consistent input quality.</div>
    </div>
    <div class="feat-card" style="--glow-color:#f97316;--icon-bg:#1a0e00;">
      <div class="feat-icon">🔤</div>
      <div class="feat-title">Dual Tokenization & Padding</div>
      <div class="feat-desc">Independently tokenizes English (3,843 tokens) and Hindi (4,556 tokens) corpora with language-specific vocabularies for maximum precision.</div>
    </div>
    <div class="feat-card" style="--glow-color:#22c55e;--icon-bg:#0a1a0a;">
      <div class="feat-icon">⚡</div>
      <div class="feat-title">Autoregressive Inference</div>
      <div class="feat-desc">Step-by-step state-passing decoder — generates each Hindi token conditioned on all previously predicted tokens and full encoder context.</div>
    </div>
    <div class="feat-card" style="--glow-color:#f43f5e;--icon-bg:#1a0a0e;">
      <div class="feat-icon">🛡️</div>
      <div class="feat-title">Early Stopping</div>
      <div class="feat-desc">Monitors <code>val_loss</code> and restores the best weights automatically — preventing overfitting on the small 2,500-pair corpus.</div>
    </div>
    <div class="feat-card" style="--glow-color:#60a5fa;--icon-bg:#0a1220;">
      <div class="feat-icon">📊</div>
      <div class="feat-title">TED Corpus Dataset</div>
      <div class="feat-desc">Trained on a sampled subset of the Hindi-English Truncated Corpus (TED category) — real-world conversational sentence pairs.</div>
    </div>
  </div>
</section>

<div class="glow-line"></div>

<!-- ARCHITECTURE -->
<section class="section" id="architecture">
  <div class="reveal">
    <div class="section-label">🧠 Architecture</div>
    <h2 class="sec-title">Model design</h2>
    <p class="sec-desc">An encoder-decoder LSTM architecture with Luong attention bridging encoder context to the decoder at every output step.</p>
  </div>

  <div class="flow-diagram reveal" style="margin-bottom:24px;">
    <div class="flow-row">
      <div class="flow-box flow-enc">English Input Tokens</div>
      <div class="flow-arr">→</div>
      <div class="flow-box flow-enc">Encoder Embedding (512d)</div>
      <div class="flow-arr">→</div>
      <div class="flow-box flow-enc">Encoder LSTM (512)</div>
    </div>
    <div style="display:flex;justify-content:center;margin:6px 0;color:var(--muted);font-size:1.2rem;">↓ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓ encoder_outputs</div>
    <div class="flow-row">
      <div class="flow-box flow-dec">Hindi Input Tokens</div>
      <div class="flow-arr">→</div>
      <div class="flow-box flow-dec">Decoder Embedding (512d)</div>
      <div class="flow-arr">→</div>
      <div class="flow-box flow-dec">Decoder LSTM (512)</div>
      <div class="flow-arr">→</div>
      <div class="flow-box flow-att">Luong Attention</div>
    </div>
    <div style="display:flex;justify-content:center;margin:6px 0;color:var(--muted);font-size:1.2rem;">↓ context vector</div>
    <div class="flow-row">
      <div class="flow-box flow-dec">Concat [decoder_out ‖ context]</div>
      <div class="flow-arr">→</div>
      <div class="flow-box flow-out">Dense Softmax → Hindi Token</div>
    </div>
  </div>

  <div class="arch-grid reveal">
    <div class="arch-card">
      <h3>⚙️ Layer configuration</h3>
      <div class="param-row"><span class="param-key">Embedding dim</span><span class="param-val">512</span></div>
      <div class="param-row"><span class="param-key">Encoder LSTM units</span><span class="param-val">512</span></div>
      <div class="param-row"><span class="param-key">Decoder LSTM units</span><span class="param-val">512</span></div>
      <div class="param-row"><span class="param-key">Dropout rate</span><span class="param-val">0.2</span></div>
      <div class="param-row"><span class="param-key">Attention type</span><span class="param-val">Luong (scaled)</span></div>
      <div class="param-row"><span class="param-key">Loss function</span><span class="param-val">SparseCatCE</span></div>
      <div class="param-row"><span class="param-key">Optimizer</span><span class="param-val">Adam</span></div>
    </div>
    <div class="arch-card">
      <h3>📦 Vocabulary sizes</h3>
      <div class="param-row"><span class="param-key">English vocab size</span><span class="param-val">3,843</span></div>
      <div class="param-row"><span class="param-key">Hindi vocab size</span><span class="param-val">4,556</span></div>
      <div class="param-row"><span class="param-key">Training pairs</span><span class="param-val">2,000</span></div>
      <div class="param-row"><span class="param-key">Validation pairs</span><span class="param-val">500</span></div>
      <div class="param-row"><span class="param-key">Train / val split</span><span class="param-val">80 / 20</span></div>
      <div class="param-row"><span class="param-key">Max epochs</span><span class="param-val">50</span></div>
      <div class="param-row"><span class="param-key">Corpus source</span><span class="param-val">TED (HI-EN)</span></div>
    </div>
  </div>
</section>

<div class="glow-line"></div>

<!-- DATASET -->
<section class="section" id="dataset">
  <div class="reveal">
    <div class="section-label">📊 Dataset & Cleaning</div>
    <h2 class="sec-title">Preprocessing pipeline</h2>
    <p class="sec-desc">Raw text goes through a unified cleaning function before tokenization — applied identically to both English and Hindi corpora.</p>
  </div>
  <div class="reveal">
    <div class="code-header">
      <div class="code-dots"><div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div></div>
      <span class="code-lang">PYTHON</span>
    </div>
<pre><span class="k">import</span> <span class="n">string</span>

<span class="k">def</span> <span class="f">clean_text</span>(<span class="n">text</span>):
    <span class="c"># Normalise casing</span>
    <span class="n">text</span> = <span class="f">str</span>(<span class="n">text</span>).<span class="f">lower</span>()
    <span class="c"># Strip punctuation</span>
    <span class="k">for</span> <span class="n">char</span> <span class="k">in</span> <span class="n">string</span>.<span class="n">punctuation</span>:
        <span class="n">text</span> = <span class="n">text</span>.<span class="f">replace</span>(<span class="n">char</span>, <span class="s">""</span>)
    <span class="c"># Strip digits</span>
    <span class="k">for</span> <span class="n">digit</span> <span class="k">in</span> <span class="n">string</span>.<span class="n">digits</span>:
        <span class="n">text</span> = <span class="n">text</span>.<span class="f">replace</span>(<span class="n">digit</span>, <span class="s">""</span>)
    <span class="k">return</span> <span class="n">text</span>.<span class="f">strip</span>()

<span class="n">lines</span>[<span class="s">'english_sentence'</span>] = <span class="n">lines</span>[<span class="s">'english_sentence'</span>].<span class="f">apply</span>(<span class="n">clean_text</span>)
<span class="n">lines</span>[<span class="s">'hindi_sentence'</span>]   = <span class="n">lines</span>[<span class="s">'hindi_sentence'</span>].<span class="f">apply</span>(<span class="n">clean_text</span>)</pre>
  </div>
</section>

<div class="glow-line"></div>

<!-- SETUP -->
<section class="section" id="setup">
  <div class="reveal">
    <div class="section-label">⚙️ Setup & Code</div>
    <h2 class="sec-title">Complete model definition</h2>
    <p class="sec-desc">Copy-paste ready TensorFlow/Keras implementation of the full Encoder-Decoder-Attention architecture.</p>
  </div>
  <div class="reveal">
    <div class="code-header">
      <div class="code-dots"><div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div></div>
      <span class="code-lang">PYTHON — model.py</span>
    </div>
<pre><span class="k">import</span> <span class="n">tensorflow</span> <span class="k">as</span> <span class="n">tf</span>
<span class="k">from</span> <span class="n">tensorflow.keras.models</span> <span class="k">import</span> <span class="n">Model</span>
<span class="k">from</span> <span class="n">tensorflow.keras.layers</span> <span class="k">import</span> (
    <span class="n">Input</span>, <span class="n">LSTM</span>, <span class="n">Dense</span>, <span class="n">Concatenate</span>, <span class="n">Embedding</span>, <span class="n">Attention</span>
)

<span class="n">latent_dim</span> = <span class="num">512</span>

<span class="c"># ── 1. ENCODER ──────────────────────────────────────────</span>
<span class="n">encoder_input</span>    = <span class="f">Input</span>(shape=(<span class="k">None</span>,), name=<span class="s">"encoder_input"</span>)
<span class="n">encoder_embedded</span> = <span class="f">Embedding</span>(<span class="n">eng_vocab_size</span>, <span class="n">latent_dim</span>,
                              name=<span class="s">"encoder_embedding"</span>)(<span class="n">encoder_input</span>)
<span class="n">encoder_outputs</span>, <span class="n">state_h</span>, <span class="n">state_c</span> = <span class="f">LSTM</span>(
    <span class="n">latent_dim</span>, return_sequences=<span class="k">True</span>, return_state=<span class="k">True</span>,
    dropout=<span class="num">0.2</span>, name=<span class="s">"encoder_lstm"</span>
)(<span class="n">encoder_embedded</span>)
<span class="n">encoder_states</span> = [<span class="n">state_h</span>, <span class="n">state_c</span>]

<span class="c"># ── 2. DECODER ──────────────────────────────────────────</span>
<span class="n">decoder_input</span>    = <span class="f">Input</span>(shape=(<span class="k">None</span>,), name=<span class="s">"decoder_input"</span>)
<span class="n">decoder_embedded</span> = <span class="f">Embedding</span>(<span class="n">hin_vocab_size</span>, <span class="n">latent_dim</span>,
                              name=<span class="s">"decoder_embedding"</span>)(<span class="n">decoder_input</span>)
<span class="n">decoder_outputs</span>, _, _ = <span class="f">LSTM</span>(
    <span class="n">latent_dim</span>, return_sequences=<span class="k">True</span>, return_state=<span class="k">True</span>,
    dropout=<span class="num">0.2</span>, name=<span class="s">"decoder_lstm"</span>
)(<span class="n">decoder_embedded</span>, initial_state=<span class="n">encoder_states</span>)

<span class="c"># ── 3. LUONG ATTENTION ──────────────────────────────────</span>
<span class="n">attention_layer</span>  = <span class="f">Attention</span>(use_scale=<span class="k">True</span>, name=<span class="s">"luong_attention"</span>)
<span class="n">context_vector</span>   = <span class="n">attention_layer</span>([<span class="n">decoder_outputs</span>, <span class="n">encoder_outputs</span>])
<span class="n">combined</span>         = <span class="f">Concatenate</span>(axis=-<span class="num">1</span>, name=<span class="s">"concat_layer"</span>)(
                       [<span class="n">decoder_outputs</span>, <span class="n">context_vector</span>])

<span class="c"># ── 4. OUTPUT LAYER ─────────────────────────────────────</span>
<span class="n">decoder_dense</span>       = <span class="f">Dense</span>(<span class="n">hin_vocab_size</span>, activation=<span class="s">"softmax"</span>,
                             name=<span class="s">"output_layer"</span>)
<span class="n">decoder_final_output</span> = <span class="n">decoder_dense</span>(<span class="n">combined</span>)

<span class="n">model</span> = <span class="f">Model</span>([<span class="n">encoder_input</span>, <span class="n">decoder_input</span>], <span class="n">decoder_final_output</span>)
<span class="n">model</span>.<span class="f">compile</span>(optimizer=<span class="s">"adam"</span>, loss=<span class="s">"sparse_categorical_crossentropy"</span>,
              metrics=[<span class="s">"accuracy"</span>])</pre>
  </div>
</section>

<div class="glow-line"></div>

<!-- TRAINING -->
<section class="section" id="training">
  <div class="reveal">
    <div class="section-label">📈 Training Performance</div>
    <h2 class="sec-title">Epoch-by-epoch results</h2>
    <p class="sec-desc">Trained on 2,500 sentence pairs with an 80/20 train-validation split over 50 epochs with early stopping on <code>val_loss</code>.</p>
  </div>
  <div class="epoch-list reveal">
    <div class="epoch-row">
      <div class="ep-label">Epoch 1</div>
      <div class="ep-metric"><div class="ep-metric-label">Train Acc</div><div class="ep-metric-val" style="color:#a78bfa">69.66%</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Train Loss</div><div class="ep-metric-val" style="color:#f97316">2.8609</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Loss</div><div class="ep-metric-val" style="color:#60a5fa">2.0314</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Acc</div><div class="ep-metric-val" style="color:#5eead4">72.86%</div></div>
      <div class="ep-bar-wrap"><div class="ep-bar" style="width:70%"></div></div>
    </div>
    <div class="epoch-row">
      <div class="ep-label">Epoch 3</div>
      <div class="ep-metric"><div class="ep-metric-label">Train Acc</div><div class="ep-metric-val" style="color:#a78bfa">72.21%</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Train Loss</div><div class="ep-metric-val" style="color:#f97316">1.9126</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Loss</div><div class="ep-metric-val" style="color:#60a5fa">2.0175</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Acc</div><div class="ep-metric-val" style="color:#5eead4">72.90%</div></div>
      <div class="ep-bar-wrap"><div class="ep-bar" style="width:77%"></div></div>
    </div>
    <div class="epoch-row">
      <div class="ep-label">Epoch 5</div>
      <div class="ep-metric"><div class="ep-metric-label">Train Acc</div><div class="ep-metric-val" style="color:#a78bfa">72.53%</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Train Loss</div><div class="ep-metric-val" style="color:#f97316">1.8200</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Loss</div><div class="ep-metric-val" style="color:#60a5fa">2.0110</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Acc</div><div class="ep-metric-val" style="color:#5eead4">73.11%</div></div>
      <div class="ep-bar-wrap"><div class="ep-bar" style="width:82%"></div></div>
    </div>
    <div class="epoch-row">
      <div class="ep-label">Epoch 8</div>
      <div class="ep-metric"><div class="ep-metric-label">Train Acc</div><div class="ep-metric-val" style="color:#a78bfa">73.44%</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Train Loss</div><div class="ep-metric-val" style="color:#f97316">1.6337</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Loss</div><div class="ep-metric-val" style="color:#60a5fa">2.0289</div></div>
      <div class="ep-metric"><div class="ep-metric-label">Val Acc</div><div class="ep-metric-val" style="color:#5eead4">73.59%</div></div>
      <div class="ep-bar-wrap"><div class="ep-bar" style="width:89%"></div></div>
    </div>
  </div>
</section>

<div class="glow-line"></div>

<!-- INFERENCE -->
<section class="section" id="inference">
  <div class="reveal">
    <div class="section-label">🔮 Inference Pipeline</div>
    <h2 class="sec-title">Sample translations</h2>
    <p class="sec-desc">Output predictions sampled from the autoregressive evaluation loop — English source → Hindi predicted translation.</p>
  </div>
  <div class="reveal">
    <div class="infer-card">
      <div class="infer-en">
        <div class="infer-lang-tag en-tag">🇬🇧 English Source</div>
        <div class="infer-text-en">we still dont know who her parents are who she is</div>
      </div>
      <div class="infer-hi">
        <div class="infer-lang-tag hi-tag">🇮🇳 Hindi Prediction</div>
        <div class="infer-text-hi">हम अभी तक नहीं जानते हैं कि उसके मातापिता कौन हैं</div>
      </div>
    </div>
    <div class="infer-card">
      <div class="infer-en">
        <div class="infer-lang-tag en-tag">🇬🇧 English Source</div>
        <div class="infer-text-en">no keyboard</div>
      </div>
      <div class="infer-hi">
        <div class="infer-lang-tag hi-tag">🇮🇳 Hindi Prediction</div>
        <div class="infer-text-hi">कोई कुंजीपटल नहीं</div>
      </div>
    </div>
    <div class="infer-card">
      <div class="infer-en">
        <div class="infer-lang-tag en-tag">🇬🇧 English Source</div>
        <div class="infer-text-en">and this particular balloon</div>
      </div>
      <div class="infer-hi">
        <div class="infer-lang-tag hi-tag">🇮🇳 Hindi Prediction</div>
        <div class="infer-text-hi">और यह खास गुब्बारा</div>
      </div>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer class="footer">
  <div style="margin-bottom:20px;font-size:1.2rem;">🌟</div>
  <a class="star-btn" href="#">⭐ Star this repository</a>
  <br/>
  <p style="margin-top:16px;">Built with ❤️ using <strong style="color:#a78bfa;">TensorFlow</strong> · <strong style="color:#5eead4;">Luong Attention</strong> · <strong style="color:#f97316;">Python 3.8+</strong></p>
  <p style="margin-top:8px;font-size:.8rem;">MIT License · Hindi-English TED Corpus · Google Colab Compatible</p>
</footer>

<script>
// Particles
const pc=document.getElementById('particles');
for(let i=0;i<40;i++){
  const p=document.createElement('div');
  p.className='particle';
  const s=Math.random()*5+2;
  const colors=['#7c6af7','#14b8a6','#f97316','#22c55e','#60a5fa'];
  p.style.cssText=`width:${s}px;height:${s}px;left:${Math.random()*100}%;
    background:${colors[Math.floor(Math.random()*colors.length)]};
    animation-duration:${Math.random()*10+8}s;
    animation-delay:${Math.random()*10}s;`;
  pc.appendChild(p);
}
// Scroll reveal
const els=document.querySelectorAll('.reveal');
const obs=new IntersectionObserver(entries=>{
  entries.forEach(e=>{if(e.isIntersecting){e.target.classList.add('visible');}});
},{threshold:.1});
els.forEach(el=>obs.observe(el));
</script>
</body>
</html>
