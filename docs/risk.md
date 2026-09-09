# RISKS.md

# Risks, Constraints & Validation Plan

## 1. Purpose

This document defines the major:

* Product constraints
* Technical constraints
* AI limitations
* Security risks
* Data risks
* Performance risks
* Agentic-system risks
* Coding sandbox risks
* Validation and testing requirements

The objective is to identify failure points before production deployment and define what must be tested.

---

# 2. Core Constraints

## 2.1 Initial Budget Constraint

The initial system must operate with minimal or zero recurring infrastructure cost.

### Constraint

* Prefer local/open-source software.
* Primary LLM should run locally.
* Databases should run locally.
* OCR should run locally.
* Coding execution should run locally in isolated containers.
* Paid APIs should not be required for the MVP.

### Risk

Local inference may be too slow or memory-intensive for larger models.

### Must Test

* LLM latency
* GPU/CPU utilization
* RAM/VRAM requirements
* Concurrent user limits
* Context-length limitations

---

# 3. LLM Constraints

## 3.1 Model Accuracy

The system depends heavily on the primary LLM for:

* Curriculum extraction
* Notes
* Quiz generation
* Quiz evaluation
* Hints
* Personalization
* Planning
* Chat

### Risk

The LLM may:

* Hallucinate
* Misinterpret content
* Generate incorrect answers
* Generate invalid MCQs
* Misjudge difficulty
* Give overly strong hints
* Make incorrect learning decisions

### Must Test

Create a fixed evaluation dataset and test:

* Content accuracy
* Question accuracy
* Answer accuracy
* Explanation accuracy
* Difficulty accuracy
* Hint progression
* Planning decisions

---

# 4. Model Replacement Constraint

The application should not be tightly coupled to Qwen.

### Constraint

The LLM interface must allow future replacement with another local or cloud model.

### Risk

Model-specific prompts or output formats may break when changing models.

### Must Test

Run the same evaluation suite against at least two compatible models.

---

# 5. Document Understanding Risks

## 5.1 Handwritten PDFs

Handwriting introduces significant uncertainty.

### Risks

* Poor handwriting
* Low-resolution scans
* Unusual handwriting styles
* Overlapping text
* Mathematical symbols
* Diagrams
* Cut-off content
* Shadows
* Skewed pages

### Required Behavior

The system must not invent unreadable content.

When confidence is insufficient:

```text id="l6etbl"
Low-confidence page
        ↓
Identify page
        ↓
Request user re-upload/re-capture
```

### Must Test

Test with:

* Clean handwriting
* Messy handwriting
* Different handwriting styles
* Mixed printed/handwritten pages
* Low-resolution scans
* Rotated pages
* Images containing diagrams
* Tables
* Mathematical notation

---

# 6. OCR Risks

### Risks

OCR may:

* Misread characters
* Merge words
* Miss symbols
* Duplicate text
* Change numbers
* Misread formulas
* Misread programming syntax

### High-risk examples

```text
0 ↔ O
1 ↔ l ↔ I
5 ↔ S
+ ↔ t
() ↔ {}
; ↔ :
```

### Must Test

Compare OCR output against manually verified ground truth.

Measure:

* Character accuracy
* Word accuracy
* Structural accuracy
* Mathematical-symbol accuracy
* Code-token accuracy

---

# 7. Curriculum Extraction Risks

The platform must infer:

```text
Subject
 → Chapter
 → Topic
 → Subtopic
```

### Risks

* Incorrect chapter boundaries
* Missing topics
* Duplicate topics
* Wrong hierarchy
* Incorrect prerequisite relationships
* Misinterpretation of document headings

### Must Test

For sample syllabi, manually create the expected hierarchy and compare generated output.

Metrics:

* Topic recall
* Topic precision
* Hierarchy accuracy
* Duplicate rate
* Prerequisite accuracy

---

# 8. Multi-Document Risks

A subject can contain multiple files.

### Risks

* Conflicting information
* Duplicate content
* Different terminology
* Different chapter structures
* Contradictory explanations
* Incorrect source attribution

