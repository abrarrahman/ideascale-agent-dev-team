# Multi-Agent Dev Team Plan

## Deterministic Ticket to Prompt
- Incoming tickets (QOL or BUG) from YouTrack are transformed into a structured prompt.
- This ensures all tickets are standardized before entering the system.
- The structured prompt becomes the **input for the Main Agent**.

---

## Main Agent
- **Responsibilities:**
    - Receives the structured prompt derived from the ticket.
    - Orchestrates the workflow across all sub-agents.
    - Breaks down the problem into steps, assigns tasks to relevant agents.
    - Gathers inputs and outputs from sub-agents, consolidates them.
    - Ensures iterative feedback and refinement until the solution is complete.
    - Provides final output: a plan, updated code branches, PRs, QA reports, and documentation updates.

- **Inputs:** Structured ticket prompt.
- **Outputs:** Finalized PRs, QA validation, updated documentation, and knowledge base contributions.

---

## Sub Agents
The Main Agent coordinates the following specialized sub-agents:

### Frontend Agent
- **Responsibilities:**
    - Implements UI-related changes for tickets.
    - Works with Information Agent to gather relevant codebase context.
    - Collaborates with Documentation Agent to map out new frontend knowledge and update existing records.

- **Interactions:**
    - With Information Agent for retrieving frontend-specific knowledge.
    - With Documentation Agent to ensure new learnings are stored in memory.
    - With Git Agent for committing and pushing changes.

### Backend Agent
- **Responsibilities:**
    - Implements API or server-side fixes and features.
    - Works with Information Agent to gather backend knowledge.
    - Collaborates with Documentation Agent to map out new backend areas and update knowledge.

- **Interactions:**
    - With Information Agent for context on backend and database.
    - With Documentation Agent for long-term updates.
    - With Git Agent for code commits and PRs.

### QA Agent
- **Responsibilities:**
    - Designs and runs test plans to validate ticket resolutions.
    - Ensures acceptance criteria are met.
    - Works with Documentation Agent to map out QA scenarios and update known test cases.

- **Interactions:**
    - With Information Agent for past QA cases and testing frameworks.
    - With Documentation Agent to log new QA coverage.
    - With Git Agent for test-related commits.

### Information Agent
- **Responsibilities:**
    - Retrieves context from structured memory for agents.
    - Conducts external research (e.g., package documentation, APIs).
    - Provides distilled, relevant snapshots of knowledge.

- **Interactions:**
    - With Main Agent for context delivery.
    - With all coding/QA agents to provide technical knowledge.

### Documentation Agent
- **Responsibilities:**
    - Curates and maintains structured knowledge base.
    - Updates Feature Cards, Code Maps, Schema Cards, QA Reports.
    - Works closely with Frontend, Backend, and QA Agents to log new discoveries or changes.

- **Interactions:**
    - With coding and QA agents to capture new knowledge.
    - With Main Agent to ensure documentation stays synchronized with changes.

### Git Agent
- **Responsibilities:**
    - Prepares working branches for tickets.
    - Keeps branches updated with develop/main.
    - Makes commits with clear messages.
    - Pushes branches and creates PRs with relevant context provided by Main Agent.

- **Interactions:**
    - With Frontend, Backend, and QA Agents for commits.
    - With Main Agent for orchestration and PR descriptions.

---

## Shared Memory
- Organized into **Frontend Architecture**, **Backend Architecture**, **Database Schemas**, and **Product Documentation**.
- Each category maintained in structured files and sub-folders.
- **How it works:**
    - **Information Agent** queries memory for relevant context.
    - **Documentation Agent** updates and curates memory.
    - **Frontend, Backend, and QA Agents** collaborate with Documentation Agent to map out new areas discovered during work and update existing memories when new information is found.

---

## Workflow Summary
1. **Ticket Received**: YouTrack ticket transformed into a structured prompt.
2. **Main Agent Planning**: Main Agent reviews prompt, creates high-level plan.
3. **Information Gathering**: Information Agent retrieves relevant memory and external knowledge.
4. **Task Delegation**: Main Agent assigns tasks to Frontend, Backend, QA, Git.
5. **Implementation**:
    - Frontend Agent → implements UI fixes/features.
    - Backend Agent → implements API/server changes.
    - QA Agent → validates implementation with tests.
    - Git Agent → manages branches, commits, and PRs.
    - Documentation Agent → logs updates to memory with input from other agents.
6. **Iteration**: Feedback loop between agents until the solution is validated.
7. **Finalization**: PRs created, QA approved, documentation updated, memory enriched.

---

## Example (BUG Ticket)
- **Ticket**: "Button click does not trigger API call."
- **Workflow:**
    1. Ticket → Structured Prompt.
    2. Main Agent → Orchestrates.
    3. Information Agent → Retrieves button feature mapping + API references.
    4. Frontend Agent → Fixes button handler.
    5. Backend Agent → Confirms API endpoint behavior.
    6. QA Agent → Validates bug fix.
    7. Git Agent → Creates PR with context.
    8. Documentation Agent → Updates knowledge base with button-API mapping.

---

