# MASTERDOC

# Autonomous AI-Based Learning Platform

## 1. Project Identity

### Project Name

**Autonomous AI-Based Learning Platform**

### Core Idea

An autonomous AI-powered learning platform that accepts a learner's syllabus and study material, understands the material, converts it into a structured curriculum, generates page-wise notes and topic-wise assessments, maintains a persistent student mastery graph, and continuously personalizes the learning journey.

The platform is **subject-agnostic**. It is designed for learners studying any academic subject.

For Computer Science and programming-related subjects, the same learning system additionally provides a lesson-specific coding environment with support for C, C++, Java, and Python.

---

# 2. Product Vision

The product should behave less like:

> "Ask an AI a question and get an answer."

and more like:

> "Give the AI your syllabus and let it manage your learning journey."

The platform should understand:

* What is present in the learner's material
* How the curriculum is structured
* Which concepts are prerequisites for others
* What the learner has already learned
* Which concepts the learner struggles with
* What should be learned next
* Whether the learner needs teaching, practice, revision, or assessment
* How difficult the next assessment should be

The ultimate objective is **mastery-oriented learning rather than content consumption**.

---

# 3. Product Philosophy

## 3.1 Learning Over Answer Generation

The platform should encourage the learner to think.

It must not immediately expose answers when a learner is struggling with a question.

The six-level hint mechanism should gradually increase assistance.

```text
Conceptual direction
        ↓
Relevant principle
        ↓
Narrow the problem
        ↓
Strong clue
        ↓
Near-complete approach
        ↓
Full solution
```

## 3.2 Autonomous but Controllable

The system provides three learning modes:

```text
Guided
Manual
Automatic
```

The learner therefore gets both autonomy and control.

## 3.3 Source-Grounded Learning

The uploaded material is the primary educational source.

When external knowledge is permitted, the system must clearly distinguish between:

```text
Information from uploaded material
```

and:

```text
External explanation
```

---

# 4. Target Users

The platform is designed for:

* School students
* College students
* Engineering students
* Science students
* Commerce students
* Medical students
* Competitive-exam learners
* Self-learners
* Anyone studying from structured educational material

The system should not assume that the learner is an engineer.

Computer Science is an extension of the general learning platform rather than the identity of the product.

---

# 5. Core Product Workflow

```text
User
 ↓
Create Subject
 ↓
Upload PDF / PPT / Book Material
 ↓
Document Understanding
 ↓
OCR + Vision
 ↓
Curriculum Extraction
 ↓
Subject → Chapter → Topic → Subtopic
 ↓
Curriculum Graph
 ↓
Generate Page Notes
 ↓
Generate Topic Quizzes
 ↓
Create Chapter Final Quiz
 ↓
Student Starts Learning
 ↓
Guided / Manual / Automatic
 ↓
Teach
 ↓
Practice
 ↓
Assess
 ↓
Evaluate
 ↓
Update Student Mastery Graph
 ↓
Identify Weaknesses
 ↓
Replan
 ↓
Continue
 ↓
Chapter Completion
 ↓
Final Revision Summary
```

---

# 6. Input System

## 6.1 Supported Material

Initial system:

* PDF
* Multiple PDFs
* PPT/PPTX

The architecture should support books and other document formats later.

## 6.2 Multiple Documents

A single subject may have multiple sources.

Example:

```text
Data Structures
├── University Syllabus.pdf
├── Unit 1 Notes.pdf
├── Unit 2 Notes.pdf
└── Reference Book.pdf
```

All relevant documents can contribute to:

* Curriculum generation
* Retrieval
* Notes
* Quiz generation
* Chatbot context

The system must retain source attribution.

---

# 7. Document Understanding

## 7.1 Digital PDFs

For digitally generated PDFs:

```text
PDF
 ↓
Text Extraction
 ↓
Structure Detection
 ↓
LLM Understanding
```

## 7.2 Scanned/Handwritten PDFs

For scanned and handwritten pages:

```text
PDF
 ↓
Render Page
 ↓
OCR
 ↓
Vision Processing
 ↓
Confidence Check
 ↓
LLM Understanding
```