### Must Test

Upload:

```text id="iy1jnv"
Syllabus.pdf
Notes.pdf
Book.pdf
Slides.pdf
```

Then verify:

* Correct topic merging
* Duplicate detection
* Source attribution
* Conflict handling
* Retrieval accuracy

---

# 9. External Knowledge Risk

The chatbot may optionally use external LLM knowledge.

### Risk

External information may be mistaken for syllabus-derived information.

### Constraint

The interface must clearly distinguish:

```text id="sci8do"
From uploaded material
```

and

```text id="i0xx9q"
External explanation
```

### Must Test

Ask questions that:

* Exist in the PDF
* Do not exist in the PDF
* Contradict the PDF
* Partially exist in the PDF

Verify correct source labeling.

---

# 10. RAG Risks

## 10.1 Retrieval Failure

The correct information may exist in the source but fail to be retrieved.

### Risks

* Wrong chunks
* Irrelevant chunks
* Missing page
* Poor chunk boundaries
* Incorrect metadata filtering

### Must Test

Evaluate:

* Recall@K
* Precision@K
* Source-page accuracy
* Topic filtering accuracy

---

# 11. Quiz Generation Risks

## 11.1 Invalid Questions

The LLM may generate:

* Ambiguous questions
* Multiple correct answers
* No correct answer
* Incorrect options
* Questions unsupported by source content
* Duplicate questions

### Must Test

Every generated question should pass automated validation.

Test for:

```text id="gk2g6h"
Exactly one correct answer
Question is source-grounded
Options are distinct
Explanation is correct
Question is unambiguous
Difficulty is valid
```

---

# 12. Quiz Evaluation Risks

### Risk

The LLM may incorrectly evaluate a learner's answer.

This is particularly risky when evaluation depends on semantic understanding.

### Must Test

Create a manually labeled test set containing:

* Correct answers
* Incorrect answers
* Partially correct answers
* Ambiguous answers
* Edge cases

Measure evaluator agreement with human labels.

---

# 13. MCQ Difficulty Risks

Difficulty is divided into:

```text
Easy
Medium
Hard
```

### Risk

LLM-generated difficulty labels may not correspond to actual learner difficulty.

### Must Test

Compare predicted difficulty against actual learner success rates.

Difficulty should be recalibrated based on real performance.

---

# 14. Adaptive Learning Risks

The platform may make incorrect decisions about what the learner should study next.

### Examples

```text
Student is weak in prerequisite A
        ↓
Agent incorrectly advances to B
```

or:

```text
Student has mastered A
        ↓
Agent repeatedly gives basic A questions
```

### Must Test

Create simulated student profiles:

* Strong learner
* Weak learner
* Mixed-performance learner
* Rapidly improving learner
* Repeated-failure learner

Verify that the learning path changes appropriately.

---

# 15. Automatic Mode Risks

Automatic Mode is the highest-risk agentic component because the system can make multiple decisions autonomously.

### Risks

* Infinite loops
* Repeated remediation
* Premature advancement
* Excessive assessments
* Unnecessary content generation
* Wrong topic selection
* Agent state corruption

### Required Controls

The system should include:

* Maximum agent steps
* Maximum retry count
* State validation
* Loop detection
* Fallback behavior
* Human override

### Must Test

Simulate:

* Successful learning path
* Repeated quiz failure
* Missing source content
* Invalid generated quiz
* Tool failure
* LLM failure
* Database failure
* Agent loop

---

# 16. Guided Mode Risks

### Risk

The system recommendation may be poor, but the learner remains responsible for choosing.

### Must Test

Verify:

* Recommendation quality
* Ability to ignore recommendation
* Correct continuation after rejection
* No forced progression

---

# 17. Manual Mode Risks

### Risk

Users can skip prerequisite topics.

### Required Behavior

The system should inform the user when their selection conflicts with known prerequisites.

Example:

```text id="qh8h5e"
You selected Trees.

Recursion is currently weak and is marked as a prerequisite.

[Continue Anyway]
[Revise Prerequisite]
```

