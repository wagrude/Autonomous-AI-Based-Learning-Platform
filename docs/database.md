# DATABASE.md

# Database Architecture

The platform uses multiple specialized databases instead of forcing all data into a single database.

```text
                         APPLICATION
                              │
                 ┌────────────┼────────────┐
                 ↓            ↓            ↓
            PostgreSQL      Neo4j        Qdrant
           Application     Knowledge      Vector
              Data           Graph         DB
                 │            │            │
                 └────────────┼────────────┘
                              ↓
                         AI / Agents
                              │
                            Redis
                       Cache / Queue / State
```

---

# 1. PostgreSQL

## Purpose

PostgreSQL is the primary relational database for transactional and structured application data.

It stores:

* Users
* Subjects
* Uploaded documents
* Curriculum metadata
* Lessons
* Quizzes
* Questions
* Quiz attempts
* Answers
* Hint usage
* Chat sessions
* Chat messages
* Coding submissions
* Coding execution results
* Learning sessions
* Revision summaries
* Agent execution metadata

PostgreSQL should be the source of truth for application-level records that require transactions, constraints, and structured querying.

---

# 2. PostgreSQL Schema

## 2.1 users

Stores learner accounts.

```text
users
-----
id                  UUID PK
name                VARCHAR
email               VARCHAR UNIQUE
password_hash       TEXT
created_at          TIMESTAMP
updated_at          TIMESTAMP
```

---

## 2.2 subjects

Represents a subject being studied by a learner.

```text
subjects
--------
id                  UUID PK
user_id             UUID FK → users.id
name                VARCHAR
description         TEXT
learning_mode       ENUM
created_at          TIMESTAMP
updated_at          TIMESTAMP
```

### learning_mode

```text
GUIDED
MANUAL
AUTOMATIC
```

A subject belongs to a user, while its curriculum structure is additionally represented in Neo4j.

---

# 3. documents

Stores uploaded source files.

```text
documents
---------
id                  UUID PK
subject_id          UUID FK → subjects.id
filename            VARCHAR
file_type           VARCHAR
storage_path        TEXT
processing_status   ENUM
page_count          INTEGER
created_at          TIMESTAMP
processed_at        TIMESTAMP
```

### processing_status

```text
UPLOADED
PROCESSING
PROCESSED
NEEDS_REUPLOAD
FAILED
```

Multiple documents can belong to one subject.

Example:

```text
Data Structures
├── syllabus.pdf
├── unit1.pdf
├── notes.pdf
└── reference.pdf
```

---

# 4. document_pages

Stores information about individual processed pages.

```text
document_pages
--------------
id                  UUID PK
document_id         UUID FK → documents.id
page_number         INTEGER
extracted_text      TEXT
ocr_text            TEXT
vision_text         TEXT
confidence_score    FLOAT
processing_status   ENUM
created_at          TIMESTAMP
```

The original page image/file reference should remain in storage rather than being stored directly as large binary data in PostgreSQL.

---

# 5. chapters

Stores application-level chapter metadata.

```text
chapters
--------
id                  UUID PK
subject_id          UUID FK → subjects.id
name                VARCHAR
chapter_number      INTEGER
description         TEXT
created_at          TIMESTAMP
```

The detailed graph relationships are represented in Neo4j.

---

# 6. topics

```text
topics
------
id                  UUID PK
chapter_id          UUID FK → chapters.id
name                VARCHAR
topic_number        VARCHAR
description         TEXT
created_at          TIMESTAMP
```

Example:

```text
Chapter 1
├── 1.1 Arrays
├── 1.2 Searching
└── 1.3 Sorting
```

---

# 7. subtopics

```text
subtopics
---------
id                  UUID PK
topic_id            UUID FK → topics.id
name                VARCHAR
subtopic_number     VARCHAR
description         TEXT
created_at          TIMESTAMP
```

Example:

```text
1.1 Arrays
├── 1.1.1 Declaration
├── 1.1.2 Traversal
├── 1.1.3 Insertion
└── 1.1.4 Deletion
```

---

# 8. lessons

Represents a learning unit shown to the learner.

```text
lessons
-------
id                  UUID PK
topic_id            UUID FK → topics.id
subtopic_id         UUID FK → subtopics.id NULL
title               VARCHAR
content             TEXT
lesson_order        INTEGER
created_at          TIMESTAMP
```

A lesson can contain:

* Explanations
* Bullet-point notes
* Examples
* Relevant source references
* Coding activity for CS lessons

---

# 9. quizzes

Stores generated assessments.

