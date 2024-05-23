## Fetching, Caching, and Revalidating
Data fetching is a core part of any application. This page goes through how you can fetch, cache, and revalidate data in React and Next.js.

There are four ways you can fetch data:

1. On the server, with `fetch`
2. On the server, with third-party libraries
3. On the client, via a Route Handler
4. On the client, with third-party libraries

### Fetching Data on the Server with `fetch`

Next.js extends the native `fetch` Web API to allow you to configure the caching and revalidating behavior for each fetch request on the server. React extends `fetch` to automatically  memoize fetch requests while rendering a React component tree.

You can use `fetch` with `async`/`await` in Server Components, in Route Handlers, and in Server Actions.

For example:

```tsx filename="app/page.tsx" switcher
async function getData() {
  const res = await fetch('https://api.example.com/...')
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.

  if (!res.ok) {
    // This will activate the closest `error.js` Error Boundary
    throw new Error('Failed to fetch data')
  }

  return res.json()
}

export default async function Page() {
  const data = await getData()

  return <main></main>
}
```

```jsx filename="app/page.js" switcher
async function getData() {
  const res = await fetch('https://api.example.com/...')
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.

  if (!res.ok) {
    // This will activate the closest `error.js` Error Boundary
    throw new Error('Failed to fetch data')
  }

  return res.json()
}

export default async function Page() {
  const data = await getData()

  return <main></main>
}
```

> **Good to know**:
>
> - Next.js provides helpful functions you may need when fetching data in Server Components such as `cookies`api-reference/functions/cookies) and `headers`api-reference/functions/headers). These will cause the route to be dynamically rendered as they rely on request time information.
> - In Route handlers, `fetch` requests are not memoized as Route Handlers are not part of the React component tree.
> - In Server Actions, `fetch` requests are not cached (defaults `cache: no-store`).
> - To use `async`/`await` in a Server Component with TypeScript, you'll need to use TypeScript `5.1.3` or higher and `@types/react` `18.2.8` or higher.

#### Caching Data

Caching stores data so it doesn't need to be re-fetched from your data source on every request.

By default, Next.js automatically caches the returned values of `fetch` in the Data Cache on the server. This means that the data can be fetched at build time or request time, cached, and reused on each data request.

```js
// 'force-cache' is the default, and can be omitted
fetch('https://...', { cache: 'force-cache' })
```

However, there are exceptions, `fetch` requests are not cached when:

- Used inside a Server Action.
- Used inside a Route Handler that uses the `POST` method.

> **What is the Data Cache?**
>
> The Data Cache is a persistent HTTP cache. Depending on your platform, the cache can scale automatically and be shared across multiple regions.
>
> Learn more about the Data Cache.

#### Revalidating Data

Revalidation is the process of purging the Data Cache and re-fetching the latest data. This is useful when your data changes and you want to ensure you show the latest information.

Cached data can be revalidated in two ways:

- **Time-based revalidation**: Automatically revalidate data after a certain amount of time has passed. This is useful for data that changes infrequently and freshness is not as critical.
- **On-demand revalidation**: Manually revalidate data based on an event (e.g. form submission). On-demand revalidation can use a tag-based or path-based approach to revalidate groups of data at once. This is useful when you want to ensure the latest data is shown as soon as possible (e.g. when content from your headless CMS is updated).

##### Time-based Revalidation

To revalidate data at a timed interval, you can use the `next.revalidate` option of `fetch` to set the cache lifetime of a resource (in seconds).

```js
fetch('https://...', { next: { revalidate: 3600 } })
```

Alternatively, to revalidate all `fetch` requests in a route segment, you can use the Segment Config Optionsapi-reference/file-conventions/route-segment-config).

```jsx filename="layout.js | page.js"
export const revalidate = 3600 // revalidate at most every hour
```

If you have multiple fetch requests in a statically rendered route, and each has a different revalidation frequency. The lowest time will be used for all requests. For dynamically rendered routes, each `fetch` request will be revalidated independently.

Learn more about time-based revalidation.

##### On-demand Revalidation

Data can be revalidated on-demand by path or by cache tag inside a Server Action or Route Handler.

Next.js has a cache tagging system for invalidating `fetch` requests across routes.

1. When using `fetch`, you have the option to tag cache entries with one or more tags.
2. Then, you can call `revalidateTag` to revalidate all entries associated with that tag.

