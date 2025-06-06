# Chatbot Development Guide

Welcome! This guide walks you through **three tiers** of chatbot complexity—from zero‑code plug‑and‑play to fully custom, model‑tuned solutions. Each section explains key concepts in plain language, lists the main components you’ll need, and shows step‑by‑step how to get started.

---

## 📊 Overview Table

| Level | Capability                         | RAG? | Fine‑tuning? | Coding Required | Main Components                                        |
|:-----:|------------------------------------|:----:|:------------:|:---------------:|-------------------------------------------------------|
| **1** | **Pure Chat**<br>“Out of the box” AI | No   | No           | ❌               | • Google Vertex AI Chat widget (chat‑bison‑001)         |
| **2** | **Doc‑Grounded Chat**<br>Answers from _your_ docs | Yes  | No           | 🔧 Minimal      | • Vertex AI Document Index + Matching Engine<br>• Embed widget |
| **3** | **Full Custom AI**<br>Custom RAG + Model Tuning | Yes  | Yes          | 💻 Yes           | • Vector store (ChromaDB / Pinecone)<br>• Embeddings model<br>• LangChain orchestration<br>• Tunable LLM (OpenAI or Gemini)<br>• Backend (FastAPI / Express)<br>• Frontend chat UI (React / Streamlit) |

---

## 🛠 Level 1: Pure Chat (Zero Code)

**What it is:**  
A basic chat interface that uses Google’s PaLM 2 Bison (“chat‑bison‑001”) model to answer any question from its general training.

**Key benefits:**  
- **Instant setup**: no servers, no code  
- **Low maintenance**: Google manages everything  
- **Good for FAQs**: quick answers on common topics

**How to set up (in 3 steps):**  
1. **Sign in** to Google Cloud Console.  
2. **Enable** the **Vertex AI** and **Vertex Matching Engine** APIs.  
3. **Copy & paste** the **chat widget snippet** from Vertex AI → Chat → Embed.

> **Result:** A chat bubble appears on your support page. Users ask, Bison answers from its “own brain.”

---

## 📚 Level 2: Doc‑Grounded Chat (Minimal Code)

**What it is:**  
A chat interface that **only** answers using _your_ uploaded documents (release notes, FAQs, manuals). It uses RAG (Retrieval‑Augmented Generation) under the hood to ground answers in your text.

**Key benefits:**  
- **Accurate**: pulls real info from your docs  
- **No heavy coding**: configuration only  
- **Instant trust**: customers know answers are from your official materials

**How to set up (5 steps):**  
1. **Prepare your docs**: Export PDFs, Markdown, or plain text.  
2. In the Cloud Console, go to **Vertex AI → Document Indexes** → **Create** → Upload your files.  
3. **Enable** the **Matching Engine** to auto‑index by keywords.  
4. In **Vertex AI Chat**, select **“Use a doc index”** and point to your index.  
5. **Embed** the updated widget snippet on your site.

> **Result:** When a user asks, the system first finds the best passages in _your_ docs, then asks Bison to craft a response based solely on that content.

---

## 🚀 Level 3: Full Custom AI (Code + Tuning)

**What it is:**  
A fully flexible chatbot where you control every layer: document retrieval, embedding storage, prompt templates, model choice, and fine‑tuning.

**Key benefits:**  
- **Complete customization**: brand voice, business logic, and advanced fallback rules  
- **Fine‑tuning**: teach the model your exact style or industry terminology  
- **Scalable & portable**: swap components (vector store, LLM) without rewriting front end

**Architecture Layers & Steps:**

1. **Data Ingestion & Chunking**  
   - _Tools:_ LangChain DocumentLoaders (Python), custom scripts  
   - _Goal:_ Split your docs into bite‑sized “chunks” (~500 words each)

2. **Embeddings & Vector Store**  
   - _Tools:_ ChromaDB (self‑hosted) or Pinecone (managed)  
   - _Goal:_ Convert each chunk into a numeric embedding and store it for fast similarity search

3. **LLM Orchestration**  
   - _Tools:_ LangChain chains or a custom orchestration layer  
   - _Flow per query:_  
     1. Retrieve top K relevant chunks from the vector store  
     2. Fill a prompt template that includes those chunks + the user’s question  
     3. Call your chosen LLM (e.g., OpenAI GPT‑3.5, GPT‑4, or Vertex Gemini)

4. **Fine‑tuning / Training**  
   - _Options:_  
     - **OpenAI Fine‑Tune** on GPT‑3.5 (upload Q&A pairs)  
     - **Vertex AI Fine‑Tune** on Gemini models  
   - _Goal:_ Adjust the model’s weights so it natively answers in your style

5. **Backend Service**  
   - _Tools:_ FastAPI (Python) or Express.js (Node)  
   - _Responsibilities:_ Session management, routing, confidence‑based fallback to humans

