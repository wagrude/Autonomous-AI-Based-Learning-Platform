# FEATURES.md

# Autonomous AI-Based Learning Platform

---

# PHASE 1 — PROJECT FOUNDATION

## Objective

Set up the complete local development foundation so the application, databases, LLM runtime, and development environments can communicate reliably.

## Functions to Be Done

### 1.1 Repository Setup

**Work**

* Create monorepo structure
* Configure Git
* Configure `.gitignore`
* Add project documentation
* Define environment-variable structure
* Define development conventions

**Tech Stack**

* Git
* GitHub

**Purpose**

Provides the base repository in which all future components will be developed.

**Role in Bigger Picture**

Every later phase builds on this repository and its shared conventions.

---

### 1.2 Frontend Bootstrap

**Work**

* Create Next.js application
* Configure TypeScript
* Configure Tailwind CSS
* Configure shadcn/ui
* Create basic application layout
* Create navigation structure
* Create placeholder dashboard

**Tech Stack**

* Next.js
* React
* TypeScript
* Tailwind CSS
* shadcn/ui

**Purpose**

Create the user-facing foundation.

**Role in Bigger Picture**

All future learning features—subjects, lessons, quizzes, chatbot, coding environment, and progress—will be exposed through this frontend.

---

### 1.3 Backend Bootstrap

**Work**

* Create FastAPI application
* Configure project structure
* Create API routing system
* Add health endpoint
* Configure CORS
* Configure environment loading
* Create service-layer structure

**Tech Stack**

* Python
* FastAPI
* Pydantic

**Purpose**

Create the central backend through which the frontend communicates with AI systems, databases, document processing, and learning services.

**Role in Bigger Picture**

The backend becomes the main integration layer for the entire platform.

---

### 1.4 PostgreSQL Setup

**Work**

* Deploy local PostgreSQL
* Configure connection
* Create migration system
* Create initial database structure
* Verify backend connectivity

**Tech Stack**

* PostgreSQL
* SQLAlchemy
* Alembic
* Docker

**Purpose**

Provide persistent structured application storage.

**Role in Bigger Picture**

Later phases store users, subjects, documents, lessons, quizzes, attempts, chat history, coding submissions, and other transactional data here.

---

### 1.5 Neo4j Setup

**Work**

* Deploy Neo4j locally
* Configure backend connection
* Create graph access layer
* Test node creation and querying

**Tech Stack**

* Neo4j
* Python Neo4j driver
* Docker

**Purpose**

Provide graph storage for curriculum relationships and student mastery.

**Role in Bigger Picture**

Later phases use Neo4j for:

```text
Subject
 → Chapter
 → Topic
 → Subtopic
```

and:

```text
Student
 → mastery / weakness / prerequisite relationships
```

---

### 1.6 Qdrant Setup

**Work**

* Deploy Qdrant locally
* Configure connection
* Create collection-management layer
* Test vector insertion and retrieval

**Tech Stack**

* Qdrant
* Python Qdrant client
* Docker

**Purpose**

Provide semantic retrieval infrastructure.

**Role in Bigger Picture**

Later phases store embeddings of uploaded educational content so the chatbot and agents can retrieve relevant source material.

---

### 1.7 Redis Setup

**Work**

* Deploy Redis locally
* Configure backend connection
* Test cache operations
* Test basic queue operations

**Tech Stack**

* Redis
* Docker

**Purpose**

Provide temporary state, caching, and background-job infrastructure.

**Role in Bigger Picture**

Used later for:

* Agent temporary state
* Processing queues
* Caching
* Rate limiting
* Background document processing

---

### 1.8 Local LLM Runtime

**Work**

* Install Ollama
* Run initial Qwen model
* Configure backend → Ollama communication
* Create generic LLM service interface
* Test prompt/response flow

**Tech Stack**

* Ollama
* Qwen
* Python

**Purpose**

Establish local AI inference.

**Role in Bigger Picture**

Qwen becomes the initial reasoning engine for:

* Curriculum understanding
* Lesson generation
* Quiz creation
* Quiz evaluation
* Hints
* Chatbot
* Personalization
* Agentic planning

---

