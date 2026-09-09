# Tech Stack — Autonomous AI-Based Learning Platform

## 1. Architecture

**Architecture Style:** Modular Agentic AI + RAG + Graph-based Student Memory

```text
Frontend
   ↓
Backend API
   ↓
Agent Orchestrator
   ├── Syllabus Analyzer
   ├── Learning Manager
   ├── Teaching Agent
   ├── Assessment Agent
   ├── Evaluation Agent
   ├── Revision Agent
   └── Coding Agent
          ↓
   ┌──────┼───────────┐
   ↓      ↓           ↓
Vector  Graph       SQL DB
 DB     Database
   ↓      ↓
Documents Student
         Memory
          ↓
     LLM / Tools
```

---

# 2. Frontend

## Framework

**Next.js + React + TypeScript**

### Why

* Component-based architecture
* Good support for complex learning dashboards
* Server/client rendering flexibility
* Type safety
* Easy API integration

## UI

* Tailwind CSS
* shadcn/ui
* Lucide Icons

## Main Frontend Areas

```text
/
├── Dashboard
├── Subjects
├── Subject Details
├── Chapter
├── Topic
├── Lesson
├── Quiz
├── Coding Environment
├── AI Chat
├── Progress / Mastery
└── Revision Summary
```

---

# 3. Backend

## Framework

**Python + FastAPI**

### Why

* Strong AI/ML ecosystem
* Easy integration with LLMs
* Native async support
* Easy API development
* Excellent support for Python-based document and coding infrastructure

## Backend Responsibilities

* Authentication
* File uploads
* Document processing
* OCR orchestration
* Curriculum generation
* Agent orchestration
* Quiz generation
* Quiz evaluation
* Student mastery updates
* Chatbot
* Coding execution
* Revision generation
* API management

---

# 4. Primary LLM

## Initial Model

**Qwen**

The initial deployment should use a locally hosted Qwen model.

Possible model selection will depend on available hardware and context-length requirements.

## LLM Responsibilities

* Understand extracted document content
* Build curriculum
* Generate notes
* Generate quizzes
* Evaluate answers
* Generate hints
* Identify weaknesses
* Plan learning activities
* Generate explanations
* Handle syllabus chatbot
* Generate revision summaries
* Assist coding assessment

## LLM Abstraction

The application should expose a model abstraction layer rather than directly coupling the codebase to Qwen.

```text
LLM Interface
     │
     ├── Qwen Local
     ├── Future Cloud Model
     └── Future Alternative Model
```

This allows future migration from local inference to cloud inference without redesigning the application.

---

# 5. Local LLM Runtime

## Recommended Initial Runtime

**Ollama**

### Purpose

* Local model management
* Easy development setup
* Local inference
* Simple API integration

Alternative runtime options can be evaluated later:

* vLLM
* llama.cpp
* Hugging Face Transformers

---

# 6. Agent Orchestration

## Recommended

**LangGraph**

### Purpose

Represent the learning system as a stateful agent workflow.

Example:

```text
START
  ↓
Analyze Student State
  ↓
Select Next Action
  ↓
Teach / Quiz / Code / Revise
  ↓
Evaluate
  ↓
Update Mastery
  ↓
Replan
  ↓
END / Continue
```

## Agent State

The state passed through the agent workflow may contain:

```text
student_id
subject_id
current_chapter
current_topic
current_subtopic
mastery_state
recent_attempts
weak_topics
current_difficulty
learning_mode
conversation_context
retrieved_content
next_action
```

---

# 7. Document Processing

## Supported Input

* PDF
* PPT/PPTX
* Multiple documents per subject

## PDF Processing

Recommended libraries/services:

* PyMuPDF
* python-pptx
* pdfplumber where required

## Processing Pipeline

```text
Upload
 ↓
File Validation
 ↓
Page Extraction
 ↓
Text Extraction
 ↓
OCR / Vision
 ↓
Content Cleaning
 ↓
LLM Structuring
```

---

# 8. OCR

