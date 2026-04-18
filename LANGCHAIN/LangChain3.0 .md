# 🦜🔗 LangChain — Complete Course Notes

> **Framework:** LangChain (Open Source) | **Updated:** January–April 2025
>
> A comprehensive guide covering every concept with detailed explanations, working code examples, and beginner-friendly breakdowns of every import and code block.

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

LangChain is an **open-source framework** for developing applications powered by large language models (LLMs). It provides modular components and end-to-end tools that help developers build complex AI applications, including chatbots, question-answering systems, RAG pipelines, autonomous agents, and more.

### Why LangChain?

| Feature | Description |
|---|---|
| LLM Support | Supports all major LLMs (OpenAI, Anthropic, Google, HuggingFace, etc.) |
| Simplicity | Simplifies developing LLM-based applications |
| Integrations | Integrations available for all major tools and data sources |
| Open Source | Free, actively developed, large community |
| GenAI Use Cases | Supports all major GenAI use cases out of the box |

### What Can You Build?

- **Conversational Chatbots** — Context-aware multi-turn conversations
- **AI Knowledge Assistants** — Q&A over your own documents
- **AI Agents** — Systems that autonomously plan and act
- **Workflow Automation** — Chain AI steps to automate complex tasks
- **Summarization / Research Helpers** — Condense large corpora into insights

### Alternatives

| Framework | Focus |
|---|---|
| **LlamaIndex** | Data indexing and retrieval for RAG |
| **Haystack** | Pipeline-based NLP framework |

---

## 2. LangChain Components Overview

LangChain is composed of five core building blocks:

```
┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   Models    │  │   Prompts   │  │   Chains    │  │   Indexes   │  │   Memory    │
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

The **Model Component** in LangChain abstracts the complexity of working directly with different LLMs, chat models, and embedding models. It provides a **uniform interface** so you can swap one model for another with minimal code changes.

Think of it like a power adapter — regardless of the underlying provider (OpenAI, Anthropic, Google), LangChain gives you the same plug to interact with all of them.

---

### 3.1 Language Models — LLMs vs Chat Models

#### LLMs (Legacy — Less Common Today)

- **Input:** A plain string
- **Output:** A plain string
- Used for raw text generation; traditionally older models

```python
# pip install langchain-openai
from langchain_openai import OpenAI

llm = OpenAI(model="gpt-3.5-turbo-instruct", api_key="YOUR_API_KEY")

# Simple string in, string out
response = llm.invoke("What is the capital of France?")
print(response)
# Output: "Paris is the capital of France."
```

**Imports explained:**
- `langchain_openai` — the LangChain package that provides OpenAI integrations; install with `pip install langchain-openai`
- `OpenAI` — wraps the older OpenAI completion models (not chat models); takes plain text in, gives plain text back; rarely used in modern apps

**What this code does:**
- Creates an `OpenAI` LLM object pointing at `gpt-3.5-turbo-instruct`
- `.invoke("...")` — the universal method to run any LangChain component; sends the string to OpenAI's API and returns the response as a plain string

---

#### Chat Models (Modern — Preferred)

- **Input:** A list of messages (with roles: `system`, `human`, `assistant`)
- **Output:** An `AIMessage` object
- Specialized for conversational tasks; the standard today

```python
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, SystemMessage

chat = ChatOpenAI(model="gpt-4o", api_key="YOUR_API_KEY")

messages = [
    SystemMessage(content="You are a helpful geography tutor."),
    HumanMessage(content="What is the capital of France?")
]

response = chat.invoke(messages)
print(response.content)
# Output: "The capital of France is Paris."

# The response is an AIMessage object — you access text via .content
print(type(response))  # <class 'langchain_core.messages.ai.AIMessage'>
```

**Imports explained:**
- `ChatOpenAI` — wrapper for OpenAI's modern chat models (GPT-4o, GPT-4, etc.); works with a list of typed messages, not a plain string; this is the class you'll use in almost every LangChain project
- `HumanMessage` — represents the user's message in the conversation; maps to the `"user"` role in OpenAI's API
- `SystemMessage` — sets the AI's behavior and persona at the start; maps to the `"system"` role; think of it as instructions given to the AI before the conversation begins

**What this code does:**
- Creates a `ChatOpenAI` model pointing at `gpt-4o`
- Builds a message list: first a `SystemMessage` (persona) then a `HumanMessage` (the actual question)
- `.invoke(messages)` — sends the full message list to OpenAI's API
- The response is an `AIMessage` object — to read the text you access `.content`; the raw object also contains metadata like token usage

> **Why does this matter?** Chat models understand conversational structure — the `system` role lets you define the AI's persona, `human` is the user's turn, and `assistant` is the model's previous responses. LLMs just see a blob of text with no concept of roles.

---

### 3.2 Setup for Different Providers

```python
# --- OpenAI ---
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o", api_key="sk-...")

# --- Anthropic ---
from langchain_anthropic import ChatAnthropic
llm = ChatAnthropic(model="claude-3-5-sonnet-20241022", api_key="sk-ant-...")