### 1.9 LLM Abstraction Layer

**Work**

Create a provider-independent interface such as:

```text
LLMProvider
├── generate()
├── generate_structured()
└── stream()
```

Then implement:

```text
QwenProvider
```

**Tech Stack**

* Python
* Pydantic

**Purpose**

Prevent the product from becoming permanently tied to one model/runtime.

**Role in Bigger Picture**

Allows future migration:

```text
Local Qwen
        ↓
Cloud LLM
```

without rewriting the entire application.

---

### 1.10 Docker Compose Infrastructure

**Work**

Create a development environment containing:

```text
frontend
backend
postgres
neo4j
qdrant
redis
ollama
```

**Tech Stack**

* Docker
* Docker Compose

**Purpose**

Allow the complete local platform to be started consistently.

**Role in Bigger Picture**

Provides the single reproducible development environment used throughout all future phases.

---

### 1.11 Shared Configuration

**Work**

Create centralized configuration for:

* Database URLs
* Redis URL
* Neo4j credentials
* Qdrant configuration
* Ollama URL
* Model name
* File storage path
* Application environment

**Tech Stack**

* Pydantic Settings
* `.env`

**Purpose**

Keep infrastructure configuration separate from application code.

**Role in Bigger Picture**

Makes local development, testing, and future cloud migration easier.

---

### 1.12 Logging Foundation

**Work**

* Configure application logging
* Define log levels
* Add request logging
* Add service error logging
* Establish structured logging format

**Tech Stack**

* Python logging
* FastAPI middleware

**Purpose**

Make system behavior and failures observable from the beginning.

**Role in Bigger Picture**

Required later for debugging OCR, LLM calls, agents, quiz generation, coding execution, and background processing.

---

### 1.13 API Contract Foundation

**Work**

Define initial API conventions:

```text
/api/v1/...
```

Establish:

* Request schemas
* Response schemas
* Error format
* HTTP status conventions

**Tech Stack**

* FastAPI
* Pydantic
* OpenAPI

**Purpose**

Create a consistent interface between frontend and backend.

**Role in Bigger Picture**

Every future feature becomes a new API module without changing the basic communication pattern.

---

### 1.14 Testing Foundation

**Work**

Create initial tests for:

* Backend health
* Database connectivity
* Redis connectivity
* Neo4j connectivity
* Qdrant connectivity
* LLM connectivity
* Basic frontend build

**Tech Stack**

* Pytest
* Vitest
* Playwright

**Purpose**

Ensure the foundation works before adding complex AI behavior.

**Role in Bigger Picture**

Prevents infrastructure failures from being confused with later AI/agent failures.

---

### 1.15 CI Foundation

**Work**

Set up basic automated checks:

```text
Push
 ↓
Install dependencies
 ↓
Lint
 ↓
Unit tests
 ↓
Build
```

**Tech Stack**

* GitHub Actions
* Ruff
* Pytest
* ESLint
* Vitest

**Purpose**

Automatically detect broken changes.

**Role in Bigger Picture**

Every future phase can add its own tests to the same CI pipeline.

---

# PHASE 1 FINAL INTEGRATION

At the end of Phase 1:

```text
                    FRONTEND
                       │
                       ▼
                     FASTAPI
                       │
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
 PostgreSQL          Neo4j            Qdrant
       │               │                │
       └───────────────┼────────────────┘
                       │
                     Redis
                       │
                    Ollama
                       │
                      Qwen
```

A basic request should successfully demonstrate:

```text
Frontend
   ↓
Backend
   ↓
Qwen
   ↓
PostgreSQL
   ↓
Neo4j
   ↓
Qdrant
   ↓
Redis
```

The application does **not yet provide the actual learning product**.

Phase 1 only establishes the foundation required to build it safely and incrementally.

---

# PHASE 2 — USER & SUBJECT MANAGEMENT

## Objective

Create the first functional product layer where a learner can create an account, create subjects, and manage their learning workspace.

## Functions to Be Done

### 2.1 Authentication

**Work**

* Registration
* Login
* Logout
* Password hashing
* JWT authentication
* Protected routes

**Tech Stack**

* FastAPI
* PostgreSQL
* JWT
* Next.js

