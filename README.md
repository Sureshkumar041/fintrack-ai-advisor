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

## 6. How the Backend Starts

Project runs the backend through **Docker Compose.**

Command to start application:
```bash
docker compose up --build
```

The important flow is:
```text
docker compose
      ↓
Build Backend Image
      ↓
Start API Container
      ↓
Start Python Application
      ↓
main.py
      ↓
FastAPI App
      ↓
Register Routers
      ↓
Backend Ready
```

### 1. Docker starts the API container

From your `docker-compose.yml`:
```text
api:
  build:
    context: ./backend
```

Docker uses:
```text
backend/Dockerfile
```
to build the Python backend image.

The Dockerfile sets up:
```text
Python 3.12
Poetry
Dependencies
Application files
Non-root user
```

### 2. Container starts Python

Your Dockerfile has a startup command/entrypoint that eventually starts the FastAPI application.

- Conceptually:
```text
Docker Container
      ↓
Python
      ↓
FastAPI / Uvicorn
      ↓
app.main
```

**Uvicorn** = ASGI server used to run FastAPI.

### 3. `main.py` creates the FastAPI application

Project `backend/app/main.py` is the application entry point.

Conceptually:
```python
app = FastAPI(...)
```

So:
```text
main.py
   ↓
FastAPI application object
```

### 4. Router gets registered

`main.py`connects the API router.

```text
main.py
   ↓
api/v1/router.py
```

### 5. Dependency Injection Container

The application also has the DI container:

> core/container.py

It prepares dependencies such as:
```text
Settings
   ↓
Database Engine
   ↓
Session Factory

Redis Client
Qdrant Client
Services
Repositories
```

For Auth:
```text
container
   ↓
build_auth_service()
   ↓
AuthService
   ├── UserRepository
   ├── PasswordHasher
   ├── TokenService
   ├── AccessTokenBlocklist
   └── RefreshSessionStore
```

### 6. Backend becomes ready

Once startup completes:
```text
FastAPI
   ↓
Uvicorn
   ↓
Listening on port 8000
```

Your Docker Compose mapping is:
```text
localhost:8000
       ↓
container:8000
```

#### Complete Startup Flow ⭐

```text
docker compose up --build
          ↓
Build backend Docker image
          ↓
Start API container
          ↓
Python + Uvicorn
          ↓
app/main.py
          ↓
Create FastAPI app
          ↓
Register API router
          ↓
api/v1/router.py
          ↓
Feature routes available
          ↓
DI Container wires dependencies
          ↓
Backend listens on :8000
          ↓
        READY
```

### Important distinction

Startup flow:
```text
Docker → main.py → router → DI → server ready
```

Request flow:
```text
Frontend → API route → Service → Infrastructure → DB/AI/etc.
```

Important:-
```text
main.py      → starts/creates application
router.py    → connects API routes
container.py → wires dependencies
Uvicorn      → runs the FastAPI application
Docker       → provides the runtime environment
```

## 7. API / Request Flow

### 1. Basic flow

```text
Frontend
   ↓
HTTP Request
   ↓
API Route
   ↓
Application Service
   ↓
Domain / Ports
   ↓
Infrastructure
   ↓
DB / Redis / Qdrant / External Service
   ↓
Infrastructure
   ↓
Service
   ↓
API Route
   ↓
HTTP Response
   ↓
Frontend
```

### 2. Example: Login

### 3. API calls Service

### 4. Service calls a Port

### 5. Infrastructure implementation runs

### 6. Database returns data

### 7. Service continues processing

### 8. API prepares response

- Sets authentication cookies
- Converts the user into `UserResponse`
- Returns HTTP response

### 9. Frontend receives response

Finally:
```text
PostgreSQL
    ↓
Repository
    ↓
Service
    ↓
API
    ↓
HTTP Response
    ↓
Next.js
    ↓
User
```

#### Complete Login Request Flow

