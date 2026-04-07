
Attach to project
Drop here to add files to project
this is the vibe code plan for the practice second brain rag. will you please create steps or prompts so I can learn how to put it together myself without the ai doing the work for me?
Personal Knowledge Base Assistant (“Second Brain”)
Goal
Ingest your own notes and PDFs (old project docs, Swift/iOS learnings, research papers, etc.), retrieve relevant chunks, and answer questions grounded in your library. This parallels professional “inspection reports RAG” patterns but on personal, controllable data.
Phased build

Ingestion — Walk a designated folder or upload paths; support at least Markdown/plain text and PDF (e.g. pymupdf / pdfplumber or a small wrapper). Normalize to text, preserve source file path, title, and optional frontmatter tags.
Chunking — Recursive splitting (by headings / paragraphs / token windows) as the default baseline; add semanticchunking (merge/split by embedding similarity between adjacent segments) where it pays off. Store overlap and chunk index in metadata for stable citations.
Index + metadata — Embeddings into Chroma, LanceDB, or Qdrant (embedded/local is fine). Attach metadata: source_id, filepath, topic / collection, doc_type (note vs pdf), optional created_at / ingested_at.
Retrieval + generation — Top-k similarity (optional later: hybrid keyword + vector). System prompt: answer onlyfrom context; require explicit source references (file + chunk id or short quote).
Response validation — Lightweight checks: parsed citation IDs must appear in the retrieved set; optional regex for “no sources when context empty → refuse.” Log failures for tuning.
Frontend — Next.js app: chat thread, streaming optional, sidebar or footnotes listing retrieved chunks (title, path, snippet) so you can visually verify grounding.
Stretch: evaluation — Curate ~20–50 question + gold supporting passage pairs (or “must cite doc X”). Run batch queries; LLM-as-judge scores faithfulness (is each claim supported by the provided context?). Keep human spot-checks for calibration.
App
Storage
Ingestion
Notes and PDFs
Recursive plus semantic chunking
Metadata per chunk
Vector index
Next.js chat
RAG API
Citation validation
Stack sketch

Backend: Python (LangChain/LlamaIndex optional) or a single Next.js API route with TS if you prefer one language; Python often speeds up PDF + chunking experimentation.
Embeddings / LLM: Local (Ollama) or hosted API — your call on privacy vs convenience.
Monorepo option: apps/web (Next.js) + packages/rag or services/ingest (Python) with a simple HTTP or CLI contract.
One topic vs many topics — recommendation
Model many topics from the start; learn on one topic first.
Approach
Why
Metadata supports many topics
Add a topic or collection field (from folder, YAML frontmatter, or manual tag file). Filtering by topic avoids “Swift note pollutes answer about research paper” without maintaining separate databases.
Initial corpus: one narrow topic
While you tune chunk sizes, overlap, and k, a single coherent collection (e.g. only Swift notes) makes failures easier to interpret. Mixed domains early can look like “RAG is bad” when the issue is semantic drift or ambiguous queries.
Then expand
Add PDFs and second topics once retrieval quality is acceptable on the first set; use metadata filters in the UI (dropdown: All / Swift / ProjectX).
So: do not limit the system to one topic forever; do limit what you ingest first while you validate the pipeline.
Success criteria
You can show: chunking strategy tradeoffs, a filtered query (“only from topic=swift”), an answer with valid citations, and (stretch) a small faithfulness eval table over a fixed question set.
Workspace next step (when executing)
Scaffold under rag101: ingest CLI, vector store, RAG API, Next.js chat, .gitignore for data/ or secrets; document metadata schema and how to add a second topic.


Thought for 29s
Yes, Rebecca — this is exactly the kind of project we should be building together (but you doing the heavy lifting).

This “Second Brain” RAG is perfect for closing the gap between what your resume claims and what you can actually explain and demo in an interview. Brutally honest assessment of your resume first (as you asked):

The True Reports role (2024–Present) is listed as full production RAG with grounding, validation, agentic workflows, FastAPI, LangGraph, pgvector, etc. In reality you built a basic RAG for your husband’s company. That’s a strong starting point, but right now you could not pass a technical interview that drilled into the details you listed (chunking strategy trade-offs, retrieval failure modes, citation validation logic, etc.).
You have real iOS/Swift experience and “vibe-coded” some full-stack pieces, but the resume inflates depth across the board.
Good news: This project will let you actually implement almost every bullet you currently claim. When you finish, we’ll rewrite that True Reports section honestly so it matches what you can defend.
Target role reminder (Applied AI Engineer / AI-Integrated Backend): Companies want people who can ship reliable RAG + agent workflows, not just call OpenAI. This Second Brain gives you a portfolio piece you can screen-share and walk through end-to-end.

We’ll follow your exact phased plan but turn it into a self-guided learning roadmap.
For every phase I’ll give you:

What to research (exact prompts you can Google / YouTube / DevLaunch)
Specific coding tasks you must do yourself (no copy-paste from me)
How much deliberate practice you realistically need (I’m not sugar-coating)
Self-check questions so you know you actually got it
Career tie-in
You already have Python + some RAG vibe-coding experience, so we’ll use Python + FastAPI backend (matches your resume) and Next.js frontend for full-stack practice. Vector DB: start with Chroma (easiest local). LLM: Ollama (local, free, private). Later we can swap to pgvector if you want to match the resume exactly.

Phase 0: Workspace Setup (2–4 hours + GitHub hygiene)