## 7.3 Unclear Page Handling

If the system cannot confidently interpret a page:

```text
Low Confidence
 ↓
Identify Exact Page
 ↓
Ask User to Re-upload/Re-capture
 ↓
Reprocess
```

The system must not invent missing educational content.

---

# 8. Curriculum Understanding

The platform automatically constructs:

```text
Subject
 └── Chapter
      └── Topic
           └── Subtopic
```

Example:

```text
Data Structures
└── Chapter 1: Arrays
    ├── 1.1 Array Basics
    │   ├── 1.1.1 Declaration
    │   ├── 1.1.2 Traversal
    │   ├── 1.1.3 Insertion
    │   └── 1.1.4 Deletion
    │
    └── 1.2 Searching
        ├── 1.2.1 Linear Search
        └── 1.2.2 Binary Search
```

The system should preserve meaningful original structure where available while inferring structure when required.

---

# 9. Curriculum Graph

The Curriculum Graph represents what the subject contains and how concepts are related.

Example:

```text
Arrays
   │
   └── prerequisite_for → Binary Search

Recursion
   │
   └── prerequisite_for → Trees
```

Core entities:

```text
Subject
Chapter
Topic
Subtopic
```

Core relationships:

```text
HAS_CHAPTER
HAS_TOPIC
HAS_SUBTOPIC
PREREQUISITE_FOR
```

This graph is fundamental to Automatic Mode.

---

# 10. Page-Wise Notes

For every usable page, the system generates concise bullet-point notes.

Example:

```text
Page 17 — Binary Search

• Requires a sorted collection
• Compares target with middle element
• Eliminates half the search space
• Uses divide-and-conquer
• Time complexity is O(log n)
```

The notes should be grounded in the source.

---

# 11. Lesson Architecture

Lessons follow the source-derived hierarchy.

```text
Chapter 1
│
├── 1.1 Topic
│   ├── 1.1.1 Subtopic
│   ├── 1.1.2 Subtopic
│   ├── 1.1.3 Subtopic
│   └── Quiz
│
├── 1.2 Topic
│   ├── 1.2.1 Subtopic
│   ├── 1.2.2 Subtopic
│   └── Quiz
│
└── Final Chapter Quiz
```

Topic quizzes are short checkpoints.

The chapter final quiz evaluates the complete chapter.

---

# 12. Assessment System

## Initial Question Type

**MCQ**

## Difficulty Levels

```text
Easy
Medium
Hard
```

## Topic Quiz

A topic may have a short quiz to verify immediate understanding.

## Chapter Final Quiz

The final chapter quiz:

* Covers the entire chapter
* Uses the learner's performance history
* Gives increased weighting to weak topics
* Can dynamically adjust difficulty
* Can be repeated

---

# 13. Personalized Assessment

Example student state:

```text
Arrays       → 91%
Linked List  → 47%
Recursion    → 35%
Trees        → Not assessed
```

The system should not simply average these values.

Instead, it should recognize:

```text
Recursion = weak
Trees = dependent on prerequisite concepts
```

and prioritize relevant remediation.

A future final assessment can give greater representation to weak areas.

---

# 14. Quiz Evaluation

The LLM evaluates submitted answers.

Evaluation should produce:

* Correct/incorrect result
* Correct answer
* Explanation
* Associated topic
* Concept involved
* Reason for error where inferable

Example:

```text
Question: ...

Your answer: B
Correct answer: C

Why:
The selected option applies to X,
while the question requires Y.
```

The result is stored and used to update the learner's mastery state.

---

# 15. Six-Tier Hint System

Hints are always available progressively.

```text
Tier 1
Think about the relevant concept.

Tier 2
Recall the principle involved.

Tier 3
Narrow the problem to the important idea.

Tier 4
Provide a stronger conceptual clue.

Tier 5
Provide an almost-complete approach.

Tier 6
Provide the complete solution.
```

The six-tier structure is fixed.

The actual content is generated according to the question.

Hint usage contributes to learner-state analysis.

---

# 16. Persistent Syllabus Chatbot

A ChatGPT-style chatbot is always available during learning.

