## 1. AI Testing Agent — Project Overview
   
What is this project?

The project is an AI-powered software testing platform.

Its purpose is to use AI agents to help QA/developers automatically create and analyze software tests from project information such as:

Requirements
User stories
Acceptance criteria
Existing test cases
Application/UI information
Historical testing knowledge

Instead of manually creating every test case, the system uses AI to assist with the process.

2. What problem is it solving?

Normally, a tester may receive:

```text
Requirement
    ↓
Read requirement
    ↓
Understand functionality
    ↓
Think of scenarios
    ↓
Write test cases
    ↓
Write expected results
    ↓
Check edge cases
    ↓
Review test cases
```

This can take significant time and can lead to missed scenarios.

The AI Testing Agent tries to automate/support these activities:

```text
Requirement
    ↓
AI analyzes it
    ↓
Find relevant existing knowledge
    ↓
Generate test scenarios
    ↓
Generate expected results
    ↓
Validate / review
    ↓
Final test cases
```

3. What does the user actually do?

Think of a simple example.

A user provides:

> ***Requirement:***
> "Users should be able to reset their password using their registered email."

The system should be able to generate things like:
```text
Positive:
✓ Registered email → reset link sent

Negative:
✓ Unregistered email
✓ Invalid email format

Edge cases:
✓ Expired reset link
✓ Already-used reset link
✓ Multiple reset requests

Security:
✓ Don't expose whether email exists
✓ Reset token should expire
```
So the AI isn't simply generating random test cases.
- It should use **context + requirements + existing knowledge** to produce useful testing scenarios.

4. Main goal

The core goal is:

> Convert software requirements into high-quality, contextual test scenarios/test cases using AI.

Important word:

**Contextual** = based on the specific situation/project, rather than generic output.

For example:

❌ Generic:

> "Test the login page."

✅ Contextual:

> "Verify that an inactive user cnnot log in even when valid credentials are provided."

5. Major parts of this project
```text
                    AI Testing Platform
                           │
          ┌────────────────┴────────────────┐
          ↓                                 ↓
     Frontend                         Backend
     Next.js                          FastAPI
          │                                 │
          └──────────────┬──────────────────┘
                         ↓
                    AI Workflow
                         │
                    LangGraph
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
        Agents          RAG            LLM
          │              │              │
          └──────────────┼──────────────┘
                         ↓
                  Reflection/Audit
                         ↓
                  Final Test Cases
```

And supporting infrastructure:
```text
PostgreSQL → application data
Redis      → sessions / token blocklist / temporary state
Qdrant     → vector/knowledge search
Docker     → run services
```

6. What are Agents?
An **agent** is an AI-driven component responsible for a particular task.

For example:
```text
Requirement
     ↓
Test Case Generation Agent
     ↓
Generated scenarios
```

Another agent might analyze UI:
```text
Figma / UI
    ↓
UI/UX Analysis Agent
    ↓
UI test scenarios
```

From the requirements we've discussed, examples include:

- Test Case Generation Agent
- UI/UX Analysis
- Historical Context Retrieval
- Multi-Layered Expected Results
- Observability
- Reuse Azure
- Later: Traceability Agent

We will understand each one separately.

7. What is LangGraph?

Don't worry about the details yet.

For now:
> LangGraph is used to control the workflow between AI agents and steps.

For example:
```text
Requirement
    ↓
Analyze
    ↓
Retrieve Context
    ↓
Generate Tests
    ↓
Review
    ↓
Improve
    ↓
Final Output
```
LangGraph helps manage this process and the state between steps.


8. What is RAG?

***RAG = Retrieval-Augmented Generation***

Simple meaning:
> Before asking the LLM to generate an answer, the system retrieves relevant information from its own knowledge/data.

Example:
```text
New Requirement
      ↓
Search existing test cases
      ↓
Find similar historical tests
      ↓
Give that context to LLM
      ↓
Generate better test cases
```

#### The complete picture

At a high level, your project is:
```text
                    USER
                      │
                      ↓
              Next.js Frontend
                      │
                      ↓
               FastAPI Backend
                      │
                      ↓
                AI Workflow
                  LangGraph
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
       Agents        RAG         LLM
          │           │           │
          └───────────┼───────────┘
                      ↓
                Reflection
                      ↓
                Audit/Validation
                      ↓
             Final Test Cases
                      │
                      ↓
                 Frontend
                      │
                      ↓
                    USER
```

