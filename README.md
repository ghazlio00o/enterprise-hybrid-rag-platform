# 🚀 Enterprise Hybrid RAG Platform

> **Production-oriented Hybrid Retrieval-Augmented Generation (RAG) platform for reliable, contextual and traceable enterprise document question answering.**




\

---

## 📌 Overview

**Enterprise Hybrid RAG Platform** is a modular Retrieval-Augmented Generation system designed to answer questions from enterprise documents while improving retrieval relevance, reducing hallucinations and maintaining complete source traceability.

The platform transforms raw documents into structured and contextualized knowledge chunks, indexes them in **Qdrant**, combines **dense and sparse retrieval**, applies **Reciprocal Rank Fusion (RRF)** and **Cross-Encoder reranking**, then generates grounded answers using an LLM.

### Core Pipeline

```text
                    DOCUMENTS
                  PDF / Markdown
                        │
                        ▼
              ┌──────────────────┐
              │ Document Loader  │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Text Extraction  │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Cleaning &       │
              │ Normalization    │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Structure        │
              │ Detection        │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Semantic /       │
              │ Structure-aware  │
              │ Chunking         │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Metadata         │
              │ Enrichment       │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Embeddings       │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │     Qdrant       │
              │   Vector Store   │
              └────────┬─────────┘
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
      Dense Retrieval      Sparse Retrieval
             │                   │
             └─────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │   RRF Fusion     │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Cross-Encoder    │
              │   Reranking      │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │   LLM / Ollama   │
              └────────┬─────────┘
                       ▼
              ┌──────────────────┐
              │ Answer + Sources │
              └──────────────────┘
```

---

# 🎯 Objectives

The project aims to build a reliable and explainable RAG architecture capable of:

* 📄 Ingesting PDF and Markdown documents
* 🧹 Cleaning and normalizing extracted content
* 🧠 Reconstructing document structure
* ✂️ Performing structure-aware / semantic chunking
* 🏷️ Enriching chunks with metadata
* 🔢 Generating semantic embeddings
* 🔎 Performing dense vector retrieval
* 🔤 Supporting sparse / BM25 retrieval
* 🔀 Combining retrieval results with RRF
* 🎯 Reranking candidates with a Cross-Encoder
* 🤖 Generating grounded answers with an LLM
* 📚 Providing source citations
* 🔗 Maintaining document provenance
* 📊 Evaluating RAG quality with DeepEval
* 🐳 Supporting containerized deployment
* 📈 Providing a foundation for observability and monitoring

---

# 🏗️ Architecture

```text
                           ┌───────────────────┐
                           │       Client      │
                           └─────────┬─────────┘
                                     │
                                     ▼
                           ┌───────────────────┐
                           │      FastAPI      │
                           └─────────┬─────────┘
                                     │
                    ┌────────────────┴────────────────┐
                    │                                 │
                    ▼                                 ▼
          ┌──────────────────┐              ┌──────────────────┐
          │    Ingestion     │              │    RAG Query     │
          │     Service      │              │     Service      │
          └────────┬─────────┘              └────────┬─────────┘
                   │                                 │
                   ▼                                 ▼
          ┌──────────────────┐              ┌──────────────────┐
          │ Structure &      │              │ Hybrid Retrieval │
          │ Chunking         │              │                  │
          └────────┬─────────┘              └────────┬─────────┘
                   │                                 │
                   ▼                                 ▼
          ┌──────────────────┐              ┌──────────────────┐
          │   Embeddings     │              │    RRF Fusion    │
          └────────┬─────────┘              └────────┬─────────┘
                   │                                 │
                   └──────────────┬──────────────────┘
                                  ▼
                       ┌─────────────────────┐
                       │       Qdrant        │
                       │ Dense + Sparse Data │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │   Cross-Encoder     │
                       │     Reranking       │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │     Ollama / LLM    │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │ Answer + Citations  │
                       └─────────────────────┘
```

---

# 📥 Knowledge Ingestion

The ingestion layer is responsible for transforming raw documents into structured knowledge units.

