# CloudLexis

**Ask in any cloud's language. Get the answer in your company's.**

CloudLexis is a knowledge assistant for new solutions engineers at a cloud company. A new engineer asks a question in the words they already know, for example *"What is our equivalent of an AWS m5.2xlarge?"*. CloudLexis turns those words into the company's own product names, finds the right internal document, and answers with its sources.

![The Ask page: an answer in Nimbus terms, with sources and a step-by-step trace](docs/screenshots/01-ask.png)

> **Prototype notice.** This is a working front-end prototype for a university NLP solution-design project. All data is synthetic. **Nimbus Cloud** is a fictional cloud provider, so its product names, prices and policies are invented. AWS, Microsoft Azure and Google Cloud names are trademarks of their owners and appear only to show how terms map between providers.

---

## Contents

- [The problem](#the-problem)
- [Who it is for](#who-it-is-for)
- [Quick start](#quick-start)
- [Features](#features)
  - [1. Ask](#1-ask)
  - [2. Term Translator](#2-term-translator)
  - [3. Spec Matcher](#3-spec-matcher)
  - [4. Knowledge Base](#4-knowledge-base)
  - [5. Onboarding](#5-onboarding)
  - [6. Evaluation](#6-evaluation)
  - [7. Trust & Safety](#7-trust--safety)
- [How it works](#how-it-works)
- [Evaluation results](#evaluation-results)
- [Five-minute guided tour](#five-minute-guided-tour)
- [Data and privacy](#data-and-privacy)
- [Project structure](#project-structure)
- [Planned production stack](#planned-production-stack)
- [Limits and roadmap](#limits-and-roadmap)

---

## The problem

Solutions engineers often move between cloud providers. Their architecture knowledge moves with them, but the product names do not. Every provider has its own name for the same thing:

| Idea | AWS | Azure | Google Cloud | Nimbus Cloud (fictional) |
|---|---|---|---|---|
| Virtual server | Amazon EC2 | Azure Virtual Machines | Compute Engine | Nimbus Elastic Server (NES) |
| 8 vCPU, 32 GiB server | m5.2xlarge | Standard_D8s_v5 | n2-standard-8 | nx-gp3-8x32 |
| Object storage | Amazon S3 | Azure Blob Storage | Cloud Storage | Nimbus Object Store (NOS) |
| Archive storage | S3 Glacier | Blob Archive tier | Archive class | NOS Frozen tier |

Internal search only matches exact words. "EC2" never appears in Nimbus documents, so a new engineer who searches for it finds nothing. CloudLexis closes that gap with entity recognition, entity linking and retrieval-augmented generation (RAG).

## Who it is for

| User | Typical question |
|---|---|
| New solutions engineer | "Customer runs r5.2xlarge on AWS. What do we offer?" |
| Support engineer | "Where do I see metrics like in CloudWatch?" |
| Account manager | "What discount can I give without approval?" |

Each role sees only the documents it is allowed to read.

## Quick start

CloudLexis is one HTML file. There is nothing to install and no server to run.

1. Download or clone this repository.
2. Double-click `index.html`, or drag it into Chrome, Edge, Firefox or Safari.
3. Click one of the example questions on the Ask page.

It works offline. To share it as a web page, turn on **GitHub Pages** (Settings → Pages → Deploy from a branch → `main` / root) and open the link GitHub gives you.

---

## Features

### 1. Ask

Chat with the knowledge base. Every answer is written in Nimbus terms, shows its sources, and passes a set of checks before you see it.

![Ask page](docs/screenshots/01-ask.png)

**How to use**

1. Type a question in the box at the bottom, or in the search bar at the top (press `/` to jump there).
2. Read the answer. The chips at the top show how terms were translated, for example `EC2 → NES`.
3. Click a source card or a small number such as `1` to open the document.
4. Look at the footer: confidence, how many numbers were checked against the sources, and a warning if a source is old.
5. Use 👍 or 👎 to send feedback. "Not helpful" answers go to the document owner.

**The trace panel** on the right shows how the answer was built, in seven steps:

![The seven-step pipeline trace](docs/screenshots/09-pipeline-trace.png)

**When there is no answer**, CloudLexis says so. Here a price is asked for, but the knowledge base has none, so it does not guess:

![Guardrail: no price in the sources](docs/screenshots/02-ask-guardrail.png)

**Questions to try**

- What is our equivalent of an AWS EC2 m5.2xlarge?
- Do we have something like S3 Glacier for cheap archive?
- How do I connect two VPCs? *(an ambiguous name: watch step 4 in the trace)*
- where are my cloudwach alarms *(a typo: fuzzy matching still finds CloudWatch)*
- How much does a Redshift node cost in Nimbus? *(no price in the sources)*
- What discount can I give without approval? *(switch the role to Support Engineer and ask again)*

### 2. Term Translator

Paste a sentence from a customer e-mail or call note. Product names, instance types and regions from other clouds are swapped for Nimbus names.

![Term Translator](docs/screenshots/03-translator.png)

**How to use**

1. Paste or type the customer's wording on the left and click **Translate**.
2. The right side shows the sentence in Nimbus terms, with a table of every change.
3. Below, search the **cross-cloud glossary** (18 concepts, four providers) or filter it by category.
4. Click a row to see the "watch out" notes, the main document, and every spelling the system recognises.

### 3. Spec Matcher

Find the closest Nimbus Elastic Server type for any instance from AWS, Azure or Google Cloud, or size a whole customer inventory at once.

![Spec Matcher](docs/screenshots/04-spec-matcher.png)

**How to use**

1. Type an instance code (`m5.2xlarge`, `Standard_F8s_v2`, `n2-highmem-8`) or plain specs (`16 vCPU 64 GB`) and click **Match**.
2. The best three Nimbus types appear with a match score and the reasons behind it (same vCPU, same memory, same family).
3. For a migration, paste one line per server in **Bulk sizing**: `name, instance type, count`.
4. Click **Size all** to get a Nimbus type and a monthly list price for each line, then **Download CSV** to attach the sizing sheet to a deal.

Prices are demo values. Real quotes go through Deal Desk.

### 4. Knowledge Base

See which documents CloudLexis can search, how they were prepared, and who may read them.

![Knowledge Base](docs/screenshots/05-knowledge-base.png)

**How to use**

1. The **ingestion pipeline** shows each preparation step: parse, clean, redact, chunk, tag, embed and index.
2. Filter the document list by type or title. A lock icon means your role cannot open the document.
3. Click a document to inspect its chunks. Recognised products, instance types and specs are highlighted.
4. Try the **redaction** box: e-mail addresses, phone numbers, access keys and names are replaced with tags such as `[EMAIL]`.

### 5. Onboarding

A 30-day plan for each role, with links straight to the right tool or document.

![Onboarding](docs/screenshots/06-onboarding.png)

**How to use**

1. Tick tasks as you finish them. Progress is saved in your browser.
2. Flip the **term of the day** card to see the Nimbus name for a familiar product.
3. Take the **service-name quiz**, five questions built from the glossary.

### 6. Evaluation

An offline test of retrieval on 24 demo questions. You can run it yourself.

![Evaluation](docs/screenshots/07-evaluation.png)

**How to use**

1. Click **Run evaluation**.
2. Compare three settings: plain keyword search (A), plus entity linking and query rewriting (B), plus entity ranking (C).
3. The table at the bottom shows the rank of the right document for every question.

### 7. Trust & Safety

The rules that keep answers grounded, private and fair.

![Trust & Safety](docs/screenshots/08-trust-safety.png)

**How to use**

1. Switch a guardrail off, for example the role-based access filter, then ask a question again and compare the trace.
2. Read the **model and data card** to see which model does which job, and why.
3. The **audit log** shows this session's questions with a pseudonymous user ID. Click **Load sample session** if it is empty.

---

## How it works

Every question goes through seven steps. The prototype runs most of them for real in the browser.

| Step | What it does | In this prototype | Planned for production |
|---|---|---|---|
| 1. Tokenise | Splits the question; keeps codes like `m5.2xlarge` in one piece | Regex rules for product codes and regions | spaCy tokenizer with the same rules |
| 2. Intent | Decides the question type (mapping, sizing, specs, pricing, how-to, concept) | Keyword model with softmax scores | Fine-tuned DistilBERT |
| 3. Entities | Finds PROVIDER, SERVICE, SKU, SPEC and REGION | Glossary lookup, patterns, fuzzy matching | spaCy EntityRuler + fine-tuned BERT NER |
| 4. Link | Ties each name to one concept and its Nimbus name | Alias match, fuzzy match, context for ambiguous names | Adds embedding similarity for new names |
| 5. Rewrite | Adds the Nimbus names to the question | ✔ runs | Same |
| 6. Retrieve | Finds and ranks chunks the user may read | Access filter, BM25, entity ranking, reciprocal rank fusion, spec-table lookup | Adds dense search (bge-small-en-v1.5) and a cross-encoder reranker |
| 7. Answer and check | Writes the answer from the chunks, then checks it | Template answer; grounding, number, price, freshness and privacy checks | Llama 3.1 8B Instruct, self-hosted, with the same checks |

Documents are prepared once, before any question: personal data is redacted, text is split into chunks of up to three sentences with the document title, and each chunk is tagged with the products it mentions.

## Evaluation results

Results from the built-in test (24 questions: 16 asked in AWS, Azure or Google Cloud words, 8 in Nimbus words):

| Setting | Recall@1 | Recall@3 | MRR | Cross-vocabulary Recall@1 |
|---|---|---|---|---|
| A. BM25 on the raw question | 0.625 | 0.750 | 0.689 | 0.500 |
| B. + entity linking and query rewrite | 0.917 | 1.000 | 0.958 | 0.938 |
| C. + entity ranking with RRF | 0.917 | 1.000 | 0.951 | 0.938 |

*Recall@k*: the right document is in the top k results. *MRR*: the average of 1 / rank of the right document (1.0 means always first).

Entity linking and query rewriting make the biggest difference. Plain keyword search cannot find "AWS Lambda" in documents that only say "Nimbus Pulse Functions". The test set is small and synthetic, so these numbers show that the pipeline works; they are not a claim about real-world accuracy.

## Five-minute guided tour

A suggested path for a live demo or a screen recording.

| Time | Page | What to show |
|---|---|---|
| 0:00 | Ask | Ask *"What is our equivalent of an AWS EC2 m5.2xlarge?"*. Point out the term chips, the spec row with its price, the sources and the "numbers checked" badge. |
| 1:00 | Ask → trace | Walk through the seven steps: the protected token, intent scores, entities, links, rewritten query, ranked chunks and checks. |
| 2:00 | Ask | Ask *"How much does a Redshift node cost in Nimbus?"*. Show that it maps Redshift to Lakehouse but refuses to invent a price. |
| 2:30 | Role switch | Switch to **Support Engineer** and ask *"What discount can I give without approval?"*. The policy is hidden for this role. |
| 3:00 | Term Translator | Translate the sample customer sentence; open a glossary row and its "watch out" note. |
| 3:30 | Spec Matcher | Match `Standard_NC8as_T4_v3`, then click **Size all** for the sample inventory. |
| 4:00 | Knowledge Base | Show the ingestion pipeline, a chunk with entity highlights, and the redaction box. |
| 4:30 | Evaluation | Click **Run evaluation** and compare settings A, B and C. |

## Data and privacy

- **All data is synthetic.** The 28 documents, 18 glossary concepts and 13 Nimbus instance types are written for this demo. Public instance specs for AWS, Azure and Google Cloud are included only for sizing examples and may change; check the official documentation.
- **Placeholder personal data.** Two documents contain made-up contact details (`jane.doe@example.com`, `+971 50 000 0000`) on purpose, to show that the redaction step removes them before indexing.
- **No network calls.** The page does not send anything anywhere. There are no external scripts, fonts or analytics.
- **Browser storage.** Only your selected role, theme and onboarding checkboxes are kept in your browser's local storage. Clear your site data to reset them.

## Project structure

```
cloudlexis/
├── index.html              # the whole prototype
├── README.md               # this guide
└── docs/
    └── screenshots/        # images used in this guide
```

`index.html` is organised in clearly commented parts:

| Part | What is inside |
|---|---|
| `<style>` | Design tokens (light and dark theme), layout and components |
| `<script id="cl-data">` | Demo data: glossary, regions, instance types, documents and the test set |
| `<script id="cl-engine">` | The NLP engine: tokeniser, intent model, entity recogniser, linker, query rewrite, indexing, BM25, rank fusion, answer composer, checks and evaluation. It never touches the page, so it can also run in Node.js. |
| `<script id="cl-app">` | The user interface: seven pages, routing, role switcher and theme |

**Deep links** open a page in a set state, which is handy for demos:

| Link | Opens |
|---|---|
| `index.html#/ask?q=What%20replaces%20AWS%20Lambda%3F` | Ask, with the question already answered |
| `index.html#/spec?sku=r5.4xlarge` | Spec Matcher, with that instance matched |
| `index.html#/kb?doc=D08` | Knowledge Base, with document D08 open |
| `index.html#/eval?run=1` | Evaluation, already run |
| `index.html#/trust?demo=1` | Trust & Safety, with a sample audit log |

## Planned production stack

| Layer | Tool | Role |
|---|---|---|
| NLP | spaCy, Hugging Face Transformers | Tokenizer rules, entity recognition, intent classifier |
| Embeddings | sentence-transformers (bge-small-en-v1.5, bge-reranker-base) | Dense search and reranking |
| Privacy | Microsoft Presidio | Redact personal data before indexing |
| Search | OpenSearch (BM25), Qdrant (vectors) | Hybrid search with metadata filters |
| Orchestration | LangChain | Retriever, prompt and model chain |
| Model serving | vLLM with Llama 3.1 8B Instruct | Answers generated on internal GPUs |
| Evaluation | scikit-learn, seqeval, RAGAS | F1, entity F1, faithfulness |
| App | FastAPI, Docker | API and packaging |

## Limits and roadmap

**Limits of this prototype**

- The intent model is a keyword stand-in, and answers are written from templates. No large language model is called.
- Dense retrieval and reranking are shown as planned steps; they do not run in the browser.
- The spec matcher compares only vCPU, memory and GPU count, not CPU generation, network or disk speed.
- English only.

**Roadmap**

1. Connect a self-hosted LLM and dense retrieval behind a small API.
2. Pilot with a group of new solutions engineers for one quarter.
3. Add support tickets as a data source, with redaction.
4. Support questions in Arabic.