## 2. Project Requirements

#### Overall requirement map
```text
AI Testing Agent
│
├── P1 — Core
│   ├── Test Case Generation
│   │   └── Contextual Test Scenarios
│   ├── UI/UX Analysis
│   └── Historical Context Retrieval
│
├── P2 — Supporting
│   ├── Multi-Layered Expected Results
│   ├── Observability
│   └── Azure Reuse
│
└── P3 — Additional
    └── Traceability Agent
```

They work together:
```text
                Requirement
                     ↓
        Historical Context Retrieval
                     ↓
              AI/Agents
                     ↓
          Test Case Generation
                     ↓
       Multi-Layer Expected Results
                     ↓
          Reflection / Validation
                     ↓
             Final Test Cases
                     ↓
              Traceability
```
`And UI/UX Analysis can provide another source of testing context.`

## 3. User Flow

> The steps a user follows while using the application.

```text
User
 ↓
Login
 ↓
Dashboard
 ↓
Provide testing input
 ↓
AI processes the input
 ↓
Test scenarios generated
 ↓
Review results
 ↓
Use / export final test cases
```

Types:

1. User opens the application
2. Login
3. User enters testing requirements
4. User starts test generation
5. Backend starts the AI workflow
6. Retrieve relevant context
7. AI generates test scenarios
8. Reflection / Validation
9. Backend returns the result
10. Frontend displays the result


Complete User Flow ⭐
```text
                         USER
                           │
                           ↓
                    Next.js Frontend
                           │
                           ↓
                       Login
                           │
                           ↓
                    FastAPI Backend
                           │
                           ↓
                    Authentication
                           │
                           ↓
                  Testing Requirement
                           │
                           ↓
                    FastAPI API
                           │
                           ↓
                      LangGraph
                           │
             ┌─────────────┴─────────────┐
             ↓                           ↓
      Context Retrieval             AI Agents
             ↓                           ↓
          Qdrant                      LLM
             │                           │
             └─────────────┬─────────────┘
                           ↓
                    Generate Tests
                           ↓
                    Reflection/Audit
                           ↓
                     Final Results
                           │
                           ↓
                    FastAPI Response
                           │
                           ↓
                    Next.js Frontend
                           │
                           ↓
                          USER
```

### One important distinction

There are **two different flows** happening in the application:

#### Authentication flow
`User → API → AuthService → PostgreSQL/Redis → API → User`

#### AI testing flow
`User → API → LangGraph → Agents → RAG + LLM → Reflection → API → User`

## 4. System Architecture

> Architecture = how the major parts of the system are organized and communicate with each other.

#### Five major layers/components:
```text
User
  ↓
Frontend
  ↓
Backend
  ↓
AI Processing
  ↓
Data / External Services
```

1. Frontend — Next.js
2. Backend — FastAPI
3. Database Layer
4. Redis
`Redis is a fast in-memory data store.`

5. Qdrant — Vector Database
`Qdrant is used for vector search.`

6. LangGraph
- This is the AI workflow orchestration layer.

**Orchestration** = coordinating multiple steps/components.

```text
              LangGraph
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
    Agent 1    Agent 2    Agent 3
       │          │          │
       └──────────┼──────────┘
                  ↓
              Final result
```

7. AI Agents
> Agents perform specific AI-related tasks.

For example:
```text
              AI Workflow
                   │
       ┌───────────┼────────────┐
       ↓           ↓            ↓
Test Generation  UI/UX     Context Retrieval
    Agent         Agent          Agent
```

8. RAG
> RAG connects your knowledge/data with the LLM.

```text
User Requirement
       ↓
Retrieve relevant information
       ↓
Qdrant
       ↓
Relevant Context
       ↓
LLM
```

9. LLM
> The LLM is the actual language intelligence.

```text
Requirement
     +
Context
     +
Prompt
     ↓
    LLM
     ↓
Generated Output
```

10. Reflection / Audit

After generation:
```text
Generated Test Cases
        ↓
Reflection / Audit
        ↓
Check quality
        ↓
Improve / validate
        ↓
Final Test Cases
```

This helps 'prevent the system from simply accepting the first AI response'.

