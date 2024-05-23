> Checkout free SEO course by Next.js: https://nextjs.org/learn/seo/introduction-to-seo

## Set Redirect from www.yoursite.com to yoursite.com

It means whenever someone visits www.yoursite.com it'll be redirected to yoursite.com without hurting SEO.

1. Go to domains settings inside Vercel: https://vercel.com/{username}/{project}/settings/domains

2. Add an entry `yoursite.com` if not added already.
   config should be like this:
   domain: yoursite.com
   Redirect to: No redirect possible
   Git Branch: main

3. Add a new entry `www.yoursite.com` if not added already
   config should be like this:
   domain: www.yoursite.com
   Redirect to: yoursite.com
   Status Code: 308 Permanent Redirect
   Git Branch: main

## Add SEO meta tags and favicon

Whenever you share a website link on Twitter, LinkedIn, Whatsapp, etc, you must have noticed that it generates a little preview containing a title, description and a preview image. Platform picks this info from site meta tags. Luckily, there's an npm library which makes it easy to add meta tags to next.js project.

1. Install [next-seo](https://github.com/garmeeh/next-seo#usage): `npm i next-seo`

2. Create a file `header.tsx` and paste below code:

```tsx
import { NextSeo } from "next-seo";
import { useRouter } from "next/router";

export default function Header(): JSX.Element {
  const title = "Gourav Goyal";
  const desc =
    "I'm a tech founder, digital nomad, and a fun guy to hang around with. I like to build things that others find helpful.";
  const ogImgRelativePath = "/og.png";

  const siteURL = "https://gourav.io";
  const ogImageURL = `${siteURL}${ogImgRelativePath}`;
  const pathName = useRouter().pathname;
  const pageURL = pathName === "/" ? siteURL : siteURL + pathName;
  const twitterHandle = "@GorvGoyl";
  const siteName = "Gourav.io";

  return (
    <NextSeo
      title={title}
      description={desc}
      canonical={pageURL}
      openGraph={{
        type: "website",
        locale: "en_US", //  Default is en_US
        url: pageURL,
        title,
        description: desc,
        images: [
          {
            url: ogImageURL,
            width: 1200,
            height: 630,
            alt: "Gourav.io - personal site and blog",
          },
        ],
        site_name: siteName,
      }}
      twitter={{
        handle: twitterHandle,
        site: twitterHandle,
        cardType: "summary_large_image",
      }}
      additionalMetaTags={[
        {
          property: "author",
          content: title,
        },
      ]}
      additionalLinkTags={[
        {
          rel: "icon",
          href: `${siteURL}/favicon.ico`,
        },
        // {
        //   rel: "manifest",
        //   href: "/site.manifest",
        // },
      ]}
    />
  );
}
```

3. Set proper value of `title`, `desc`, `ogImgRelativePath`, `siteURL`, etc.

4. Create favicon.ico:

   - create favicon by image, text, or emoji: https://favicon.io/
   - put `favicon.ico` inside `public/` folder.

5. Create an OG Image (preview image) of size 1200x630, name it `og.png` and put it in `public/` folder.

6. import `Header.tsx` to all of your website pages which need meta tags.

```tsx
import Header from "@/components/header";

export default function Home(): JSX.Element {
  return (
    <>
      <Header />
      ...rest of code
    </>
  );
}
```

To verify metatags:

- Locally you can verify it in dev tools -> Elements -> html/head section:

  ![](https://gourav.io/blog/nextjs-cheatsheet/Untitled--967677947.png)

- After deployment you can verify it via https://metatags.io (enter your site name in input field)

## Create dynamic sitemap

### Using custom post-build script

Up-to date sitemap will be generated every time you do a deployment (during build process) so no need to manually add/update/remove urls in sitemap.

1. Create `/scripts` folder and create new file inside it: `generate-sitemap.mjs`

2. Install required `globby` package: ` npm i -D globby`

3. Paste below to `generate-sitemap.mjs`. Replace `homeURL` with your domain.

```js
import { writeFileSync } from "fs";
import { globby } from "globby";
const homeURL = "https://yoursite.com";

(async () => {
  try {
    console.log("generating sitemap..");

    const pages = await globby([
      "pages/**/*.tsx",
      "!pages/_*.tsx",
      "!pages/api",
      "!pages/404.tsx",
    ]);

    const sitemap = `<?xml version="1.0" encoding="UTF-8"?>
        <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
            ${pages
              .map((page) => {
                const path = page
                  .replace("pages/", "/")
                  .replace(".tsx", "")
                  .replace("/index", "");
                const route = path === "/index" ? "" : path;
                const fullUrl = `${homeURL}${route}`;
                console.log(fullUrl);
                return `
                        <url>
                            <loc>${fullUrl}</loc>
                        </url>
                    `;
              })
              .join("")}
        </urlset>`;

    writeFileSync("public/sitemap.xml", sitemap);
    console.log("sitemap generated");
  } catch (e) {
    console.log(e);
    process.exit(1);
  }
})();
```

4. Ignore this file from linting as it's not typescript compatible:

   1. Create`.eslintignore` file at root of project

   2. Add below line:

   ```
   *.mjs
   ```

5. in `package.json` add below `postbuild` step. `postbuild` step will run automatically after the `build` step (due to `post`prefix).

```js
{
"scripts": {
    "build": "next build",
    "postbuild": "node ./scripts/generate-sitemap.mjs"
  },
}

```

- Now if you run `npm run build`, a new file `sitemap.xml` will be created under `./public`:

```
<?xml version="1.0" encoding="UTF-8"?>
        <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">

                        <url>
                            <loc>https://yoursite.com</loc>
                        </url>

        </urlset>
```

- Also create `/public/robots.txt` and paste below and replace mysite.com with your site. This gives permission to web crawlers (e.g. Google) to crawl and index your site and its pages.

```
User-agent: *
Sitemap: https://mysite.com/sitemap.xml
```

- Check in code and deploy. you should see sitemap by visiting: `https://mysite.com/sitemap.xml`

- Submit this source map to Google: https://search.google.com/search-console

### Using npm package `next-sitemap`

1. Install [next-sitemap](https://www.npmjs.com/package/next-sitemap):

```
npm i -D next-sitemap
```

2. Create a config file. `next-sitemap` will look for a file named `next-sitemap.js` in the `public/` by default. Using Windows, this does not work because of a naming conflict. Instead, create a file named `sitemap-generator.js` with the following configuration:

```js
module.exports = {
  siteUrl: "https://yourdomain.com",
  generateRobotsTxt: true,
  exclude: ["/en*", "/de*", "/disallowed"],
  alternateRefs: [
    {
      href: "https://yourdomain.com/en",
      hreflang: "en",
    },
    {
      href: "https://yourdomain.com/de",
      hreflang: "de",
    },
  ],
  robotsTxtOptions: {
    policies: [
      {
        userAgent: "*",
        disallow: "/disallowed",
      },
      {
        userAgent: "*",
        allow: "/",
      },
    ],
  },
};
```

3. Finally, add the `postbuild` script to `package.json`

```json
"scripts": {
	"dev": "next dev",
	"build": "next build",
	"start": "next start",
	"lint": "next lint",
	"postbuild": "next-sitemap --config sitemap-generator.js"
},
```

Notice the optional `--config sitemap-generator.js` to point to a different filename than the default `next-sitemap.js`

## Add HTML language attribute and locale

It'll convert from `<html>...</html>` to `<html lang="en-US">...</html>`.

- In `next.config.js` add:

```js
module.exports = {
  i18n: {
    locales: ["en-US"],
    defaultLocale: "en-US",
  },
};
```

## Create dynamic RSS feed

Add this to `./scripts/generate-rss.mjs`
```ts
import { Feed } from "feed";
import { writeFileSync } from "fs";
import { join } from "path";
import { getPublishedPostsFrontmatter, homeUrl } from "./helper.mjs";

const imgOutputPath = `${homeUrl}/img/blog/`;

const author = {
  name: "Gourav Goyal",
  email: "hey@gourav.io",
  link: "https://gourav.io/blog",
};

function generateRSS() {
  try {
    console.log("generating blog feed...");
    const feed = new Feed({
      title: "Gourav Goyal",
      description: "Gourav's Blog - Tech | Productivity | Life",
      id: homeUrl,
      link: homeUrl,
      language: "en",
      image: `${homeUrl}/favicon-32x32.png`,
      favicon: `${homeUrl}/favicon.ico`,
      generator: `Feed for ${homeUrl}`,
      copyright: `All rights reserved ${new Date().getFullYear()}, Gourav Goyal`,
      feedLinks: {
        rss: `${homeUrl}/feed.xml`,
        json: `${homeUrl}/feed.json`,
        atom: `${homeUrl}/atom.xml`,
      },
      author,
    });

    const postsFrontmatter = getPublishedPostsFrontmatter();

    // don't add posts to rss feed: preview posts, posts that starts with _folder
    postsFrontmatter.forEach((frontmatter) => {
      try {
        const postURL = `https://gourav.io/blog/${frontmatter.slug}`;

        const postDir = join(
          process.cwd(),
          "content",
          "blog",
          frontmatter.slug
        );

        // use default og image if no image is provided
        let ogImgFullPath = `${homeUrl}/og.png`;
        if (frontmatter.ogImage) {
          ogImgFullPath = `${postURL}/${frontmatter.ogImage}`;
        }

        feed.addItem({
          title: frontmatter.title,
          id: frontmatter.title,
          link: postURL,
          description: frontmatter.desc,
          // content: html + postText,
          author: [author],
          date: new Date(frontmatter.date),
          image: ogImgFullPath,
        });
        // feed.addCategory("Technology");
      } catch (e) {
        console.error("Error: ", e);
      }
    });

    writeFileSync("./public/feed.xml", feed.rss2());
    writeFileSync("./public/atom.xml", feed.atom1());
    writeFileSync("./public/feed.json", feed.json1());
    console.log("feed generated");
  } catch (e) {
    console.error(e);
    process.exit(1);
  }
}

generateRSS();
```