```text
PDF / Markdown
      │
      ▼
Extraction
      │
      ▼
Cleaning
      │
      ▼
Structure Reconstruction
      │
      ▼
Chunking
      │
      ▼
Metadata Enrichment
      │
      ▼
Embeddings
      │
      ▼
Qdrant
```

## Supported Documents

* PDF
* Markdown

### PDF

PDF documents are processed using **PyMuPDF** while preserving page-level information.

### Markdown

Markdown structure is used to reconstruct:

```text
# Section
## Subsection
### Subsection
```

This allows the system to preserve the logical hierarchy of the source document.

---

# ✂️ Intelligent Chunking

Instead of blindly splitting documents every N characters, the platform uses document structure to create more meaningful knowledge units.

### Traditional approach

```text
Document
   │
   ▼
Fixed-size chunks
   │
   ├── Chunk 1
   ├── Chunk 2
   ├── Chunk 3
   └── ...
```

### Proposed approach

```text
Document
   │
   ▼
Sections
   │
   ▼
Subsections
   │
   ▼
Paragraphs
   │
   ▼
Token limits + overlap
   │
   ▼
Contextualized chunks
```

The objective is to avoid cutting concepts or contextual relationships at arbitrary boundaries.

---

# 🏷️ Metadata & Provenance

Each knowledge chunk contains both its textual content and contextual metadata.

Example:

```json
{
  "chunk_id": "chunk_001",
  "document_id": "doc_001",
  "document_name": "cybersecurity.pdf",
  "text": "JWT is an authentication mechanism...",
  "page": 42,
  "section": "Authentication",
  "subsection": "JWT",
  "chunk_index": 15,
  "document_type": "pdf"
}
```

Metadata enables:

* 🔗 Source citations
* 🔍 Metadata filtering
* 🐛 Retrieval debugging
* 📍 Document provenance
* 📄 Page-level traceability

### Provenance Flow

```text
LLM Answer
    │
    ▼
Retrieved Chunk
    │
    ▼
Section
    │
    ▼
Page 42
    │
    ▼
Original Document
```

The goal is not only to generate an answer, but also to understand **where the information came from**.

---

# 🔎 Hybrid Retrieval

The platform combines multiple retrieval strategies.

## Dense Retrieval

Dense retrieval uses embeddings to identify semantically similar chunks.

```text
Query
  │
  ▼
Embedding Model
  │
  ▼
Vector Search
  │
  ▼
Semantic Results
```

## Sparse Retrieval

Sparse retrieval focuses on lexical / keyword relevance using approaches such as BM25 or sparse vectors.

```text
Query
  │
  ▼
Sparse Retrieval
  │
  ▼
Keyword Results
```

### Hybrid Strategy

```text
             Query
               │
       ┌───────┴────────┐
       ▼                ▼
Dense Retrieval   Sparse Retrieval
       │                │
       └───────┬────────┘
               ▼
          RRF Fusion
```

This allows semantic similarity and exact keyword matching to complement each other.

---

# 🔀 Reciprocal Rank Fusion

The results returned by the different retrieval strategies are combined using **Reciprocal Rank Fusion (RRF)**.

```text
Dense Results
      │
      ├────────────┐
      │            │
      ▼            ▼
                  RRF
      ▲            ▲
      │            │
Sparse Results ────┘
                   │
                   ▼
            Unified Ranking
```

The resulting candidate set is then passed to the reranking stage.

---

# 🎯 Cross-Encoder Reranking

The hybrid retriever can initially return a larger candidate set.

Example:

```text
Hybrid Retrieval
      │
      ▼
Top 20 candidates
      │
      ▼
Cross-Encoder
      │
      ▼
Top 3–5 relevant chunks
```

The Cross-Encoder evaluates the relationship between the question and each candidate chunk to improve the final ranking.

---

# 🤖 Generation

The final retrieved context is passed to the LLM.

```text
User Question
      │
      ▼
Hybrid Retrieval
      │
      ▼
RRF
      │
      ▼
Cross-Encoder
      │
      ▼
Relevant Context
      │
      ▼
LLM
      │
      ▼
Answer + Sources
```

The generation layer is designed to:

* Ground responses in retrieved context
* Reduce unsupported claims
* Provide source references
* Refuse to fabricate information when sufficient context is unavailable