The system should not silently prevent manual navigation.

---

# 18. Student Mastery Graph Risks

The Student Mastery Graph directly influences personalization.

### Risks

* Incorrect mastery score
* Stale data
* Contradictory state
* Incorrect relationship mapping
* Score inflation from repeated easy attempts

### Must Test

Verify mastery updates under:

* Correct answer
* Incorrect answer
* Repeated correct answer
* Repeated failure
* Easy → Medium transition
* Medium → Hard transition
* Hint usage
* Long gaps between attempts
* Retakes

---

# 19. Mastery Score Manipulation

A learner could repeatedly answer easy questions until mastery becomes artificially high.

### Constraint

Mastery must not depend solely on raw percentage.

It should account for:

* Difficulty
* Attempts
* Hint dependency
* Recency
* Historical consistency

### Must Test

Compare:

```text
10/10 Easy
```

against:

```text
7/10 Hard
```

and verify that the mastery model behaves sensibly.

---

# 20. Hint System Risks

The six-tier system must encourage learning instead of immediately revealing the solution.

### Risks

* Tier 1 too informative
* Tier 6 not actually solving the problem
* Same hint repeated across levels
* Later hints revealing answer too early
* Hint content inconsistent with question

### Must Test

Every question should be manually sampled across all six tiers.

Verify:

```text
Tier 1 < Tier 2 < Tier 3 < Tier 4 < Tier 5 < Tier 6
```

in terms of information provided.

---

# 21. Chatbot Risks

### Risks

* Hallucinations
* Context leakage
* Irrelevant answers
* Ignoring current lesson
* Ignoring student mastery
* Giving unsupported information

### Must Test

Ask:

* Direct syllabus questions
* Cross-topic questions
* Out-of-syllabus questions
* Ambiguous questions
* Questions requiring source retrieval
* Questions involving weak topics

---

# 22. Chat Context Risks

The chatbot receives:

```text
Source Context
+
Current Lesson
+
Student Mastery
+
Conversation Context
```

### Risk

Too much context may exceed model context limits.

### Must Test

Measure:

* Context length
* Retrieval size
* Response latency
* Accuracy with long conversations
* Conversation truncation behavior

---

# 23. Coding Sandbox Risks

The coding environment is a major security risk because arbitrary code is executed.

### Threats

* Infinite loops
* Fork bombs
* Memory exhaustion
* CPU exhaustion
* Filesystem attacks
* Network access
* Process spawning
* Escape from container
* Malicious system calls

### Required Controls

Every execution must have:

* CPU limit
* Memory limit
* Execution timeout
* Process limit
* Read/write filesystem isolation
* No unnecessary network access
* Restricted system calls
* Container isolation

---

# 24. Coding Language Risks

Supported languages:

```text
C
C++
Java
Python
```

### Must Test

For every language:

* Successful compilation
* Syntax errors
* Runtime errors
* Infinite loops
* Memory-heavy programs
* Large input
* Unicode input
* File access attempts
* Network attempts
* Process spawning attempts

---

# 25. Coding Evaluation Risks

### Risks

* Incorrect test cases
* Weak test coverage
* False positive
* False negative
* Incorrect complexity feedback
* LLM misdiagnosis

### Must Test

Use manually verified coding problems with known expected outputs.

Verify:

```text
Correct solution → PASS
Incorrect solution → FAIL
Edge-case failure → FAIL
Timeout → TIMEOUT
Compilation error → COMPILE_ERROR
Runtime crash → RUNTIME_ERROR
```

---

# 26. Revision Summary Risks

The final summary may omit important information.

### Risks

* Important concept missing
* Weak topic omitted
* Incorrect formula
* Incorrect definition
* Excessive compression

### Must Test

Compare generated revision files against:

* Source material
* Curriculum
* Student mistake history

Verify important concepts are preserved.

---

# 27. Data Consistency Risks

The same topic exists across:

```text
PostgreSQL
Neo4j
Qdrant
```

### Risk

