# Testing Guidelines

## Base URLs
- **Production Environment**: https://ideas.ideascale.me
- **Application Paths**:
  - Ideation: `/c`
  - Idea Portfolio: `/ip`
  
## Authentication
- **Email**: {from secrets.env: IDEASCALE_TEST_EMAIL}
- **Password**: {from secrets.env: IDEASCALE_TEST_PASSWORD}

## Testing Protocol
1. Always use production environment (ideas.ideascale.me)
2. Never use localhost for integration testing
3. Capture screenshots for every significant interaction using browser_take_screenshot
4. Document authentication flow if login required
5. Report any access issues immediately using TESTING BLOCKED protocol
6. Save screenshots with descriptive names: `test-{timestamp}-{description}.png`

## Email Testing
- **Email Monitor**: https://mail.ideascale.me/monitor

## Required Evidence for All Tests
- Screenshot file paths for every claimed interaction
- Network request logs using browser_network_requests when testing form submissions
- Console output using browser_console_messages for any JavaScript errors
- Exact DOM element details for any UI interactions

## Failure Reporting
If unable to access application or complete testing:
1. Use browser_take_screenshot to capture error states
2. Use browser_console_messages to capture JavaScript errors  
3. Report exact URL accessed and error encountered
4. Do NOT fabricate test results - report inability to test instead
