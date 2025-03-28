# Contents
- [0. Introduction](#introduction)
- [1. Integrating ESLint with a project](#1-integrating-eslint-with-a-project)
- [2. Configuration](#2-configuration)
	- [2.1 Which security plugins should we use?](#21-which-security-plugins-should-we-use)
		- [2.1.1 Installing eslint-plugin-security](#211-installing-eslint-plugin-security)
		- [2.1.2 Installing eslint-plugin-no-unsanitized](#212-installing-eslint-plugin-no-unsanitized)
		- [2.1.3 Installing eslint-plugin-no-secrets](#213-installing-eslint-plugin-no-secrets)
- [3. Run ESLint (For Developers)](#3-run-eslint-for-developers)
	- [3.1 Regularly run ESLint or automate it with GitHub Actions](#31-regularly-run-eslint-or-automate-it-with-github-actions)
- [4. Resolving security problems (For Developers)](#4-resolving-security-problems-for-developers)
	- [4.1 Common security rules and how to resolve them](#41-common-security-rules-and-how-to-resolve-them)
		- [4.1.1 @typescript-eslint/no-explicit-any](#411-typescript-eslintno-explicit-any)
		- [4.1.2 security/detect-object-injection](#412-securitydetect-object-injection)
- [5. Tracking exceptions (For Developers)](#5-tracking-exceptions-for-developers)
	- [5.1 Tracking Exceptions with an Excel Template](#51-tracking-exceptions-with-an-excel-template)

#  Introduction 

ESLint statically analyses your JavaScript codebase for problems. ESLint comes with pre-installed syntax and logical **rules** that are used to check against your JavaScript code, and report on when the code fails to pass those rules. Third-party plugins can be used to check against certain security rules.

# 1\. Integrating ESLint with a project 

Download your project from a safe development environment and install it locally, according to the project's installation instructions.

The project may already have packages related to ESLint, so ensure they are the latest releases. To do this, use `pnpm outdated` **|** `npm outdated` to view outdated packages, and update all ESLint related package with the commands `pnpm remove package-name && pnpm add package-name -D`. Repeat the process until all ESLint packages are up to date.

# 2\. Configuration

The project may already have an ESLint configuration file. If this is the case, delete that configuration file because we will create a new one with the commands `pnpm dlx @eslint/create-config` **|** `npm init @eslint/config@latest`. Also delete all comment eslint configurations, they look like this ```eslint-disable ...``` This command will prompt you to answer some questions about your project and once you answer the questions, you should have a eslint.config.mjs file that looks like this:

```
import { defineConfig } from "eslint/config";
import globals from "globals";
import eslint from "@eslint/js";
import tseslint from "typescript-eslint";


export default defineConfig([
  { files: ["**/*.{js,mjs,cjs,ts}"] },
  { files: ["**/*.js"], languageOptions: { sourceType: "script" } },
  { files: ["**/*.{js,mjs,cjs,ts}"], languageOptions: { globals: {...globals.browser, ...globals.node} } },
  { 
    files: ["**/*.{js,mjs,cjs,ts}"], 
    plugins: { eslint }, 
    extends: ["eslint/recommended"],
  },
  tseslint.configs.recommended,
]);

```

This configuration code extends the recommended configs for eslint and tseslint. Some of the rules are not security related, here's a better representation of which rules are security related (set to "error") and which are not (set to "warn"):

```
import { defineConfig } from "eslint/config";
import globals from "globals";
import eslint from "@eslint/js";
import tseslint from "typescript-eslint";


export default defineConfig([
  { files: ["**/*.{js,mjs,cjs,ts}"] },
  { files: ["**/*.js"], languageOptions: { sourceType: "script" } },
  { files: ["**/*.{js,mjs,cjs,ts}"], languageOptions: { globals: {...globals.browser, ...globals.node} } },
  { 
    files: ["**/*.{js,mjs,cjs,ts}"], 
    plugins: { eslint }, 
    extends: ["eslint/recommended"], 
    rules: { 
      "no-unused-labels": "warn",
      "no-useless-catch": "warn",
      "no-empty": "warn"
    } // Disabling an eslint rule
  },
  tseslint.configs.recommended,
  {
    files: ["**/*.{ts,tsx}"], 
    plugins: {tseslint},
    rules: { 
      "@typescript-eslint/no-unused-vars": "warn",
      "@typescript-eslint/no-unused-expressions": "warn",

      /** SECURITY RELATED RULES */
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/no-require-imports": "error",
      "@typescript-eslint/no-unsafe-function-type": "error",
    }
  }
]);
```

## 2.1 Which security plugins should we use?

Security plugins for nodejs should be selected based a) on the reputation of the group that maintains the plugin, b) their popularty (weekly downloads) and c) how long since their last publish.

For example, these are some plugins selected with this criteria:

**1\. eslint-plugin-security -- Maintained by ESLint Community  
2\. eslint-plugin-no-unsanitized -- Maintained by Mozilla  
3\. @microsoft/eslint-plugin-sdl -- Maintained by Microsoft  **

Other plugins may not necessarily be security related, but they may still be useful to incorporate in the SDL. For example **@microsoft/eslint-formatter-sarif** so it can be imported into tools like GitHub Advanced Security.

Some people are attempting to maintain a list of cool ESLint plugins [here](https://github.com/dustinspecker/awesome-eslint).

### 2.1.1 Installing eslint-plugin-security

[eslint-plugin-security](https://www.npmjs.com/package/eslint-plugin-security) can help identify potential security hotspots, but finds a lot of false positives which need triage by a human.

To install the plugin's package, use the command `pnpm add eslint-plugin-security -D` or `npm install --save-dev eslint-plugin-security`.

Then import the package within the eslint.config.mjs file, like this:

```
import security from "eslint-plugin-security";

```

and add these lines in the defineConfig() function:

```
export default defineConfig([
    security.configs.recommended,
    {
    	files: ["**/*.{js,mjs,cjs,ts}"],
    	plugins: {security},
    	rules: {
      		"security/detect-object-injection": "error",
      		"security/detect-non-literal-fs-filename": "error",
    	}
  	}
])
```

**NOTE**: that eslint-plugin-security has all its rules as warnings. As you discover new security rule failures, set them as an error.

### 2.1.2 Installing eslint-plugin-no-unsanitized

[eslint-plugin-no-unsanitized](https://www.npmjs.com/package/eslint-plugin-no-unsanitized) has rules that disallow certain function calls. E.g., document.write() or insertAdjacentHTML(), and certain assignment expressions, e.g., to innerHTML.

To install the plugin's package, use the command `pnpm add eslint-plugin-no-unsanitized -D` or `npm install --save-dev eslint-plugin-no-unsanitized`.

Then import the package within the eslint.config.mjs file, like this:

```
import noUnsanitized from "eslint-plugin-no-unsanitized";

```

and add these lines in the defineConfig() function:

```
export default defineConfig([
    {
        files: ["**/*.{js,mjs,cjs,ts}"],
        plugins: {noUnsanitized},
        rules: {
            "noUnsanitized/method": "error",
            "noUnsanitized/property": "error",
        },
    },
])
```

# 3\. Run ESLint (For Developers)

While in the project folder you can run: `pnpm eslint .` or `pnpm run lint` **|** `npm eslint .` or `npm run lint`. The script for the keyword 'lint' can be found in the packages.json under "script" and it should look like this `lint": "eslint \"{src,apps,libs,test}/**/*.ts\" --fix"`.

## 3.1 Regularly run ESLint or automate it with GitHub Actions

Developers are expected to regularly run ESLint before uploading their code to a safe development environment, like GitHub. Ultimately, ESLint should be used in combination with GitHub Actions to only allow code that passes our ESLint security tests.

It is the security team's responsibility to make the running of ESLint tests as easy as possible for the developers. It is the developer's responsibility to get familiar, over time, with resolving the simpler issues that are raised by ESLint. If unsure a Developer should reach out to a senior developer or a security expert for further consultation. Security issues should not be taking much of your time, if the security team is doing it's job well.

# 4\. Resolving security problems (For Developers)
When a developer runs ESLint and encounters errors (in red), they can travel to the file and line where the problem is occuring, and they can Google for how to resolve it. 

If it takes too long (more than 10 mins), the developer should consult this document, ask a security expert or another developer for help.

If all these strategies fail to provide a resolution in a timely manner, the developer should consider adding this issue to the list of exceptions (see [section 5](#)).
## 4.1 Common security rules and how to resolve them
### 4.1.1 [@typescript-eslint/no-explicit-any](https://github.com/eslint-community/eslint-plugin-security/blob/main/docs/rules/detect-object-injection.md) 
This error tends to occur when catching and handling errors:
```
try {
  // Your code here
} catch (error: any) {
  // Handle error
}
```
To resolve this, we set the [useUnknownInCatchVariables](https://www.typescriptlang.org/tsconfig/#useUnknownInCatchVariables) TypeScript compiler option to 'true' and use this coding pattern to catch errors instead:
```
try {
  // ...
} catch (err: unknown) {
  // We have to verify err is an
  // error before using it as one.
  if (err instanceof Error) {
    console.log(err.message);
  }
}
```

### 4.1.2 security/detect-object-injection
This error tends to occur when accessing an object properties using bracket notation. For example, any of these patterns may get flagged:
```
object[key] = value;

value = object[key];

doSomething(object[key]);
```

In practice, you may encounter this error for the line:
```
state.subsidiaries[index] = action.payload;
```
You may replace this line with these lines instead, which should have the same logic:
```
state.subsidiaries = state.subsidiaries.map((subsidiary) => {
	if (subsidiary.id === action.payload.id) {
		return action.payload;
	} else {
		return subsidiary;
	}
});
```


# 5\. Tracking exceptions (For Developers)

Developers cannot be expected to achieve 100% compliance with the security policies, all the time. As such, security teams should allow for developers to formally request exceptions. Exceptions are expected but they must be justified by the developer who requests it. A simple example of a justification is "it took too long for me to research the failed rule".


## 5.1 Tracking Exceptions with an Excel Template

For each project, an ESLint Exceptions excel template will be privately shared by the security lead to the relevant developers. This shared excel can be used by developers and security team members to record exceptions. Once recorded, the exception's ID can be forwarded via email to the security lead to approve or deny the exception.

Security exceptions can be a big vulnerability, especially if they are left untracked. For each justified exception, if possible, the security lead should offer a temporary resolution, as well as a plan for a permanent resolution. These exceptions must be reviewed by the security team on a regular basis.