# --- Google Gemini ---
from langchain_google_genai import ChatGoogleGenerativeAI
llm = ChatGoogleGenerativeAI(model="gemini-1.5-pro", google_api_key="AIza...")

# --- HuggingFace (open source, runs locally via API) ---
from langchain_huggingface import HuggingFaceEndpoint
llm = HuggingFaceEndpoint(repo_id="mistralai/Mistral-7B-Instruct-v0.2", huggingfacehub_api_token="hf_...")
```

**Imports explained:**
- `langchain_anthropic` — LangChain's integration package for Anthropic's Claude models; install with `pip install langchain-anthropic`
- `langchain_google_genai` — LangChain's integration for Google's Gemini models; install with `pip install langchain-google-genai`
- `langchain_huggingface` — LangChain's integration for HuggingFace models; lets you call hosted models via HuggingFace's Inference API
- `ChatAnthropic`, `ChatGoogleGenerativeAI`, `HuggingFaceEndpoint` — provider-specific wrappers, all sharing the same `.invoke()` interface

**What this code does:**
- Shows that swapping between providers is just a matter of changing the import and class — the rest of your code (prompts, chains, etc.) stays identical
- Each class takes provider-specific arguments (api_key, model name) but all expose `.invoke()`, `.stream()`, `.batch()` in exactly the same way

---

### 3.3 Open Source Models

Open-source language models are freely available AI models that can be downloaded, modified, fine-tuned, and deployed without restrictions.

**Famous Open Source Models:** Llama 3 (Meta), Mistral 7B / Mixtral 8x7B, Gemma (Google), Falcon

**Where to find them:** HuggingFace — the largest repository of open-source LLMs.

#### Running Open Source Models Locally via Ollama

```python
# First: install Ollama and pull a model
# $ ollama pull llama3

from langchain_ollama import ChatOllama

llm = ChatOllama(model="llama3")

response = llm.invoke("Explain quantum computing in simple terms.")
print(response.content)
```

**Imports explained:**
- `langchain_ollama` — LangChain's integration for Ollama, a tool that lets you run open-source LLMs completely locally on your machine with no API key needed
- `ChatOllama` — a chat model class that talks to your locally running Ollama server (runs on `localhost:11434` by default); the interface is identical to `ChatOpenAI`

**What this code does:**
- Assumes Ollama is installed and `llama3` model is downloaded locally
- Creates a `ChatOllama` object connecting to the local server
- `.invoke()` is identical to using `ChatOpenAI` — the only difference is the model runs on your machine, not in the cloud

---

#### Running via HuggingFace Pipeline (Local)

```python
from langchain_huggingface import HuggingFacePipeline
from transformers import pipeline

# Load model locally
hf_pipeline = pipeline("text-generation", model="gpt2", max_new_tokens=100)
llm = HuggingFacePipeline(pipeline=hf_pipeline)

response = llm.invoke("Once upon a time")
print(response)
```

**Imports explained:**
- `HuggingFacePipeline` — wraps a HuggingFace `transformers` pipeline so it works as a LangChain LLM
- `transformers` — HuggingFace's library for loading and running ML models locally; `pipeline()` is a high-level function that handles model loading and tokenization automatically
- `pipeline("text-generation", model="gpt2")` — downloads and loads GPT-2 locally, ready for text generation

**What this code does:**
- Downloads and loads `gpt2` locally using HuggingFace's `transformers` library
- Wraps it in `HuggingFacePipeline` so it has the standard LangChain `.invoke()` interface
- Text is generated fully locally — no API calls, no internet needed after the model is downloaded

---

### 3.4 Embedding Models

Embedding models convert text into **dense numerical vectors** that capture semantic meaning. Similar texts produce similar vectors — this is how semantic search works.

```
"cat" → [0.23, -0.87, 0.45, ...]   (1536 dimensions)
"dog" → [0.21, -0.84, 0.48, ...]   (very close — similar meaning)
"car" → [-0.54, 0.12, -0.33, ...]  (far away — different meaning)
```

```python
from langchain_openai import OpenAIEmbeddings

embedder = OpenAIEmbeddings(model="text-embedding-3-small", api_key="YOUR_API_KEY")

# Embed a single text (used for queries at search time)
vector = embedder.embed_query("What is machine learning?")
print(len(vector))    # 1536 — number of dimensions
print(vector[:5])     # [0.003, -0.021, 0.045, -0.011, 0.032, ...]

# Embed multiple texts at once (used for documents during indexing)
texts = ["LangChain is great", "Python is popular", "AI is transforming the world"]
vectors = embedder.embed_documents(texts)
print(len(vectors))   # 3 — one vector per text
```

**Imports explained:**
- `OpenAIEmbeddings` — calls OpenAI's embedding API to convert text into numerical vectors; `text-embedding-3-small` is fast and cheap; `text-embedding-3-large` is more accurate but slower and costlier

**What this code does:**
- `.embed_query("text")` — embeds a single string; used at query time when a user asks a question (one API call)
- `.embed_documents(["text1", "text2", ...])` — embeds multiple strings in a single batch API call; used during indexing to embed many document chunks efficiently
- The output is a list of floats (the vector) — the same text always produces the same vector

---

#### Computing Similarity Between Embeddings

```python
import numpy as np

