## Add ESLint to Next.js Typescript project

> To add ESLint in Next.js **Javascript** project. see https://stackoverflow.com/a/61160352/3073272

There are few steps required for adding proper support of ESLint to TS project:

1. First step to install few ESLint packages is already done at time of creating Next.js project i.e. `npx create-next-app`, it installed `eslint` and `eslint-config-next `.

2. Install TS related ESLint packages:
   `npm i -D @typescript-eslint/parser @typescript-eslint/eslint-plugin @next/eslint-plugin-next`

3. Install VSCode ESLint extension: [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

> Explanations:
>
> `eslint` is the main ESLint package.
> `@typescript-eslint/parser` will allow ESLint to parse TypeScript files.
> `@typescript-eslint/eslint-plugin` will add TypeScript specific lint rules.
> `eslint-config-next` adds multiple useful plugins (next, react, and react-hooks) ([full list of plugins](https://github.com/vercel/next.js/blob/canary/packages/eslint-config-next/package.json))
> `@next/eslint-plugin-next` to catch common issues and problems in a Next.js application.

4. Create/edit file `.eslintrc.json` at root of project and replace with below content:
   (to mention plugins, configs, rules for linting)

```json
{
  "root": true,
  "env": {
    "browser": true,
    "es6": true,
    "node": true
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  },
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "./tsconfig.json", // tells parser relative path of tsconfig.json
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": 12,
    "sourceType": "module"
  },

  // all plugins (eslint-plugin-xxx) go here:
  "plugins": [
    "@typescript-eslint",
    "@next/eslint-plugin-next" // https://github.com/vercel/next.js/blob/canary/packages/eslint-plugin-next/lib/index.js
  ],

  // all configs (eslint-config-xxx) go here:
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/recommended-requiring-type-checking", // contains rules that specifically require type information
    "plugin:@next/next/recommended",
    "next", // https://github.com/vercel/next.js/blob/canary/packages/eslint-config-next/package.json
    "next/core-web-vitals"
  ],
  "rules": {
    // ...add rules which you'd like to disable
  }
}
```

5. Create file `.eslintignore` at root of project:
   (It tells ESLint to ignore files when linting)

```
# don't ever lint node_modules
**/node_modules/*
# don't lint build output (make sure it's set to your correct build folder name)
dist
# don't lint nyc coverage output
coverage
**/.next/*
*.json
*.lock
*.css
*.scss
**/out/*
next-env.d.ts
# next.config.js
```

6. Include support for `*.js` files in `tsconfig.json`:

```json
{
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", "**/*.js"],
  "exclude": ["node_modules", ".next", ".vercel", "out"]
}
```

7. Restart VSCode to activate ESLint.

### Test Linting (ESLint)

VSCode will show warnings and errors in files which have linting issues.

![](https://gourav.io/blog/nextjs-cheatsheet/Untitled--77660144.png)

![](https://gourav.io/blog/nextjs-cheatsheet/Untitled---1702509233.png)

We can also explicitly check for these issues by running a command.

1. Add below to `package.json` scripts if it's not added already:

   ```json
   {
     "scripts": {
       "lint": "next lint"
     }
   }
   ```

2. Run `npm run lint` and you should see some errors & warnings.

Fix lint warnings & errors:

Modify `_app.tsx` according to it:

```tsx
import { AppProps } from "next/app";

import "../styles/globals.css";

function MyApp({ Component, pageProps }: AppProps): JSX.Element {
  return <Component {...pageProps} />;
}

export default MyApp;
```

In `index.tsx`, add return type as `JSX.Element` to `Home` function:

```
export default function Home(): JSX.Element {
```

In `hello.ts`:

```ts
/* eslint-disable import/no-anonymous-default-export */
import type { NextApiRequest, NextApiResponse } from "next";

type Data = {
  name: string;
};
export default (req: NextApiRequest, res: NextApiResponse<Data>): void => {
  res.status(200).json({ name: "John Doe" });
};
```

Run `npm run lint` to test again. There shouldn't be any issues now.

### Disable or suppress linting errors and warnings

- To suppress issue for a particular line basis: `ctrl+.` and select disable for this line. (`ctrl+.` is a VSCode shortcut)

- To suppress issue for entire file: `ctrl+.` and select disable for entire file

- Disable rule for entire project

  - Add that rule to `.eslintrc.json` .

    Options for any specific rule:

    "off": to completely shut this rule.

    "warn": rule violation will show as warning.

    "on": rule violation will show it as error.

    `.eslintrc.json`:

    ```js
    {
    rules: {
          "jsx-a11y/anchor-is-valid": "off", // disable this rule
      },
    }
    ```

`package.json` at this point:

```json
{
  "name": "nextjs-template",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "tsc": "tsc"
  },
  "dependencies": {
    "next": "11.1.2",
    "react": "17.0.2",
    "react-dom": "17.0.2"
  },
  "devDependencies": {
    "@next/eslint-plugin-next": "^11.1.2",
    "@types/node": "^16.10.3",
    "@types/react": "^17.0.27",
    "@typescript-eslint/eslint-plugin": "^4.33.0",
    "eslint": "^7.23.0",
    "eslint-config-next": "11.1.2",
    "typescript": "^4.4.3"
  }
}
```

- You can see [repo changes](https://github.com/GorvGoyl/nextjs-template/commit/c2bae33419aa7bfd67d59339fbbb1209847c4520) at this point.

## Add support for Prettier in Next.js project with ESLint

Prettier is an opinionated code formatter with support for many popular languages (js, ts, html, css, etc.). So, whenever you hit `ctrl+s`, it'll beautify/format the file.

1. Install VSCode extension: [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

(that's it! do not install any other prettier related eslint plugins or extensions as those make the whole project slow. Let prettier do its job independently. [read more](https://prettier.io/docs/en/comparison.html)

2. In VSCode settings (`ctrl+,`):

- Set `Default Formatter` to `Prettier - code formatter`

- Enable `Format on Save`

2. You may need to disable any formatting related eslint rules as Prettier will handle formatting from now on. You can disable those rules in `.eslintrc.json` or use [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier#installation).
3. Restart VSCode and try to save any unformatted file.

