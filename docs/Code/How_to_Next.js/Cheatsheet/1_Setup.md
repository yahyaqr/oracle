## Create new Next.js project

- run command:`npx create-next-app`
  - It'll install several packages including react: `react`,`react-dom` and for linting:`eslint`, `eslint-config-next `.
  - You can see [repo changes](https://github.com/GorvGoyl/nextjs-template/commit/a6f5baaa9f334319394d8336b132e33788d7e0ea) at this point.

## Run locally

1. Run command: `npm run dev`

2. Visit `http://localhost:3000`. You'd see something like below image:

![](https://gourav.io/blog/nextjs-cheatsheet/Untitled--165393072.png)

## Debug Next.js in VSCode

Useful to put breakpoints in VSCode and pause active session to inspect data.

1. Create a file named `.vscode/launch.json` at the root of your project and paste below content:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Next.js: attach",
      "port": 9229,
      "request": "attach",
      "skipFiles": ["<node_internals>/**"],
      "type": "node",
      "restart": true
    },
    {
      "name": "Next.js: debug server-side",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev"
    },
    {
      "name": "Next.js: debug client-side",
      "type": "pwa-chrome",
      "request": "launch",
      "url": "http://localhost:3000"
    },
    {
      "name": "Next.js: debug full stack",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev",
      "console": "integratedTerminal",
      "serverReadyAction": {
        "pattern": "started server on .+, url: (https?://.+)",
        "uriFormat": "%s",
        "action": "debugWithChrome"
      }
    }
  ]
}
```

2. Go to VSCode Debug panel (Ctrl+Shift+D on Windows/Linux, ⇧+⌘+D on macOS), select a launch configuration, then press F5 or select **Debug: Start Debugging** from the Command Palette to start your debugging session.

- Types of debugging sessions:

  - `Next.js: debug server-side`: It'll run `npm run dev` command and you'd be able to put breakpoints for both frontend files and api routes.
  - `Next.js: debug client-side`: you need to run `npm run dev` prior to starting this debugging session. Upon starting session it'll open a new chrome window and load your site. You can put breakpoints only in frontend code (no api routes).
  - `Next.js: debug full stack`: It'll open a new chrome window and you'll be able to put breakpoints in both frontend and api routes.
  - `Next.js: attach`: Works for both frontend and backend. Extra steps are needed to make it work:
    1. Run: `npm i cross-env`
    2. Add this to package.json `"start:debug": "cross-env NODE_OPTIONS='--inspect' next dev"`
    3. Run: `npm run start:debug`
    4. Run `Next.js: attach` session

Also, there's a [separate guide](https://nextjs.org/docs/advanced-features/debugging#debugging-with-chrome-devtools) to debug using Chrome DevTools instead.

## Run localhost in mobile

1. Run: `npm run dev`

2. Find your desktop IPv4 address. (For Windows: `ipconfig /all`, on Mac: `ipconfig getifaddr en0`)

3. Open site in mobile: http://laptop_IPv4_address:3000 (replace `laptop_IPv4_address` with actual IPv4 address)

## Disable telemetry

Next.js collects completely anonymous telemetry data about general usage. Run this command to disable it:

- `npx next telemetry disable`

## Disable emails from vercel[bot] which comes after every github commit

![](https://gourav.io/blog/nextjs-cheatsheet/Untitled---1922263919.png)

1. Create `vercel.json` at root of project and paste this:

```json
{
  "github": {
    "silent": true
  }
}
```

2. Commit and push this file to Github.