## Initial OCR

**PaddleOCR**

### Purpose

* Printed text extraction
* Scanned document processing
* Handwritten-content preprocessing where supported
* Bounding-box/location information

Additional OCR/vision components may be introduced depending on handwriting quality.

---

# 9. Vision Processing

## Purpose

Used for pages containing:

* Handwritten notes
* Diagrams
* Tables
* Mathematical notation
* Images with embedded educational information

Pipeline:

```text
PDF Page
   ↓
Render Page as Image
   ↓
OCR
   ↓
Vision Processing
   ↓
Structured Content
   ↓
LLM
```

## Confidence Handling

Every processed page should have a processing confidence score.

```text
High confidence
      ↓
Continue

Low confidence
      ↓
Ask user to re-upload page
```

---

# 10. Embeddings

## Purpose

Convert educational content into vector representations for semantic retrieval.

Initial local options:

* sentence-transformers
* BGE embedding models
* Qwen-compatible embedding model where appropriate

The embedding model should be configurable independently of the primary LLM.

---

# 11. Vector Database

## Initial Recommendation

**Qdrant**

### Stores

* Document chunks
* Page content
* Notes
* Source metadata
* Topic references
* Embeddings

Example metadata:

```text
document_id
page_number
subject_id
chapter_id
topic_id
subtopic_id
source_type
content_type
```

---

# 12. Graph Database

## Initial Recommendation

**Neo4j**

### Purpose

Store:

### Curriculum Graph

```text
Subject
 ↓
Chapter
 ↓
Topic
 ↓
Subtopic
```

and relationships:

```text
Topic A
   └── prerequisite_for → Topic B
```

### Student Mastery Graph

```text
Student
   ↓
Topic
   ├── mastery
   ├── attempts
   ├── difficulty
   ├── weak_concepts
   ├── last_attempt
   └── revision_state
```

---

# 13. Relational Database

## Initial Recommendation

**PostgreSQL**

### Stores

* Users
* Authentication metadata
* Subjects
* Documents
* Quizzes
* Questions
* Attempts
* Sessions
* Chat history metadata
* Application configuration
* Agent execution metadata

PostgreSQL should handle transactional application data while Neo4j handles relationships and Qdrant handles semantic retrieval.

---

# 14. Database Architecture

```text
                 Backend
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
 PostgreSQL      Neo4j        Qdrant
   SQL Data    Graph Data    Vector Data
       │            │            │
       └────────────┼────────────┘
                    ↓
                AI Agents
```

---

# 15. RAG Architecture

The chatbot and learning agents should use Retrieval-Augmented Generation.

```text
User Question
      ↓
Query Understanding
      ↓
Embedding
      ↓
Qdrant Search
      ↓
Relevant PDF Content
      ↓
Student Mastery Context
      ↓
LLM
      ↓
Grounded Response
```

## Source Attribution

Retrieved content should preserve:

* Document
* Page
* Chapter
* Topic

This allows responses to identify where information came from.

---

# 16. Chatbot

## Technology

* FastAPI
* LangGraph
* Qwen
* Qdrant
* PostgreSQL / Redis for conversation/session state

## Context

The chatbot receives:

```text
Current Lesson
+
Relevant PDF Content
+
Curriculum Graph
+
Student Mastery Graph
+
Conversation Context
```

External information should be explicitly marked when permitted.

---

# 17. Quiz Engine

## Initial Question Type

**MCQ**

## Difficulty

```text
Easy
Medium
Hard
```

## Quiz generation uses:

* Topic
* Subtopic
* Source material
* Student mastery
* Previous questions
* Previous mistakes
* Current difficulty

## Quiz Flow

```text
Generate
 ↓
Validate
 ↓
Store
 ↓
Present
 ↓
Evaluate
 ↓
Update Mastery
```

---

# 18. AI Evaluation

The LLM evaluates quiz responses.

Evaluation should generate structured output such as:

```json
{
  "correct": false,
  "topic": "Recursion",
  "concept": "Base Case",
  "reason": "The base condition is missing.",
  "correct_answer": "B",
  "difficulty": "Medium"
}
```

Structured outputs should be validated before being saved.

---

# 19. Hint System

Six fixed hint levels:

```text
1 → Conceptual direction
2 → Recall relevant principle
3 → Narrow the problem
4 → Strong conceptual clue
5 → Near-complete approach
6 → Full solution
```

The hint engine should track:

```text
hint_level_used
time_before_hint
number_of_hints
question_attempt
```

This information can contribute to mastery estimation.

---

# 20. Student Mastery Engine

The system should maintain a mastery score for each topic/subtopic.

Example:

```json
{
  "topic": "Binary Search",
  "mastery": 0.78,
  "difficulty": "Medium",
  "attempts": 5,
  "weak_concepts": [
    "boundary conditions"
  ],
  "last_revised": "2026-09-09"
}
```

Mastery updates should consider:

* Correctness
* Difficulty
* Repeated attempts
* Hint dependency
* Historical performance
* Recency
* Topic relationships

---

# 21. Learning Modes

The backend should expose one common learning engine with three decision modes.

## Guided

```text
AI recommends
        ↓
User decides
```

## Manual

```text
User selects
        ↓
System executes
```

## Automatic

```text
AI observes
 ↓
AI plans
 ↓
AI executes
 ↓
AI evaluates
 ↓
AI replans
```

---

# 22. Coding Environment

## Languages

Initial support:

* C
* C++
* Java
* Python

## Important Requirement

Code execution must happen inside an isolated sandbox.

```text
Student Code
     ↓
Sandbox
     ↓
Compiler / Interpreter
     ↓
Test Cases
     ↓
Execution Result
     ↓
Code Evaluation Agent
```

The coding environment is available only for relevant computer-science lessons.

---

# 23. Compiler Infrastructure

Possible initial technologies:

* Docker
* gVisor where available
* Firecracker for stronger isolation in future

Each execution should have:

* CPU limit
* Memory limit
* Time limit
* Process limit
* Filesystem isolation
* Network restrictions

---

# 24. Coding Evaluation

The coding module should evaluate:

* Compilation success
* Runtime success
* Output correctness
* Test case performance
* Basic complexity considerations
* Topic-specific mistakes

Example:

```text
Submission
 ↓
Compile
 ↓
Run Tests
 ↓
Collect Results
 ↓
Analyze Failure
 ↓
Generate Feedback
 ↓
Update Student Mastery
```

---

# 25. Agent Evaluation / AI Harness

The platform should include an AI evaluation and observability layer.

## Initial Options

One of:

* LangSmith
* Arize Phoenix
* OpenTelemetry-based custom tracing

The final implementation should prioritize a free/local setup during development.

## Harness Responsibilities

* Trace agent runs
* Monitor tool calls
* Evaluate outputs
* Detect regressions
* Validate generated quizzes
* Evaluate grounding
* Measure hint quality
* Measure agent decision quality

---

# 26. Cache / Session Layer

## Recommended

**Redis**

Used for:

* Session state
* Temporary agent state
* Rate limiting
* Caching
* Streaming responses
* Short-lived processing state

Redis is optional for the earliest prototype but recommended for a production-oriented architecture.

---

# 27. Background Processing

Document processing should not block normal API requests.

Recommended:

**Celery + Redis**

or an equivalent background job architecture.

Example:

```text
PDF Upload
   ↓
Create Processing Job
   ↓
Background Worker
   ↓
OCR
   ↓
Embedding
   ↓
Curriculum Generation
   ↓
Graph Creation
   ↓
Ready
```

---

# 28. Authentication

Initial options:

* JWT-based authentication
* FastAPI security utilities
* PostgreSQL user store

Future option:

* OAuth / Google login

---

# 29. API Architecture

REST API initially.

Example:

```text
/api/auth
/api/subjects
/api/documents
/api/curriculum
/api/lessons
/api/quizzes
/api/attempts
/api/mastery
/api/chat
/api/learning
/api/coding
/api/revision
```