**Purpose**

Identify each learner securely.

**Role in Bigger Picture**

Every future document, learning session, quiz, mastery graph, and chat history must belong to a specific user.

---

### 2.2 Subject Management

**Work**

* Create subject
* Rename subject
* Delete subject
* List subjects
* Subject dashboard

**Tech Stack**

* Next.js
* FastAPI
* PostgreSQL

**Purpose**

Provide the top-level container for learning material.

**Role in Bigger Picture**

Everything later belongs to a subject:

```text
Subject
├── Documents
├── Curriculum
├── Lessons
├── Quizzes
├── Chat
└── Mastery
```

---

### 2.3 Learning Mode Selection

**Work**

Allow selection of:

```text
Guided
Manual
Automatic
```

**Tech Stack**

* Next.js
* FastAPI
* PostgreSQL

**Purpose**

Store how the learner wants the learning engine to behave.

**Role in Bigger Picture**

The same learning system will later use this setting to determine who controls progression.

---

# PHASE 3 — DOCUMENT INGESTION

## Objective

Allow users to upload learning material and transform raw files into processable pages.

## Functions to Be Done

### 3.1 Multi-File Upload

**Tech Stack**

* Next.js
* FastAPI
* Local file storage
* PostgreSQL

**Purpose**

Accept multiple sources for one subject.

**Role in Bigger Picture**

These files become the source material for the entire learning system.

---

### 3.2 PDF Processing

**Tech Stack**

* PyMuPDF
* Python

**Purpose**

Extract pages and available text.

**Role in Bigger Picture**

Creates the raw document units used by OCR, vision, curriculum extraction, and RAG.

---

### 3.3 PPT Processing

**Tech Stack**

* python-pptx

**Purpose**

Convert slides into processable educational content.

**Role in Bigger Picture**

Allows multiple educational source formats to contribute to the same curriculum.

---

### 3.4 Processing Queue

**Tech Stack**

* Celery
* Redis

**Purpose**

Move expensive document operations into background jobs.

**Role in Bigger Picture**

Prevents large PDF processing from blocking normal API requests.

---

# PHASE 4 — OCR & VISION

## Objective

Make both digital and handwritten educational material understandable to the AI system.

## Functions to Be Done

### 4.1 OCR

**Tech Stack**

* PaddleOCR

**Purpose**

Extract text from scanned and handwritten pages.

**Role in Bigger Picture**

Converts visual document content into machine-readable information.

---

### 4.2 Vision Processing

**Tech Stack**

* Vision-capable model/tooling
* Python

**Purpose**

Interpret content that pure OCR may not capture reliably:

* Diagrams
* Tables
* Mathematical content
* Mixed handwritten/digital pages

**Role in Bigger Picture**

Improves the quality of information supplied to Qwen.

---

### 4.3 Confidence Detection

**Purpose**

Determine whether the extracted page content is sufficiently reliable.

**Role in Bigger Picture**

Prevents the AI from silently inventing information from unreadable pages.

---

### 4.4 Re-Upload Workflow

**Purpose**

Ask the learner to re-upload unclear pages.

**Role in Bigger Picture**

Creates a controlled fallback when document understanding fails.

---

# PHASE 5 — CURRICULUM INTELLIGENCE

## Objective

Convert processed educational material into a structured curriculum.

## Functions to Be Done

### 5.1 Chapter Extraction

**Tech Stack**

* Qwen
* LangGraph
* PostgreSQL

**Purpose**

Identify chapter boundaries.

**Role in Bigger Picture**

Creates the top level of the learning journey.

---

### 5.2 Topic Extraction

**Purpose**

Identify major concepts under each chapter.

**Role in Bigger Picture**

Topics become learning and assessment units.

---

### 5.3 Subtopic Extraction

**Purpose**

Break topics into smaller teachable units.

**Role in Bigger Picture**

Allows fine-grained lessons and mastery tracking.

---

### 5.4 Prerequisite Detection

**Tech Stack**

* Qwen
* Neo4j

**Purpose**

Identify concept dependencies.

**Role in Bigger Picture**

Allows the Automatic Agent to understand:

```text
A must be understood before B
```

---

### 5.5 Curriculum Graph

**Tech Stack**

* Neo4j

**Purpose**

Represent the academic structure and relationships.

**Role in Bigger Picture**

Becomes the structural map used by the Learning Manager Agent.

---

# PHASE 6 — LEARNING CONTENT

## Objective

Turn the curriculum and source content into actual lessons.

## Functions to Be Done

### 6.1 Page-Wise Bullet Notes

**Tech Stack**

* Qwen
* Qdrant
* PostgreSQL

**Purpose**

Give concise notes for each page.

**Role in Bigger Picture**

Provides the learner with immediately usable study material.

---

### 6.2 Topic Lessons

**Purpose**

Combine relevant source pages into structured topic learning.

**Role in Bigger Picture**

Becomes the main unit of the learner experience.

---

### 6.3 Source References

**Purpose**

Maintain document/page references for generated content.

**Role in Bigger Picture**

Supports grounded explanations and transparent source attribution.

---

# PHASE 7 — ASSESSMENT

## Objective

Test whether the learner actually understood each topic and chapter.

## Functions to Be Done

### 7.1 Topic Quiz

**Tech Stack**

* Qwen
* PostgreSQL

**Purpose**

Provide small topic-level checks.

**Role in Bigger Picture**

Provides immediate evidence of learning.

---

### 7.2 Chapter Final Quiz

**Purpose**

Assess the complete chapter.

**Role in Bigger Picture**

Provides the main signal for chapter-level mastery.

---

### 7.3 Difficulty Levels

```text
Easy
Medium
Hard
```

**Purpose**

Measure performance at different difficulty levels.

**Role in Bigger Picture**

Provides better evidence for personalized difficulty selection.

---

### 7.4 Repeat Attempts

**Purpose**

Allow learners to retake assessments.

**Role in Bigger Picture**

Creates historical evidence for mastery improvement.

---

### 7.5 Question Validation

**Purpose**

Reject invalid or ambiguous generated questions.

**Role in Bigger Picture**

Protects the quality of the assessment system before questions reach learners.

---

# PHASE 8 — EVALUATION & HINTS

## Objective

Turn assessment into diagnosis rather than simple scoring.

## Functions to Be Done

### 8.1 LLM Answer Evaluation

**Tech Stack**

* Qwen
* Pydantic

**Purpose**

Evaluate the learner's submitted answer.

**Role in Bigger Picture**

Transforms answers into structured learning signals.

---

### 8.2 Incorrect Answer Explanation

**Purpose**

Explain what was wrong and what the correct reasoning is.

**Role in Bigger Picture**

Connects assessment directly back to teaching.

---

### 8.3 Weak Concept Detection

**Purpose**

Identify the concept associated with the mistake.

**Role in Bigger Picture**

Feeds the Student Mastery Graph.

---

### 8.4 Six-Tier Hint System

**Purpose**

Provide progressively stronger hints without immediately revealing the answer.

**Role in Bigger Picture**

Encourages active problem solving and can become a signal of learner independence.

---

# PHASE 9 — STUDENT MEMORY

## Objective

Create persistent learner intelligence.

## Functions to Be Done

### 9.1 Student Mastery Graph

**Tech Stack**

* Neo4j
* PostgreSQL

**Purpose**

Represent topic-level learner mastery.

**Role in Bigger Picture**

Provides the current learner state to the personalization and agent systems.

---

### 9.2 Mastery Calculation

**Inputs**

* Correctness
* Difficulty
* Attempts
* Hint usage
* Recency
* Historical performance

**Role in Bigger Picture**

Transforms raw activity into a useful learner-state representation.

---

### 9.3 Weakness History

**Purpose**

Persist recurring weaknesses.

**Role in Bigger Picture**

Allows the system to detect long-term patterns instead of reacting only to the latest quiz.

---

# PHASE 10 — RAG & CHATBOT

## Objective

Provide a persistent syllabus-aware AI assistant.

## Functions to Be Done

### 10.1 Document Chunking

**Tech Stack**

* Python
* Qdrant

**Purpose**

Break source material into retrievable pieces.

**Role in Bigger Picture**

