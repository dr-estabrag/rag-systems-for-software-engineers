
# Embeddings: A Software Engineer's Perspective

## Introduction

Embeddings are one of the fundamental building blocks of modern AI systems.

They support applications such as:

- Retrieval-Augmented Generation (RAG)
- Semantic search
- Recommendation systems
- Document classification
- Clustering
- Duplicate detection
- Similarity analysis

Embeddings are often introduced as mathematical vectors in a high-dimensional space. That description is correct, but it does not fully explain why embeddings matter to software engineers.

From a software engineering perspective, embeddings provide a practical mechanism for converting unstructured information into a representation that software systems can compare, index, retrieve, and process.

This article explains embeddings through that engineering lens.

---

## The Problem with Exact-Match Search

Consider these three user queries:

1. `How do I reset my password?`
2. `I forgot my login credentials.`
3. `I cannot sign into my account.`

The wording is different, but the underlying intent is similar.

A traditional keyword-based system may struggle because:

- `password` does not appear in every query
- `login` and `sign in` are different phrases
- exact word overlap may be limited
- synonyms and paraphrases are common in natural language

A human reader can immediately recognise the semantic relationship.

The engineering problem is:

> How can a software system compare meaning rather than only matching words?

Embeddings help address this problem.

---

## What Is an Embedding?

An embedding is a numerical representation of an item.

The item might be:

- a word
- a sentence
- a document
- an image
- a product
- a user
- a source-code fragment

An embedding model converts the item into a vector: an ordered sequence of numbers.

For example:

```text
"Software architecture"

        ↓

[0.21, -0.48, 0.77, 0.13, ...]

````markdown
# Embeddings: A Software Engineer's Perspective

## Introduction

Embeddings are one of the fundamental building blocks of modern AI systems.

They support applications such as:

- Retrieval-Augmented Generation (RAG)
- Semantic search
- Recommendation systems
- Document classification
- Clustering
- Duplicate detection
- Similarity analysis

Embeddings are often introduced as mathematical vectors in a high-dimensional space. That description is correct, but it does not fully explain why embeddings matter to software engineers.

From a software engineering perspective, embeddings provide a practical mechanism for converting unstructured information into a representation that software systems can compare, index, retrieve, and process.

This article explains embeddings through that engineering lens.

---

## The Problem with Exact-Match Search

Consider these three user queries:

1. `How do I reset my password?`
2. `I forgot my login credentials.`
3. `I cannot sign into my account.`

The wording is different, but the underlying intent is similar.

A traditional keyword-based system may struggle because:

- `password` does not appear in every query
- `login` and `sign in` are different phrases
- exact word overlap may be limited
- synonyms and paraphrases are common in natural language

A human reader can immediately recognise the semantic relationship.

The engineering problem is:

> How can a software system compare meaning rather than only matching words?

Embeddings help address this problem.

---

## What Is an Embedding?

An embedding is a numerical representation of an item.

The item might be:

- a word
- a sentence
- a document
- an image
- a product
- a user
- a source-code fragment

An embedding model converts the item into a vector: an ordered sequence of numbers.

For example:

```text
"Software architecture"

        ↓

[0.21, -0.48, 0.77, 0.13, ...]
````

A real embedding may contain hundreds or thousands of values.

For example, the `all-MiniLM-L6-v2` sentence-transformer model produces vectors with 384 dimensions.

The individual numbers are not normally meaningful to a developer. Their value comes from the relationships between complete vectors.

The central idea is:

> Items with similar meaning should be represented by vectors that are close together.

---

## Embeddings as Coordinates

A useful mental model is to think of an embedding as a coordinate.

In an ordinary two-dimensional map, a location might be represented by:

```text
(x, y)
```

Nearby coordinates correspond to nearby physical locations.

Embeddings work similarly, except that the space may have hundreds or thousands of dimensions.

Conceptually:

```text
"Reset my password"          ─┐
"I forgot my credentials"    ─┼─ Close together
"I cannot log in"            ─┘

"Today's weather forecast"   ─── Farther away
```

The embedding space is not a geographical map. It is a learned semantic space in which distance reflects patterns discovered by the model during training.

---

## Embeddings Do Not Store Knowledge Directly

An embedding is not a database record containing a human-readable explanation.

It does not normally tell us:

* why two documents are related
* whether a statement is true
* whether a source is authoritative
* whether information is current
* whether access should be permitted

