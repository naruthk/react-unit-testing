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
2. [testing-library/dom](https://testing-library.com/docs/dom-testing-library/intro): Test DOM nodes 
3. [testing-library/jest-dom](https://github.com/testing-library/jest-dom): A set of custom Jest matchers to aid with testing (i.e. `toBeVisible()`, `toBeInTheDocument()`)
4. [jest](https://jestjs.io/): JavaScript testing framework
5. [babel-jest](https://www.npmjs.com/package/babel-jest): Transform our test code with Babel
6. [@babel/preset-env](https://www.npmjs.com/package/babel-preset-env): Transformation for latest JavaScript syntax without needing polyfills
7. [@babel/preset-react](https://www.npmjs.com/package/babel-preset-react): Transformation  support for React JSX syntax

```bash
npm install --save-dev @testing-library/dom @testing-library/react testing-library/jest-dom babel-jest jest
```

#### 1A: Optional

If you are using [CSS Modules](https://github.com/css-modules/css-modules) for styling, then also install the following:
- [identity-obj-proxy](https://github.com/keyz/identity-obj-proxy): Mock CSS modules (useful for snapshot testing)

```bash
npm install --save-dev identity-obj-proxy
```

## 2. Configuring Jest

1. Create a file called `jest.setup.js` at the project root.
```javascript
// jest.setup.js
import "@testing-library/jest-dom/extend-expect";
```

2. Create a file called `babel.config.js` at the project root.
```json
module.exports = {
  "presets": ["@babel/preset-react", "@babel/preset-env"]
}
```

3. Create a `jest.config.js` file in the root directory
```javascript
// jest.config.js
module.exports = {
  testPathIgnorePatterns: ["<rootDir>/.next/", "<rootDir>/node_modules/"],
  setupFilesAfterEnv: ["<rootDir>/jest.setup.js"],
  transform: {
    "^.+\\.(js|jsx|ts|tsx)$": "babel-jest",
  }
};
```

4. We also need to mock stylesheets:
- If you are using CSS modules (see section 1A above), then include the following as a property of `transform`. This will attach your classnames in the rendered output (which is really useful for snapshot testing).
  ```javascript
  // jest.config.js
  transform: {
    "\\.(css|less|scss|sass)$": "identity-obj-proxy"
  },
  ```
- On the other hand, if you use ordinary stylesheets (.scss, .css, .less), then we can simply mock them out.
  ```javascript
  // jest.config.js
  transform: {
    "\\.(css|less)$": "<rootDir>/__mocks__/styleMock.js"
  },
  ```
  ```javascript
  // __mocks__/styleMock.js
  module.exports = {};
  ```
5. Files are also unnecessary during our tests. So we also exclude them by mocking them out.
```javascript
// jest.config.js
transform: {
  "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/__mocks__/fileMock.js"
},
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

## 3. Basic Concept of Jest

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

## 4. Basic Concept of React Testing Library

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

**An example test file `CustomButtonsArea.test.js` for the component above:**

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

## 5. Running Tests

Run all test files using the following command:

```bash
npm run test
```

To run Jest in watch mode, use:

```bash
npm run jest --watchAll
```

---

## 6. Advanced Concepts
### Snapshot Testing

Refer to the [guide on Snapshot Testing](docs/Snapshot-Testing.md) to learn more.

### Mocking

Refer to the [guide on Mocks](docs/Mocks.md) to learn more.

### Test Coverage

Refer to the [guide on Test Coverage](docs/Test-Coverage.md) to learn more.

---

## 7. Tips and Tricks

Visit the [Tips and Tricks section](docs/Tips-Tricks.md) for more detail.

---

## 8. Issues You Might Encounter

Commonly found [issues](Issues.md) worth exploring.
