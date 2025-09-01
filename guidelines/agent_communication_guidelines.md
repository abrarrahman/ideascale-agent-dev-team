# Agent Communication Guidelines

*Single source of truth for how agents communicate effectively in the multi-agent development system.*

**For Sub-Agents:** Follow these patterns for clear communication
**For Main Agent:** Reference the additional orchestration guide in your system prompt

## Communication Structure

### Main Agent Communications (Orchestration)
**Main Agent coordinates the workflow using these patterns:**

```yaml
# Task Delegation
To: [Agent]
Type: TASK_ASSIGNMENT
Content:
  - Task description
  - Success criteria  
  - Context/constraints
  - Dependencies (wait for X before starting)
  - Expected deliverables

# Status Requests  
To: [Agent]
Type: STATUS_REQUEST
Expected Response: Current progress, blockers, ETA

# Quality Gate Validation
To: [Agent] 
Type: VALIDATION_REQUEST
Content: Specific criteria to verify
Expected Response: PASS/FAIL with evidence
```

### Direct Agent Communications (Implementation)
**Agents coordinate directly for specialized collaboration:**

```yaml
# Frontend <-> Backend
Type: CONTRACT_NEGOTIATION
Content: API specifications, data formats, error handling

# Development Agent <-> QA Agent
Type: TEST_COLLABORATION  
Content: Test scenarios, expected behaviors, edge cases

# Any Agent <-> Documentation Agent
Type: KNOWLEDGE_UPDATE
Content: New discoveries, architectural decisions, patterns
```

### Information Flow Rules
```yaml
# Information Discovery
When any agent discovers valuable information:
-> Share with Main Agent (for immediate task context)
-> Record with Documentation Agent (for future knowledge)

# Context Requests
Agent needs context -> Information Agent -> Returns relevant knowledge
Agent needs current task context -> Main Agent -> Returns task-specific details
```

## Core Principles

**Be Clear, Be Specific, Be Helpful**
- Include ticket numbers: "Working on BUG-4567"
- Name files you're changing: "Updated Login.tsx and Login.test.tsx"
- Explain what you actually did: "Fixed preventDefault issue in click handler"
- Give the next agent what they need: "API returns {id, name, email} structure"

## Practical Communication Examples

### Main Agent Task Delegation
```
Type: TASK_ASSIGNMENT
To: @frontend-agent

BUG-4567 - Fix login button not calling API

Success Criteria:
- Button triggers POST /auth/login on click
- Loading state shows during request  
- Error messages display for failed attempts

Context: Users report login button does nothing. Network tab shows no request.
Dependencies: None (backend API already working)
Deliverables: Fixed Login component + updated tests

Priority: High (blocks user authentication)
```

### Agent-to-Agent Contract Negotiation
```
Type: CONTRACT_NEGOTIATION
@backend-agent -> @frontend-agent

User Profile API Contract:

GET /api/users/:id
Response: {
  id: string,
  name: string, 
  email: string,
  avatar?: string,
  preferences: {
    theme: 'light' | 'dark',
    notifications: boolean
  }
}

Errors:
- 404: User not found
- 401: Invalid token  
- 500: Server error

Ready for frontend integration. Questions?
```

### QA Test Collaboration
```
Type: TEST_COLLABORATION  
@qa-agent <-> @frontend-agent

Login Flow Testing:

Test Scenarios:
1. Valid credentials -> successful login + redirect
2. Invalid email -> error message displayed
3. Invalid password -> error message displayed  
4. Network failure -> retry mechanism + error handling
5. Loading states -> spinner shows during API call

Edge Cases:
- Empty form submission
- SQL injection attempts in email field
- Very long passwords (>100 chars)

Expected Behaviors:
- Form validates before API call
- Errors clear when user retypes
- Session persists across page refresh

Need any clarification on expected UI behavior?
```

### Knowledge Update Flow
```
Type: KNOWLEDGE_UPDATE
@frontend-agent -> @documentation-agent

New Discovery: Login Error Handling Pattern

Context: While fixing BUG-4567, discovered effective pattern for form error handling.

Pattern:
- Use error state per field + global error state
- Clear field errors onChange, global errors onSubmit
- Show loading state during API calls
- Provide retry mechanism for network failures

Files: src/components/Login.tsx, src/hooks/useFormValidation.ts
Category: frontend/patterns/form-handling

This pattern could be reused for signup, password reset, and profile editing forms.
```

## Status Reporting (Sub-Agent -> Main Agent)

```
Type: STATUS_UPDATE or TASK_COMPLETE
Format: {ticket-id} {STATUS}: {what you did} | Files: {files} | {handoff/notes}

Examples:
"BUG-4567 COMPLETE: Fixed preventDefault issue in Login.tsx click handler. 
Files: src/components/Login.tsx, tests/Login.test.tsx  
Ready for @qa-agent"

"FEATURE-123 COMPLETE: Created user profile API at GET /api/users/:id
Files: src/routes/users.js, tests/users.test.js
Returns: {id, name, email, avatar, preferences}
Ready for @frontend-agent integration"

Include:
✅ Ticket ID + clear status (COMPLETE/BLOCKED/ERROR)
✅ Summary of what changed/built  
✅ Files modified
✅ Handoff or context for next agent
❌ Progress percentages
❌ Verbose technical details not needed for coordination
```

## Information Requests

