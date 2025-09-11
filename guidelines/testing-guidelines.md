# Testing Guidelines

## Base URLs
- **Local Development Environment** (via host mapping): https://ideas.ideascale.me
- **Note**: ideas.ideascale.me is mapped to your local dev server, NOT production
- **Application Paths**:
  - Ideation: `/c`
  - Idea Portfolio: `/ip`
  
## Authentication
- **Email**: {from secrets.env: IDEASCALE_TEST_EMAIL}
- **Password**: {from secrets.env: IDEASCALE_TEST_PASSWORD}

---

# CRITICAL: MANDATORY TESTING PROTOCOLS

## ABSOLUTE REQUIREMENT: Evidence-Based Testing ONLY

**MANDATORY: ALL testing MUST be performed through live Playwright browser execution**

**ABSOLUTELY PROHIBITED:**
- Assuming functionality based on code reading
- Creating test reports without actual browser execution
- Fabricating test results when unable to test
- Providing test conclusions without screenshot evidence

## MANDATORY: Evidence Collection for Every Test

**REQUIRED EVIDENCE - NO EXCEPTIONS:**
- **Screenshot file paths**: Exact path to screenshot files you captured
- **Network request logs**: Using browser_network_requests for form submissions
- **Console output**: Using browser_console_messages for JavaScript errors
- **DOM element details**: Actual element text/attributes from interactions

**FABRICATION DETECTION:**
- If you cannot provide file paths to screenshots → You didn't test
- If you cannot provide specific DOM element details → You didn't interact
- If you cannot provide network request logs → You didn't observe behavior

## MANDATORY: Testing Protocol

**EVERY test session MUST follow this protocol:**
1. Always use ideas.ideascale.me URLs (host-mapped to local development)
2. This tests your LOCAL changes via the dev server
3. NEVER test actual production - all testing is local development
4. Capture screenshots for every significant interaction using browser_take_screenshot
5. Document authentication flow if login required
6. Report any access issues immediately using TESTING BLOCKED protocol
7. Save screenshots with descriptive names: `test-{timestamp}-{description}.png`

## MANDATORY: Failure Reporting Protocol

**If unable to access application or complete testing:**
1. Use browser_take_screenshot to capture error states
2. Use browser_console_messages to capture JavaScript errors  
3. Report exact URL accessed and error encountered
4. **Do NOT fabricate test results - report inability to test instead**

**REQUIRED RESPONSE FORMAT when blocked:**
```
## TESTING BLOCKED - CANNOT PROCEED

**Attempted Action**: [Specific action tried]
**Blocker Encountered**: [Specific error/issue]
**Evidence**: [Screenshot file path, console logs, error messages]
**Environment Details**: [URL, browser, authentication status]
**Required Resolution**: [What needs to be fixed to proceed]

## NO TESTING RESULTS AVAILABLE
Unable to provide test results due to above blocker.
```

## VIOLATION CONSEQUENCES

- **Any test report without browser evidence = immediate rejection**
- **Any fabricated results = critical system failure**
- **Any assumption-based testing = boundary violation**
- **Must provide actual evidence or explicitly report inability to test**

## REMEMBER: Evidence-First Testing

- Every claim requires proof through actual browser interaction
- Screenshots, network logs, and console outputs are mandatory
- When blocked, report inability rather than fabricating results
