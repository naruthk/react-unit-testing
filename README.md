# Unit Testing for React

[Jest](https://jestjs.io/) is JavaScript testing framework created by Facebook.

- Provides **test **coverage****
- Comes with a command-line-interface
- Can mock functions and methods
- Runs in a Node.js environment (not in the browser)

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

**Optional:** If you are using [CSS Modules](https://github.com/css-modules/css-modules) for styling, then also install the following:
- [identity-obj-proxy](https://github.com/keyz/identity-obj-proxy): Mock CSS modules (useful for snapshot testing)

```bash
npm install --save-dev identity-obj-proxy
```

### Configuring Jest

1. Create a file called `setupTests.js` at the project root. The code inside `setupTests.js` is used globally.

```javascript
import "@testing-library/jest-dom/extend-expect";
```

2. Create a file called `.babelrc` at the project root. [next/babel](https://nextjs.org/docs/advanced-features/customizing-babel-config) helps compile React applications and server-side code.

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
  }
};
```

4. If you are using CSS modules, then also include the following as a property of `transform`. Otherwise, skip to #5.

```javascript
  ...
  transform: {
    ...,
    "\\.(css|less|scss|sass)$": "<rootDir>/node_modules/identity-obj-proxy"
  },
  ...
```

5. For static assets (stylesheets and images), they are not really useful for testing purposes. So we mock them out via `fileMock.js` and `styleMock.js`.

```javascript
  ...
  transform: {
    ...,
    "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/__mocks__/fileMock.js",
    "\\.(css|less)$": "<rootDir>/__mocks__/styleMock.js"
  },
  ...
```

```javascript
// __mocks__/styleMock.js
module.exports = {};
```

```javascript
// __mocks__/fileMock.js
module.exports = 'test-file-stub';
```

6. Finally, add the following to `package.json` file

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

const CustomButtonsArea = ({ primaryButton, secondaryButton }) => (
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

export default CustomButtonsArea;
```

**Here's how an example test file `CustomButtonsArea.test.js` might be written as:**

```jsx
import { render } from "@testing-library/react";
import React from "react";

import CustomButtonsArea from "./CustomButtonsArea";

describe("CustomButtonsArea:", () => {
  it("renders primary and secondary buttons", async () => {

    // https://testing-library.com/docs/react-testing-library/api#render
    const { getByDisplayValue, getAllByRole, getByTestId } = render(
      <CustomButtonsArea
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

**Let's dive into each function:**

### `getByDisplayValue`

In this example, we're expecting to find the value `Sam` in the rendered DOM.

```html
// Sample HTML
<input type="text" id="lastName" value="Sam" />
```

```javascript
expect(getByDisplayValue(/Sam/)).toBeInTheDocument();
```

[See official documentation](https://testing-library.com/docs/queries/bydisplayvalue)

### `getAllByRole`

It returns multiple elements with the given role. To find and return a single value, use `getByRole`.

Based on the example component `CustomButtonsArea`:

```javascript
expect(getAllByRole("button")).toHaveLength(2); // Passes because 2 buttons are rendered (primary and secondary buttons)
```

You can also utilize some nice JavaScript functions like so:

```javascript
const buttons = getAllByRole("button");

expect(buttons).toHaveLength(2); // passes!

buttons.forEach(button => {
  expect(button).toHaveDisplayValue(); // passes!
});
```

You can look up HTML elements and their given roles [here](https://www.w3.org/TR/html-aria/#docconformance). For instance, the `a` HTML element with `href` attribute has a `link` role.

[See official documentation](https://testing-library.com/docs/queries/byrole)


### `getByTestId`



**Note:** `getByTestId` is not something that is truly recommended. Based on the principles of Testing Library, we are testing how our applications work the way the user sees them.

`data-testid` attributes do not resemble how your software is used.

[See official documentation](https://testing-library.com/docs/queries/bytestid)

---

## 4. Running Tests

Run all test files using the following command:

```bash
npm run test
```

To run Jest in watch mode, use:

```bash
npm run jest --watchAll
```

---

## 5. Advanced Concepts
### Snapshot Testing

Refer to the [guide on Snapshot Testing](docs/Snapshot-Testing.md) to learn more.

### Mocking

Refer to the [guide on Mocks](docs/Mocks.md) to learn more.

### Test Coverage

Refer to the [guide on Test Coverage](docs/Test-Coverage.md) to learn more.

---

## 6. Tips and Tricks

Visit the [Tips and Tricks section](docs/Tips-Tricks.md) for more detail.
