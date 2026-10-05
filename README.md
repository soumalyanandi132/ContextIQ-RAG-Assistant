# 🔍 GraphRAG Knowledge Assistant

A **Knowledge Graph-based Retrieval-Augmented Generation (GraphRAG)** system that extracts entities and relationships from unstructured text, constructs a knowledge graph using Neo4j, and performs multi-hop retrieval to generate context-aware answers with source attribution.

The application uses locally hosted Large Language Models through **Ollama**, allowing the complete retrieval and generation pipeline to run locally.

---

## 🚀 Features

- 🧠 **LLM-Based Knowledge Extraction** — Extracts entities, concepts, technologies, organizations, and relationships from unstructured text.
- 🕸️ **Knowledge Graph Construction** — Stores extracted entities and relationships as graph structures in Neo4j.
- 🔗 **Multi-Hop Graph Retrieval** — Traverses connected entities to retrieve information across multiple relationships.
- 🤖 **Local LLM Inference** — Uses Ollama with models such as Llama 3.2, Mistral, and Phi-3.
- 📚 **Source Attribution** — Associates generated responses with their supporting source context.
- 🔎 **Reasoning Trace** — Displays graph traversal and retrieval steps used while answering a query.
- 🖥️ **Interactive Interface** — Streamlit-based interface for document ingestion, querying, and graph monitoring.
- 🐳 **Docker Support** — Docker Compose configuration for running Neo4j, Ollama, and the Streamlit application.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| **Python** | Core application and retrieval logic |
| **Llama 3.2** | Local language model for extraction and generation |
| **Ollama** | Local LLM inference engine |
| **Neo4j** | Knowledge graph database |
| **Cypher** | Graph querying and traversal |
| **Streamlit** | Interactive web interface |
| **Docker** | Containerized application setup |

---

## 🏗️ System Architecture

```text
                Unstructured Text
                       │
                       ▼
                Local LLM (Ollama)
                       │
                       ▼
          Entity & Relationship Extraction
                       │
                       ▼
                ┌─────────────┐
                │    Neo4j    │
                │ Knowledge   │
                │    Graph    │
                └──────┬──────┘
                       │
                       │
User Question ─────────┘
        │
        ▼
Initial Graph Node Search
        │
        ▼
Multi-Hop Graph Traversal
        │
        ▼
Relevant Graph Context
        │
        ▼
Local LLM Generation
        │
        ▼
 Answer + Source Attribution
```

---

## ⚙️ How It Works

### 1. Knowledge Extraction

The user provides unstructured text through the Streamlit interface.

The selected local LLM analyzes the input and extracts structured information consisting of:

- Entities
- Entity types
- Entity descriptions
- Relationships between entities
- Relationship context

For example:

```text
Artificial Intelligence
        │
        └── contains ──> Machine Learning
                              │
                              └── contains ──> Deep Learning
```

The extracted information is converted into graph nodes and edges.

---

### 2. Knowledge Graph Construction

Extracted entities are represented as **GraphNodes**, while relationships between entities are represented as **GraphEdges**.

The structures are stored inside a **Neo4j graph database**.

Example:

```text
(Deep Learning)
       │
       │ SUBSET_OF
       ▼
(Machine Learning)
       │
       │ SUBSET_OF
       ▼
(Artificial Intelligence)
```

This graph representation makes it possible to retrieve information based on relationships between concepts rather than relying only on isolated text.

---

### 3. Query Processing

When the user submits a question, the application searches the Neo4j knowledge graph for matching entities and descriptions.

Example question:

```text
How are Artificial Intelligence, Machine Learning and Deep Learning related?
```

Relevant graph nodes are identified as starting points for retrieval.

---

### 4. Multi-Hop Graph Retrieval

After identifying relevant nodes, the system traverses their connected relationships.

The current implementation supports graph traversal up to **two hops**, allowing information connected through intermediate entities to be included in the retrieved context.

Example:

```text
Deep Learning
      │
      ▼
Machine Learning
      │
      ▼
Artificial Intelligence
```

This enables the system to answer questions requiring information distributed across related graph entities.

---

### 5. Context Generation

The retrieved nodes and relationships are converted into structured context for the language model.

Each retrieved context element receives a reference identifier such as:

```text
[1] Entity: Artificial Intelligence - Field of computer science...

[2] Entity: Machine Learning - Subfield of Artificial Intelligence...

[3] Entity: Deep Learning - Uses multi-layer neural networks...
```

The resulting context is passed to the selected local language model.

---

### 6. Answer Generation & Source Attribution

The LLM generates an answer using the retrieved graph context and is instructed to include inline source references.

Example:

```text
Machine Learning is a subset of Artificial Intelligence [1],
while Deep Learning is a specialized subset of Machine Learning [2].
```

The application identifies these references and maps them back to their corresponding source context.

This allows users to inspect the information used to generate the response.

---

## 📖 Example

### Input Document 1

```text
Artificial Intelligence is a field of computer science focused on
developing systems capable of performing tasks that typically require
human intelligence.

Machine Learning is a subset of Artificial Intelligence that enables
systems to learn patterns from data.

Deep Learning is a subset of Machine Learning that uses multi-layer
neural networks.
```

### Input Document 2

```text
Generative AI refers to artificial intelligence systems capable of
generating new content such as text, images, audio, and code.

Large Language Models use transformer architectures and are trained
on large collections of textual data.

Retrieval-Augmented Generation combines information retrieval with
language generation to provide additional context to language models.
```

### Example Extracted Knowledge