For example, the following `fetch` request adds the cache tag `collection`:

```tsx filename="app/page.tsx" switcher
export default async function Page() {
  const res = await fetch('https://...', { next: { tags: 'collection'] } })
  const data = await res.json()
  // ...
}
```

```jsx filename="app/page.js" switcher
export default async function Page() {
  const res = await fetch('https://...', { next: { tags: 'collection'] } })
  const data = await res.json()
  // ...
}
```

You can then revalidate this `fetch` call tagged with `collection` by calling `revalidateTag` in a Server Action:

```ts filename="app/actions.ts" switcher
'use server'

import { revalidateTag } from 'next/cache'

export default async function action() {
  revalidateTag('collection')
}
```

```js filename="app/actions.js" switcher
'use server'

import { revalidateTag } from 'next/cache'

export default async function action() {
  revalidateTag('collection')
}
```


##### Error handling and revalidation

If an error is thrown while attempting to revalidate data, the last successfully generated data will continue to be served from the cache. On the next subsequent request, Next.js will retry revalidating the data.

#### Opting out of Data Caching

`fetch` requests are **not** cached if:

- The `cache: 'no-store'` is added to `fetch` requests.
- The `revalidate: 0` option is added to individual `fetch` requests.
- The `fetch` request is inside a Router Handler that uses the `POST` method.
- The `fetch` request comes after the usage of `headers` or `cookies`.
- The `const dynamic = 'force-dynamic'` route segment option is used.
- The `fetchCache` route segment option is configured to skip cache by default.
- The `fetch` request uses `Authorization` or `Cookie` headers and there's an uncached request above it in the component tree.

##### Individual `fetch` Requests

To opt out of caching for individual `fetch` requests, you can set the `cache` option in `fetch` to `'no-store'`. This will fetch data dynamically, on every request.

```js filename="layout.js | page.js"
fetch('https://...', { cache: 'no-store' })
```

View all the available `cache` options in the `fetch` API referenceapi-reference/functions/fetch).

##### Multiple `fetch` Requests

If you have multiple `fetch` requests in a route segment (e.g. a Layout or Page), you can configure the caching behavior of all data requests in the segment using the Segment Config Optionsapi-reference/file-conventions/route-segment-config).

However, we recommend configuring the caching behavior of each `fetch` request individually. This gives you more granular control over the caching behavior.

### Fetching data on the Server with third-party libraries

In cases where you're using a third-party library that doesn't support or expose `fetch` (for example, a database, CMS, or ORM client), you can configure the caching and revalidating behavior of those requests using the Route Segment Config Optionapi-reference/file-conventions/route-segment-config) and React's `cache` function.

Whether the data is cached or not will depend on whether the route segment is statically or dynamically rendered. If the segment is static (default), the output of the request will be cached and revalidated as part of the route segment. If the segment is dynamic, the output of the request will _not_ be cached and will be re-fetched on every request when the segment is rendered.

You can also use the experimental `unstable_cache` APIapi-reference/functions/unstable_cache).

#### Example

In the example below:

- The React `cache` function is used to  memoize data requests.
- The `revalidate` option is set to `3600` in the Layout and Page segments, meaning the data will be cached and revalidated at most every hour.

```ts filename="app/utils.ts" switcher
import { cache } from 'react'

export const getItem = cache(async (id: string) => {
  const item = await db.item.findUnique({ id })
  return item
})
```

```js filename="app/utils.js" switcher
import { cache } from 'react'

export const getItem = cache(async (id) => {
  const item = await db.item.findUnique({ id })
  return item
})
```

Although the `getItem` function is called twice, only one query will be made to the database.

```tsx filename="app/item/[id]/layout.tsx" switcher
import { getItem } from '@/utils/get-item'

export const revalidate = 3600 // revalidate the data at most every hour

export default async function Layout({
  params: { id },
}: {
  params: { id: string }
}) {
  const item = await getItem(id)
  // ...
}
```

```jsx filename="app/item/[id]/layout.js" switcher
import { getItem } from '@/utils/get-item'

export const revalidate = 3600 // revalidate the data at most every hour

export default async function Layout({ params: { id } }) {
  const item = await getItem(id)
  // ...
}
```