def cosine_similarity(v1, v2):
    # Returns a value between -1 and 1. Closer to 1 = more similar.
    return np.dot(v1, v2) / (np.linalg.norm(v1) * np.linalg.norm(v2))

embedder = OpenAIEmbeddings()
vec1 = embedder.embed_query("king")
vec2 = embedder.embed_query("queen")
vec3 = embedder.embed_query("automobile")

print(cosine_similarity(vec1, vec2))  # ~0.85 — very similar
print(cosine_similarity(vec1, vec3))  # ~0.30 — not similar
```

**Imports explained:**
- `numpy` — Python's core numerical computing library; used here for `np.dot` (dot product) and `np.linalg.norm` (vector magnitude) to compute cosine similarity

**What this code does:**
- `cosine_similarity` — measures the angle between two vectors; 1.0 = same direction (same meaning), 0 = perpendicular (unrelated), -1 = opposite
- Embeds three words and compares them — demonstrates that "king" and "queen" have high similarity while "automobile" is semantically distant from both
- This is the math that powers vector store search: query vector vs stored document vectors

> Embeddings are the foundation of RAG — you embed your documents, store the vectors, then at query time embed the question and find the closest document vectors.

---

## 4. Prompts

### What are Prompts?

Prompts are the **input instructions or queries** given to a model to guide its output. A bad prompt gives vague, unreliable output. A well-engineered prompt gives precise, consistent, useful output.

---

### 4.1 Static vs Dynamic Prompts

#### Static Prompt (Hardcoded — Avoid in Production)

```python
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage

llm = ChatOpenAI(model="gpt-4o")

# Static: fixed, cannot reuse for different inputs
response = llm.invoke([HumanMessage(content="Summarize Attention Is All You Need in 3 sentences.")])
print(response.content)
# Problem: if you want a different paper or different style, you rewrite the whole string.
```

**Imports explained:**
- `ChatOpenAI` — the OpenAI chat model wrapper (covered in section 3)
- `HumanMessage` — wraps the user's text into a typed message object the chat model expects

**What this code does:**
- The prompt is completely hardcoded — the paper name and sentence count are baked in
- To change anything, you edit the code directly — not scalable for real applications
- Fine for quick experiments, but unsuitable for production where inputs vary

---

#### Dynamic Prompt (Templated — Preferred)

```python
paper = "BERT: Pre-training of Deep Bidirectional Transformers"
style = "beginner-friendly"

prompt_text = f"Summarize {paper} in a {style} way in 3 sentences."
response = llm.invoke([HumanMessage(content=prompt_text)])
# Better, but still a plain f-string — no validation
```

**What this code does:**
- Uses Python's f-string to inject variables — better than hardcoding
- Still problematic: no validation (typos silently produce wrong prompts), and doesn't integrate with LangChain's pipeline system

---

### 4.2 PromptTemplate

`PromptTemplate` is LangChain's structured way to define dynamic prompts with **named placeholders**. It is the building block of all prompt engineering in LangChain.

```python
from langchain.prompts import PromptTemplate

# Define the template with {placeholders}
template = PromptTemplate(
    input_variables=["paper", "style", "length"],
    template="""Summarize the research paper titled "{paper}" with the following specifications:
- Explanation Style: {style}
- Explanation Length: {length}

If certain information is not available, respond with "Insufficient information available."
Ensure the summary is clear and accurate."""
)

# Fill in variables at runtime
filled_prompt = template.format(
    paper="Attention Is All You Need",
    style="Technical",
    length="Medium (3-5 paragraphs)"
)

print(filled_prompt)

from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke(filled_prompt)
print(response.content)
```

**Imports explained:**
- `langchain.prompts` — the prompts module inside LangChain's core library
- `PromptTemplate` — holds a prompt string with `{placeholders}` and a list of expected variable names; validates that all required variables are provided; integrates natively with LangChain's `|` pipe operator

**What this code does:**
- `input_variables=["paper", "style", "length"]` — declares all placeholders; LangChain will error if you forget to pass one
- `template="""..."""` — the prompt string with `{paper}`, `{style}`, `{length}` as placeholders
- `.format(...)` — fills in all placeholders and returns a complete prompt string ready to send to the model

---

#### Why PromptTemplate over f-strings?

```python
# f-string — no validation, typos crash at runtime
topic = "machine learning"
prompt = f"Explain {topicc} in simple terms."
# NameError: name 'topicc' is not defined

