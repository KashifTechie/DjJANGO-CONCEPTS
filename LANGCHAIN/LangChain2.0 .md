# 🦜🔗 LangChain — Complete Course Notes

> **Framework:** LangChain (Open Source) | **Updated:** January–April 2025
>
> A comprehensive guide covering every concept with detailed explanations and working code examples.

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

> **Why does this matter?** Chat models understand conversational structure — the `system` role lets you define the AI's persona, `human` is the user's turn, and `assistant` is the model's previous responses. LLMs just see a blob of text and have no concept of roles.

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

---

### 3.3 Open Source Models

Open-source language models are freely available AI models that can be downloaded, modified, fine-tuned, and deployed without restrictions. Unlike closed-source models (GPT-4, Claude, Gemini), open-source models give you **full control**.

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

**Disadvantages of open-source models:** require significant compute resources, may need fine-tuning, generally lag behind frontier closed-source models on benchmarks.

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

#### Dynamic Prompt (Templated — Preferred)

```python
paper = "BERT: Pre-training of Deep Bidirectional Transformers"
style = "beginner-friendly"

prompt_text = f"Summarize {paper} in a {style} way in 3 sentences."
response = llm.invoke([HumanMessage(content=prompt_text)])
# Better, but still a plain f-string — no validation
```

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
# Summarize the research paper titled "Attention Is All You Need" ...

from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke(filled_prompt)
print(response.content)
```

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

> By passing the full conversation history as `messages`, the model remembers the context of earlier turns — this is how chatbots maintain state.

---

### 4.4 ChatPromptTemplate

`ChatPromptTemplate` constructs message-based prompts for chat models. It produces a **list of typed messages** rather than a plain string.

```python
from langchain.prompts import ChatPromptTemplate

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

llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke(messages)
print(response.content)

# Use directly in a chain with the | operator
chain = chat_prompt | llm | StrOutputParser()
result = chain.invoke({"domain": "astronomy", "concept": "black holes"})
```

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

> Without `MessagesPlaceholder`, you'd have to manually concatenate messages — error-prone and messy. The placeholder makes history injection clean and declarative.

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
# {'summary': '...', 'sentiment': 'positive', 'score': 8}
print(result.model_dump_json(indent=2))
```

---

### 5.2 TypedDict

`TypedDict` defines a dictionary with specific keys and value types. Lighter than Pydantic — good for quick, simple schemas.

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
print(result["name"])    # John — access with [] like a dict
```

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

> **Important:** TypedDict does **not** validate data at runtime. If the model returns `age: "twenty-eight"` instead of `age: 28`, TypedDict won't catch it. Use Pydantic for runtime validation.

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

#### Default Values

```python
class Config(BaseModel):
    model:       str   = "gpt-4o"   # default value
    temperature: float = 0.7
    max_tokens:  int   = 1000
    verbose:     bool  = False

c  = Config()                            # Use all defaults
c2 = Config(model="gpt-3.5-turbo", temperature=0.0)  # Override some
print(c2.max_tokens)  # 1000 (from default)
```

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
print(result.headline)     # "Modi Inaugurates New Mumbai Metro Line"
print(result.key_people)   # ["Modi", "Maharashtra Chief Minister"]
print(result.location)     # "Mumbai"
print(result.sentiment)    # "positive"

print(result.model_dump())
# {'headline': '...', 'key_people': [...], 'location': 'Mumbai', 'sentiment': 'positive'}
```

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

> **Key difference from `with_structured_output`:** Output parsers work by prompting the model to follow a format (less reliable) and then parsing the text. `with_structured_output` uses model-native function calling (more reliable). Prefer `with_structured_output` when possible.

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

> Use `StrOutputParser` in almost every basic chain — it makes working with the output much easier since you get a string instead of an `AIMessage`.

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

---

### 6.3 StructuredOutputParser

Uses `ResponseSchema` to define exactly which fields to extract. Injects formatting instructions into the prompt.

```python
from langchain.output_parsers import StructuredOutputParser, ResponseSchema
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

# Define the schema — what fields you want
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
print(result.model_dump()) # {'title': '...', 'author': '...', 'year': 1960, 'themes': [...]}
```

> `PydanticOutputParser` gives you a typed Python object with Pydantic validation, which means you can call `.model_dump()`, `.model_dump_json()`, and access fields with dot notation.

---

## 7. Chains

### What & Why

A **Chain** is a sequence of steps where the output of one step automatically becomes the input to the next. Chains let you compose modular AI pipelines without writing loops and glue code manually.

Think of it like a conveyor belt: `Raw material → Station 1 → Station 2 → Station 3 → Finished product`

---

### 7.1 Simple Chain

The most basic chain: `Prompt → LLM → Parser`.

```python
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("human", "What is {topic}? Explain in 2 sentences.")
])
llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

# Chain them together with the | operator
chain  = prompt | llm | parser

result = chain.invoke({"topic": "neural networks"})
print(result)
# "Neural networks are computational models inspired by the human brain..."
```

---

### 7.2 Sequential Chain

Multiple chains executed in order — the output of one becomes the input of the next.

```python
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

# Chain 1: Generate a blog title
title_prompt = ChatPromptTemplate.from_template(
    "Generate a catchy blog post title about: {topic}"
)
title_chain = title_prompt | llm | parser

# Chain 2: Write an intro paragraph for that title
intro_prompt = ChatPromptTemplate.from_template(
    "Write an engaging introduction paragraph for a blog post titled: {title}"
)
intro_chain = intro_prompt | llm | parser

# Sequential chain: title_chain output feeds into intro_chain
full_chain = (
    title_chain
    | (lambda title: {"title": title})   # rename key for next chain
    | intro_chain
)

result = full_chain.invoke({"topic": "the future of AI in education"})
print(result)
# "In an era where technology is reshaping every industry..."
```

---

### 7.3 Parallel Chain

Multiple chains run **simultaneously** on the same input, producing a combined dict of results.

```python
from langchain_core.runnables import RunnableParallel
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

pros_chain = (
    ChatPromptTemplate.from_template("List 3 pros of {technology}") | llm | parser
)
cons_chain = (
    ChatPromptTemplate.from_template("List 3 cons of {technology}") | llm | parser
)

# Run both in parallel — same input, two separate outputs
parallel = RunnableParallel(pros=pros_chain, cons=cons_chain)

result = parallel.invoke({"technology": "blockchain"})
print(result["pros"])   # "1. Decentralized... 2. Transparent... 3. Secure..."
print(result["cons"])   # "1. Slow... 2. Energy intensive... 3. Complex..."
```

---

### 7.4 Conditional Chain

Routes input to different chains based on a condition — like `if/elif/else` for AI pipelines.