---

# 🧪 Evaluation

RAG systems should be evaluated rather than judged only by subjective examples.

The project includes an evaluation strategy based on **DeepEval**.

## Chunking Comparison

```text
Fixed-size
    │
    VS
Recursive
    │
    VS
Structure-aware
    │
    VS
Semantic
```

## Retrieval Comparison

```text
Dense Retrieval
       │
       VS
Hybrid Retrieval
       │
       VS
Hybrid + Reranking
```

Potential evaluation dimensions include:

* Faithfulness
* Answer Relevancy
* Context Relevance
* Retrieval quality

The objective is to measure whether each architectural improvement actually improves the final RAG system.

---

# 🛠️ Technology Stack

| Layer               | Technology                      |
| ------------------- | ------------------------------- |
| Language            | Python                          |
| API                 | FastAPI                         |
| PDF Processing      | PyMuPDF                         |
| Markdown Processing | Python-Markdown / Custom Parser |
| Chunking            | Custom / LangChain              |
| Embeddings          | Sentence Transformers           |
| Vector Database     | Qdrant                          |
| Sparse Retrieval    | BM25 / Sparse Vectors           |
| Fusion              | Reciprocal Rank Fusion          |
| Reranking           | Cross-Encoder                   |
| LLM Runtime         | Ollama                          |
| RAG                 | Modular Python / LangChain      |
| Evaluation          | DeepEval                        |
| Containers          | Docker                          |
| Monitoring          | Prometheus / Grafana            |
| Testing             | Pytest                          |

---

# 📂 Project Structure

```text
enterprise-hybrid-rag-platform/
│
├── app/
│   │
│   ├── ingestion/
│   │   ├── loaders/
│   │   │   ├── pdf_loader.py
│   │   │   └── markdown_loader.py
│   │   │
│   │   ├── cleaning/
│   │   │   └── text_cleaner.py
│   │   │
│   │   ├── structure/
│   │   │   ├── section_detector.py
│   │   │   └── hierarchy_builder.py
│   │   │
│   │   ├── chunking/
│   │   │   ├── semantic_chunker.py
│   │   │   └── chunk.py
│   │   │
│   │   ├── metadata/
│   │   │   └── metadata_builder.py
│   │   │
│   │   └── pipeline.py
│   │
│   ├── embeddings/
│   │   └── embedding_service.py
│   │
│   ├── retrieval/
│   │   ├── dense_retriever.py
│   │   ├── sparse_retriever.py
│   │   ├── hybrid_retriever.py
│   │   └── rrf.py
│   │
│   ├── reranking/
│   │   └── cross_encoder.py
│   │
│   ├── generation/
│   │   └── llm_service.py
│   │
│   ├── vectorstore/
│   │   └── qdrant_client.py
│   │
│   └── api/
│       └── routes/
│
├── tests/
│
├── documents/
│
├── evaluation/
│
├── docker/
│
├── .env.example
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── README.md
```

---

# 🚀 Installation

## Clone the repository

```bash
git clone https://github.com/ghazlio00o/enterprise-hybrid-rag-platform.git
cd enterprise-hybrid-rag-platform
```

## Create virtual environment

### Linux / macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Windows

```powershell
python -m venv .venv
.venv\Scripts\activate
```

## Install dependencies

```bash
pip install -r requirements.txt
```

---

# ⚙️ Configuration

Create your environment file:

```bash
cp .env.example .env
```

Example:

```env
QDRANT_HOST=localhost
QDRANT_PORT=6333

OLLAMA_HOST=http://localhost:11434
LLM_MODEL=<your-llm-model>

EMBEDDING_MODEL=<your-embedding-model>

TOP_K=20
RERANK_TOP_K=5
```

---

# 🐳 Docker

Start the infrastructure:

```bash
docker compose up -d
```

Check services:

```bash
docker compose ps
```

Stop services:

```bash
docker compose down
```

---

# ▶️ Run the API

```bash
uvicorn app.main:app --reload
```

API:

```text
http://localhost:8000
```

Swagger documentation:

```text
http://localhost:8000/docs
```

---