# PromptTemplate — catches errors at definition time
from langchain.prompts import PromptTemplate
template = PromptTemplate(
    input_variables=["topic"],
    template="Explain {topic} in simple terms."
)
template.format(topicc="machine learning")
# ValueError: "topicc" is not a valid variable. Expected: ['topic']
```

**What this code does:**
- Left side: the f-string typo `{topicc}` crashes with a confusing `NameError` at runtime
- Right side: `PromptTemplate` immediately raises a clear `ValueError` telling you exactly what went wrong and what variable names are valid
- The error message from `PromptTemplate` is beginner-friendly: "topicc is not valid, expected: ['topic']"

| Reason | Explanation |
|---|---|
| **Default Validation** | Raises clear errors if a variable is missing or misspelled |
| **Reusable** | Define once, use across multiple chains and agents |
| **LangChain Ecosystem** | Natively composable with the `|` pipe operator in LCEL |

---

### 4.3 Messages

LangChain uses typed message classes to represent each turn in a conversation:

```python
from langchain_core.messages import (
    SystemMessage,   # Sets AI persona/instructions
    HumanMessage,    # User's input
    AIMessage,       # Model's previous response
    ToolMessage      # Result returned from a tool
)

# Build a conversation manually
messages = [
    SystemMessage(content="You are a concise Python tutor. Keep answers short."),
    HumanMessage(content="What is a list comprehension?"),
    AIMessage(content="A list comprehension creates a list in one line: [x*2 for x in range(5)] → [0,2,4,6,8]."),
    HumanMessage(content="Can you show a more complex example with filtering?")
]

from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke(messages)
print(response.content)
# The model sees the full conversation history and responds in context
```

**Imports explained:**
- `SystemMessage` — the "instructions to the AI" message; placed first; defines behavior, tone, and persona for the entire conversation
- `HumanMessage` — wraps text from the user's side; each new user turn is a new `HumanMessage`
- `AIMessage` — wraps the AI's previous responses; used to replay past turns so the model has memory of what it already said
- `ToolMessage` — holds the result of a tool/function call; used in agentic workflows (section 17)

**What this code does:**
- Builds a multi-turn conversation as a list of typed messages, simulating a conversation already in progress
- The model reads all four messages in order and responds to the latest `HumanMessage` in context
- The `AIMessage` in the middle is the AI's previous answer — by including it, the model "remembers" the previous exchange

> By passing the full conversation history as `messages`, the model remembers the context of earlier turns — this is how chatbots maintain state.

---

### 4.4 ChatPromptTemplate

`ChatPromptTemplate` constructs message-based prompts for chat models. It produces a **list of typed messages** rather than a plain string.

```python
from langchain.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# Define a chat prompt with system + human messages
chat_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are an expert in {domain}. Answer clearly and concisely."),
    ("human", "Explain {concept} in simple terms.")
])

# Format with variables
messages = chat_prompt.format_messages(
    domain="machine learning",
    concept="backpropagation"
)

print(messages)
# [SystemMessage(content='You are an expert in machine learning...'),
#  HumanMessage(content='Explain backpropagation in simple terms.')]

from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke(messages)
print(response.content)

# Use directly in a chain with the | operator
chain = chat_prompt | llm | StrOutputParser()
result = chain.invoke({"domain": "astronomy", "concept": "black holes"})
```

**Imports explained:**
- `ChatPromptTemplate` — like `PromptTemplate` but designed for chat models; takes `(role, template)` tuples and produces typed message objects (`SystemMessage`, `HumanMessage`)
- `StrOutputParser` — converts the `AIMessage` response to a plain string (covered in section 6)

**What this code does:**
- `.from_messages([...])` — defines the prompt as a list of `(role, template_string)` tuples; `"system"` → `SystemMessage`, `"human"` → `HumanMessage`
- Variables like `{domain}` work across all messages in the template
- `.format_messages(...)` — fills in all variables and returns a ready-to-use list of typed message objects
- The last lines show LCEL usage: pipe `ChatPromptTemplate` directly into a model with `|`

---

### 4.5 Few-Shot Prompting

Provides **examples** in the prompt to guide the model's output format or reasoning — without any training.

```python
from langchain.prompts import FewShotPromptTemplate, PromptTemplate

# Define examples
examples = [
    {"input": "happy",  "output": "sad"},
    {"input": "tall",   "output": "short"},
    {"input": "fast",   "output": "slow"},
]

# Template for how each example is formatted
example_template = PromptTemplate(
    input_variables=["input", "output"],
    template="Word: {input}\nAntonym: {output}"
)

# Assemble the full few-shot prompt
few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_template,
    prefix="Give the antonym of each word.\n",
    suffix="\nWord: {input}\nAntonym:",
    input_variables=["input"]
)

# See what gets sent to the model
print(few_shot_prompt.format(input="hot"))
# Give the antonym of each word.
# Word: happy
# Antonym: sad
# Word: tall
# Antonym: short
# Word: fast
# Antonym: slow
# Word: hot
# Antonym:

# Use with a model
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke(few_shot_prompt.format(input="hot"))
print(response.content)  # "cold"
```

**Imports explained:**
- `FewShotPromptTemplate` — automatically formats and injects a list of input→output examples into the prompt before asking the actual question; teaches the model by demonstration, not fine-tuning
- `PromptTemplate` — used here as `example_prompt` to define how each individual example should be displayed (the per-example format)

**What this code does:**
- `examples` — a list of dicts, each one a demonstration of the expected input→output behavior
- `example_template` — defines the display format for each example: `"Word: happy\nAntonym: sad"`
- `prefix` — text added before all examples (the task instruction)
- `suffix` — text added after all examples, containing the actual new question; the model is expected to continue the pattern and fill in the blank
- `.format(input="hot")` — assembles everything: instruction + all examples + the new question

---

### 4.6 MessagesPlaceholder

`MessagesPlaceholder` is a special placeholder inside a `ChatPromptTemplate` to **dynamically inject chat history** at runtime. Essential for building chatbots that remember previous messages.

```python
from langchain.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.messages import HumanMessage, AIMessage
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

# Define prompt with a slot for chat history
chat_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant. Remember details the user shares."),
    MessagesPlaceholder(variable_name="chat_history"),  # history injected here
    ("human", "{question}")
])

# Simulate stored chat history from previous turns
chat_history = [
    HumanMessage(content="My name is Nitish and I live in Gurgaon."),
    AIMessage(content="Nice to meet you, Nitish from Gurgaon! How can I help?"),
]

llm = ChatOpenAI(model="gpt-4o")
chain = chat_prompt | llm | StrOutputParser()

# The model has access to both history and the new question
response = chain.invoke({
    "chat_history": chat_history,
    "question": "What city do I live in?"
})
print(response)
# "You mentioned that you live in Gurgaon."
```

**Imports explained:**
- `MessagesPlaceholder` — a special slot inside `ChatPromptTemplate` that accepts a list of message objects at runtime and inserts them directly at that position in the prompt
- `AIMessage` — used here to represent a previous AI response that becomes part of the injected history

**What this code does:**
- The prompt has three parts: system instructions, a history slot, and the new question
- `MessagesPlaceholder(variable_name="chat_history")` — reserves a slot; at runtime, any list of messages passed as `"chat_history"` gets inserted there
- The final prompt the model sees is: system → old user message → old AI response → new question
- The model correctly answers "What city do I live in?" because the earlier message about Gurgaon is in the history

> Without `MessagesPlaceholder`, you'd manually concatenate messages — error-prone and messy. The placeholder makes history injection clean and declarative.

---

## 5. Structured Output

### What is Structured Output?

By default, LLMs return free-form text. **Structured output** forces the model to return data in a well-defined format (like JSON) so your code can reliably parse and use it.

```
Free-form:   "The sentiment is positive and the score is 9 out of 10."
Structured:  {"sentiment": "positive", "score": 9}
```

### Why Do We Need It?

- **Data Extraction** — Pull specific fields from unstructured text
- **API Building** — Return typed, consistent responses from LLM-backed endpoints
- **Agents** — LLM needs to call tools with structured arguments

---

### 5.1 `with_structured_output` (Recommended)

The cleanest, highest-level approach. Wrap your LLM with a schema and it automatically constrains output to match.

```python
from langchain_openai import ChatOpenAI
from pydantic import BaseModel, Field
from typing import Literal

# Define your desired output schema
class ProductReview(BaseModel):
    summary: str = Field(description="A brief 1-2 sentence overview of the review")
    sentiment: Literal["positive", "neutral", "negative"] = Field(description="Overall tone")
    score: int = Field(description="Rating out of 10", ge=1, le=10)

llm = ChatOpenAI(model="gpt-4o")

# Wrap the LLM with the schema
structured_llm = llm.with_structured_output(ProductReview)

# Invoke — returns a ProductReview object, NOT a raw string
result = structured_llm.invoke(
    "I bought this phone last week. The camera is outstanding, "
    "battery lasts all day, but the price is a bit steep."
)

print(result.summary)    # "A well-rounded phone with excellent camera and battery..."
print(result.sentiment)  # "positive"
print(result.score)      # 8
print(type(result))      # <class '__main__.ProductReview'>

# Convert to dict or JSON
print(result.model_dump())
print(result.model_dump_json(indent=2))
```

**Imports explained:**
- `BaseModel` — the base class from Pydantic that all data schemas inherit from; gives automatic validation, serialization, and type safety
- `Field` — a Pydantic helper that adds metadata (description, constraints) to each field; `ge=1` means "greater-or-equal to 1", `le=10` means "less-or-equal to 10"
- `Literal` — from Python's `typing` module; restricts a field to a specific set of allowed string values — the model can only return one of the listed options

**What this code does:**
- `ProductReview` defines the exact output structure with three fields and their types/constraints
- `.with_structured_output(ProductReview)` — uses OpenAI's native function-calling feature to guarantee structured output
- The response is a real Python `ProductReview` object — access fields with dot notation: `result.summary`
- `.model_dump()` converts to a plain Python dict; `.model_dump_json()` converts to a JSON string

---

### 5.2 TypedDict

`TypedDict` defines a dictionary with specific keys and value types. Lighter than Pydantic — good for quick schemas without runtime validation.

```python
from typing_extensions import TypedDict
from langchain_openai import ChatOpenAI

