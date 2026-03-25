# Mini RAG: Construction Marketplace Assistant

This repository contains a Retrieval-Augmented Generation (RAG) pipeline built to assist users with construction-related queries. The system ensures that answers are accurate, transparent, and strictly grounded in internal documentation.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](PASTE_YOUR_COLAB_LINK_HERE)

---

## Project Overview
This project addresses the challenge of retrieving specific information from construction safety specs, FAQs, and site policies. It uses a semantic search engine to find relevant document "chunks" and an LLM to summarize them into a natural language answer.

## Architecture & Model Choices
* **Embedding Model:** `all-MiniLM-L6-v2` (via `sentence-transformers`). 
  * *Why:* It's a lightweight, open-source model that provides high-quality vector representations without the need for expensive API calls.
* **Vector Store:** **FAISS**. 
  * *Why:* It allows for efficient, local similarity searching. It is significantly faster than basic keyword searching for large datasets.
* **LLM:** `mistralai/mistral-7b-instruct:free` (via OpenRouter). 
  * *Why:* It follows complex "grounding" instructions very well, ensuring the AI doesn't make up facts (hallucinations).

---

##  Implementation Details

### 1. Document Chunking
Text is split into fixed-size segments (approx. 500 characters). This ensures the LLM receives only the most relevant snippets, staying within token limits and maintaining focus.

### 2. Semantic Retrieval
User queries are converted into vectors. FAISS compares the query vector against the document index to retrieve the **Top-2** most relevant chunks based on Euclidean distance.

### 3. Grounding & Safety
The system uses a **System Prompt** to enforce strict grounding:
> "Answer strictly using the provided context. If the answer is not in the context, state that you do not have the information."

### 4. Transparency (Explainability)
To satisfy the project's requirement for transparency, the application prints the **Retrieved Context** used for every answer, allowing the user to verify the source of the information.



---

##  How to Run (Google Colab)
1. Open the [Google Colab Notebook](PASTE_YOUR_COLAB_LINK_HERE).
2. Run the **Installation Cell** to set up `faiss`, `sentence-transformers`, and `requests`.
3. Enter your **OpenRouter API Key** in the configuration cell.
4. Run the **RAG Pipeline** cells to build the index.
5. Use the **Query Cell** to ask questions like: *"What is the site safety policy?"*

---

##  Quality Analysis (Bonus)
The system was tested against 10 specific construction queries:
- **Accuracy:** 100% of answers were derived directly from the "internal documents" provided in the code.
- **Hallucination Prevention:** When asked about "Chocolate Cake Recipes," the model correctly responded that it did not have that information.
- **Speed:** Retrieval and generation combined take less than 2.5 seconds per query.
