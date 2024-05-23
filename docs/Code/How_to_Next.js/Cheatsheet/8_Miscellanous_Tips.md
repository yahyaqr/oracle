## Open localhost site whenever you start Next.js server

Open https://localhost:3000 site in browser whenever you run `npm run start`. Add below to `package.json` file:

```json
"scripts": {
    "start": "start http://localhost:3000 & next dev", // for MAC, use `open` instead of `start`
    }
```

## Check if site is running in production or dev server

Many a times we need to enable some code only on production or dev server. You can check if site is running on production environment or dev server:

```js
const isProd = process.env.NODE_ENV === "production";

if (isProd) {
  console.log("it's prod server bois");
}
```

## Use absolute path like @/components instead of relative path

You can use `@/` symbol to access path related to root of project instead of relative to current file path.
For example, you can use `import Header from "@/components/Header";` no matter how deeply nested your file is. No more `../../../components/Header` etc.

1. Add below to `tsconfig.json`:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      // use @/ to access path related to root of project
      "@/*": ["./*"]
    }
  }
}
```

2. Add below to `.eslintrc.json`:

```json
{
  "settings": {
    // to support @/ path
    "import/resolver": {
      "typescript": {} // this loads <rootdir>/tsconfig.json to eslint
    },
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
  "extends": ["plugin:import/typescript"],
};
```

3. Restart VSCode.

- Now import some code using `@/` in a file e.g. `import Header from "@/components/Header";`
- You can test for any import errors by running `npm run lint`

## Redirect a page to an external URL

Useful for creating short memorable links to your projects, resume, etc.

When user visits _https://gourav.io/chrome_ then redirect to _https://chrome.google.com/webstore/detail/notion-boost/eciepnnimnjaojlkcpdpcgbfkpcagahd_.

Create `next.config.js` at root of project and paste:

```js
module.exports = {
  async redirects() {
    return [
      {
        source: "/chrome",
        destination:
          "https://chrome.google.com/webstore/detail/notion-boost/eciepnnimnjaojlkcpdpcgbfkpcagahd",
        permanent: true,
      },
    ];
  },
};
```