# Simple TypedDict
class PersonInfo(TypedDict):
    name: str
    age: int
    city: str

llm = ChatOpenAI(model="gpt-4o")
structured_llm = llm.with_structured_output(PersonInfo)

result = structured_llm.invoke("John is a 28-year-old software engineer from Bangalore.")
print(result)            # {'name': 'John', 'age': 28, 'city': 'Bangalore'}
print(result["name"])    # John — access with [] like a regular dict
```

**Imports explained:**
- `typing_extensions` — a backport library that provides newer Python typing features; `TypedDict` was here before being added to Python's standard `typing` module
- `TypedDict` — defines what keys a dictionary must have and what types their values should be; for type hints only — no runtime validation happens

**What this code does:**
- `PersonInfo` describes a dict with three required keys and their types
- `llm.with_structured_output(PersonInfo)` — tells the model to extract these three fields
- The output is a regular Python dict — access with `result["name"]`, not dot notation

---

#### Annotated TypedDict (with descriptions)

```python
from typing import Annotated
from typing_extensions import TypedDict

class MovieInfo(TypedDict):
    title:    Annotated[str, "The title of the movie"]
    director: Annotated[str, "The director's full name"]
    year:     Annotated[int, "Year of release"]
    genre:    Annotated[str, "Primary genre"]

structured_llm = llm.with_structured_output(MovieInfo)
result = structured_llm.invoke("Inception was directed by Christopher Nolan and released in 2010.")
print(result)
# {'title': 'Inception', 'director': 'Christopher Nolan', 'year': 2010, 'genre': 'Thriller'}
```

**Imports explained:**
- `Annotated` — wraps a type with extra metadata; `Annotated[str, "description"]` means "this is a string AND here is a hint for the LLM about what it should contain"

**What this code does:**
- Same as plain `TypedDict` but each field carries a description string passed to the model
- The model uses these descriptions to understand exactly what to extract — reduces ambiguity

---

#### Literal (Constrained values)

```python
from typing import Literal
from typing_extensions import TypedDict

class SentimentResult(TypedDict):
    sentiment:  Literal["positive", "neutral", "negative"]  # Only these 3 values allowed
    confidence: Literal["high", "medium", "low"]

structured_llm = llm.with_structured_output(SentimentResult)
result = structured_llm.invoke("This product is absolutely terrible.")
print(result)
# {'sentiment': 'negative', 'confidence': 'high'}
```

**What this code does:**
- `Literal["positive", "neutral", "negative"]` — the model is not allowed to invent other values; it must pick one of these three exactly
- Useful for categorical outputs where downstream code does `if result["sentiment"] == "negative":`

> **Important:** TypedDict does **not** validate data at runtime. Use Pydantic for runtime validation.

---

### 5.3 Pydantic

Pydantic is a **data validation library** for Python that ensures data is correct, structured, and type-safe — with full **runtime validation**.

```python
from pydantic import BaseModel, Field
from typing import Optional, List
from langchain_openai import ChatOpenAI

# Basic Pydantic model
class Person(BaseModel):
    name: str
    age: int

p = Person(name="Nitish", age=35)
print(p.name)   # Nitish
print(p.age)    # 35

# Pydantic validates types at runtime
try:
    p2 = Person(name="Nitish", age="not-a-number")
except Exception as e:
    print(e)  # age must be an integer
```

**Imports explained:**
- `pydantic` — a widely used Python data validation library; when you create a Pydantic model instance, it immediately checks all values match their declared types
- `BaseModel` — the parent class all Pydantic models inherit from; provides automatic validation, `.model_dump()`, `.model_dump_json()`
- `Field` — adds constraints (min, max, regex, gt, lt) and descriptions to fields
- `Optional` — marks a field as optional (can be `None`)
- `List` — specifies a field is a list of a given type; e.g., `List[str]` is a list of strings

**What this code does:**
- `Person(name="Nitish", age=35)` — Pydantic validates: is `name` a string? Is `age` an integer? Yes → object created
- `Person(age="not-a-number")` — Pydantic immediately raises `ValidationError` because `"not-a-number"` cannot be converted to `int` — this is runtime validation that TypedDict doesn't do

---

#### Default Values

```python
class Config(BaseModel):
    model:       str   = "gpt-4o"
    temperature: float = 0.7
    max_tokens:  int   = 1000
    verbose:     bool  = False

c  = Config()                            # Use all defaults
c2 = Config(model="gpt-3.5-turbo", temperature=0.0)
print(c2.max_tokens)  # 1000 (from default)
```

**What this code does:**
- Fields with `= value` have defaults — they become optional when creating the object
- `Config()` with no arguments uses all defaults
- `Config(model="gpt-3.5-turbo")` overrides only `model`; `max_tokens` stays at its default `1000`

---

#### Optional Fields

```python
from typing import Optional