### Context Discovery
```
Type: CONTEXT_REQUEST
Agent -> @information-agent

"Need context on user authentication patterns in the codebase.
Working on BUG-4567 - login button issue.
Specifically looking for: existing auth components, API patterns, error handling approaches."

Expected Response: Relevant documentation, code examples, established patterns
```

### Task Context
```
Type: STATUS_REQUEST
Main Agent -> Sub-Agent

"@frontend-agent: Status check on BUG-4567 - login button fix.
Need: Current progress, any blockers, estimated completion."

Expected Response: Progress update, blockers if any, next steps
```

## Error Reporting and Escalation

### Blocked Tasks
```
Type: BLOCKED_TASK
Format: {ticket-id} BLOCKED: {specific issue} | Need: {what's needed}

Examples:
"BUG-4567 BLOCKED: Database connection timeout during user validation
Need: Check if user service is running"

"FEATURE-456 BLOCKED: Missing API specification for payment endpoint  
Need: @backend-agent to confirm expected request/response format"
```

### System Errors
```
Type: ERROR_REPORT  
Format: {ticket-id} ERROR: {technical failure} | Impact: {BLOCKING/WARNING} | {resolution info}

Example:
"FEATURE-789 ERROR: TypeScript compilation failed after adding new interface
Impact: BLOCKING
Files: src/types/User.ts - missing import for UserPreferences type"
```

## Quality Gates and Validation

### Validation Requests (Main Agent -> Sub-Agent)
```
Type: VALIDATION_REQUEST
Content: Specific criteria to verify

"@qa-agent: Validate BUG-4567 fix - login functionality
Criteria:
- Login button triggers network request
- Loading spinner appears during request  
- Error messages show for invalid credentials
- No regression on successful login flow
Expected Response: PASS/FAIL with test evidence"
```

### Validation Responses (Sub-Agent -> Main Agent)
```
Type: VALIDATION_COMPLETE
Format: {ticket-id} {PASS/FAIL}: {test results} | {evidence/issues}

Examples:
"BUG-4567 PASS: All login functionality tests passing
✅ API call triggers correctly
✅ Loading states work
✅ Error handling functional  
✅ No regressions detected
Cross-browser tested: Chrome, Firefox, Safari"

"BUG-4567 FAIL: Error handling not working
❌ Network failures show generic 'Something went wrong'
❌ Error messages not clearing when user retypes
Need: Specific error messages + proper error state management"
```

## Agent Addressing and Handoffs

**Direct Addressing:** Use @agent-name for specific coordination needs
- `@qa-agent` - testing and validation
- `@git-agent` - version control operations
- `@frontend-agent` - UI/component work
- `@backend-agent` - API/service work
- `@information-agent` - context and research
- `@documentation-agent` - knowledge base updates

**Handoff Patterns:**
```
Ready Handoffs:
"Ready for @qa-agent" - task complete, needs testing
"@backend-agent ready for integration" - API available for frontend
"Hand to @git-agent for deployment" - ready for release process

Dependency Handoffs:  
"Need @backend-agent to create API first" - blocking dependency
"Waiting for @information-agent context on auth patterns" - info dependency
"@qa-agent should review approach before implementation" - validation dependency
```

## Information Sharing Best Practices

### Context for Next Agent
```
Good Examples:
✅ "API endpoint ready: POST /auth/login - expects {email, password}, returns {token, user}"
✅ "Updated Login.tsx click handler - removed form submission, added async API call"
✅ "Database schema updated - added user_preferences table with theme/notification columns"

Poor Examples:
❌ "Authentication API is done"  
❌ "Fixed the login issue"
❌ "Database work complete"
```

### File References
- Always use relative paths: `src/components/Login.tsx`
- List all modified files: `Files: Login.tsx, Login.test.tsx, auth.service.js`
- Distinguish new vs. updated: `New: UserProfile.tsx | Updated: Dashboard.tsx`

### Technical Integration Details
Include what the next agent needs:
- **API contracts**: Request/response formats, error codes
- **Data structures**: Interface definitions, schema changes
- **Behavior changes**: New features, modified workflows
- **Dependencies**: Required imports, configuration changes

Skip what they don't need:
- Implementation details that don't affect integration
- Internal refactoring that doesn't change external behavior
- Debugging steps that led to the solution

## Knowledge Update Patterns

When documenting discoveries:
```yaml
Type: KNOWLEDGE_UPDATE
To: @documentation-agent
Path: knowledge-base/frontend/ideation/gotchas.md
Content: 
  Issue: Form submission prevents button onClick
  Solution: Use preventDefault or remove form wrapper
  Discovered: BUG-4567
  Affects: All button components inside forms
```

## Communication Tone and Best Practices

- **Professional but conversational** - teammates working together
- **Helpful and informative** - provide what others need to succeed
- **Concise but complete** - essential details, skip unnecessary information
- **Solution-focused** - when reporting problems, suggest fixes when possible
- **Collaborative** - recognize interdependencies and shared goals

**Status Keywords** (for consistent parsing):
- **COMPLETE** - task finished successfully
- **BLOCKED** - cannot continue without external help
- **ERROR** - something failed, needs investigation
- **PASS/FAIL** - validation results
- **IN PROGRESS** - working on it (only when status requested)

---

**Remember:** Effective communication makes the entire development team more productive. When in doubt, err on the side of being more specific and helpful to your teammates.