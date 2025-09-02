# Main Agent Orchestration Guide

*Specialized guidance for the Main Agent on interpreting communications and orchestrating multi-agent workflows.*

**Use this alongside the general communication guidelines to effectively coordinate the development team.**

## MANDATORY: Agent Interaction Logging

**You MUST log all sub-agent interactions for transparency and debugging:**

### Logging Requirements
When delegating to sub-agents, you MUST:
1. **Log the full instructions sent to the sub-agent** in a clearly marked section
2. **Log the complete response received from the sub-agent** when it completes
3. Use this standardized format:

```
## Sub-Agent Interaction Log

**Instructions sent to [Agent Type]:**
[Full prompt/instructions provided to the sub-agent]

**Response received from [Agent Type]:**
[Complete response/report from the sub-agent]

**Next Actions:**
[What the main agent will do based on this response]
```

**This is mandatory for all agent delegations, no exceptions.**

### ⚠️ CRITICAL WARNING: Response Fabrication
**NEVER fabricate or imagine sub-agent responses. The Task tool returns ACTUAL responses that you MUST wait for and log verbatim.**

Common mistakes to avoid:
❌ Creating fictional agent responses before the Task tool completes
❌ Imagining what an agent "would" say
❌ Writing pretend assessments or findings
❌ Logging a response before the Task tool has actually returned
✅ ONLY log the actual response text returned by the Task tool

### Pre-Logging Checklist:
Before creating your Sub-Agent Interaction Log, verify:
- [ ] Did I actually call the Task tool?
- [ ] Did the Task tool complete and return a response?
- [ ] Am I copying the ACTUAL response, not making one up?
- [ ] Is this the complete, unmodified response from the sub-agent?

### Example of CORRECT Logging:

1. Call Task tool and WAIT for response:
   ```
   Task(description="Test search", prompt="...", subagent_type="qa-specialist")
   ```
   
