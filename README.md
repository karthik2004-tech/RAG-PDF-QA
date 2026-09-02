# 📄 PDF Q&A Assistant — A Retrieval-Augmented Generation (RAG) Pipeline

> Ask questions about your PDF documents and get accurate, source-grounded answers — built from scratch to understand how modern AI search assistants (like ChatGPT-with-documents) actually work under the hood.

---

## 🧠 What does this project actually do? (In plain English)

Imagine you have a 30-page PDF and you don't want to read the whole thing to find one answer. Instead, you just **ask a question in plain English**, and the system:

1. **Reads and understands** your PDF documents.
2. **Finds the exact paragraphs** most relevant to your question — like a super-smart "Ctrl+F".
3. **Hands those paragraphs to an AI model**, which reads them and writes a clear, direct answer — instead of you having to search and read manually.

This is the same core idea behind tools like ChatGPT's "chat with your files" feature, Notion AI, and enterprise document-search assistants — except this version is built end-to-end, piece by piece, so every step is transparent and customizable.

**In short:** *Upload documents → Ask a question → Get an accurate answer, backed by the actual content of your files.*

---

## 🎯 Why this project matters

Large Language Models (LLMs) like ChatGPT are powerful, but they don't automatically know what's inside *your* private PDFs — and they can "hallucinate" (confidently make things up) when they don't know an answer.

This project solves that using a technique called **Retrieval-Augmented Generation (RAG)**: instead of asking the AI to answer from memory, we first **retrieve** the exact relevant text from the document, and only then ask the AI to **generate** an answer using that real, grounded context. This is one of the most in-demand skills in AI/ML and Data Science roles today.

---

## ⚙️ How it works (Technical Overview)

```
                ┌─────────────┐
                │  PDF Files  │
                └──────┬──────┘
                       │  (1) Load & Extract Text
                       ▼
              ┌──────────────────┐
              │  Document Loader │  (PyPDFLoader)
              └────────┬─────────┘
                       │  (2) Split into small overlapping chunks
                       ▼
              ┌──────────────────┐
              │   Text Chunker    │  (RecursiveCharacterTextSplitter)
              └────────┬─────────┘
                       │  (3) Convert text → numeric vectors (embeddings)
                       ▼
              ┌──────────────────┐
              │ Embedding Model   │  (SentenceTransformer: all-MiniLM-L6-v2)
              └────────┬─────────┘
                       │  (4) Store vectors for fast semantic search
                       ▼
              ┌──────────────────┐
              │   Vector Store    │  (ChromaDB)
              └────────┬─────────┘
                       │  (5) User asks a question
                       ▼
              ┌──────────────────┐
              │     Retriever     │  (Cosine similarity search)
              └────────┬─────────┘
                       │  (6) Top relevant chunks + question → LLM
                       ▼
              ┌──────────────────┐
              │   Groq LLM API    │  (openai/gpt-oss-120b)
              └────────┬─────────┘
                       │
                       ▼
              ✅ Final, grounded answer
```

### Pipeline stages

| Stage | What happens | Tools/Libraries used |
|---|---|---|
| **1. Ingestion** | Reads all PDF files from a folder | `PyPDFLoader` (LangChain) |
| **2. Chunking** | Splits long documents into small, overlapping text chunks so context isn't lost | `RecursiveCharacterTextSplitter` |
| **3. Embedding** | Converts each text chunk into a 384-dimensional numeric vector that captures its *meaning* | `sentence-transformers` (`all-MiniLM-L6-v2`) |
| **4. Vector Storage** | Stores all chunk vectors in a searchable, persistent database | `ChromaDB` |
| **5. Retrieval** | Converts the user's question into a vector too, then finds the most semantically similar chunks using cosine similarity | Custom `RAGRetriever` class |
| **6. Generation** | Sends the retrieved chunks + question to a fast, hosted LLM to generate the final answer | `Groq API` (`langchain-groq`) |

---

## 🛠️ Tech Stack

- **Language:** Python 3.13
- **Orchestration:** LangChain, LangChain-Community, LangChain-Groq
- **PDF Parsing:** PyPDF / PyMuPDF
- **Embeddings:** Sentence-Transformers (`all-MiniLM-L6-v2`)
- **Vector Database:** ChromaDB (persistent, local)
- **LLM Inference:** Groq API (`openai/gpt-oss-120b`) — chosen for extremely low-latency inference
- **Similarity Search:** scikit-learn (cosine similarity)

---



## 🚀 How to Run It Yourself

```bash
# 1. Clone the repo
git clone https://github.com/<your-username>/rag-pdf-qa.git
cd rag-pdf-qa

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your Groq API key
cp .env.example .env
# then open .env and paste your own key (get one free at console.groq.com)

# 4. Add your PDFs to the data/ folder

# 5. Open and run the notebook
jupyter notebook RAG_pipeline.ipynb
```

---

## 💬 Example

**Question:** *"What is the difference between DISTINCT() and ROW_NUMBER()?"*

**Answer (generated by the pipeline, grounded in the actual PDF content):**
> DISTINCT() removes duplicate rows from a result set entirely, while ROW_NUMBER() assigns a unique sequential number to each row — allowing you to identify or filter duplicates without removing the underlying rows...

*(Add your own real example + a screenshot here — recruiters love seeing actual output.)*

---

## 📈 What I Learned Building This

- How embeddings turn unstructured text into a format machines can compare mathematically.
- Why chunk size and overlap matter for retrieval quality.
- How to design and debug a multi-stage AI pipeline (ingestion → chunking → embedding → storage → retrieval → generation).
- Practical experience with vector databases (ChromaDB) and fast LLM inference APIs (Groq).
- Debugging real production-style issues: silent failures from bad indentation, misleading `for-else` logic, and handling deprecated model IDs gracefully.

---

## 🔮 Future Improvements

- [ ] Wrap this into a simple web app (Streamlit/Gradio) for a live interactive demo
- [ ] Add source citations (page numbers) alongside each generated answer
- [ ] Support multi-turn conversational follow-up questions
- [ ] Add automated evaluation of answer quality (RAGAS or similar)

---

## 👤 Author

**Karthik (Telukutla Karthik)**
B.Tech in AI & Data Science | Aspiring Data Analyst / ML Engineer / AI Engineer

📍 Hyderabad, India
🔗 [LinkedIn](#) · [Portfolio](#) · [Email](#)

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
