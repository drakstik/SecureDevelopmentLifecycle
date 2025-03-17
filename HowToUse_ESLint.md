ESLint can be used as an extension or in the command line. This describes how to do it in the command line.

`npm init @eslint/config@latest` - This command initializes ESLint files, including **eslint.config.mjs** and **.eslintrc.js**. The user must then answer some questions that will help create these config files and download relevant dependencies. Once this setup is complete, you can simply install and enable ESLint in VSCode and it should automatically refer to those configuration files.

`npx eslint "**/*.ts"` - This runs ESLint on all available files that end with .ts
`npx eslint "**/*.js"` - This runs ESLint on all available files that end with .js

## How to use ESLint's publicly available plugins and configs

**References:** https://github.com/dustinspecker/awesome-eslint

**Step 1:** Download your Node project's codebase. Follow normal installation process for developers.
**Step 2:** Initialize eslint using `npm init @eslint/config@latest`. You will answer some questions, download some dependencies and create an eslint.config.mjs for your project.
**Step 3:** Install the necessary security dependencies, `npm install --save-dev  eslint-plugin-no-secrets eslint-plugin-security eslint-plugin-xss eslint-config-scanjs eslint-plugin-no-unsanitized`

List of Security Modules:
1. eslint-plugin-no-secrets 
2. @types/eslint-plugin-security 
3. eslint-plugin-no-unsanitized  
5. eslint-config-scanjs
6. @microsoft/eslint-plugin-sdl
**Step 4:** Modify the eslint.config.mjs file, so it can include all the security plugins we've just installed.

Copy/paste this into the eslint.config.mjs file:
`
import eslint from "@eslint/js";
import tseslint from "typescript-eslint";
// Importing security plugins
import noSecrets from "eslint-plugin-no-secrets";
import pluginSecurity from "eslint-plugin-security";
import noUnsanitizedPlugin from "eslint-plugin-no-unsanitized";
// import pluginMicrosoftSdl from '@microsoft/eslint-plugin-sdl';

export default tseslint.config(
	eslint.configs.recommended,
	tseslint.configs.recommended,
	pluginSecurity.configs.recommended,
	noUnsanitizedPlugin.configs.recommended,
	// pluginMicrosoftSdl.configs.recommended,
	{
		files: ["/*.ts"],
		plugins: {
			sec: pluginSecurity,
			"no-secrets": noSecrets,
			noUnsanitized: noUnsanitizedPlugin,
		},
		rules: {
			"no-secrets/no-secrets": "error",
			"no-unsanitized/method": "error",
			"no-unsanitized/property": "error",
			"no-eval": "error",
			"@microsoft/sdl/no-inner-html": "error",
		},
	}
);
`
