# 🦜🔗 LangChain — Complete Course Notes

> **Framework:** LangChain (Open Source) | **Updated:** January–April 2025
>
> A comprehensive guide covering everything from LangChain basics to building production-grade RAG systems and AI Agents.

---

## 📋 Table of Contents

1. [Introduction to LangChain](#1-introduction-to-langchain)
2. [LangChain Components Overview](#2-langchain-components-overview)
3. [Models](#3-models)
4. [Prompts](#4-prompts)
5. [Structured Output](#5-structured-output)
6. [Output Parsers](#6-output-parsers)
7. [Chains](#7-chains)
8. [Runnables](#8-runnables)
9. [LCEL (LangChain Expression Language)](#9-lcel-langchain-expression-language)
10. [Document Loaders](#10-document-loaders)
11. [Text Splitters](#11-text-splitters)
12. [Vector Stores](#12-vector-stores)
13. [Retrievers](#13-retrievers)
14. [RAG (Retrieval-Augmented Generation)](#14-rag-retrieval-augmented-generation)
15. [RAG using LangChain](#15-rag-using-langchain)
16. [Tools](#16-tools)
17. [Tool Calling](#17-tool-calling)
18. [Agents](#18-agents)

---

## 1. Introduction to LangChain

### What is LangChain?

LangChain is an **open-source framework** for developing applications powered by large language models (LLMs). It provides modular components and end-to-end tools that help developers build complex AI applications, including:

- 🤖 Chatbots
- ❓ Question-answering systems
- 📚 Retrieval-Augmented Generation (RAG)
- 🕵️ Autonomous agents
- And much more

### Why LangChain?

| Feature | Description |
|---|---|
| ✅ LLM Support | Supports all major LLMs (OpenAI, Anthropic, Google, HuggingFace, etc.) |
| ✅ Simplicity | Simplifies developing LLM-based applications |
| ✅ Integrations | Integrations available for all major tools and data sources |
| ✅ Open Source | Free, actively developed, large community |
| ✅ GenAI Use Cases | Supports all major GenAI use cases out of the box |

### Key Benefits

- **Concept of Chains** — Compose multiple steps into a single pipeline
- **Model Agnostic Development** — Switch between LLMs with minimal code changes
- **Complete Ecosystem** — One framework for the entire LLM application stack
- **Memory & State Handling** — Built-in support for conversation history and context

### What Can You Build?

- 💬 **Conversational Chatbots** — Context-aware multi-turn conversations
- 🧠 **AI Knowledge Assistants** — Q&A over your own documents
- 🤖 **AI Agents** — Systems that autonomously plan and act
- ⚙️ **Workflow Automation** — Chain AI steps to automate complex tasks
- 📝 **Summarization / Research Helpers** — Condense and extract knowledge from large corpora

### Alternatives to LangChain

- **LlamaIndex** — Focuses primarily on data indexing and retrieval
- **Haystack** — Alternative pipeline-based framework for NLP tasks

---

## 2. LangChain Components Overview

LangChain is composed of five core building blocks:

```
┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   Models    │  │   Prompts   │  │   Chains    │  │   Indexes   │  │   Memory    │
│             │  │             │  │             │  │             │  │             │
│  LLMs       │  │  Templates  │  │  Sequences  │  │  Loaders    │  │  Buffer     │
│  Chat       │  │  Few-shot   │  │  Parallel   │  │  Splitters  │  │  Window     │
│  Embeddings │  │  Role-based │  │  Conditional│  │  Embeddings │  │  Summarizer │
└─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘
```

| Component | Role |
|---|---|
| **Models** | Core interfaces to interact with AI (LLMs, Chat models, Embeddings) |
| **Prompts** | Dynamic, reusable templates for constructing model inputs |
| **Chains** | Sequences of components/steps linked together |
| **Indexes** | Connect your app to external knowledge (PDFs, websites, databases) |
| **Memory** | Maintain conversation state across LLM API calls |
| **Agents** | Autonomous systems that decide which tools/actions to use |

---

## 3. Models

### What are Models?

The **Model Component** in LangChain is a crucial part of the framework that abstracts the complexity of working directly with different LLMs, chat models, and embedding models. It provides a **uniform interface** to communicate with them, making it easier to build applications that rely on:

- AI-generated text
- Text embeddings for similarity search
- Retrieval-Augmented Generation (RAG)

### Language Models

Language Models are AI systems designed to process, generate, and understand natural language text. LangChain supports two types:

#### 1. LLMs (Legacy)
- **Input:** Plain string (raw text)
- **Output:** Plain string
- General-purpose models for raw text generation
- Traditionally older models — less commonly used today

#### 2. Chat Models (Modern — Preferred)
- **Input:** Sequence of messages (with roles: system, human, assistant)
- **Output:** Chat message
- Specialized for conversational tasks
- The standard in modern LLM application development

### Supported Providers

LangChain supports all major model providers:

| Provider | Examples |
|---|---|
| **OpenAI** | GPT-4, GPT-4o, GPT-3.5-turbo |
| **Anthropic** | Claude 3.5 Sonnet, Claude 3 Opus |
| **Google** | Gemini Pro, Gemini Flash |
| **HuggingFace** | Thousands of open-source models |

### Open Source Models

Open-source language models are freely available AI models that can be **downloaded, modified, fine-tuned, and deployed** without restrictions from a central provider. Unlike closed-source models (OpenAI GPT-4, Anthropic Claude, Google Gemini), open-source models give you full control and customization.

**Where to find them:** [HuggingFace](https://huggingface.co) — the largest repository of open-source LLMs.

**Disadvantages of open-source models:**
- Require significant compute resources to run locally
- May require fine-tuning for specific tasks
- Performance may lag behind frontier closed-source models

### Embedding Models

Embedding models convert text into **dense numerical vectors** that capture semantic meaning. These vectors are used for:
- Similarity search
- Semantic retrieval in RAG pipelines
- Clustering and classification

---

## 4. Prompts

### What are Prompts?

Prompts are the **input instructions or queries** given to a model to guide its output. They act as the communication layer between the developer and the LLM.

### Static vs Dynamic Prompts

| Type | Description |
|---|---|
| **Static Prompts** | Hardcoded, fixed strings passed directly to the model |
| **Dynamic Prompts** | Templated prompts with variables filled at runtime |

Dynamic prompts are far more powerful — they allow you to build reusable, flexible, and parameterized instructions.

### Prompt Template

A `PromptTemplate` in LangChain is a structured way to create prompts dynamically by inserting variables into a predefined template. Instead of hardcoding prompts, `PromptTemplate` allows you to define **placeholders** that can be filled at runtime with different inputs.

**Why use `PromptTemplate` over Python f-strings?**

1. **Default Validation** — Automatically validates that all required variables are provided
2. **Reusable** — Define once, use everywhere across your pipeline
3. **LangChain Ecosystem** — Natively compatible with chains, agents, and LCEL

### Types of Prompts

#### 1. Dynamic & Reusable Prompts
Parameterized templates that accept variables at runtime.

```python
from langchain.prompts import PromptTemplate

template = PromptTemplate(
    input_variables=["topic"],
    template="Explain {topic} in simple terms."
)
```

#### 2. Role-Based Prompts
Structured conversations using system, human, and AI message roles.

```python
from langchain.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("human", "{question}")
])
```

#### 3. Few-Shot Prompting
Provide examples in the prompt to guide the model's output format or reasoning.

### Messages

LangChain uses typed message classes to represent conversations:

| Message Type | Role |
|---|---|
| `SystemMessage` | Sets the AI's behavior and persona |
| `HumanMessage` | User's input |
| `AIMessage` | Model's response |
| `FunctionMessage` | Result from a tool/function call |

### Chat Prompt Templates

`ChatPromptTemplate` is used to construct message-based prompts for chat models. Unlike `PromptTemplate`, it works with a **list of messages** rather than a single string.

### Message Placeholder

`MessagesPlaceholder` is a special placeholder used inside a `ChatPromptTemplate` to **dynamically insert chat history** or a list of messages at runtime. This is essential for building conversational applications that maintain context across turns.

---

## 5. Structured Output

### What is Structured Output?

In LangChain, **structured output** refers to the practice of having language models return responses in a **well-defined data format** (e.g., JSON), rather than free-form text. This makes the model output easier to parse and work with programmatically.

### Why Do We Need Structured Output?

- **Data Extraction** — Pull specific fields (name, date, sentiment) from unstructured text
- **API Building** — Return consistent, typed responses from LLM-backed APIs
- **Agents** — Enable reliable tool/function calls with structured arguments

### Ways to Get Structured Output

LangChain provides three main approaches:

#### 1. `with_structured_output`

The recommended high-level method. Accepts a schema (TypedDict or Pydantic) and automatically constrains the model output to match it.

```python
from langchain_openai import ChatOpenAI
from pydantic import BaseModel

class Review(BaseModel):
    summary: str
    sentiment: str  # "positive", "neutral", or "negative"

llm = ChatOpenAI(model="gpt-4o")
structured_llm = llm.with_structured_output(Review)
result = structured_llm.invoke("The product was amazing and worked perfectly!")
```

#### 2. TypedDict

A way to define a dictionary in Python where you specify what keys and values should exist. It helps ensure that your dictionary follows a specific structure.

```python
from typing_extensions import TypedDict

class PersonInfo(TypedDict):
    name: str
    age: int
    city: str
```

> **Note:** TypedDict does **not** validate data at runtime — it only provides type hints for better coding.

Variants: simple TypedDict, Annotated TypedDict, Literal, and more complex configurations.

#### 3. Pydantic

Pydantic is a **data validation and data parsing library** for Python. It ensures that the data you work with is correct, structured, and type-safe.

Key features:
- Basic validation with type enforcement
- Default values and optional fields
- Data coercion (e.g., string `"42"` → int `42`)
- Built-in validators
- `Field()` function for constraints, descriptions, and regex patterns
- Returns a Pydantic object that can be converted to JSON/dict

### When to Use What?

| Scenario | Recommended Approach |
|---|---|
| Quick prototyping | `TypedDict` |
| Production with validation | `Pydantic` |
| Complex nested schemas | `Pydantic` |
| Simple key-value extraction | `TypedDict` or `with_structured_output` |

---

## 6. Output Parsers

### What are Output Parsers?

Output Parsers in LangChain help **convert raw LLM responses** into structured formats like JSON, CSV, Pydantic models, and more. They ensure consistency, validation, and ease of use in applications.

### Types of Output Parsers

#### 1. `StrOutputParser`

The simplest output parser — extracts and returns the model's output as a **plain string**.

```python
from langchain_core.output_parsers import StrOutputParser

chain = prompt | llm | StrOutputParser()
```

Use when: you just need the text response without any structure.

#### 2. `JSONOutputParser`

Parses LLM output as JSON. Useful when you prompt the model to return JSON but don't have a specific schema to enforce.

#### 3. `StructuredOutputParser`

Extracts structured JSON data from LLM responses based on **predefined field schemas** using `ResponseSchema`. It works by defining a list of fields the model should return, ensuring the output follows a structured format.

```python
from langchain.output_parsers import StructuredOutputParser, ResponseSchema

schemas = [
    ResponseSchema(name="name", description="The person's name"),
    ResponseSchema(name="age", description="The person's age"),
]
parser = StructuredOutputParser.from_response_schemas(schemas)
```

#### 4. `PydanticOutputParser`

Parses LLM output directly into a **Pydantic model**, providing runtime type validation.

---

## 7. Chains

### What & Why

A **Chain** in LangChain is a sequence of components connected together, where the output of one step becomes the input of the next. Chains allow you to compose modular, reusable AI pipelines.

**Why use chains?**
- Encapsulate complex multi-step logic into a single callable object
- Reuse sub-chains across different workflows
- Easier debugging and testing at each step

### Types of Chains

#### Simple Chain

A linear pipeline: `prompt → model → output_parser`.

```
Input → Prompt Template → LLM → Output Parser → Final Output
```

#### Sequential Chain

Multiple steps executed in order, where each step's output feeds the next.

```
Step 1 → Step 2 → Step 3 → ... → Final Output
```

**Use case:** Generating a blog post outline first, then writing each section.

#### Parallel Chain

Multiple chains execute simultaneously on the same input and return a combined result.

```
             ┌──→ Chain A ──┐
Input ───────┤              ├──→ Merged Output
             └──→ Chain B ──┘
```

**Use case:** Translating text into multiple languages at once.

#### Conditional Chain

Routes input to different chains based on a condition — like an `if/else` block for AI pipelines.

```
              ┌── Condition A → Chain A
Input → Route ┤
              └── Condition B → Chain B
```

**Use case:** Route a customer query to a refund chain or a FAQ chain depending on intent.

---

## 8. Runnables

### The Why

As LangChain applications grow in complexity, a unified protocol was needed to:
- Connect arbitrary components (prompts, models, parsers, functions)
- Support streaming, batching, async, and parallel execution natively
- Enable introspection and composition with a consistent API

Runnables solve this by providing a **standardized interface** for every component in a pipeline.

### The What

A `Runnable` is any object in LangChain that implements the `invoke`, `batch`, `stream`, and `astream` methods. All major LangChain components (prompts, models, parsers, chains, retrievers) are Runnables.

| Method | Description |
|---|---|
| `.invoke(input)` | Process a single input |
| `.batch(inputs)` | Process a list of inputs in parallel |
| `.stream(input)` | Stream output token by token |
| `.astream(input)` | Async streaming |

---

## 9. LCEL (LangChain Expression Language)

### Overview

**LCEL (LangChain Expression Language)** is the declarative syntax for composing Runnables using the `|` (pipe) operator, inspired by Unix shell piping. It enables building powerful pipelines with minimal boilerplate.

```python
chain = prompt | llm | output_parser
result = chain.invoke({"input": "Hello!"})
```

### LCEL Primitives

#### 1. RunnableSequence

Executes each step **one after another**, passing the output of one step as the input to the next. This is the default behavior when using the `|` operator.

```python
chain = prompt | llm | parser  # RunnableSequence
```

**Use when:** You need a linear, ordered pipeline.

#### 2. RunnableParallel

Allows **multiple runnables to execute in parallel**. Each runnable receives the same input and processes it independently, producing a dictionary of outputs.

```python
from langchain_core.runnables import RunnableParallel

parallel = RunnableParallel(
    summary=summary_chain,
    sentiment=sentiment_chain
)
result = parallel.invoke({"text": "..."})
# result = {"summary": "...", "sentiment": "..."}
```

**Use when:** You need to run multiple independent tasks on the same input simultaneously.

#### 3. RunnablePassthrough

Simply **returns the input as output** without modifying it. Useful for passing data alongside to the next step unchanged.

```python
from langchain_core.runnables import RunnablePassthrough

chain = RunnablePassthrough() | llm
```

**Use when:** You need to preserve the original input and pass it through the chain unchanged.

#### 4. RunnableLambda

Allows you to **apply custom Python functions** within an AI pipeline. Acts as middleware between AI components, enabling preprocessing, transformation, API calls, filtering, and post-processing.

```python
from langchain_core.runnables import RunnableLambda

def format_input(text):
    return text.strip().upper()

chain = RunnableLambda(format_input) | prompt | llm
```

**Use when:** You need to apply custom logic or transformations at any point in the pipeline.

#### 5. RunnableBranch

A **control flow component** that conditionally routes input data to different chains or runnables based on custom logic. Functions like an `if/elif/else` block for chains — the first matching condition is executed. If no condition matches, a default runnable is used.

```python
from langchain_core.runnables import RunnableBranch

branch = RunnableBranch(
    (lambda x: "urgent" in x, urgent_chain),
    (lambda x: "billing" in x, billing_chain),
    default_chain  # fallback
)
```

**Use when:** You need to route requests to different processing paths based on content or intent.

---

## 10. Document Loaders

### Introduction to RAG

**RAG (Retrieval-Augmented Generation)** is a technique that combines information retrieval with language generation, where a model retrieves relevant documents from a knowledge base and then uses them as context to generate accurate and grounded responses.

**Benefits of using RAG:**
1. Use of up-to-date information (beyond the model's training cutoff)
2. Better privacy (keep sensitive data in your own infrastructure)
3. No limit on document size (chunk and retrieve as needed)

### Document Loaders in LangChain

Document loaders are components in LangChain used to **load data from various sources** into a standardized format (usually as `Document` objects), which can then be used for chunking, embedding, retrieval, and generation.

### Common Document Loaders

#### TextLoader

Reads **plain text (`.txt`) files** and converts them into LangChain `Document` objects.

```python
from langchain_community.document_loaders import TextLoader

loader = TextLoader("data.txt")
docs = loader.load()
```

**Use case:** Chat logs, scraped text, transcripts, code snippets, or any plain text data.  
**Limitation:** Works only with `.txt` files.

#### PyPDFLoader

Loads content from **PDF files** and converts each page into a `Document` object.

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("document.pdf")
docs = loader.load()  # One Document per page
```

**Limitations:** Uses the PyPDF library under the hood — not great with scanned PDFs or complex layouts.

#### DirectoryLoader

Loads **multiple documents from an entire directory** (folder) of files.

```python
from langchain_community.document_loaders import DirectoryLoader

loader = DirectoryLoader("./docs/", glob="**/*.txt")
docs = loader.load()
```

**Use when:** You have a collection of files to load in bulk.

#### WebBaseLoader

Loads and extracts text content from **web pages (URLs)**. Uses BeautifulSoup under the hood to parse HTML and extract visible text.

```python
from langchain_community.document_loaders import WebBaseLoader

loader = WebBaseLoader("https://example.com/article")
docs = loader.load()
```

**Use when:** Blogs, news articles, or public websites with primarily static text.  
**Limitations:**
- Doesn't handle JavaScript-heavy pages well (use `SeleniumURLLoader` for that)
- Only loads static content visible in raw HTML

#### CSVLoader

Loads **CSV files** into LangChain `Document` objects — one document per row by default.

```python
from langchain_community.document_loaders.csv_loader import CSVLoader

loader = CSVLoader("data.csv")
docs = loader.load()
```

### Load vs Lazy Load

| Method | Description |
|---|---|
| `.load()` | Loads all documents into memory immediately |
| `.lazy_load()` | Returns a generator — loads documents one at a time (memory efficient for large datasets) |

---

## 11. Text Splitters

### What is Text Splitting?

**Text Splitting** is the process of breaking large chunks of text (like articles, PDFs, HTML pages, or books) into smaller, manageable pieces called **chunks** that an LLM can handle effectively.

```
Large Text ──────────────────────────→ [Chunk 1] [Chunk 2] [Chunk 3] ...
```

### Why Split Text?

- **Overcome model limitations:** Many embedding and language models have maximum input size constraints. Splitting allows processing documents that would otherwise exceed these limits.
- **Improve downstream tasks:** Text splitting improves nearly every LLM-powered task — retrieval, summarization, Q&A, etc.
- **Optimize computational resources:** Working with smaller chunks is more memory-efficient and allows better parallelization.

### 4 Strategies for Text Splitting

#### 1. Length-Based Text Splitting

Splits text based purely on **character or token count**, regardless of content structure.

```python
from langchain.text_splitter import CharacterTextSplitter

splitter = CharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks = splitter.split_text(text)
```

**Pros:** Simple and fast.  
**Cons:** May split mid-sentence, breaking semantic meaning.

#### 2. Text-Structure Based Splitting (Recursive Character Text Splitter)

Splits text using a **hierarchy of separators** (paragraphs → sentences → words → characters), trying to keep semantically related content together.

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    separators=["\n\n", "\n", " ", ""]
)
```

**Most commonly used splitter** — balances simplicity with semantic coherence.

#### 3. Document-Structure Based Splitting

Uses the **inherent structure of documents** (e.g., Markdown headers, HTML tags, Python function boundaries) to determine split points.

**Examples:**
- `MarkdownHeaderTextSplitter` — splits by `#`, `##`, `###` headers
- `HTMLHeaderTextSplitter` — splits by HTML heading tags
- `PythonCodeTextSplitter` — splits Python code by functions/classes

#### 4. Semantic Meaning-Based Splitting

Splits text based on **semantic shifts** — detecting where the topic or meaning changes using embedding similarity. Groups sentences with similar meaning into the same chunk.

```
"Farmers were working hard in fields... The air smelled of earth."   → Chunk A (Agriculture)
"The IPL is the biggest cricket league..."                           → Chunk B (Cricket)
"Terrorism is a big danger to peace..."                             → Chunk C (Security)
```

**Best quality** — but computationally expensive due to embedding calculations.

---

## 12. Vector Stores

### Why Vector Stores?

Traditional databases use exact keyword matching. Vector stores enable **semantic search** — finding content based on meaning, not just keywords.

### What are Vector Stores?

A **vector store** is a system designed to store and retrieve data represented as **numerical vectors** (embeddings).

**Key Features:**

| Feature | Description |
|---|---|
| **Storage** | Retains vectors and associated metadata (in-memory or on-disk) |
| **Similarity Search** | Retrieves vectors most similar to a query vector |
| **Indexing** | Enables fast approximate nearest-neighbor lookups on high-dimensional vectors |
| **CRUD Operations** | Add, read, update, and delete vectors and their associated data |

**Use Cases:**
- Semantic Search
- RAG (Retrieval-Augmented Generation)
- Recommender Systems
- Image / Multimedia Search

### Vector Store vs Vector Database

| | Vector Store | Vector Database |
|---|---|---|
| **Focus** | Lightweight, similarity search | Full-featured database system |
| **Features** | Basic CRUD + similarity search | Distributed, durable, ACID-like, auth, metadata filtering |
| **Best For** | Prototyping, small-medium scale | Production, large-scale deployments |
| **Examples** | FAISS | Milvus, Qdrant, Weaviate |

> 💡 A **vector database** is effectively a vector store **with extra database features** (clustering, scaling, security, metadata filtering, and durability).

### Vector Stores in LangChain

LangChain integrates with multiple vector stores through a **common interface**:

- **Supported Stores:** FAISS, Pinecone, Chroma, Qdrant, Weaviate, and more
- **Common Interface:** Uniform API lets you swap backends (e.g., FAISS → Pinecone) with minimal code changes
- **Metadata Handling:** Attach metadata (timestamps, authors, source URLs) to enable filter-based retrieval

### Chroma Vector Store

**Chroma** is a lightweight, open-source vector database that is especially friendly for local development and small- to medium-scale production needs.

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=OpenAIEmbeddings()
)

# Similarity search
results = vectorstore.similarity_search("What is LangChain?", k=3)
```

---

## 13. Retrievers

### What are Retrievers?

A **retriever** is a component in LangChain that **fetches relevant documents** from a data source in response to a user's query.

Key properties:
- All retrievers in LangChain are **Runnables** (can be used in LCEL chains)
- Multiple types available for different retrieval strategies
- Can be combined and composed

### Types of Retrievers

#### 1. Wikipedia Retriever

Queries the **Wikipedia API** to fetch relevant content for a given query.

```python
from langchain_community.retrievers import WikipediaRetriever

retriever = WikipediaRetriever(top_k_results=3)
docs = retriever.invoke("LangChain framework")
```

**Use when:** You need a general knowledge base without setting up a vector store.

#### 2. Vector Store Retriever

The **most common retriever** — searches and fetches documents from a vector store based on semantic similarity using vector embeddings.

```python
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
docs = retriever.invoke("What is RAG?")
```

#### 3. Maximal Marginal Relevance (MMR)

MMR is an information retrieval algorithm designed to **reduce redundancy** in retrieved results while maintaining high relevance to the query.

It balances two objectives:
1. **Relevance** — documents should be similar to the query
2. **Diversity** — documents should be different from each other (avoiding repetition)

```python
retriever = vectorstore.as_retriever(
    search_type="mmr",
    search_kwargs={"k": 5, "lambda_mult": 0.5}
)
```

#### 4. Multi-Query Retriever

Automatically generates **multiple variations of the user's query** using an LLM, runs them all through the retriever, and deduplicates the results. This helps overcome limitations of single-query search.

```python
from langchain.retrievers.multi_query import MultiQueryRetriever

retriever = MultiQueryRetriever.from_llm(
    retriever=vectorstore.as_retriever(),
    llm=llm
)
```

**Use when:** A single query might not capture all relevant documents.

#### 5. Contextual Compression Retriever

An **advanced retriever** that improves retrieval quality by **compressing documents after retrieval** — keeping only the content relevant to the user's query. Instead of returning full document chunks, it extracts just the relevant snippets.

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

compressor = LLMChainExtractor.from_llm(llm)
retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=vectorstore.as_retriever()
)
```

---

## 14. RAG (Retrieval-Augmented Generation)

### What is RAG?

**RAG (Retrieval-Augmented Generation)** combines information retrieval with language generation. A model retrieves relevant documents from a knowledge base and uses them as context to generate accurate, grounded responses.

> 💡 **In-Context Learning:** A core capability of LLMs (like GPT-4, Claude, Llama) where the model learns to solve a task by seeing context in the prompt — without updating its weights.

### The 4 Stages of RAG

#### Stage 1: Indexing 📥

Preparing your knowledge base for efficient search at query time. Consists of 4 sub-steps:

1. **Document Ingestion** — Load source knowledge into memory using document loaders
2. **Text Chunking** — Break large documents into small, semantically meaningful chunks
3. **Embedding Generation** — Convert each chunk into a dense vector that captures its meaning
4. **Storage in a Vector Store** — Store vectors + original chunk text + metadata in a vector database

#### Stage 2: Retrieval 🔍

The real-time process of finding the most relevant pieces of information from the pre-built index based on the user's question.

1. User submits a query
2. Query is embedded using the same embedding model used during indexing
3. Nearest-neighbor search retrieves the top-k most similar chunks

#### Stage 3: Augmentation 🔗

The retrieved documents (chunks of relevant context) are **combined with the user's query** to form a new, enriched prompt for the LLM.

```
Enriched Prompt = System Instructions + Retrieved Chunks + User Query
```

#### Stage 4: Generation ✍️

The final step where a Large Language Model uses the user's query and the retrieved & augmented context to **generate a response** that is:
- Grounded in the provided documents
- More accurate than relying on training data alone
- Up-to-date with your most recent knowledge

### RAG Architecture Diagram

```
User Query
    │
    ▼
Embed Query
    │
    ▼
Vector Store ──── Similarity Search ──── Top-K Chunks
                                              │
                                              ▼
                              Augment Prompt with Context
                                              │
                                              ▼
                                            LLM
                                              │
                                              ▼
                                      Grounded Response
```

---

## 15. RAG using LangChain

### Implementation Flow

A complete RAG pipeline in LangChain:

```python
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_chroma import Chroma
from langchain.chains import RetrievalQA

# 1. Load documents
loader = PyPDFLoader("document.pdf")
docs = loader.load()

# 2. Split into chunks
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks = splitter.split_documents(docs)

# 3. Create vector store
vectorstore = Chroma.from_documents(chunks, OpenAIEmbeddings())

# 4. Create retriever
retriever = vectorstore.as_retriever()

# 5. Build RAG chain
qa_chain = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(),
    retriever=retriever
)

# 6. Query
result = qa_chain.invoke({"query": "What is the main topic?"})
```

### Improvements & Advanced RAG Techniques

| Category | Improvement |
|---|---|
| **UI** | Streamlit/Gradio interfaces |
| **Evaluation** | Ragas, LangSmith |
| **Indexing** | Better document ingestion, smart splitting strategies |
| **Pre-Retrieval** | Query rewriting with LLM, multi-query generation, domain-aware routing |
| **During Retrieval** | MMR, hybrid retrieval (keyword + semantic), reranking |
| **Post-Retrieval** | Contextual compression |
| **Augmentation** | Prompt templating, answer grounding, context window optimization |
| **Generation** | Answer with citation, guardrailing |
| **System Design** | Multimodal, agentic, memory-based architectures |

---

## 16. Tools

### What is a Tool?

A **tool** is a Python function (or API) that is packaged in a way the LLM can understand and call when needed.

```
Tool = Python Function + Schema (description of inputs/outputs) + Name
```

### How Tools Fit into the Agent Ecosystem

An **AI agent** is an LLM-powered system that can **autonomously think, decide, and take actions** using external tools or APIs to achieve a goal. Tools are the "hands" of an agent — they let the LLM interact with the real world.

### Built-in Tools

A **built-in tool** is a tool that LangChain already provides — pre-built, production-ready, and requiring minimal setup. You don't write the function logic yourself — just import and use it.

Examples:
- `WikipediaQueryRun` — search Wikipedia
- `DuckDuckGoSearchRun` — web search
- `PythonREPLTool` — execute Python code
- `ShellTool` — run shell commands

```python
from langchain_community.tools import WikipediaQueryRun
from langchain_community.utilities import WikipediaAPIWrapper

tool = WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper())
result = tool.invoke("LangChain framework")
```

### Custom Tools

A **custom tool** is a tool that you define yourself for domain-specific tasks.

#### Ways to Create Custom Tools

**1. `@tool` Decorator** — Simplest approach

```python
from langchain.tools import tool

@tool
def get_weather(city: str) -> str:
    """Returns the current weather for a given city."""
    return f"The weather in {city} is sunny and 25°C."
```

**2. `StructuredTool`** — For tools with structured (multi-field) inputs defined using Pydantic models

**3. `BaseTool`** — The abstract base class for all tools; gives maximum control over tool behavior

> `@tool` and `StructuredTool` are built on top of `BaseTool`.

### Toolkits

A **toolkit** is a collection (bundle) of related tools that serve a common purpose — packaged together for convenience and reusability.

```python
# Example: GoogleDriveToolkit bundles:
# - Read files
# - Write files
# - List files
# - Search files
from langchain_google_community import GoogleDriveToolkit
```

---

## 17. Tool Calling

### Tool Binding

Before a model can use a tool, the tool must be **bound** to the model. Binding tells the LLM what tools are available and describes their schemas.

```python
llm_with_tools = llm.bind_tools([tool_1, tool_2, tool_3])
```

### Tool Calling Flow

1. **Bind tools** to the LLM
2. **Invoke** the LLM with a user query
3. LLM decides whether to call a tool and returns a **ToolCall** object (not a final answer)
4. **Execute** the tool with the arguments the LLM provided
5. Return the **ToolMessage** (result) back to the LLM
6. LLM generates a **final response** using the tool's output

### Tool Execution

```python
# Simplified tool execution loop
response = llm_with_tools.invoke(messages)

# If the model wants to use a tool
if response.tool_calls:
    for tool_call in response.tool_calls:
        tool_result = tools_map[tool_call["name"]].invoke(tool_call["args"])
        messages.append(ToolMessage(content=tool_result, tool_call_id=tool_call["id"]))
    
    # Final response after tool use
    final_response = llm_with_tools.invoke(messages)
```

---

## 18. Agents

### What are AI Agents?

An **AI agent** is an LLM-powered system that can:
- **Think** — reason about a problem step by step
- **Decide** — choose which action or tool to use next
- **Act** — execute tools and process results
- **Iterate** — loop until the task is complete

Agents differ from chains in that they are **dynamic** — the sequence of steps is determined at runtime by the LLM, not hardcoded.

### Agent Architecture

```
User Input
    │
    ▼
  Agent (LLM)
    │
    ├──→ Thought: "I need to look this up..."
    │
    ├──→ Action: Call Tool (e.g., web_search)
    │
    ├──→ Observation: Tool returns result
    │
    └──→ Repeat until... Final Answer
```

### 1. ReAct (Reasoning + Acting)

**ReAct** is the most common agent design pattern. It stands for **Reasoning + Acting** and allows an LLM to interleave internal reasoning (Thought) with external actions (like tool use) in a structured, multi-step process.

Instead of generating an answer in one go, the model **thinks step by step**, deciding what it needs to do next and optionally calling tools to help it.

**ReAct Loop:**

```
Thought: I need to find the current population of India.
Action: web_search("India population 2025")
Observation: India's population is approximately 1.44 billion.
Thought: I now have the answer.
Final Answer: India's population is approximately 1.44 billion.
```

### 2. Agent & Agent Executor

| Component | Role |
|---|---|
| **Agent** | The LLM + tools + reasoning strategy (e.g., ReAct). Makes decisions about what to do next. |
| **AgentExecutor** | The runtime that calls the agent, executes the chosen tools, feeds results back, and loops until done. |

### 3. Building an Agent in LangChain

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain import hub

# 1. Define tools
tools = [search_tool, calculator_tool]

# 2. Get ReAct prompt
prompt = hub.pull("hwchase17/react")

# 3. Create the agent
agent = create_react_agent(llm=llm, tools=tools, prompt=prompt)

# 4. Create the executor
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 5. Run
result = agent_executor.invoke({"input": "What is the square root of the population of India?"})
```

### Agent Flow Chart

```
Start
  │
  ▼
Receive Input
  │
  ▼
Agent thinks (LLM call)
  │
  ├─── Need to use tool? → YES → Select Tool → Execute Tool → Get Observation ──┐
  │                                                                               │
  │                                                                               ▼
  └─── Need to use tool? → NO ────────────────────────────────────────── Generate Final Answer
                                                                                  │
                                                                                  ▼
                                                                                 End
```

---

## 📚 Key Concepts Summary

| Concept | One-Liner |
|---|---|
| **LangChain** | Open-source framework for building LLM applications |
| **Model** | Abstraction layer over LLMs, chat models, and embeddings |
| **Prompt Template** | Reusable, parameterized prompt with variable injection |
| **Chain** | Sequence of components forming a pipeline |
| **LCEL** | `\|` pipe syntax for composing Runnables declaratively |
| **Runnable** | Standard interface all LangChain components implement |
| **Document Loader** | Ingests raw data (PDFs, URLs, CSVs) into Document objects |
| **Text Splitter** | Breaks large documents into manageable chunks |
| **Embedding** | Converts text into a numerical vector capturing meaning |
| **Vector Store** | Database for storing and searching vectors by similarity |
| **Retriever** | Fetches relevant documents for a given query |
| **RAG** | Combine retrieval + generation for grounded, up-to-date answers |
| **Tool** | Python function the LLM can call to interact with the world |
| **Agent** | LLM that autonomously decides which tools to use and when |

---

*Notes compiled from LangChain course materials, January–April 2025.*