The chatbot should be:

* Syllabus-aware
* Current-lesson-aware
* Student-context-aware

It receives:

```text
Source Content
+
Curriculum Context
+
Current Lesson
+
Student Mastery
+
Conversation History
```

It should answer syllabus-related questions rather than acting as an unrestricted general assistant.

---

# 17. External Knowledge

The learner can choose whether external information is permitted.

### Syllabus-only

Only uploaded material is used.

### Syllabus + External

External knowledge may supplement explanations.

The UI must clearly distinguish:

```text
From your uploaded material
```

from:

```text
External explanation
```

---

# 18. Student Mastery Graph

The Student Mastery Graph stores learner-specific knowledge state.

Example:

```text
Student
   │
   └── WEAK_IN → Recursion
                    │
                    ├── mastery: 0.42
                    ├── attempts: 5
                    ├── difficulty: Medium
                    ├── weak_concepts: Base Case
                    └── last_reviewed: ...
```

The graph may maintain:

* Mastery
* Attempts
* Difficulty performance
* Weak concepts
* Revision state
* Last attempt
* Last revision
* Hint dependency
* Historical learning state

---

# 19. Student Memory

Memory persists across sessions.

A returning learner should retain:

* Mastery
* Attempts
* Mistakes
* Weak concepts
* Revision history
* Difficulty history
* Quiz performance
* Learning state

This makes personalization cumulative rather than session-specific.

---

# 20. Three Learning Modes

## 20.1 Guided Mode

The AI recommends.

```text
AI recommends next topic
        ↓
Student chooses
```

The learner can accept or reject the recommendation.

## 20.2 Manual Mode

The learner selects the next chapter/topic/subtopic.

Prerequisite warnings may be shown, but manual choice remains possible.

## 20.3 Automatic Mode

The AI owns the progression.

```text
Observe
 ↓
Analyze
 ↓
Plan
 ↓
Teach
 ↓
Assess
 ↓
Evaluate
 ↓
Update Mastery
 ↓
Remediate
 ↓
Reassess
 ↓
Advance/Replan
```

Automatic Mode is the primary agentic experience.

---

# 21. Agentic Architecture

## Learning Manager Agent

The central reasoning/orchestration agent.

Responsibilities:

* Read learner state
* Read curriculum graph
* Read recent performance
* Select next action
* Invoke specialized capabilities
* Evaluate outcomes
* Update state
* Replan

## Specialized Agents

### Syllabus Analyzer Agent

Creates structured curriculum from source documents.

### Teaching Agent

Generates explanations and lesson content.

### Assessment Agent

Generates and manages quizzes.

### Evaluation Agent

Evaluates answers and identifies conceptual weaknesses.

### Coding Agent

Handles programming-specific lesson activities.

### Revision Agent

Generates revision material.

### Chat Agent

Handles syllabus-grounded discussion.

---

# 22. Agentic Core Loop

The system should demonstrate genuine closed-loop autonomy:

```text
OBSERVE
   ↓
Student Performance
   ↓
ANALYZE
   ↓
Weakness / Mastery / Prerequisites
   ↓
PLAN
   ↓
Select Next Activity
   ↓
ACT
   ↓
Teach / Quiz / Code / Revise
   ↓
EVALUATE
   ↓
Update Student Mastery
   ↓
REPLAN
```

The next step must be capable of changing based on new evidence.

---

# 23. Computer Science Extension

Computer Science uses the **same lesson framework**.

Example:

```text
1.1 Arrays
│
├── 1.1.1 Theory
├── 1.1.2 Theory
├── 1.1.3 Theory
└── 1.1.4 Coding Practice
```

The coding environment only appears inside relevant coding lessons.

The platform does not become a generic online compiler.

---

# 24. Coding Environment

Initial languages:

```text
C
C++
Java
Python
```

Flow:

```text
Coding Lesson
 ↓
Problem
 ↓
Student Writes Code
 ↓
Sandbox
 ↓
Compile/Execute
 ↓
Test Cases
 ↓
Result
 ↓
Evaluation
 ↓
Mastery Update
```

Coding performance contributes to the same Student Mastery Graph.