```python
from langchain_core.runnables import RunnableBranch
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

positive_chain = (
    ChatPromptTemplate.from_template("The user left positive feedback: '{text}'. Write a warm thank-you reply.")
    | llm | parser
)
negative_chain = (
    ChatPromptTemplate.from_template("The user left negative feedback: '{text}'. Write an empathetic apology and offer a solution.")
    | llm | parser
)
neutral_chain = (
    ChatPromptTemplate.from_template("The user left neutral feedback: '{text}'. Write a professional acknowledgement.")
    | llm | parser
)

branch = RunnableBranch(
    (lambda x: "great" in x["text"].lower() or "love" in x["text"].lower(), positive_chain),
    (lambda x: "bad"   in x["text"].lower() or "terrible" in x["text"].lower(), negative_chain),
    neutral_chain  # default fallback
)

print(branch.invoke({"text": "The product is great, loved it!"}))
# "Thank you so much for your wonderful feedback!..."

print(branch.invoke({"text": "Terrible experience, very bad quality."}))
# "We're truly sorry to hear about your experience..."
```

---

## 8. Runnables

### The Why

As pipelines grow in complexity, you need a **uniform protocol** for every component. Without it, connecting a custom function to an LLM to a retriever requires custom glue code each time. Runnables solve this by providing a standardized interface for every LangChain component.

### 8.1 The Runnable Interface

Every Runnable exposes these methods:

```python
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain.prompts import ChatPromptTemplate

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()
chain  = ChatPromptTemplate.from_template("Name the capital of {country}") | llm | parser

# invoke — single input
print(chain.invoke({"country": "Japan"}))
# "Tokyo"

# batch — multiple inputs at once (runs in parallel threads)
results = chain.batch([
    {"country": "Japan"},
    {"country": "Brazil"},
    {"country": "India"}
])
print(results)  # ["Tokyo", "Brasília", "New Delhi"]

# stream — token by token output
for token in chain.stream({"country": "Japan"}):
    print(token, end="", flush=True)
# T o k y o  (printed progressively as model generates)
print()

# Async versions (use in async functions / FastAPI etc.)
import asyncio

async def async_example():
    result = await chain.ainvoke({"country": "Japan"})
    print(result)

    async for token in chain.astream({"country": "Japan"}):
        print(token, end="", flush=True)

asyncio.run(async_example())
```

---

## 9. LCEL (LangChain Expression Language)

### Overview

LCEL is the declarative syntax for composing Runnables using the **`|` (pipe) operator**, inspired by Unix shell piping. The `|` operator chains runnables so the output of the left side becomes the input of the right side.

```python
# Unix shell analogy:
# cat file.txt | grep "error" | sort | uniq

# LCEL equivalent:
chain  = prompt | llm | parser
result = chain.invoke({"question": "..."})
```

---

### 9.1 RunnableSequence

Executes runnables **one after another**. Using `|` between runnables automatically creates a `RunnableSequence`.

```python
from langchain_core.runnables import RunnableSequence
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_template("Translate to French: {text}")
llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

# These two are equivalent — | creates a RunnableSequence
chain_pipe     = prompt | llm | parser
chain_explicit = RunnableSequence(first=prompt, middle=[llm], last=parser)

result = chain_pipe.invoke({"text": "Hello, how are you?"})
print(result)  # "Bonjour, comment allez-vous ?"
```

---

### 9.2 RunnableParallel

Runs multiple runnables **simultaneously**, each receiving the same input. Returns a dictionary of outputs.

```python
from langchain_core.runnables import RunnableParallel
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

summary_chain   = ChatPromptTemplate.from_template("Summarize in 1 sentence: {text}")  | llm | parser
keywords_chain  = ChatPromptTemplate.from_template("Extract 5 keywords from: {text}")  | llm | parser
sentiment_chain = ChatPromptTemplate.from_template("What is the sentiment of: {text}") | llm | parser

# Run all three in parallel
analysis = RunnableParallel(
    summary=summary_chain,
    keywords=keywords_chain,
    sentiment=sentiment_chain
)

text   = "LangChain is an amazing framework that simplifies building AI applications."
result = analysis.invoke({"text": text})

print(result["summary"])    # "LangChain simplifies AI application development."
print(result["keywords"])   # "LangChain, framework, AI, applications, simplifies"
print(result["sentiment"])  # "Positive"
```

---

### 9.3 RunnablePassthrough

**Returns the input unchanged** — passes data through without modification. Most useful for preserving the original input alongside transformed data.

```python
from langchain_core.runnables import RunnablePassthrough, RunnableParallel
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

# Preserve original question alongside the answer
chain  = RunnableParallel(
    question=RunnablePassthrough(),   # passes input unchanged
    answer=(
        ChatPromptTemplate.from_template("Answer briefly: {question}")
        | llm | parser
    )
)

result = chain.invoke("What is 2 + 2?")
print(result["question"])  # "What is 2 + 2?"   <- original, unchanged
print(result["answer"])    # "4"

# Most common use in RAG: pass the question through alongside retrieved context
rag_parallel = RunnableParallel(
    context=retriever,               # retrieves docs based on question
    question=RunnablePassthrough()   # keeps original question for the prompt
)
```

---

### 9.4 RunnableLambda

Wraps **any custom Python function** as a Runnable, making it composable with `|`.

```python
from langchain_core.runnables import RunnableLambda
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

def preprocess(text: str) -> str:
    """Clean and standardize input."""
    return text.strip().lower()[:500]

def postprocess(text: str) -> str:
    """Add formatting to output."""
    return f"=== ANSWER ===\n{text}\n============="

pre  = RunnableLambda(preprocess)
post = RunnableLambda(postprocess)

chain = (
    pre
    | ChatPromptTemplate.from_template("Explain {question} briefly.")
    | llm
    | parser
    | post
)

result = chain.invoke("  What Is Python?  ")
print(result)
# === ANSWER ===
# Python is a high-level, interpreted programming language...
# =============
```

---

### 9.5 RunnableBranch

Routes input to **different chains based on conditions**. Like a switch/if-else for runnables.

```python
from langchain_core.runnables import RunnableBranch
from langchain.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm    = ChatOpenAI(model="gpt-4o")
parser = StrOutputParser()

math_chain = (
    ChatPromptTemplate.from_template("You are a math tutor. Answer: {question}") | llm | parser
)
history_chain = (
    ChatPromptTemplate.from_template("You are a history expert. Answer: {question}") | llm | parser
)
general_chain = (
    ChatPromptTemplate.from_template("You are a helpful assistant. Answer: {question}") | llm | parser
)

router = RunnableBranch(
    (lambda x: any(w in x["question"].lower() for w in ["calculate", "equation", "math", "solve"]),
     math_chain),
    (lambda x: any(w in x["question"].lower() for w in ["history", "war", "ancient", "century"]),
     history_chain),
    general_chain  # default
)

print(router.invoke({"question": "Solve 3x + 5 = 20"}))
# Uses math_chain → "Solving 3x + 5 = 20: 3x = 15, x = 5."

print(router.invoke({"question": "When did World War II end?"}))
# Uses history_chain → "World War II ended in 1945..."
```

---

## 10. Document Loaders

### What is a Document Object?

Before loading data, understand what LangChain stores it in:

```python
from langchain_core.documents import Document

doc = Document(
    page_content="This is the text content of the document.",
    metadata={"source": "file.pdf", "page": 1, "author": "Nitish"}
)
print(doc.page_content)  # The actual text
print(doc.metadata)      # Dict of metadata — source, page, etc.
```

