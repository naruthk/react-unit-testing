# Tips and Tricks

> You may find this library helpful when it comes to linting your Jest test files: [eslint-plugin-jest](https://github.com/jest-community/eslint-plugin-jest)

---

1. Better to use pure functions instead of classes.
2. Don't try to mix logic with the rendering UI. It makes it even harder to break them down and unit test them.
3. Use the method name to describe the block name:

```javascript
describe('methodName', () => {
  it('passes', () => {
    expect(true).toEqual(true);
  });
});
```

4. Make sure all Promises are handled by using `async/await` or wrap inside `try/catch`
5. Don't use methods like `toBeTruthy` or `toBeFalsy`. The reason is because this can happen:

```javascript
expect(hasItem).toBeFalsy() // Passes!
// Passes even though hasItem === null and when hasItem === false
```

6. Also don't use `toBeDefined`: If your function returns `null`, the test will pass!
7. If you're dealing with randoms, mock it.

```javascript
beforeEach(() => {
  jest.spyOn(Math, 'random').mockReturnValue(1.5);
});
```

8. It's not always necessary to aim for 100% code coverage. It does not mean the application is bug-free even though it is fully covered by unit test!

---

**Sources:**

- https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a
- https://docs.gitlab.com/ee/development/testing_guide/frontend_testing.html