```tsx filename="app/item/[id]/page.tsx" switcher
import { getItem } from '@/utils/get-item'

export const revalidate = 3600 // revalidate the data at most every hour

export default async function Page({
  params: { id },
}: {
  params: { id: string }
}) {
  const item = await getItem(id)
  // ...
}
```

```jsx filename="app/item/[id]/page.js" switcher
import { getItem } from '@/utils/get-item'

export const revalidate = 3600 // revalidate the data at most every hour

export default async function Page({ params: { id } }) {
  const item = await getItem(id)
  // ...
}
```

### Fetching Data on the Client with Route Handlers

If you need to fetch data in a client component, you can call a Route Handler from the client. Route Handlers execute on the server and return the data to the client. This is useful when you don't want to expose sensitive information to the client, such as API tokens.

> **Server Components and Route Handlers**
>
> Since Server Components render on the server, you don't need to call a Route Handler from a Server Component to fetch data. Instead, you can fetch the data directly inside the Server Component.

### Fetching Data on the Client with third-party libraries

You can also fetch data on the client using a third-party library such as SWR or TanStack Query. These libraries provide their own APIs for memoizing requests, caching, revalidating, and mutating data.

> **Future APIs**:
>
> `use` is a React function that **accepts and handles a promise** returned by a function. Wrapping `fetch` in `use` is currently **not** recommended in Client Components and may trigger multiple re-renders. Learn more about `use` in the React docs.

## Data Fetching Patterns and Best Practices

There are a few recommended patterns and best practices for fetching data in React and Next.js. This page will go over some of the most common patterns and how to use them.

### Fetching data on the server

Whenever possible, we recommend fetching data on the server with Server Components. This allows you to:

- Have direct access to backend data resources (e.g. databases).
- Keep your application more secure by preventing sensitive information, such as access tokens and API keys, from being exposed to the client.
- Fetch data and render in the same environment. This reduces both the back-and-forth communication between client and server, as well as the work on the main thread on the client.
- Perform multiple data fetches with single round-trip instead of multiple individual requests on the client.
- Reduce client-server waterfalls.
- Depending on your region, data fetching can also happen closer to your data source, reducing latency and improving performance.

Then, you can mutate or update data with Server Actions.

### Fetching data where it's needed

If you need to use the same data (e.g. current user) in multiple components in a tree, you do not have to fetch data globally, nor forward props between components. Instead, you can use `fetch` or React `cache` in the component that needs the data without worrying about the performance implications of making multiple requests for the same data.

This is possible because `fetch` requests are automatically memoized. Learn more about request memoization.

> **Good to know**: This also applies to layouts, since it's not possible to pass data between a parent layout and its children.

### Streaming

Streaming and Suspense are React features that allow you to progressively render and incrementally stream rendered units of the UI to the client.

With Server Components and nested layouts, you're able to instantly render parts of the page that do not specifically require data, and show a loading state for parts of the page that are fetching data. This means the user does not have to wait for the entire page to load before they can start interacting with it.

<Image
  alt="Server Rendering with Streaming"
  srcLight="/docs/light/server-rendering-with-streaming.png"
  srcDark="/docs/dark/server-rendering-with-streaming.png"
  width="1600"
  height="785"
/>

To learn more about Streaming and Suspense, see the Loading UI and Streaming and Suspense pages.

### Parallel and sequential data fetching

When fetching data inside React components, you need to be aware of two data fetching patterns: Parallel and Sequential.

<Image
  alt="Sequential and Parallel Data Fetching"
  srcLight="/docs/light/sequential-parallel-data-fetching.png"
  srcDark="/docs/dark/sequential-parallel-data-fetching.png"
  width="1600"
  height="525"
/>

- With **sequential data fetching**, requests in a route are dependent on each other and therefore create waterfalls. There may be cases where you want this pattern because one fetch depends on the result of the other, or you want a condition to be satisfied before the next fetch to save resources. However, this behavior can also be unintentional and lead to longer loading times.
- With **parallel data fetching**, requests in a route are eagerly initiated and will load data at the same time. This reduces client-server waterfalls and the total time it takes to load data.

#### Sequential Data Fetching

