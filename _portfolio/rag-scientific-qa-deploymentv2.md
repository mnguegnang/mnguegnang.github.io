---
title: "RAG Chatboard: A Platform for Addressing Research-Oriented Questions in Natural Language Processing"
excerpt: "Production RAG system serving cited, streamed answers over Natural Language Processing (NLP) papers, deployed via GitHub Actions CI/CD to GHCR with one-command Docker Compose startup."
img: assets/img/rag-scientific-qa-card.webp
collection: portfolio
---

<div class="links" style="margin-bottom: 1.5rem;">
  <a href="https://github.com/mnguegnang/RAG-app-scientific-QA" target="_blank" class="btn btn-sm z-depth-0" role="button">
    🔗 GitHub — Deployment Repo
  </a>
  <a href="https://github.com/mnguegnang/RAG-for-Scientific-QA" target="_blank" class="btn btn-sm z-depth-0" role="button">
     GitHub — Research & Evaluation Repo
  </a>
</div>

---

## TL;DR

- Researchers navigating thousands of NLP papers waste time manually synthesising answers from keyword search results that return documents, not answers.
- Deployed a production RAG application — FastAPI backend, Chainlit chat UI, hybrid FAISS + BM25 retrieval, ColBERT v2 late-interaction reranking, full CRAG framework generation, and a GitHub Actions CI/CD pipeline publishing immutable Docker images to GHCR on every commit.
- One-command deployment (docker compose up) on CPU or GPU hardware, with real-time token-streamed cited answers, transparent chain-of-thought reasoning, and hallucination suppression via the full CRAG framework. Evaluated on **84 QASPER questions**, ALCE Citation Precision **0.8373** (ongoing additional metrics to be published).

---

## Problem Statement & Business Objective

### Context & Significance

Scientific literature is growing faster than any researcher can read. The NLP field alone produces thousands of papers annually, and practitioners routinely need precise, cited answers to technical questions that span entire corpora — questions like *"What retrieval fusion strategies have been evaluated on QASPER?"* or *"Which encoder architectures outperform LSTM baselines on NER?"*

Traditional keyword search returns a ranked list of documents. The user must still open, skim, compare, and mentally synthesise the answer. This is slow, cognitively expensive, and does not scale.

### Technical Formulation

The problem is framed as **open-domain question answering** over a pre-indexed corpus of scientific NLP papers: given a natural-language query, retrieve the top-7 most relevant passages from a **47,810-chunk FAISS + BM25 index** (built from the QASPER benchmark — 5,049 NLP paper QA pairs), then synthesise a grounded, cited answer via a local LLM. The pipeline must suppress generation when retrieved context is below a confidence threshold, to avoid hallucination.

### Scope