All document loaders produce a list of `Document` objects following this structure.

---

### 10.1 TextLoader

Reads **plain `.txt` files** and converts them into `Document` objects.

```python
from langchain_community.document_loaders import TextLoader

loader = TextLoader("data/notes.txt", encoding="utf-8")
docs   = loader.load()

print(len(docs))                    # 1 — one Document for the whole file
print(docs[0].page_content[:200])   # First 200 chars of the text
print(docs[0].metadata)             # {'source': 'data/notes.txt'}
```

**Limitation:** Works only with `.txt` files.

---

### 10.2 PyPDFLoader

Loads **PDF files** — one `Document` per page.

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("documents/research_paper.pdf")
docs   = loader.load()

print(len(docs))                # Number of pages in the PDF
print(docs[0].page_content)     # Text of page 1
print(docs[0].metadata)         # {'source': 'research_paper.pdf', 'page': 0}

# Iterate through pages
for i, doc in enumerate(docs):
    print(f"Page {i+1}: {len(doc.page_content)} characters")
```

**Limitation:** Uses PyPDF under the hood — poor with scanned PDFs or complex multi-column layouts.

---

### 10.3 DirectoryLoader

Loads **all matching files from a directory**.

```python
from langchain_community.document_loaders import DirectoryLoader, TextLoader, PyPDFLoader

# Load all .txt files from a folder (including subfolders)
txt_loader = DirectoryLoader(
    path="./knowledge_base/",
    glob="**/*.txt",
    loader_cls=TextLoader,
    show_progress=True
)
docs = txt_loader.load()
print(f"Loaded {len(docs)} text documents")

# Load all PDFs from a directory
pdf_loader = DirectoryLoader(
    path="./pdfs/",
    glob="**/*.pdf",
    loader_cls=PyPDFLoader
)
pdf_docs = pdf_loader.load()
print(f"Loaded {len(pdf_docs)} PDF pages")
```

---

### 10.4 WebBaseLoader

Scrapes and loads **web pages** as documents using BeautifulSoup.

```python
from langchain_community.document_loaders import WebBaseLoader

# Load a single URL
loader = WebBaseLoader("https://en.wikipedia.org/wiki/LangChain")
docs   = loader.load()

print(docs[0].page_content[:500])    # Extracted text from the webpage
print(docs[0].metadata["source"])    # The URL

# Load multiple URLs at once
loader = WebBaseLoader([
    "https://docs.langchain.com/docs/",
    "https://python.langchain.com/docs/introduction/",
])
docs = loader.load()
print(f"Loaded {len(docs)} pages")
```

**Limitation:** Doesn't handle JavaScript-rendered content (use `SeleniumURLLoader` for SPAs).

---

### 10.5 CSVLoader

Loads **CSV files** — one `Document` per row by default.

```python
from langchain_community.document_loaders import CSVLoader

loader = CSVLoader(
    file_path="products.csv",
    source_column="product_id"   # Use this column as the source metadata
)
docs = loader.load()

print(len(docs))               # Number of rows in the CSV
print(docs[0].page_content)    # "product_id: P001\nname: Widget\nprice: 29.99"
print(docs[0].metadata)        # {'source': 'P001', 'row': 0}
```

---

### 10.6 Load vs Lazy Load

```python
from langchain_community.document_loaders import TextLoader

loader = TextLoader("large_file.txt")

# load() — loads ALL documents into memory at once
docs = loader.load()              # Returns a list — all in RAM at the same time

# lazy_load() — generator, loads one at a time — memory efficient for large files
for doc in loader.lazy_load():
    process(doc)                  # Process one doc, then discard it from memory
```

Use `lazy_load()` when loading thousands of files or very large documents where loading everything at once would exhaust RAM.

---

## 11. Text Splitters

### What is Text Splitting?

Text splitting breaks large documents into **small, overlapping chunks** that fit within an LLM's context window and improve retrieval precision.

**Two key parameters:**
- `chunk_size` — Maximum number of characters per chunk
- `chunk_overlap` — Characters shared between consecutive chunks (prevents cutting context mid-thought)

```
Without overlap:  [......chunk 1......] [......chunk 2......]
With overlap:     [......chunk 1......] 
                             [......chunk 2......]
                       ↑ overlap ↑  (shared text keeps context intact)
```

---

### 11.1 CharacterTextSplitter (Length-Based)

Splits by character count using a defined separator.

```python
from langchain.text_splitter import CharacterTextSplitter

text = """LangChain is an open-source framework for developing applications powered by 
large language models. It provides modular components and end-to-end tools.

LangChain supports all major LLMs including OpenAI, Anthropic, Google, and HuggingFace.
The framework makes it easy to build chatbots, RAG systems, and AI agents."""

splitter = CharacterTextSplitter(
    separator="\n\n",   # Split on paragraph breaks first
    chunk_size=200,      # Max 200 characters per chunk
    chunk_overlap=20     # 20-char overlap between consecutive chunks
)

chunks = splitter.split_text(text)
print(f"Number of chunks: {len(chunks)}")

for i, chunk in enumerate(chunks):
    print(f"\n--- Chunk {i+1} ({len(chunk)} chars) ---")
    print(chunk)
```

---

### 11.2 RecursiveCharacterTextSplitter (Most Common)

Tries a hierarchy of separators in order: `\n\n` → `\n` → `. ` → ` ` → `""`, choosing the one that produces the best splits while respecting natural text boundaries.

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import PyPDFLoader

long_text = """
# Introduction to Neural Networks

Neural networks are computational models inspired by the human brain. They consist 
of layers of interconnected nodes called neurons.

## How They Work

Each neuron receives inputs, applies a weight, and passes the result through an 
activation function. The network learns by adjusting weights through backpropagation.

## Applications

Neural networks are used in image recognition, natural language processing, 
speech recognition, and many other fields.
"""

splitter = RecursiveCharacterTextSplitter(
    chunk_size=200,
    chunk_overlap=30,
    separators=["\n\n", "\n", ". ", " ", ""]  # Tries these in order
)

chunks = splitter.split_text(long_text)
print(f"Chunks: {len(chunks)}")
for i, c in enumerate(chunks):
    print(f"\nChunk {i+1}: '{c[:80]}...'")

# Works directly on Document objects too — preserves metadata!
loader    = PyPDFLoader("document.pdf")
docs      = loader.load()
doc_chunks = splitter.split_documents(docs)
print(doc_chunks[0].metadata)   # {'source': 'document.pdf', 'page': 0}
```

> `RecursiveCharacterTextSplitter` is the **go-to default** for most use cases. It respects natural text boundaries and produces coherent chunks.

---

### 11.3 MarkdownHeaderTextSplitter (Document-Structure Based)

Uses inherent document structure (Markdown headers) as split points — chunks carry header metadata.

