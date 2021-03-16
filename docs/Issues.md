# Issues

## Setup

####  Cannot read property `cwd` of undefined

- Make sure that your versions of Jest and babel-jest are matched. If babel-jest is a version higher than Jest, you might be encountering this error.

- See this [issue on GitHub](https://github.com/facebook/jest/issues/7868) for more detail.

####  I'm getting `Support for the experimental syntax 'jsx' isn't currently enabled`

- Rename `.babelrc` to `babel.config.js` and use `module.exports = { ... }`
- See this [issue on Stackoverflow](https://stackoverflow.com/questions/52237855/support-for-the-experimental-syntax-classproperties-isnt-currently-enabled#comment97129674_52351194)

