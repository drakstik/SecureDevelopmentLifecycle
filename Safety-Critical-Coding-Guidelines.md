# What is Safety-Critical Code?
Safety-Critical code is developed to reliably offer the C.I.A. properties (Confidentiality, Integrity and Availbability), in a verifiable way. Sometimes, automated tools are not enough to verify source code reliability, and safety-critical coding guidelines can be used by developers to analyze their code for security flaws, as they design and write it. 

Safety-Critical code can be found in financial or medical systems, national infrastructure, and other high-risk systems.

# What kind of rules should we have in our guidelines?
Our coding guidelines are based on [NASA's Critical Coding Rules](https://web.eecs.umich.edu/~imarkov/10rules.pdf) which have been used since 2006 to build safety-critical and secure code. NASA's rules are based on C, so we will have to adapt each rule to focus on TypeScript language components.

NASA has some principals it follows to document these rules:
- Each rule in the coding guideline should be strict enough that developers and auditors can use the rules to strongly check source code against.
- The number of rules should be limited for the guidelines to be memorable, and therefor effective.

Our secure coding rules can be categorized into guideline documents that focus on:
- Memory Safety
- Type Safety
- Control Flow Safety
	- Concurrency Handling
	- Error Handling

Rules, guidelines and recommendations can be categorized and documented seperately. Each document should:
1. describe the broad security risk associated with NOT adhering to that category of rules, 
2. list the specific rules in that category,
3. list risk management strategies, frameworks or tools that can be used to reduce the risk of NOT adhering to the rule.






