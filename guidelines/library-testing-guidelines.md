# Library Testing Guidelines

**Read this file when working on `@ideascale/commons` or `@ideascale/ui` library changes.**

## Local Library Testing Protocol

**MANDATORY before any QA handoff when modifying library code:**

### Library Local Publishing
1. Navigate to library directory (`commons` or `ui`)
2. Run: `./gradlew local_publish`  
3. Verify successful local publish completion

### Target App Integration Testing
1. Navigate to target app directory
2. Update `app/build.gradle.kts` - change library versions to `"0.0.0-local"`
3. Run: `./gradlew npmInstall`

### Build Verification
- Run `./gradlew build` on the consuming app if changes seem like they might break compilation.
- Resolve any build errors before proceeding to QA

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