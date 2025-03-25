# Contents
- [Introduction](#introduction)
- [How to run (for Developers)](#how-to-run-for-developers)
- [How to resolve dependency vulnerabilities (for Developers)](#how-to-resolve-dependency-vulnerabilities-for-developers)
- [How to raise exceptions (for Developers)](#how-to-raise-exceptions-for-developers)

# Introduction
```npm audit``` or ```pnpm audit``` is a command that can be used to analyze your project's dependencies for any known vulnerabilities.

This command is particularly useful when using a Third-Party's open-source software (OSS) in your project. The command will connect to a vulnerabilities tracking database and look for any known vulnerabilities in the packages found in your package.json file.

# How to run (for Developers)
Make sure you have installed your project and simply run the ```npm audit``` or ```pnpm audit``` command to receive a report on the security of your dependencies.

# How to resolve dependency vulnerabilities (for Developers)
If you find reported vulnerabilities in your project's dependencies, then you must update the dependency to a safe version, or replace the dependency to a safer dependency that achieves the same thing or remove the dependency and find a safer way to achieve your goals.

# How to raise exceptions (for Developers)
When in doubt or if there are no solutions in sight, and you find yourself spending more time than necessary to fix a found vulnerability, please raise an exception.

A security exceptions tracking document for ```npm audit``` will be shared with all developers.

Once an exception is raised, the security team will track the exception until resolution.
