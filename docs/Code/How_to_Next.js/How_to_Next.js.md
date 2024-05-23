## Next.js Core Concepts
### Next.js

Next.js is a React framework that supports the development of robust web applications. It provides key features such as routing, rendering, data fetching, server components, and optimization. These features enable high-performing, scalable, and search-engine-friendly web applications.

### Next.js Rendering

Rendering is the process of converting code into viewable user interfaces. In Next.js, different rendering strategies can be adopted depending on the application’s requirements.

### Next.js Rendering Strategies

Next.js enables developers to employ various rendering strategies, allowing the creation of hybrid web applications. These strategies allow developers to decide the rendering environment for individual components. The primary environments supported by Next.js are server-side and client-side rendering.

### Server-side Rendering

In server-side rendering, the server infrastructure is used to render the webpage content. Then, a viewable, non-interactive webpage is sent to the client.

In Next.js, components are server-side rendered by default using server components. No additional configuration is necessary.

### Client-side Rendering

In client-side rendering, the server sends the client all the necessary files for a webpage. The client uses the instructions to render the components on the browser and enable interactivity.

In Next.js, a component can be marked for client-side rendering with the `'use client'` directive.

```tsx
'use client'
 
function MyComponent() { 
	return <div>Hello, World!</div>; 
}
```

### `create-next-app`

A Next.js project can be created with the CLI tool `create-next-app`. It starts an interactive setup experience and generates a basic project structure and configuration when used.

### Next.js App Router

Next.js uses the App Router, providing routing for the web application based on the `/app` directory. Each folder name determines a route that exists. To make the route accessible, a `page.tsx` file must live in the directory.

To make the `/` path accessible, the file structure must look like this:

```
├── app
│   ├── page.tsx
```

### CSS Modules

Next.js supports multiple ways of styling the web app. It supports CSS modules, allowing a locally scoped styling method.

```tsx
import styles from './MyComponent.module.css'; 

function MyComponent() { 
	return <div className={styles.myClass}>Hello World</div>; 
}
```

### Next.js Hydration

After webpages are server-side rendered, they are made interactive on the client through hydration.

Hydration executes the bundled JavaScript, attaching event handlers and linking the React components to their HTML counterparts. Reconciliation is performed to compare the results made on the client with those sent by the server, ensuring they’re in sync.

## Next.js Routing

### Next.js App Router

In Next.js, the App Router is used to define and structure an application’s routes and their UIs. It is defined by creating a folder named `app` at the project’s root level.

### Next.js Folders and Reserved Files

In the Next.js App Router, folders define path segments, and reserved files within, like `page.tsx`, define that segment’s UI.

```
├── app
│   ├── users               /users
│   │   ├── page.tsx          UI
│   ├──settings             /settings
└── │   ├── page.tsx          UI
```

### Next.js Nested Routes

In Next.js, nested routes can be created by creating nested folders.

```
├── app
│   ├── users
│   │   ├── profiles        /users/profiles
└── │   │   ├── page.tsx      UI
```

### Composition of URL Paths

A URL path is composed of path segments and comes after the URL domain.

For example, in the URL `https://www.codecademy.com/catalog/language/javascript`:

- `codecademy.com` is the domain.
- `/catalog/language/javascript` is the path.
- `/catalog`, `/language`, and `/javascript` are path segments.

### Next.js Navigation

Next.js provides SPA-like navigation with the `useRouter()` hook and `<Link>` component.

The `useRouter()` hook returns a `router` object which contains methods like `push(path)`, `back()`, and `forward()` for navigation.

The `<Link>` component extends the `<a>` element by adding features like prefetching.

```tsx
'use client'

import { useRouter } from 'next/navigation'
import Link from 'next/link'

export default function MyPage() {
	const router = useRouter()
	return (
		<div>
			<Link href="/">Home</Link>
		  <button onClick={router.back}>Back</button>
		</div>
	)
}
```

