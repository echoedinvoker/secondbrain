---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Install React Query for Lazy Days

## Install React Query and React Query Dev Tools

```bash
npm i @tanstack/react-query @tanstack/react-query-devtools
#                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ why not install as dev dependency?
```
Why not install dev tool as dev dependency?

1. it will be used in development only

![dev-tool-env.png](../assets/imgs/dev-tool-env.png)

2. it is load lazily, so it will not be included in production bundle

![lazy-load.png](../assets/imgs/lazy-load.png)

3. So we can toggle it in production build

![toggle-dev.png](../assets/imgs/toggle-dev.png)


## Install ESLint Plugin of React Query

![es-rules.png](../assets/imgs/es-rules.png)

**eshaustive-deps** rule above is most important. It will check the content of `queryFn` and `queryKey` and will warn if any of them is missing in the dependency array.

```bash
~/D/g/s/t/u/b/client > npm i -D @tanstack/eslint-plugin-query
```

```cjs
// .eslintrc.cjs 

const vitest = require("eslint-plugin-vitest");

module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  settings: {
    "import/resolver": {
      node: {
        extensions: [".js", ".jsx", ".ts", ".tsx"],
        paths: ["src"],
      },
    },
  },
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react/jsx-runtime",
    "plugin:react-hooks/recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:vitest/recommended",
    "plugin:testing-library/react",
    "plugin:@tanstack/eslint-plugin-query/recommended",  // <------ add this line to enable the plugin for react-query
  ],
  ignorePatterns: ["dist", ".eslintrc.cjs"],
  parser: "@typescript-eslint/parser",
  parserOptions: { ecmaVersion: "latest", sourceType: "module" },
  settings: { react: { version: "18.2" } },
  plugins: ["react-refresh", "simple-import-sort"],
  rules: {
    "react-refresh/only-export-components": [
      "warn",
      { allowConstantExport: true },
    ],
    // we're using TypeScript here, not propTypes!
    "react/prop-types": "off",

    // obscure error that we don't need
    "react/display-name": "off",

    // to avoid "no-unused-vars" warnings in function type declarations
    "no-unused-vars": "off",
    "@typescript-eslint/no-unused-vars": "warn",

    // imports
    "import/prefer-default-export": 0,
    "import/no-anonymous-default-export": 0,

    // sort alias imports that start with `@` separately from modules that start with `@`
    "simple-import-sort/imports": [
      "warn",
      {
        groups: [["^\\u0000"], ["^@?\\w"], ["^@src", "^@shared"], ["^\\."]],
      },
    ],
    "simple-import-sort/exports": "warn",
    "sort-imports": "off",
    "import/order": "off",

    // eliminate distracting red squiggles while writing tests
    "vitest/expect-expect": "off",
  },
  // don't flag vitest globals like `describe` and `test`
  globals: {
    ...vitest.environments.env.globals,
  },
};

```
