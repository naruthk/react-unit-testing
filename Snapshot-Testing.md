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

2. Run the test. Jest will create a snapshot file with the following output:

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


## Inline Snapshots

The expected output is written directly into the code. So you don't need to refer to an external snapshot file for debugging.

This is done via the method: `toMatchInlineSnapshot()`.

```javascript
it('renders correctly', () => {
  const tree = renderer
    .create(<Link page="https://prettier.io">Prettier</Link>)
    .toJSON();
  expect(tree).toMatchInlineSnapshot(`
<a
  className="normal"
  href="https://prettier.io"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}
>
  Prettier
</a>
`);
});
```

## Updating Snapshots

Only do this if the changes in the UI is expected. 

```bash
jest --updateSnapshot
```

Can be shortened as

```bash
jest --u
```

---

Resources:

- https://jestjs.io/blog/2020/01/21/jest-25
- https://jestjs.io/docs/snapshot-testing