### Next.js `page.tsx`

In Next.js, a `page.tsx` is a reserved file that defines a unique UI for its containing path segment and makes it accessible.

To use `page.tsx`, you must default export a React component.

```tsx
// in page.tsx in the folder /app/settings

export default function SettingsPage() {
	return (
		<h1>Your Settings</h1>
	)
}
```

### Next.js `layout.tsx` and `template.tsx`

In Next.js, `layout.tsx` and `template.tsx` are reserved files used to create shared UIs.

Similarities include:

- Requiring a default export React component.
- Exported component receives a `children` prop of type `ReactNode`.
- Wraps other reserved files and nested routes.

Differences include:

- A `template.tsx` component will be re-instantiated while `layout.tsx` component will not.
- At least one root `layout.tsx` returning the `<html>` and `<body>` elements is required in the App Router.

```tsx
// In root layout.tsx

export default function RootLayout({children}: { children: React.ReactNode}) {
	return (
		<html>
			  <body>
				   <h1>Root Layout</h1>
				   {children}
			  </body>
		</html>
	)
}
```

```tsx
// In template.tsx

export default function Template({children}: {children: React.ReactNode}) {
	return (
		<h2>Template Layout</h2>
		{children}
	)
}
```

### Next.js Dynamic Segments

In Next.js, a dynamic segment is created by wrapping a folder’s name in square brackets, for example, `/app/users/[userId]`.

The `page.tsx` component exported from this folder will receive a `params` prop which will contain the dynamic segment data (as a `string`) and be referenced using the dynamic segment folder name (`userId`).

```tsx
// In /app/users/[userId]/page.tsx

export default function UserPage({ params }: { params: { userId: string }}) {
	const userId = params.userId  // Access dynamic segment data
	return (
		<h2>My User Page for: {userId}</h2>
	)
}
```

### Next.js Catch-all Dynamic Segment

In Next.js, dynamic segments can be further modified to be made catch-all and optional. A catch-all segment is created by prefixing a dynamic segment with ellipses like `/app/articles/[...articleIds]`.

A catch-all segment’s `page.tsx` component will receive a `params` prop containing the dynamic data as an array of `string`s referenced using the dynamic folder name (`articleIds`).

To make the catch-all dynamic segment optional, you wrap it in another pair of square brackets like: `/app/articles/[[...articleIds]]`.

```tsx
// In /app/articles/[[...articleIds]]/page.tsx

export default function ArticlesPage({ params }: { params: { articleIds: string[]}}) {
	const articleIds = params.articleIds  // Retrieve dynamic segment data
	
	return (
		<>
			<h2>Articles</h2>
			{articleIds.map(id => (<p>Article Id is: {id}</p>))}
		</>
	)
}
```

### Next.js Reserved Files

Next.js reserves special files used to define UIs by default exporting a React component. Some of the special files include:

- `layout.tsx`: Defines a shared UI.
- `template.tsx`: Defines a shared UI.
- `error.tsx`: Defines an `ErrorBoundary` fallback UI.
- `loading.tsx`: Defines a `Suspense` fallback UI.
- `not-found.tsx`: Defines an `ErrorBoundary` fallback UI for an unknown segment or nested segments.

### Next.js Reserved File Component Hierarchy

Next.js defines a component hierarchy for reserve file components. The hierarchy is:

1. `layout.tsx`
2. `template.tsx`
3. `error.tsx`
4. `loading.tsx`
5. `not-found.tsx`
6. `page.tsx`

Any nested hierarchy will be placed within the hierarchy of its parent.

## Next.js Server Components

### Hybrid Rendering

Next.js applications offer patterns to combine Server and Client Components for managing static and dynamic content effectively.

- Client Components can be directly imported into Server Components.
- Server Components must be passed into Client Components as props for decoupled rendering.

```tsx
// Example of using Client and Server Components together

import ClientComponent from './ClientComponent';

export default function ServerComponent() {
	return (
		<div>
			<ClientComponent />
		</div>
	);
}
```