Identifiers or state may become inconsistent.

### Must Test

Verify:

```text
PostgreSQL topic_id
=
Neo4j topic external_id
=
Qdrant metadata.topic_id
```

after:

* Creation
* Update
* Deletion
* Regeneration

---

# 28. Database Failure Risks

### Must Test

Simulate:

* PostgreSQL unavailable
* Neo4j unavailable
* Qdrant unavailable
* Redis unavailable
* Connection timeout
* Partial database failure

The application must fail gracefully and avoid corrupting learner state.

---

# 29. Background Processing Risks

Document processing may involve:

```text
Upload
→ OCR
→ Vision
→ Embedding
→ Curriculum extraction
→ Graph construction
→ Quiz generation
```

### Risks

* Worker crashes
* Duplicate jobs
* Partial processing
* Job retries generating duplicate data
* Corrupted state

### Must Test

* Worker restart
* Job retry
* Duplicate submission
* Partial failure
* Interrupted processing

Jobs should be idempotent where practical.

---

# 30. Privacy Risks

Educational documents may contain sensitive information.

### Risks

* Student data leakage
* Document leakage between users
* Chat history leakage
* Incorrect authorization
* Logs containing private content

### Must Test

Verify strict isolation:

```text
User A
   ✕
User B's Documents

User A
   ✕
User B's Mastery Graph

User A
   ✕
User B's Chat History
```

---

# 31. Authentication Risks

### Must Test

* Unauthorized API access
* Invalid tokens
* Expired tokens
* Session hijacking scenarios
* Direct access to another user's subject IDs
* Direct access to another user's documents

Authorization must be checked server-side.

---

# 32. File Upload Risks

### Risks

* Malicious files
* Oversized files
* Unsupported files
* Corrupted PDFs
* ZIP bombs or equivalent archive attacks
* File path manipulation

### Must Test

* Maximum file size
* Maximum page count
* Invalid MIME type
* Corrupted PDF
* Malicious filename
* Duplicate upload
* Extremely large document

---

# 33. Performance Risks

## Potential Bottlenecks

```text
OCR
↓
LLM inference
↓
Embeddings
↓
Vector retrieval
↓
Agent execution
↓
Compiler execution
```

### Must Test

Measure:

* Single-user latency
* Multiple simultaneous users
* Large PDF processing time
* Quiz generation latency
* Chat latency
* Coding execution latency
* Memory/VRAM usage

---

# 34. Local Deployment Constraint

Because the MVP is intended to run locally:

### Must Test Minimum Hardware Profile

Determine the minimum practical hardware required for:

* OCR
* Qwen inference
* PostgreSQL
* Neo4j
* Qdrant
* Redis
* Docker
* Compiler sandbox

A lightweight fallback model should be considered if the selected Qwen model cannot run on common student hardware.

---

# 35. Agent Tool Failure Risks

Agents depend on tools such as:

* Retriever
* Graph database
* Quiz engine
* Compiler
* Document processor

### Risk

The agent may receive:

* Timeout
* Empty result
* Malformed result
* Tool error
* Partial result

### Must Test

Every tool must expose structured success/failure states.

The agent should never assume a failed tool succeeded.

---

# 36. Structured Output Risks

LLM outputs should be machine-readable where required.

### Risk

The model returns invalid JSON or missing fields.

### Must Test

Examples:

```text id="d7tptq"
Missing field
Wrong type
Invalid enum
Malformed JSON
Extra unexpected fields
```

Use schema validation and retry/fallback logic.

---

# 37. Infinite Agent Loop Risk

Example:

```text
Evaluate
 ↓
Weak
 ↓
Re-teach
 ↓
Evaluate
 ↓
Weak
 ↓
Re-teach
 ↓
...
```

### Required Constraint

Automatic Mode must have:

* Maximum planning iterations
* Maximum remediation attempts
* Loop detection
* Safe fallback to learner choice

### Must Test

Create deliberately failing student profiles and confirm the agent terminates.

---

# 38. Cost/Resource Explosion

Even with local AI, agentic workflows can create excessive computation.