Makes large educational documents searchable by meaning.

---

### 10.2 Embedding Generation

**Tech Stack**

* Sentence Transformers / BGE-family embedding model

**Purpose**

Convert source content into vectors.

**Role in Bigger Picture**

Enables semantic retrieval.

---

### 10.3 Syllabus Chatbot

**Tech Stack**

* Qwen
* Qdrant
* Neo4j
* PostgreSQL

**Purpose**

Answer syllabus-related questions using source content and learner state.

**Role in Bigger Picture**

Provides continuous support during every lesson.

---

### 10.4 External Information Labeling

**Purpose**

Clearly separate source-derived information from external explanations.

**Role in Bigger Picture**

Prevents the learner from confusing generated external information with their uploaded material.

---

# PHASE 11 — PERSONALIZATION

## Objective

Use learner data to adapt the educational experience.

## Functions to Be Done

### 11.1 Weak Topic Prioritization

**Tech Stack**

* Neo4j
* Python

**Purpose**

Identify topics that require more attention.

**Role in Bigger Picture**

Determines which content should receive higher learning priority.

---

### 11.2 Dynamic Difficulty

**Purpose**

Move between Easy, Medium, and Hard according to learner performance.

**Role in Bigger Picture**

Prevents assessments from remaining too easy or too difficult.

---

### 11.3 Remedial Learning

**Purpose**

When a learner struggles, provide targeted teaching and practice.

**Role in Bigger Picture**

Creates the feedback loop between assessment and learning.

---

### 11.4 Weak Topic Final Quiz Weighting

**Purpose**

Give greater assessment representation to concepts the learner has struggled with.

**Role in Bigger Picture**

Makes final assessments personalized rather than static.

---

# PHASE 12 — LEARNING MODES

## Objective

Expose different levels of AI autonomy.

## Functions to Be Done

### 12.1 Guided Mode

**Purpose**

AI recommends the next learning activity; user decides.

**Role in Bigger Picture**

Provides AI assistance without removing learner control.

---

### 12.2 Manual Mode

**Purpose**

Allow learner-controlled navigation.

**Role in Bigger Picture**

Provides freedom to study any available topic.

---

### 12.3 Automatic Mode

**Purpose**

Allow the AI to determine and execute the learning sequence.

**Role in Bigger Picture**

This is the primary autonomous learning mode.

---

# PHASE 13 — AGENTIC LEARNING SYSTEM

## Objective

Convert the platform from an adaptive application into a genuinely autonomous learning system.

## Functions to Be Done

### 13.1 Learning Manager Agent

**Tech Stack**

* LangGraph
* Qwen
* Neo4j
* Qdrant
* PostgreSQL

**Purpose**

Determine what the learner should do next.

**Role in Bigger Picture**

Acts as the central decision-maker of Automatic Mode.

---

### 13.2 Agent State

**Purpose**

Track:

* Current lesson
* Mastery
* Weaknesses
* Attempts
* Retrieved content
* Previous actions
* Current learning mode

**Role in Bigger Picture**

Provides the information required for reliable autonomous decisions.

---

### 13.3 Tool Calling

**Tools**

```text
Document Retrieval
Curriculum Graph
Student Mastery Graph
Quiz Engine
Teaching Engine
Coding Engine
Revision Engine
```

**Purpose**

Allow the agent to perform real actions rather than only produce text.

**Role in Bigger Picture**

Transforms the LLM from a chatbot into an orchestrating agent.

---

### 13.4 Autonomous Replanning

**Purpose**

Change the learning path after receiving new learner evidence.

**Role in Bigger Picture**

Creates the actual closed-loop behavior:

```text
Observe
 ↓
Analyze
 ↓
Plan
 ↓
Act
 ↓
Evaluate
 ↓
Update
 ↓
Replan
```

---

### 13.5 Loop and Failure Controls

**Purpose**

Prevent:

* Infinite loops
* Excessive retries
* Repeated remediation
* Unbounded LLM calls

**Role in Bigger Picture**

Makes autonomous behavior safe and predictable.

---

# PHASE 14 — COMPUTER SCIENCE CODING EXTENSION

## Objective

