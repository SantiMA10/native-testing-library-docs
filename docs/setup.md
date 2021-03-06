---
id: setup
title: Setup
sidebar_label: Setup
---

## Setting up your project

`native-testing-library` should work out of the box for simple tests. Most of the snippets you'll
find throughout the website should work without any additional configuration assuming you're using
Jest and a moderately recent version of React Native.

We do strongly encourage you to use Jest with the `react-native` preset. In addition, there may be
some additional mocks you would want to provide to allow for a smoother testing experience, (for
example mocks for `react-native-gesture-handler`).

## Custom Render

It's often useful to define a custom render method that includes things like global context
providers, data stores, etc. To make this available globally, one approach is to define a utility
file that re-exports everything from `native-testing-library`. You can replace
`native-testing-library` with this file in all your imports. See
[below](#configuring-jest-with-test-utils) for a way to make your test util file accessible without
using relative paths.

The example below sets up data providers using the [`wrapper`](api-render.md#render-options) option to
`render`.

```diff
// my-component.test.js
- import { render, fireEvent } from 'native-testing-library';
+ import { render, fireEvent } from '../test-utils';
```

```js
// test-utils.js
import { render } from 'native-testing-library';
import { ThemeProvider } from 'my-ui-lib';
import { TranslationProvider } from 'my-i18n-lib';
import defaultStrings from 'i18n/en-x-default';

const AllTheProviders = ({ children }) => {
  return (
    <ThemeProvider theme="light">
      <TranslationProvider messages={defaultStrings}>{children}</TranslationProvider>
    </ThemeProvider>
  );
};

const customRender = (ui, options) => render(ui, { wrapper: AllTheProviders, ...options });

// re-export everything
export * from 'native-testing-library';

// override render method
export { customRender as render };
```

### Configuring Jest with Test Utils

To make your custom test file accessible in your Jest test files without using relative imports
(`../../test-utils`), add the folder containing the file to the Jest `moduleDirectories` option.

This will make all the `.js` files in the test-utils directory importable without `../`.

```diff
// my-component.test.js
- import { render, fireEvent } from '../test-utils';
+ import { render, fireEvent } from 'test-utils';
```

```diff
// jest.config.js
module.exports = {
  moduleDirectories: [
    'node_modules',
+   // add the directory with the test-utils.js file, for example:
+   'utils', // a utility folder
+    __dirname, // the root directory
  ],
  // ... other options ...
}
```
