## Add custom fonts

Holy Tip: These days you need not serve multiple font files as [variable fonts](https://web.dev/variable-fonts/) are [widely supported](https://caniuse.com/variable-fonts) by using just one file. Moreover, the newer font format `woff2` has a better compression ratio and is again [widely supported](https://caniuse.com/?search=woff2).

1. Put variable font file (`.woff2`) in `/public/fonts/` folder

2. Add it to `_document.tsx` file so that it's fetched for all pages:

```tsx
export default class MyDocument extends Document {
  render(): JSX.Element {
    return (
      <Html>
        <Head>
          <link
            rel="preload"
            href="/fonts/inter-var-latin.woff2"
            as="font"
            type="font/woff2"
            crossOrigin="anonymous"
          />
        </Head>
        ...
```

3. Mention in global CSS file so that it's applied to all pages:

```css
@font-face {
  font-family: "Inter";
  font-style: normal;
  font-weight: 100 900;
  font-display: optional;
  src: url(/fonts/inter-var-latin.woff2) format("woff2");
}
```

4. Tell browser to cache this font file for a long time (~1yr) to avoid unnecessary re-downloads for subsequent site visits.  
   Add headers to `next.config.json`:

```js
module.exports = {
  async headers() {
    return [
      {
        source: "/fonts/inter-var-latin.woff2",
        headers: [
          {
            key: "Cache-Control",
            value: "public, max-age=31536000, immutable",
          },
        ],
      },
    ];
  },
};
```

## Add Tailwind CSS to Next.js project

https://tailwindcss.com/docs/guides/nextjs

1. Install VSCode extension: [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)

2. Run: `npm i -D tailwindcss@latest postcss@latest autoprefixer@latest`

3. Run:`npx tailwindcss init -p` (It'll generate `tailwind.config.js` and `postcss.config.js` files)

4. Paste below to `tailwind.config.js`:

```js
module.exports = {
  future: {
    removeDeprecatedGapUtilities: true,
  },
  mode: "jit",
  purge: ["./pages/**/*.{js,ts,jsx,tsx}", "./components/**/*.{js,ts,jsx,tsx}"], // remove unused styles in production
  darkMode: false, // or 'media' or 'class'
  theme: {
    extend: {},
  },
  variants: {
    extend: {},
  },
  plugins: [],
};
```

5. Make sure `postcss.config.js` is also generated with below code:

```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

6. Paste below to `styles\globals.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

7. Import style in `pages\_app.tsx` if not done already

```tsx
import { AppProps } from "next/app";
import "../styles/globals.css";

function MyApp({ Component, pageProps }: AppProps): JSX.Element {
  return <Component {...pageProps} />;
}

export default MyApp;
```

- To add custom CSS styles, see:
  - https://tailwindcss.com/docs/adding-base-styles ,
  - https://tailwindcss.com/docs/adding-new-utilities,
  - https://tailwindcss.com/docs/extracting-components

You can see [repo changes](https://github.com/GorvGoyl/nextjs-template/commit/de5bf82d6d2257ea4bfdaeb8ddd3a37b406490ad) at this point.

## Add SASS/SCSS support to Next.js project

1. Run: `npm i sass`

2. Rename all `*.css` files to `*.scss`

3. Also, update this change in `import` statements i.e. `import "../styles/globals.scss"` , `import styles from "../styles/Home.module.scss";`

That's all. Start writing css styles in scss syntax.