---

# 25. Coding Sandbox

Student code must execute in an isolated environment.

Controls:

* CPU limit
* Memory limit
* Time limit
* Process limit
* Filesystem isolation
* Network restriction
* Resource controls

The main application server must never execute arbitrary student code directly.

---

# 26. Coding Evaluation

The coding component should evaluate:

* Compilation
* Runtime behavior
* Test-case correctness
* Execution time
* Basic complexity considerations
* Conceptual weaknesses where identifiable

Example:

```text
Binary Search Coding
 ↓
6/10 Tests Passed
 ↓
Boundary Condition Failure
 ↓
Mastery Update
 ↓
Learning Manager
 ↓
Targeted Practice
```

---

# 27. AI and LLM Architecture

## Primary LLM

Initial candidate:

**Qwen**

The model should be locally hosted during the initial implementation.

## Runtime

Recommended initial runtime:

**Ollama**

The system should expose an abstraction layer so the LLM can later be replaced without changing the product architecture.

---

# 28. LLM Responsibilities

The LLM handles:

* Document semantic understanding
* Curriculum structuring
* Notes
* Explanations
* Quiz generation
* Quiz evaluation
* Hint generation
* Weakness interpretation
* Learning planning
* Chat responses
* Revision generation
* Coding assessment feedback

---

# 29. Deterministic Tool Responsibilities

LLMs should not be responsible for every operation.

Deterministic services handle:

* OCR
* PDF parsing
* PPT parsing
* Embeddings
* Database access
* Graph traversal
* Code execution
* Test execution
* Authentication
* File storage
* Queue management
* API operations

The LLM reasons over these tools and invokes them when required.

---

# 30. RAG Architecture

The platform uses Retrieval-Augmented Generation.

```text
User Question
 ↓
Query Processing
 ↓
Embedding
 ↓
Vector Search
 ↓
Relevant Source Chunks
 ↓
Curriculum Context
 ↓
Student Context
 ↓
Qwen
 ↓
Grounded Response
```

The retrieval system maintains source metadata:

* Document
* Page
* Chapter
* Topic
* Subtopic

---

# 31. Database Architecture

The system uses specialized storage.

```text
PostgreSQL
    +
Neo4j
    +
Qdrant
    +
Redis
```

## PostgreSQL

Stores transactional application data:

* Users
* Subjects
* Documents
* Lessons
* Quizzes
* Questions
* Attempts
* Answers
* Chat history
* Coding submissions
* Revision summaries
* Application metadata

## Neo4j

Stores:

* Curriculum Graph
* Prerequisites
* Student Mastery Graph
* Learning relationships

## Qdrant

Stores:

* Source embeddings
* PDF chunks
* Retrieval metadata
* Semantic representations

## Redis

Used for:

* Cache
* Sessions
* Background queues
* Temporary agent state
* Rate limiting

---

# 32. Storage Architecture

Original uploaded documents should remain in file/object storage.

Initial local storage:

```text
/storage
├── documents/
├── pages/
├── generated/
└── revisions/
```

PostgreSQL stores references to those files.

---

# 33. AI Harness and Evaluation Layer

The platform should include an AI evaluation/observability layer.

It should evaluate:

* Agent decisions
* Quiz generation
* Answer evaluation
* Hint quality
* Difficulty selection
* Source grounding
* Agent traces
* Regression behavior

Generated assessments must be validated before being presented to learners.

Example:

```text
Question Generator
 ↓
AI Evaluation Harness
 ↓
Question valid?
Answer valid?
Explanation valid?
Difficulty appropriate?
 ↓
PASS → Store/Present
FAIL → Regenerate
```

---

# 34. AI Reliability Principles

The system should follow:

> When uncertain, retrieve, validate, ask, or defer rather than confidently fabricate.

And:

> Every autonomous action must have a bounded execution path and recoverable failure mode.

---

# 35. Major Constraints

## Budget

The MVP should operate without requiring paid infrastructure.

## Local First

Initially run:

```text
Qwen
PostgreSQL
Neo4j
Qdrant
Redis
OCR
Compiler Sandbox
```

