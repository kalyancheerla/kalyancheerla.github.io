---
title: "FHE vs Garbled Circuits: Making MLaaS possible through Secure Computation"
author: Kalyan Cheerla
pubDatetime: 2025-10-11T22:00:00Z
description: A simple walkthrough of how Fully Homomorphic Encryption and Garbled Circuits help make Machine Learning private, based on my recent paper comparing both for secure neural network inference.
tags:
  - research
  - cryptography
  - machine-learning
  - privacy
  - fhe
  - garbled-circuits
---

## 🧠 TL;DR - Why this matters to you?

Machine Learning-as-a-Service (MLaaS) is amazing, until privacy becomes a dealbreaker. How can you use a model without revealing your data? And how can a model owner protect their intellectual property?

Enter **Privacy-Preserving Machine Learning (PPML)**. Using cryptography, we can:
- Run models on encrypted data.
- Keep the model itself secret.
- Achieve predictions without either side exposing sensitive info.

In my recent work, I compared two heavyweights in this space:
- **Fully Homomorphic Encryption (FHE)** - do math directly on encrypted data.
- **Garbled Circuits (GC)** - encrypt the computation itself.

Both let MLaaS happen securely, your data and the model stay private, but predictions still happen.

---

## 🔍 FHE - Encrypt Everything, Compute Securely

Imagine sending your data in a locked box to a server, and the server can manipulate it without ever opening it. That's FHE.

- You encrypt your input `x`.
- The server computes `f(x)` **on the encrypted input**.
- You decrypt the result locally.

Formally:
```
Enc(x) → Server computes f over Enc(x) → Enc(y)
y = Dec(Enc(y))
```

---

## ⚡ Garbled Circuits - Encrypt the Computation

GC flips the script: instead of encrypting data, you encrypt the *neural network itself* as a Boolean circuit.

- The model owner "garbles" their network into a Boolean circuit.
- The client gets encrypted input labels via *Oblivious Transfer*.
- Both parties evaluate the circuit step by step.
- Client receives the output without seeing the model. Server never sees the input.

Formally:
```
GenLabels(x) → x_labels
G(f, labels) → GC, GT
Eval(GC, GT, x_labels) → y_labels
Decode(y_labels) → y
```

---

## 📖 Wanna know more?

Checkout the arXiv paper or complete thesis below.

### Paper:

Cheerla, K., Ben Othmane, L., & Morozov, K. (2025). Comparison of Fully Homomorphic Encryption and Garbled Circuit Techniques in Privacy-Preserving Machine Learning Inference [Preprint]. arXiv. [arxiv.org/abs/2510.07457v1](https://arxiv.org/abs/2510.07457v1)

If you'd like to reference it:
```bibtex
@article{cheerla2025fhevsgc,
  title={Comparison of Fully Homomorphic Encryption and Garbled Circuit Techniques in Privacy-Preserving Machine Learning Inference},
  author={Cheerla, Kalyan and Ben Othmane, Lotfi and Morozov, Kirill},
  journal={arXiv preprint arXiv:2510.07457},
  year={2025}
}
```
### Thesis:

Cheerla, Kalyan. Comparison of Fully Homomorphic Encryption and Garbled Circuits approaches in Privacy-Preserving Machine Learning, thesis, July 2025; Denton, Texas. ([https://digital.library.unt.edu/ark:/67531/metadc2481661/](https://digital.library.unt.edu/ark:/67531/metadc2481661/)), University of North Texas Libraries, UNT Digital Library, [https://digital.library.unt.edu](https://digital.library.unt.edu).

---

## 🧪 Want to Try It?

Find the complete open-sourced code: 🔗 [github.com/kalyancheerla/snni-fhe-gc](https://github.com/kalyancheerla/snni-fhe-gc)

It includes:
- Complete details about setup (emp-tool, emp-ot, TinyGarble2.0, SEAL).
- Two-layer neural network implementations in both **FHE (SEAL)** and **GC (TinyGarble2.0)**.
- Benchmarks for round-trip time, peak memory usage, communication overhead, communication rounds, and inference output error.

---

## 👋 Final Thoughts

This project started as my Master's thesis at UNT and turned into a deeper exploration
of how cryptography and AI meet in the real world. And if you end up using our work, feel free to cite, share, or reach out.

🔐 Privacy doesn't have to be a mystery: it just needs better math.