```text
quizzes
-------
id                  UUID PK
subject_id          UUID FK → subjects.id
chapter_id          UUID FK → chapters.id NULL
topic_id            UUID FK → topics.id NULL
quiz_type           ENUM
difficulty          ENUM
title               VARCHAR
question_count      INTEGER
is_generated        BOOLEAN
created_at          TIMESTAMP
```

### quiz_type

```text
TOPIC
CHAPTER_FINAL
```

### difficulty

```text
EASY
MEDIUM
HARD
```

---

# 10. questions

Stores individual quiz questions.

```text
questions
---------
id                  UUID PK
quiz_id             UUID FK → quizzes.id
question_text       TEXT
option_a            TEXT
option_b            TEXT
option_c            TEXT
option_d            TEXT
correct_option      CHAR
explanation         TEXT
source_reference    JSONB
created_at          TIMESTAMP
```

The question should preserve source metadata where applicable.

Example:

```json
{
  "document_id": "...",
  "page_numbers": [17, 18],
  "topic_id": "..."
}
```

---

# 11. quiz_attempts

Stores each attempt made by a learner.

```text
quiz_attempts
------------
id                  UUID PK
quiz_id             UUID FK → quizzes.id
user_id             UUID FK → users.id
score               FLOAT
total_questions     INTEGER
difficulty          VARCHAR
started_at          TIMESTAMP
completed_at        TIMESTAMP
```

A user may attempt the same quiz multiple times.

---

# 12. quiz_answers

Stores answers for individual questions within an attempt.

```text
quiz_answers
------------
id                  UUID PK
attempt_id          UUID FK → quiz_attempts.id
question_id         UUID FK → questions.id
selected_option     CHAR
is_correct          BOOLEAN
evaluation_reason   TEXT
evaluated_at        TIMESTAMP
```

The evaluation result may be generated by the LLM and validated before storage.

---

# 13. hint_attempts

Stores usage of the six-level hint system.

```text
hint_attempts
------------
id                  UUID PK
user_id             UUID FK → users.id
question_id         UUID FK → questions.id
hint_level          INTEGER
requested_at        TIMESTAMP
```

`hint_level` must be between `1` and `6`.

```text
1 → Conceptual direction
2 → Relevant principle
3 → Narrow the problem
4 → Strong clue
5 → Near-complete approach
6 → Full solution
```

Hint usage can be used as one signal in mastery estimation.

---

# 14. chat_sessions

Stores chatbot sessions.

```text
chat_sessions
-------------
id                  UUID PK
user_id             UUID FK → users.id
subject_id          UUID FK → subjects.id
lesson_id           UUID FK → lessons.id NULL
created_at          TIMESTAMP
updated_at          TIMESTAMP
```

The chatbot is scoped to the selected subject/syllabus.

---

# 15. chat_messages

Stores chat history.

```text
chat_messages
-------------
id                  UUID PK
session_id          UUID FK → chat_sessions.id
role                ENUM
content             TEXT
source_references   JSONB
created_at          TIMESTAMP
```

### role

```text
USER
ASSISTANT
SYSTEM
```

---

# 16. coding_submissions

Stores coding activity for computer science lessons.

```text
coding_submissions
------------------
id                  UUID PK
user_id             UUID FK → users.id
lesson_id           UUID FK → lessons.id
language            ENUM
source_code         TEXT
submitted_at        TIMESTAMP
```

### language

```text
C
CPP
JAVA
PYTHON
```

---

# 17. coding_execution_results

Stores sandbox execution results.

```text
coding_execution_results
------------------------
id                  UUID PK
submission_id       UUID FK → coding_submissions.id
compile_success     BOOLEAN
execution_success   BOOLEAN
tests_passed        INTEGER
tests_total         INTEGER
stdout              TEXT
stderr              TEXT
execution_time_ms   INTEGER
memory_used_mb      INTEGER
created_at          TIMESTAMP
```

The actual code execution occurs outside PostgreSQL inside an isolated sandbox.

---

# 18. learning_sessions

Stores learner sessions and mode information.

```text
learning_sessions
-----------------
id                  UUID PK
user_id             UUID FK → users.id
subject_id          UUID FK → subjects.id
mode                ENUM
current_chapter_id  UUID NULL
current_topic_id    UUID NULL
current_lesson_id   UUID NULL
started_at          TIMESTAMP
ended_at            TIMESTAMP
```

---

# 19. revision_summaries

Stores generated revision summaries.

```text
revision_summaries
------------------
id                  UUID PK
user_id             UUID FK → users.id
subject_id          UUID FK → subjects.id
chapter_id          UUID FK → chapters.id NULL
content             TEXT
storage_path        TEXT NULL
created_at          TIMESTAMP
```

A revision summary may include:

* Important concepts
* Definitions
* Formulas
* Key relationships
* Important examples
* Common mistakes
* Weak topics
* Frequently missed concepts
* Coding concepts where applicable

---

# 20. agent_runs

Stores metadata about agent executions.

```text
agent_runs
----------
id                  UUID PK
user_id             UUID FK → users.id
subject_id          UUID FK → subjects.id NULL
agent_type          VARCHAR
input_context       JSONB
decision             JSONB
status              ENUM
latency_ms          INTEGER
created_at          TIMESTAMP
```

This table is for application-level tracking.

Detailed traces can additionally be sent to the AI observability/evaluation system.

---

# 21. Neo4j — Curriculum Graph

Neo4j stores relationships that benefit from graph traversal.

## Core Nodes

```text
Subject
Chapter
Topic
Subtopic
Document
Student
```

## Core Relationships

```text
(:Subject)-[:HAS_CHAPTER]->(:Chapter)

(:Chapter)-[:HAS_TOPIC]->(:Topic)

(:Topic)-[:HAS_SUBTOPIC]->(:Subtopic)

(:Topic)-[:PREREQUISITE_FOR]->(:Topic)

(:Subtopic)-[:PREREQUISITE_FOR]->(:Subtopic)
```

---

# 22. Neo4j — Student Mastery Graph

Student-specific learning state is represented as graph relationships and properties.

Example:

```text
(:Student)-[:LEARNING {
    mastery: 0.62,
    attempts: 4,
    difficulty: "MEDIUM",
    last_attempt: "...",
    last_revision: "..."
}]->(:Topic)
```

Weakness:

```text
(:Student)-[:WEAK_IN {
    mastery: 0.38
}]->(:Topic)
```

Mastery:

```text
(:Student)-[:MASTERED {
    mastery: 0.91
}]->(:Topic)
```

---

# 23. Example Knowledge Graph

```text
                    ┌──────────────┐
                    │   Subject    │
                    │ Data Struct. │
                    └──────┬───────┘
                           │
                      HAS_CHAPTER
                           ↓
                    ┌──────────────┐
                    │   Chapter 1  │
                    └──────┬───────┘
                           │
                       HAS_TOPIC
                           ↓
                    ┌──────────────┐
                    │   Recursion  │
                    └──────┬───────┘
                           │
                   PREREQUISITE_FOR
                           ↓
                    ┌──────────────┐
                    │     Trees    │
                    └──────────────┘

(Student)
    │
    └── WEAK_IN ──> (Recursion)
```

The Learning Manager can query this graph to identify prerequisite weaknesses before advancing the learner.

---

# 24. Student Mastery Data

The mastery graph should track signals such as:

```text
mastery_score
attempt_count
correct_count
incorrect_count
hint_usage
difficulty_performance
last_attempt
last_revision
weak_concepts
confidence
```

The exact mastery algorithm is an application-level concern rather than a database constraint.

---

# 25. Qdrant — Vector Database

Qdrant stores embeddings for semantic retrieval.

## Content Types

```text
SOURCE_PAGE
SOURCE_CHUNK
GENERATED_NOTE
LESSON_CONTENT
REVISION_CONTENT
```

The initial priority is source-grounded retrieval.

---

# 26. Qdrant Payload

Each vector should contain metadata similar to:

```json
{
  "document_id": "...",
  "page_number": 17,
  "subject_id": "...",
  "chapter_id": "...",
  "topic_id": "...",
  "subtopic_id": "...",
  "content_type": "SOURCE_PAGE",
  "source_type": "UPLOADED_DOCUMENT"
}
```

This allows retrieval results to be filtered by:

* Subject
* Chapter
* Topic
* Subtopic
* Document
* Page

---

# 27. RAG Retrieval Flow

```text
Student Question
       ↓
Query Embedding
       ↓
Qdrant Search
       ↓
Relevant Source Chunks
       ↓
Filter by Subject
       ↓
Student Context
       ↓
Qwen
       ↓
Grounded Response
```

---

# 28. Redis

Redis is not the primary persistent database.

It is used for operational state.

## Uses

```text
Redis
├── Session cache
├── Agent temporary state
├── Background job queue
├── Rate limiting
├── Streaming state
└── Frequently accessed cache
```

---

# 29. Data Ownership

Each datastore has a clear responsibility.

