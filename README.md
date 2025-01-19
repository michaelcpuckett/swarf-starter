# Service Worker with App Router Starter

This is a boilerplate for a multi-page web application (MPA) that runs entirely
in a service worker context, demonstrating how to serve dynamically generated
HTML using
[@express-worker/router](https://github.com/michaelcpuckett/express-worker-router).

## Features

### App Router

Similar to [Next.js](https://nextjs.org/), routes are defined as a directory
structure under `src/app`.

Dynamic routes are defined by using square brackets in the folder name. For
example, if you want to create a dynamic route for user profiles, you can create
a folder with a path of `src/app/profiles/[id]`.

Each route folder should have a `page.tsx` inside. (Unlike Next.js, there is no
`layout.tsx` file.)

### React SSR

The initial page HTML is rendered by React-DOM/Server and gets hydrated by
React-DOM/Client.

### Static File Handling

Static files in the `public` directory are cached and served by the service
worker.

## Installation

1. Clone this starter kit

```sh
git clone https://github.com/michaelcpuckett/sw-app-router-starter
```

2. Install dependencies

```sh
npm install
```

3. Run the `app-router` script to generate list of routes and static files.

```sh
npm run app-router
```

4. Build the project.

```sh
npm run build
```

5. Serve the project.

```sh
npm run serve
```

6. Open in the browser.

```sh
http://localhost:8080
```

## .app-router Directory

### `index.tsx`

This file serves static files and routes requests to the appropriate React
components, utilizing the underlying
[ExpressWorker](https://www.github.com/michaelcpuckett/express-worker)
framework.

### `client.tsx`

This file hydrates the React components on the rendered pages using
`react-dom/client`.

### `PageShell.tsx`

This component wraps Page Components, injecting metadata and initial data into
the HTML.

### `prepare.ts`

The `app-router` script generates the route and static files configuration
required by the service worker. It scans the app directory for pages and the
public directory for static files.

## Usage

To add a new page:

1. Create a new folder in the `app` directory and create a `page.tsx` file
   inside it.

2. Define the React component for the page as the `default` export.

3. Define and export `getStaticProps` and `metadata`. (See below.)

4. Run `npm run app-router` to regenerate the routes.

### Static Props

`getStaticProps` is a function used to fetch data at render time. It allows you
to fetch data from an API or database and pass it as props to the page
component. The path params are passed to this function. You can define
`getStaticProps` as follows:

```ts
export const getStaticProps: GetStaticProps = async function ({
  params: { id },
}) {
  const data = await fetchData({ id });

  return {
    props: {
      data,
    },
  };
};
```

### Metadata

`metadata` is an object that contains information about the page, such as the
title and description. You can define `metadata` as follows:

```ts
export const metadata: Metadata = {
  title: 'Page Title',
  description: 'Page description',
};
```

If you need to access the route params, you can export a function instead:

```ts
export const metadata: GetMetadata = ({ params: { id } }) => ({
  title: 'Note ' + id,
});
```

### Page Component

The `default` export should be the Page component. It will receive the props
defined in `getStaticProps`. It will be wrapped in the `PageShell`.

```tsx
export default function HomePage({ data }: { data: Data }) {
  return <main>{data.foo}</main>;
}
```

## Development

Use `npm run serve` and `npm run dev` during development.

For easiest debugging, in the Web Inspector, under the Application tab, under
Service Workers, select the checkbox for "Update on reload".

Styles can be edited directly in `public/styles.css`.

## Production Builds

Use `npm run build` to generate a production build.

The built-in strategy for invalidating the old cache and serving the updated
content is through incrementing the version in `public/cache.json`.

## Benefits

### Fast Page Rendering

Routes can pre-cache or inline key assets. This allows navigation between pages
to be nearly instantaneous.

### Simplified Hosting

The server only needs to be able to serve the initial static assets. This
project uses Firebase Static Hosting.

### Offline-Ready

When a user navigates to a route while offline, the service worker can serve a
previously cached response or generate a new one based on stored data.

## Drawbacks

### No Search Engine Indexing

Pages generated by a service worker aren't indexed by search engines. You may
need alternative strategies for SEO, such as generating static HTML snapshots or
using a separate build process to create server-rendered pages.

## License

This project is licensed under the MIT License.
