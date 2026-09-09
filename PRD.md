# Product Requirements Document (PRD)

## 1. Product Overview

### Product Name

**Autonomous AI-Based Learning Platform**

### Product Vision

Build an autonomous, personalized learning platform that can understand a learner’s syllabus and study material, convert it into a structured learning curriculum, teach and assess the learner, identify knowledge gaps, and continuously adapt the learning journey to improve mastery.

The platform is designed for **all academic subjects**, not only engineering or computer science. Computer science learners receive an additional coding environment integrated directly into relevant lessons.

### Core Value Proposition

The platform transforms static educational material into an **adaptive learning system** that continuously understands:

* What the learner needs to study
* What the learner already understands
* Where the learner is struggling
* What should be taught next
* When a topic should be revised
* How difficult the next assessment should be

---

# 2. Goals

## Primary Goals

1. Accept educational material in multiple formats, including digital and handwritten PDFs.
2. Understand the uploaded material and automatically structure it into:

   * Subject
   * Chapters
   * Topics
   * Subtopics
3. Generate page-level bullet-point notes from the source material.
4. Generate topic-level quizzes and chapter-level final quizzes.
5. Build a personalized learning plan from the learner’s syllabus and performance.
6. Maintain persistent student mastery information using a graph-based representation.
7. Automatically identify weak concepts and adapt future learning activities.
8. Provide a syllabus-grounded chatbot throughout the learning experience.
9. Provide a six-level hint system that promotes reasoning instead of immediately revealing answers.
10. Provide a coding environment for relevant computer science lessons.
11. Support Guided, Manual, and Automatic learning modes.
12. Run initially using a locally hosted LLM to minimize infrastructure cost.

## Secondary Goals

1. Support repeated quiz attempts.
2. Dynamically adjust quiz difficulty across Easy, Medium, and Hard levels.
3. Generate a complete revision summary for a chapter or subject.
4. Keep the learner’s historical performance available across sessions.
5. Provide transparent distinction between information sourced from uploaded material and external knowledge.

---

# 3. Non-Goals

The initial version will not:

1. Replace formal teachers or institutional learning management systems.
2. Provide unrestricted general-purpose chatbot conversations unrelated to the learner’s syllabus.
3. Provide a coding compiler outside relevant coding lessons.
4. Initially support every possible question type; the first assessment version will focus on MCQs.
5. Initially depend on paid cloud infrastructure.
6. Automatically execute arbitrary code outside an isolated compiler/sandbox environment.
7. Treat external LLM knowledge as syllabus content without explicitly identifying it as external.

---

# 4. Target Users

## Primary Users

Students and independent learners studying academic or professional subjects.

Examples:

* School students
* College students
* Engineering students
* Medical students
* Commerce students
* Science students
* Competitive-exam learners
* Self-learners

## Secondary Users

Future versions may support:

* Teachers
* Tutors
* Coaching institutes
* Educational organizations

---

# 5. Core User Journey

```text
User creates/selects a subject
        ↓
Uploads one or more files
        ↓
System processes and understands material
        ↓
Builds curriculum structure
        ↓
Creates notes + quizzes
        ↓
Builds learning plan
        ↓
User selects:
Guided / Manual / Automatic
        ↓
Learns topic
        ↓
Takes quiz
        ↓
AI evaluates performance
        ↓
Student Mastery Graph updated
        ↓
Weak areas identified
        ↓
Next learning activity determined
        ↓
Repeat until chapter/subject mastery
        ↓
Generate complete revision summary
```

---

# 6. Input Requirements

## 6.1 Supported Learning Material

The platform shall support:

* PDF
* PPT/PPTX
* Multiple files for one subject

Initial implementation may prioritize PDF as the primary format.

## 6.2 PDF Types

The system shall support:

* Digitally generated PDFs
* Scanned PDFs
* Handwritten PDFs

## 6.3 Multiple Sources

A user may upload multiple documents belonging to the same subject.

Example:

```text
Data Structures
├── University Syllabus.pdf
├── Unit 1 Notes.pdf
├── Unit 2 Notes.pdf
└── Reference Book.pdf
```