```python
from langchain.text_splitter import MarkdownHeaderTextSplitter

markdown_text = """
# Chapter 1: Introduction
LangChain is a powerful framework.

## Section 1.1: What is LangChain?
LangChain helps build LLM applications.

## Section 1.2: Why LangChain?
It provides modular components.

# Chapter 2: Components
LangChain has many components.

## Section 2.1: Models
Models are the core interface.
"""

headers_to_split_on = [
    ("#",  "Chapter"),
    ("##", "Section"),
]

splitter = MarkdownHeaderTextSplitter(headers_to_split_on=headers_to_split_on)
chunks   = splitter.split_text(markdown_text)

for chunk in chunks:
    print(chunk.page_content)
    print(chunk.metadata)
    # {'Chapter': 'Chapter 1: Introduction', 'Section': 'Section 1.1: What is LangChain?'}
    print("---")
```

---

### 11.4 Python Code Splitter

Splits code intelligently — keeps functions and classes together.

```python
from langchain.text_splitter import Language, RecursiveCharacterTextSplitter

python_code = """
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

class Calculator:
    def multiply(self, a, b):
        return a * b
    
    def divide(self, a, b):
        if b == 0:
            raise ValueError("Cannot divide by zero")
        return a / b
"""

splitter = RecursiveCharacterTextSplitter.from_language(
    language=Language.PYTHON,
    chunk_size=100,
    chunk_overlap=0
)
chunks = splitter.split_text(python_code)
for chunk in chunks:
    print(f"---\n{chunk}")
```

---

### 11.5 SemanticChunker (Semantic Meaning-Based)

Splits based on **topic shifts** detected via embedding similarity — most intelligent but slowest.

```python
from langchain_experimental.text_splitter import SemanticChunker
from langchain_openai import OpenAIEmbeddings

text = """
Farmers were working hard in the fields, preparing the soil and planting seeds for 
the next season. The sun was bright, and the air smelled of earth and fresh grass.

The Indian Premier League (IPL) is the biggest cricket league in the world. People 
all over the world watch the matches and cheer for their favourite teams.

Terrorism is a big danger to peace and safety. It causes harm to people and creates 
fear in cities and villages.
"""

# Uses embeddings to detect where the topic changes
splitter = SemanticChunker(
    embeddings=OpenAIEmbeddings(),
    breakpoint_threshold_type="percentile",
    breakpoint_threshold_amount=95
)

chunks = splitter.split_text(text)
print(f"Chunks: {len(chunks)}")  # Should be 3 — one per topic
for i, chunk in enumerate(chunks):
    print(f"\nChunk {i+1}: {chunk[:100]}...")
```

---

## 12. Vector Stores

### Why Vector Stores?

Traditional databases match exact keywords. Vector stores enable **semantic search** — finding content by meaning, not just keywords.

```
Query:   "what is the boiling point of water"
Matches: "water boils at 100°C"   <- semantically similar, zero keyword overlap
```

### What are Vector Stores?

A vector store stores and retrieves **numerical vectors (embeddings)** with four key capabilities: Storage, Similarity Search, Indexing, and CRUD operations.

---

### 12.1 Vector Store vs Vector Database

| | Vector Store (e.g., FAISS) | Vector Database (e.g., Qdrant) |
|---|---|---|
| **Scale** | Small-medium, in-memory | Large-scale, distributed |
| **Persistence** | Manual (save/load files) | Built-in, automatic |
| **Features** | Core similarity search | Auth, replication, filtering, backup |
| **Best For** | Prototyping, notebooks | Production systems |

> A **vector database** is effectively a vector store with extra database features (clustering, scaling, security, metadata filtering, durability).

---

### 12.2 FAISS Vector Store

FAISS (Facebook AI Similarity Search) — fast, in-memory. Great for local development.

```python
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings
from langchain_core.documents import Document

embeddings = OpenAIEmbeddings(api_key="YOUR_KEY")

# Create from a list of plain texts
texts = [
    "LangChain is an open-source framework for LLM applications.",
    "Python is a popular programming language for data science.",
    "RAG combines retrieval with text generation.",
    "Neural networks are inspired by the human brain.",
    "Vector stores enable semantic search over documents.",
]

vectorstore = FAISS.from_texts(texts, embedding=embeddings)

# Similarity search — returns top-k most similar documents
results = vectorstore.similarity_search("How do I build an AI app?", k=2)
for doc in results:
    print(doc.page_content)
# "LangChain is an open-source framework for LLM applications."
# "RAG combines retrieval with text generation."

# Search with relevance scores (lower score = more similar in FAISS)
results_with_scores = vectorstore.similarity_search_with_score("AI app framework", k=2)
for doc, score in results_with_scores:
    print(f"Score: {score:.4f} | {doc.page_content}")

# Save to disk
vectorstore.save_local("faiss_index")

# Reload later
loaded_vs = FAISS.load_local(
    "faiss_index", embeddings, allow_dangerous_deserialization=True
)
```

---

### 12.3 Chroma Vector Store

Chroma — lightweight, open-source, persists to disk automatically.

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_core.documents import Document

embeddings = OpenAIEmbeddings(api_key="YOUR_KEY")

# Load, split, embed in one pipeline
loader   = PyPDFLoader("document.pdf")
docs     = loader.load()
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks   = splitter.split_documents(docs)

# Create and persist Chroma vector store
vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    persist_directory="./chroma_db"   # Automatically saved to disk
)

# Similarity search
results = vectorstore.similarity_search("What is the main topic?", k=3)
for r in results:
    print(r.page_content[:150])

# Reload an existing store
vectorstore = Chroma(
    persist_directory="./chroma_db",
    embedding_function=embeddings
)

# Add more documents later
new_docs = [Document(page_content="New information added later", metadata={"source": "update"})]
vectorstore.add_documents(new_docs)
```

---

### 12.4 Full Pipeline: Documents → Vector Store

```python
from langchain_community.document_loaders import WebBaseLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma

# 1. Load
loader = WebBaseLoader("https://en.wikipedia.org/wiki/Artificial_intelligence")
docs   = loader.load()
print(f"Loaded {len(docs)} pages, {sum(len(d.page_content) for d in docs)} total chars")

# 2. Split
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks   = splitter.split_documents(docs)
print(f"Split into {len(chunks)} chunks")

# 3. Embed and Store
embeddings  = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(chunks, embeddings)
print(f"Vector store created with {vectorstore._collection.count()} vectors")

# 4. Search
query   = "Who invented artificial intelligence?"
results = vectorstore.similarity_search(query, k=3)
for i, r in enumerate(results):
    print(f"\nResult {i+1}: {r.page_content[:200]}")
```

---

## 13. Retrievers

### What are Retrievers?

A **retriever** is a component that fetches relevant documents from a data source given a query. All retrievers in LangChain are **Runnables** — they support `.invoke()`, `.batch()`, `.stream()`.

```python
# Every retriever follows the same interface:
docs = retriever.invoke("What is machine learning?")
# Returns a list of Document objects
```

---

### 13.1 Vector Store Retriever

The most common retriever — wraps a vector store for similarity-based search.

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

vectorstore = Chroma(persist_directory="./chroma_db", embedding_function=OpenAIEmbeddings())

# Basic retriever — returns top 4 most similar chunks
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

docs = retriever.invoke("Explain neural networks")
print(f"Retrieved {len(docs)} documents")
for doc in docs:
    print(doc.page_content[:150])
    print("---")
```

---

