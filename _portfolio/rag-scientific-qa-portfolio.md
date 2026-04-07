---
title: "Four-Stage RAG Pipeline for Grounded Scientific Question Answering"
excerpt: "SPECTER2 + BM25 hybrid retrieval, ColBERT v2 late-interaction reranking, and CRAG gating for cited, hallucination-resistant answers over NLP research papers."
img: assets/img/rag-scientific-qa-card.webp
collection: portfolio
order: 2
category: [nlp, generative-ai, ml-engineering]
---

<div class="links" style="margin-bottom: 1.5rem;">
  <a href="https://github.com/mnguegnang/RAG-for-Scientific-QA" target="_blank" class="btn btn-sm z-depth-0" role="button">
    🔗 GitHub Repository
  </a>
</div>

---

## 1. Executive Summary (TL;DR)

- Open-domain question answering over scientific literature is severely hampered by hallucination — language models confidently fabricate citations and misrepresent methodology when answering without grounded retrieval.
- Designed and implemented a four-stage RAG pipeline combining SPECTER2 dense retrieval, BM25 sparse retrieval, Reciprocal Rank Fusion, **ColBERT v2 late-interaction reranking**, a Corrective RAG (CRAG) relevance gate with dual-mode calibration, and HyDE query expansion — all orchestrated to deliver cited, source-grounded answers from a 47,810-chunk index of NLP research papers. The evaluation stack uses a fully local LLM-as-judge (vLLM) for both RAGAS and ALCE NLI entailment checks, with `nomic-embed-text-v1.5` for Answer Relevancy scoring.
- On the latest evaluation run (n=84 QASPER questions), the pipeline achieves **ALCE Citation Precision of 0.8373**, **ALCE Citation Recall of 0.7151**, **Faithfulness of 0.7150**, and **Answer Relevancy of 0.6911**. Active work on Context Recall continues with the ColBERT v2 upgrade.

---

## 2. Problem Statement & Business Objective

Scientific question answering is one of the highest-stakes applications of language models. A researcher querying a corpus of NLP papers needs not just a plausible answer, but a *verifiable* one — directly traceable to specific passages in specific documents. Standard LLM prompting fails this bar entirely: models hallucinate citations, blend findings across papers, and fabricate methodology details with confident fluency.

The technical formulation is an open-domain extractive-generative QA task: given a natural-language question and a large heterogeneous corpus of full-text research papers, retrieve the most relevant passages and generate a concise answer with inline citations pointing to the source documents. The challenge has two compounding layers of difficulty. First, scientific text is densely packed with domain-specific jargon, making general-purpose retrieval models unreliable — a model trained only on textual co-occurrence will conflate papers that share vocabulary but reach opposite conclusions. Second, generation quality depends entirely on retrieval quality — a well-calibrated answer is impossible if the wrong passages reach the LLM.

This project scopes to the **QASPER** benchmark (Dasigi et al., 2021): 5,049 QA pairs over full-text NLP research papers, evaluated against the training split (the same split used for indexing, ensuring retrieval is in-distribution). Out of scope: real-time paper ingestion, cross-domain generalisation beyond NLP, and deployment as a live service. Success criteria: high ALCE Citation Precision and Recall (answer claims traced to source documents), strong Faithfulness (claims grounded in retrieved context), and improving Context Recall (gold evidence surfaces in top-60 candidates).

---

## 3. Data Acquisition & Engineering

