# Implement Feature (Interview Mode)

Implement a feature with proper requirements clarification. Use this during interviews to ensure edge cases and scope are clarified before writing any code.

## Arguments
$ARGUMENTS - Feature description from interviewer

## Steps

1. **Analyze Requirements** (NO CODE YET)
   - Parse the feature description
   - Identify ambiguities, edge cases, and unclear scope
   - DO NOT write any code in this step

2. **Ask Clarifying Questions** (REQUIRED)
   Use AskUserQuestion tool to clarify:
   - **Scope**: What's explicitly OUT of scope?
   - **Edge cases**: How to handle empty states, errors, loading?
   - **Validation**: What rules apply to user input?
   - **Success criteria**: What does "done" look like?

   Format questions with specific options, not open-ended. Example:
   ```
   Question: "How should we handle the empty state?"
   Options:
   - Show illustration with message
   - Show simple text message
   - Hide the section entirely
   ```

3. **Confirm Understanding**
   Summarize the requirements back to the interviewer:
   - What will be built
   - What edge cases will be handled
   - What is explicitly out of scope

4. **Explore Existing Code**
   - Search for existing patterns in the codebase
   - Identify reusable components, utilities, or patterns
   - Note the project's architectural conventions

5. **Plan Implementation**
   Create a brief implementation plan:
   - Files to create/modify
   - Existing components to reuse
   - Architecture patterns to follow

6. **Implement**
   - Follow project architecture patterns
   - Reuse existing components where possible
   - Add strings to `res/values/strings.xml`
   - Handle the edge cases identified in step 2

7. **Verify**
   - Run the build command (e.g., `./gradlew assembleDebug`)
   - Confirm all edge cases from step 2 are addressed