### 13.2 Wikipedia Retriever

Queries the Wikipedia API — no vector store needed.

```python
from langchain_community.retrievers import WikipediaRetriever

retriever = WikipediaRetriever(
    top_k_results=3,
    doc_content_chars_max=1000
)

docs = retriever.invoke("What is quantum computing?")
for doc in docs:
    print(f"Source: {doc.metadata.get('title', 'N/A')}")
    print(doc.page_content[:200])
    print("---")
```

---

### 13.3 MMR (Maximal Marginal Relevance)

MMR reduces redundancy in results by balancing **relevance** (documents should match the query) with **diversity** (documents should not repeat each other).

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

vectorstore = Chroma(persist_directory="./chroma_db", embedding_function=OpenAIEmbeddings())

mmr_retriever = vectorstore.as_retriever(
    search_type="mmr",
    search_kwargs={
        "k": 5,              # Return 5 final documents
        "fetch_k": 20,       # First fetch 20 candidates, then re-rank with MMR
        "lambda_mult": 0.5   # 0 = max diversity, 1 = max relevance, 0.5 = balanced
    }
)

docs = mmr_retriever.invoke("Tell me about LangChain features")
# Results are diverse — no near-duplicate chunks

# Compare with standard retriever (may return redundant chunks):
standard_retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
```

---

### 13.4 Multi-Query Retriever

Uses an LLM to generate **multiple variations** of the user's query, retrieves documents for each, then deduplicates — capturing documents a single query might miss.

```python
from langchain.retrievers.multi_query import MultiQueryRetriever
from langchain_openai import ChatOpenAI
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings
import logging

# Enable logging to see what queries get generated
logging.basicConfig()
logging.getLogger("langchain.retrievers.multi_query").setLevel(logging.INFO)

vectorstore = Chroma(persist_directory="./chroma_db", embedding_function=OpenAIEmbeddings())
llm         = ChatOpenAI(model="gpt-4o")

retriever = MultiQueryRetriever.from_llm(
    retriever=vectorstore.as_retriever(search_kwargs={"k": 3}),
    llm=llm
)

# One query → multiple variations generated automatically:
# Original:  "What are LangChain agents?"
# Generated: ["How do autonomous agents work in LangChain?",
#             "Explain the agent framework in LangChain",
#             "LangChain agent tools and capabilities"]
docs = retriever.invoke("What are LangChain agents?")
print(f"Retrieved {len(docs)} unique documents")  # More than k=3 due to multi-query
```

---

### 13.5 Contextual Compression Retriever

After retrieving chunks, uses an LLM to **extract only the relevant portion** from each chunk — reducing noise in the context passed to the final LLM.

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor
from langchain_openai import ChatOpenAI
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

vectorstore    = Chroma(persist_directory="./chroma_db", embedding_function=OpenAIEmbeddings())
base_retriever = vectorstore.as_retriever(search_kwargs={"k": 4})
llm            = ChatOpenAI(model="gpt-4o", temperature=0)

# The compressor extracts only relevant sentences from each chunk
compressor = LLMChainExtractor.from_llm(llm)

retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=base_retriever
)

query = "What is the memory component in LangChain?"
docs  = retriever.invoke(query)

# Each doc now contains ONLY sentences relevant to the query
for doc in docs:
    print(f"Compressed: {doc.page_content}")
    print("---")
```

---

## 14. RAG (Retrieval-Augmented Generation)

### What is RAG?

RAG combines a **retrieval system** with a **language model** to generate answers grounded in your own documents — not just the model's training data.

```
Without RAG: "What's in our Q3 2024 report?" → LLM guesses (hallucination risk)
With RAG:    "What's in our Q3 2024 report?" → Retrieve Q3 doc → LLM answers from it
```

### In-Context Learning

RAG works because of **in-context learning** — a core capability of LLMs where the model learns to solve a task purely by seeing examples or context in the prompt, without updating its weights. You give the model relevant context → the model uses it to answer correctly.

### The 4 Stages of RAG

```
╔══════════════════════════════════════════════════════╗
║              OFFLINE INDEXING (done once)            ║
║  Documents → Load → Split → Embed → Store in Vector DB ║
╚══════════════════════════════════════════════════════╝
                          ↓
╔══════════════════════════════════════════════════════╗
║           ONLINE QUERYING (per user request)         ║
║  Query → Embed → Retrieve → Augment → LLM → Answer   ║
╚══════════════════════════════════════════════════════╝
```

#### Stage 1: Indexing

```python
# Step 1: Document Ingestion
from langchain_community.document_loaders import PyPDFLoader
loader   = PyPDFLoader("knowledge_base.pdf")
raw_docs = loader.load()

# Step 2: Text Chunking
from langchain.text_splitter import RecursiveCharacterTextSplitter
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks   = splitter.split_documents(raw_docs)

# Step 3: Embedding Generation + Step 4: Storage
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
embeddings  = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(chunks, embeddings, persist_directory="./db")
```

#### Stage 2: Retrieval

```python
retriever       = vectorstore.as_retriever(search_kwargs={"k": 4})
relevant_chunks = retriever.invoke("What are the Q3 revenue figures?")
```

#### Stage 3: Augmentation

```python
context = "\n\n".join([doc.page_content for doc in relevant_chunks])

augmented_prompt = f"""Use ONLY the following context to answer the question.
If the answer is not in the context, say "I don't know."

Context:
{context}

Question: What are the Q3 revenue figures?
Answer:"""
```

#### Stage 4: Generation

```python
from langchain_openai import ChatOpenAI
llm      = ChatOpenAI(model="gpt-4o")
response = llm.invoke(augmented_prompt)
print(response.content)
```

---

## 15. RAG using LangChain

### 15.1 Complete RAG Pipeline

```python
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_chroma import Chroma
from langchain.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

# ── INDEXING ──────────────────────────────────────────────────
loader     = PyPDFLoader("document.pdf")
docs       = loader.load()
splitter   = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks     = splitter.split_documents(docs)
embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(chunks, embeddings)
retriever   = vectorstore.as_retriever(search_kwargs={"k": 4})

# ── RAG PROMPT ────────────────────────────────────────────────
prompt = ChatPromptTemplate.from_template("""
You are an assistant for question-answering tasks. 
Use ONLY the following context to answer the question.
If you don't know the answer, say "I don't know."

Context:
{context}

Question: {question}

Answer:
""")

llm = ChatOpenAI(model="gpt-4o", temperature=0)

def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

# ── RAG CHAIN ─────────────────────────────────────────────────
rag_chain = (
    {
        "context":  retriever | format_docs,   # retrieve docs and format as text
        "question": RunnablePassthrough()       # pass question through unchanged
    }
    | prompt
    | llm
    | StrOutputParser()
)

# ── QUERY ─────────────────────────────────────────────────────
answer = rag_chain.invoke("What is the main topic of this document?")
print(answer)

# Stream the answer token by token
for token in rag_chain.stream("Summarize the key points"):
    print(token, end="", flush=True)
```

---

### 15.2 Advanced RAG Improvements