```text
                FRONTEND
                   │
                   │ POST /auth/login
                   ↓
          ┌──────────────────┐
          │ api/v1/auth.py   │
          │ API Route        │
          └────────┬─────────┘
                   ↓
          ┌──────────────────┐
          │   AuthService    │
          │ Application      │
          └────────┬─────────┘
                   ↓
          ┌──────────────────┐
          │ UserRepository   │
          │ Domain Port      │
          └────────┬─────────┘
                   ↓
       ┌─────────────────────────┐
       │ SqlAlchemyUserRepository│
       │ Infrastructure          │
       └────────────┬────────────┘
                    ↓
                SQLAlchemy
                    ↓
               PostgreSQL
                    │
                    ↓
              UserRecord
                    ↓
               UserAccount
                    ↓
              AuthService
                    ↓
          Token + AuthenticatedUser
                    ↓
              API Response
                    ↓
                Frontend
```

### 10. What about an AI request?

This is where your project becomes more interesting.

Instead of:
```text
Service → Repository → PostgreSQL
```

an AI request may look more like:
```text
Frontend
   ↓
API Route
   ↓
Application Service
   ↓
LangGraph
   ↓
Agent
   ├── RAG → Qdrant
   ├── Prompt
   └── LLM
   ↓
Reflection / Audit
   ↓
Final result
   ↓
API
   ↓
Frontend
```
So **not every API request goes to PostgreSQL.**

Depending on the feature, it may interact with:

- PostgreSQL
- Redis
- Qdrant
- LLM
- AI agents
- External services
- Or several of them.

## 8. LangGraph

> LangGraph controls the sequence of AI steps and keeps the information/state between those steps.

Instead of asking an LLM one question:
```text
Input → LLM → Output
```

Project needs multiple steps:
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
Final Tests
```
LangGraph helps manage this workflow.

### 1. Why do we need LangGraph?
Imagine we `don't use LangGraph.`

We might manually write:
```text
result1 = analyze(requirement)

result2 = retrieve_context(result1)

result3 = generate_tests(result2)

result4 = review(result3)

result5 = improve(result4)
```
As the workflow becomes bigger, this becomes difficult to manage.

### 2. LangGraph uses a Graph

Think of a graph like a flowchart:
```text
        START
          ↓
      Analyze
          ↓
    Retrieve Context
          ↓
    Generate Tests
          ↓
       Review
       ↙    ↘
   Good      Bad
     ↓        ↓
    END    Improve
              ↓
            Review
```
This is basically what a LangGraph workflow represents.

### 3. Node

> Node = a task/step.

A Node is one step/function in the workflow.

For example:
```text
Analyze Requirement
Retrieve Context
Generate Test Cases
Review Test Cases
```

Each can be a node.
```text
Graph
 ├── Analyze Node
 ├── Retrieval Node
 ├── Generation Node
 └── Review Node
```

### 4. Edge

An Edge defines where the workflow goes next.

- The arrow is an edge.

```text
Node A ─────→ Node B
        Edge
```

> connection/path between steps.

### 5. Conditional Edge

This is where LangGraph becomes powerful.

```text
              Review
             /      \
          Good      Bad
           ↓         ↓
          END      Improve
                     ↓
                   Review
```

### 6. State

> State = information currently being carried through the workflow.

### 7. Simple example

> "Users should reset their password using email."

Initial state:
```text
{
    requirement: "Password reset using email"
}
```
**Analyze node**

Adds:
```text
{
    requirement: "...",
    analysis: "Password reset functionality"
}
```

**Retrieval node**

Adds:
```text
{
    requirement: "...",
    analysis: "...",
    retrieved_context: [
        "Previous password reset tests",
        "Security requirements"
    ]
}
```

**Generation node**

Adds:
```text
{
    requirement: "...",
    analysis: "...",
    retrieved_context: "...",
    generated_tests: [...]
}
```

**Review node**

Adds:
```text
{
    ...
    review_result: "Missing expired-token scenario"
}
```

**Improve node**

Updates:
```text
generated_tests
```
And the workflow eventually finishes.

### 8. LangGraph in your project

Now connect this to your AI Testing Agent.

```text
                    LangGraph
                       │
                 Shared State
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
   Requirement      Retrieval      Generation
     Analysis          │              │
                       ↓              ↓
                     RAG            LLM
                                      │
                                      ↓
                                   Review
                                      │
                                ┌─────┴─────┐
                                ↓           ↓
                              Good        Improve
                                ↓           │
                               END ←────────┘
```
This is the AI workflow orchestration we discussed earlier.

