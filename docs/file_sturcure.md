autonomous-learning-platform/
│
├── README.md
├── PRD.md
├── TECH-STACK.md
├── docker-compose.yml
├── .env.example
├── .gitignore
│
├── apps/
│   │
│   ├── frontend/
│   │   ├── package.json
│   │   ├── next.config.ts
│   │   ├── tsconfig.json
│   │   ├── public/
│   │   └── src/
│   │       ├── app/
│   │       │   ├── login/
│   │       │   ├── dashboard/
│   │       │   ├── subjects/
│   │       │   ├── subjects/[subjectId]/
│   │       │   ├── lessons/[lessonId]/
│   │       │   ├── quizzes/[quizId]/
│   │       │   ├── coding/[lessonId]/
│   │       │   ├── chat/
│   │       │   ├── progress/
│   │       │   └── revision/
│   │       │
│   │       ├── components/
│   │       │   ├── ui/
│   │       │   ├── dashboard/
│   │       │   ├── curriculum/
│   │       │   ├── lesson/
│   │       │   ├── quiz/
│   │       │   ├── coding/
│   │       │   ├── chatbot/
│   │       │   └── progress/
│   │       │
│   │       ├── hooks/
│   │       ├── lib/
│   │       ├── services/
│   │       ├── stores/
│   │       ├── types/
│   │       └── styles/
│   │
│   └── backend/
│       ├── pyproject.toml
│       ├── requirements.txt
│       │
│       ├── app/
│       │   ├── main.py
│       │   │
│       │   ├── api/
│       │   │   ├── auth.py
│       │   │   ├── subjects.py
│       │   │   ├── documents.py
│       │   │   ├── curriculum.py
│       │   │   ├── lessons.py
│       │   │   ├── quizzes.py
│       │   │   ├── attempts.py
│       │   │   ├── mastery.py
│       │   │   ├── learning.py
│       │   │   ├── chat.py
│       │   │   ├── coding.py
│       │   │   └── revision.py
│       │   │
│       │   ├── agents/
│       │   │   ├── learning_manager/
│       │   │   │   ├── agent.py
│       │   │   │   ├── state.py
│       │   │   │   ├── planner.py
│       │   │   │   └── prompts.py
│       │   │   │
│       │   │   ├── syllabus_analyzer/
│       │   │   ├── teaching/
│       │   │   ├── assessment/
│       │   │   ├── evaluation/
│       │   │   ├── coding/
│       │   │   ├── revision/
│       │   │   └── chatbot/
│       │   │
│       │   ├── workflows/
│       │   │   ├── guided.py
│       │   │   ├── manual.py
│       │   │   └── automatic.py
│       │   │
│       │   ├── document/
│       │   │   ├── parser.py
│       │   │   ├── pdf.py
│       │   │   ├── ppt.py
│       │   │   ├── pages.py
│       │   │   └── confidence.py
│       │   │
│       │   ├── ocr/
│       │   │   ├── paddle.py
│       │   │   ├── preprocessing.py
│       │   │   └── confidence.py
│       │   │
│       │   ├── vision/
│       │   │   ├── processor.py
│       │   │   └── handwriting.py
│       │   │
│       │   ├── llm/
│       │   │   ├── base.py
│       │   │   ├── qwen.py
│       │   │   ├── prompts/
│       │   │   └── structured_output.py
│       │   │
│       │   ├── rag/
│       │   │   ├── chunking.py
│       │   │   ├── embeddings.py
│       │   │   ├── retriever.py
│       │   │   └── qdrant.py
│       │   │
│       │   ├── graphs/
│       │   │   ├── curriculum_graph.py
│       │   │   ├── mastery_graph.py
│       │   │   ├── relationships.py
│       │   │   └── neo4j.py
│       │   │
│       │   ├── mastery/
│       │   │   ├── calculator.py
│       │   │   ├── updater.py
│       │   │   └── weakness.py
│       │   │
│       │   ├── quiz/
│       │   │   ├── generator.py
│       │   │   ├── validator.py
│       │   │   ├── scorer.py
│       │   │   ├── difficulty.py
│       │   │   └── hints.py
│       │   │
│       │   ├── coding/
│       │   │   ├── problem_generator.py
│       │   │   ├── executor.py
│       │   │   ├── sandbox.py
│       │   │   ├── test_runner.py
│       │   │   └── languages/
│       │   │       ├── c.py
│       │   │       ├── cpp.py
│       │   │       ├── java.py
│       │   │       └── python.py
│       │   │
│       │   ├── revision/
│       │   │   └── generator.py
│       │   │
│       │   ├── models/
│       │   │   ├── user.py
│       │   │   ├── subject.py
│       │   │   ├── document.py
│       │   │   ├── curriculum.py
│       │   │   ├── lesson.py
│       │   │   ├── quiz.py
│       │   │   ├── attempt.py
│       │   │   └── mastery.py
│       │   │
│       │   ├── schemas/
│       │   │   ├── auth.py
│       │   │   ├── document.py
│       │   │   ├── curriculum.py
│       │   │   ├── quiz.py
│       │   │   ├── mastery.py
│       │   │   ├── chat.py
│       │   │   └── coding.py
│       │   │
│       │   ├── db/
│       │   │   ├── postgres.py
│       │   │   ├── redis.py
│       │   │   └── migrations/
│       │   │
│       │   ├── services/
│       │   │   ├── subject_service.py
│       │   │   ├── lesson_service.py
│       │   │   ├── quiz_service.py
│       │   │   ├── mastery_service.py
│       │   │   └── chat_service.py
│       │   │
│       │   ├── workers/
│       │   │   ├── celery_app.py
│       │   │   ├── document_tasks.py
│       │   │   ├── quiz_tasks.py
│       │   │   └── revision_tasks.py
│       │   │
│       │   ├── storage/
│       │   │   ├── local.py
│       │   │   └── interface.py
│       │   │
│       │   ├── evaluation/
│       │   │   ├── harness.py
│       │   │   ├── datasets/
│       │   │   ├── graders/
│       │   │   └── regression/
│       │   │
│       │   ├── core/
│       │   │   ├── config.py
│       │   │   ├── logging.py
│       │   │   ├── security.py
│       │   │   └── exceptions.py
│       │   │
│       │   └── utils/
│       │
│       └── tests/
│           ├── unit/
│           ├── integration/
│           ├── agents/
│           ├── rag/
│           ├── mastery/
│           ├── quiz/
│           └── coding/
│
├── infrastructure/
│   ├── docker/
│   │   ├── frontend.Dockerfile
│   │   ├── backend.Dockerfile
│   │   └── sandbox.Dockerfile
│   │
│   ├── nginx/
│   │   └── nginx.conf
│   │
│   └── monitoring/
│       ├── prometheus.yml
│       └── grafana/
│
├── storage/
│   ├── documents/
│   ├── pages/
│   ├── generated/
│   └── revisions/
│
├── scripts/
│   ├── setup.sh
│   ├── seed.py
│   └── dev.sh
│
└── docs/
    ├── architecture/
    ├── api/
    ├── agents/
    ├── database/
    └── deployment/