```python
# 1. Pre-Retrieval: Query Rewriting
query_rewrite_prompt = ChatPromptTemplate.from_template(
    "Rewrite this question to be more specific and search-friendly: {question}"
)
rewrite_chain = query_rewrite_prompt | llm | StrOutputParser()

better_query = rewrite_chain.invoke({"question": "tell me about langchain agents"})
print(better_query)
# "How do AI agents work in LangChain and what tools can they use?"

# 2. During Retrieval: Hybrid Search (keyword + semantic)
from langchain_community.retrievers import BM25Retriever
from langchain.retrievers import EnsembleRetriever

bm25_retriever     = BM25Retriever.from_documents(chunks)
bm25_retriever.k   = 4
semantic_retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

hybrid_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, semantic_retriever],
    weights=[0.4, 0.6]   # 40% keyword, 60% semantic
)

# 3. Generation with Citations
citation_prompt = ChatPromptTemplate.from_template("""
Answer the question using the context below. 
For each claim, cite the source document number like [1], [2], etc.

{context}

Question: {question}
""")

def format_docs_with_numbers(docs):
    return "\n\n".join(f"[{i+1}] {doc.page_content}" for i, doc in enumerate(docs))

citation_chain = (
    {"context": retriever | format_docs_with_numbers, "question": RunnablePassthrough()}
    | citation_prompt
    | llm
    | StrOutputParser()
)

result = citation_chain.invoke("What does LangChain support?")
print(result)
# "LangChain supports all major LLMs [1] and provides modular components [2]."
```

---

## 16. Tools

### What is a Tool?

A **tool** = a Python function + a name + a description packaged for the LLM to understand and call. Tools let the LLM interact with external systems — databases, APIs, file systems, the web.

```
Without tools: LLM can only generate text based on training data
With tools:    LLM can search the web, query databases, run code, call APIs
```

### How Tools Fit into the Agent Ecosystem

An **AI agent** is an LLM-powered system that can autonomously think, decide, and take actions using tools. Tools are the "hands" of an agent.

---

### 16.1 Built-in Tools

```python
# Wikipedia search
from langchain_community.tools import WikipediaQueryRun
from langchain_community.utilities import WikipediaAPIWrapper

wiki_tool = WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper(top_k_results=2))
result    = wiki_tool.invoke("LangChain AI framework")
print(result[:300])

# DuckDuckGo web search
from langchain_community.tools import DuckDuckGoSearchRun

search_tool = DuckDuckGoSearchRun()
result      = search_tool.invoke("Latest news about AI in 2025")
print(result)

# Python REPL — execute Python code
from langchain_experimental.tools import PythonREPLTool

repl_tool = PythonREPLTool()
result    = repl_tool.invoke("print(2 ** 10)")
print(result)  # "1024\n"

# Inspect any tool's metadata
print(wiki_tool.name)         # "wikipedia"
print(wiki_tool.description)  # "A wrapper around Wikipedia..."
print(wiki_tool.args)         # {"query": {"type": "string", ...}}
```

---

### 16.2 Custom Tools — `@tool` Decorator (Simplest)

```python
from langchain.tools import tool

@tool
def get_weather(city: str) -> str:
    """
    Returns the current weather for a given city.
    Use this tool when the user asks about weather conditions.
    """
    weather_data = {
        "Mumbai":    "32°C, Humid, Partly cloudy",
        "Delhi":     "28°C, Clear sky",
        "Bangalore": "24°C, Light rain",
    }
    return weather_data.get(city, f"Weather data not available for {city}")

# Use the tool directly
print(get_weather.invoke("Mumbai"))   # "32°C, Humid, Partly cloudy"
print(get_weather.name)               # "get_weather"
print(get_weather.description)        # The docstring above

@tool
def calculate_compound_interest(principal: float, rate: float, years: int) -> str:
    """
    Calculates compound interest.
    
    Args:
        principal: Initial investment amount
        rate: Annual interest rate as a percentage (e.g., 8 for 8%)
        years: Number of years to compound
    """
    amount   = principal * (1 + rate / 100) ** years
    interest = amount - principal
    return (f"Principal: {principal:,.2f}, "
            f"Final Amount: {amount:,.2f}, "
            f"Interest Earned: {interest:,.2f}")

print(calculate_compound_interest.invoke({"principal": 100000, "rate": 8, "years": 10}))
# "Principal: 100,000.00, Final Amount: 215,892.50, Interest Earned: 115,892.50"
```

---

### 16.3 Custom Tools — StructuredTool (Multiple Structured Inputs)

For tools with **multiple, structured inputs** defined via Pydantic.

```python
from langchain.tools import StructuredTool
from pydantic import BaseModel, Field

class FlightSearchInput(BaseModel):
    origin:      str = Field(description="Departure city (e.g., 'Mumbai')")
    destination: str = Field(description="Arrival city (e.g., 'Delhi')")
    date:        str = Field(description="Travel date in YYYY-MM-DD format")
    passengers:  int = Field(default=1, description="Number of passengers")

def search_flights(origin: str, destination: str, date: str, passengers: int = 1) -> str:
    """Searches for available flights between two cities."""
    return f"Found 3 flights from {origin} to {destination} on {date} for {passengers} passenger(s). Cheapest: Rs.4,500"

flight_tool = StructuredTool.from_function(
    func=search_flights,
    name="search_flights",
    description="Search for flights between two cities on a specific date",
    args_schema=FlightSearchInput
)

result = flight_tool.invoke({
    "origin": "Mumbai", "destination": "Delhi",
    "date": "2025-06-15", "passengers": 2
})
print(result)
# "Found 3 flights from Mumbai to Delhi on 2025-06-15 for 2 passenger(s). Cheapest: Rs.4,500"
```

---

### 16.4 Custom Tools — BaseTool (Full Control)

```python
from langchain.tools import BaseTool
from typing import Type
from pydantic import BaseModel

class StockPriceInput(BaseModel):
    ticker:   str
    currency: str = "USD"

class StockPriceTool(BaseTool):
    name:        str             = "stock_price"
    description: str             = "Get the current stock price for a given ticker symbol."
    args_schema: Type[BaseModel] = StockPriceInput

    def _run(self, ticker: str, currency: str = "USD") -> str:
        mock_prices = {"AAPL": 195.50, "GOOGL": 175.20, "MSFT": 415.80}
        price = mock_prices.get(ticker.upper())
        if price is None:
            return f"Price not found for {ticker}"
        return f"{ticker.upper()}: {price} {currency}"

    async def _arun(self, ticker: str, currency: str = "USD") -> str:
        return self._run(ticker, currency)

stock_tool = StockPriceTool()
print(stock_tool.invoke({"ticker": "AAPL"}))          # "AAPL: 195.50 USD"
print(stock_tool.invoke({"ticker": "AAPL", "currency": "EUR"}))  # "AAPL: 195.50 EUR"
```

---

### 16.5 Toolkits

A **toolkit** bundles related tools together.