**Dataset.** The system indexes the training split of [allenai/qasper](https://huggingface.co/datasets/allenai/qasper) via HuggingFace `datasets`. QASPER is a public, peer-reviewed benchmark with no personally identifiable information; it is Findable, Accessible, Interoperable, and Reusable (FAIR) by construction.

**Chunking strategy.** Raw paper texts are segmented using a custom `QasperChunker` into overlapping 500-token chunks (10% overlap, ~50 tokens). Each chunk is prefixed with a contextual header:

```
Title: <paper title>. Section: <section heading>.
<chunk text>
```

This prefix anchors each chunk to its provenance in both BM25 keyword matching (title and section heading become high-IDF tokens) and ColBERT scoring (the reranker reasons about relevance in context). Without it, short chunks detached from their section heading lose critical disambiguation signal.

The resulting corpus contains **47,810 chunks** stored as a FAISS flat index (`IndexFlatIP`, 768-dim FP16 vectors) for dense retrieval and a pickled BM25 model with NLTK-tokenized, stop-word-filtered, Porter-stemmed corpus for sparse retrieval. Both indices must always be rebuilt together to keep chunk metadata aligned.

![Chunking and Indexing Pipeline](assets/rag-scientific-qa/chunking-pipeline.webp)
<!-- PLACEHOLDER: Flow diagram: raw QASPER paper → QasperChunker (500 tokens, 10% overlap, contextual prefix) → two branches: SPECTER2 encoder → FAISS index, and NLTK tokenizer → BM25 index. Export from draw.io or matplotlib. Save as WebP. -->

---

## 4. Architecture & Modeling Approach

The `ScientificRAGPipeline` orchestrates four sequential stages. Every architectural decision is motivated by the specific demands of scientific text retrieval.

<div style="margin: 1.5rem 0;">
<svg width="100%" viewBox="0 0 680 930" xmlns="http://www.w3.org/2000/svg" style="max-width:620px; display:block; margin:0 auto;">
  <defs>
    <marker id="arr" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>
  <style>
    .bx{fill:#f4f3ff;stroke:#7f77dd;stroke-width:0.5;}
    .bx-teal{fill:#e1f5ee;stroke:#0f6e56;stroke-width:0.5;}
    .bx-blue{fill:#e6f1fb;stroke:#185fa5;stroke-width:0.5;}
    .bx-amber{fill:#faeeda;stroke:#854f0b;stroke-width:0.5;}
    .bx-gray{fill:#f1efe8;stroke:#5f5e5a;stroke-width:0.5;}
    .bx-inner{fill:#9fe1cb;stroke:#0f6e56;stroke-width:0.5;}
    .bx-rrf{fill:#c8ece0;stroke:#0f6e56;stroke-width:0.5;}
    .lbl{font-family:sans-serif;font-size:14px;font-weight:500;fill:#3c3489;text-anchor:middle;dominant-baseline:central;}
    .sub{font-family:sans-serif;font-size:12px;font-weight:400;fill:#534ab7;text-anchor:middle;dominant-baseline:central;}
    .lbl-teal{font-family:sans-serif;font-size:14px;font-weight:500;fill:#085041;text-anchor:middle;dominant-baseline:central;}
    .sub-teal{font-family:sans-serif;font-size:12px;font-weight:400;fill:#0f6e56;text-anchor:middle;dominant-baseline:central;}
    .lbl-blue{font-family:sans-serif;font-size:14px;font-weight:500;fill:#0c447c;text-anchor:middle;dominant-baseline:central;}
    .sub-blue{font-family:sans-serif;font-size:12px;font-weight:400;fill:#185fa5;text-anchor:middle;dominant-baseline:central;}
    .lbl-amber{font-family:sans-serif;font-size:14px;font-weight:500;fill:#633806;text-anchor:middle;dominant-baseline:central;}
    .sub-amber{font-family:sans-serif;font-size:12px;font-weight:400;fill:#854f0b;text-anchor:middle;dominant-baseline:central;}
    .lbl-gray{font-family:sans-serif;font-size:14px;font-weight:500;fill:#2c2c2a;text-anchor:middle;dominant-baseline:central;}
    .num{font-family:sans-serif;font-size:11px;font-weight:400;fill:#888780;text-anchor:end;dominant-baseline:central;}
    .conn{stroke:#888780;stroke-width:1;fill:none;marker-end:url(#arr);}
    .conn-dash{stroke:#b4b2a9;stroke-width:0.8;fill:none;stroke-dasharray:4 3;marker-end:url(#arr);}
    .conn-teal{stroke:#1d9e75;stroke-width:0.8;fill:none;marker-end:url(#arr);}
    .ln-teal{stroke:#1d9e75;stroke-width:0.8;fill:none;}
    @media(prefers-color-scheme:dark){
      .bx{fill:#26215c;stroke:#afa9ec;}
      .bx-teal{fill:#04342c;stroke:#5dcaa5;}
      .bx-blue{fill:#042c53;stroke:#85b7eb;}
      .bx-amber{fill:#412402;stroke:#ef9f27;}
      .bx-gray{fill:#2c2c2a;stroke:#b4b2a9;}
      .bx-inner{fill:#085041;stroke:#5dcaa5;}
      .bx-rrf{fill:#04342c;stroke:#5dcaa5;}
      .lbl{fill:#cecbf6;}.sub{fill:#afa9ec;}
      .lbl-teal{fill:#9fe1cb;}.sub-teal{fill:#5dcaa5;}
      .lbl-blue{fill:#b5d4f4;}.sub-blue{fill:#85b7eb;}
      .lbl-amber{fill:#fac775;}.sub-amber{fill:#ef9f27;}
      .lbl-gray{fill:#d3d1c7;}
      .num{fill:#5f5e5a;}.conn{stroke:#5f5e5a;}.conn-dash{stroke:#444441;}
      .conn-teal{stroke:#5dcaa5;}.ln-teal{stroke:#5dcaa5;}
    }
  </style>

  <!-- User Query -->
  <rect class="bx-gray" x="240" y="20" width="200" height="44" rx="8"/>
  <text class="lbl-gray" x="340" y="42">User query</text>

  <!-- Arrow to decision -->
  <line class="conn" x1="340" y1="64" x2="340" y2="96"/>

  <!-- Decision diamond -->
  <polygon points="340,96 394,128 340,160 286,128" fill="none" stroke="#888780" stroke-width="0.8"/>
  <text class="sub" x="340" y="122" style="fill:#534ab7;">&lt; 10 words?</text>

  <!-- yes branch -->
  <line class="conn-dash" x1="394" y1="128" x2="492" y2="128"/>
  <text style="font-family:sans-serif;font-size:11px;fill:#888780;text-anchor:middle;dominant-baseline:central;" x="443" y="120">yes</text>
  <rect class="bx" x="494" y="106" width="152" height="44" rx="8"/>
  <text class="lbl" x="570" y="124">HyDE expansion</text>
  <text class="sub" x="570" y="140">Hypothetical passage</text>
  <!-- HyDE rejoins -->
  <path class="conn-dash" d="M570 150 L570 218 L424 218" marker-end="url(#arr)"/>

  <!-- no branch -->
  <line class="conn" x1="340" y1="160" x2="340" y2="220"/>
  <text style="font-family:sans-serif;font-size:11px;fill:#888780;text-anchor:middle;dominant-baseline:central;" x="324" y="190">no</text>

  <!-- ═══ STAGE 1 — taller to breathe ═══ -->
  <text class="num" x="88" y="318">01</text>
  <rect class="bx-teal" x="100" y="222" width="480" height="212" rx="10"/>
  <text class="lbl-teal" x="340" y="250">Stage 1 — Hybrid retrieval</text>

  <!-- inner left: Dense -->
  <rect class="bx-inner" x="118" y="268" width="210" height="60" rx="6"/>
  <text class="lbl-teal" x="223" y="291" style="fill:#085041;">Dense search</text>
  <text class="sub-teal" x="223" y="309" style="fill:#0f6e56;">SPECTER2 + FAISS IndexFlatIP</text>

  <!-- inner right: Sparse -->
  <rect class="bx-inner" x="352" y="268" width="210" height="60" rx="6"/>
  <text class="lbl-teal" x="457" y="291" style="fill:#085041;">Sparse search</text>
  <text class="sub-teal" x="457" y="309" style="fill:#0f6e56;">BM25 · NLTK · Porter stemming</text>

  <!-- Convergence lines from inner boxes to horizontal bar -->
  <line class="ln-teal" x1="223" y1="328" x2="223" y2="366"/>
  <line class="ln-teal" x1="457" y1="328" x2="457" y2="366"/>
  <line class="ln-teal" x1="223" y1="366" x2="457" y2="366"/>
  <line class="conn-teal" x1="340" y1="366" x2="340" y2="382"/>

  <!-- RRF box — clearly separated and labelled -->
  <rect class="bx-rrf" x="172" y="382" width="336" height="38" rx="6"/>
  <text class="lbl-teal" x="340" y="397" style="fill:#085041;font-size:13px;">Reciprocal Rank Fusion  (k = 60)</text>
  <text class="sub-teal" x="340" y="413" style="fill:#0f6e56;">→ top-60 candidates</text>

  <!-- Arrow out of Stage 1 -->
  <line class="conn" x1="340" y1="434" x2="340" y2="464"/>

  <!-- ═══ STAGE 2 — ColBERT v2 ═══ -->
  <text class="num" x="88" y="500">02</text>
  <rect class="bx-blue" x="100" y="466" width="480" height="74" rx="10"/>
  <text class="lbl-blue" x="340" y="492">Stage 2 — ColBERT v2 late-interaction reranking</text>
  <text class="sub-blue" x="340" y="512">Per-token MaxSim scoring — token-level precision at retrieval scale</text>
  <text class="sub-blue" x="340" y="530">top-60 → top-7</text>

  <!-- Arrow to Stage 3 -->
  <line class="conn" x1="340" y1="540" x2="340" y2="572"/>

  <!-- ═══ STAGE 3 — CRAG ═══ -->
  <text class="num" x="88" y="614">03</text>
  <rect class="bx-amber" x="100" y="574" width="480" height="84" rx="10"/>
  <text class="lbl-amber" x="340" y="600">Stage 3 — CRAG relevance gate</text>
  <text class="sub-amber" x="340" y="620">best logit ≥ threshold → pass through</text>
  <text class="sub-amber" x="340" y="638">best logit &lt; threshold → "insufficient context"</text>
  <!-- reject exit -->
  <path class="conn-dash" d="M580 616 L644 616" marker-end="url(#arr)"/>
  <text style="font-family:sans-serif;font-size:11px;fill:#854f0b;text-anchor:start;dominant-baseline:central;" x="648" y="616">reject</text>

  <!-- Arrow to Stage 4 -->
  <line class="conn" x1="340" y1="658" x2="340" y2="692"/>

  <!-- ═══ STAGE 4 — Generation ═══ -->
  <text class="num" x="88" y="742">04</text>
  <rect class="bx" x="100" y="694" width="480" height="112" rx="10"/>
  <text class="lbl" x="340" y="720">Stage 4 — Generation</text>
  <text class="sub" x="340" y="740">Llama-3.1-8B-Instruct via vLLM (dynamic tensor-parallel)</text>
  <text class="sub" x="340" y="758">CoT reasoning + [Doc N] inline citation prompt</text>
  <text class="sub" x="340" y="776">Final Answer extracted — reasoning block stripped before eval</text>
  <text class="sub" x="340" y="794">Fallback: single-GPU (50% VRAM) · CPU / Ollama</text>

  <!-- Arrow to output -->
  <line class="conn" x1="340" y1="806" x2="340" y2="840"/>

  <!-- Cited answer -->
  <rect class="bx-gray" x="210" y="842" width="260" height="44" rx="8"/>
  <text class="lbl-gray" x="340" y="864">Cited answer</text>
</svg>
<p style="text-align:center; font-size:0.8rem; color:#888; margin-top:0.5rem;">
  Figure 1. Four-stage RAG pipeline. HyDE triggers only for short queries (&lt;10 words). The CRAG gate suppresses generation when no retrieved passage exceeds the calibrated threshold.
</p>
</div>

**Stage 0 — HyDE Query Expansion.** Queries shorter than 10 words trigger **Hypothetical Document Embeddings (HyDE)**: the LLM generates a hypothetical passage that *would* answer the question, used as the dense query in place of the raw question. This bridges the vocabulary gap between short queries and the dense scientific text in the index.

**Stage 1 — Hybrid Retrieval (top-60, RRF k=60).** Two complementary signals are fused. **Dense retrieval** uses `allenai/specter2_base` (768-dim, FP16) with its retrieval adapter. The choice over general-purpose sentence transformers is empirically grounded: general models learn similarity from textual co-occurrence, conflating papers that share scientific jargon but reach contradictory conclusions. SPECTER2 trains on the **global citation graph** — if Paper A cites Paper B they are semantically related regardless of vocabulary overlap — embedding scientific *intent and methodology* rather than surface jargon. This advantage is documented by Cohan et al. (2020) on SciDocs and extended to SOTA across 25 scientific tasks by Singh et al. (2022) using SPECTER2's adapter architecture trained on 68 million Semantic Scholar papers. **Sparse retrieval** via `rank-bm25` (NLTK / Porter stemming) complements dense search with exact-match recall for distinctive method names and acronyms. The two ranked lists are fused via **Reciprocal Rank Fusion** (k=60, Cormack et al. 2009), avoiding score normalisation across incomparable retrieval scales. Retrieval is scoped per-paper (`filter_paper_id`) to the source paper of each QASPER question, improving precision.

**Stage 2 — ColBERT v2 Late-Interaction Reranking (top-7).** The top-60 hybrid candidates are reranked by **ColBERT v2**, replacing the prior BGE cross-encoder. ColBERT stores per-token embeddings offline and scores query–passage pairs via **MaxSim** — the maximum cosine similarity between each query token and every passage token — summed across all query tokens. This late-interaction paradigm retains token-level precision comparable to full cross-attention cross-encoders while avoiding the O(|q|·|d|) full attention computation at query time. For scientific text, where the relevance of a passage may hinge on a single technical term matching precisely, token-level MaxSim is a significant upgrade over sentence-level bi-encoder scoring.

**Stage 3 — CRAG Relevance Gate.** The best reranker logit is compared against a calibrated threshold. If no passage exceeds it, the system returns `"insufficient context"` rather than generating from weak evidence. The gate is calibrated by `calibrate_crag.py` in two modes: **F1-max mode** (grid-searches 300 threshold candidates against `context_recall`-derived binary labels, following Yan et al. 2024 CRAG §3.3); and a **percentile fallback mode** (sets the threshold at the P-th percentile of the `best_rerank_score` distribution from `evaluation_dataset.csv`, requiring no RAGAS labels — following Karpukhin et al. 2020 DPR §5.4 and Asai et al. 2023 Self-RAG §3.2).

**Stage 4 — Generation.** The top-7 passages are passed to `meta-llama/Llama-3.1-8B-Instruct` via **vLLM** with a dynamically allocated GPU pool (`run_evaluation.sh` auto-detects GPU count: tensor-parallel on multiple GPUs, 50% VRAM share on single GPU, Ollama fallback on CPU). The prompt enforces **Chain-of-Thought reasoning** followed by a `<Final Answer>` block with `[Doc N]` inline citations. Critically, `generate_predictions.py` strips the `<Reasoning>` block before passing output to RAGAS and ALCE — reasoning sentences carry no citations and were previously inflating false-negative Citation Recall counts. Three edge cases are handled: missing closing tags, multiple `<Final Answer>` blocks (last taken), and line-wrapped opening tags.

<table style="width:100%; border-collapse:collapse; font-size:0.9rem;">
  <thead>
    <tr style="background:#f5f5f5;">
      <th style="padding:8px; border:1px solid #ddd;">Component</th>
      <th style="padding:8px; border:1px solid #ddd;">Model / Library</th>
      <th style="padding:8px; border:1px solid #ddd;">Role</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Dense embedding</td>
      <td style="padding:8px; border:1px solid #ddd;"><code>allenai/specter2_base</code> + retrieval adapter</td>
      <td style="padding:8px; border:1px solid #ddd;">Citation-graph-trained scientific encoder</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Vector store</td>
      <td style="padding:8px; border:1px solid #ddd;">FAISS <code>IndexFlatIP</code> (768-dim, FP16)</td>
      <td style="padding:8px; border:1px solid #ddd;">GPU-accelerated ANN search</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Sparse retrieval</td>
      <td style="padding:8px; border:1px solid #ddd;"><code>rank-bm25</code> + NLTK / Porter stemming</td>
      <td style="padding:8px; border:1px solid #ddd;">Exact-match keyword recall</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Retrieval fusion</td>
      <td style="padding:8px; border:1px solid #ddd;">Reciprocal Rank Fusion (k=60)</td>
      <td style="padding:8px; border:1px solid #ddd;">Score-agnostic rank combination</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;"><strong>Reranker</strong></td>
      <td style="padding:8px; border:1px solid #ddd;"><strong>ColBERT v2</strong> (late-interaction, per-token MaxSim)</td>
      <td style="padding:8px; border:1px solid #ddd;">Token-level precision reranking</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Relevance gate</td>
      <td style="padding:8px; border:1px solid #ddd;">CRAG — F1-max or percentile calibration</td>
      <td style="padding:8px; border:1px solid #ddd;">Hallucination suppression</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">LLM</td>
      <td style="padding:8px; border:1px solid #ddd;"><code>meta-llama/Llama-3.1-8B-Instruct</code> via vLLM</td>
      <td style="padding:8px; border:1px solid #ddd;">CoT + [Doc N] citation generation</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">RAGAS / ALCE judge LLM</td>
      <td style="padding:8px; border:1px solid #ddd;">Llama-3.1-8B-Instruct via local vLLM (port 8000)</td>
      <td style="padding:8px; border:1px solid #ddd;">NLI entailment + RAGAS metric scoring</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Answer relevancy embeddings</td>
      <td style="padding:8px; border:1px solid #ddd;"><code>nomic-ai/nomic-embed-text-v1.5</code></td>
      <td style="padding:8px; border:1px solid #ddd;">RAGAS AnswerRelevancy cosine scoring</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Compute</td>
      <td style="padding:8px; border:1px solid #ddd;">2× A100-SXM4-80GB — dynamic GPU allocation</td>
      <td style="padding:8px; border:1px solid #ddd;">vLLM tensor-parallel; ColBERT + encoders on last GPU</td>
    </tr>
  </tbody>
</table>

---

## 5. Evaluation, Results & Impact

### Framework

The evaluation stack is fully local — no external API calls. Two complementary frameworks run in a single SLURM job:

- **RAGAS** (Es et al., 2023) measures retrieval and generation quality via an LLM judge: Context Precision, Context Recall, Faithfulness, and Answer Relevancy. The judge is Llama-3.1-8B-Instruct served by the same vLLM instance used for generation. Answer Relevancy uses `nomic-ai/nomic-embed-text-v1.5` embeddings (via `_RagasHFEmbeddingsFixed`, a thin subclass that bridges the LangChain `embed_query` / `embed_documents` interface gap in the native ragas `HuggingFaceEmbeddings`).
- **ALCE** (Gao et al., 2023, EMNLP) measures citation-level grounding via a local NLI entailment check: for each sentence in the `<Final Answer>` block, `ALCE_RAGASevaluator._check_entailment()` prompts the local LLM judge to determine whether the cited passage supports the claim.
  
A key pre-processing step in `generate_predictions.py` underpins the validity of both metrics: the `extract_final_answer()` function strips the `<Reasoning>` block from LLM output before evaluation. Reasoning sentences contain no `[Doc N]` citations; including them inflated false-negative Citation Recall in prior runs. The fix handles three known Llama-3 output failure modes (missing closing tags, multiple `<Final Answer>` blocks, line-wrapped tags) and is the primary driver of the jump from ALCE scores of ~0.05 to ~0.84.

### Latest results (2026-04-04, n=84)

<table style="width:100%; border-collapse:collapse; font-size:0.9rem;">
  <thead>
    <tr style="background:#f5f5f5;">
      <th style="padding:8px; border:1px solid #ddd;">Metric</th>
      <th style="padding:8px; border:1px solid #ddd;">Framework</th>
      <th style="padding:8px; border:1px solid #ddd;">Score</th>
      <th style="padding:8px; border:1px solid #ddd;">Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background:#f0faf5;">
      <td style="padding:8px; border:1px solid #ddd;"><strong>ALCE Citation Precision</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">ALCE</td>
      <td style="padding:8px; border:1px solid #ddd;"><strong>0.8373</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">Post extract_final_answer fix</td>
    </tr>
    <tr style="background:#f0faf5;">
      <td style="padding:8px; border:1px solid #ddd;"><strong>ALCE Citation Recall</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">ALCE</td>
      <td style="padding:8px; border:1px solid #ddd;"><strong>0.7151</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">Post extract_final_answer fix</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Faithfulness</td>
      <td style="padding:8px; border:1px solid #ddd;">RAGAS</td>
      <td style="padding:8px; border:1px solid #ddd;"><strong>0.7150</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">1 NaN/timeout skipped</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Answer Relevancy</td>
      <td style="padding:8px; border:1px solid #ddd;">RAGAS</td>
      <td style="padding:8px; border:1px solid #ddd;"><strong>0.6911</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">nomic-embed-text-v1.5 embeddings</td>
    </tr>
    <tr>
      <td style="padding:8px; border:1px solid #ddd;">Context Recall</td>
      <td style="padding:8px; border:1px solid #ddd;">RAGAS</td>
      <td style="padding:8px; border:1px solid #ddd;"><strong>0.5882</strong></td>
      <td style="padding:8px; border:1px solid #ddd;">2 NaN/timeout skipped</td>
    </tr>
  </tbody>
</table>

*Evaluation run: 2026-04-04, n=84 questions, 32 minutes 9 seconds. Context Precision excluded — 18 NaN/timeout skips rendered it unreliable (0.0000); re-running with a more stable judge configuration.*

### Error Analysis & Known Limitations

The ALCE jump from 0.057 → **0.8373** Citation Precision and 0.057 → **0.7151** Citation Recall is entirely attributable to `extract_final_answer()`. The reasoning block was the entire problem — once stripped, the LLM's actual cited answers are well-grounded. Faithfulness also improved from 0.47 → **0.7150**, confirming that the CRAG threshold recalibration and prompt tightening reduced prior-knowledge leakage.

Context Recall (**0.5882**) is the remaining bottleneck — the retrieval stack fails to surface gold evidence for roughly 41% of questions. The most likely causes are out-of-vocabulary scientific terms defeating both BM25 and SPECTER2, and fixed 500-token chunk boundaries splitting answer-bearing passages. The **ColBERT v2 upgrade** (Stage 2) directly targets this by enabling token-level MaxSim matching, which is more robust to exact-term mismatches than sentence-level cross-encoder scores. Results from the ColBERT run are pending.


> **[Ongoing Work]** ColBERT v2 reranker evaluation in progress — expected to improve Context Recall. Context Precision to be re-evaluated with timeout-resistant judge configuration. CRAG threshold to be re-calibrated post-ColBERT.

### Research Impact

The evaluation stack — a fully local LLM judge serving both generation and RAGAS/ALCE scoring, `extract_final_answer()` for evaluation hygiene, and dual-mode CRAG calibration — establishes a rigorous, reproducible evaluation protocol for open-domain scientific QA. Citation grounding (ALCE Precision 0.84, Recall 0.72) approaches the upper range for comparable open-weight models in the ALCE paper. The dataset-agnostic design means the pipeline can be reindexed over any document collection by replacing the data loader in `src/data/make_dataset.py`.

---

## 7. Links & Artifacts

<div class="links">
  <a href="https://github.com/mnguegnang/RAG-for-Scientific-QA" target="_blank" class="btn btn-sm z-depth-0" role="button">
    🔗 GitHub Repository
  </a>
  &nbsp;
  <a href="https://huggingface.co/datasets/allenai/qasper" target="_blank" class="btn btn-sm z-depth-0" role="button">
    QASPER Dataset
  </a>
  &nbsp;
  <a href="https://huggingface.co/allenai/specter2_base" target="_blank" class="btn btn-sm z-depth-0" role="button">
    SPECTER2
  </a>
  &nbsp;
  <a href="https://huggingface.co/colbert-ir/colbertv2.0" target="_blank" class="btn btn-sm z-depth-0" role="button">
    ColBERT v2
  </a>
</div>

---

## 8. Tags / Keywords

**Domain:** NLP · Information Retrieval · Generative AI · Scientific Question Answering

**Project Type:** Research · End-to-End RAG Pipeline · Ongoing

**Key Technologies:** RAG · SPECTER2 · FAISS · BM25 · Reciprocal Rank Fusion · ColBERT v2 · Late-Interaction Reranking · CRAG · HyDE · Llama-3.1-8B · vLLM · nomic-embed-text · RAGAS · ALCE · QASPER · Python · SLURM
