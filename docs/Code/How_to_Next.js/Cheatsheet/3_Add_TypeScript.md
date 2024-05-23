> typescript version at the time of writing this article: 4.4

## Add Typescript when creating a new Next.js project

- Just run `npx create-next-app@latest --ts`

## Add Typescript to existing Next.js project

1. Run `npm i -D typescript @types/react @types/node`.

2. Create empty `tsconfig.json` file at root of project.

3. Run `npm run dev` and it'll fill `tsconfig.json`.

4. Now, Change Javascript files extension to Typescript extension.
   `*.js` files which have react related code (components etc) should be converted to `*.tsx` and rest of `*.js` files should be converted to `*.ts`

```
pages\api\hello.js -> pages\api\hello.ts
pages\_app.js -> pages\_app.tsx
pages\index.js -> pages\index.tsx

```

You can see [repo changes](https://github.com/GorvGoyl/nextjs-template/commit/a28ca0f5329dcafbe149c661710b734823d191b0) at this point.

Test it locally:

- Run `npm run dev` and visit `http://localhost:3000/` (page should look same as before)

- Also, test the api route for file (pages\api\hello.ts)

Visit `http://localhost:3000/api/hello`
output should be:

```
{"name":"John Doe"}
```

## Check for any Typescript errors

1. You can either build the project: `npm run build` (it'll show TS errors)
2. Or create a separate command for it:
   add `"tsc": "tsc"` to `package.json` scripts section

```json
{
  "scripts": {
    "tsc": "tsc"
  }
}
```

- Run `npm run tsc`

## Strict Type checking (Optional)

Strict type checking is useful if you want to further reduce the scope of errors.

- To enable:
  - in `tsconfig.json`
    change `"strict": false` to `"strict": true`
- Now, if you run `npm run build` or `npm run tsc` it'll throw below errors like `XXX implicitly has an 'any' type.`

```
pages/_app.tsx:3:18 - error TS7031: Binding element 'Component' implicitly has an 'any' type.

3 function MyApp({ Component, pageProps }) {
                   ~~~~~~~~~

pages/_app.tsx:3:29 - error TS7031: Binding element 'pageProps' implicitly has an 'any' type.

3 function MyApp({ Component, pageProps }) {
                              ~~~~~~~~~

pages/api/hello.ts:3:33 - error TS7006: Parameter 'req' implicitly has an 'any' type.

3 export default function handler(req, res) {
                                  ~~~

pages/api/hello.ts:3:38 - error TS7006: Parameter 'res' implicitly has an 'any' type.

3 export default function handler(req, res) {
                                       ~~~


Found 4 errors.
```

You can either define the `type` for those errors or suppress these errors by enabling use of `any`.

- To allow use of `any`
  open `tsconfig.json` and add `"noImplicitAny": false` to `compilerOptions`

```json
{
  "compilerOptions": {
    "noImplicitAny": false //set 'true' to throw error for 'any'
  }
}
```

- Run `npm run tsc` and now it shouldn't give you any error.

At this point, complete `tsconfig.json` should look like this:

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true, // strict type checking is enabled
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "noImplicitAny": false, // set 'true' to throw error for 'any'
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve"
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

At this point, complete `package.json` should look like this:

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
    "@types/node": "^16.10.3",
    "@types/react": "^17.0.27",
    "eslint": "8.0.0",
    "eslint-config-next": "11.1.2",
    "typescript": "^4.4.3"
  }
}
```

- You can also checkout the [sample repository](https://github.com/GorvGoyl/nextjs-template/) for Next.js.