class UserProfile(BaseModel):
    username: str                    # Required
    email:    str                    # Required
    bio:      Optional[str] = None   # Optional, defaults to None
    age:      Optional[int] = None

u = UserProfile(username="nitish", email="n@example.com")
print(u.bio)  # None
```

**What this code does:**
- `Optional[str] = None` — the field can be a string OR `None`; omitting it when creating the object is allowed
- `u.bio` returns `None` because no bio was provided — no error raised

---

#### Field Function — Constraints, Descriptions, Regex

```python
from pydantic import BaseModel, Field

class Product(BaseModel):
    name:   str   = Field(min_length=2,  max_length=100, description="Product name")
    price:  float = Field(gt=0,          description="Price in USD, must be positive")
    rating: float = Field(ge=1.0, le=5.0, description="Rating between 1 and 5")
    sku:    str   = Field(pattern=r"^[A-Z]{3}-\d{4}$", description="Format: ABC-1234")

p = Product(name="Widget", price=29.99, rating=4.5, sku="WDG-0042")
print(p)
```

**What this code does:**
- `min_length` / `max_length` — enforce string length bounds
- `gt=0` — "greater than 0"; price must be strictly positive
- `ge=1.0, le=5.0` — "greater-or-equal to 1.0, less-or-equal to 5.0"; rating must be in that range
- `pattern=r"..."` — validates the string against a regex; `WDG-0042` matches `ABC-1234` pattern

---

#### Using Pydantic with `with_structured_output`

```python
from pydantic import BaseModel, Field
from typing import List
from langchain_openai import ChatOpenAI

class NewsArticle(BaseModel):
    """Structured information extracted from a news article."""
    headline:    str       = Field(description="The main headline")
    key_people:  List[str] = Field(description="Names of key people mentioned")
    location:    str       = Field(description="Primary location of the event")
    sentiment:   str       = Field(description="Overall tone: positive/negative/neutral")

llm = ChatOpenAI(model="gpt-4o")
structured_llm = llm.with_structured_output(NewsArticle)

article = """
Prime Minister Modi inaugurated the new metro line in Mumbai today,
joined by Maharashtra Chief Minister. The Rs.12,000 crore project 
is expected to ease traffic congestion for 1.2 million daily commuters.
"""

result = structured_llm.invoke(article)
print(result.headline)
print(result.key_people)   # ["Modi", "Maharashtra Chief Minister"]
print(result.location)     # "Mumbai"
print(result.model_dump())
```

**What this code does:**
- `List[str]` — tells the model that `key_people` should be a list of names, not a single string
- The docstring on the class gives the model a high-level description of the entire schema's purpose
- `.with_structured_output(NewsArticle)` uses function-calling to guarantee all fields are filled correctly
- `result.model_dump()` converts the full Pydantic object into a plain Python dict

---

### 5.4 When to Use What?

| Scenario | Use |
|---|---|
| Quick prototype, no validation needed | `TypedDict` |
| Production with type safety + validation | `Pydantic` |
| Complex nested schemas | `Pydantic` |
| Constrained string values (enums) | `Literal` with either |
| Simple key-value extraction | `TypedDict` |

---

## 6. Output Parsers

### What are Output Parsers?

Output Parsers **convert raw LLM text responses** into structured Python objects. They're useful when you can't use `with_structured_output` (e.g., older models) or when parsing free-form text post-hoc.

> **Key difference from `with_structured_output`:** Output parsers work by prompting the model to follow a format (less reliable) and parsing the text. `with_structured_output` uses model-native function calling (more reliable). Prefer `with_structured_output` when possible.

---

### 6.1 StrOutputParser

The simplest parser — extracts the model's output as a **plain Python string**, stripping the `AIMessage` wrapper.

```python
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
prompt = ChatPromptTemplate.from_messages([("human", "Tell me a fun fact about {topic}")])
parser = StrOutputParser()

# Without parser: returns an AIMessage object
raw = llm.invoke(prompt.format_messages(topic="black holes"))
print(type(raw))      # <class 'langchain_core.messages.ai.AIMessage'>
print(raw.content)    # "A fun fact about black holes is..."

# With parser: returns plain string — much easier to work with
chain  = prompt | llm | parser
result = chain.invoke({"topic": "black holes"})
print(type(result))   # <class 'str'>
print(result)         # "A fun fact about black holes is..."
```

**Imports explained:**
- `langchain_core.output_parsers` — the module containing all built-in output parsers in LangChain's core
- `StrOutputParser` — the simplest parser; receives an `AIMessage` and returns just its `.content` as a plain string; removes the need to write `.content` manually every time

**What this code does:**
- Without parser: `llm.invoke()` returns an `AIMessage` object — you'd have to write `response.content` to get the text
- With parser at the end of the chain: output is already a plain Python string — no `.content` needed
- `StrOutputParser` is used in almost every chain for this reason

---

### 6.2 JSONOutputParser

Instructs the model to return JSON and parses the response into a Python dict.

```python
from langchain_core.output_parsers import JsonOutputParser
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