```python
from langchain_community.agent_toolkits import FileManagementToolkit

toolkit = FileManagementToolkit(root_dir="/tmp/workspace")
tools   = toolkit.get_tools()

for tool in tools:
    print(f"{tool.name}: {tool.description[:60]}")
# write_file:      Write file to disk
# read_file:       Read file from disk
# list_directory:  List directory contents
# ...

# Use individual tools
write_tool = next(t for t in tools if t.name == "write_file")
write_tool.invoke({"file_path": "hello.txt", "text": "Hello from LangChain!"})

read_tool  = next(t for t in tools if t.name == "read_file")
content    = read_tool.invoke({"file_path": "hello.txt"})
print(content)  # "Hello from LangChain!"
```

---

## 17. Tool Calling

### 17.1 Tool Binding

Before the LLM can use a tool, you must **bind** it — this tells the model what tools are available and what their input schemas look like.

```python
from langchain_openai import ChatOpenAI
from langchain.tools import tool

@tool
def add(a: int, b: int) -> int:
    """Adds two numbers."""
    return a + b

@tool
def multiply(a: int, b: int) -> int:
    """Multiplies two numbers."""
    return a * b

@tool
def get_weather(city: str) -> str:
    """Gets the current weather for a city."""
    return f"The weather in {city} is sunny and 25°C."

llm              = ChatOpenAI(model="gpt-4o")
llm_with_tools   = llm.bind_tools([add, multiply, get_weather])

# When asked about math, the model returns tool_calls rather than a text answer
response = llm_with_tools.invoke("What is 15 multiplied by 7?")
print(response.tool_calls)
# [{'name': 'multiply', 'args': {'a': 15, 'b': 7}, 'id': 'call_abc123'}]
# Note: the model has NOT executed the tool yet — it just decided to call it
```

---

### 17.2 Full Tool Calling Flow

The complete loop — model decides to use a tool, you execute it, feed the result back.

```python
from langchain_core.messages import HumanMessage, ToolMessage
from langchain_openai import ChatOpenAI
from langchain.tools import tool

@tool
def get_stock_price(ticker: str) -> str:
    """Get the current stock price for a ticker symbol."""
    prices = {"AAPL": "195.50", "GOOGL": "175.20", "MSFT": "415.80"}
    return prices.get(ticker.upper(), "Ticker not found")

@tool
def get_company_info(ticker: str) -> str:
    """Get company information for a given stock ticker."""
    info = {
        "AAPL":  "Apple Inc. — Consumer electronics, software, and services.",
        "GOOGL": "Alphabet Inc. — Internet services and products.",
    }
    return info.get(ticker.upper(), "Company not found")

tools         = [get_stock_price, get_company_info]
tools_by_name = {t.name: t for t in tools}
llm           = ChatOpenAI(model="gpt-4o").bind_tools(tools)

# Step 1: User sends a message
messages = [HumanMessage(content="What is Apple's stock price and what does the company do?")]

# Step 2: LLM responds with tool calls (not a final answer yet)
response = llm.invoke(messages)
messages.append(response)
print("Tool calls:", response.tool_calls)
# [{'name': 'get_stock_price', 'args': {'ticker': 'AAPL'}, 'id': 'call_abc'},
#  {'name': 'get_company_info', 'args': {'ticker': 'AAPL'}, 'id': 'call_xyz'}]

# Step 3: Execute each tool and collect results
for tool_call in response.tool_calls:
    tool_result = tools_by_name[tool_call["name"]].invoke(tool_call["args"])
    messages.append(
        ToolMessage(content=str(tool_result), tool_call_id=tool_call["id"])
    )

# Step 4: Feed results back — LLM generates final answer
final_response = llm.invoke(messages)
print(final_response.content)
# "Apple's (AAPL) current stock price is $195.50. 
#  Apple Inc. is a technology company focused on consumer electronics, software, and services."
```

---

### 17.3 Currency Conversion Tool — Full Example

```python
from langchain.tools import tool
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, ToolMessage

@tool
def convert_currency(amount: float, from_currency: str, to_currency: str) -> str:
    """
    Converts an amount from one currency to another.
    
    Args:
        amount: The amount to convert
        from_currency: Source currency code (USD, INR, EUR, GBP, JPY)
        to_currency: Target currency code
    """
    rates = {"USD": 1.0, "INR": 83.5, "EUR": 0.92, "GBP": 0.79, "JPY": 149.5}

    if from_currency not in rates or to_currency not in rates:
        return f"Unsupported currency. Supported: {', '.join(rates.keys())}"

    usd_amount = amount / rates[from_currency]
    converted  = usd_amount * rates[to_currency]
    return f"{amount} {from_currency} = {converted:.2f} {to_currency}"

tools         = [convert_currency]
tools_by_name = {t.name: t for t in tools}
llm           = ChatOpenAI(model="gpt-4o").bind_tools(tools)

messages  = [HumanMessage(content="How much is 5000 INR in EUR and USD?")]
response  = llm.invoke(messages)
messages.append(response)

for tc in response.tool_calls:
    result = tools_by_name[tc["name"]].invoke(tc["args"])
    messages.append(ToolMessage(content=result, tool_call_id=tc["id"]))

final = llm.invoke(messages)
print(final.content)
# "5000 INR is approximately 55.09 EUR and 59.88 USD."
```

---

## 18. Agents

### What are AI Agents?

An **AI agent** is an LLM that can **reason, decide, and act** autonomously. Unlike a chain (fixed sequence), an agent dynamically decides which tools to use, how many times, in what order — until it reaches a final answer.

```
Chain:  Input → Step1 → Step2 → Step3 → Output  (fixed, predictable)
Agent:  Input → Think → Act → Observe → Think → Act → ... → Final Answer  (dynamic)
```

---

### 18.1 ReAct (Reasoning + Acting)

The most common agent pattern. The agent explicitly produces:
- **Thought** — internal reasoning about what to do next
- **Action** — which tool to call with what input
- **Observation** — the tool's result
- Repeats until it reaches a **Final Answer**

```
User: What is the population of the capital of France?

Thought: I need to find the capital of France first.
Action: wikipedia("capital of France")
Observation: Paris is the capital of France.

Thought: Now I need the population of Paris.
Action: wikipedia("population of Paris")
Observation: Paris has a population of approximately 2.1 million.

Thought: I have enough information.
Final Answer: The population of Paris, capital of France, is approximately 2.1 million.
```

---

### 18.2 Building a ReAct Agent

```python
from langchain import hub
from langchain.agents import create_react_agent, AgentExecutor
from langchain_openai import ChatOpenAI
from langchain.tools import tool
from langchain_community.tools import WikipediaQueryRun
from langchain_community.utilities import WikipediaAPIWrapper

# ── TOOLS ─────────────────────────────────────────────────────
@tool
def calculator(expression: str) -> str:
    """
    Evaluates a mathematical expression.
    Input should be a valid Python math expression like '2 ** 10' or '15 * 7'.
    Do NOT include units or words, only the math expression.
    """
    try:
        result = eval(expression, {"__builtins__": {}}, {})
        return str(result)
    except Exception as e:
        return f"Error: {e}"

wiki_tool = WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper(top_k_results=1))
tools     = [calculator, wiki_tool]

# ── LLM ───────────────────────────────────────────────────────
llm = ChatOpenAI(model="gpt-4o", temperature=0)

# ── PROMPT (standard ReAct prompt from LangChain Hub) ─────────
prompt = hub.pull("hwchase17/react")

# ── AGENT ─────────────────────────────────────────────────────
agent = create_react_agent(llm=llm, tools=tools, prompt=prompt)

agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,         # Prints Thought / Action / Observation at each step
    max_iterations=10,    # Safety limit to prevent infinite loops
    handle_parsing_errors=True
)

# ── RUN ───────────────────────────────────────────────────────
result = agent_executor.invoke({
    "input": "What is the population of Tokyo? Then calculate 1% of that number."
})
print(result["output"])
# "The population of Tokyo is approximately 13.96 million.
#  1% of 13,960,000 is 139,600."
```

