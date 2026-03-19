# NPMAI-RAG-API-Pipeline

A powerful **FastAPI-based multi-modal ingestion system** that processes PDFs, scanned documents, images, videos, YouTube links, and text files — then optionally performs semantic retrieval using FAISS + HuggingFace embeddings and refines answers using an LLM (Ollama via NPMAI).

---

## 🚀 Features

- 📄 Extract text from searchable PDFs
- 🖨️ OCR for scanned PDFs
- 🖼️ Image OCR (Tesseract + OpenCV preprocessing)
- 🎥 Local video speech-to-text (Whisper)
- 📺 YouTube video transcription (yt-dlp + Whisper)
- 📃 Plain text processing
- 🧠 FAISS vector database creation & loading
- 🔎 Semantic similarity search
- ♻️ Iterative refinement using LLM (Ollama)
- 🗂 Automatic ingestion routing based on file type
- Supabse Storage for persistent storage.

---

## To understand repo project with AI in detail with full documentation visit here:-
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/sonuramashishnpm/NPM-Rag-API-Framework)

## Workflow:-
<img src="https://i.ibb.co/W43CqndR/NPMAI-RAG-API-Pipleline.png" alt="Example Screenshot" width="1100" style="display: block; margin: 0 auto; margin-left:20px">

## IMPORTANT UPDATE IN NPMAI RAG API PIPELINE:-

# 🚀 NPMAI Update: Advanced RAG & Refine Architecture

We have officially upgraded the **NPMAI Ecosystem** to a more intelligent, cost-efficient, and "Product-Ready" pipeline. These updates move beyond basic RAG into **High-Performance Agentic Retrieval**.

---

### 🔍 1. Dynamic K-Context Retrieval (70% Coverage)

**The Problem:** 
Standard RAG systems use a fixed `k` value (e.g., `k=4`). This is inefficient—it provides too little context for large documents (missing facts) and too much "noise" for tiny documents (wasting tokens).

**The Solution:** 
I have engineered a **Proportional Scaling Logic** that calculates the optimal number of chunks to retrieve based on the actual density of your vectorized database.

*   **Logic:** `dynamic_k = max(1, int(total_chunks * 0.70))`
*   **How it works:**
    *   **Short Documents:** If your database has only 2 chunks, the system retrieves only those 2.
    *   **Large PDFs:** If your PDF generates 100 chunks, the system automatically scales up to retrieve **70 relevant chunks** ($k=70$).
*   **The Impact:** This ensures the AI always sees a **statistically significant slice** of the knowledge base, adapting perfectly to any document size.

---

### 🔄 2. Sliding Window Batch-Refinement (3-Chunk Window)

**The Problem:** 
Traditional "Refine" strategies process one chunk at a time. This is incredibly slow because it makes $N$ separate API calls. For a 30-chunk document, the user waits too long.

**The Solution:** 
I have implemented a **Sliding Window Batch-Refine** system that processes chunks in groups of 3 instead of 1.

*   **Logic:** `for i in range(0, total_chunks, 3):`
*   **How it works:**
    *   Instead of making a single LLM call for every 1,000 characters, the system sends a **batch of 3 related chunks** (3,000 characters) in one go.
    *   It uses the previous answer as a "Running Memory" to merge new information from the current 3-chunk batch.
*   **The Impact:** 
    *   **3x Faster Execution:** We have reduced total API latency by **66%**.
    *   **Improved Coherence:** The AI sees a broader context ($3,000$ chars vs $1,000$ chars), allowing it to spot connections between facts that are split across neighboring chunks.

---

### ☁️ 3. Infrastructure: Persistent Supabase Integration (v0.1.8)

We have successfully integrated **Supabase Object Storage** to move from temporary memory to **Persistent Knowledge Bases**.

*   **Vector Persistence:** All `.faiss` and `.pkl` index files are now automatically uploaded to a secure Supabase bucket.
*   **Multi-Platform Access:** This allows **NPM-Rag-AI**, **NPM-AutoCode-AI**, and the **npmai SDK** to share and load the same vectorized data from anywhere in the world.