If you have nested components, and each component fetches its own data, then data fetching will happen sequentially if those data requests are different (this doesn't apply to requests for the same data as they are automatically memoized).

For example, the `Playlists` component will only start fetching data once the `Artist` component has finished fetching data because `Playlists` depends on the `artistID` prop:

```tsx filename="app/artist/[username]/page.tsx" switcher
// ...

async function Playlists({ artistID }: { artistID: string }) {
  // Wait for the playlists
  const playlists = await getArtistPlaylists(artistID)

  return (
    <ul>
      {playlists.map((playlist) => (
        <li key={playlist.id}>{playlist.name}</li>
      ))}
    </ul>
  )
}

export default async function Page({
  params: { username },
}: {
  params: { username: string }
}) {
  // Wait for the artist
  const artist = await getArtist(username)

  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <Playlists artistID={artist.id} />
      </Suspense>
    </>
  )
}
```

```jsx filename="app/artist/[username]/page.js" switcher
// ...

async function Playlists({ artistID }) {
  // Wait for the playlists
  const playlists = await getArtistPlaylists(artistID)

  return (
    <ul>
      {playlists.map((playlist) => (
        <li key={playlist.id}>{playlist.name}</li>
      ))}
    </ul>
  )
}

export default async function Page({ params: { username } }) {
  // Wait for the artist
  const artist = await getArtist(username)

  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <Playlists artistID={artist.id} />
      </Suspense>
    </>
  )
}
```

In cases like this, you can use `loading.js` (for route segments) or React `<Suspense>` (for nested components) to show an instant loading state while React streams in the result.

This will prevent the whole route from being blocked by data fetching, and the user will be able to interact with the parts of the page that are not blocked.

> **Blocking Data Requests:**
>
> An alternative approach to prevent waterfalls is to fetch data globally, at the root of your application, but this will block rendering for all route segments beneath it until the data has finished loading. This can be described as "all or nothing" data fetching. Either you have the entire data for your page or application, or none.
>
> Any fetch requests with `await` will block rendering and data fetching for the entire tree beneath it, unless they are wrapped in a `<Suspense>` boundary or `loading.js` is used. Another alternative is to use parallel data fetching #parallel-data-fetching) or the preload pattern #preloading-data).

#### Parallel Data Fetching

To fetch data in parallel, you can eagerly initiate requests by defining them outside the components that use the data, then calling them from inside the component. This saves time by initiating both requests in parallel, however, the user won't see the rendered result until both promises are resolved.

In the example below, the `getArtist` and `getArtistAlbums` functions are defined outside the `Page` component, then called inside the component, and we wait for both promises to resolve:

```tsx filename="app/artist/[username]/page.tsx" switcher
import Albums from './albums'

async function getArtist(username: string) {
  const res = await fetch(`https://api.example.com/artist/${username}`)
  return res.json()
}

async function getArtistAlbums(username: string) {
  const res = await fetch(`https://api.example.com/artist/${username}/albums`)
  return res.json()
}

export default async function Page({
  params: { username },
}: {
  params: { username: string }
}) {
  // Initiate both requests in parallel
  const artistData = getArtist(username)
  const albumsData = getArtistAlbums(username)

  // Wait for the promises to resolve
  const artist, albums] = await Promise.all([artistData, albumsData])

  return (
    <>
      <h1>{artist.name}</h1>
      <Albums list={albums}></Albums>
    </>
  )
}
```

```jsx filename="app/artist/[username]/page.js" switcher
import Albums from './albums'

async function getArtist(username) {
  const res = await fetch(`https://api.example.com/artist/${username}`)
  return res.json()
}

async function getArtistAlbums(username) {
  const res = await fetch(`https://api.example.com/artist/${username}/albums`)
  return res.json()
}

export default async function Page({ params: { username } }) {
  // Initiate both requests in parallel
  const artistData = getArtist(username)
  const albumsData = getArtistAlbums(username)

  // Wait for the promises to resolve
  const artist, albums] = await Promise.all([artistData, albumsData])

  return (
    <>
      <h1>{artist.name}</h1>
      <Albums list={albums}></Albums>
    </>
  )
}
```

To improve the user experience, you can add a Suspense Boundary to break up the rendering work and show part of the result as soon as possible.

### Preloading Data

Another way to prevent waterfalls is to use the preload pattern. You can optionally create a `preload` function to further optimize parallel data fetching. With this approach, you don't have to pass promises down as props. The `preload` function can also have any name as it's a pattern, not an API.

```tsx filename="components/Item.tsx" switcher
import { getItem } from '@/utils/get-item'