parser = JsonOutputParser()

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a data extractor. Always respond with valid JSON only."),
    ("human", "Extract the name, age, and city from this text: {text}\n\n{format_instructions}")
]).partial(format_instructions=parser.get_format_instructions())

llm   = ChatOpenAI(model="gpt-4o")
chain = prompt | llm | parser

result = chain.invoke({"text": "Riya is a 24-year-old doctor from Chennai."})
print(result)
# {'name': 'Riya', 'age': 24, 'city': 'Chennai'}
print(type(result))   # <class 'dict'>
print(result["name"]) # Riya
```

**Imports explained:**
- `JsonOutputParser` — a parser that expects the model's output to be a JSON string; calls `json.loads()` on the output and returns a Python dict

**What this code does:**
- `parser.get_format_instructions()` — generates a text instruction like "Respond with a JSON object..." that gets injected into the prompt via `{format_instructions}`
- `.partial(format_instructions=...)` — pre-fills the `format_instructions` variable so you don't pass it on every invoke
- The parser converts the model's JSON string response into a real Python dict

---

### 6.3 StructuredOutputParser

Uses `ResponseSchema` to define exactly which fields to extract. Injects formatting instructions into the prompt.

```python
from langchain.output_parsers import StructuredOutputParser, ResponseSchema
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

schemas = [
    ResponseSchema(name="movie_name",   description="The name of the movie"),
    ResponseSchema(name="release_year", description="Year the movie was released"),
    ResponseSchema(name="genre",        description="Primary genre of the movie"),
    ResponseSchema(name="director",     description="Name of the director"),
]

parser = StructuredOutputParser.from_response_schemas(schemas)
format_instructions = parser.get_format_instructions()

prompt = ChatPromptTemplate.from_template(
    "Extract movie information from the following text.\n{format_instructions}\n\nText: {text}"
)

llm   = ChatOpenAI(model="gpt-4o")
chain = prompt | llm | parser

result = chain.invoke({
    "text": "Interstellar is a 2014 science fiction film directed by Christopher Nolan.",
    "format_instructions": format_instructions
})

print(result)
# {'movie_name': 'Interstellar', 'release_year': '2014', 'genre': 'Science Fiction', 'director': 'Christopher Nolan'}
print(result["director"])  # Christopher Nolan
```

**Imports explained:**
- `StructuredOutputParser` — a parser that expects specific named fields in the model's output; generates detailed formatting instructions and extracts the values into a dict
- `ResponseSchema` — defines a single field by name and description; a list of these becomes the full schema

**What this code does:**
- `ResponseSchema(name="movie_name", description="...")` — defines one field the model should return
- `parser.get_format_instructions()` — generates a prompt snippet like "Return a markdown JSON block with keys: movie_name, release_year..." that teaches the model the expected format
- After the model responds with a formatted JSON block, the parser extracts all values into a Python dict

---

### 6.4 PydanticOutputParser

Parses LLM output directly into a validated **Pydantic model object**.

```python
from langchain.output_parsers import PydanticOutputParser
from pydantic import BaseModel, Field
from typing import List
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

class BookInfo(BaseModel):
    title:  str       = Field(description="Title of the book")
    author: str       = Field(description="Author's full name")
    year:   int       = Field(description="Year of publication")
    themes: List[str] = Field(description="List of main themes in the book")

parser = PydanticOutputParser(pydantic_object=BookInfo)

prompt = ChatPromptTemplate.from_template(
    "Extract book information from this text.\n{format_instructions}\n\nText: {text}"
)

llm   = ChatOpenAI(model="gpt-4o")
chain = prompt | llm | parser

result = chain.invoke({
    "text": "To Kill a Mockingbird was written by Harper Lee and published in 1960. "
            "It deals with themes of racial injustice, moral growth, and compassion.",
    "format_instructions": parser.get_format_instructions()
})

print(type(result))        # <class '__main__.BookInfo'>  — a real Pydantic object
print(result.title)        # To Kill a Mockingbird
print(result.author)       # Harper Lee
print(result.year)         # 1960
print(result.themes)       # ['racial injustice', 'moral growth', 'compassion']
print(result.model_dump())
```

**Imports explained:**
- `PydanticOutputParser` — a parser that takes a Pydantic model class, prompts the model to return JSON matching that schema, then validates and constructs a real Pydantic object from the response

**What this code does:**
- `PydanticOutputParser(pydantic_object=BookInfo)` — links the parser to the `BookInfo` schema
- `parser.get_format_instructions()` — generates instructions telling the model exactly what JSON structure to return
- After the model responds with JSON, the parser validates it against `BookInfo` and returns a real `BookInfo` Pydantic object
- You get dot-notation access (`result.title`), Pydantic validation, and `.model_dump()` for serialization

> `PydanticOutputParser` gives you a typed Python object with full Pydantic validation and dot-notation access.
