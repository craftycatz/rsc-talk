---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# some information about your slides, markdown enabled
title: Exploring RSCs in the TBeverage rewrite
# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: fade-out
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Exploring RSCs in the TBeverage rewrite

Why we choose them and what we learned

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10"> 
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# The Problem

The legacy TBeverage application is hard to maintain

<v-clicks>

- 🚧 **Proprietary Framework** - built on a janky proprietary MVC framework that lacks support and documentation
- 🧩 **Vanilla JS** - relies on vanilla JavaScript for client-side interactivity, leading to inconsistent and hard-to-manage code
- 🔄 **Repetition** - heavy use of repetitive code without reusable components, resulting in maintenance headaches
- 🔐 **Security Issues** - plagued with bad security practices, making it vulnerable to attacks
- 🧩 **Complex Codebase** - overly complex and verbose code that is difficult to understand and debug
- ❌ **Lack of Standardization** - absence of coding standards and best practices, causing inconsistency across the codebase

</v-clicks>
  <br>
  <br>

Want to see untold horrors? [Source Code](https://sli.dev/guide/why)

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<!--
Here is another comment.
-->

---

# What now?

<!-- https://sli.dev/guide/animations.html#click-animations -->

<div class="justify-center flex items-center h-[80%]">
  <v-clicks every="2">
    <h2>Lets just rewrite the whole thing in React</h2>
    <img class="rotating-image" src="/img/1174949_js_react js_logo_react_react native_icon.png"></img>
  </v-clicks>
</div>

<style>
.rotating-image {
  width: 200px; /* Adjust the size as needed */
  height: auto;
  animation: rotate 12s linear infinite;
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

</style>

---

# So a Single Page App?

<v-clicks depth="2">

### Premise: Single Page Apps are good for UX

- 🚀 **Fast Navigation**
- 🌟 **Rich Interactivity**
- 📊 **State Management**

<br>

### Problems with SPAs

- 🌊 **Cascading Data Waterfalls**
- 🏋️ **Obscenely Large JS Bundles**
- 🚫 **No Progressive Enhancement**

</v-clicks>

---

# CSR data loading

<div>
  <img src="/img/csr-dia-darkk.png">
</div>

---

# CSR data loading

```jsx
import { LoadingSpinner } from "@/components/spinner";
import { AppLayout } from "@/components/layout";
import { useQuery } from "react-query";

export default function Home() {
  const query = useQuery({ queryFn: getProducts });

  return (
    <AppLayout>
      {query.isLoading && <LoadingSpinner />}
      {query.data.map((product) => (
        <ProductCard data={product} key={product.id} />
      ))}
    </AppLayout>
  );
}
```

```ts
import {dbClient} from "../db"

app.get("/api/products", (req, res) => {
  const products = await db.query.products.findMany({
    where: (products, {eq}) => eq(products.active, true),
  });
  res.json{products: products}
})
```

---

<div class="w-full h-full">
  <WithSpinner />
</div>

---

# Client-Server Boundaries

<div class="justify-center h-full flex items-center">
  <img src="/img/boundaries.png" class="h-96">
</div>

---

# SSR data loading

<div class="justify-center h-full flex items-center">
  <img src="/img/SSR-dia.png">
</div>

---

# SSR data loading

````md magic-move {lines: true}
```tsx
// server app
app.get("/api/products", (req, res) => {
  const products = await db.query.products.findMany({
    where: (products, {eq}) => eq(products.active, true),
  });
  res.json{products: products}
})

//client app
export default function Home() {
  const query = useQuery({ queryFn: getProducts });

  return (
    <AppLayout>
      {query.isLoading && <LoadingSpinner />}
      {query.data.map((product) => (
        <ProductCard data={product} key={product.id} />
      ))}
    </AppLayout>
  );
}
```

```jsx
import { dbClient } from "../db";
import { AppLayout } from "@/components/layout";
import { LoadingSpinner } from "@/components/spinner";

export async function getServerSideProps() {
  const products = await db.query.products.findMany({
    where: (products, { eq }) => eq(products.active, true),
  });

  return {
    props: products,
  };
}

export default function Home(data) {
  return (
    <AppLayout>
      {data.map((product) => (
        <ProductCard data={product} key={product.id} />
      ))}
    </AppLayout>
  );
}
```
````

---

# SSR Pitfalls

<v-clicks>

- ⚛️ **Limited Scope**: This strategy only works at the route level, for components at the very top of the tree. We can't do this in any component.
- 🔄 **Framework Variability**: Each meta-framework came up with its own approach. Next.js has one approach, Gatsby has another, Remix has yet another. It hasn't been standardized.
- 📦 **Client Hydration**: All of our React components will always hydrate on the client, even when there's no need for them to do so.
- ⏳ **Long DB Queries**: Long database queries will lead to long times till first paint.

</v-clicks>

---

<div class="w-full h-full">
  <WhitoutSpinner />
</div>

---

# RSCs and Streaming to the rescue!

````md magic-move
```jsx
import { dbClient } from "../db";
import { AppLayout } from "@/components/layout";

export async function getServerSideProps() {
  const products = await db.query.products.findMany({
    where: (products, { eq }) => eq(products.active, true),
  });

  return {
    props: products,
  };
}

export default function Home(data) {
  return (
    <AppLayout>
      {data.map((product) => (
        <ProductCard data={product} key={product.id} />
      ))}
    </AppLayout>
  );
}
```

```jsx
import { dbClient } from "../db";
import { AppLayout } from "@/components/layout";
import { LoadingSpinner } from "@/components/spinner";

async function Homepage() {
  const products = await db.query.products.findMany({
    where: (products, { eq }) => eq(products.active, true),
  });

  return (
    <AppLayout>
      <Suspense fallback={<LoadingSpinner />}>
        {products.map((product) => (
          <ProductCard data={product} key={product.id} />
        ))}
      </Suspense>
    <AppLayout/>
  );
}

export default Homepage;
```
````

---

<div class="w-full h-full">
  <WithSpinner2 />
</div>

---

# Data loading with streaming

<div class="justify-center h-full flex items-center">
  <img src="/img/rsc-dia.png">
</div>

---

# Client-Server Boundaries

<div class="h-[80%] flex justify-center items-center text-xl italic">

\[RSCs are about\] removing all boundaries
<span v-mark.underline>
inessential
</span>
to the problem
<br>
-- <cite>Dan Abramov, React for two computers</cite>

</div>

---

# Client-Server Boundaries

````md magic-move
```jsx{all|4-6}
export const AmountSelect = (props) => {
  return (
    <div className="flex justify-between items-center">
      <button onClickt={() => props.setAmount((prev) => prev + 1)}>+</button>
      <p>{props.amount ?? 0}</p>
      <button onClickt={() => props.setAmount((prev) => prev - 1)}>-</button>
    </div>
  );
};
```

```jsx {1}
"use client";

export const AmountSelect = (props) => {
  return (
    <div className="flex justify-between items-center">
      <button onClickt={() => props.setAmount((prev) => prev + 1)}>+</button>
      <p>{props.amount ?? 0}</p>
      <button onClickt={() => props.setAmount((prev) => prev - 1)}>-</button>
    </div>
  );
};
```
````

---

# Client-Server Boundaries

<div class="justify-center h-full flex items-center">
  <img src="/img/boundaries-rsc.png" class="h-80">
</div>

---

# Client-Server Boundaries

<div class="justify-center h-full flex items-center">
  <img src="/img/boundary-border.png" class="h-80">
</div>

---

# Conclusion

RSCs and Streaming provides obvious benifits over SPAs or classical SSR.

- 🚀 **Faster Time-to-First-Byte (TTFB)**: Streaming SSR allows the server to start sending HTML to the client as soon as possible, reducing the time users wait to see the first part of the content.
- 🌐 **Progressive Rendering**: React Suspense lets you incrementally load and render components, allowing parts of your application to become interactive sooner while other parts continue to load in the background.
- 🔄 **Improved User Experience**: Users start interacting with the page sooner, as the critical parts of the UI are rendered first, providing a smoother and more responsive experience.
- ⚡ **Better Performance Under Load**: Streaming can handle large applications and high traffic more efficiently, as it distributes the load over time rather than trying to deliver everything at once.
- 📡 **Efficient Data Fetching**: React Suspense coordinates data fetching with rendering, making it easier to manage asynchronous data and avoid waterfalls, which can delay rendering.

---

# But wait theres more

things this talk couldn't touch upon: **Server Actions**

- 🌍 **Progressive Enhancement**: Server actions align well with the principles of progressive enhancement, ensuring that basic functionality works for all users, regardless of their device capabilities or JavaScript support.
- 📐 **Web Standards Compliance**: Server actions promote adherence to web standards by encouraging a clear separation between client and server logic, fostering better-structured and more maintainable code.
- 🔄 **Simplified Client-Side Logic**: Offloading complex interactions to the server reduces the complexity of client-side code, making it easier to follow best practices for web development and maintain a clean codebase.
- 📡 **Efficient Data Processing**: By handling data processing on the server, you can leverage server resources more effectively, reducing the need for large client-side libraries and frameworks.
