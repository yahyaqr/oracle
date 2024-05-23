## Add Cloudflare Web Analytics

> It's free free, lightweight, privacy-friendly, and cookie-less analytics provided by Cloudflare.

1. Create free account at [Cloudflare web-analytics](https://www.cloudflare.com/web-analytics) and get JS Snippet.

2. Create `pages/_document.tsx` and paste below code.
   - Replace `your_token` with your token (from that JS snippet)

```tsx
import Document, { Html, Main, NextScript } from "next/document";
const isProd = process.env.NODE_ENV === "production";

export default class MyDocument extends Document {
  render(): JSX.Element {
    return (
      <Html>
        <body>
          <Main />
          <NextScript />
          {/* Cloudflare Web Analytics */}
          {isProd && (
            <>
              <script
                defer
                src="https://static.cloudflareinsights.com/beacon.min.js"
                data-cf-beacon='{"token": "your_token", "spa": true}'
              />
            </>
          )}
        </body>
      </Html>
    );
  }
}
```

Done. When you deploy your site to production. Cloudflare will track site analytics.

## Add Google Analytics

> It's free but bloated and uses cookies to track visitors.

1. Create a [Google analytics project](https://analytics.google.com/analytics/web/) and get Measurement ID.

2. In your Next.js project, create `/lib/gtag.ts` file and add your Google Measurement ID:

```ts
export const GA_ANALYTICS_MEASUREMENT_ID = "<INSERT_MEASUREMENT_ID>";

// https://developers.google.com/analytics/devguides/collection/gtagjs/pages
export const pageview = (url: URL): void => {
  window.gtag("config", GA_ANALYTICS_MEASUREMENT_ID, {
    page_path: url,
  });
};

type GTagEvent = {
  action: string;
  category: string;
  label: string;
  value: number;
};

// https://developers.google.com/analytics/devguides/collection/gtagjs/events
export const event = ({ action, category, label, value }: GTagEvent): void => {
  window.gtag("event", action, {
    event_category: category,
    event_label: label,
    value,
  });
};
```

3. Also install gtag `types`:

```
npm i -D @types/gtag.js
```

4. Create `/pages/_document.tsx`:

```tsx
import Document, { Html, Head, Main, NextScript } from "next/document";

import { GA_ANALYTICS_MEASUREMENT_ID } from "../lib/gtag";

const isProd = process.env.NODE_ENV === "production";

export default class MyDocument extends Document {
  render(): JSX.Element {
    return (
      <Html>
        <Head>
          {/* enable analytics script only for production */}
          {isProd && (
            <>
              <script
                async
                src={`https://www.googletagmanager.com/gtag/js?id=${GA_ANALYTICS_MEASUREMENT_ID}`}
              />
              <script
                // eslint-disable-next-line react/no-danger
                dangerouslySetInnerHTML={{
                  __html: `
            window.dataLayer = window.dataLayer || [];
            function gtag(){dataLayer.push(arguments);}
            gtag('js', new Date());
            gtag('config', '${GA_ANALYTICS_MEASUREMENT_ID}', {
              page_path: window.location.pathname,
            });
          `,
                }}
              />
            </>
          )}
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}
```

5. Create `/pages/_app.tsx`:

```tsx
import { AppProps } from "next/app";
import { useRouter } from "next/router";
import { useEffect } from "react";
import * as gtag from "../lib/gtag";
const isProd = process.env.NODE_ENV === "production";

const App = ({ Component, pageProps }: AppProps): JSX.Element => {
  const router = useRouter();

  useEffect(() => {
    const handleRouteChange = (url: URL) => {
      /* invoke analytics function only for production */
      if (isProd) gtag.pageview(url);
    };
    router.events.on("routeChangeComplete", handleRouteChange);
    return () => {
      router.events.off("routeChangeComplete", handleRouteChange);
    };
  }, [router.events]);
  // eslint-disable-next-line react/jsx-props-no-spreading
  return <Component {...pageProps} />;
};

export default App;
```

Done. When you deploy your site to production. Google will track site analytics.