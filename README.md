## Overview

This repository documents a set of intelligent automation pipelines
designed to eliminate manual bottlenecks in content production workflows.

Each system is architected around a core principle:
**LLMs are unreliable in isolation. Systems make them reliable.**

Instead of single-prompt solutions, every workflow here uses
multi-stage AI orchestration — where each LLM call has a specific,
scoped responsibility, and outputs are validated before moving
to the next stage.

---

## System Architecture
```
Webhook Trigger
      │
      ▼
Data Ingestion (Google Sheets)
      │
      ▼
Pre-processing & Prompt Construction (Code Node)
      │
      ▼
LLM Stage 1 — Generation (Gemini)
      │
      ▼
LLM Stage 2 — Refinement & Formatting (OpenAI)
      │
      ▼
Output Parsing & Validation (Code Node)
      │
      ▼
Structured Output → Google Sheets / Drive / ElevenLabs
```

This pattern ensures:
- Each stage has a single responsibility
- Failures are isolated and debuggable
- Output format is enforced at every transition

---
##  Why n8n?

- Visual orchestration of complex workflows  
- Faster debugging  
- Scalable automation pipelines 

## Tech Stack

| Layer | Tool | Role |
|-------|------|------|
| Orchestration | n8n | Pipeline execution & node routing |
| LLM — Generation | Google Gemini | Primary content generation |
| LLM — Refinement | OpenAI GPT | Output formatting & correction |
| Voice Synthesis | ElevenLabs | Script-to-audio conversion |
| Data Layer | Google Sheets | Structured I/O storage |
| File Storage | Google Drive | Audio & asset management |
| Triggers | Webhooks + REST | External pipeline activation |

---

## Workflows

---

### 01 — AI Content Generation Pipeline

**Problem:** Manual content creation was slow, inconsistent,
and didn't scale across subjects and grade levels.

**Solution:** An end-to-end pipeline that takes structured
inputs (subject, grade, topic) and produces production-ready
vocational content using a chained LLM approach.

**Key Engineering Decisions:**
- Prompt constructed dynamically using pre-processing code nodes
- Gemini used for generation; OpenAI used for formatting enforcement
- Output validated against expected schema before writing to Sheets


---

### 02 — Simulation Script Generator

**Problem:** Writing structured, step-based simulation scripts
manually was time-consuming and error-prone.

**Solution:** A pipeline that accepts a content brief via webhook,
generates a structured simulation script with numbered steps,
scene descriptions, and on-screen text — all in a consistent format.

**Key Engineering Decisions:**
- Tagged prompt architecture (`⟦S1⟧...⟦/S1⟧`) for deterministic parsing
- Regex-based output parser to extract structured fields reliably
- Handles edge cases: empty fields, encoding issues, whitespace normalization


---

### 03 — Translation Automation Pipeline

**Problem:** Translating EdTech content to Hindi required
domain expertise — standard translation APIs produced
academically incorrect outputs.

**Solution:** A multi-stage pipeline that translates English
storyboard content to Hindi while enforcing:
- NCERT syllabus terminology alignment
- 1:1 sentence correspondence (no merging, splitting, or omissions)
- Preservation of symbols, units, and equations in English
- Academic tone calibrated to the target grade level

**Key Engineering Decisions:**
- Reference box system — domain-specific terms injected into the prompt
- Segment tagging ensures structural integrity across 50+ scene blocks
- Output parsed scene-by-scene and written back to structured Sheets


---

### 04 — AI Audio Generation Pipeline

**Problem:** Converting finalized scripts to audio was a
manual, file-by-file process with no version tracking.

**Solution:** A pipeline that reads scripts from Google Sheets,
sends them to ElevenLabs for voice synthesis, and automatically
stores the generated audio files in Google Drive —
with metadata written back to the source sheet.

**Key Engineering Decisions:**
- Row-by-row processing with error handling per scene
- Drive folder structure auto-managed by the pipeline
- Fully stateless — can be re-triggered without duplicate outputs

---

## Engineering Principles Applied

**Single Responsibility per Node**
Each node in the pipeline does one thing.
This makes debugging fast and changes isolated.

**Deterministic Output Parsing**
LLM outputs are never trusted as-is.
Every output goes through a parsing layer that enforces structure
before it touches downstream systems.

**Prompt as a Contract**
Prompts are written as strict contracts — defining not just
what to generate, but what format, what to preserve,
what to never do, and how to handle edge cases.

**Fail Loudly, Recover Gracefully**
Pipelines are designed to surface failures immediately
rather than silently passing bad data downstream.

---

## Challenges & Engineering Solutions

| Challenge | Root Cause | Solution |
|-----------|-----------|----------|
| Inconsistent LLM output format | Non-deterministic generation | Tagged prompt architecture + regex parser |
| Sentences merged or split in translation | LLM paraphrasing tendency | Explicit 1:1 mapping contract in prompt |
| Pipeline failures mid-batch | Single point of failure design | Modular node isolation + per-item error handling |
| Large content batches timing out | Sequential processing bottleneck | Chunked processing with batch size control |
| Symbol/unit corruption in translation | LLM over-translating | Hard rules in prompt + post-processing validation |

---

## Repository Structure
```
/
├── Translation_tool_Copy.json       # Hindi translation pipeline
├── SimScript_Gen_v2.json        # Simulation script generator
├── script_generator_final.json  # AI content generation pipeline
├── AudioGenTool.json                # Audio generation pipeline
├── screenshots/                     # Workflow screenshots
└── README.md
```

---

## Setup & Usage

### Prerequisites
- n8n instance (self-hosted or cloud)
- API keys: OpenAI, Google Gemini, ElevenLabs
- Google Cloud OAuth configured for Sheets & Drive

### Import Steps
1. Clone this repository
2. Open your n8n instance
3. Import each `.json` workflow file
4. Configure credentials for each service
5. Update webhook paths to match your environment
6. Activate and trigger via webhook or manual run

> **Note:** All credentials have been removed from workflow files.
> No API keys, credential IDs, or webhook UUIDs are present
> in this repository.

---
The difference:

| Using AI Tools | Building AI Systems |
|----------------|-------------------|
| One prompt, one output | Chained stages with validation |
| Manual error fixing | Automated error handling |
| Works in demos | Works in production |
| Prompt engineering | System design + prompt engineering |

The hardest problems weren't about AI —
they were about **data flow, failure handling, and output reliability.**
That's what separates a prototype from a production pipeline.

---
🙋‍♀️ Author


Priyanka Koyalkar
|Gold Medalist in Statistics | AI Developer | MCA Graduate 

https://www.linkedin.com/in/k-p-priyanka 

