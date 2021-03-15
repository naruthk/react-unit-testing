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

1. Create a file called `setupTests.js` at the project root. The code inside `setupTests.js` is used globally.

```javascript
import "@testing-library/jest-dom/extend-expect";
```

2. Create a file called `.babelrc` at the project root. [next/babel](https://nextjs.org/docs/advanced-features/customizing-babel-config) helps compile React applications and server-side code

```json
{
  "presets": ["next/babel"]
}
```

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

4. Add the following to your `package.json` file

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

---

## 2. Basic Concept of Jest

This section is based on the [documentation provided on Jest's official documentation website](https://jestjs.io/docs/getting-started).

1. Create a file called `sum.js`

```javascript
function sum(a, b) {
  return a + b;
}
module.exports = sum;
```

2. Create a file called `sum.test.js`. Any files with the following extension `*.test.js` will be picked up by Jest to be run.

```javascript
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

3. Run `npm run test` to see the following output:

```bash
PASS  ./sum.test.js
✓ adds 1 + 2 to equal 3 (5ms)
```

---

## 3. Basic Concept of React Testing Library

**Consider the following React component:**

```javascript
import React from "react";
import PropTypes from "prop-types";

const CustomButton = ({ primaryButton, secondaryButton }) => (
  <div>
    {primaryButton ? (
      <input
        data-testid="custom-button-primary-button"
        className="primary-btn"
        type="button"
        value={primaryButton.value}
        onClick={primaryButton.onClick}
      />
    ) : null}
    {secondaryButton ? (
      <input
        data-testid="custom-button-secondary-button"
        className="secondary-btn"
        type="button"
        value={secondaryButton.value}
        onClick={secondaryButton.onClick}
      />
    ) : null}
  </div>
);

export default CustomButton;
```

**Here's how an example test file `CustomButton.test.js` might be written as:**

```javascript
import { render } from "@testing-library/react";
import React from "react";

import CustomButton from "./CustomButton";

describe("CustomButton:", () => {
  it("renders primary and secondary buttons", async () => {

    // https://testing-library.com/docs/react-testing-library/api#render
    const { getByDisplayValue, getAllByRole, getByTestId } = render(
      <CustomButton
        primaryButton={{
          value: "Agree",
          onClick: () => {}
        }}
        secondaryButton={{
          value: "Cancel",
          onClick: () => {}
        }}
      />
    );

    // https://testing-library.com/docs/queries/bydisplayvalue/
    expect(getByDisplayValue(/Agree/)).toBeInTheDocument();
    expect(getByDisplayValue(/Cancel/)).toBeInTheDocument();

    // https://testing-library.com/docs/queries/byrole
    expect(getAllByRole("button")).toHaveLength(2);

    // https://testing-library.com/docs/queries/bytestid
    expect(getByTestId("custom-button-primary-button")).toBeInTheDocument();
    expect(
      getByTestId("custom-button-secondary-button")
    ).toBeInTheDocument();
  });
});
```

**Note:** `getByTestId` is not something that is truly recommended. Based on the principles of Testing Library, we are testing how our applications work the way the user sees them.

`data-testid` attributes do not resemble how your software is used.

---

## 4. Advanced Concepts
### Snapshot Testing

[Snapshot Testing](https://jestjs.io/docs/snapshot-testing) makes sure that the user interface does not change unexpectedly.

```javascript
describe('Rendering:', () => {
 it('should render the component with default props', () => {
   const { container } = render(<YourComponent {...withWhateverProps} />)
   expect(container).toMatchSnapshot();
 });
});
```

### Mocking

https://jestjs.io/docs/manual-mocks

https://jestjs.io/docs/jest-object#jestrequireactualmodulename

### Firing Events (Simulations)