### 9. LangGraph vs LLM

#### LLM

> LLM = intelligence/generation

```text
Input
 ↓
LLM
 ↓
Text/AI output
```

#### LangGraph

> LangGraph = workflow orchestration

```text
Step 1
 ↓
Step 2
 ↓
Step 3
 ↓
Condition
 ↓
Step 4
```

>> LLM thinks/generates. LangGraph controls the workflow.

### The 4 terms you must remember

| Term      | Simple meaning                              |
| --------- | ------------------------------------------- |
| **Graph** | Complete workflow                           |
| **Node**  | One step/task                               |
| **Edge**  | Connection between steps                    |
| **State** | Shared information carried through workflow |

And:
```text
LangGraph
   ↓
Graph
   ├── Nodes
   ├── Edges
   └── State
```

Where we are now
```text
Normal Backend
Frontend
   ↓
FastAPI
   ↓
Service
   ↓
Infrastructure


AI Backend
Frontend
   ↓
FastAPI
   ↓
LangGraph
   ↓
Agents
   ↓
RAG + LLM
   ↓
Reflection
```

## 9. Agent + State 🤖

### 1. What is an Agent?

An `Agent` is a specialized AI worker that performs a particular task.

In this project, agents are used to perform tasks such as:
```text
Requirement Analysis
       ↓
Context Retrieval
       ↓
Test Case Generation
       ↓
Reflection / Review
       ↓
Final Output
```

Think:

> Agent = worker that performs one AI task

For example:

***Test Case Generation Agent***

Input:
```text
Requirement:
User can reset password using registered email.
```

Agent thinks using the LLM + available context and produces:
```text
1. Verify reset works with valid email
2. Verify unregistered email is rejected
3. Verify expired reset link
4. Verify already-used reset link
5. Verify invalid email format
```

### 2. What is State?

***State = shared information carried through the LangGraph workflow.***

Imagine the workflow starts with: `Requirement`

Then each node adds information:
```text
START
  ↓
State
{
  requirement
}
  ↓
Analysis Agent
{
  requirement
  analysis
}
  ↓
RAG
{
  requirement
  analysis
  retrieved_context
}
  ↓
Test Agent
{
  requirement
  analysis
  retrieved_context
  generated_tests
}
  ↓
Reflection
{
  requirement
  analysis
  retrieved_context
  generated_tests
  review_result
}
```

So **State is like a shared notebook** used by all steps.

### 3. Agent vs State

This distinction is very important:
| Concept       | Meaning                  |
| ------------- | ------------------------ |
| **Agent**     | Does the work            |
| **State**     | Stores the information   |
| **LLM**       | Provides AI intelligence |
| **LangGraph** | Controls the workflow    |

Simple analogy:
```text
LangGraph  → Manager
Agent      → Employee
LLM        → Employee's intelligence
State      → Shared notebook
```

### 4. How Agents use State

Suppose:
```text
State
├── requirement
├── retrieved_context
├── generated_tests
└── review_result
```

A generation agent might:
```text
Read:
  requirement
  retrieved_context

Do:
  LLM-based test generation

Write:
  generated_tests
```

Then Reflection reads:
```text
generated_tests
```

and writes:
```text
review_result
```

Then another node can decide:
```text
Review result
   ↓
Good? ── Yes → END
   │
   No
   ↓
Improve tests
```

### 5. Why State is important in this project

Without state:
```text
Agent 1 → output
Agent 2 → doesn't know Agent 1's output
Agent 3 → doesn't know previous work
```

With state:
```text
Agent 1
  ↓
updates State
  ↓
Agent 2 reads State
  ↓
updates State
  ↓
Agent 3 reads State
```
This is what makes the workflow multi-step and stateful.

> Stateful = the workflow remembers information from previous steps.

### 6. In project

### The key picture to remember

```text
                 ┌──────────────┐
                 │    STATE     │
                 │ requirement  │
                 │ context      │
                 │ tests        │
                 │ review       │
                 └──────┬───────┘
                        │
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
      Agent 1        Agent 2       Agent 3
      Analyze        Generate      Review
          │             │             │
          └─────────────┴─────────────┘
                        ↓
                 Updated State
```