6. **Frontend Chat UI**  
   - _Tools:_ React chat widget (e.g. `react-chat-widget`), Streamlit for rapid prototyping  
   - _Features:_  
     - Real‑time messaging  
     - “Talk to a human” button on low‑confidence replies  
     - Branding, theming, and analytics hooks

**Example Tech Stack:**  
```text
Docs → LangChain → ChromaDB
User Query → FastAPI → LangChain Retrieval → OpenAI GPT‑3.5 → FastAPI → React Widget
```

# A Beginner’s Guide to AI Chatbots and RAG

If you’ve never heard of terms like “LLM,” “RAG,” or “embeddings,” don’t worry! This guide explains the key ideas in plain English.

---

## 1. What Is a Language Model (LLM)?

- **Language Model** = “AI that writes.”  
  - It’s a computer program trained on tons of text (books, websites, articles).  
  - You give it a prompt (some words), and it continues writing in a natural way.

- **Examples**  
  - **GPT‑3.5 / GPT‑4** (by OpenAI)  
  - **PaLM 2 / Gemini** (by Google)  
  - **Llama, Falcon** (open‑source)

- **Why it matters**  
  - You can ask questions, write emails, generate code, or draft blog posts—just by typing what you need.

---

## 2. The Problem: “AI Hallucinations”

- LLMs are powerful BUT sometimes make up facts or give outdated info.  
- If you ask “What’s new in Version 2.3 of my app?” it might guess—because it wasn’t trained on *your* release notes.

---

## 3. Enter RAG: Retrieval‑Augmented Generation

**RAG** = **R**etrieval + **A**ugmented **G**eneration. It “grounds” AI answers in your own documents.

1. **Retrieval**  
   - The system “searches” your documents (release notes, manuals) to find the most relevant passages.

2. **Augmented Generation**  
   - It then feeds those passages to the language model, asking:  
     > “Based only on this text, answer the user’s question.”

**Result:** Answers come directly from *your* docs, not the AI’s own “memory.”

---

## 4. Key Building Blocks

| Term                | What It Means                                                  |
|---------------------|----------------------------------------------------------------|
| **Embedding**       | A numeric “fingerprint” of a text snippet.                     |
| **Vector Store**    | A database that holds embeddings for fast similarity search.   |
| **Prompt**          | The text you send to the model, including instructions & context. |
| **Prompt Engineering** | Crafting prompts to get better, more accurate answers.       |
| **Fine‑Tuning**     | Training the model further on your own examples (Q&A pairs).   |

---

## 5. How RAG Works, Step by Step

1. **Prepare your docs**  
   - Convert PDFs, Word files, or Markdown into plain text.

2. **Create embeddings**  
   - Break text into small chunks (e.g., paragraphs).  
   - Run each chunk through an embedding model → store in vector database.

3. **User asks a question**  
   - System turns the question into an embedding.  
   - Finds the top K most similar document chunks in the vector store.

4. **Generate the answer**  
   - Builds a prompt:  
     ```
     “Here are 3 passages from our docs:
       [Passage A]
       [Passage B]
       [Passage C]
     Please answer: [User’s question]”
     ```
   - Sends prompt to the LLM → returns a grounded answer.

5. **Optional fallback**  
   - If the model is uncertain, show “Let me get a human to help” and route to support.

---

## 6. When to Use RAG vs. Pure Chat

- **Pure Chat** (no docs)  
  - Fast to set up (zero code), but may hallucinate.  
  - Good for general chit‑chat or creative writing.

- **RAG Chat** (with your docs)  
  - A bit more setup, but *answers are always backed by your info.*  
  - Essential for support bots, product docs, or any specialized knowledge.

---

## 7. Next Steps

1. **Try a no‑code RAG tool** (Botpress Cloud, Tidio, Vertex AI Document Index).  
2. **Learn the basics of embeddings & vector stores** (ChromaDB, Pinecone).  
3. **Experiment with prompts** to see how context shapes AI answers.

With these building blocks in place, you’ll have a chatbot that’s both **smart** and **reliable**, pulling answers straight from *your* documentation. Happy building!  

## Monthly Cost Comparison: Pure Chat vs RAG Chat (ChromaDB Self‑Hosted)

**Assumptions**  
- **Model:** Google PaLM 2 Text Bison (`text‑bison‑001`)  
- **Pricing:** \$0.00025 per 1 000 input tokens, \$0.0005 per 1 000 output tokens :contentReference[oaicite:0]{index=0}  
- **Per‑query usage:**  
  - **Pure Chat:** 100 input tokens + 400 output tokens  
  - **RAG Chat:** 100 input tokens + 600 doc‑context tokens + 400 output tokens  
- **Volume:** 5 000 queries per month  
- **ChromaDB:** self‑hosted on a small VPS (~\$5/month)

