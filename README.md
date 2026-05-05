# Why Most RAG Systems Fail in Production
#LINK TO MEDIUM ARTICLE https://marouasaoud.medium.com/why-most-rag-systems-fail-in-production-and-how-to-fix-them-62b2aae36e76

A hands-on toolkit of **7 Jupyter notebooks** that diagnose and fix the most common failure modes in production Retrieval-Augmented Generation (RAG) pipelines. from naive chunking to silent hallucination on out-of-domain queries.

Each notebook is **runnable in Google Colab** and tackles one specific failure point with a working before/after demonstration.

## The Problem

Most RAG demos look great in a notebook and break the moment they hit real traffic. The naive pipeline — `embed → search → top-k → LLM` has at least five unguarded failure points: domain-blind embeddings, similarity ≠ relevance, noisy top-k, "lost in the middle," and silent failure on out-of-domain queries.

This repo walks through each failure, shows you how to detect it, and gives you a working fix.

## The 7 Notebooks

| # | Notebook | Failure Mode | Fix |
|---|----------|--------------|-----|
| 01 | [Semantic Chunking vs. Naive Chunking](notebooks/01_semantic_chunking.ipynb) | Token-based chunking splits ideas mid-sentence | Detect semantic boundaries before splitting |
| 02 | [Embedding Model Comparison](notebooks/02_embedding_model_comparison.ipynb) | Generic embeddings miss domain meaning | Benchmark domain-aware vs. general models |
| 03 | [Cross-Encoder Reranking](notebooks/03_cross_encoder_reranking.ipynb) | Top-K is full of noise | Retrieve 20, rerank with a cross-encoder, keep top 3–5 |
| 04 | [Context Compression](notebooks/04_context_compression.ipynb) | "Lost in the Middle" — long contexts hurt accuracy | Compress retrieved chunks to keep only relevant spans |
| 05 | [Confidence-Gated Fallback](notebooks/05_confidence_gated_fallback.ipynb) | LLM hallucinates when retrieval finds nothing | Gate generation on retrieval confidence threshold |
| 06 | [RAGAS Evaluation](notebooks/06_ragas_evaluation.ipynb) | No way to measure pipeline health | Weekly evaluation across 4 metrics |
| 07 | [End-to-End Production RAG Pipeline](notebooks/07_end_to_end_production_rag.ipynb) | Demo pipeline ≠ production pipeline | Full architecture: rerank + compress + gate + evaluate |

## Visual Guide

The [figures guide](figures/figures_guide.pdf) contains 7 diagrams that visualize each failure mode and its fix. useful as a reference while working through the notebooks.

## Quick Start

### Run on Google Colab (recommended)

Click any notebook above, then click the **"Open in Colab"** badge at the top. Add your `OPENAI_API_KEY` to Colab Secrets and run all cells.

### Run locally

```bash
git clone https://github.com/YOUR_USERNAME/rag-production-failures.git
cd rag-production-failures

pip install -r requirements.txt

export OPENAI_API_KEY="sk-..."

jupyter notebook notebooks/
```

## The Four RAGAS Metrics (Notebook 06)

RAGAS scores your pipeline on four dimensions — each one points to a different failure mode:

| Metric | What it measures | When it drops |
|--------|------------------|---------------|
| **Faithfulness** | Is the answer grounded in retrieved context? | Retrieval surfacing outdated or off-topic chunks |
| **Answer Relevancy** | Does the answer address the question? | Prompt template issues |
| **Context Precision** | Are retrieved chunks actually useful? | Embedding model or chunking strategy is off |
| **Context Recall** | Did retrieval capture all needed information? | k is too low or knowledge base has gaps |

**Score interpretation:**
- `> 0.80` 🟢 Healthy
- `0.60 – 0.80` 🟡 Monitor closely
- `< 0.60` 🔴 Action required

## Recommended Reading Order

1. Start with the [figures guide] for the conceptual map
2. Work through notebooks **01 → 06** in order — each one builds on the previous failure mode
3. Finish with notebook **07**, which assembles all 6 fixes into one production pipeline


## License

MIT : see [LICENSE](LICENSE)

## Contributing

Issues and PRs welcome. If you've hit a RAG failure mode not covered here, open an issue.