export const preload = (id: string) => {
  // void evaluates the given expression and returns undefined
  // https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void
  void getItem(id)
}
export default async function Item({ id }: { id: string }) {
  const result = await getItem(id)
  // ...
}
```

```jsx filename="components/Item.js" switcher
import { getItem } from '@/utils/get-item'

export const preload = (id) => {
  // void evaluates the given expression and returns undefined
  // https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void
  void getItem(id)
}
export default async function Item({ id }) {
  const result = await getItem(id)
  // ...
}
```

```tsx filename="app/item/[id]/page.tsx" switcher
import Item, { preload, checkIsAvailable } from '@/components/Item'

export default async function Page({
  params: { id },
}: {
  params: { id: string }
}) {
  // starting loading item data
  preload(id)
  // perform another asynchronous task
  const isAvailable = await checkIsAvailable()

  return isAvailable ? <Item id={id} /> : null
}
```

```jsx filename="app/item/[id]/page.js" switcher
import Item, { preload, checkIsAvailable } from '@/components/Item'

export default async function Page({ params: { id } }) {
  // starting loading item data
  preload(id)
  // perform another asynchronous task
  const isAvailable = await checkIsAvailable()

  return isAvailable ? <Item id={id} /> : null
}
```

#### Using React `cache`, `server-only`, and the Preload Pattern

You can combine the `cache` function, the `preload` pattern, and the `server-only` package to create a data fetching utility that can be used throughout your app.

```ts filename="utils/get-item.ts" switcher
import { cache } from 'react'
import 'server-only'

export const preload = (id: string) => {
  void getItem(id)
}

export const getItem = cache(async (id: string) => {
  // ...
})
```

```js filename="utils/get-item.js" switcher
import { cache } from 'react'
import 'server-only'

export const preload = (id) => {
  void getItem(id)
}

export const getItem = cache(async (id) => {
  // ...
})
```

With this approach, you can eagerly fetch data, cache responses, and guarantee that this data fetching only happens on the server.

The `utils/get-item` exports can be used by Layouts, Pages, or other components to give them control over when an item's data is fetched.

> **Good to know:**
>
> - We recommend using the `server-only` package to make sure server data fetching functions are never used on the client.

### Preventing sensitive data from being exposed to the client

We recommend using React's taint APIs, `taintObjectReference` https://react.dev/reference/react/experimental_taintObjectReference) and `taintUniqueValue` https://react.dev/reference/react/experimental_taintUniqueValue), to prevent whole object instances or sensitive values from being passed to the client.

To enable tainting in your application, set the Next.js Config `experimental.taint` option to `true`:

```js filename="next.config.js"
module.exports = {
  experimental: {
    taint: true,
  },
}
```

Then pass the object or value you want to taint to the `experimental_taintObjectReference` or `experimental_taintUniqueValue` functions:

```ts filename="app/utils.ts" switcher
import { queryDataFromDB } from './api'
import {
  experimental_taintObjectReference,
  experimental_taintUniqueValue,
} from 'react'

export async function getUserData() {
  const data = await queryDataFromDB()
  experimental_taintObjectReference(
    'Do not pass the whole user object to the client',
    data
  )
  experimental_taintUniqueValue(
    "Do not pass the user's address to the client",
    data,
    data.address
  )
  return data
}
```

```js filename="app/utils.js" switcher
import { queryDataFromDB } from './api'
import {
  experimental_taintObjectReference,
  experimental_taintUniqueValue,
} from 'react'

export async function getUserData() {
  const data = await queryDataFromDB()
  experimental_taintObjectReference(
    'Do not pass the whole user object to the client',
    data
  )
  experimental_taintUniqueValue(
    "Do not pass the user's address to the client",
    data,
    data.address
  )
  return data
}
```

```tsx filename="app/page.tsx" switcher
import { getUserData } from './data'

export async function Page() {
  const userData = getUserData()
  return (
    <ClientComponent
      user={userData} // this will cause an error because of taintObjectReference
      address={userData.address} // this will cause an error because of taintUniqueValue
    />
  )
}
```

```jsx filename="app/page.js" switcher
import { getUserData } from './data'

export async function Page() {
  const userData = await getUserData()
  return (
    <ClientComponent
      user={userData} // this will cause an error because of taintObjectReference
      address={userData.address} // this will cause an error because of taintUniqueValue
    />
  )
}
```

Learn more about Security and Server Actions https://nextjs.org/blog/security-nextjs-server-components-actions).