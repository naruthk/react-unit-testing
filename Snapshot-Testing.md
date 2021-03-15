# Snapshot Testing

[Snapshot Testing](https://jestjs.io/docs/snapshot-testing)
- makes sure that the user interface does not change unexpectedly
- **should be committed**
- **should be reviewed as part of code review process**

---

## Implementation

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

```
toMatchInlineSnapshot()
```

The expected output is written directly into the code. So you don't need to refer to an external snapshot file for debugging.

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

```bash
jest --updateSnapshot
```

Can be shortened as

```bash
jest --u
```

