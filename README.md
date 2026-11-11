# Shabee Ibn Ehtesham — Portfolio

A multi-page personal portfolio: **Home**, **Projects** (a scrollable picker), **Certificates**, and **About**, plus a project detail page ("Neurons to Transformers") with four real, live demos — client-side in the browser *and* server-side via a FastAPI + Streamlit webapp — running on actual trained model weights, no mocked data anywhere.

This repo is the portfolio itself. The ML projects it showcases (the from-scratch MLP → RNN/LSTM → Transformer → RAG progression) live in their own separate repo: **[evolution-to-ai-agents-from-ml](https://github.com/shabeeehtesham/evolution-to-ai-agents-from-ml)**. This repo vendors in just what the live demos need to run standalone — it doesn't depend on that repo at runtime.

Live at **https://shabeeehtesham.github.io/**.

## Running the static site locally

This is a plain static site at the repo root (no build step, no framework) — but it **must be served over `http://`, not opened directly as a `file://` URL**. The live demos `fetch()` JSON weight files from `model_weights/`, and browsers block `fetch()` of local files under the `file://` protocol. Double-clicking `index.html` will load the page but the demos will silently fail to load their weights.

From this repo's root, run a local server:

```bash
python -m http.server 8000
```

Then open **http://localhost:8000/index.html** in your browser.

Any static file server works instead — e.g. VS Code's "Live Server" extension, or `npx serve`.

## Pages

| File | What it is |
|---|---|
| `index.html` | Home — intro, quick links, featured project |
| `projects.html` | Projects picker (scroll/drag/arrow-keys, tick sound, snap-to-center) |
| `neurons-to-transformers.html` | Project detail page: architecture overview + 4 live demos (MLP, RNN/LSTM, Transformer attention, RAG) |
| `certificates.html` | Searchable/filterable grid of real certificates (PDFs in `certificates/`) |
| `about.html` | Bio, experience timeline, skills |

Shared across all pages: `style.css` (design tokens + all component styles) and `common.js` (nav active-state highlighting). Each other page loads only the JS it actually needs — `neurons-to-transformers.js`, `certificates.js`, or `projects-picker.js` — so the ~1-2MB of model weights only load on the one page that uses them.

## The client-side (browser) demos

`neurons-to-transformers.js` re-implements each model's real forward pass by hand in JavaScript (matrix ops, LSTM gates, causal self-attention, TF-IDF cosine similarity) and runs it on weights exported from the trained PyTorch/NumPy models, shipped here as `model_weights/*.json`.

## The server-side webapp

`webapp/` is a second, independent presentation of the same four demos — a FastAPI backend that reconstructs the real trained model objects (not a JS reimplementation) and runs genuine Python inference, with a Streamlit frontend. See [webapp/README.md](webapp/README.md) for how to run it locally, with Docker, or deploy it to Hugging Face Spaces.

It vendors its own copies of the 4 model-definition files it needs under `webapp/vendor_projects/`, so this repo can build and deploy on its own.

## Keeping this in sync with the ML-projects repo

When a model gets retrained in [evolution-to-ai-agents-from-ml](https://github.com/shabeeehtesham/evolution-to-ai-agents-from-ml):

1. Run the matching `scripts/export_*_weights.py` there — it writes fresh JSON into that repo's `exported_weights/`.
2. Copy the updated file(s) into this repo's `model_weights/`.
3. If the model's *class definition* changed (not just its weights), also copy the updated `.py` file into the matching `webapp/vendor_projects/` subfolder here.

## Adding a new project to the picker

Edit the `PROJECTS` array at the top of `projects-picker.js` — add `{ id, title, tagline, tags, href, githubHref }`. No other wiring needed; the reel, preview panel, and keyboard/scroll handling are all data-driven.