### Integration of Server within Client Components

In Next.js, Server Components cannot be directly imported into Client Component modules. Instead, they should be passed through a prop for independent rendering. This decoupled approach ensures that server-side logic remains separate from client-side execution.

```tsx
// ClientComponent.tsx
'use client';

export default function ClientComponent({ children }: { children: React.ReactNode }) {
	return <div>{children}</div>;
}
```

```tsx
// Page.tsx

import ClientComponent from './ClientComponent.tsx';
import ServerComponent from './ServerComponent.tsx';

export default function Page() {
	return (
		<ClientComponent>
			<ServerComponent />
		</ClientComponent>
	)
}
```

### Next.js Server Components

In Next.js, Server Components, also referred to as React Server Components (RSCs), are the default component type. They optimize load times and enhance SEO efficiency by rendering on the server.

### Rendering Server Components

Server Components are rendered through a server-client coordination process.

When a request is received:

- Next.js configures the environment for route-specific React components.
- Rendering work is segmented into smaller units, or chunks, based on route segments.
- Chunks are converted into the React Server Component Payload (RSC Payload).
- The server sends the HTML and RSC Payload to the client.
- Upon receiving the rendering data, React utilizes the RSC Payload to update the browser’s DOM, synchronizing server-rendered components with their client-side counterparts.
- Client Components are loaded and made interactive through JavaScript hydration.

### Rendering Strategies

Next.js offers three rendering strategies: **static**, **dynamic**, and **streaming**. These strategies enable efficient content distribution, real-time content generation, and improved load times, respectively. Developers can adjust settings to balance between serving static and dynamic responses.

### Static Rendering

Static rendering in Next.js pre-generates pages at build time and can be cached and distributed via a CDN. This approach efficiently caches content, reducing server load, and is ideal for static, unchanging content.

### Dynamic Rendering

Dynamic rendering in Next.js generates real-time content per request, making it suitable for personalized or time-sensitive content. However, this approach may slow response time due to its resource-intensive nature.

### Streaming in Next.js

Streaming in Next.js enhances load times by progressively sending UI chunks to the client. This feature allows users to interact with parts of the page as they become available. It’s especially useful for pages with delayed data fetching, enabling immediate display of available content.

### Server Components Implementation

In Next.js, implementing Server Components involves creating a React component, with Next.js automatically handling the server-side rendering process.

```tsx
import React from 'react';

// Server Component for displaying item details
export default function ItemDetails({ itemId }: { itemId: ItemIdType }) {
	return (
	    <div>
			<h2>Item: {itemId.name}</h2>
		</div>
	);
}
```

### Benefits of Server Components

Server Components in Next.js offer multiple advantages:

- They execute server-side, reducing client-side load by excluding them from the JavaScript bundle.
- They fetch data server-side, optimizing application performance by offloading tasks to the server.
- Server Components transmit only necessary output to the client, enhancing efficiency and reducing bandwidth consumption.

### Suspense with Fallback Components

React Suspense and Fallback Components, when used alongside Next.js Server Components, enable developers to define custom Suspense Boundaries. This enhances user interaction by suspending rendering during loading and displaying placeholders like loaders or skeleton screens.

```tsx
import { Suspense } from 'react';

// Define a fallback component
const FallbackComponent = () => <p>Loading...</p>;

function MyComponent() {
	return (
	    <Suspense fallback={<FallbackComponent />}>
			...
	    </Suspense>
	);
}
```

### Roles of Server and Client Components

Server Components efficiently handle data and backend interactions, while Client Components focus on interactivity and state management. Understanding and applying these distinct roles optimizes server-side efficiency and user experience in Next.js applications.

## Next.js Data Fetching

### Data Fetching

In a Next.js app, data fetching can occur both on the server and the client. While client-side data fetching may be necessary in some cases, data fetching on the server should be prioritized.