## 10. Project/ai_testing_agent

### 1. There are actually 2 levels of State

Project has:

#### A. `PipelineState` — **master workflow state**

File:

```text
backend/app/infrastructure/orchestration/graph.py
```

This controls the overall AI pipeline:
```text
Requirement
   ↓
Retrieve Context
   ↓
Build Prompt
   ↓
Generate Tests
   ↓
Decision
   ↓
Checkpoint
```

Its state contains things like:
```text
requirement
profile
context
final_prompt
generation_result
decision
checkpoint
```

So:
> PipelineState = state for the complete AI pipeline.

#### B. GenerationState — Test Generation Agent state

File:

```text
backend/app/infrastructure/test_generation_agent/graph.py
```

This is the one we should focus on for **Agent + State.**

It contains:
```text
class GenerationState(TypedDict):
    requirement
    context
    profile
    overrides

    unit_result
    unit_error

    api_result
    api_error

    bdd_result
    bdd_error

    e2e_result
    e2e_error

    edge_case_result
    edge_case_error
```

Think of it as:
```text
                 GenerationState
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      Unit            API            BDD
      Agent           Agent          Agent
        ↓              ↓              ↓
     result         result         result

        ┌──────────────┴──────────────┐
        ↓                             ↓
      E2E                       Edge Case
      Agent                         Agent
```

### 2. What are the "Agents" here?

Project has 5 test generators.

```text
backend/app/infrastructure/test_generation_agent/bootstrap.py
```

*****Specifically:*****
```text
UnitTestCaseGenerator
ApiTestCaseGenerator
BddScenarioGenerator
E2eTestCaseGenerator
EdgeCaseTestGenerator
```

> Each generator acts as a specialized test-generation agent.

Their files are:
```text
infrastructure/test_generation_agent/generators/

├── unit_generator.py
├── api_generator.py
├── bdd_generator.py
├── e2e_generator.py
└── edge_case_generator.py
```

### 3. What does each Agent receive?

All five agents receive the same common input:
```text
Requirement
Context
Testing Profile
Generation Overrides
```

From the graph:
```python
generator.generate(
    state["requirement"],
    state["context"],
    state["profile"],
    state["overrides"]
)
```

So:
```text
                 GenerationState
                       │
        ┌──────────────┼──────────────┐
        │              │              │
   requirement      context        profile
        │              │              │
        └──────────────┼──────────────┘
                       ↓
             Each Generator Agent
```

### 4. What does an Agent return?

For example, Unit Agent:
```text
requirement
   +
context
   +
profile
   ↓
UnitTestCaseGenerator
   ↓
LLM
   ↓
GeneratorResult
```

The result is stored in:
```text
unit_result
```

API generator:
```text
ApiTestCaseGenerator
      ↓
api_result
```

BDD: ***Behavior-Driven Development***
```text
BddScenarioGenerator
      ↓
bdd_result
```

### 5. The important part: Parallel execution

```text
                       START
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
        Unit            API            BDD
          │              │              │
          ↓              ↓              ↓
        Result         Result         Result

          ↓              ↓              ↓
        E2E          Edge Case
          │              │
          ↓              ↓
        Result         Result
                         │
                        END
```

- five independent generators can execute in parallel.

Why?

Because they don't depend on each other's results.

- Unit doesn't need API result.

- API doesn't need BDD result.

- BDD doesn't need E2E result.

That's why the code comment says:

> "the five generators can run in any order or in parallel."

### 6. How does State actually move?

`TestGenerationService.generate()` starts the graph with:

```text
GenerationState
{
    requirement,
    context,
    profile,
    overrides,

    unit_result = None,
    api_result = None,
    bdd_result = None,
    e2e_result = None,
    edge_case_result = None
}
```

Then **LangGraph sends this state** to the generator nodes.

For example:
```text
                 State
                   │
                   ├────────→ Unit Agent
                   │             │
                   │             ↓
                   │        unit_result
                   │
                   ├────────→ API Agent
                   │             │
                   │             ↓
                   │         api_result
                   │
                   ├────────→ BDD Agent
                   │             │
                   │             ↓
                   │         bdd_result
                   │
                   ├────────→ E2E Agent
                   │
                   └────────→ Edge Agent
```
Each node returns only its own update.