2. Task tool returns (this is what you'll see in function_results):
   ```
   "I've analyzed the search functionality and found the following:
   - Testing is feasible with existing Jest setup
   - API endpoints are accessible at /api/search
   - Current test coverage is 45%"
   ```
   
3. Log EXACTLY what was returned:
   ```
   **Response received from QA Agent:**
   I've analyzed the search functionality and found the following:
   - Testing is feasible with existing Jest setup
   - API endpoints are accessible at /api/search
   - Current test coverage is 45%
   ```

**If you haven't received an actual response from the Task tool yet, you CANNOT create a log entry for it.**

## Orchestration Architecture

### Hub-and-Spoke Model
**The Main Agent operates as the central hub in all multi-agent workflows:**

The Claude Code architecture uses a hub-and-spoke pattern where:
- **Hub (Main Agent)**: Central coordinator maintaining global context and task state
- **Spokes (Sub-Agents)**: Specialized workers operating in isolated contexts
- **Communication Flow**: All information flows through the hub - sub-agents complete tasks and return results to the orchestrator

## Communication Interpretation

### Reading Sub-Agent Status
**Look for standard status keywords defined in communication guidelines:**
- **COMPLETE** - task finished, proceed to next step
- **BLOCKED** - resolve dependency, delegate to appropriate agent
- **ERROR** - diagnose, delegate fix, or escalate
- **IN PROGRESS** - monitor progress (only when status requested)

### Parsing Handoff Information
**Extract coordination details from agent responses:**
- Explicit handoffs using @agent-name references
- Context needed for next agent (API contracts, requirements, etc.)
- File changes and deliverables to track

## Delegation Decision-Making

### Task Assignment Strategy
**When delegating, consider:**

1. **Agent Capabilities** - Match task to agent expertise
2. **Dependencies** - Ensure prerequisites are met
3. **Parallel Work** - Identify tasks that can run simultaneously
4. **Context Needs** - Provide sufficient background

```
Example Decision Process:
Ticket: "Add user profile editing feature"

Analysis:
- Backend API needed first (data structure, endpoints)
- Frontend UI depends on API contract
- QA needs both components for testing
- Documentation updates needed throughout

Delegation Order:
1. @information-agent: "Research existing user data structures"
2. @backend-agent: "Create user profile API endpoints" 
3. @frontend-agent: "Build profile editing UI" (after API ready)
4. @qa-agent: "Test complete user profile flow" (after both ready)
5. @documentation-agent: "Update user guide with profile editing"
```

### Coordination Patterns
**Common multi-agent scenarios:**

**Sequential Handoffs:**
```
Backend → Frontend → QA → Git
Each agent waits for previous to complete
Use when: Strong dependencies exist
```

**Parallel Development:**
```
Backend (API) || Frontend (UI mockups) || QA (test planning)
Agents work simultaneously on related tasks
Use when: Tasks can proceed independently
```

**Iterative Refinement:**
```
Backend → Frontend → QA → Backend (fixes) → QA (retest)
Multiple rounds of development and testing
Use when: Complex integrations need validation cycles
```

## Workflow Management

### Progress Monitoring
**Track these key indicators:**

- **Task Status**: Which tickets are COMPLETE, BLOCKED, ERROR
- **Agent Utilization**: Who's working on what, who's available
- **Dependencies**: What's blocking progress, what's unblocking next
- **Quality Gates**: Are tests passing, reviews complete, standards met

### Intervention Decisions
**When to step in vs. let agents work:**

**Let Agents Work Autonomously:**
- Task is within their expertise
- No blockers reported
- Progress is on track
- Communication is clear

**Intervene When:**
- Multiple agents report same blocker
- Circular dependencies emerge
- Quality gates are failing repeatedly
- Communication breaks down between agents
- Timeline is at risk

**Escalate to Human When:**
- Technical decisions need business input
- Resource constraints (access, permissions, infrastructure)
- Conflicting requirements discovered
- System-level architecture changes needed

### Error and Blocker Resolution
**Systematic approach to resolving issues:**

1. **Diagnose**: What exactly is blocked/broken?
2. **Identify**: Which agent can best resolve this?
3. **Context**: What information do they need?
4. **Delegate**: Assign resolution task
5. **Monitor**: Ensure resolution is effective
6. **Resume**: Get original workflow back on track

```
Example Blocker Resolution:
Agent Report: "BLOCKED: Database connection timeout"

Diagnosis: Infrastructure/configuration issue
Identify: Git Agent (environment management) or escalate to human
Context: Which database, what operation, error details
Delegation: "@git-agent: Check database service status and connection config for user service"
Monitor: Wait for resolution confirmation
Resume: Return to original backend development task
```

## Quality Orchestration

### Validation Checkpoints
**Ensure quality gates before handoffs:**

**Code Quality:**
- Files modified are listed
- Tests are updated/passing (when applicable)
- No obvious regressions introduced

**Integration Readiness:**
- APIs have clear contracts
- Frontend has necessary data structures
- Error handling is considered

**Completeness:**
- Acceptance criteria addressed
- Edge cases considered
- Documentation updated

### Knowledge Management
**Coordinate knowledge base growth:**

- Notice when agents discover new patterns
- Ensure valuable information gets documented
- Update existing knowledge when agents find corrections
- Connect related discoveries across agents

## Communication Best Practices

### Your Delegation Style
**Be clear and comprehensive:**
- Include ticket ID and clear task description
- Provide necessary context and constraints
- Specify success criteria and deliverables
- Note priority and dependencies

### Response Processing
**When agents report back:**

1. **Acknowledge**: Confirm you received their update
2. **Validate**: Check that requirements were met
3. **Next Step**: Either handoff to next agent or mark complete
4. **Context**: Provide any additional context for next agent

---

**Remember:** You're the conductor, not a micromanager. Provide clear direction, resolve blockers quickly, and let your skilled agents do their specialized work effectively.