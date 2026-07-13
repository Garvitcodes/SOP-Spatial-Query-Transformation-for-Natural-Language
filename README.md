<div align="center">

# 🗺️ BITS Pilani Spatial AI Agent

**An intelligent, geospatial campus guide powered by Small Language Models and real-world OpenStreetMap data.**

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-PostGIS-336791?style=for-the-badge&logo=postgresql&logoColor=white)](https://postgis.net)
[![Gradio](https://img.shields.io/badge/UI-Gradio-FF7C00?style=for-the-badge&logo=gradio&logoColor=white)](https://gradio.app)
[![llama.cpp](https://img.shields.io/badge/Inference-llama.cpp-8B5CF6?style=for-the-badge)](https://github.com/ggml-org/llama.cpp)
[![License: MIT](https://img.shields.io/badge/License-MIT-22C55E?style=for-the-badge)](LICENSE)

*Ask anything about campus — in Hindi, English, or Hinglish — and get answers from your very own AI Senior.*

</div>

---

## 📖 Overview

The **BITS Pilani Spatial AI Agent** is a fully offline, end-to-end AI pipeline that transforms natural language questions into precise geospatial database queries. It is designed to act as an interactive campus guide — understanding Hinglish queries from students, translating them into PostGIS SQL, executing against a real OpenStreetMap database, and responding in the warm, helpful persona of a BITSian Senior.

> **Example:** *"Yaar, koi photogenic spot hai campus mein?"*
> → The agent maps "photogenic" → `tourism`, `amenity` OSM tags → runs a spatial SQL query → responds with curated location suggestions, just like a Senior would.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🧠 **Dual-Model Architecture** | `defog/llama-3-sqlcoder-8b` for Text-to-SQL; `Qwen2.5-1.5B-Instruct` / Groq `Llama-3.3-70B` for NLG |
| 🗄️ **Geospatial Backend** | PostgreSQL + PostGIS database populated with live OSM data via `osmnx` |
| ⚡ **Fully Offline Inference** | Quantized GGUF models via `llama.cpp` — no cloud API required |
| 🎭 **BITSian Senior Persona** | Responds in Hinglish with the warmth and wit of a campus senior |
| 🔧 **Strict SQL Rules** | Custom prompt engineering prevents invalid multi-token `AND` filters (enforces `OR`) |
| 📊 **Rigorous Evaluation** | SQLGlot AST-based structural checks + semantic F1-scoring on a 50-query golden benchmark |
| 🖥️ **Gradio Web UI** | Chat-like interface for a smooth, interactive user experience |

---

## 🏗️ System Architecture

The pipeline processes each user query through four sequential stages:

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Input (Hinglish)                    │
└──────────────────────────────┬──────────────────────────────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │  Stage 1 · Intent Translation  │
              │  Qwen2.5 / Llama-3.3-70B       │
              │  Hindi/Hinglish → English query │
              └────────────────┬───────────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │  Stage 2 · Text-to-SQL         │
              │  defog/llama-3-sqlcoder-8b     │
              │  English query → PostGIS SQL   │
              └────────────────┬───────────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │  Stage 3 · DB Execution        │
              │  PostgreSQL + PostGIS          │
              │  SQL → Geo-tagged Results      │
              └────────────────┬───────────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │  Stage 4 · Persona Generation  │
              │  Qwen2.5 / Llama-3.3-70B       │
              │  Results → Hinglish Response   │
              └────────────────────────────────┘
```

---

## 📂 Repository Structure

```
bits-pilani-spatial-ai/
│
├── 📁 PPTs/                                                    # Project presentations (PPRs)
├── 📁 Reports/                                                 # Contains midsem report, final report, and IEEE format report
│
├── 📓 01_baseline_model_exploration                            # Baseline API experiments using Gemini/Groq and initial prompt design
├── 📓 02_streamlit_interface.ipynb                             # Early Streamlit-based web UI prototype
├── 📓 03_data_schema_extraction.ipynb                          # PostgreSQL/PostGIS setup, DB configuration, and OSM data ingestion
├── 📓 04_tester_for_sqlcoder.ipynb                             # SQLGlot AST structural checks and F1 semantic evaluation framework
├── 📓 05_Fully_offline_chatbot_using_llama.cpp.ipynb           # Terminal-based offline chat loop using quantized GGUF models
├── 📓 Final_Prototype.ipynb                                    # Final Gradio Web UI combining full Hinglish intent translation & SQL execution
│
├── 📄 Manually created Dataset for Sqlcoder Testing....pdf     # Hand-curated golden benchmark dataset for testing queries
├── 📄 README.md                                                # Project documentation
└── 🎥 Video demo.mp4                                           # Video demonstration of the final working prototype
```

---

## 🚀 Setup & Installation

### Prerequisites

- Python **3.10+**
- **PostgreSQL** with the **PostGIS** extension
- **NVIDIA GPU** *(recommended for local SLM inference)*

---

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/bits-pilani-spatial-ai.git
cd bits-pilani-spatial-ai
```

### 2. Install Python Dependencies

```bash
pip install -r requirements.txt
```

> **Key dependencies:** `osmnx`, `psycopg2`, `sqlglot`, `gradio`, `llama-cpp-python`, `transformers`

### 3. Configure the PostgreSQL Database

```bash
# Set postgres user password
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'postgres';"

# Create the campus database
sudo -u postgres createdb pilani_db

# Enable the PostGIS spatial extension
sudo -u postgres psql -d pilani_db -c "CREATE EXTENSION postgis;"
```

### 4. Ingest OSM Data

Open and run **`03_data_schema_extraction.ipynb`** to download the BITS Pilani campus boundary from OpenStreetMap and populate the `pilani_db` database with nodes, ways, and spatial geometries.

### 5. Download Model Weights

| Model | Role | Format |
|---|---|---|
| `defog/llama-3-sqlcoder-8b` | Text-to-SQL | GGUF (quantized) |
| `Qwen2.5-1.5B-Instruct` | NLG / Persona | GGUF (quantized) |

Place downloaded `.gguf` files in a `/models` directory at the project root.

> **Alternatively**, configure a [Groq API key](https://console.groq.com) for cloud-based `Llama-3.3-70B` inference without a local GPU.

### 6. Launch the Application

Open and run **`Final_Prototype.ipynb`**. The Gradio interface will be available at `http://localhost:7860`.

---

## 📊 Evaluation

The project includes a rigorous two-pronged evaluation framework in **`04_tester_for_sqlcoder.ipynb`**:

| Method | Tool | What it Checks |
|---|---|---|
| **Structural (AST)** | SQLGlot | Query structure, clause correctness, operator validity |
| **Semantic (F1)** | Custom scorer | Result-set overlap against golden benchmark answers |

The golden benchmark (`Manually created Dataset for Sqlcoder Testing....pdf`) is a manually curated set of **50 diverse queries** covering amenities, study spaces, landmarks, food, and recreational spots across campus.

---

## 🧩 Key Design Decisions

**Why `OR` for multi-token name filters?**
When a user asks for a "reading room", a naive `AND` filter across multiple tags (`amenity='reading' AND name='room'`) returns zero results. The system prompt explicitly enforces `OR`-based filtering (`amenity='reading' OR name LIKE '%room%'`), dramatically improving recall.

**Why llama.cpp + GGUF?**
Running 8B-parameter models quantized to 4-bit precision enables inference on consumer-grade GPUs (≥8GB VRAM) while maintaining competitive accuracy — making the entire pipeline portable and offline-first.

**Why a denormalized schema?**
OSM data is ingested into a single flat table with wide columns for all common tags. This trades storage efficiency for query simplicity, letting SQLCoder generate clean, readable SQL without complex joins.

---

## 🎬 Demo

A full walkthrough of the working prototype is available in **`Video demo.mp4`** included in the repository.

---

## 🤝 Contributing

Contributions are welcome! If you'd like to improve the prompt templates, extend the benchmark dataset, or add new amenity tag mappings:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add your feature'`
4. Push and open a Pull Request

---

## 📜 License

This project is licensed under the **MIT License**. See [LICENSE](LICENSE) for details.

---

<div align="center">

Made with ❤️ at **BITS Pilani**

</div>