The system shall combine the sources while maintaining source attribution.

---

# 7. Document Understanding Pipeline

## 7.1 Processing Flow

```text
Uploaded File
      ↓
Document Parser
      ↓
Page Extraction
      ↓
OCR / Vision Processing
      ↓
Content Validation
      ↓
LLM Understanding
      ↓
Structured Curriculum
```

## 7.2 OCR and Vision

The system shall use OCR and vision preprocessing so the LLM receives clear textual and visual information.

It shall support:

* Printed text extraction
* Handwriting recognition
* Diagrams
* Tables
* Mathematical notation where feasible
* Mixed handwritten/digital pages

## 7.3 Unclear Content Handling

When the system cannot confidently understand a page:

```text
Unclear page detected
        ↓
Identify page
        ↓
Ask user to re-upload/re-capture page
        ↓
Reprocess
```

The system should not silently fabricate missing content.

---

# 8. Curriculum Generation

The platform shall automatically derive a curriculum hierarchy from the uploaded material.

## 8.1 Hierarchy

```text
Subject
 └── Chapter
      └── Topic
           └── Subtopic
                └── Learning Content
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
    └── 1.2 Searching
        ├── 1.2.1 Linear Search
        └── 1.2.2 Binary Search
```

The system shall preserve meaningful headings from the original document where possible while allowing the LLM to infer missing structure.

---

# 9. Page-Level Notes

For each processed page, the platform shall generate concise bullet-point notes.

Example:

```text
Page 17 — Binary Search

• Searches a sorted array
• Uses divide-and-conquer
• Compares target with middle element
• Reduces search space by half
• Time complexity: O(log n)
```

Notes shall remain grounded in the uploaded material.

---

# 10. Source Grounding and External Knowledge

The platform shall allow the learner to control whether the AI may use information outside the uploaded material.

## 10.1 Modes

### Syllabus-Only

The system answers using uploaded material only.

### Syllabus + External Knowledge

The system may supplement the material using the LLM’s external knowledge.

## 10.2 External Information Flagging

When external knowledge is used, the response shall clearly distinguish:

```text
From your material:
...

External explanation:
...
```

The system must not present externally generated information as if it came from the uploaded source.

---

# 11. Learning Modes

The platform shall provide three learning modes.

## 11.1 Guided Mode

The AI recommends the next learning activity.

The learner remains in control and can accept or skip recommendations.

```text
AI:
"Your performance suggests revising Topic 1.2 first."

User:
[Follow Recommendation]
[Choose Something Else]
```

## 11.2 Manual Mode

The learner selects the chapter, topic, or subtopic directly.

No autonomous sequencing is required.

## 11.3 Automatic Mode

The system autonomously decides the learning sequence.

The AI may:

* Select the next topic
* Choose whether to teach or assess
* Identify prerequisites
* Detect weak areas
* Revisit previous topics
* Adjust difficulty
* Trigger remediation
* Re-test the learner
* Advance the learner to subsequent topics

The automatic learning loop shall be:

```text
Observe
 ↓
Analyze
 ↓
Plan
 ↓
Teach / Assess
 ↓
Evaluate
 ↓
Update Mastery
 ↓
Replan
```

---

# 12. Lesson Structure

Each lesson shall follow a structured hierarchy.

For a normal subject:

```text
Chapter 1
│
├── 1.1 Topic
│   ├── 1.1.1 Subtopic
│   ├── 1.1.2 Subtopic
│   ├── 1.1.3 Subtopic
│   └── Topic Quiz
│
├── 1.2 Topic
│   ├── 1.2.1 Subtopic
│   ├── 1.2.2 Subtopic
│   └── Topic Quiz
│
└── Chapter Final Quiz
```

Topic-level quizzes are optional during progression.

Chapter final quizzes are comprehensive and mandatory as the primary chapter-level assessment.

---

# 13. Learning Content

Each subtopic should provide learning content derived from the uploaded material.

The system may include:

* Bullet-point explanation
* Examples
* Clarifications
* Concept relationships
* Key facts
* Important definitions
* Formulas where applicable

