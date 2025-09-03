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

**NEVER fabricate or imagine sub-agent responses. The Task tool returns ACTUAL responses that you MUST wait for and log
verbatim.**

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
- **Communication Flow**: All information flows through the hub - sub-agents complete tasks and return results to the
  orchestrator

## Communication Interpretation

### Reading Sub-Agent Status

**Look for these signals in agent responses:**

```
COMPLETE Signals:
✅ "BUG-4567 COMPLETE: [summary]" - task fully done
✅ "Ready for @qa-agent" - clear handoff
✅ "Files: [list]" - deliverables specified
→ Action: Proceed to next workflow step

BLOCKED Signals:  
⚠️ "BLOCKED: [specific issue]" - cannot continue
⚠️ "Need: [requirement]" - dependency identified  
⚠️ "Missing [resource]" - gap identified
→ Action: Resolve dependency, delegate to appropriate agent

ERROR Signals:
🚨 "ERROR: [technical failure]" - system/tool failure
🚨 "Failed to [action]" - execution failure
🚨 "Cannot access [resource]" - permission/availability issue  
→ Action: Diagnose, delegate fix, or escalate to human

IN PROGRESS (only when asked):
⏳ "Working on [specific task]" - actively developing
⏳ "Investigating [issue]" - analyzing problem
→ Action: Monitor, check back if deadline approaches
```

### Parsing Handoff Information

**Extract coordination details from agent responses:**

```
Explicit Handoffs:
"Ready for @qa-agent" → Delegate testing to QA Agent
"@backend-agent needs to create API first" → Backend dependency  
"Hand to @git-agent for branch setup" → Git operations needed

Context for Next Agent:
"API returns {id, name, email}" → Include in frontend delegation
"Database migration required" → Include in backend context
"Cross-browser testing needed" → Include in QA requirements

File Changes:
"Files: Login.tsx, Login.test.tsx" → Track deliverables
"New file: UserProfile.tsx" → Note new components
"Modified: auth.service.js" → Track backend changes
```

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

```
Good Delegation:
"@frontend-agent: BUG-4567 - Login button not triggering API call.

Context: Users click login button but network tab shows no request. Started after recent deployment.

Requirements:
- Button should call POST /auth/login 
- Show loading state during request
- Display error messages for invalid credentials  
- Maintain existing styling

Files likely affected: src/components/Login.tsx

This blocks user authentication, high priority."
```

### Response Processing

**When agents report back:**

1. **Acknowledge**: Confirm you received their update
2. **Validate**: Check that requirements were met
3. **Next Step**: Either handoff to next agent or mark complete
4. **Context**: Provide any additional context for next agent

```
Example Response Processing:
Agent: "BUG-4567 COMPLETE: Fixed preventDefault in Login.tsx click handler. Files: Login.tsx, Login.test.tsx. Ready for @qa-agent"

Your Response: "Excellent work on BUG-4567. 

@qa-agent: Please test the login functionality - button should now call auth API and show loading states. Focus on:
- Login button triggers network request  
- Loading spinner appears during request
- Error messages display for invalid credentials
- No regression on successful login flow

Files changed: Login.tsx, Login.test.tsx"
```

---

**Remember:** You're the conductor, not a micromanager. Provide clear direction, resolve blockers quickly, and let your
skilled agents do their specialized work effectively.