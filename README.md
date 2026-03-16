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