The content should be appropriate to the learner’s current state.

---

# 14. Quiz System

## 14.1 Initial Quiz Type

The first version shall use **multiple-choice questions (MCQs)**.

## 14.2 Quiz Levels

Each quiz shall support three difficulty levels:

* Easy
* Medium
* Hard

## 14.3 Topic Quizzes

A topic may contain a small quiz designed to check immediate understanding.

## 14.4 Chapter Final Quiz

The chapter final quiz shall evaluate the entire chapter.

The final quiz shall:

* Cover all relevant topics
* Give additional weighting to weak topics
* Use the learner’s previous performance
* Adjust difficulty dynamically
* Detect recurring weaknesses

Example:

```text
Topic A → 90%
Topic B → 45%
Topic C → 78%

Next final quiz weighting:
Topic A → lower
Topic B → higher
Topic C → medium
```

## 14.5 Repeated Attempts

Learners shall be able to retake quizzes.

Each attempt shall be recorded.

Future quiz generation may consider:

* Previous score
* Previous incorrect questions
* Topic mastery
* Difficulty performance
* Number of attempts

---

# 15. Quiz Evaluation

After submission, the LLM shall evaluate the learner’s answers.

The evaluation system shall identify:

* Correct answers
* Incorrect answers
* Topic associated with each mistake
* Relevant concept
* Reason for the mistake where inferable
* Correct solution/explanation

Example:

```text
Question:
Which traversal visits the root before its children?

Your answer:
B

Correct answer:
A

Why:
Preorder traversal processes the root before its subtrees.
```

The system shall update the learner’s mastery state based on the evaluation.

---

# 16. Hint System

The platform shall implement a fixed **six-tier hint system**.

The system shall not immediately reveal the solution.

Example progression:

```text
Tier 1 → Think about the relevant concept
Tier 2 → Recall an important principle
Tier 3 → Narrow the relevant idea
Tier 4 → Give a stronger conceptual clue
Tier 5 → Provide an almost-complete approach
Tier 6 → Provide the solution
```

The exact hint content shall vary by question.

The six tiers shall remain fixed in structure.

---

# 17. Personalized Learning

Personalization shall be driven by the learner’s accumulated performance.

The system shall consider:

* Topic mastery
* Subtopic mastery
* Quiz scores
* Incorrect answers
* Attempts
* Difficulty level
* Hint usage
* Historical performance
* Recency of learning
* Prerequisite relationships

The platform shall be capable of deciding:

```text
Mastered → Advance
Weak → Re-teach
Very Weak → Revisit prerequisite
Improving → Increase difficulty
Repeated Failure → Targeted practice
```

---

# 18. Student Mastery Graph

The system shall maintain persistent learner memory using a graph-based representation.

## 18.1 Curriculum Graph

Represents the subject structure.

```text
Topic A
   │
prerequisite_for
   ↓
Topic B
```

## 18.2 Student Mastery Graph

Represents the learner’s state against the curriculum.

Example:

```text
Recursion
├── mastery: 0.62
├── difficulty: Medium
├── attempts: 4
├── status: Learning
├── weak_concepts: Base Case
└── last_reviewed: ...
```

## 18.3 Graph Relationships

The system may represent relationships such as:

```text
Topic A → prerequisite_for → Topic B
Student → studying → Topic A
Student → mastered → Topic B
Student → weak_in → Subtopic C
```

The mastery graph shall be used by the learning manager for planning and personalization.

---

# 19. Student Memory

The platform shall persist learner history across sessions.

Memory may include:

* Topic mastery
* Subtopic mastery
* Quiz attempts
* Incorrect questions
* Mistake patterns
* Hint usage
* Difficulty performance
* Revision history
* Last activity
* Learning progress

A returning learner should be able to continue from their previous state.

---

# 20. Persistent Syllabus Chatbot

A ChatGPT-style chatbot shall remain available throughout the learning experience.

## Capabilities

The chatbot shall:

* Answer questions about uploaded material
* Explain concepts
* Clarify confusing content
* Discuss topics
* Use the learner’s current context
* Use the learner’s mastery information when helpful