For example:
```python
return {result_key: result}
```

For Unit:
```python
{
    "unit_result": result
}
```

For API:
```python
{
    "api_result": result
}
```

LangGraph `combines those updates into the final state`.

### 7. What happens if one Agent fails?

This project deliberately isolates failures.

Inside `_make_node()`:

```python
try:
    result = generator.generate(...)
    return {result_key: result}

except Exception as error:
    return {error_key: str(error)}
```

Example:
```text
Unit       → ✅
API        → ✅
BDD        → ❌
E2E        → ✅
Edge Case  → ✅
```

State becomes:
```text
unit_result       → result
api_result        → result
bdd_result        → None
bdd_error         → error message
e2e_result        → result
edge_case_result  → result
```

So one failed generator **doesn't kill the other generators**.

That's a useful design choice.

### 8. Where does the actual AI/LLM happen?

The LangGraph node itself isn't the LLM.

The node calls:
```python
generator.generate(...)
```
> The node asks the generator to generate something; the concrete generator is responsible for translating that request into a call to the structured completion client, which actually communicates with the LLM and returns the structured result.

The wiring happens in:
```text
bootstrap.py
```

Conceptually:
```text
LangGraph
    ↓
UnitTestCaseGenerator
    ↓
StructuredCompletionClient
    ↓
Anthropic
    ↓
LLM
    ↓
Generated test cases
```

So remember:
```text
LangGraph → controls workflow
Agent     → performs specialized task
LLM       → provides AI generation
State     → carries information/results
```

### 9. One important project-specific correction

Earlier we used an example like:
```text
Analyze
 ↓
RAG
 ↓
Generate
 ↓
Reflection
```

Also, the code explicitly says the current master pipeline has:
> No reflection/retry loop yet.

#### Final mental model

```text
                    LangGraph
                       │
              PipelineState
                       │
       Requirement → Retrieve → Prompt
                       │
                       ↓
                Test Generation
                       │
                GenerationState
                       │
       ┌───────────────┼───────────────┐
       ↓               ↓               ↓
     Unit              API             BDD
     Agent             Agent           Agent
       │               │               │
       └───────────────┼───────────────┘
                       ↓
                    E2E / Edge
                       │
                       ↓
                TestGenerationResult
                       │
                       ↓
                    Decision
                       │
                       ↓
                  Checkpoint
```

#### In one sentence:

> State carries the data; Agents perform specialized work; LangGraph controls how that work is executed.

## 11. RAG — Retrieval-Augmented Generation

### 1. First: What problem does RAG solve?

An LLM by itself knows general information:

```text
Requirement
    ↓
   LLM
    ↓
Generic test cases
```
But your project needs project-specific context.

For example:
> "Generate tests for our payment API."

The LLM should know things like:

- Existing tests
- Past bugs
- Business rules
- Architecture
- Requirements
- Swagger/API definitions
- SQL/schema information

That's where RAG comes in.
> RAG = retrieve relevant project knowledge first, then give that knowledge to the LLM.

### 2. Project's RAG flow

 actual implementation is:
 ```text
 Documents
   ↓
Load
   ↓
Chunk
   ↓
Embedding
   ↓
Index
   ↓
         ← Query
           ↓
      Retrieve
           ↓
       Rerank
           ↓
    Build Context
           ↓
     ContextPackage
           ↓
    Test Generation
           ↓
          LLM
 ```

 There are two different phases:

 #### Ingestion
 Preparing knowledge:
 ```text
 Document
 ↓
Loader
 ↓
Chunks
 ↓
Embeddings
 ↓
Index
 ```

#### Query / Retrieval

Finding knowledge when needed:
```text
Requirement
 ↓
Embedding
 ↓
Retrieve similar chunks
 ↓
Rerank
 ↓
ContextPackage
 ↓
LLM
```

### 3. Step 1 — Document Loader

Actual file:
```bash
infrastructure/rag_framework/loaders/
```

There are loaders for:
```text
Plain text
Swagger
SQL
```