# NextJS-Vercel-Dashboard
This project is follow the instruction in NextJS Vercel Dashboard document

ref: [NextJs](https://nextjs.org/learn/dashboard-app/getting-started)

# Introduction

# Setup Nextjs
## How to?
NextJs recommend us to use **pnpm** to install and create NextJs project. Here is how we install **pnpm**

```bash
$ npm install -g pnpm
```

After successfully install **pnpm** we pull the **Dashboard** project from github. In this tutorial we use the pre-written code from NextJs.

```bash
npx create-next-app@latest nextjs-dashboard --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example" --use-pnpm
```

## Explore the project
First we look at the project structure. 

```bash
$ cd nextjs-dashboard
```

Inside we can see folder **app** and **public**. App contain the main source code and public stores the pictures that using for this tutorial.

Inside the **app** we have these folders:

![folder-structure](./img/folder-structure.png)

Here is the explaination of the folders according to NextJS

* /app: Contains all the routes, components, and logic for your application, this is where you'll be mostly working from.
* /app/lib: Contains functions used in your application, such as reusable utility functions and data fetching functions.
* /app/ui: Contains all the UI components for your application, such as cards, tables, and forms. To save time, we've pre-styled these components for you.
*/public: Contains all the static assets for your application, such as images.
* Config Files: You'll also notice config files such as next.config.ts at the root of your application. Most of these files are created and pre-configured when you start a new project using create-next-app. You will not need to modify them in this course.

## How to run the project
To run the project first use this command line to install all the package need
```bash
$ pnpm i
```
then
```bash
pnpm dev
```
to run the project.

## Layout and Page
* In NextJS, they use file-base routing, each page.tsx is a route, so we use folders and page to define the routing in our project. 
* Unlike page, layout is the UI that shared between pages. UI keep state and do not re-render.

### Create a page
* To creata a page just simply create a folder, for instance **dashboard** inside app
```bash
$ mkdir dashboard
```
then
```bash
$ cd dashboard
```
and create page.tsx
```bash
$ touch page.tsx
```
And just like that we have the dashboard page.
```
https:localhost:3000/dashboard
```

### Create a layout
* To create layout, same with page.tsx, we create a layout inside the folder we want. Normally it is the parent folder that will need a UI to shared accross its child.

```bash
$ touch /dashboard/layout.tsx
```

### Create a Nested Route
A nested route is a route with many URL level. For example ***/blog/[slug]*** contains:
* ***"/"*** : Root Segment
* ***"blog"***: Segment
* ***"[slug]"***: leaf Segment

In nextjs:
* A folder is defined as a segment map with URL
* Files like page.tsx and layout.tsx is the UI shown for that URL segment.

### Create Nested Layout
By default the layout page wrap around ***children*** prop. You can nest layout by adding layout inside specific route. For example:

![nested-layout](./img/nested-layout.png)

```tsx
export default function BlogLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

If you were to combine the two layouts above, the root layout ***(app/layout.js)*** would wrap the blog layout (app/blog/layout.js), which would wrap the blog (app/blog/page.js) and blog post page (app/blog/[slug]/page.js).

### Create Dynamic Segment
Dynamic Segment use when you want to pass a parameter to the URL. For example, instead of creater many different URL for each blog post, you can pass a data and generate the route base on it.
* To create ***Dynamic Segment*** you wrap the segment in ***[]***.

* For instance ***app/blog/[slug]/page.tsx*** :
```tsx
export default async function BlogPostPage({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = await params
  const post = await getPost(slug)
 
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  )
}
```

#### Param
Param is a props that promise resolves to an object containing in the dynamic route from root segment down to that page.

* Example Routes:
![param](./img/param-route.png)

***Param*** prop is a promise, we muse use ***async/await***

### Rendering with search params

### Linking between pages
You can use ***Link*** component to navigate between routes. Tag Link is a built-in Next.js extend from HTML tag ***a***.

```tsx
import Link from 'next/link'
import { getPosts } from '@/lib/posts'
 
export default async function Posts() {
  const posts = await getPosts()
 
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.slug}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

### Route Props Helpers

## Linking and Navigating
In Nextjs, routes are rendered on the server by default. Client has to wait for a server response before a new route appear. Nextjs offers built-in prefetching, streamin and client-side transitions make sure navigation fast and responsive.

* These are the concepts need to familiar fo understand navigation:
  * Server Rendering
  * Prefetching
  * Streaming
  * Client-side transitions

### Server Rendering
By default in Nextjs, ***Layout*** and ***Pages*** are React Server Component, which means the UI is load in the server before sent to the client. Below we will discuss when the UI is render in Server.

* There are 2 types of ***Server Rendering***:
  * ***Prerendering*** happens at build time(run the project) or during the [revalidation](https://nextjs.org/docs/app/getting-started/revalidating) and the result is cached.
  * Dynamic Rendering happens at request time in response to client request.

#### Prefetching
Prefetching is the process when browser decide which navigation to fetch before the user click the to navigate. For example:

```tsx
import Link from 'next/link'
 
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <body>
        <nav>
          {/* Prefetched when the link is hovered or enters the viewport */}
          <Link href="/blog">Blog</Link>
          {/* No prefetching */}
          <a href="/contact">Contact</a>
        </nav>
        {children}
      </body>
    </html>
  )
}
```

Nextjs in client-side will prefer prefetch the ***Link** over the ***a***.

* Static Route will do the full prefetch
* Dynamic Route will skill prefetch or partially prefetch if we have the ***loading.tsx*** page.

#### Streaming
Streaming allows server to send parts of dynamic route to browser instead of waiting the whole page to finish.

* For dynamic routes, it means they can be ***partially prefetched***.

* To use streaming just create a ***loading.tsx***

app/dashboard/loading.tsx
```tsx
export default function Loading() {
  // Add fallback UI that will be shown while the route is loading.
  return <LoadingSkeleton />
}
```
NextJS will automatically wrap the page.tsx in the ***'<Suspense>'*** boundary.

Read more about Suspense: [Suspense](https://react.dev/reference/react/Suspense)

### Client-side transition
Next.js avoids this with client-side transitions using the ***Link*** component. Instead of reloading the page, it updates the content dynamically by:
* Keeping any shared layouts and UI.
* Replacing the current page with the prefetched loading state or a new page if available.

Client-side transitions are what makes a server-rendered apps feel like client-rendered apps. And when paired with prefetching and streaming, it enables fast transitions, even for dynamic routes.

Read more about what make Transition slow here: [What makes transition slow](https://nextjs.org/docs/app/getting-started/linking-and-navigating#what-can-make-transitions-slow)

## Server and Component
* Basically by default, all pages and layouts are server component.

### When to use which?
#### Client Component
Use Client Component when you need:
* State and event hanlder like ***onClick***, ***onChange***
* Lfiecyle logic, eg ***useEffect***
* Browser-only API eg ***localStorage***, ***window***, ***Navigator.geolocation***.
* Custom hooks
#### Server Component
We use Server Component when: 
* Fetch data from database or APIs close to source.
* Use API keys, tokens, and other secrets without exposing them to client
* Reduce the amount of Javascript sent to browser.
* Improve [First Contentful Paint(FCP)](https://web.dev/fcp/), and stream content progressively to the client.

The example below demonstrate the Page.tsx is a Server Component fetch data of Posts and pass it as a prop to LikeButton Component which is a client-side.

```tsx
import LikeButton from '@/app/ui/like-button'
import { getPost } from '@/lib/data'
 
export default async function Page({
  params,
}: {
  params: Promise<{ id: string }>
}) {
  // Get Posts
  const { id } = await params
  const post = await getPost(id)
 
  return (
    <div>
      <main>
        <h1>{post.title}</h1>
        {/* ... */}
        <LikeButton likes={post.likes} />
      </main>
    </div>
  )
}
```

```tsx
'use client' // This statement define this page as client-side
 
import { useState } from 'react'
 
export default function LikeButton({ likes }: { likes: number }) {
  // ...
}
```

### How do Server and Client work in NextJS
#### On the Server 
* On the server, Next.js uses React's APIs to orchestrate rendering. The rendering work is split into chunks, by individual route segments (layouts and pages), including parallel route slots whether or not they are displayed:
  * Server Components are rendered into a special data format called the React Server Component Payload (RSC Payload).
  * Client Components and the RSC Payload are used to prerender HTML.

#### On the Client
* Then, on the client:
  * HTML is used to immediately show a fast non-interactive preview of the route to the user.
  * RSC Payload is used to reconcile the Client and Server Component trees.
  * JavaScript is used to hydrate Client Components and make the application interactive.

### Few notes
* To make a page a Client-side just ***'use client'*** at the top of the page
* You can pass data from Server Component to Client Component via props. Example passing the post above.
* Context provider, the [React Context](https://react.dev/learn/passing-data-deeply-with-context) can only use in Client Component. To use Context create a client Component that accept ***children***
```tsx
'use client'
 
import { createContext } from 'react'
 
export const ThemeContext = createContext({})
 
export default function ThemeProvider({
  children,
}: {
  children: React.ReactNode
}) {
  return <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>
}
```
then import into Server Component
```tsx
import ThemeProvider from './theme-provider'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <ThemeProvider>{children}</ThemeProvider>
      </body>
    </html>
  )
}
```
* Third party Component
  * When import the 3rd party that relies on client-only feature but yet doesn't have ***'use client'*** and we want to use it in the server component we need to wrap it.
  * For example, we have the ***Carousel*** imported from the ***acme-carousel***. If you use it in the Client Component then it fine.
```tsx
'use client'
 
import { useState } from 'react'
import { Carousel } from 'acme-carousel'
 
export default function Gallery() {
  const [isOpen, setIsOpen] = useState(false)
 
  return (
    <div>
      <button onClick={() => setIsOpen(true)}>View pictures</button>
      {/* Works, since Carousel is used within a Client Component */}
      {isOpen && <Carousel />}
    </div>
  )
}
```

It work inside another 'use client' component. But not the Server Component, here is how we fix it
```tsx
'use client'
 
import { Carousel } from 'acme-carousel'
 
export default Carousel
```
now we can use inside the Server Component
```tsx
import Carousel from './carousel'
 
export default function Page() {
  return (
    <div>
      <p>View pictures</p>
      {/*  Works, since Carousel is a Client Component */}
      <Carousel />
    </div>
  )
}
```