locally.

## Cloud Later

The architecture should support future cloud deployment.

---

# 36. Major Risks

## AI Risks

* Hallucination
* Incorrect quiz generation
* Incorrect answer evaluation
* Incorrect difficulty
* Incorrect planning
* Incorrect weakness detection
* Poor hints

## Document Risks

* OCR errors
* Handwriting recognition errors
* Poor scans
* Diagrams
* Tables
* Mathematical notation
* Incorrect document structure

## Agent Risks

* Infinite loops
* Repeated remediation
* Premature advancement
* Excessive LLM calls
* Tool failures

## Data Risks

* Cross-user data leakage
* Inconsistent graph state
* Incorrect mastery state
* Database synchronization problems

## Coding Risks

* Sandbox escape
* Resource exhaustion
* Malicious source code
* Incorrect test cases

---

# 37. Critical Validation Areas

Before production, validate:

```text
Document Understanding
Curriculum Extraction
OCR Accuracy
RAG Retrieval
Quiz Validity
Quiz Evaluation
Hint Quality
Mastery Calculation
Automatic Planning
Chatbot Grounding
Coding Sandbox Security
Coding Evaluation
Database Consistency
Cross-user Isolation
Agent Loop Handling
```

---

# 38. Project Phases

## PHASE 0 — Foundation and Repository Setup

### Objective

Create the project foundation before implementing AI features.

### Tasks

* Create monorepo
* Create frontend
* Create backend
* Configure Docker Compose
* Setup PostgreSQL
* Setup Neo4j
* Setup Qdrant
* Setup Redis
* Setup Ollama
* Configure environment management
* Setup Git/GitHub
* Setup CI basics
* Create project documentation

### Output

Working development environment.

### Completion Criteria

All infrastructure starts locally and services can communicate.

---

# 39. PHASE 1 — Authentication and Subject Management

### Objective

Create the basic learner platform.

### Features

* Registration
* Login
* User session
* Dashboard
* Create subject
* Edit subject
* Delete subject
* Select learning mode

### Output

A learner can create a subject and access it from a dashboard.

---

# 40. PHASE 2 — Document Upload and Processing

### Objective

Accept educational material.

### Features

* Multi-file upload
* PDF validation
* File storage
* Page extraction
* Processing queue
* Processing status
* Basic digital PDF extraction

### Add Later in This Phase

* OCR
* Handwritten support
* Vision preprocessing
* Confidence detection
* Re-upload workflow

### Output

The system can transform uploaded files into structured page content.

---

# 41. PHASE 3 — Curriculum Intelligence

### Objective

Turn documents into a structured syllabus.

### Features

* Subject detection
* Chapter extraction
* Topic extraction
* Subtopic extraction
* Prerequisite detection
* Curriculum Graph construction

### Output

```text
Subject
 ↓
Chapter
 ↓
Topic
 ↓
Subtopic
```

stored in PostgreSQL + Neo4j.

---

# 42. PHASE 4 — Content and Notes Engine

### Objective

Turn source material into learnable content.

### Features

* Page-wise bullet notes
* Topic explanations
* Source references
* Lesson generation
* Source-grounded explanations

### Output

A complete lesson structure generated from uploaded content.

---

# 43. PHASE 5 — Quiz Engine

### Objective

Create the first assessment system.

### Features

* MCQ generation
* Easy/Medium/Hard
* Topic quizzes
* Chapter final quizzes
* Question validation
* Answer storage
* Repeated attempts

### Output

A learner can study a topic and take an assessment.

---

# 44. PHASE 6 — Evaluation and Hint System

### Objective

Make assessment intelligent.

### Features

* LLM answer evaluation
* Incorrect-answer explanations
* Weak concept identification
* Six-tier hints
* Hint tracking
* Detailed attempt history

### Output

The system does more than mark correct/incorrect; it diagnoses weaknesses.

---

# 45. PHASE 7 — Student Mastery Graph

### Objective

Build persistent learner memory.

### Features

* Mastery score
* Topic-level state
* Subtopic-level state
* Attempt history
* Weak concepts
* Difficulty history
* Revision history
* Hint dependency

