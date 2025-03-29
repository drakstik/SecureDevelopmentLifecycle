# ggshield
gglshield is a command, installed using Python (pip), that was created by GitGuardian. By running the command ```ggshield secret scan pre-commit```, ggshield will scan your code base, including all files, for any common secret, including API Keys, credentials, encryption keys, tokens etc...

It uses a mix of pattern matching, entropy-based detection, and specific regex patterns to identify these secrets.

ggshield can be integrate with git hooks to check for secrets at every commit. We suggest you make ggshield's secret scan a blocking rule.

# How to get a GitGuardian API Key
**Step 1:** Sign up for a GitGuardian account
**Step 2:** Go to 'Settings', then 'Personal Access Token' and create a new token.
**Step 3:** Copy the token and store it somewhere safe and easy to remember and reach. You only get to see the token once, so make sure you copy & store it safely.