### Server-Side Data Fetching

In Next.js applications, data fetching should ideally occur on the server due to several benefits:

- The server has direct access to the database, ensuring efficient data retrieval.
- Fetching data on the server reduces client-server waterfalls, optimizing performance.
- Server components enable data fetching and rendering in the same environment, enhancing efficiency.
- Security is increased as sensitive data can remain unexposed to the client, reducing the risk of data breaches.

### Route Handlers in Next.js

Route Handlers in Next.js are used to define custom request handlers responsible for fetching data and preparing responses for client interactions. These handlers are typically defined in the `/app` directory and are structured to handle various HTTP methods such as `GET`, `POST`, and `PUT`.

```tsx
export async function GET() {
	const response = await fetch('https://api.example.com/data');

	if (!response.ok) {
		throw new Error('Failed to fetch data.');
	}
	
	const data = await response.json();
	return data;
}
```

### Fetch Calls in Server Components

In Next.js applications, `fetch` calls can be directly used within server components, enabling seamless data retrieval and processing on the server side. However, this practice can potentially lead to the exposure of sensitive data when not used in conjunction with the `'server-only'` package.

```tsx
// Example of fetch call within a Server Component

export async function MyServerComponent() {
	const response = await fetch('https://api.example.com/data', { cache: 'no-store' });

	if (!response.ok) {
	    throw new Error('Failed to fetch data.');
	}
	
	const data = await response.json();
	// Additional logic for the component
}
```

### `'server-only'` Package

The `'server-only'` package serves to prevent server-only code from being transmitted to the client in Next.js applications.

Using the `'server-only'` package can ensure that sensitive server-side logic or resources remain exclusive to the server environment, enhancing security and minimizing the risk of exposing critical information to clients.

```tsx
// Example of using the 'server-only' package

import 'server-only';

// Server-only code here
export async function MyServerComponent() {

}
```

### Cache Control in Data Fetching

Next.js automatically caches fetched data by default. To disable caching, include `cache: 'no-store'` in the options object of a `fetch` call.

Disabling caching ensures that data is fetched freshly from the server each time, preventing stale data issues in Next.js applications.

```tsx
const response = await fetch('https://api.example.com/data', {
	cache: 'no-store',
});
```

### Sequential Data Fetching Pattern

When using the sequential data fetching pattern, requests create waterfalls as they happen one after the other. This approach is used when one data fetch depends on the result of another.

### Parallel Data Fetching Pattern

In the parallel data fetching pattern, requests within a route occur simultaneously, without waiting for one request to complete before starting another. This approach makes it easier to fetch multiple resources concurrently, leading to faster performance and better user experience.

### Optimizing Parallel Data Fetching

The parallel data fetching pattern can be optimized by preloading data. By creating a function, such as `preload()`, data can be fetched and cached before it needs to be rendered.

```tsx
// utils/getPosts.tsx

import { cache } from 'react';
import 'server-only';

export const preload = () => {
	void getPosts();
};

export const getPosts = cache(async () => {
	const response = await fetch('https://api.com/some/route');
	// more fetch logic
});
```

```tsx
// In a component file

import { preload } from '../utils/getPosts';
import Posts from '../components/Posts/Posts';

export default function Home() {
	preload();
	return (
		<Posts />
	);
}
```

### Cached Data Revalidation

Next.js enables cached data revalidation through two methods:

- **Time-based Revalidation:** Set the interval for data revalidation.
- **On-demand Revalidation:** Group data by path or tag, updating it collectively upon specific event triggers.

### Time-based Revalidation

Time-based revalidation in Next.js allows for the automatic revalidation of cached data after a specified duration, measured in seconds. By setting the `next.revalidate` option in a fetch call, developers can control how frequently data is refreshed to ensure its accuracy and timeliness.

```tsx
const response = await fetch('https://api.com/some/route', {
	next: { revalidate: 30 } // Revalidate data every 30 seconds
});
```

