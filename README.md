# cairnveil-rag

![Cairnveil Security Agent banner](banner.png)

Cybersecurity-focused Self-RAG notebook for answering questions from internal company knowledge documents for a fictional cybersecurity company, Cairnveil Security.

## What This Builds

The notebook implements a Self-RAG inspired workflow:

1. Decide whether the question needs retrieval.
2. Embed company document chunks with Gemini Embedding.
3. Retrieve candidate chunks from a FAISS vector index.
4. Grade retrieved chunks for relevance.
5. Generate an answer grounded only in relevant context.
6. Critique whether the answer is supported by the evidence.
7. Revise unsupported answers.
8. Critique whether the final answer is useful.
9. Rewrite the retrieval query and retry when needed.

The original Self-RAG paper trains a model to use reflection tokens for adaptive retrieval, generation, and critique. This project does not fine-tune a Self-RAG model. Instead, it implements the same control ideas as a practical LangGraph notebook that calls an OpenAI-compatible Kilo AI Gateway model. (tested for free :D )

## Architecture

```text
                         CAIRNVEIL SECURITY AGENT
                 Self-RAG workflow for grounded cyber answers

  +----------------+
  |  User Question |
  +-------+--------+
          |
          v
  +-------------------+        no        +-------------------+
  | decide_retrieval  +----------------->| generate_direct   |
  | Need company docs?|                  | Generic response  |
  +---------+---------+                  +---------+---------+
            | yes                                  |
            v                                      v
  +-------------------+                    +---------------+
  | retrieve          |                    |     END       |
  | FAISS / NumPy     |                    +---------------+
  | Gemini embeddings |
  +---------+---------+
            |
            v
  +-------------------+        none       +-------------------+
  | grade_relevance   +------------------>| rewrite_query     |
  | Keep useful docs  |                   | Better search     |
  +---------+---------+                   +---------+---------+
            | relevant                              |
            v                                       |
  +-------------------+                             |
  | generate_from_    |                             |
  | context           |                             |
  | Grounded answer   |                             |
  +---------+---------+                             |
            |                                       |
            v                                       |
  +-------------------+    weak support   +---------v---------+
  | grade_support     +------------------>| revise_answer     |
  | Evidence check    |                   | Remove weak claims|
  +---------+---------+                   +---------+---------+
            | supported                             |
            v                                       |
  +-------------------+    not useful     +---------v---------+
  | grade_usefulness  +------------------>| rewrite_query     |
  | Final quality gate|                   | Retry retrieval   |
  +---------+---------+                   +---------+---------+
            | useful                               |
            v                                      |
      +-------------+                              |
      |    END      |<-----------------------------+
      +-------------+
```

### Node Roles

| Node | Job |
| --- | --- |
| `decide_retrieval` | Routes the question to either direct answering or document-grounded retrieval. |
| `retrieve` | Searches Cairnveil documents with Gemini embeddings and FAISS, falling back to NumPy cosine search if FAISS is unavailable. |
| `grade_relevance` | Filters retrieved chunks so only useful evidence enters the answer context. |
| `generate_from_context` | Drafts an answer using only the selected document evidence. |
| `grade_support` | Checks whether the answer is actually backed by the retrieved context. |
| `revise_answer` | Removes or rewrites unsupported claims, then sends the answer back for support grading. |
| `grade_usefulness` | Checks whether the grounded answer is concise, responsive, and useful. |
| `rewrite_query` | Creates a sharper retrieval query when evidence or answer quality is not good enough. |
| `generate_direct` | Handles general cybersecurity questions that do not need Cairnveil-specific facts. |

### Data Flow

```text
Documents -> chunking -> Gemini embeddings -> vector index
                                      |
                                      v
Question -> routing -> retrieval -> evidence grading -> grounded generation
                                      |
                                      v
                         support critique -> revision loop
                                      |
                                      v
                         utility critique -> retry or final answer
```

## Research Reference

The architecture is based on:

- Akari Asai, Zeqiu Wu, Yizhong Wang, Avirup Sil, and Hannaneh Hajishirzi. "Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection." ICLR 2024. https://arxiv.org/abs/2310.11511
- Original implementation: https://github.com/AkariAsai/self-rag


The paper describes Self-RAG as a framework where a language model adaptively retrieves passages on demand and reflects on retrieved passages and its own generations. This notebook mirrors those stages with explicit graph nodes and JSON-graded decisions.

## LLM Provider

This project uses the Kilo AI Gateway, which exposes an OpenAI-compatible API at:

```text
https://api.kilo.ai/api/gateway
```

Default model:

```text
nvidia/nemotron-3-super-120b-a12b:free
```

The notebook reads `KILOCODE_API_KEY` from `.env`. It also accepts `KILO_API_KEY` as a fallback alias.

## Embeddings

Retrieval uses Google's Gemini API embedding model:

```text
gemini-embedding-001
```

Document chunks are embedded with the `RETRIEVAL_DOCUMENT` task type, user queries are embedded with `RETRIEVAL_QUERY`, and vectors are searched with FAISS inner-product similarity after normalization. If FAISS is unavailable in the local Python environment, the notebook falls back to NumPy cosine search so the retrieval path remains understandable.

Gemini embedding docs: https://ai.google.dev/api/embeddings

## Setup

Create an environment and install dependencies:

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Copy `.env.example` values into `.env`, then add your key:

```text
KILOCODE_API_KEY=your_kilo_gateway_api_key
KILO_API_BASE=https://api.kilo.ai/api/gateway
KILO_MODEL=nvidia/nemotron-3-super-120b-a12b:free
GEMINI_API_KEY=your_gemini_api_key
GEMINI_EMBEDDING_MODEL=gemini-embedding-001
```

Run the notebook:

```bash
jupyter lab self_rag_cybersecurity.ipynb
```

## Knowledge Documents

Sample cybersecurity company documents live in `documents/`:

- `cairnveil_company_profile.md`
- `cairnveil_services_and_pricing.md`
- `cairnveil_security_policies.md`
- `cairnveil_incident_response_playbook.md`

Replace or extend these with your own approved cybersecurity company documents. Do not place production secrets, private keys, passwords, or raw customer incident evidence in this repository.

## Notes
- The sample documents are fictional and are meant to demonstrate retrieval behavior.
- The retriever uses Gemini embeddings plus FAISS. The Kilo LLM is used for routing, grading, revision, and final answer generation.