### Output

```text
Student
 ↓
Mastery State
 ↓
Curriculum Graph
```

The learner's state persists across sessions.

---

# 46. PHASE 8 — Syllabus Chatbot

### Objective

Provide continuous syllabus-grounded discussion.

### Features

* Persistent chat
* RAG retrieval
* Current lesson context
* Student mastery context
* Conversation memory
* External-information labeling

### Output

A context-aware assistant available during every lesson.

---

# 47. PHASE 9 — Personalization Engine

### Objective

Convert learner history into adaptive learning behavior.

### Features

* Weakness detection
* Difficulty adaptation
* Topic prioritization
* Prerequisite awareness
* Retake recommendations
* Revision recommendations

### Output

The system can answer:

> What should this learner do next?

---

# 48. PHASE 10 — Learning Modes

### Objective

Implement the three learning modes.

### Guided

AI recommends.

### Manual

User chooses.

### Automatic

AI decides and executes.

### Output

All modes operate over the same underlying curriculum and student state.

---

# 49. PHASE 11 — Autonomous Learning Agent

### Objective

Build the full agentic loop.

### Features

* Learning Manager Agent
* Tool calling
* Agent state
* Planning
* Replanning
* Remediation
* Reassessment
* Loop limits
* Failure handling
* Agent trace

### Core Loop

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

### Output

A learner can select Automatic Mode and allow the platform to manage progression autonomously.

---

# 50. PHASE 12 — Coding Extension

### Objective

Extend the same learning framework to Computer Science.

### Features

* Lesson-specific coding activities
* C compiler
* C++ compiler
* Java compiler
* Python execution
* Isolated sandbox
* Test-case execution
* Coding evaluation
* Coding mastery updates

### Output

The same student learning journey now supports both theory and coding.

---

# 51. PHASE 13 — Revision Engine

### Objective

Generate a complete revision artifact.

### Features

* Full chapter summary
* Full subject summary
* Bullet-point format
* Important concepts
* Definitions
* Formulas
* Key relationships
* Common mistakes
* Weak topics
* Important examples
* Coding concepts

### Output

A downloadable revision file.

---

# 52. PHASE 14 — AI Harness and Evaluation

### Objective

Make the agent system measurable and reliable.

### Features

* Agent tracing
* Regression datasets
* Quiz validation
* Grounding evaluation
* Planning evaluation
* Hint evaluation
* Difficulty evaluation
* Agent failure tracking

### Output

A repeatable test suite that detects degradation when prompts/models/agents change.

---

# 53. PHASE 15 — Security Hardening

### Objective

Prepare the application for real users.

### Focus Areas

* Authentication
* Authorization
* File upload security
* Database access
* Cross-user isolation
* Sandbox security
* Rate limiting
* Secret management
* Logging

### Output

Security-tested local deployment.

---

# 54. PHASE 16 — Performance Optimization

### Objective

Make the local system practical.

### Optimize

* OCR
* Embeddings
* LLM inference
* Retrieval
* Agent step count
* Database queries
* Caching
* Background processing
* Compiler execution

### Measure

* Processing latency
* Chat latency
* Quiz generation latency
* Agent latency
* Memory usage
* CPU/GPU usage
* Concurrent sessions

---

# 55. PHASE 17 — Cloud-Ready Architecture

### Objective

Prepare for deployment without redesigning the product.

### Future Migration

```text
Local Qwen
   ↓
Cloud GPU / LLM

Local PostgreSQL
   ↓
Managed PostgreSQL

Local Qdrant
   ↓
Managed Vector DB

Local Storage
   ↓
S3 / R2 / Equivalent

Local Redis
   ↓
Managed Redis
```

Cloud deployment is a future phase, not an MVP dependency.

---

# 56. MVP Definition

The first usable MVP should include:

```text
[✓] User Authentication
[✓] Subject Creation
[✓] Multi-PDF Upload
[✓] Digital PDF Processing
[✓] Basic Handwritten OCR
[✓] Chapter/Topic/Subtopic Extraction
[✓] Curriculum Graph
[✓] Page-Level Notes
[✓] Topic Lessons
[✓] MCQ Generation
[✓] Topic Quiz
[✓] Chapter Final Quiz
[✓] Easy/Medium/Hard
[✓] Six-Level Hints
[✓] LLM Answer Evaluation
[✓] Weak Topic Detection
[✓] Student Mastery Graph
[✓] Syllabus Chatbot
[✓] Guided Mode
[✓] Manual Mode
[✓] Automatic Mode
[✓] Local Qwen
[✓] RAG
[✓] CS Coding Lessons
[✓] C/C++/Java/Python Sandbox
[✓] Revision Summary
```

---

# 57. Post-MVP Enhancements

Possible later additions:

* Voice learning
* Speech interaction
* Video integration
* More question types
* Teacher dashboards
* Classroom management
* Mobile application
* Spaced repetition
* Collaborative learning
* Analytics
* Institution deployment
* Advanced coding analysis
* Cloud model selection
* Multi-model routing

---

# 58. Recommended Repository Structure

```text
autonomous-learning-platform/
│
├── README.md
├── PRD.md
├── TECH-STACK.md
├── DATABASE.md
├── RISKS.md
├── MASTERDOC.md
├── docker-compose.yml
├── .env.example
├── .gitignore
│
├── apps/
│   ├── frontend/
│   │   └── src/
│   │
│   └── backend/
│       └── app/
│           ├── api/
│           ├── agents/
│           ├── workflows/
│           ├── document/
│           ├── ocr/
│           ├── vision/
│           ├── llm/
│           ├── rag/
│           ├── graphs/
│           ├── mastery/
│           ├── quiz/
│           ├── coding/
│           ├── revision/
│           ├── models/
│           ├── schemas/
│           ├── services/
│           ├── workers/
│           ├── evaluation/
│           ├── db/
│           └── core/
│
├── infrastructure/
│   ├── docker/
│   ├── nginx/
│   └── monitoring/
│
├── storage/
│   ├── documents/
│   ├── pages/
│   ├── generated/
│   └── revisions/
│
├── scripts/
└── docs/
```

---

# 59. Final System Architecture

```text
                         STUDENT
                            │
                            ▼
                    ┌───────────────┐
                    │    FRONTEND   │
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │    FastAPI    │
                    └───────┬───────┘
                            │
                  ┌─────────┴─────────┐
                  │   AGENT SYSTEM    │
                  │                   │
                  │ Learning Manager  │
                  │ Teaching Agent    │
                  │ Assessment Agent  │
                  │ Evaluation Agent  │
                  │ Coding Agent      │
                  │ Revision Agent    │
                  │ Chat Agent        │
                  └─────────┬─────────┘
                            │
              ┌─────────────┼──────────────┐
              ▼             ▼              ▼
           Qdrant         Neo4j       PostgreSQL
         Source/RAG    Curriculum +     App Data
                       Mastery Graph
              │             │              │
              └─────────────┼──────────────┘
                            │
                          QWEN
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
             OCR        Vision        Coding Sandbox
                                          │
                                    C/C++/Java/Python
```

---

# 60. Product Success Definition

The platform succeeds when a learner can provide a syllabus or study material and the system can independently transform it into a meaningful learning journey.

The complete journey should look like:

```text
UPLOAD
 ↓
UNDERSTAND
 ↓
STRUCTURE
 ↓
TEACH
 ↓
ASSESS
 ↓
DIAGNOSE
 ↓
PERSONALIZE
 ↓
REMEDIATE
 ↓
REASSESS
 ↓
MASTER
 ↓
REVISE
```

For non-coding subjects, the system provides the full academic learning experience.

For Computer Science, the same system additionally connects theory to coding through the integrated lesson-specific compiler environment.

The central intelligence of the product is the **Learning Manager Agent**, which combines the **Curriculum Graph**, **Student Mastery Graph**, **source retrieval**, and **LLM reasoning** to determine what the learner should do next.

---

# 61. Final Product Principle

> **Give the platform the learner's material. Let the platform understand the curriculum, understand the learner, and continuously decide how to move that learner toward mastery—while keeping the learner in control when they choose.**