# 🔄 Example Workflow

## 1. Upload a document

```http
POST /documents/upload
```

Example:

```text
cybersecurity.pdf
```

## 2. Ingestion

```text
Document
   ↓
Extraction
   ↓
Cleaning
   ↓
Structure Detection
   ↓
Chunking
   ↓
Metadata
   ↓
Embeddings
   ↓
Qdrant
```

## 3. Query

```http
POST /query
```

Example:

```json
{
  "question": "How does JWT authentication work?"
}
```

## 4. Retrieval

```text
Question
   │
   ├── Dense Retrieval
   │
   └── Sparse Retrieval
          │
          ▼
       RRF Fusion
          │
          ▼
       Top 20
          │
          ▼
    Cross-Encoder
          │
          ▼
        Top 3–5
```

## 5. Generation

```text
Retrieved Context
        │
        ▼
       LLM
        │
        ▼
Answer + Sources
```

---

# 📊 Monitoring

The production-oriented architecture can be extended with:

```text
Application
     │
     ▼
Prometheus
     │
     ▼
Grafana
```

Potential metrics:

* API latency
* Retrieval latency
* Reranking latency
* LLM latency
* Number of queries
* Number of documents
* Number of chunks
* Error rate
* Throughput

---

# 🧪 Testing

Run all tests:

```bash
pytest
```

Verbose mode:

```bash
pytest -v
```

---

# 🗺️ Roadmap

## Phase 1 — Knowledge Preparation

* [x] Project architecture
* [ ] PDF ingestion
* [ ] Markdown ingestion
* [ ] Text extraction
* [ ] Cleaning
* [ ] Structure detection
* [ ] Metadata enrichment
* [ ] Structure-aware chunking

## Phase 2 — Vector Search

* [ ] Embedding generation
* [ ] Qdrant integration
* [ ] Dense retrieval
* [ ] Metadata filtering

## Phase 3 — Hybrid Retrieval

* [ ] Sparse retrieval
* [ ] BM25
* [ ] Dense + sparse retrieval
* [ ] RRF fusion

## Phase 4 — Reranking & Generation

* [ ] Cross-Encoder
* [ ] Ollama integration
* [ ] Context construction
* [ ] Source citations
* [ ] "I don't know" handling

## Phase 5 — Evaluation

* [ ] DeepEval integration
* [ ] Faithfulness evaluation
* [ ] Answer relevancy
* [ ] Retrieval comparison
* [ ] Chunking comparison

## Phase 6 — Production

* [ ] Docker
* [ ] CI/CD
* [ ] Automated testing
* [ ] Prometheus
* [ ] Grafana
* [ ] Production deployment

---

# 💡 Engineering Principles

### Modular Design

Each major RAG component is isolated:

```text
Ingestion
   ↓
Chunking
   ↓
Embeddings
   ↓
Retrieval
   ↓
Reranking
   ↓
Generation
   ↓
Evaluation
```

### Traceability

Every generated answer should be traceable back to its retrieved knowledge chunks and original source document.

### Retrieval Quality

The system combines semantic and lexical retrieval instead of relying exclusively on vector similarity.

### Evaluation-Driven Development

Different chunking and retrieval strategies are experimentally compared using measurable evaluation metrics.

### Production-Oriented Architecture

The platform is designed around modular services, containerization, testing and observability.

---

# 🎓 Skills Demonstrated

This project demonstrates practical experience with:

* Data Engineering
* Information Retrieval
* Natural Language Processing
* Large Language Models
* Retrieval-Augmented Generation
* Vector Databases
* Hybrid Search
* Semantic Search
* Sparse Retrieval
* Reranking
* MLOps
* RAG Evaluation
* API Development
* Docker
* Observability
* Software Architecture

---

# 👨‍💻 Author

## Mohamed Amine Ghazli

**Data Science & Big Data Student**

Interested in:

* Data Engineering
* Data Science
* MLOps
* Generative AI
* Machine Learning
* Cloud & DevOps

### Links

* GitHub: https://github.com/ghazlio00o
* Portfolio: https://medghazli.com

---

# 📄 License

This project is developed for educational, research and portfolio purposes.
