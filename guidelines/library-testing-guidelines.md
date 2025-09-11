# Library Testing Guidelines

**Read this file when working on `@ideascale/commons` or `@ideascale/ui` library changes.**


## Library-Specific QA Handoff Instructions

**In addition to standard frontend QA handoff procedures, include:**

1. **Library Change Details**
   - Which specific library was modified (`@ideascale/commons` or `@ideascale/ui`)
   - Specific components/functions/utilities affected
   - Version changed to "0.0.0-local" for testing

2. **Multi-App Testing Requirements**
   - List all apps that use the modified library components
   - Specify which apps need QA validation
   - Note any app-specific behaviors or styling differences

## Cross-App Impact Analysis

**Before making library changes:**

1. **Identify Usage** - Search codebase to find which apps use the component/function
2. **Document Dependencies** - Note any app-specific behaviors or styling overrides
3. **Plan Rollback** - Ensure you can revert changes if issues are found

## Common Issues & Solutions

**Local Publish Issues:**
- Ensure you're in the correct library directory
- Check for syntax errors in library code
- Verify gradle build succeeds before publishing

**App Integration Issues:**
- Confirm version changed to "0.0.0-local" in build.gradle.kts
- Run `./gradlew clean npmInstall` to force dependency refresh
- Check for TypeScript errors after library changes
- Remember to ask the orchestrator to close and restart dev server after npmInstall

---

# CRITICAL: MANDATORY LIBRARY TESTING WORKFLOW

## ABSOLUTE REQUIREMENT: Library Local Testing Protocol

**MANDATORY when modifying `@ideascale/commons` or `@ideascale/ui` library code:**

**FAILURE TO FOLLOW THIS WORKFLOW = INVALID HANDOFF**

### Step 1: Library Local Publishing (NO EXCEPTIONS)
1. Navigate to library directory (`commons` or `ui`)
2. Run: `./gradlew local_publish`  
3. Verify successful local publish completion
4. **DO NOT PROCEED** until local_publish succeeds

### Step 2: Target App Integration Testing (MANDATORY)
1. Navigate to target app directory
2. Update `app/build.gradle.kts` - change library versions to `"0.0.0-local"`
3. Run: `./gradlew npmInstall`
4. **CRITICAL**: After npmInstall, notify Main Orchestrator: "npmInstall completed, please restart dev server"

### Step 3: Build Verification (REQUIRED)
- Run `./gradlew build` on the consuming app if changes seem like they might break compilation
- Resolve any build errors before proceeding to QA
- **DO NOT HANDOFF** until builds succeed

## ABSOLUTELY PROHIBITED: Server Management

**NEVER attempt server operations yourself:**
- **NEVER start/stop/restart development servers**
- **NEVER manage server processes or ports**
- **NEVER execute server management commands (npm start, kill processes, etc.)**

**ALWAYS notify Main Orchestrator instead:**
- "npmInstall completed, please restart dev server"
- "Server restart needed for library changes"  
- "Dev server management required for [specific reason]"

## MANDATORY: Multi-App Testing Requirements

**In addition to standard QA handoff procedures, MUST include:**

1. **Library Change Details**
   - Which specific library was modified (`@ideascale/commons` or `@ideascale/ui`)
   - Specific components/functions/utilities affected
   - Version changed to "0.0.0-local" for testing

2. **Cross-App Impact Analysis**
   - List all apps that use the modified library components
   - Specify which apps need QA validation
   - Note any app-specific behaviors or styling differences

## VIOLATION CONSEQUENCES

- **Any library changes without local_publish = immediate task rejection**
- **Any handoff without "0.0.0-local" version update = invalid workflow**
- **Any server management attempts = boundary violation**
- **Must complete ALL steps in exact order before QA handoff**

## REMEMBER: Frontend Agent Library Boundaries

- **YOU HANDLE**: Code implementation, local_publish, build.gradle.kts updates, npmInstall
- **MAIN ORCHESTRATOR HANDLES**: All server management (start/stop/restart/monitor)
- **PROTOCOL**: After npmInstall notify orchestrator: "npmInstall completed, please restart dev server"
- **When in doubt, follow the workflow exactly as specified**