## Context

The chatbot shall have access to:

1. Source documents
2. Retrieved source chunks
3. Curriculum structure
4. Student mastery state
5. Current lesson context

The chatbot shall remain syllabus-focused.

---

# 21. Coding Learning Extension

For computer science subjects, the same lesson framework shall be extended with a coding environment.

Example:

```text
1.1 Topic
│
├── 1.1.1 Theory
├── 1.1.2 Theory
├── 1.1.3 Theory
│
└── 1.1.4 Coding Practice
        ↓
     Code Editor
        ↓
     Compiler
```

Coding shall occur only inside relevant lessons.

---

# 22. Coding Support

The initial compiler shall support:

* C
* C++
* Java
* Python

The compiler environment shall be isolated from the main application infrastructure.

---

# 23. Coding Assessment

The coding module shall support:

* Problem generation
* Topic-specific coding tasks
* Difficulty selection
* Code execution
* Test cases
* Output verification
* Basic code analysis
* Result feedback

The coding assessment shall be connected to the same Student Mastery Graph.

Example:

```text
Binary Search Coding
        ↓
Code submitted
        ↓
Tests executed
        ↓
Passed: 6/10
        ↓
Detected weakness:
Boundary conditions
        ↓
Mastery updated
        ↓
Learning Manager replans
```

---

# 24. AI Architecture

## 24.1 Primary LLM

The initial system shall use a **locally hosted LLM**, with Qwen as the initial candidate model.

The architecture shall avoid hard-coding the application to one model so that the model can later be replaced or moved to cloud inference.

## 24.2 LLM Responsibilities

The LLM shall handle:

* Content understanding
* Curriculum structuring
* Explanations
* Question generation
* Quiz evaluation
* Hint generation
* Weakness interpretation
* Learning-plan reasoning
* Chatbot responses
* Personalized recommendations
* Agentic decision making

## 24.3 Non-LLM Components

Deterministic services should handle tasks such as:

* OCR
* File processing
* Embeddings
* Vector retrieval
* Database operations
* Graph operations
* Quiz storage
* Code execution
* Test execution
* Authentication
* Application logic

The LLM shall orchestrate or reason over these components rather than replacing deterministic systems unnecessarily.

---

# 25. Agent Architecture

The system should be implemented as an agentic architecture.

## 25.1 Learning Manager Agent

The primary orchestrating agent.

Responsibilities:

* Observe learner state
* Understand curriculum state
* Select next action
* Invoke specialized capabilities
* Evaluate outcomes
* Replan

## 25.2 Specialized Agent Roles

Potential agents include:

### Syllabus Analyzer Agent

Converts uploaded material into structured curriculum.

### Teaching Agent

Generates explanations and learning content.

### Assessment Agent

Generates and manages quizzes.

### Evaluation Agent

Evaluates learner responses and identifies weaknesses.

### Coding Agent

Generates and analyzes coding exercises.

### Revision Agent

Creates revision material and review tasks.

The implementation may combine some roles initially, but responsibilities should remain modular.

---

# 26. Agentic Learning Loop

The defining behavior of Automatic Mode shall be:

```text
Student State
     ↓
Observe
     ↓
Analyze
     ↓
Determine Knowledge Gap
     ↓
Select Action
     ↓
Teach / Quiz / Code / Revise
     ↓
Evaluate Outcome
     ↓
Update Student Mastery Graph
     ↓
Replan
```

The system must be able to change its planned path based on new evidence.

---

# 27. Content Storage

The system shall use separate storage mechanisms for different types of information.

## 27.1 Document / Vector Storage

Used for:

* PDF content
* Page chunks
* Notes
* Retrieval context
* Source references

## 27.2 Graph Storage

Used for:

* Curriculum relationships
* Prerequisites
* Student mastery
* Learning history relationships

## 27.3 Relational / Application Storage

Used for:

* User accounts
* Subjects
* Quiz records
* Sessions
* System metadata
* Application state

---

# 28. Revision Summary

At the completion of a chapter or subject, the platform shall generate a comprehensive bullet-point revision file.

