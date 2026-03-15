# 🤖 WhatsApp RAG AI Assistant (Zero-Hallucination Architecture)

An enterprise-grade, fully automated WhatsApp Customer Service & Sales Agent built with n8n, Pinecone, and Google Gemini. 

This system ingests company documents directly from Google Drive, stores them in a vector database, and uses Retrieval-Augmented Generation (RAG) to answer customer queries on WhatsApp 24/7—without ever hallucinating or making up policies.

## 🔴 The Pain (The Problem)
Small businesses and e-commerce stores face a massive customer service bottleneck:
1. **Humans sleep:** Customers message at 2 AM wanting to buy a laptop or asking about return policies, and get ignored.
2. **Standard AI is dangerous:** If you connect a standard ChatGPT bot to your business WhatsApp, it will happily invent fake 15% restocking fees or recommend a competitor's products. 
3. **Training is slow:** Updating a bot's knowledge usually requires a developer to rewrite code.

## 🟢 The Solution
I engineered a two-part automated system that acts as a completely autonomous digital employee:
* **The Feeder:** A business owner simply drops a Word Document (e.g., `Refund_Policy.docx`) into a Google Drive folder. The system automatically reads it, translates it into embeddings, and updates the AI's "brain."
* **The Receptionist:** A WhatsApp AI Agent that receives customer texts, checks the company database, and replies instantly. 

**The core feature? The "Ironclad Padlock."** Through strict prompt engineering and tool boundaries, the AI is explicitly forbidden from using outside internet knowledge. If the answer isn't in the company handbook, it safely hands the chat over to a human.

## 💡 Why You Should Care (The Value Prop)
* **Zero Hallucinations:** Protects brand reputation by only serving verified company facts.
* **Instant Scalability:** Handles hundreds of customer inquiries simultaneously without API rate-limit crashing.
* **No-Code Maintenance for Clients:** Business owners update the bot simply by typing in a Google Doc. 
* **Sales & Support in One:** Seamlessly pivots between pitching current inventory and answering technical warranty questions.

## 🛠️ The Tech Stack
* **Orchestration:** [n8n](https://n8n.io/) (Self-hosted/Cloud)
* **Trigger/Output:** Meta WhatsApp Cloud API
* **Vector Database:** [Pinecone](https://www.pinecone.io/) (Serverless)
* **LLM & Embeddings:** Google Gemini (`gemini-1.5-flash` & `text-embedding-004`)
* **Data Source:** Google Drive API

## 🧠 System Architecture

### Workflow 1: The Automated Knowledge Engine
`Google Drive Trigger` ➡️ `Read Document` ➡️ `Text Splitter` ➡️ `Gemini Embeddings (3072 dimensions)` ➡️ `Pinecone Vector Store`

### Workflow 2: The WhatsApp AI Agent
`WhatsApp Trigger` ➡️ `AI Agent (with Simple Memory)` ➡️ `Company_Knowledge_Base Tool (Pinecone RAG)` ➡️ `WhatsApp Output`

## 🚀 Key Engineering Challenges Overcome
During development, I solved several complex system integration issues:
1. **Dimension Mismatches:** Upgraded vector search to Google's `text-embedding-004` model to support high-fidelity 3072-dimension mathematical translations.
2. **Namespace Isolation:** Engineered specific Pinecone namespaces (e.g., `Electronics`) so the Agent doesn't search an empty void, optimizing retrieval speed and accuracy.
3. **The "People-Pleaser" Bypass:** Implemented hard-stop boundary prompts to prevent the AI from executing jailbreaks, writing poetry, or acknowledging competitors.
4. **Rate Limit Handling:** Managed API webhook traffic to respect Google's execution tier limits during rapid background Tool-calling loops.

## 🤝 Let's Connect
I'm a digital entrepreneur building automation tools for content creators and small businesses. If you want to see how AI automation can scale your operations, let's talk!


graph TD
    subgraph Workflow 1: The Knowledge Engine (The Feeder)
        Drive[Google Drive\nRefund_Policy.docx] --> Splitter[n8n Text Splitter]
        Splitter --> Embed[Gemini Embeddings\ntext-embedding-004]
        Embed --> Pinecone[(Pinecone Vector DB\n'Electronics' Namespace)]
    end

    subgraph Workflow 2: The WhatsApp AI Agent (The Receptionist)
        Customer[Customer on WhatsApp] <-->|Messages| WhatsApp[WhatsApp Cloud API]
        WhatsApp <--> Agent{AI Agent\nGemini 1.5 Flash}
        Agent -->|Checks Policies| Tool[Company_Knowledge_Base Tool]
        Tool -.->|Searches| Pinecone
        Pinecone -.->|Returns Exact Facts| Tool
        Tool -->|Sends Data| Agent
    end
