# Snapshot Testing

[Snapshot Testing](https://jestjs.io/docs/snapshot-testing)
- makes sure that the user interface does not change unexpectedly
- **should be committed**
- **should be reviewed as part of code review process**

---

## Matching Snapshots

This is the general use case for snapshot testing.

1. Write the test file

```javascript
describe('Rendering:', () => {
 it('should render the component with default props', () => {
  expect(tree).toMatchSnapshot();
    const { container } = render(
      <Link page="http://www.facebook.com">Facebook</Link>
    );
    expect(container).toMatchSnapshot();
 });
});
```

2. Run the test. Jest will then create a snapshot file with the following output:

```snap
exports[`Link renders correctly 1`] = `
<a
  className="normal"
  href="http://www.facebook.com"
  onMouseEnter={[Function bound _onMouseEnter]}
  onMouseLeave={[Function bound _onMouseLeave]}>
  Facebook
</a>
`;
```

The snapshot file is stored inside the `__snapshots__` folder next to the test file.

## Inline Snapshots

The expected output is written directly into the code. So you don't need to refer to an external snapshot file for debugging.

This is done via the method: `toMatchInlineSnapshot()`.

```javascript
it('renders correctly', () => {
  const tree = renderer
    .create(<Link page="https://google.com">Google</Link>)
    .toJSON();
  expect(tree).toMatchInlineSnapshot(`
<a
  className="normal"
  href="https://google.com"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}
>
  Google
</a>
`);
});
```

## Updating Snapshots

Let's say we change the page props from `https://google.com` to `https://tesla.com`. Snapshot will output an error the next time you run your test.

If the error is expected (the user interface/requirements need to be changed), then run the following command:

```bash
jest --updateSnapshot
```

The command can also be shortened into

```bash
jest --u
```

This will output an updated Snapshot that matches the current code. Remember to commit the changes.

---

Resources:

- https://jestjs.io/blog/2020/01/21/jest-25
- https://jestjs.io/docs/snapshot-testing
