# Creating an npm Package with TypeScript

This guide will walk you through the process of creating a new npm package using TypeScript, following best practices for security, stability, and performance.

## 1. Set up the project

First, create a new directory for your project and navigate into it:

```bash
mkdir my-awesome-package
cd my-awesome-package
```

Initialize a new npm project:

```bash
npm init -y
```

## 2. Set up TypeScript

Install TypeScript and necessary dev dependencies:

```bash
npm install --save-dev typescript @types/node
```

Create a `tsconfig.json` file:

```bash
npx tsc --init
```

Edit the `tsconfig.json` file to include these recommended settings:

```json
{
  "compilerOptions": {
    "target": "es2018",
    "module": "commonjs",
    "declaration": true,
    "outDir": "./lib",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "**/__tests__/*"]
}
```

## 3. Set up the project structure

Create the following directories:

```bash
mkdir src
mkdir tests
```

## 4. Write your package code

Create your main TypeScript file in the `src` directory, e.g., `src/index.ts`:

```typescript
// src/index.ts
export function greet(name: string): string {
  return `Hello, ${name}!`;
}
```

## 5. Set up testing

Install Jest for testing:

```bash
npm install --save-dev jest ts-jest @types/jest
```

Create a Jest configuration file `jest.config.js`:

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.ts'],
};
```

Create a test file `tests/index.test.ts`:

```typescript
// tests/index.test.ts
import { greet } from '../src/index';

test('greet function', () => {
  expect(greet('World')).toBe('Hello, World!');
});
```

## 6. Update `package.json`

Update your `package.json` file with the following scripts and configuration:

```json
{
  "name": "my-awesome-package",
  "version": "1.0.0",
  "description": "An awesome npm package",
  "main": "lib/index.js",
  "types": "lib/index.d.ts",
  "files": [
    "lib/**/*"
  ],
  "scripts": {
    "build": "tsc",
    "test": "jest",
    "prepare": "npm run build",
    "prepublishOnly": "npm test && npm run lint",
    "version": "git add -A src",
    "postversion": "git push && git push --tags"
  },
  "keywords": [],
  "author": "Your Name <your.email@example.com>",
  "license": "MIT",
  "devDependencies": {
    "@types/jest": "^27.0.0",
    "@types/node": "^16.0.0",
    "jest": "^27.0.0",
    "ts-jest": "^27.0.0",
    "typescript": "^4.5.0"
  }
}
```

## 7. Set up linting (optional but recommended)

Install ESLint and related plugins:

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

Create an `.eslintrc.js` file:

```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  extends: [
    'plugin:@typescript-eslint/recommended',
  ],
  parserOptions: {
    ecmaVersion: 2018,
    sourceType: 'module',
  },
  rules: {
    // Custom rules here
  },
};
```

Add a lint script to your `package.json`:

```json
"scripts": {
  "lint": "eslint 'src/**/*.ts'"
}
```

## 8. Build the package

Run the build script:

```bash
npm run build
```

This will compile your TypeScript code into JavaScript in the `lib` directory.

## 9. Test your package

Run the test script:

```bash
npm test
```

## 10. Publish your package

Before publishing, make sure you're logged in to npm:

```bash
npm login
```

Then, publish your package:

```bash
npm publish
```

## Best Practices and Security Considerations

1. Use semantic versioning for your package versions.
2. Include a LICENSE file in your project (e.g., MIT License).
3. Add a comprehensive README.md file explaining how to install and use your package.
4. Use `npm audit` regularly to check for vulnerabilities in your dependencies.
5. Consider setting up continuous integration (CI) for automated testing and linting.
6. Use `npm pack` to review the contents of your package before publishing.
7. Consider using `npm shrinkwrap` to lock down dependency versions.
8. Use `prepublishOnly` script to ensure tests and linting pass before publishing.
9. Keep your npm token secure and don't commit it to version control.
10. Regularly update your dependencies to their latest stable versions.

By following these steps and best practices, you'll create a secure, stable, and fast npm package using TypeScript. Remember to keep your package updated and respond to any security issues promptly.