Extend the general learning platform for Computer Science without changing the underlying learning architecture.

## Functions to Be Done

### 14.1 Coding Lesson Type

**Purpose**

Add a coding activity to relevant lessons.

**Role in Bigger Picture**

Connect theory learning to practical implementation.

---

### 14.2 Code Editor

**Tech Stack**

* Monaco Editor

**Purpose**

Provide an in-browser coding environment.

**Role in Bigger Picture**

Allows learners to implement concepts learned in the lesson.

---

### 14.3 Compiler/Interpreter

**Languages**

```text
C
C++
Java
Python
```

**Tech Stack**

* Docker
* Language-specific compilers/interpreters

**Purpose**

Compile and execute learner code.

**Role in Bigger Picture**

Provides the practical coding component of CS lessons.

---

### 14.4 Test Runner

**Purpose**

Run predefined/generated test cases.

**Role in Bigger Picture**

Provides objective evidence of coding correctness.

---

### 14.5 Coding Evaluation

**Purpose**

Analyze execution results and identify potential conceptual weaknesses.

**Role in Bigger Picture**

Feeds coding performance back into the same Student Mastery Graph.

---

# PHASE 15 — REVISION ENGINE

## Objective

Convert complete learning history into useful revision material.

## Functions to Be Done

### 15.1 Chapter Summary

**Tech Stack**

* Qwen
* Qdrant
* Neo4j

**Purpose**

Generate structured chapter revision notes.

**Role in Bigger Picture**

Provides a final review artifact after chapter completion.

---

### 15.2 Subject Summary

**Purpose**

Generate a complete subject-level revision document.

**Role in Bigger Picture**

Creates a reusable final study resource.

---

### 15.3 Learner-Specific Revision

**Purpose**

Include:

* Weak topics
* Common mistakes
* Frequently missed concepts
* Important concepts
* Formulas
* Key facts

**Role in Bigger Picture**

Makes revision personalized instead of simply summarizing the source PDF.

---

# PHASE 16 — AI HARNESS & EVALUATION

## Objective

Measure and improve the reliability of the AI and agentic system.

## Functions to Be Done

### 16.1 Agent Tracing

**Tech Stack**

* OpenTelemetry
* Phoenix/LangSmith or equivalent

**Purpose**

Record agent decisions and tool calls.

**Role in Bigger Picture**

Allows developers to understand why an autonomous decision occurred.

---

### 16.2 Quiz Quality Evaluation

**Purpose**

Automatically test:

* Question validity
* Correct answer
* Explanation
* Difficulty
* Source grounding

**Role in Bigger Picture**

Prevents low-quality assessments from reaching users.

---

### 16.3 Agent Regression Testing

**Purpose**

Run known scenarios after model or prompt changes.

**Role in Bigger Picture**

Ensures new AI changes do not silently degrade the learning system.

---

### 16.4 Grounding Evaluation

**Purpose**

Measure whether AI responses remain supported by source content.

**Role in Bigger Picture**

Protects the syllabus-grounded learning experience.

---

# PHASE 17 — SECURITY & RELIABILITY

## Objective

Protect users, documents, data, and infrastructure.

## Functions to Be Done

### 17.1 Authentication Hardening

### 17.2 Authorization

### 17.3 Cross-User Data Isolation

### 17.4 Secure File Handling

### 17.5 Database Access Control

### 17.6 Agent Failure Handling

### 17.7 Structured Output Validation

### 17.8 Coding Sandbox Security

**Tech Stack**

* FastAPI security
* JWT
* Docker
* Database permissions
* Validation layers

**Purpose**

Ensure the system behaves safely under normal and malicious inputs.

**Role in Bigger Picture**

Makes the platform suitable for real users rather than only local experimentation.

---

# PHASE 18 — PERFORMANCE & LOCAL OPTIMIZATION

## Objective

Make the entire system practical on limited local hardware.

## Functions to Be Done

### 18.1 LLM Optimization

### 18.2 OCR Optimization

### 18.3 Retrieval Optimization

### 18.4 Database Query Optimization

### 18.5 Caching

### 18.6 Background Job Optimization

### 18.7 Agent Step Reduction