| Component             | Pure Chat                   | RAG Chat                         |
|-----------------------|-----------------------------|----------------------------------|
| **Tokens per Query**  | 100 in / 400 out            | 700 in / 400 out                 |
| **Cost per Query**    | (0.1×\$0.00025)+(0.4×\$0.0005) = **\$0.000225** | (0.7×\$0.00025)+(0.4×\$0.0005) = **\$0.000375** |
| **Monthly Token Cost**| 5 000×\$0.000225 = **\$1.13** | 5 000×\$0.000375 = **\$1.88**    |
| **ChromaDB VPS**      | —                           | **\$5.00**                       |
| **Total Monthly Cost**| **\$1.13**                  | **\$6.88**                       |

> \*You can run ChromaDB on a basic DigitalOcean or AWS Lightsail droplet (~1 vCPU, 1 GB RAM) for around \$5/month.

---

### Key Takeaways

- **Pure Chat** (no docs) costs only **\$1.13/month** for 5 000 queries.  
- **RAG Chat** (grounded in *your* docs) is still **very economical** at **\$6.88/month**, including hosting your vector store.  

For just an extra \$5.75/month, you gain **document‑backed accuracy** and reduce AI “hallucinations.”

## Full RAG Chatbot Stack Comparison

Below is a side‑by‑side comparison of leading RAG setups. Each row shows the base model’s benchmark accuracy (MMLU), inference & embedding costs, fine‑tuning support, ease of updating your document index, and estimated monthly cost for **5 000 queries at 500 tokens/query**.

| Stack                                        | Base Model (MMLU)                         | RAG? | Fine‑Tune?               | Inference Cost (IN/OUT per 1 k tokens)           | Embedding Cost per 1 k | Add Docs Workflow                   | Est. Monthly Cost |
|----------------------------------------------|-------------------------------------------|:----:|:-------------------------|:-----------------------------------------------|:----------------------|--------------------------------------|-------------------|
| **LangChain + OpenAI GPT‑3.5 Turbo + ChromaDB** | GPT‑3.5 Turbo – 70.0 %     | ✅    | ✅ (OpenAI Fine‑Tune)       | \$0.0005 / \$0.0015        | \$0.0001| Re‑run loader & upsert into ChromaDB | **\$5.00**       |
| **LangChain + OpenAI GPT‑4 Turbo + ChromaDB**   | GPT‑4 Turbo – 86.4 %     | ✅    | ❌ (not supported yet)      | \$0.0100 / \$0.0300      | \$0.0001  | Re‑run loader & upsert into ChromaDB | **\$100.00**     |
| **Vertex AI Doc Index + PaLM 2 Bison**            | PaLM 2 Bison – 81.2 %    | ✅    | ❌                        | \$0.0005 / \$0.0005        | Managed (no extra)     | Upload & re‑index via Console        | **\$2.50**       |
| **AWS Bedrock (Titan Text) + OpenSearch**        | Titan Text Premier – 70.5 %  | ✅    | ✅ (Preview via SageMaker)  | \$0.0005 / \$0.0015      | Managed (no extra)     | Push docs → OpenSearch auto-sync     | **\$5.00**       |
| **Azure CogSearch + Azure OpenAI**               | GPT‑3.5 Turbo – 70.0 %    | ✅    | ✅ (Azure Fine‑Tune)        | \$0.0015 / \$0.0020      | Managed (no extra)     | Update Search index via Portal       | **\$7.50**       |

\* PaLM 2 Bison can’t be weight‑level fine‑tuned; for model‑level training use Vertex AI’s Gemini fine‑tune pipeline.

---

### Notes

- **Monthly Cost** is calculated as:  
  \[(500 tokens ÷ 1 000) × (IN rate + OUT rate)\] × 5 000 queries  
- **Embedding Cost** (self‑hosted stacks) is a one‑time ingestion fee; adding new docs later only incurs the embedding API charge.  
- **Docs Workflow**: All solutions let you add or update documents and have them immediately serve future queries—either by re‑indexing (managed) or re‑ingesting/upserting (self‑hosted).

---

### Which Stack to Choose?

- **Lowest Cost with RAG:**  
  **Vertex AI Doc Index + PaLM 2 Bison** at **\$2.50/mo** for 5 000 queries.  
- **Full Fine‑Tune Support:**  
  **LangChain + OpenAI GPT‑3.5 Turbo** (\$5 /mo) or **AWS Bedrock (Titan Text)** (\$5 /mo).  
- **Highest Accuracy:**  
  **GPT‑4 Turbo** (\$100 /mo), with no fine‑tuning yet but top‑tier responses.

By choosing any of these RAG pipelines, you can ground answers in **your** documents—and whenever you spot a missing or incorrect response, simply update your docs (or fine‑tune) so the next query is accurate.  