```text
Artificial Intelligence
        │
        ├── Machine Learning
        │       │
        │       └── Deep Learning
        │
        └── Generative AI
                 │
                 └── Large Language Models
```

### Example Query

```text
How are Artificial Intelligence, Machine Learning and Deep Learning related?
```

The system retrieves relevant graph entities, follows their relationships, constructs the required context, and sends it to the selected LLM to generate an attributed response.

---

## 📂 Project Structure

```text
GraphRAG-Knowledge-Assistant/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── README.md
└── .gitignore
```

### File Description

**`app.py`**

Contains the primary application logic including:

- Graph node and edge data structures
- Neo4j database operations
- LLM-based entity extraction
- Relationship extraction
- Multi-hop graph retrieval
- Source attribution
- Streamlit user interface

**`docker-compose.yml`**

Configures the services required by the application:

- Neo4j
- Ollama
- Streamlit application

**`Dockerfile`**

Defines the container configuration for the Streamlit application.

**`requirements.txt`**

Contains the required Python dependencies.

---

# 🚀 Installation

## Option 1 — Run Locally

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/GraphRAG-Knowledge-Assistant.git
cd GraphRAG-Knowledge-Assistant
```

Replace `YOUR_USERNAME` with your GitHub username.

---

### 2. Install Python Dependencies

```bash
pip install -r requirements.txt
```

---

### 3. Install Ollama

Install Ollama on your system and download the Llama 3.2 model:

```bash
ollama pull llama3.2
```

Verify that Ollama is running:

```bash
ollama list
```

---

### 4. Start Neo4j

You can start Neo4j using Docker:

```bash
docker run -d \
  --name graphrag-neo4j \
  -p 7474:7474 \
  -p 7687:7687 \
  -e NEO4J_AUTH=neo4j/password \
  neo4j:latest
```

Neo4j Browser will be available on port:

```text
7474
```

The application connects to Neo4j through the Bolt protocol on port:

```text
7687
```

Default credentials:

```text
Username: neo4j
Password: password
```

---

### 5. Run the Application

```bash
streamlit run app.py
```

The Streamlit application will normally be available on:

```text
localhost:8501
```

---

# 🐳 Running with Docker Compose

The repository also provides a Docker Compose configuration for running the required services.

Run:

```bash
docker compose up --build
```

This starts:

```text
Neo4j
   │
   ├── 7474  → Neo4j Browser
   └── 7687  → Bolt Connection

Ollama
   │
   └── 11434 → Local LLM API

GraphRAG Application
   │
   └── 8501  → Streamlit Interface
```

To stop the services:

```bash
docker compose down
```

---

# 🖥️ Using the Application

### Step 1 — Configure the Graph Database

Enter the Neo4j configuration in the sidebar.

For a local installation:

```text
URI: bolt://127.0.0.1:7687
Username: neo4j
Password: password
```

When running completely through Docker Compose, the application can connect using:

```text
bolt://neo4j:7687
```

---

### Step 2 — Select the LLM

Choose one of the supported local models:

```text
Llama 3.2
Mistral
Phi-3
```

Make sure the selected model has already been downloaded through Ollama.

For example:

```bash
ollama pull llama3.2
```

---

### Step 3 — Ingest Knowledge

Navigate to:

```text
Document Ingestion
```

Enter the source text and provide a document label.

Click:

```text
Extract & Ingest Nodes
```

The LLM extracts entities and relationships and stores them in Neo4j.

---

### Step 4 — Ask Questions

Navigate to:

```text
Ask Assistant
```

Enter a question related to the indexed information.

The application will:

```text
Question
   ↓
Search Knowledge Graph
   ↓
Identify Relevant Nodes
   ↓
Perform Multi-Hop Traversal
   ↓
Construct Context
   ↓
Generate LLM Response
   ↓
Validate Source References
```

---

### Step 5 — Inspect Sources

After generating the answer, the interface displays:

- Generated response
- Graph traversal logs
- Source document
- Supporting context
- Reasoning trace

---

# 🔬 Database Monitoring

The **Database Status** section allows users to inspect:

- Total graph nodes
- Total graph relationships

The knowledge graph can also be cleared directly from the application when a fresh graph is required.

---

# 🔐 Local LLM Processing

The project uses **Ollama** for language-model inference.

This makes it possible to run supported models locally instead of depending on a hosted LLM API for the current generation pipeline.

Supported model options in the application include:

```text
llama3.2
mistral
phi3
```

---

# 🎯 Key Concepts Demonstrated

This project demonstrates practical implementation of:

- Generative AI
- Retrieval-Augmented Generation
- GraphRAG
- Knowledge Graphs
- Large Language Models
- Entity Extraction
- Relationship Extraction
- Multi-Hop Retrieval
- Graph Databases
- Neo4j
- Cypher
- Source Attribution
- Local LLM Inference
- Docker-based service orchestration

---

# 🔮 Future Improvements

Potential extensions include:

- PDF and document file upload
- Automatic text chunking for large documents
- Embedding-based semantic retrieval
- Hybrid vector + graph retrieval
- Interactive knowledge graph visualization
- Page-level citation tracking
- Improved entity deduplication
- Conversation history
- Graph retrieval evaluation
- Support for additional document formats
- REST API using FastAPI

---

## 👨‍💻 Author

**Soumalya Nandi**

B.Tech Computer Science & Engineering  
The LNM Institute of Information Technology (LNMIIT), Jaipur

---

## 📄 License

This project is intended for educational and research purposes.
