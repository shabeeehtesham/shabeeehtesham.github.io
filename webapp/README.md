---
title: AI Portfolio — Shabee Ibn Ehtesham
emoji: 🧠
colorFrom: indigo
colorTo: pink
sdk: docker
app_port: 7860
pinned: false
---

# Portfolio — server-side webapp

A FastAPI backend (real Python inference) + Streamlit frontend for the four
from-scratch architectures showcased by this portfolio (MLP → RNN/LSTM →
Transformer → RAG). Every endpoint reconstructs the actual trained model
objects from the weight files committed under `model_weights/` and calls
the vendored project code under `webapp/vendor_projects/` directly — no
reimplementation, no mocked responses. This repo is self-contained: it
doesn't depend on the separate ML-projects repo at runtime (those `.py`
files are vendored copies, kept in sync manually when the model code or
weights change there).

## Run locally without Docker

From the repo root:

```bash
pip install -r webapp/requirements.txt
cd webapp
uvicorn backend.main:app --reload --port 8000          # terminal 1
cd frontend && streamlit run Home.py                     # terminal 2
```

Streamlit defaults to `BACKEND_URL=http://localhost:8000` (override via env var).

## Run with Docker

From the repo root (the Dockerfile needs this repo's root as build context,
since it copies `model_weights/` and `webapp/` — nothing else):

```bash
docker build -f webapp/Dockerfile -t ai-portfolio-webapp .
docker run -p 7860:7860 ai-portfolio-webapp
```

Open http://localhost:7860.

## Deploy to Hugging Face Spaces

1. Create a new Space, SDK = **Docker**.
2. Push this repo's contents to the Space's git remote — no changes needed,
   since the Space's Dockerfile resolves `model_weights/` and `webapp/`
   relative to the repo root exactly as this one does.
3. The Space will build the image and serve it on port 7860 automatically.
