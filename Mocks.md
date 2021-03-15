# Mocks

Mock functions allows you to
- **control inputs and outputs of a function**
- make sure that every time the mocked function is called, it will **always return the expected mock value**
- erases the actual behavior of objects/modules/libraries to make it easier to test our applications

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
// users.test.js
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

**We can also mock a `Promise.reject()`** to test for error cases.

```javascript
test('should fetch channels', () => {
  ...
    // https://jestjs.io/docs/mock-function-api#mockfnmockimplementationfn
  axios.get.mockImplementation(() => Promise.reject({
    status: "ERROR",
    statusCode: 401
  }));

  expect(getListing).then(data => expect(data).toEqual(channels));
  ...
});
```

### Mock Methods via `jest.spyOn()`



---

Resources:

- https://jestjs.io/docs/mock-functions
- https://jestjs.io/docs/manual-mocks
- https://jestjs.io/docs/jest-object#jestrequireactualmodulename
- https://pawelgrzybek.com/mocking-functions-and-modules-with-jest/
- https://medium.com/codeclan/mocking-es-and-commonjs-modules-with-jest-mock-37bbb552da43
  