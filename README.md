1. AI Testing Agent — Project Overview
   
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