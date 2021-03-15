# Unit Testing for React

[Jest](https://jestjs.io/) is JavaScript testing framework created by Facebook.

- Provides **test **coverage****
- Comes with a command-line-interface
- Can mock functions and methods

[React Testing Library](https://testing-library.com/docs/) is a library for testing React applications.

- Focuses on testing the application based on **how the user would see it**
- **Avoids testing the internal states and methods** of the components
- **Avoids testing lifecycles**

---

## 1. Getting Started

### Install the following packages:

1. [testing-library/react](https://github.com/testing-library/react-testing-library): React DOM testing utilities

2. [testing-library/jest-dom](https://github.com/testing-library/jest-dom): A set of custom Jest matchers to aid with testing
   - i.e. `toBeVisible()`,
   - `toBeInTheDocument()`

3. [babel-jest](https://www.npmjs.com/package/babel-jest): Transform our test code with Babel

```bash
npm install --save-dev @testing-library/react testing-library/jest-dom babel-jest
```

### Configuring Jest

1. Create a file called `setupTests.js` at the project root. 

```javascript
import "@testing-library/jest-dom/extend-expect";
```

The code inside `setupTests.js` is used globally.

1. Create a file called `.babelrc` at the project root.

```json
{
  "presets": ["next/babel"]
}
```

[next/babel](https://nextjs.org/docs/advanced-features/customizing-babel-config): Helps compile React applications and server-side code

3. Create a `jest.config.js` file in the root directory
   
```javascript
module.exports = {
  testPathIgnorePatterns: ["<rootDir>/.next/", "<rootDir>/node_modules/"],
  setupFilesAfterEnv: ["<rootDir>/setupTests.js"],
  transform: {
    "^.+\\.(js|jsx|ts|tsx)$": "<rootDir>/node_modules/babel-jest",
    "\\.(css|less|scss|sass)$": "identity-obj-proxy"
  }
};
```

---