### Server Action in Next.js

A Server Action is an asynchronous function executed on the server. It is created using the `'use server'` directive. This enables specific tasks to be executed on the server side, such as data revalidation or fetching.

```tsx
'use server'

import { revalidateTag } from 'next/cache'

export async function updatePost() {
	// Revalidate cached data with the 'posts' tag
	revalidateTag('posts')
}
```

```tsx
// The component that uses the updatePost() Server Action
import { updatePost } from './actions';

export default function EditPost() {
	// Component logic here
  
	return (
		<form action={updatePost}>
			<button type="submit">Update</button>
		</form>
	);
}
```

### On-Demand Revalidation

On-demand revalidation ensures data freshness by updating cached data selectively when specific events occur. This optimization technique allows developers to control data revalidation either by path or tag, providing precise control over data fetching.

The `revalidateTag()` function from `next/cache` enables developers to trigger revalidation of cached data associated with a particular tag.

```tsx
'use server'

import { revalidateTag } from 'next/cache';

// Server action to update cached data with a specific tag
export async function updateCachedData() {
	revalidateTag('blogs'); // Revalidate cached data with the 'blogs' tag
}
```

### Loading UIs

Loading UIs provides visual feedback to users, indicating that data is being fetched, as fetching large amounts of data can take time. These UIs, like spinners, skeletons, progress indicators, or loading messages, serve as instant loading states, replacing segments while data loads.

```tsx
//loading.tsx

export default function Loading(){
	return (
		<p>Loading data...</p>
	)
}
```

### Streaming using `<Suspense>` boundaries

Streaming using `<Suspense>` boundaries allows a web app to progressively render and incrementally stream parts of the UI to the client, enhancing user experience by displaying content as it becomes available.

```tsx
<Suspense fallback={<Loading />} >
	<UserPosts />
</Suspense>
```

### Server-Only Forms With Server Actions

Server-only forms can be created in a server component using Server Actions to efficiently manage form submissions by retrieving, validating, and processing user data on the server side.

```tsx
// components/FeedbackForm/actions.ts
'use server'  
import { redirect } from 'next/navigation'

export async function handleFeedback(formData: FormData) {
	// process form data
	redirect('/feedback/thankyou');
}  
```

```tsx
// components/FeedbackForm/FeedbackForm.tsx  
import { handleFeedback } from './actions'

export default function FeedbackForm() {
	return (
	    <form action={handleFeedback}>
			{/* form fields */}
			<button type="submit">Submit</button>
	    </form>
	);
}
```

## Next.js Optimization

Web applications should prioritize providing quality and optimized end-user experiences. Optimization of web applications with Next.js revolves around enhancing loading speed, minimizing interactivity delays, and ensuring visual consistency.

### Largest Contentful Paint (LCP)

Largest Contentful Paint (LCP) is the time from the start of the navigation until the largest block of content is visible to the user. The LCP should be visible within 2.5 seconds of the page initially loading. The LCP can be any element, including a large text block, video, or image.