WebSockets/SSE should be used where real-time streaming is required.

---

# 30. Observability

## Backend

* Python logging
* OpenTelemetry
* Prometheus
* Grafana

## AI

* Agent traces
* LLM latency
* Token usage
* Retrieval quality
* Agent failures
* Tool execution failures
* Evaluation scores

---

# 31. File Storage

## Initial Local Deployment

Use local filesystem/object storage abstraction.

Example:

```text
/storage
   /subjects
   /documents
   /pages
   /generated
   /revisions
```

## Future Cloud

Make storage interface-compatible with:

* S3
* Cloudflare R2
* Supabase Storage

No application-level dependency should assume local storage permanently.

---

# 32. Deployment — Initial

The first version should be fully local to minimize cost.

Recommended:

```text
Docker Compose
│
├── Next.js
├── FastAPI
├── PostgreSQL
├── Neo4j
├── Qdrant
├── Redis
├── Ollama
├── Worker
└── Code Sandbox
```

---

# 33. Deployment — Future Cloud

The architecture should allow migration to:

```text
Frontend
   ↓
Cloud Backend
   ↓
Managed PostgreSQL
Managed Neo4j
Managed Vector DB
Cloud Object Storage
Cloud LLM / GPU
Isolated Code Execution
```

The application should not require major architectural changes during migration.

---

# 34. Development Tools

## Version Control

**Git + GitHub**

## Containerization

**Docker + Docker Compose**

## API Testing

* Postman
* Swagger/OpenAPI from FastAPI

## Code Quality

### Python

* Ruff
* Black
* Pytest
* MyPy

### TypeScript

* ESLint
* Prettier
* Vitest / Jest
* Playwright

---

# 35. Testing Strategy

## Unit Tests

Test:

* OCR preprocessing
* Curriculum parsing
* Mastery calculations
* Quiz scoring
* Graph updates
* API functions

## Integration Tests

Test:

```text
PDF
 ↓
OCR
 ↓
LLM
 ↓
Curriculum
 ↓
Graph
 ↓
Quiz
 ↓
Evaluation
 ↓
Mastery
```

## Agent Evaluation

Create fixed evaluation scenarios for:

* Correct topic selection
* Weak-topic detection
* Difficulty selection
* Hint progression
* Grounded answers
* Curriculum adherence
* Automatic-mode planning

---

# 36. Recommended Final Stack

| Layer               | Technology                          |
| ------------------- | ----------------------------------- |
| Frontend            | Next.js + React + TypeScript        |
| UI                  | Tailwind CSS + shadcn/ui            |
| Backend             | Python + FastAPI                    |
| Agent Orchestration | LangGraph                           |
| Primary LLM         | Qwen                                |
| Local LLM Runtime   | Ollama                              |
| OCR                 | PaddleOCR                           |
| PDF Processing      | PyMuPDF                             |
| PPT Processing      | python-pptx                         |
| Embeddings          | BGE / Sentence Transformers         |
| Vector DB           | Qdrant                              |
| Graph DB            | Neo4j                               |
| SQL DB              | PostgreSQL                          |
| Cache/Queue         | Redis                               |
| Background Jobs     | Celery                              |
| Code Sandbox        | Docker initially                    |
| AI Observability    | OpenTelemetry + Phoenix/LangSmith   |
| Authentication      | JWT initially                       |
| API                 | REST + SSE/WebSocket where required |
| Containerization    | Docker Compose                      |
| Version Control     | Git + GitHub                        |
| Testing             | Pytest + Vitest + Playwright        |

---

# 37. Design Principle

The technology stack should remain **model-agnostic, storage-agnostic, and deployment-agnostic** wherever practical.

The system should be able to evolve from:

```text
Local Qwen
+
Local Databases
+
Local Storage
```

to:

```text
Cloud LLM
+
Managed Databases
+
Cloud Storage
+
Scalable Code Execution
```

without changing the core product logic.
