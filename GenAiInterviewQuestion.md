# Generative AI Interview Questions

## 1. What is Generative AI?

Generative AI is a type of AI that can generate new content such as:

* Text
* Images
* Code
* Audio
* Video

It uses Large Language Models (LLMs) trained on huge datasets.

Examples:

* OpenAI ChatGPT
* Google Gemini
* Microsoft Copilot

---

# LLM Questions

## 2. What is an LLM?

LLM = Large Language Model.

It is a deep learning model trained on massive text datasets to understand and generate human language.

Examples:

* GPT-4
* Llama
* Claude
* Gemini

---

## 3. What is the difference between AI, ML, Deep Learning, and Generative AI?

| Term          | Meaning                   |
| ------------- | ------------------------- |
| AI            | Mimics human intelligence |
| ML            | Learns from data          |
| Deep Learning | Uses neural networks      |
| Generative AI | Creates new content       |

---

## 4. What is Prompt Engineering?

Prompt engineering means designing effective prompts to get accurate AI responses.

Example:

```txt
Bad Prompt:
Explain SQL

Good Prompt:
Explain SQL joins with real-time examples for beginners
```

---

## 5. What is a Token in Generative AI?

Tokens are small pieces of text processed by LLMs.

Example:

```txt
"Hello Aman"
```

May become:

```txt
["Hello", "Aman"]
```

Cost and limits are usually based on tokens.

---

# RAG Questions

## 6. What is RAG (Retrieval-Augmented Generation)?

RAG combines:

* Information retrieval
* LLM generation

Flow:

```txt
User Question
   ↓
Search Documents
   ↓
Send Context to LLM
   ↓
Generate Accurate Answer
```

Used in:

* Enterprise chatbots
* Document search
* Medical systems
* Knowledge bases

---

## 7. Why use RAG instead of training a model?

| Fine-Tuning         | RAG              |
| ------------------- | ---------------- |
| Expensive           | Cheaper          |
| Requires retraining | Uses latest data |
| Slow updates        | Fast updates     |

---

## 8. What is Vector Database?

Stores embeddings for semantic search.

Popular vector DBs:

* Pinecone
* ChromaDB
* FAISS
* Azure AI Search

---

## 9. What are Embeddings?

Embeddings are numerical vector representations of text.

Similar meaning → similar vectors.

Used for:

* Semantic search
* Recommendation systems
* RAG

---

# OpenAI / Azure AI Questions

## 10. How do you integrate OpenAI with .NET?

Using:

* REST API
* OpenAI SDK
* Azure OpenAI

Typical flow:

```txt
React UI
   ↓
.NET API
   ↓
Azure OpenAI
   ↓
AI Response
```

---

## 11. What is Azure OpenAI Service?

Microsoft Azure OpenAI provides OpenAI models hosted securely on Azure cloud.

Benefits:

* Enterprise security
* Private networking
* RBAC
* Compliance

---

## 12. Difference between OpenAI and Azure OpenAI?

| OpenAI             | Azure OpenAI                  |
| ------------------ | ----------------------------- |
| Direct OpenAI APIs | Hosted on Azure               |
| Public endpoints   | Enterprise secure             |
| Simpler setup      | Better enterprise integration |

---

# Architecture Questions

## 13. Explain AI chatbot architecture.

Architecture:

```txt
Frontend (React/Angular)
      ↓
.NET Web API
      ↓
Authentication
      ↓
AI Service (Azure OpenAI)
      ↓
Vector DB / SQL / Cosmos DB
```

---

## 14. How do you secure AI APIs?

Methods:

* JWT authentication
* API Management
* Rate limiting
* Managed Identity
* Key Vault

---

## 15. What is hallucination in AI?

Hallucination means AI generates incorrect or fake information confidently.

Solution:

* Use RAG
* Add validation
* Use grounded data
* Human review

---

# Practical Enterprise Questions

## 16. How is Generative AI used in enterprise applications?

Examples:

* Resume screening
* Medical assistant
* Code generation
* Ticket summarization
* Email drafting
* Document Q&A

---

## 17. Explain MCP Server.

MCP = Model Context Protocol.

It allows AI models to interact with:

* APIs
* Databases
* Tools
* External systems

Useful for AI agents.

Since you are already working on MCP server integration, this is important for interviews.

---

## 18. What are AI Agents?

AI agents can:

* Think
* Decide
* Use tools
* Execute actions

Example:

```txt
User asks:
"Book appointment and send email"

Agent:
1. Checks calendar
2. Books slot
3. Sends email
```

---

# Coding Questions

## 19. How do you call OpenAI API in .NET?

Example:

```csharp
var client = new HttpClient();

client.DefaultRequestHeaders.Authorization =
    new AuthenticationHeaderValue("Bearer", apiKey);

var response = await client.PostAsJsonAsync(
    "https://api.openai.com/v1/chat/completions",
    requestBody);
```

---

## 20. What is Semantic Kernel?

Microsoft Semantic Kernel is an SDK for building AI orchestration applications in .NET.

Features:

* Prompt management
* Memory
* AI plugins
* Function calling

---

# Scenario-Based Questions

## 21. How would you build a medical chatbot?

Possible answer:

* React frontend
* .NET Core API
* Azure OpenAI
* Vector DB
* Medical knowledge PDFs
* RAG implementation
* JWT authentication
* Audit logging

---

## 22. How would you reduce AI cost?

Methods:

* Token optimization
* Caching
* Smaller models
* Chunking
* Prompt optimization

---

## 23. What is Fine-Tuning?

Training a pre-trained model on custom data for specific tasks.

Used when:

* Domain-specific responses needed
* Consistent output format required

---

# Important AI Terms

| Term          | Meaning                      |
| ------------- | ---------------------------- |
| LLM           | Large Language Model         |
| Prompt        | Input to AI                  |
| Token         | Small text unit              |
| Embedding     | Vector representation        |
| RAG           | Retrieval + Generation       |
| Fine-tuning   | Custom model training        |
| Hallucination | Fake AI response             |
| Agent         | AI system performing actions |

---

# Interview Tips for You

Since you are a Senior .NET Developer:

* Focus on AI integration architecture
* Learn Azure OpenAI
* Learn RAG implementation
* Understand Vector DB
* Learn MCP and AI Agents
* Prepare one real project explanation

Good project idea for interview:

```txt
AI-Powered Medicine Management System
```

Features:

* Medicine search using AI
* Expiry prediction
* OCR bill scanning
* AI chatbot
* Medicine recommendation
* Stock forecasting

That project can strongly improve your CV.
