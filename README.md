# Husky Pre-commit Hook Setup for Angular Project

This repository is configured with Husky pre-commit hooks to enforce code quality standards using `lint-staged`, `Prettier`, and `ESLint` before committing changes. This setup ensures that your code is properly formatted and free of linting errors before it can be committed.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Lint-staged Configuration](#lint-staged-configuration)
- [Pre-commit Hook](#pre-commit-hook)
- [How It Works](#how-it-works)
- [Testing the Setup](#testing-the-setup)
- [Common Issues](#common-issues)

## Prerequisites

Before setting up Husky, ensure you have the following installed:

- [Node.js](https://nodejs.org/) (v14.x or higher)
- [npm](https://www.npmjs.com/) (v6.x or higher)
- [Angular CLI](https://angular.io/cli) (v12.x or higher)

## Installation

Follow these steps to install and configure Husky, `lint-staged`, `Prettier`, and `ESLint`:

1. **Install Husky, lint-staged, Prettier, and ESLint**:

   ```bash
   npm install --save-dev husky lint-staged prettier eslint
   ```

2. **Enable Git Hooks in Husky**:

   Add the following `prepare` script in your `package.json` to ensure Git hooks are installed automatically:

   ```json
   "scripts": {
     "prepare": "husky install"
   }
   ```

3. **Set Up Husky**:

   Initialize Husky in your project:

   ```bash
   npx husky-init && npm install
   ```

4. **Add the Pre-commit Hook**:

   Update the `.husky/pre-commit` file with the following content:

   ```bash
   #!/usr/bin/env sh
   . "$(dirname -- "$0")/_/husky.sh"

   # Run lint-staged to lint and format staged files
   npx lint-staged

   # Check the exit code of lint-staged
   if [ $? -ne 0 ]; then
     echo "Linting failed. Please fix the errors before committing."
     exit 1
   fi

   # Run tests only if lint-staged passed or there were no relevant files to lint
   npm run test
   ```

## Lint-staged Configuration

The `lint-staged` configuration is stored in a `.lintstagedrc.json` file in the root of the project. This configuration specifies the tasks to be run on staged files matching specific patterns.

### Example `.lintstagedrc.json`

```json
{
  "*.ts": ["prettier --write", "eslint"],
  "*.html": ["eslint", "prettier --write"],
  "*.scss": "prettier --write"
}
```

- **`.ts` files**: These files will be formatted with Prettier and then linted with ESLint.
- **`.html` files**: These files will be linted with ESLint and then formatted with Prettier.
- **`.scss` files**: These files will be formatted with Prettier.

## Pre-commit Hook

The pre-commit hook ensures that linting is applied only to staged files and that the commit is blocked if there are any linting errors. The hook script is located in the `.husky/pre-commit` file.

## How It Works

1. **Staging Changes**: When you stage changes with `git add`, `lint-staged` checks the staged files against the patterns specified in `.lintstagedrc.json`.
2. **Running Tasks**: `lint-staged` runs the configured tasks (Prettier and ESLint) on the matching files.
3. **Blocking Commits**: If any errors occur during linting, the commit is blocked, and you are prompted to fix the issues.
4. **Running Tests**: If linting passes or no relevant files are found, the pre-commit hook will run tests using the `npm run test` command.
5. **Commit Success**: If all tasks pass, the commit proceeds as usual.

## Testing the Setup

To verify that the setup is working correctly:

1. **Stage Files Without Matching Patterns**:

   - Stage files that do not match the `.lintstagedrc.json` patterns (e.g., `.json` files).
   - Attempt to commit. The commit should succeed.

2. **Stage Files with Matching Patterns**:

   - Stage files that match the patterns and introduce a linting error.
   - Attempt to commit. The commit should be blocked until the errors are fixed.

3. **Stage Correctly Formatted Files**:
   - Fix the linting errors, stage the files, and commit. The commit should succeed.

## Common Issues

### Commit Blocked When No Matching Files

If you find that commits are being blocked even when no files match the `lint-staged` patterns, ensure that your `.husky/pre-commit` script is correctly handling this scenario by checking the exit status of `lint-staged`.

### Lint-staged Errors

If `lint-staged` fails due to an error, review the output provided by `lint-staged` for details on the cause. Adjust the configuration in `.lintstagedrc.json` if necessary.

## Troubleshooting

If you encounter any issues during the commit process, such as linting errors blocking your commit, here’s an example screenshot of what you might see:

![Linting Error Example](<precommit error message.png>)

Make sure to fix any linting errors before committing your changes.

---
