

---

# 📘 AI / LLM / RAG / Transformers

---

## 1️⃣ LLMs are Stateless

LLM APIs (like OpenAI) do NOT remember past calls.

What ChatGPT does:

👉 Resends conversation history every time (managed by app)

So:

```
memory = application feature
not model feature
```

You must manage:

• chat history  
• summaries  
• DB memory  
• vector memory

---

## 2️⃣ Embedding Model vs LLM

### 🤖 LLM (Chat Model)

Input → text output

Used for:

• reasoning  
• summarizing  
• explaining

---

### 📐 Embedding Model

Input → vector (array of floats)

Used for:

• semantic similarity  
• search  
• retrieval

NO language generation.

---

## 3️⃣ What is an Embedding?

An embedding is:

👉 the numeric output of a neural network layer  
👉 representing meaning

Example:

```
"text" → [0.12, -0.44, 0.91, ...]
```

These numbers:

• don’t represent words directly  
• represent learned semantic features

Similar meaning → vectors close in space.

---

## 4️⃣ What is a Vector?

A vector = 1D tensor (array of floats)

Stored in Postgres using pgvector:

```
VECTOR(1536)
```

Looks like:

```
[0.12, -0.44, 0.91, ...]
```

---

## 5️⃣ RAG = Retrieval Augmented Generation

Pipeline:

```
text → embedding → vector
vector → similarity search (math)
top results → injected into prompt
LLM → final response
```

Important:

❗ Retrieval happens BEFORE LLM  
❗ Similarity search is pure math

LLM is only for:

• reasoning  
• phrasing

---

## 6️⃣ Vector Search Mechanics

Vectors stored in DB.

Query:

```
similarity(query_vector, stored_vector)
```

Usually:

• cosine similarity  
• dot product  
• euclidean distance

Vector DBs (or pgvector) use smart indexes (HNSW).

No ML here — just math + data structures.

---

## 7️⃣ pgvector (Postgres Extension)

Adds:

```
VECTOR(n)
```

columns to Postgres.

Allows:

```
ORDER BY embedding <-> query_vector
LIMIT k;
```

So Postgres becomes a vector DB.

Used heavily in industry.

---

## 8️⃣ Tensor

A tensor is just:

• scalar (0D)  
• vector (1D)  
• matrix (2D)  
• higher-dim array

NumPy arrays = tensors  
PyTorch/TF tensors = optimized tensors + ML features.

---

## 9️⃣ Transformers (Core Architecture)

Transformers replaced old RNN/LSTM models.

They process ALL tokens at once using:

### Two main parts per layer:

1️⃣ Attention layer  
2️⃣ Feed-forward neural network

Stacked many times.

---

## 🔁 Transformer Block (simplified)

```
Token embeddings
   ↓
Self-Attention
   ↓
Feed Forward NN
   ↓
Next layer
```

---

## 🔍 Attention Mechanism (intuition)

Each word:

👉 looks at all other words  
👉 assigns weights dynamically  
👉 mixes important info

Computed via:

• dot products  
• softmax  
• weighted sums

NOT static.

Context dependent.

---

## 1️⃣0️⃣ Parameters (the “billions”)

Parameters = learned numbers inside huge matrices.

Mostly in:

• attention matrices (Wq, Wk, Wv)  
• feed-forward layers

Example sizes:

```
4096 x 4096
4096 x 16000
```

Stack many layers → billions of numbers.

They are NOT rules.

Just weights in matrix multiplications.

---

## 1️⃣1️⃣ Training (Backpropagation)

Process:

1. Model predicts something
    
2. Loss computed
    
3. Gradients flow backward
    
4. Matrices updated slightly
    

Repeat billions of times.

Matrices slowly evolve to encode language.

---

## 🧊 Inference (API usage)

No learning.

Just forward pass:

```
input → matrix ops → output
```

---

## 1️⃣2️⃣ How Embeddings are Produced

During forward pass:

```
tokens → embeddings → attention → NN layers → final vector
```

That final vector is your embedding.

Same math.

Just outputting features instead of words.

---

## 1️⃣3️⃣ Relation between Attention and Vector Similarity

Inside attention:

```
Qi · Kj   (dot product)
```

Outside in RAG:

```
vecA · vecB
```

Same math idea.

Transformers are FULL of dot products.

---

## 1️⃣4️⃣ Prompt Injection = SQL Injection of AI

LLMs mix:

trusted instructions + untrusted input

So attackers can override prompts.

Fix with:

• strong system prompts  
• input validation  
• output validation  
• backend enforcement

Never trust AI output blindly.

---

## 1️⃣5️⃣ Hybrid AI System (Best Real Design)

Instead of pure RAG:

```
AI → parse messy input
DB → facts
Backend → logic
AI → explanation
```

RAG only when data is:

• huge  
• unstructured  
• private

---

# 📌 FINAL BIG PICTURE

```
Text
 ↓
Neural Network (Transformer)
 ↓
Tensor (embedding vector)
 ↓
Vector DB / pgvector
 ↓
Similarity math
 ↓
Relevant data
 ↓
LLM reasoning
 ↓
Answer
```

---

# 🧠 KEY TAKEAWAYS

• Embeddings = neural network outputs  
• Vectors = numeric meaning representations  
• Transformers = stacked attention + NN layers  
• Billions of parameters = giant learned matrices  
• RAG = semantic search + LLM  
• Vector search is math, not AI  
• LLMs are stateless  
• Backend must enforce security

---

