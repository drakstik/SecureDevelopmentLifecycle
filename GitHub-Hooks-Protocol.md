Git hooks can be used to alert developers of security issues that must be resolved before git commit, push and pull. These git hooks will run some static analysis security tools (ESLint, npm/pnpm audit, ggshield secret scan, etc...) and verify no critical security flaws are found. If critical security vulnerabilities or coding errors are found then the hook will alert the developer, create a tool report file and maybe block the developer from pushing to remote until the issues are resolved or exceptions made.

# Which security tools do we run?

- Remove old reports:
    -   ```rm .cache_ggshield .eslint-report .npm-audit-report .pnpm-audit-report```
- No ESLint errors + configure eslint so only security issues are errors,
    -   ```pnpm eslint . --fix > .eslint-report```
- trying to disable ESLint,
    -   ```grep -r --exclude-dir=node_modules --include='*.js' --include='*.ts' --include='*.jsx' --include='*.tsx' "eslint-disable" . ```
- critical/high dependency vulnerabilities
    -   ```npm audit --fix > .npm-audit-report```
    -   ```pnpm audit --fix > .pnpm-audit-report```
- No secrets:
    -   ```ggshield secret scan pre-commit "$@"```
      

# How to integrate git hooks into your project
Security team is responsible for ensuring smooth integration of git hooks into every project.

Here are some resources on what are git hooks and how to integrate them into your project:
- [Youtube video: Complete guide to GitHooks - Creating your own pre-commit hooks
](https://www.youtube.com/watch?v=ObksvAZyWdo)
- [Atlasian tutorial on git hooks](https://www.atlassian.com/git/tutorials/git-hooks)

Here's an example of how the hook file should look like:

```
#!/bin/bash

rm .cache_ggshield .eslint-report .npm-audit-report .pnpm-audit-report

# Run GitGuardian secret scan before anything else
ggshield secret scan pre-commit "$@"

# Check for eslint-disable comments in the codebase, excluding node_modules
grep -r --exclude-dir=node_modules --include='*.js' --include='*.ts' --include='*.jsx' --include='*.tsx' "eslint-disable" .

if [ $? -eq 0 ]; then
    echo -e "\e[31mAttention! You are disabling some ESLint rules. Please make sure you remove the comments \"disable-eslint\"  before pushing!\e[0m"
    # exit 1
fi



# Run ESLint with the fix command, 
#       if the eslint report contains the string "error" then echo an alert 
#       and write the eslint report to a file called .eslint-report
pnpm eslint . --fix > .eslint-report 2>&1
if grep -q "error" .eslint-report; then
    echo -e "\e[31mAttention! ESLint found errors in the code (see .eslint-report). Please resolve them or request exceptions before pushing!\e[0m"
    # exit 1
fi

# Run npm audit & pnpm audit with the fix command
#       if vulnerable dependencies are found in the reports with "critical" or "high" severity,
#       echo an alert and write the two audit reports (npm & pnpm) to a file called .npm-audit-report
npm audit --fix > .npm-audit-report 2>&1
pnpm audit --fix > .pnpm-audit-report 2>&1

if grep -E "critical|high" .npm-audit-report || grep -E "critical|high" .pnpm-audit-report; then
    echo -e "\e[31mAttention! npm/pnpm audit found critical/high security vulnerabilities in dependencies (see .npm-audit-report and pnpm-audit-report). Please resolve them or request exceptions before pushing!\e[0m"
    # exit 1
fi

exit 0
```

