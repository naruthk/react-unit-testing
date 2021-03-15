# Mocks

Mock functions allows you to
- **control inputs and outputs of a function**
- make sure that every time the mocked function is called, it will **always return the expected mock value**
- erases the actual behavior of objects/modules/libraries to make it easier to test our applications

---

## Various Types of Mocking

### 1. [Mock Functions](https://jestjs.io/docs/mock-functions)

`jest.fn(() => { ... something here... })`

- Replaces the content of the function with your mock content

```javascript
export const randomSoccerPlayer = () => {
  const players = ['Messi', 'Salah', 'De Bryune', 'Ronaldo'];
  return players[Math.floor(Math.random() * players.length)];
};
```

To mock the `randomSoccerPlayer` function, we can do the following:

```javascript
// randomSoccerPlayer.test.js

jest.mock('./randomSoccerPlayer', () => {
  randomSoccerPlayer: () => 'Messi'
});
```

Now every time that we use the `randSoccerPlayer` function, it will always output 'Messi'. This makes it easy to set the expected values when testing.

### 2. Mock Modules & API Calls

Suppose there is this class:

```javascript
// channels.js
import axios from 'axios';

function getListing() {
  return axios.get('/channels_list.json').then(resp => resp.data);
}

export { getListing };
```

**Then for our test, we can do the following:**

```javascript
// channels.test.js
import axios from 'axios';
import { getListing } from "./channels.js";

jest.mock('axios'); // mock the module

test('should fetch channels', () => {
  const channels = [{ id: '11020202' }, { id: '1303030' }];
  const response = { data: channels };

  // https://jestjs.io/docs/mock-function-api#mockfnmockimplementationfn
  axios.get.mockImplementation(() => Promise.resolve(resolve))

  expect(getListing).then(data => expect(data).toEqual(channels));
});
```

**We can also mock a `Promise.reject()`** to test for error cases. This can be very helpful if you want to make sure that the Error UI is rendered as expected.

```javascript
test('should fetch channels', () => {
  ...
  const response = {
    status: "ERROR",
    statusCode: 401
  };

    // https://jestjs.io/docs/mock-function-api#mockfnmockimplementationfn
  axios.get.mockImplementation(() => Promise.reject(response));

  expect(getListing).then(data => expect(data).toEqual(response));
  ...
});
```

### Mock Methods via `jest.spyOn()`

[`jest.spyOn`](https://jestjs.io/docs/jest-object): This creates a mock function just like `jest.fn` but it also tracks the function's methods.

```javascript
const myObject = {
  doSomething() {
    console.log("Hello");
  }
};

test("spyOn", () => {
  const functionBeingSpyOn = jest.spyOn(myObject, "doSomething");
  myObject.doSomething();
  
  expect(functionBeingSpyOn).toBeCalled();
});
```

---

## Additional Readings

It's not always that mocking is useful. Eric Elliott discusses this in the article [Mocking is a code smell](https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a).

---

**Sources:**

- https://jestjs.io/docs/mock-functions
- https://jestjs.io/docs/manual-mocks
- https://jestjs.io/docs/jest-object#jestrequireactualmodulename
- https://pawelgrzybek.com/mocking-functions-and-modules-with-jest/
- https://medium.com/codeclan/mocking-es-and-commonjs-modules-with-jest-mock-37bbb552da43
  