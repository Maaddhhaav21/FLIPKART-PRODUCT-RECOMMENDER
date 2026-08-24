# 🛍️ Flipkart Product Recommender

A **Retrieval-Augmented Generation (RAG) chatbot** that answers product-related questions using real Flipkart customer reviews. Built with LangChain, AstraDB vector search, and Groq-hosted LLMs, and served through a lightweight Flask web app with built-in Prometheus monitoring.

Ask natural-language questions like *"Is this phone good for gaming?"* or *"What do customers say about the battery life?"* and get concise, context-grounded answers pulled directly from real product reviews — with full conversational memory across the session.

---

## ✨ Features

- **Conversational RAG pipeline** — history-aware retriever reformulates follow-up questions into standalone queries before retrieval, enabling natural multi-turn conversations.
- **Semantic search over reviews** — product reviews are embedded and stored in **AstraDB** (vector database) for fast, relevant retrieval.
- **LLM-powered answers** — uses **Groq's** `openai/gpt-oss-20b` model via `langchain-groq` for fast, low-latency responses.
- **Web chat interface** — simple Flask + HTML/CSS front end for interacting with the bot.
- **Observability built-in** — a `/metrics` endpoint exposes Prometheus metrics (e.g. total requests) out of the box.
- **Clean, modular codebase** — ingestion, conversion, and chain-building are separated into their own modules for easy extension.

---

## 🏗️ Architecture

```
Flipkart reviews (CSV)
        │
        ▼
 DataConverter  ──►  LangChain Documents (review + product title)
        │
        ▼
 DataIngestor   ──►  HuggingFace Embeddings ──►  AstraDB Vector Store
        │
        ▼
 RAGChainBuilder
   ├─ History-aware retriever (rewrites follow-up Qs using chat history)
   ├─ Retriever (top-k similarity search over AstraDB)
   └─ Stuff-documents QA chain (Groq LLM answers using retrieved context)
        │
        ▼
   Flask App (/  and  /get)  ──►  Chat UI
        │
        ▼
   /metrics  ──►  Prometheus
```

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3 |
| Web Framework | Flask |
| Orchestration | LangChain (`langchain-classic`, `langchain-community`) |
| Vector Store | AstraDB (`langchain-astradb`) |
| Embeddings | HuggingFace Inference Endpoint (`BAAI/bge-base-en-v1.5`) |
| LLM | Groq (`openai/gpt-oss-20b` via `langchain-groq`) |
| Monitoring | Prometheus (`prometheus_client`) |
| Data | Pandas |

---

## 📁 Project Structure

```
FLIPKART-PRODUCT-RECOMMENDER/
├── app.py                     # Flask app entry point (routes: /, /get, /metrics)
├── flipkart/
│   ├── config.py               # Environment-based configuration
│   ├── data_converter.py       # Converts raw CSV reviews into LangChain Documents
│   ├── data_ingestion.py       # Embeds & ingests documents into AstraDB
│   └── rag_chain.py            # Builds the history-aware RAG chain
├── utils/
│   ├── custom_exception.py     # Custom exception handling
│   └── logger.py                # Logging utility
├── data/
│   └── flipkart_product_review.csv   # Source dataset (product_title, review)
├── static/
│   └── style.css                # Chat UI styling
├── templates/
│   └── index.html               # Chat UI markup
├── requirements.txt
├── setup.py
└── README.md
```

---

## ⚙️ Prerequisites

- Python 3.10+
- An [AstraDB](https://www.datastax.com/products/datastax-astra) database with a vector-enabled collection
- A [Groq API key](https://console.groq.com/) for LLM inference
- A HuggingFace access token (for the embedding endpoint)

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/Maaddhhaav21/FLIPKART-PRODUCT-RECOMMENDER.git
cd FLIPKART-PRODUCT-RECOMMENDER
```

### 2. Create a virtual environment & install dependencies

```bash
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Or install as an editable package:

```bash
pip install -e .
```

### 3. Configure environment variables

Create a `.env` file in the project root:

```env
ASTRA_DB_API_ENDPOINT=your_astra_db_api_endpoint
ASTRA_DB_APPLICATION_TOKEN=your_astra_db_application_token
ASTRA_DB_KEYSPACE=your_astra_db_keyspace
GROQ_API_KEY=your_groq_api_key
```

### 4. Ingest the review data into AstraDB

Run the ingestion script once to embed and load `data/flipkart_product_review.csv` into your AstraDB collection:

```bash
python flipkart/data_ingestion.py
```

> This populates the `flipkart_database` collection in AstraDB. Subsequent app runs load the existing collection rather than re-ingesting.

### 5. Run the app

```bash
python app.py
```

The app starts on **http://localhost:5001**. Open it in your browser and start chatting with the recommender.

---

## 🔌 API / Routes

| Route | Method | Description |
|---|---|---|
| `/` | GET | Renders the chat UI |
| `/get` | POST | Accepts `msg` form field, returns the bot's answer |
| `/metrics` | GET | Prometheus-formatted request metrics |

---

## 📊 Dataset

The project uses `data/flipkart_product_review.csv`, containing Flipkart product titles and their corresponding customer reviews (`product_title`, `review`). Each review is embedded as a separate document, tagged with its product name as metadata, enabling the retriever to surface the most relevant reviews for a given query.

---

## 🗺️ Roadmap

- [ ] Add automated evaluation of RAG answer quality
- [ ] Support multi-product comparison queries
- [ ] Containerize with Docker for easier deployment
- [ ] Add CI/CD pipeline
- [ ] Persist chat history across sessions (currently in-memory per session)

---

## 🤝 Contributing

Contributions are welcome! Please open an issue to discuss what you'd like to change, then submit a pull request.

1. Fork the repo
2. Create your feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes
4. Push to the branch and open a PR

---

## 👤 Author

**Madhav Manoj**
GitHub: [@Maaddhhaav21](https://github.com/Maaddhhaav21)