An embedding provides a representation that supports comparison.

This distinction is important:

> Embeddings represent relationships. They do not independently provide truth, reasoning, governance, or understanding.

Those responsibilities belong to the larger system.

---

## From Language to Vectors

An embedding model acts as a translation layer:

```text
Human-readable information
            ↓
      Embedding model
            ↓
   Numerical representation
```

Once information has been represented numerically, software can apply mathematical operations to it.

This enables systems to ask questions such as:

* Which document is most similar to this query?
* Which support tickets describe related problems?
* Which products are semantically related?
* Which passages should be supplied to an LLM?
* Which documents appear to be duplicates?
* Which items belong in the same cluster?

This is what makes embeddings architecturally important.

They connect unstructured information with conventional software mechanisms such as indexing, ranking, filtering, storage, and retrieval.

---

## Measuring Similarity

To compare embeddings, systems use similarity or distance measures.

Common choices include:

* Cosine similarity
* Dot product
* Euclidean distance

### Cosine Similarity

Cosine similarity compares the direction of two vectors.

Its value usually ranges from:

```text
-1  opposite directions
 0  unrelated directions
 1  identical directions
```

For many text-embedding models, semantically similar texts tend to have higher cosine-similarity scores.

Conceptually:

```text
Query:
"How can I recover my account password?"

Document A:
"Instructions for resetting forgotten passwords"
Similarity: high

Document B:
"Quarterly financial reporting procedures"
Similarity: low
```

The precise score is model-dependent. A score should not be treated as a universal measure of semantic truth.

---

## A Simple Python Example

The following example uses Sentence Transformers:

```python
from sentence_transformers import SentenceTransformer
from sentence_transformers.util import cos_sim

model = SentenceTransformer("all-MiniLM-L6-v2")

sentences = [
    "How do I reset my password?",
    "I forgot my login credentials.",
    "The weather is sunny today.",
]

embeddings = model.encode(
    sentences,
    convert_to_tensor=True,
    normalize_embeddings=True,
)

similarity_matrix = cos_sim(embeddings, embeddings)

print("Embedding shape:", tuple(embeddings.shape))
print(similarity_matrix)
```

Expected embedding shape:

```text
(3, 384)
```

The first two sentences should normally receive a higher similarity score than either receives when compared with the weather sentence.

The exact values may vary by model and library version.

---

## Embeddings in a RAG System

Embeddings are frequently used in Retrieval-Augmented Generation.

A simplified ingestion flow is:

```text
Documents
    ↓
Content extraction
    ↓
Chunking
    ↓
Embedding model
    ↓
Embeddings
    ↓
Vector index
```

The query flow is:

```text
User question
    ↓
Query embedding
    ↓
Similarity search
    ↓
Relevant chunks
    ↓
Prompt construction
    ↓
LLM
    ↓
Generated response
```

The document chunks and the user's query are embedded using compatible models. The system then searches for document vectors that are close to the query vector.

The retrieved content is supplied to the LLM as context.

---

## The Role of the Vector Database

Embeddings need to be stored and searched efficiently.

Possible storage technologies include:

* FAISS
* Chroma
* Qdrant
* Weaviate
* Pinecone
* Milvus
* PostgreSQL with `pgvector`
* Elasticsearch or OpenSearch with vector-search support

A vector database or vector index typically provides:

* vector storage
* nearest-neighbour search
* metadata filtering
* indexing
* persistence
* scaling
* replication
* operational controls

However, a vector database does not solve the complete retrieval problem.

The quality of a RAG system also depends on:

* source-document quality
* chunking strategy
* embedding-model choice
* query formulation
* metadata design
* filtering
* ranking
* reranking
* access control
* evaluation

---

## Embedding Model Selection

Choosing an embedding model is an architectural decision.

Important factors include:

### Retrieval Quality

Different models perform differently across domains, languages, and task types.

### Vector Dimension

Larger vectors may capture richer relationships but require more:

* storage
* memory
* network bandwidth
* computation

### Latency

Embedding speed matters in interactive systems and high-volume ingestion pipelines.

### Cost

Hosted embedding APIs may charge per token or request.

Local models require infrastructure, memory, monitoring, and maintenance.

### Language Support

Some models are English-focused. Others support multilingual retrieval.

### Domain Suitability

A general-purpose model may perform poorly on:

* legal documents
* medical terminology
* source code
* financial reports
* specialist engineering language

### Maximum Input Length

Embedding models have input-length limits. Long documents therefore require splitting or summarisation strategies.

### Privacy and Deployment

Sensitive content may require:

* local execution
* private cloud deployment
* regional data processing
* encryption
* audit controls

---

## Model Compatibility and Versioning

Document embeddings and query embeddings must be produced in a compatible vector space.

A serious operational mistake is to:

1. Embed documents with Model A.
2. Replace it with Model B.
3. Embed queries using Model B.
4. Search against old Model A vectors.

Even when both models produce vectors of the same dimension, their vector spaces are not necessarily compatible.

An embedding migration may therefore require:

* model versioning
* index versioning
* complete re-embedding
* dual-running old and new indexes
* evaluation before cutover
* rollback capability

Embedding-model identifiers should be treated as part of the stored data's schema.

Useful metadata may include:

```text
embedding_model
embedding_model_version
embedding_dimension
created_at
source_version
chunking_strategy
pipeline_version
```

---

## Chunking and Embeddings

Documents are usually too long to embed as a single unit.

They are divided into chunks.

Chunking affects retrieval quality because an embedding represents the content of the entire chunk.

### Chunks That Are Too Large

Large chunks may contain several unrelated ideas.

The resulting embedding may become semantically diluted.

### Chunks That Are Too Small

Small chunks may lose context and become ambiguous.

### Overlap

Overlapping chunks can preserve continuity, but excessive overlap creates:

* duplicate results
* higher storage usage
* longer ingestion times
* repeated context in prompts

### Semantic Boundaries

Where possible, chunks should respect meaningful structures such as:

* paragraphs
* sections
* headings
* code blocks
* table boundaries
* conversational turns

There is no universally correct chunk size. It should be selected and evaluated for the specific domain and retrieval task.

---

## Metadata Is as Important as the Vector

A vector captures semantic relationships, but production retrieval often requires exact filtering.

Examples include:

* tenant identifier
* department
* document type
* publication date
* country
* security classification
* product version
* access-control group

A query may need to retrieve semantically similar content only from documents that the user is authorised to access.

A practical retrieval operation may therefore combine:

```text
Semantic similarity
        +
Metadata filtering
        +
Access-control enforcement
        +
Reranking
```

This is one reason embeddings should be treated as part of a wider information-retrieval architecture.

---

## Embedding Pipelines

A production ingestion pipeline may include:

```text
Source systems
      ↓
Document acquisition
      ↓
Parsing and extraction
      ↓
Cleaning and normalisation
      ↓
Chunking
      ↓
Metadata enrichment
      ↓
Embedding generation
      ↓
Validation
      ↓
Vector storage
```

The pipeline must also handle:

* retries
* partial failures
* duplicate documents
* deleted documents
* updated documents
* unsupported file types
* rate limits
* malformed content
* model timeouts
* audit logging

These are conventional software-engineering concerns. The presence of an AI model does not remove the need for robust pipeline design.

---

## Updating Embeddings

Knowledge sources change over time.

A production system needs a clear update strategy.

Possible approaches include:

### Full Rebuild

Reprocess and re-embed the entire collection.

This is simple but can be expensive.

### Incremental Updates

Embed only new or changed content.

This is efficient but requires reliable change detection.

### Content Hashing

Generate a hash for each source or chunk and re-embed only when its content changes.

### Event-Driven Ingestion

Trigger ingestion when documents are created, updated, or deleted.

### Scheduled Synchronisation

Periodically compare source systems with the retrieval index.

Deletion must also be handled correctly. Removing a source document should remove or invalidate all embeddings derived from it.

---

## Security Considerations

Embeddings can represent sensitive information.

Although an embedding is not directly human-readable, it should not automatically be treated as anonymous or harmless.

Security concerns include:

* unauthorised retrieval
* cross-tenant leakage
* inference attacks
* insecure metadata
* exposed vector-database endpoints
* unencrypted backups
* excessive logging
* inappropriate use of third-party APIs

Access control should be applied during retrieval, not only after documents have been returned.

The architecture should ensure that a user cannot retrieve content they were not authorised to access in the source system.

---

## Evaluating Embedding Quality

A model should not be selected because it is fashionable or performs well on a generic leaderboard.

It should be evaluated using representative queries and documents from the target domain.