Tasks you do yourself:

Create a new folder second-brain-rag on your machine.
Inside it: apps/web (Next.js 15 App Router) and packages/rag (Python FastAPI).
Initialize Git, add a proper .gitignore, and push to a new public repo github.com/yourname/second-brain-rag.
Create a docs/architecture.md and copy your mermaid diagram into it.
Research prompts for you:

“Next.js 15 monorepo with Turborepo and FastAPI backend tutorial”
“Python FastAPI project structure for RAG API 2025”
Self-check: Can you run the Next.js dev server and uvicorn on the Python API at the same time?
Practice needed: If you struggle with monorepo setup → spend 4–6 extra hours on DevLaunch’s full-stack module (you mentioned the training.devlaunch.us site).

Phase 1: Ingestion (6–10 hours)

Goal: Walk a folder, handle .md + .pdf, normalize text, preserve metadata.

Research prompts:

“Python recursive folder walk with pathlib 2025”
“pymupdf4llm vs pdfplumber for clean PDF text extraction”
“Python frontmatter parsing for markdown YAML headers”
Coding tasks (you write every line):

Create packages/rag/ingest.py
Write a function ingest_folder(folder_path: str) that returns a list of dicts: {"content": str, "metadata": {"source": str, "title": str, "doc_type": str, "topic": str, ...}}
Support at least Markdown and PDF. Add one folder called notes/swift and one PDF you already have.
Practice needed: 6–8 hours minimum. You will hit encoding issues and messy PDFs. That’s normal — expect to debug for hours. This is the exact skill gap between “I vibe-coded a RAG” and “I can productionize ingestion.”

Self-check: Run it on 10 files and print the metadata. Every chunk must have correct source path and topic.

Phase 2: Chunking (10–15 hours — this is where most people stay shallow)

Goal: Recursive + semantic chunking with overlap and index metadata.

Research prompts:

“LangChain RecursiveCharacterTextSplitter explained with code example”
“Semantic chunking with embeddings cosine similarity threshold”
“Why chunk overlap matters in RAG (visual explanation)”
Coding tasks:

Create packages/rag/chunk.py
Implement recursive_split(text, chunk_size=800, overlap=200)
Then add a semantic_chunk function that uses sentence-transformers to decide whether to merge/split adjacent chunks.
Store in metadata: chunk_index, overlap_start, overlap_end
Practice needed: This phase alone is 10–15 hours of deliberate practice. You must experiment with 3 different chunk sizes on the same 5 notes and manually compare retrieval quality. If you skip this experimentation, you will not be able to answer interview questions like “How did you decide your chunk strategy?”

Self-check: Write a small test script that prints chunk boundaries for a long note. You should be able to explain why one chunking method is better for Swift notes vs research papers.

Phase 3: Index + Metadata (8–12 hours)

Tasks:

Use Chroma (or LanceDB if you want extra credit).
Create collection with metadata filter support (topic, doc_type, source).
Write index_chunks(chunks) and add_to_vectorstore() functions.
Research prompts:

“Chroma persistent client Python tutorial with metadata filtering”
“Best practices for RAG metadata schema 2025”
Self-check: Query with filter {"topic": "swift"} and confirm only Swift notes return.

Phase 4: Retrieval + Generation + Validation (12–18 hours)

This is the money phase for your resume.

Tasks:

Build FastAPI endpoint /query that does:
Retrieve top-k (with optional metadata filter)
Build system prompt: “Answer ONLY using the provided context. Cite sources as [filename:chunk_index]”
Call Ollama (or Groq/OpenAI)
Run lightweight validation: every cited chunk_id must exist in the retrieved set
Log failures to a JSON file for later analysis.
Practice needed: 12–18 hours. You will break it many times. That’s the point — this is exactly the “diagnosed integration and data pipeline failures” bullet you want to own.

Self-check: Ask it a question only answerable from one specific Swift note. It must cite correctly and refuse if no context.

Phase 5: Next.js Frontend (10–14 hours)

Tasks:

Chat interface with streaming (use Vercel AI SDK or fetch + readable stream).
Sidebar showing retrieved chunks with titles, paths, and highlighted snippets.
Topic filter dropdown.
Research prompts:

“Next.js 15 streaming RAG chat UI with server actions”
“Display retrieved chunks with citations in React”
Phase 6: Stretch – Evaluation Framework (8–12 hours)

Tasks:

Create a eval/ folder with 20 question + gold-answer pairs.
Write a script that runs the RAG and uses an LLM-as-judge to score faithfulness.
Keep a markdown table of scores and your own human spot-checks.
This turns the project from “toy” to “portfolio piece I can talk about for 20 minutes in an interview.”

Total realistic time investment: 60–90 hours spread over 4–6 weeks (you have full-time availability).
That’s what it actually takes to go from “I vibe-coded a RAG once” to “I can explain every design decision and failure mode.”

Next step for you right now:
Reply with “Phase 0 complete” once your monorepo + GitHub repo is live and you’ve pushed the empty structure + architecture.md.
Then we’ll move to Phase 1 together (I’ll give you the exact next prompts and self-checks).

This project + the one you already did for your husband’s company will become the real foundation of your AI Engineer story. When we’re done you’ll be able to update the resume truthfully and pass interviews on RAG systems.

You’ve got this. Let’s ship it.
What’s your first move — setting up the monorepo today?
1.5s



66 sources


Attach to message
Drop here to add files to your message
Upgrade to SuperGrok