**Tech Stack**

* Qwen/Ollama
* Redis
* PostgreSQL
* Neo4j
* Qdrant
* Docker

**Purpose**

Reduce resource consumption and latency.

**Role in Bigger Picture**

Makes the zero/low-cost local-first product realistically usable.

---

# PHASE 19 — CLOUD-READY DEPLOYMENT

## Objective

Prepare the local-first platform for future cloud deployment without changing its core product logic.

## Functions to Be Done

### 19.1 Storage Abstraction

Local storage → cloud object storage.

### 19.2 LLM Abstraction

Local Qwen → cloud inference.

### 19.3 Database Migration Strategy

Local databases → managed databases.

### 19.4 Scalable Coding Execution

Local Docker → scalable isolated execution infrastructure.

### Tech Stack

Future options:

* S3/R2
* Managed PostgreSQL
* Managed Neo4j
* Managed Qdrant
* Managed Redis
* Cloud GPU/LLM

**Purpose**

Move from a student-scale local deployment to a scalable service.

**Role in Bigger Picture**

Allows the same platform to grow without redesigning the product architecture.

---

# FINAL FEATURE INTEGRATION

After all phases are complete, all features must operate as one system.

```text
                         STUDENT
                            │
                            ▼
                     CREATE SUBJECT
                            │
                            ▼
                     UPLOAD MATERIAL
                            │
                            ▼
                    DOCUMENT ENGINE
                            │
                    OCR + VISION
                            │
                            ▼
                    SOURCE CONTENT
                            │
                  ┌─────────┴─────────┐
                  ▼                   ▼
          CURRICULUM ENGINE        QDRANT
                  │                   │
                  ▼                   │
           CURRICULUM GRAPH           │
                  │                   │
                  └────────┬──────────┘
                           ▼
                     LESSON ENGINE
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
               NOTES                QUIZ
                                     │
                                     ▼
                                  EVALUATE
                                     │
                                     ▼
                           STUDENT MASTERY
                                GRAPH
                                     │
                                     ▼
                           LEARNING MANAGER
                                AGENT
                                     │
               ┌─────────────────────┼─────────────────────┐
               ▼                     ▼                     ▼
            GUIDED                 MANUAL              AUTOMATIC
               │                     │                     │
               └─────────────────────┼─────────────────────┘
                                     ▼
                              NEXT ACTIVITY
                                     │
                    ┌────────────────┴────────────────┐
                    ▼                                 ▼
              NORMAL SUBJECT                    CS SUBJECT
                    │                                 │
                    ▼                                 ▼
                 LESSON                         CODING LESSON
                                                      │
                                                      ▼
                                                  COMPILER
                                                      │
                                                      ▼
                                                TEST/EVALUATE
                                                      │
                                                      ▼
                                             MASTERY UPDATE
                                                      │
                                                      ▼
                                                  REPLAN
                                     │
                                     ▼
                              CHAPTER COMPLETE
                                     │
                                     ▼
                              REVISION SUMMARY
```

# FINAL ROLE OF EACH MAJOR SYSTEM

| System           | Main Role                          | Bigger Picture                        |
| ---------------- | ---------------------------------- | ------------------------------------- |
| OCR/Vision       | Understand documents               | Makes arbitrary study material usable |
| Qwen             | Reasoning and generation           | Provides intelligence                 |
| Qdrant           | Source retrieval                   | Grounds AI in learning material       |
| Neo4j            | Curriculum + mastery relationships | Gives AI structural knowledge         |
| PostgreSQL       | Application state                  | Stores reliable transactional data    |
| Redis            | Temporary state/queues/cache       | Supports system operation             |
| Learning Manager | Decision-making                    | Makes the platform autonomous         |
| Quiz Engine      | Assessment                         | Produces evidence of understanding    |
| Mastery Graph    | Learner state                      | Enables personalization               |
| Chatbot          | Continuous assistance              | Supports learning at any point        |
| Coding Sandbox   | Practical CS learning              | Connects theory to implementation     |
| Revision Engine  | Consolidation                      | Produces final study material         |
| AI Harness       | Evaluation/observability           | Makes AI behavior measurable          |