Useful retrieval metrics include:

* Recall@k
* Precision@k
* Mean Reciprocal Rank
* Mean Average Precision
* Normalised Discounted Cumulative Gain
* hit rate

Human evaluation is also important.

A simple evaluation dataset may contain:

```text
Query
Expected relevant document or chunk
Known irrelevant documents
Optional relevance grades
```

Without evaluation, changing an embedding model, chunk size, or search strategy becomes guesswork.

---

## Common Misconceptions

### Misconception 1: Embeddings Understand Meaning Like Humans

Embedding models learn statistical relationships from training data.

They may capture useful semantic patterns, but they do not possess human understanding.

### Misconception 2: Similarity Means Truth

A passage can be semantically similar to a query and still be:

* incorrect
* outdated
* unauthorised
* misleading
* incomplete

Similarity is a retrieval signal, not a truth guarantee.

### Misconception 3: Higher Dimension Always Means Better

More dimensions increase storage and computation. They do not automatically improve application-level retrieval.

### Misconception 4: One Model Works for Every Domain

Embedding quality varies across languages, industries, document types, and tasks.

### Misconception 5: A Vector Database Solves Retrieval

A vector database provides infrastructure. Retrieval quality depends on the entire pipeline.

### Misconception 6: Embeddings Eliminate Keyword Search

Semantic and lexical retrieval often complement each other.

Hybrid search can combine:

* dense vector retrieval
* keyword or BM25 search
* metadata filters
* reranking

### Misconception 7: Embeddings Never Need Updating

Changes to documents, models, metadata, chunking policies, or business rules may require reprocessing.

---

## Dense and Sparse Representations

Most modern embeddings used in RAG are dense vectors.

A dense vector contains many non-zero floating-point values:

```text
[0.21, -0.48, 0.77, 0.13, ...]
```

Traditional lexical retrieval often uses sparse representations, where most dimensions are zero and dimensions may correspond to terms or features.

Dense retrieval is generally effective at semantic similarity.

Sparse retrieval is often effective for:

* exact terminology
* identifiers
* product codes
* rare names
* legal clauses
* error messages

This is why hybrid retrieval can outperform either method used alone.

---

## Reranking

Initial vector search is often optimised for speed.

It returns a candidate set of potentially relevant chunks.

A reranker can then evaluate those candidates more precisely:

```text
Query
  ↓
Fast vector retrieval
  ↓
Top 20 candidates
  ↓
Reranking model
  ↓
Top 5 results
```

This separates two concerns:

* efficient candidate generation
* accurate relevance ranking

It is a common architectural pattern in higher-quality retrieval systems.

---

## Operational Considerations

Embedding-based systems require normal operational discipline.

Important concerns include:

* index size
* query latency
* ingestion throughput
* embedding API failure rates
* cost per document
* cost per query
* cache utilisation
* stale-content percentage
* retrieval-quality trends
* model and index versions
* backup and recovery

Observability should cover both infrastructure and retrieval quality.

A system can be technically available while returning poor results.

---

## Architecture Before Frameworks

Frameworks make it easy to create demonstrations:

```python
vector_store.add_documents(documents)
results = vector_store.similarity_search(query)
```

But production systems require deeper questions:

* What is the authoritative source?
* How are document changes detected?
* What happens when embedding generation fails?
* How are old vectors removed?
* How is access control enforced?
* How is retrieval quality measured?
* How are model migrations managed?
* How is the index rebuilt after failure?
* How are costs monitored?
* How are results explained and audited?

These questions are more enduring than any individual framework.

---

## Conclusion

Embeddings convert unstructured information into numerical representations that software systems can compare and retrieve.

Their practical value lies in enabling:

* semantic search
* information retrieval
* recommendation
* classification
* clustering
* RAG systems

However, an embedding model is only one component within a larger architecture.

Production-quality systems also require:

* reliable ingestion
* thoughtful chunking
* metadata design
* access control
* versioning
* evaluation
* monitoring
* operational discipline

For a software engineer, the most useful way to think about embeddings is not as mathematical magic.

They are an architectural bridge between unstructured information and programmable retrieval.

The quality of the final system depends not only on the vectors, but on how the complete system around them is engineered.

````

After saving it, use this commit sequence:

```powershell
git add docs/concepts/embeddings.md
git commit -m "Expand embeddings concept article"
git push origin main
````