---
**Summary:** 
These architectural changes make **NPMAI** one of the most efficient open-source RAG frameworks available for developers who need **Speed + Accuracy** without the high cost of standard 1-by-1 refinement.

## 🏗 Architecture Overview

```
Client Request
      ↓
/ingestion Endpoint
      ↓
File Type Detection
      ↓
Text Extraction (PDF/OCR/Video/etc.)
      ↓
Optional Vector DB Retrieval (FAISS)
      ↓
Refinement via LLM
      ↓
Final Response
```

---

## 📌 API Endpoints

### Health Check
```
GET /
```
Returns:
```json
{ "ok": true }
```

---

### Main Ingestion Endpoint

```
POST /ingestion
```

### Supported Inputs:
- `file` → Upload file (pdf, txt, mp4, jpg, png, etc.)
- `query` → Optional semantic query
- `DB_PATH` → Path to vector database
- `link` → YouTube link
- `output_path` → Download location for video
- `temperature` → LLM temperature
- `model` → Ollama model name

---

## 📂 Supported File Types

| Type | Processing Method |
|------|-------------------|
| PDF (text-based) | PyMuPDF |
| PDF (scanned) | pdf2image + Tesseract |
| Image | OpenCV + Tesseract |
| TXT | Direct read |
| MP4 | Whisper transcription |
| YouTube | yt-dlp + Whisper |

---

## 🔍 Retrieval Pipeline

If `query` and `DB_PATH` are provided:

1. Check if FAISS DB exists
2. If yes → Load and perform similarity search
3. If no → Create embeddings & save DB
4. Retrieve top 4 chunks
5. Send to LLM refine loop

---

## 🧠 Vector Store

- Embeddings: `all-MiniLM-L6-v2`
- Vector DB: FAISS
- Chunk Size: 1000
- Overlap: 200

---

## 🔄 Refinement Logic

For each retrieved chunk:

1. Pass context to LLM
2. Iteratively refine previous answer
3. Return final refined response

---

## 📦 Dependencies

Install required packages:

```bash
pip install fastapi uvicorn
pip install langchain langchain-community
pip install faiss-cpu
pip install whisper
pip install moviepy
pip install pytesseract
pip install pdf2image
pip install pymupdf
pip install yt-dlp
pip install opencv-python
pip install pillow
pip install numpy
```

Make sure:

- Tesseract OCR is installed in system
- FFmpeg is installed
- Ollama is running locally

---

## ▶️ Running the Server

```bash
uvicorn main:app --reload
```

---

## 🧩 Example Usage

### Upload a PDF with Retrieval

```
POST /ingestion
Form Data:
file = document.pdf
query = "Summarize key points"
DB_PATH = vector_db
model = llama3
temperature = 0.7
```

---

## ⚠️ Notes

- GPU is disabled (`CUDA_VISIBLE_DEVICES=""`)
- Whisper model loads once (thread-safe singleton)
- FAISS uses dangerous deserialization (use trusted DB paths only)
- Temporary audio saved as `temp.wav`

---

## 🔮 Future Improvements

- Streaming responses
- Async video processing
- Chunk-level caching
- Background task queue
- Better refine logic
- Support for multiple vector stores
- Use through ##Docker

---

## 🛠 Tech Stack

- FastAPI
- FAISS
- HuggingFace Embeddings
- Whisper
- OpenCV
- Tesseract OCR
- PyMuPDF
- yt-dlp
- ##npmai

---

## 📜 License

MIT License

---

## 💡 Summary

This system acts as a **universal AI ingestion pipeline** capable of processing multi-modal data and performing intelligent semantic retrieval with LLM refinement.

It can serve as:
- AI document assistant
- Video summarizer
- Research helper
- OCR intelligence engine
- Knowledge base system

---

```