| Data                          | Database   |
| ----------------------------- | ---------- |
| Users                         | PostgreSQL |
| Subjects                      | PostgreSQL |
| Documents metadata            | PostgreSQL |
| Page metadata                 | PostgreSQL |
| Quiz records                  | PostgreSQL |
| Quiz attempts                 | PostgreSQL |
| Chat history                  | PostgreSQL |
| Coding submissions            | PostgreSQL |
| Execution results             | PostgreSQL |
| Curriculum relationships      | Neo4j      |
| Prerequisites                 | Neo4j      |
| Student mastery relationships | Neo4j      |
| Student learning state        | Neo4j      |
| PDF semantic content          | Qdrant     |
| Embeddings                    | Qdrant     |
| Retrieval metadata            | Qdrant     |
| Temporary state               | Redis      |
| Background jobs               | Redis      |

---

# 30. Source of Truth

The system should avoid unnecessary duplication.

### PostgreSQL

Source of truth for transactional application entities.

### Neo4j

Source of truth for curriculum and learner relationship graphs.

### Qdrant

Source of truth for vectorized retrieval representations, while original files remain in file storage.

### Redis

Never treated as permanent source of truth.

---

# 31. Relationships Between Databases

The databases are connected through stable UUIDs.

Example:

```text
PostgreSQL
topic_id = 7f...

        ↓

Neo4j
Topic.external_id = "7f..."

        ↓

Qdrant
payload.topic_id = "7f..."
```

This allows the same curriculum entity to be referenced across systems without duplicating its entire data model.

---

# 32. File Storage

Original uploaded files and generated artifacts should not be stored directly in PostgreSQL.

Initial local storage:

```text
/storage
├── documents/
├── pages/
├── generated/
└── revisions/
```

PostgreSQL stores references such as:

```text
storage_path
```

---

# 33. Multi-Document Subject

A single subject can contain multiple uploaded files.

```text
Subject
│
├── Document 1
├── Document 2
├── Document 3
└── Document 4
```

All documents contribute to:

* Curriculum generation
* Retrieval
* Notes
* Quizzes
* Chatbot context

The system must retain source-level attribution so the learner can distinguish where information came from.

---

# 34. Data Flow

## Document Processing

```text
File
 ↓
PostgreSQL metadata
 ↓
OCR / Vision
 ↓
document_pages
 ↓
Curriculum Extraction
 ↓
PostgreSQL metadata
 ↓
Neo4j curriculum graph
 ↓
Embeddings
 ↓
Qdrant
```

## Quiz Attempt

```text
Question
 ↓
Student Answer
 ↓
PostgreSQL quiz_answer
 ↓
LLM Evaluation
 ↓
Mastery Update
 ↓
Neo4j Student Mastery Graph
```

## Automatic Learning

```text
Neo4j Student State
        +
PostgreSQL History
        +
Qdrant Source Context
        ↓
Learning Manager
        ↓
Action
        ↓
Outcome
        ↓
Update databases
        ↓
Replan
```

---

# 35. Database Infrastructure

Initial local development should use Docker Compose.

```text
docker-compose
│
├── postgres
├── neo4j
├── qdrant
├── redis
└── ollama
```

The architecture should allow each component to be replaced with a managed cloud service later.

---

# 36. Database Backup Requirements

PostgreSQL and Neo4j should have regular backups once the system contains real learner data.

Qdrant collections should be recoverable from source documents and embeddings, but backups are still recommended.

Redis data should be considered disposable unless a specific queue/state requires persistence.

---

# 37. Security Requirements

Database access must be isolated from public internet access.

Requirements:

* Database credentials stored in environment variables/secrets
* Separate service credentials
* No direct client access to PostgreSQL, Neo4j, or Qdrant
* Backend-only database connectivity
* Per-user authorization at application level
* Strict subject/user ownership checks
* Sensitive logs must not contain passwords or authentication tokens

---

# 38. Future Scalability

The initial architecture is optimized for low-cost local deployment.

Future migration may replace:

```text
Local PostgreSQL → Managed PostgreSQL
Local Neo4j → Managed Neo4j
Local Qdrant → Managed Vector DB
Local Storage → S3/R2
Local Redis → Managed Redis
Local Qwen → Cloud GPU/LLM
```

The logical data model should remain unchanged where practical.

---

# 39. Final Database Stack

```text
┌───────────────────────────────────────────┐
│              APPLICATION                  │
└─────────────────────┬─────────────────────┘
                      │
           ┌──────────┼──────────┐
           ↓          ↓          ↓
     PostgreSQL     Neo4j      Qdrant
     ───────────   ─────────   ─────────
     App data      Curriculum  Semantic
     Transactions  + Mastery   Retrieval
           │          │          │
           └──────────┼──────────┘
                      │
                    Redis
                Cache / Queue
```

The database architecture is therefore:

**PostgreSQL + Neo4j + Qdrant + Redis**, with local file storage initially and a migration path to cloud/object storage later.