**In scope:** The full serving pipeline — FastAPI backend, Chainlit frontend, Docker Compose orchestration, CI/CD, artifact management, and RAG inference. **Out of scope for this repo:** index construction, model training, embedding, and evaluation — these live in the companion research repository ([RAG-for-Scientific-QA](https://github.com/mnguegnang/RAG-for-Scientific-QA){:target="_blank"}).

### Success Criteria

Grounded cited answers delivered with visible reasoning, hallucinations suppressed by the CRAG relevance gate, and the full stack deployable on commodity hardware via a single command.

---

## Architecture & Modeling Approach

### Application Architecture

The system is a **two-service Docker Compose stack**, designed so that end users never build anything locally — they pull frozen, versioned images from the GitHub Container Registry.

<svg width="100%" viewBox="0 0 680 580" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>

  <!-- Outer container -->
  <rect x="30" y="20" width="620" height="540" rx="16" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#2C2C2A" x="340" y="48" text-anchor="middle">Docker Compose — host network</text>

  <!-- CI/CD badge -->
  <rect x="490" y="32" width="148" height="28" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="12" fill="#085041" x="564" y="46" text-anchor="middle" dominant-baseline="central">GitHub Actions CI/CD</text>

  <!-- GHCR badge -->
  <rect x="490" y="68" width="148" height="24" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="12" fill="#085041" x="564" y="80" text-anchor="middle" dominant-baseline="central">Images pulled from GHCR</text>

  <!-- frontend-ui -->
  <rect x="55" y="100" width="200" height="80" rx="10" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#26215C" x="155" y="130" text-anchor="middle" dominant-baseline="central">frontend-ui</text>
  <text font-family="sans-serif" font-size="12" fill="#3C3489" x="155" y="153" text-anchor="middle" dominant-baseline="central">Chainlit 2.9.6 · port 8001</text>

  <!-- backend-api -->
  <rect x="330" y="100" width="290" height="80" rx="10" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#042C53" x="475" y="130" text-anchor="middle" dominant-baseline="central">backend-api</text>
  <text font-family="sans-serif" font-size="12" fill="#0C447C" x="475" y="153" text-anchor="middle" dominant-baseline="central">FastAPI + Uvicorn · port 8080</text>

  <!-- Health check annotation -->
  <text font-family="sans-serif" font-size="12" fill="#5F5E5A" x="475" y="196" text-anchor="middle">/health · 30s interval · 300s start period</text>

  <!-- HTTP arrow: frontend → backend -->
  <line x1="255" y1="140" x2="328" y2="140" stroke="#7F77DD" stroke-width="1.5" marker-end="url(#arrow)"/>
  <text font-family="sans-serif" font-size="12" fill="#534AB7" x="291" y="132" text-anchor="middle">HTTP</text>

  <!-- RAG Pipeline container -->
  <rect x="330" y="220" width="290" height="230" rx="10" fill="#E6F1FB" fill-opacity="0.4" stroke="#185FA5" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#042C53" x="475" y="246" text-anchor="middle" dominant-baseline="central">RAG pipeline</text>

  <!-- Stage 1 -->
  <rect x="350" y="262" width="250" height="36" rx="6" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#042C53" x="475" y="274" text-anchor="middle" dominant-baseline="central">Stage 1 — hybrid retrieval</text>
  <text font-family="sans-serif" font-size="12" fill="#0C447C" x="475" y="290" text-anchor="middle" dominant-baseline="central">FAISS dense + BM25 sparse · RRF · HyDE</text>
  <line x1="475" y1="298" x2="475" y2="312" stroke="#444441" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Stage 2 -->
  <rect x="350" y="312" width="250" height="36" rx="6" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#042C53" x="475" y="324" text-anchor="middle" dominant-baseline="central">Stage 2 — BGE reranker</text>
  <text font-family="sans-serif" font-size="12" fill="#0C447C" x="475" y="340" text-anchor="middle" dominant-baseline="central">BAAI/bge-reranker-v2-m3 · top-7</text>
  <line x1="475" y1="348" x2="475" y2="362" stroke="#444441" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Stage 3 CRAG -->
  <rect x="350" y="362" width="250" height="36" rx="6" fill="#FAEEDA" stroke="#BA7517" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#412402" x="475" y="374" text-anchor="middle" dominant-baseline="central">Stage 3 — CRAG gate</text>
  <text font-family="sans-serif" font-size="12" fill="#633806" x="475" y="390" text-anchor="middle" dominant-baseline="central">Confidence threshold · blocks hallucination</text>
  <line x1="475" y1="398" x2="475" y2="412" stroke="#444441" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Stage 4 -->
  <rect x="350" y="412" width="250" height="36" rx="6" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#042C53" x="475" y="424" text-anchor="middle" dominant-baseline="central">Stage 4 — generation</text>
  <text font-family="sans-serif" font-size="12" fill="#0C447C" x="475" y="440" text-anchor="middle" dominant-baseline="central">Llama 3 · NDJSON stream · 6-turn memory</text>

  <!-- Arrow: backend → RAG pipeline -->
  <line x1="475" y1="180" x2="475" y2="218" stroke="#444441" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Ollama -->
  <rect x="55" y="300" width="200" height="60" rx="10" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#2C2C2A" x="155" y="322" text-anchor="middle" dominant-baseline="central">Ollama (host)</text>
  <text font-family="sans-serif" font-size="12" fill="#444441" x="155" y="343" text-anchor="middle" dominant-baseline="central">Llama-3.1-8B · CPU / GPU auto</text>

  <!-- Arrow: RAG pipeline → Ollama -->
  <line x1="350" y1="440" x2="259" y2="340" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Google Drive -->
  <rect x="55" y="440" width="200" height="64" rx="10" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.5"/>
  <text font-family="sans-serif" font-size="14" font-weight="500" fill="#2C2C2A" x="155" y="462" text-anchor="middle" dominant-baseline="central">Google Drive</text>
  <text font-family="sans-serif" font-size="12" fill="#444441" x="155" y="481" text-anchor="middle" dominant-baseline="central">~230 MB FAISS artifacts</text>
  <text font-family="sans-serif" font-size="12" fill="#444441" x="155" y="497" text-anchor="middle" dominant-baseline="central">auto-downloaded · gdown</text>

  <!-- Dashed arrow: Google Drive → backend-api bottom edge -->
  <path d="M255 472 L310 472 L310 182 L390 182"
        fill="none" stroke="#888780"
        stroke-width="1" stroke-dasharray="4 3"
        marker-end="url(#arrow)"/>
  <text font-family="sans-serif" font-size="12" fill="#5F5E5A" x="280" y="466" text-anchor="middle">first boot</text>

  <!-- depends_on annotation -->
  <text font-family="sans-serif" font-size="12" fill="#5F5E5A" x="155" y="224" text-anchor="middle">depends on healthy backend</text>
  <path d="M155 214 L155 182" fill="none" stroke="#888780" stroke-width="0.8" stroke-dasharray="3 3" marker-end="url(#arrow)"/>
</svg>

Both containers run with `network_mode: host`, giving the backend direct access to Ollama running on the host. The frontend depends on a **health-gated** backend (`condition: service_healthy`) — a `/health` endpoint is polled every 30 seconds with a 300-second start period to accommodate first-boot artifact download.

### RAG Pipeline (Model as a Component)

The inference pipeline, developed and evaluated in the companion research repo, executes in four stages within the backend container:

**Stage 1: Hybrid Retrieval.** FAISS dense search (SPECTER2 embeddings, 768-dim, IndexFlatIP, 47,810 vectors) is fused with BM25 sparse search via **Reciprocal Rank Fusion**, returning top-50 candidates. Short queries (< 10 words) trigger **HyDE**, the LLM generates a hypothetical passage used as the dense query instead of the raw question, improving recall on brief or ambiguous inputs. Short queries are sparse in embedding space; a raw two-word query like *"attention mechanism"* produces a generic vector that retrieves broadly, whereas a hypothetical answer passage anchors the dense search to the specific technical context of the question, yielding more precise top-k candidates.

**Stage 2: ColBERT v2 Reranking.** Upgraded from a cross-encoder to `ColBERT v2` late-interaction reranking. Document token embeddings are pre-computed offline; query time requires only a cheap MaxSim operation over the token embeddings, delivering cross-encoder-level precision at a fraction of the inference cost. The **top-7** documents are selected for generation context.

**Stage 3: CRAG Framework.** Implements the complete Corrective RAG framework with three actions: **Correct** (retrieved context is confidently relevant, proceed to generation), **Incorrect** (retrieved context is below threshold, suppress generation and return a low-confidence warning), and **Ambiguous** (borderline confidence generation proceeds with a hedged response). 

**Stage 4: Streamed Generation.** Llama-3.1-8B-Instruct (via Ollama on CPU, or HuggingFace Transformers on GPU — auto-detected) generates a chain-of-thought answer structured with `<Reasoning>` and `<Final Answer>` XML tags. The last **6 conversation turns** are injected as memory for multi-turn follow-up questions. Tokens are pushed via an **async queue** to a NDJSON streaming endpoint, delivering token-by-token output to the frontend.

### Key Design Decisions

**Why `network_mode: host`?** Ollama runs as a host-level service. Bridge networking would require exposing Ollama on a reachable IP; host networking eliminates this complexity and avoids latency overhead for token streaming.

**Why immutable `sha-<commit>` image tags alongside `:latest`?** Each push produces two tags — `:latest` for ease of use, and `:sha-<git-sha>` for reproducibility and rollback. Any deployed version can be precisely identified and re-deployed from the registry.

**Why `gdown` + volume mount?** FAISS indices (~230 MB) are too large for the Docker image. `gdown` fetches them from Google Drive on first boot into a volume-mounted directory (`./backend/app/artifacts:/app/artifacts:rw`), ensuring they persist across container restarts without re-downloading.

---

## Evaluation, Results & Impact

### Pipeline Evaluation Metrics

The RAG pipeline was evaluated on **84 questions from the QASPER benchmark** using RAGAS (LLM-judge framework) and ALCE (citation grounding via NLI). Results reflect the current state of the pipeline; active improvement work is ongoing. Additional metrics will be published in a future evaluation run.

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>Framework</th>
      <th>Score</th>
      <th>What It Measures</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Answer Relevancy</td>
      <td>RAGAS</td>
      <td><strong>0.6809</strong></td>
      <td>Semantic alignment between answer and question</td>
    </tr>
    <tr>
      <td>Context Precision</td>
      <td>RAGAS</td>
      <td><strong>0.5184</strong></td>
      <td>Fraction of retrieved chunks relevant to the question</td>
    </tr>
    <tr>
      <td>Context Recall</td>
      <td>RAGAS</td>
      <td><strong>0.5308</strong></td>
      <td>Fraction of gold evidence covered by retrieved chunks</td>
    </tr>
    <tr>
      <td>Faithfulness</td>
      <td>RAGAS</td>
      <td><strong>0.4699</strong></td>
      <td>Fraction of answer claims supported by retrieved context</td>
    </tr>
    <tr>
      <td>ALCE Citation Precision</td>
      <td>ALCE</td>
      <td><strong>0.0573</strong></td>
      <td>Fraction of cited sentences entailed by cited document</td>
    </tr>
    <tr>
      <td>ALCE Citation Recall</td>
      <td>ALCE</td>
      <td><strong>0.0568</strong></td>
      <td>Fraction of answer sentences with a supporting citation</td>
    </tr>
  </tbody>
</table>


### Limitations & Failure Modes

The full CRAG framework introduces an Ambiguous action for borderline-confidence retrievals, reducing the number of outright rejections compared to the previous binary gate — but ambiguous responses carry hedged language that may frustrate users expecting a definitive answer. CRAG threshold calibration (`calibrate_crag.py`) in the research repo provides a diagnostic tool for tuning the Correct / Incorrect / Ambiguous boundaries. The QASPER corpus is domain-specific to NLP papers; out-of-domain queries will retrieve poorly regardless of reranker quality.

### Application-Level Impact

- **Zero-build deployment** — end users run `docker compose up` and pull pre-built images from GHCR; no Python environment, no model download script, no manual index management
- **CPU-accessible** — Ollama backend runs on commodity hardware (~2–5 tokens/sec on CPU), democratising access without requiring a GPU
- **Transparent reasoning** — every answer exposes `<Reasoning>` before `<Final Answer>`, allowing users to audit the LLM's logic
- **Selective citation display** — only documents actually cited in the answer are shown with click-to-expand content, reducing information overload
- **Conversational continuity** — 6-turn memory allows follow-up questions without repeating context

---

## Deployment & MLOps

### CI/CD Pipeline (GitHub Actions → GHCR)

Every push to the `master` branch triggers a single-job GitHub Actions workflow (`deploy.yml`) that:

1. Checks out the repository on `ubuntu-latest`
2. Authenticates to GHCR using the automatic `GITHUB_TOKEN` (no secrets to manage)
3. Builds and pushes the **backend** Docker image with two tags: `:latest` and `:sha-<commit>`
4. Builds and pushes the **frontend** Docker image with the same dual-tag strategy

Images are published to `ghcr.io/mnguegnang/rag-app-scientific-qa-backend` and `ghcr.io/mnguegnang/rag-app-scientific-qa-frontend`. The commit-pinned `:sha-<commit>` tag ensures every deployed version is fully reproducible and rollback is trivial. Any production issue can be diagnosed against an exact commit and recovered by pinning `image: ...backend:sha-<commit>` in `compose.yml` — no rebuild required, no environment drift.

### Serving Infrastructure

```yaml
# Minimal compose.yml — full stack in one command
services:
  backend-api:
    image: ghcr.io/mnguegnang/rag-app-scientific-qa-backend:latest
    network_mode: "host"
    healthcheck:
      test: ["CMD", "python", "-c", "import urllib.request; urllib.request.urlopen('http://localhost:8080/health')"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 300s

  frontend-ui:
    image: ghcr.io/mnguegnang/rag-app-scientific-qa-frontend:latest
    network_mode: "host"
    depends_on:
      backend-api:
        condition: service_healthy
```

### Artifact Management

FAISS indices (~230 MB total: `dense.index` 147 MB, `dense.index.meta` 31 MB, `sparse.pkl` 51 MB) are fetched automatically from Google Drive via `gdown` on first boot and persisted to a host-mounted volume. Subsequent container restarts are instant.

### Hardware Flexibility

The backend auto-detects CUDA at startup and selects the appropriate LLM backend — **HuggingFace Transformers** on GPU, **Ollama** on CPU — with no configuration change required from the user.

---

## Links & Artifacts

- 🔗 [Deployment Repository](https://github.com/mnguegnang/RAG-app-scientific-QA){:target="_blank"}
-    [Research & Evaluation Repository](https://github.com/mnguegnang/RAG-for-Scientific-QA){:target="_blank"}
- 📦 [Backend Container Image — GHCR](https://github.com/mnguegnang/RAG-app-scientific-QA/pkgs/container/rag-app-scientific-qa-backend){:target="_blank"}
- 📦 [Frontend Container Image — GHCR](https://github.com/mnguegnang/RAG-app-scientific-QA/pkgs/container/rag-app-scientific-qa-frontend){:target="_blank"}

---