![A diagram showing metrics for the Largest Contentful Paint (LCP). Below 2.5 seconds is excellent. Between 2.5 and 4 seconds needs improvement. Above 4 seconds is poor.](https://static-assets.codecademy.com/Courses/learn-nextjs/optimization/lcp.svg)

### First Input Delay (FID)

First Input Delay (FID) is the time from when the user first interacts to the time when the browser begins processing the events. The FID should take no longer than 0.1 seconds or 100ms. Interactions can include the following actions:

- user clicking a link
- user tapping on a button
- calling a custom, JavaScript-powered control on an interaction

![A diagram showing metrics for First Input Delay (FID). Below 100ms is excellent. Between 100ms and 300ms needs improvement. Above 300ms is poor.](https://static-assets.codecademy.com/Courses/learn-nextjs/optimization/fid.svg)

### Cumulative Layout Shift (CLS)

Cumulative Layout Shift (CLS) measures the largest burst of layout shift for every unexpected layout during the lifespan of a page. A layout shift occurs any time a visible element changes its position from one rendered frame to the next. Layout shift is calculated using the following formula:

`layout shift score = impact fraction * distance fraction`

CLS should score less than 0.1.

![A diagram showing metrics for Cumulative Layout Shift (CLS). Below 0.1 is excellent. Between 0.1 and 0.25 needs improvement. Above 0.25 is poor.](https://static-assets.codecademy.com/Courses/learn-nextjs/optimization/cls.svg)

### Next.js Script Optimization

Next.js’ built-in [`next/script` Component](https://nextjs.org/docs/pages/building-your-application/optimizing/scripts) helps with loading third-party scripts, only loading them one time even if the user navigates between pages. There are four strategies to fine-tune script execution:

- `beforeInteractive`: Load the script before any Next.js code and before any page hydration occurs.
- `afterInteractive`: (default) Load the script early, but after some hydration on the page occurs.
- `lazyOnload`: Load the script later during the browser’s idle time.
- `worker`: (experimental) Load the script in a web worker.

### Next.js Font Optimization

Next.js’ built-in [`next/font` Component](https://nextjs.org/docs/pages/building-your-application/optimizing/fonts) adds web fonts with zero layout shift and zero requests to Google. Next.js uses font loading for both Google fonts and local fonts. Next.js also provides the capability to preload and reuse fonts efficiently in your application.

### Next.js Image Optimization

Next.js’ built-in [`next/image` Component](https://nextjs.org/docs/pages/building-your-application/optimizing/images) extends the HTML `<img>` element to include automatic image optimization. Image optimization focuses on:

- Size Optimization: Automatically serve correctly sized images for each device.
- Visual Stability: Prevent layout shift automatically when images are loading.
- Faster Page Loads: Images are only loaded when they enter the viewport using native browser lazy loading.
- Asset Flexibility: On-demand image resizing, even for images stored on remote servers.

### Next.js Optimization Tools

Next.js has built-in tools to optimize images, fonts, scripts, and metadata.

- [Images](https://nextjs.org/docs/pages/api-reference/components/image): `next/image`
- [Fonts](https://nextjs.org/docs/pages/api-reference/components/font): `next/font/google` and `next/font/local`
- [Scripts](https://nextjs.org/docs/pages/api-reference/components/script): `next/script`
- [Metadata](https://nextjs.org/docs/app/building-your-application/optimizing/metadata): the `Metadata` object, `robots.tsx`, and `sitemap.tsx`.

### Next.js Metadata Optimization

Next.js has a [Metadata API](https://nextjs.org/docs/app/building-your-application/optimizing/metadata) to help define application metadata for improved SEO and shareability. Developers can use the `generateMetadata()` function for dynamic, config-based metadata generation or a static metadata object for static, config-based metadata generation. Developers can also use any of the [four available file-based metadata generation techniques](https://nextjs.org/docs/app/building-your-application/optimizing/metadata#file-based-metadata).

### Next.js Config-Based Metadata Optimization

Config-based Metadata exports a metadata object to a `layout.tsx` or `page.tsx` file.

```tsx
export async function generateMetadata({
	params,
}: MetadataProps): Promise<Metadata> {
	const slug = params.slug
	return {
	    title: `${slug}`,
	    description: `${slug}`,
	}
}
```

### Next.js File-Based Metadata Optimization

File-based Metadata adds a statically or dynamically generated file to a route file.

```tsx
export const metadata: Metadata = {
	title: 'Title of Page',
	description: 'Description of Page',
}
```

### Lighthouse

[Lighthouse](https://developer.chrome.com/docs/lighthouse/overview) is an open-source, automated tool to measure and assess web performance. Its primary goal is to enhance a website’s overall experience using the following metrics:

- Performance
- Accessibility
- Best Practices
- SEO