---

### 18.3 Memory Types

LLM API calls are **stateless** — each call knows nothing about previous ones. Memory components store conversation history and inject it into each new call.

```python
# 1. ConversationBufferMemory — stores the full transcript
from langchain.memory import ConversationBufferMemory
memory = ConversationBufferMemory()
memory.save_context({"input": "My name is Nitish"}, {"output": "Nice to meet you!"})
print(memory.load_memory_variables({}))
# {'history': 'Human: My name is Nitish\nAI: Nice to meet you!'}

# 2. ConversationBufferWindowMemory — keep only last K turns
from langchain.memory import ConversationBufferWindowMemory
memory = ConversationBufferWindowMemory(k=3)
# Old messages are automatically dropped — prevents token limit issues

# 3. ConversationSummaryMemory — summarize older messages
from langchain.memory import ConversationSummaryMemory
from langchain_openai import ChatOpenAI
memory = ConversationSummaryMemory(llm=ChatOpenAI(model="gpt-4o"))
# Older parts of conversation are summarized to save tokens
# Useful for very long conversations

# Using memory in a chain
from langchain.chains import ConversationChain
from langchain_openai import ChatOpenAI

llm   = ChatOpenAI(model="gpt-4o")
chain = ConversationChain(llm=llm, memory=ConversationBufferMemory(), verbose=True)

chain.invoke({"input": "I love cricket."})
chain.invoke({"input": "What sport did I mention?"})
# → "You mentioned cricket."
```

---

### 18.4 Conversational Agent with Memory

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain.memory import ConversationBufferMemory
from langchain import hub
from langchain_openai import ChatOpenAI
from langchain.tools import tool

@tool
def get_time() -> str:
    """Returns the current date and time."""
    from datetime import datetime
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

@tool
def calculator(expression: str) -> str:
    """Evaluates a math expression."""
    try:
        return str(eval(expression, {"__builtins__": {}}, {}))
    except Exception as e:
        return f"Error: {e}"

tools  = [get_time, calculator]
llm    = ChatOpenAI(model="gpt-4o", temperature=0)
prompt = hub.pull("hwchase17/react-chat")   # Chat version of the ReAct prompt
memory = ConversationBufferMemory(memory_key="chat_history", return_messages=True)

agent          = create_react_agent(llm=llm, tools=tools, prompt=prompt)
agent_executor = AgentExecutor(
    agent=agent, tools=tools, memory=memory, verbose=True
)

# Turn 1
result1 = agent_executor.invoke({"input": "My name is Nitish."})
print(result1["output"])  # "Nice to meet you, Nitish!"

# Turn 2 — agent remembers the name
result2 = agent_executor.invoke({"input": "What is my name?"})
print(result2["output"])  # "Your name is Nitish."

# Turn 3 — agent uses a tool
result3 = agent_executor.invoke({"input": "What is 25 squared?"})
print(result3["output"])  # "25 squared is 625."
```

---

### 18.5 Agent Flow Chart

```
┌─────────────────────────────────────────────────────┐
│                    USER INPUT                        │
└───────────────────────┬──────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────┐
│           AGENT (LLM + ReAct Prompt)                 │
│  Produces: Thought → Action → Action Input           │
└───────────────────────┬──────────────────────────────┘
                        │
               ┌────────▼────────┐
               │  Need a Tool?   │
               └────────┬────────┘
          Yes ◄──────────┴──────────► No
           │                           │
           ▼                           ▼
┌──────────────────┐       ┌───────────────────────┐
│  Execute Tool    │       │     FINAL ANSWER       │
│  Get Observation │       │   Return to User       │
└────────┬─────────┘       └───────────────────────┘
         │
         └──────────────────────────── Loop back to AGENT
```

---

## 📚 Key Concepts Summary

| Concept | One-Liner |
|---|---|
| **LangChain** | Open-source framework for building LLM applications |
| **Chat Model** | Modern LLM that works with message lists (preferred over raw LLMs) |
| **Embeddings** | Convert text into vectors for semantic similarity search |
| **PromptTemplate** | Reusable, validated prompt with variable placeholders |
| **ChatPromptTemplate** | Role-based prompt template for chat models |
| **MessagesPlaceholder** | Inject chat history dynamically into a prompt |
| **Few-Shot Prompting** | Guide model output by providing examples in the prompt |
| **Structured Output** | Force LLM to return typed, parseable data (JSON/Pydantic) |
| **with_structured_output** | Best method for structured output using native function calling |
| **Pydantic** | Runtime data validation library; preferred for production schemas |
| **StrOutputParser** | Converts AIMessage to plain Python string |
| **PydanticOutputParser** | Parses LLM text output into a Pydantic model |
| **Chain** | Sequence of components forming a reusable AI pipeline |
| **Runnable** | Standard interface every LangChain component implements |
| **LCEL** | `\|` pipe syntax for composing Runnables declaratively |
| **RunnableParallel** | Run multiple chains simultaneously on the same input |
| **RunnableBranch** | Conditionally route input to different chains |
| **RunnableLambda** | Wrap any Python function as a composable Runnable |
| **Document Loader** | Ingests raw data (PDFs, URLs, CSVs) into Document objects |
| **Text Splitter** | Breaks large documents into smaller, overlapping chunks |
| **RecursiveCharacterTextSplitter** | Go-to default splitter; respects natural text boundaries |
| **Vector Store** | Database for storing and retrieving vectors by similarity |
| **FAISS** | Fast in-memory vector store; good for prototyping |
| **Chroma** | Lightweight persistent vector database; good for local dev |
| **Retriever** | Fetches relevant documents given a query |
| **MMR** | Retrieval strategy that maximizes relevance + diversity |
| **Multi-Query Retriever** | Generates multiple query variations to improve recall |
| **RAG** | Combine retrieval + generation for grounded, accurate answers |
| **Tool** | Python function packaged for the LLM to call when needed |
| **@tool decorator** | Simplest way to create a custom tool |
| **BaseTool** | Abstract base class for full control over tool behaviour |
| **Tool Binding** | Attach tools to an LLM so it knows what it can call |
| **Agent** | LLM that autonomously decides which tools to use and when |
| **ReAct** | Agent pattern: Reason + Act in a loop until done |
| **AgentExecutor** | Runtime that runs the agent loop and executes tools |
| **Memory** | Store conversation history and inject into future LLM calls |

---

*Notes compiled from LangChain course materials, January–April 2025.*