### Risks

* Repeated LLM calls
* Excessive quiz regeneration
* Large document processing
* Infinite retries
* Large context windows

### Must Test

Track:

* LLM calls/session
* Tokens/session
* Processing time
* Retry count
* Agent steps
* Storage growth

---

# 39. Evaluation Harness Requirements

The AI evaluation layer must maintain regression datasets.

## Test Categories

```text
Document Understanding
Curriculum Extraction
RAG
Quiz Generation
Quiz Evaluation
Hint Generation
Mastery Updates
Learning Planning
Chatbot
Coding Evaluation
Revision Generation
```

Every major prompt/model change should run the regression suite.

---

# 40. Human Evaluation

Not every AI quality metric can be automated.

A human evaluation set should periodically review:

* Notes quality
* Quiz validity
* Hint usefulness
* Explanation correctness
* Learning-plan quality
* Chatbot grounding
* Revision quality

---

# 41. Acceptance Test Scenarios

## Scenario A — Normal Subject

```text
Upload Biology PDF
→ Process
→ Generate Chapters
→ Generate Topics
→ Generate Notes
→ Generate Quizzes
→ Study
→ Final Quiz
→ Update Mastery
→ Generate Revision
```

All steps must succeed.

---

## Scenario B — Handwritten PDF

```text
Upload handwritten page
→ OCR/Vision
→ High confidence
→ Continue
```

Low-confidence variant:

```text
Upload unclear page
→ Detect low confidence
→ Ask for re-upload
→ Continue after replacement
```

---

## Scenario C — Weak Topic

```text
Topic A = 90%
Topic B = 35%

Final quiz
→ Higher weighting for Topic B
→ Student retakes
→ Mastery updates
```

Verify the agent identifies Topic B as the priority.

---

## Scenario D — Automatic Mode

```text
Study
→ Quiz
→ Fail
→ Analyze weakness
→ Remedial lesson
→ Re-test
→ Improve
→ Advance
```

Verify no manual topic selection is required.

---

## Scenario E — Coding Subject

```text
Study theory
→ Coding lesson
→ Write C++ code
→ Compile
→ Run tests
→ Receive result
→ Mastery update
→ Continue learning
```

---

# 42. Critical Security Tests

The following must be treated as blocking tests before production:

```text
[ ] Container escape
[ ] Network access from sandbox
[ ] Filesystem escape
[ ] Resource exhaustion
[ ] Cross-user data access
[ ] Unauthorized API access
[ ] Malicious upload
[ ] Credential exposure
[ ] Database credential exposure
```

---

# 43. Critical AI Tests

These should also be blocking for Automatic Mode:

```text
[ ] Hallucination control
[ ] Source grounding
[ ] Correct quiz answer
[ ] Correct quiz evaluation
[ ] Correct weakness detection
[ ] Correct prerequisite reasoning
[ ] Correct next-topic planning
[ ] Six-tier hint ordering
[ ] No infinite agent loops
[ ] Correct mastery updates
```

---

# 44. MVP Risk Priority

## P0 — Must Resolve

* Code sandbox security
* Cross-user data isolation
* Hallucinated educational content
* Invalid quiz generation
* Incorrect quiz evaluation
* Agent infinite loops
* Database consistency
* Handwriting failure handling

## P1 — High Priority

* RAG retrieval quality
* Difficulty calibration
* Mastery accuracy
* Automatic planning quality
* OCR accuracy
* Local LLM performance

## P2 — Later

* Advanced analytics
* Large-scale concurrency
* Cloud optimization
* Additional modalities
* Advanced question types

---

# 45. Principle for Reliability

The system should follow:

> **When the AI is uncertain, it should ask, retrieve, validate, or defer rather than confidently fabricate.**

For autonomous decisions:

> **Every autonomous action must have a bounded execution path, observable state, and recoverable failure mode.**

For learner data:

> **Student memory must be persistent, explainable, and isolated.**

For code execution:

> **Untrusted code must always execute inside a strongly isolated environment.**