The summary may contain:

* All important concepts
* Important definitions
* Key facts
* Formulas
* Important relationships
* Common mistakes
* Weak topics
* Frequently missed concepts
* Important examples
* Key coding concepts where applicable

The revision summary should be generated from both source content and relevant learner history.

---

# 29. AI Harness / Evaluation Layer

The architecture should include an AI evaluation and observability layer.

The harness should be capable of evaluating:

* Agent decisions
* Generated questions
* Generated answers
* Explanations
* Hint quality
* Difficulty appropriateness
* Source grounding
* Agent execution traces
* Regression behavior

For generated assessments, the harness should be able to validate whether:

```text
Question is valid
AND
Correct answer is valid
AND
Explanation is consistent
AND
Difficulty is reasonable
```

Invalid outputs should be rejected or regenerated.

---

# 30. Safety and Reliability Requirements

The platform shall:

1. Avoid fabricating unreadable source content.
2. Request re-upload when document understanding confidence is insufficient.
3. Clearly identify external information.
4. Keep code execution isolated.
5. Validate generated assessment questions before presenting them.
6. Keep learner data isolated between users.
7. Never expose another learner’s mastery data.
8. Avoid treating generated content as verified merely because it came from the LLM.

---

# 31. Initial MVP

The first MVP should include:

### Input

* PDF upload
* Multiple PDFs
* Digital PDF processing
* Basic handwritten PDF support
* OCR

### Curriculum

* Subject
* Chapter
* Topic
* Subtopic extraction

### Learning

* Page-wise bullet notes
* Topic learning
* Topic quizzes
* Chapter final quiz

### Personalization

* Student Mastery Graph
* Weak-topic detection
* Easy/Medium/Hard difficulty
* Repeated attempts

### AI

* Local Qwen-based LLM
* Syllabus-grounded chatbot
* Six-level hints
* Answer evaluation
* Personalized next-step recommendations

### Modes

* Guided
* Manual
* Automatic

### CS Extension

* C
* C++
* Java
* Python
* Lesson-specific compiler

### Output

* Chapter/subject revision summary

---

# 32. Future Enhancements

Potential future features include:

* More question formats
* Voice-based learning
* Multimodal interactive lessons
* Teacher dashboards
* Classroom management
* Collaborative learning
* Mobile application
* Cloud deployment
* Multiple LLM providers
* Advanced code analysis
* Spaced-repetition scheduling
* Performance analytics
* Institution-level deployment

---

# 33. Success Metrics

The platform should measure:

### Learning Metrics

* Quiz improvement between attempts
* Topic mastery improvement
* Chapter completion rate
* Weak-topic recovery rate
* Retention/revision performance

### AI Quality Metrics

* Assessment validity
* Answer evaluation accuracy
* Source-grounding accuracy
* Hint usefulness
* Appropriate difficulty selection
* Agent planning success rate

### System Metrics

* PDF processing success rate
* OCR confidence
* Average response latency
* Compiler execution success rate
* Agent failure/recovery rate

---

# 34. Definition of Done

The MVP will be considered successful when a learner can:

1. Upload one or more educational PDFs.
2. Process both digital and handwritten content.
3. Receive a structured subject → chapter → topic → subtopic hierarchy.
4. Read page-wise bullet-point notes.
5. Study a topic.
6. Take topic and chapter quizzes.
7. Receive evaluated answers with explanations.
8. Use six progressive hints.
9. Ask syllabus-related questions in the persistent chatbot.
10. Have weaknesses recorded in persistent student memory.
11. Retake assessments.
12. Receive dynamically adjusted difficulty and weighting.
13. Use Guided, Manual, or Automatic learning mode.
14. Have Automatic Mode autonomously teach, assess, analyze, remediate, reassess, and replan.
15. Generate a complete revision summary.
16. For computer science lessons, write and execute C, C++, Java, or Python code inside the relevant lesson.
17. Resume learning later with previous mastery state intact.

---

# 35. Product Principle

The central product principle is:

> **The platform should not merely provide answers. It should continuously determine what the learner needs to understand next and guide them toward mastery.**
