# @kodingdotninja/style-guide

![version](https://badgen.net/npm/v/@kodingdotninja/style-guide)
![downloads](https://badgen.net/npm/dt/@kodingdotninja/style-guide)
![license](https://badgen.net/npm/license/@kodingdotninja/style-guide)

ESLint and Prettier style guide for various Koding Ninja projects, which includes configs for popular linting and styling tools. Heavily based on [Vercel's style guide](https://github.com/vercel/style-guide).

The following configs are available, and are designed to be used together.

- [Prettier](#prettier)
- [ESLint](#eslint)
- [TypeScript](#typescript)

---

## Installing

Starting with v3.0.0, using this package requires installing its direct peer dependencies: `eslint`, `prettier`, and `typescript`.

```sh
# using npm
npm install --save-dev @kodingdotninja/style-guide eslint prettier typescript

# using yarn
yarn add --dev @kodingdotninja/style-guide eslint prettier typescript

# using pnpm
pnpm install --save-dev @kodingdotninja/style-guide eslint prettier typescript
```

Some of our ESLint configs require peer dependencies. We'll note those
alongside the available configs in the [ESLint](#eslint) section.

If you're not working with frontend related projects (React, Next.js, TailwindCSS), you can install [`@kodingdotninja/style-guide-core`](https://npm.im/@kodingdotninja/style-guide-core) which [does not include packages listed here](./scripts/trim-core.js).

## Prettier

> Note: Prettier is a peer-dependency of this package, and should be installed
> at the root of your project.
>
> See: https://prettier.io/docs/en/install.html

To use the shared Prettier config, set the following in `package.json`.

```json
{
  "prettier": "@kodingdotninja/style-guide/prettier"
}
```

## ESLint

> Note: ESLint is a peer-dependency of this package, and should be installed
> at the root of your project.
>
> See: https://eslint.org/docs/user-guide/getting-started#installation-and-usage

This ESLint config is designed to be composable.

The following base configs are available. You can use one or both of these
configs, but they should always be first in `extends`:

- `@kodingdotninja/style-guide/eslint/browser`
- `@kodingdotninja/style-guide/eslint/node`

Note that you can scope configs, so that configs only target specific files.
For more information, see: [Scoped configuration with `overrides`](#scoped-configuration-with-overrides).

The following additional configs are available:

- `@kodingdotninja/style-guide/eslint/jest` (requires [`eslint-plugin-jest`](https://npm.im/eslint-plugin-jest) and [`eslint-plugin-testing-library`](https://npm.im/eslint-plugin-testing-library) to be installed)
- `@kodingdotninja/style-guide/eslint/next` (requires `@next/eslint-plugin-next` to be installed at the same version as `next`)
- `@kodingdotninja/style-guide/eslint/playwright-test` (requires [`eslint-plugin-playwright`](https://npm.im/eslint-plugin-playwright) to be installed)
- `@kodingdotninja/style-guide/eslint/react`
- `@kodingdotninja/style-guide/eslint/tailwindcss` (requires [`tailwindcss`](https://npm.im/tailwindcss) to be installed)
- `@kodingdotninja/style-guide/eslint/typescript` (requires [`typescript`](https://npm.im/typescript) to be installed and [additional configuration](#configuring-eslint-for-typescript))

> You'll need to use `require.resolve` to provide ESLint with absolute paths,
> due to an issue around ESLint config resolution (see
> [eslint/eslint#9188](https://github.com/eslint/eslint/issues/9188)).

For example, use the shared ESLint config(s) in a Next.js project, set the
following in `.eslintrc.js`.

```js
module.exports = {
  extends: [
    require.resolve("@kodingdotninja/style-guide/eslint/browser"),
    require.resolve("@kodingdotninja/style-guide/eslint/react"),
    require.resolve("@kodingdotninja/style-guide/eslint/next"),
  ],
};
```

### Configuring ESLint for TypeScript

Some of the rules enabled in the TypeScript config require additional type
information, you'll need to provide the path to your `tsconfig.json`.

For more information, see: https://typescript-eslint.io/docs/linting/type-linting

```js
const { getTsconfigPath } = require("@kodingdotninja/style-guide/utils/tsconfig");

const tsconfigPath = getTsconfigPath();

module.exports = {
  extends: [
    require.resolve("@kodingdotninja/style-guide/eslint/node"),
    require.resolve("@kodingdotninja/style-guide/eslint/typescript"),
  ],
  parserOptions: {
    project: tsconfigPath,
  },
  settings: {
    "import/resolver": {
      typescript: {
        project: tsconfigPath,
      },
    },
  },
  root: true,
};
```

### Configuring custom components for `jsx-a11y`

It's common practice for React apps to have shared components like `Button`,
which wrap native elements. You can pass this information along to `jsx-a11y`
via the `components` setting.

The below list is not exhaustive.

```js
module.exports = {
  root: true,
  extends: [require.resolve("@vercel/style-guide/eslint/react")],
  settings: {
    "jsx-a11y": {
      components: {
        Article: "article",
        Button: "button",
        Image: "img",
        Input: "input",
        Link: "a",
        Video: "video",
      },
    },
  },
};
```

### Scoped configuration with `overrides`

ESLint configs can be scoped to include/exclude specific paths. This ensures
that rules don't "leak" to places where those rules don't apply.

In this example, Jest rules are only being applied to files matching Jest's
default test match pattern.

```js
module.exports = {
  extends: [require.resolve("@kodingdotninja/style-guide/eslint/node")],
  overrides: [
    {
      files: ["**/__tests__/**/*.[jt]s?(x)", "**/?(*.)+(spec|test).[jt]s?(x)"],
      extends: [require.resolve("@kodingdotninja/style-guide/eslint/jest")],
    },
  ],
};
```

#### A note on file extensions

By default, all TypeScript rules are scoped to files ending with `.ts` and
`.tsx`.

However, when using overrides, file extensions must be included or ESLint will
only include `.js` files.

```js
module.exports = {
  overrides: [
    {
      files: ["directory/**/*.[jt]s?(x)"],
      rules: {
        "my-rule": ["off"],
      },
    },
  ],
};
```

## TypeScript

To use the shared TypeScript config, set the following in `tsconfig.json`.

```json
{
  "extends": "@kodingdotninja/style-guide"
}
```

The following optional configs are available:

- `@kodingdotninja/style-guide/tsconfig` (same as `@kodingdotninja/style-guide`)
- `@kodingdotninja/style-guide/tsconfig/next` (for Next.js projects)

## Acknowledgements

- https://github.com/vercel/style-guide

## License

[Mozilla Public License Version 2.0](./LICENSE.txt)