11. Docker
Docker is not part of the business logic.

> It provides the runtime environment for the different services.

 ##### Complete System Architecture ⭐

 ```text
                          USER
                           │
                           ↓
                 ┌─────────────────┐
                 │ Next.js Frontend│
                 └────────┬────────┘
                          │
                       HTTP/API
                          │
                          ↓
                 ┌─────────────────┐
                 │ FastAPI Backend │
                 └────────┬────────┘
                          │
              ┌───────────┴───────────┐
              ↓                       ↓
       Normal Application        AI Workflow
              │                       │
              ↓                    LangGraph
       Application Services            │
              │               ┌────────┼────────┐
              ↓               ↓        ↓        ↓
       Infrastructure       Agents    RAG      LLM
              │                         │
        ┌─────┼─────┐                   ↓
        ↓     ↓     ↓                Qdrant
       DB   Redis  APIs
        │
    PostgreSQL
 ```

 ## 5. Backend Structure

 Backend is basically organized like this:
 ```text
backend/
└── app/
    ├── main.py
    ├── api/
    ├── application/
    ├── domain/
    ├── infrastructure/
    └── core/
 ```

 > Each folder has one responsibility.

 ### 1. `main.py` — Application Entry Point

 ### 2. `api/` — HTTP/API Layer

Responsibilities:

- Receive request
- Validate request
- Call application/service layer
- Convert errors to HTTP responses
- Return response

### 3. `application/` — Business/Application Logic

handles:

- Login
- Logout
........

It doesn't directly care whether the database is PostgreSQL, MongoDB, etc.
- It depends on `interfaces/ports.`

```text
API
 ↓
Application Service
 ↓
Domain Ports
```

### 4. `domain/` — Core Business Definitions

Inside domain like:
```text
domain/
├── auth/
├── requirements/
├── projects/
├── ...
```

Inside each domain you'll find:
```text
models.py   - Defines business data structures.
ports.py    - Defines interfaces/contracts.
enums.py    - Defines domain/application-specific errors.
errors.py   - Defines fixed allowed values.
```

### 5. `infrastructure/` — External/Technical Implementation

For Auth:
```text
infrastructure/auth/
├── sqlalchemy_user_repository.py
├── orm_models.py
├── bcrypt_password_hasher.py
├── jwt_token_service.py
├── redis_access_token_blocklist.py
├── redis_refresh_session_store.py
└── bootstrap.py
```

This is where the **actual implementations** live.

### 6. `core/` — Application Infrastructure / Configuration

This contains things needed to run and configure the application.

From the files
```text
core/
├── config.py
├── container.py
├── logging.py
└── ...
```

#### config.py

```text
Database URL
Redis URL
Qdrant URL
JWT configuration
CORS
Environment
```

#### container.py

This is the ***Dependency Injection container.***
> It wires components together.

```text
db_session_factory
       ↓
SqlAlchemyUserRepository
       ↓
AuthService
```

### 7. api/v1/router.py

This is the central API router.

### 8. The Backend Request Flow ⭐

```text
Frontend
   ↓
main.py
   ↓
api/v1/router.py
   ↓
api/v1/<feature>.py
   ↓
application/<feature>/service.py
   ↓
domain/<feature>/ports.py
   ↓
infrastructure/<feature>/
   ↓
Database / Redis / Qdrant / External Service
```

### 9. Auth Example — Actual Project

```text
POST /auth/login
       ↓
api/v1/auth.py
       ↓
AuthService
       ↓
UserRepository
       ↓
SqlAlchemyUserRepository
       ↓
UserRecord
       ↓
SQLAlchemy
       ↓
PostgreSQL
```

And:

```text
container.py
       ↓
bootstrap.py
       ↓
wires AuthService dependencies
```

### Simple Meaning of Each Folder

| Folder            | Simple meaning                     |
| ----------------- | ---------------------------------- |
| `main.py`         | Backend starts here                |
| `api/`            | Handles HTTP requests/responses    |
| `application/`    | Application/business workflow      |
| `domain/`         | Business rules, models, contracts  |
| `infrastructure/` | Actual DB/external implementations |
| `core/`           | Configuration + dependency wiring  |


#### One-line memory trick

```text
API → receives
Application → processes
Domain → defines
Infrastructure → connects
Core → configures/wires
Main → starts
```