---
theme: bricks
title: Hono with database on browser playground
class: text-center
highlighter: shiki
drawings:
  persist: false
transition: slide-left
mdc: true
---

# The Hacks in the Hanabi.REST playground.

How to develop a Workers Like execution environment in the browser.

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <a href="https://x.com/hanabi_rest" target="_blank" alt="GitHub"
    title="Open in Twitter"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-twitter />
  </a>
  <a href="https://hanabi.rest/" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
   <twemoji-sparkler />
  </a>
</div>

---
layout: items
cols: 3
---

# Members

::items::

<img src="https://avatars.githubusercontent.com/u/91340399?v=4" alt="yutakobayasi" class="rounded-full border w-36 h-36 mx-auto" />
<img src="https://avatars.githubusercontent.com/u/71250660?v=4" alt="inaridiy" class="rounded-full border w-36 h-36 mx-auto" />
<img src="https://avatars.githubusercontent.com/u/67491867?v=4" alt="moons14" class="rounded-full border w-36 h-36 mx-auto" />

<h2 class="text-2xl!">Yuta Kobayashi</h2>
<h2 class="text-2xl!">Inaridiy</h2>
<h2 class="text-2xl!">Moons14</h2>

[<carbon-logo-github class="mr-1" />yutakobayashidev](https://github.com/yutakobayashidev)

[<carbon-logo-github class="mr-1" />inaridiy](https://github.com/inaridiy)

[<carbon-logo-github class="mr-1" />moons14](https://github.com/moons-14)

15yo, I like programming, languages, music and space.

18yo, engineer playing at LLM SFC24, seccmp23 inaridiy.eth

17yo, engineer, LLM, seccamp23 @moons_dev(X)

---

# About Hanabi.rest

Build a REST API from prompt and screenshots with LLM.

- 🏗️ **Prompt to REST** - Build a REST API from prompt and screenshots with LLM.
- 🧪 **Browser Playground** - Test and Develop APIs in the browser.
- 🚀 **One Click Deploy** - Deploy APIs to Cloudflare Workers with a one click.
- 🎨 **Local Build** - Clone API in your PC by CLI.

<div class="abs-br m-6 flex gap-2">
  <img src="https://hanabi.rest/landing.jpg" alt="Hanabi.rest" class="w-[360px] border rounded-md shadow-sm" />
</div>

---

# Tech Stack Overview

```mermaid
graph TD
    subgraph "Vercel"
        subgraph "Frontend"
            A[Next.js App Router]
            B[Jotai]
            C[shadcn/ui]
        end
    end

    subgraph "Cloudflare"
        subgraph "Backend"
            subgraph "Main Backend"
                E[Hono.js]
                G[Cloudflare Workers]
                F[Drizzle ORM]
            end

            subgraph "Generation Durable Object"
                J[Durable Object]
                O[API Generation Logic]
            end

            subgraph "Storage"
                H[D1 SQLite Database]
                I[R2 Object Storage]
            end
        end
    end

    subgraph "External Services"
        N[Claude Haiku LLM]
        K[Clerk]
    end

    subgraph "Development & Deployment"
        L[pnpm workspace]
        M[Github Actions]
    end

    A --> B
    A --> C
    G -->|Hosts| E
    E -->|ORM| F
    F -->|Connects to| H
    G -->|Calls during API generation| J
    J -->|Executes| O
    G -->|Stores data| I
    G -->|Calls LLM| N
    J -->|Calls LLM| N
    K --> A
    K --> E
    L --> A
    L --> E
    M --> Vercel
    M --> Cloudflare

    classDef environment fill:#f0f0f0,stroke:#333,stroke-width:4px;
    classDef frontend fill:#f9f,stroke:#333,stroke-width:2px;
    classDef backend fill:#bbf,stroke:#333,stroke-width:2px;
    classDef durable fill:#ffb,stroke:#333,stroke-width:2px;
    classDef storage fill:#bff,stroke:#333,stroke-width:2px;
    classDef external fill:#fdb,stroke:#333,stroke-width:2px;
    classDef devops fill:#fbb,stroke:#333,stroke-width:2px;

    class Vercel,Cloudflare environment;
    class A,B,C frontend;
    class E,F,G backend;
    class J,O durable;
    class H,I storage;
    class N,K external;
    class L,M devops;
```

Genereated by Claude3.5

---
layout: cover
---

# Demo

<div class="abs-br m-6 flex gap-2">
  <img src="/qrcode_hanabi.rest.png" alt="Hanabi.rest" class="w-[320px] border rounded-md shadow-sm" />
</div>

---
layout: section
---

# Today's Topic

::right::

##  Hanabi's Playground
<img src="/foucus.png" alt="Hanabi.rest" class="w-[480px] border rounded-md shadow-sm" />

---
layout: section
---

# Today's Topic

::right::

## Execute Hono with D1 in the browser
How to run Hono with D1 in your browser

## Build with external packages
Use esbuild to bundle with **external packages** into a single file

## Typescript Editor
Bundle the npm package **type definitions** into a single file and insert it into the editor

---

# Hono is Just a Function
Execute Hono with D1 in the browser#1

<div grid="~ cols-2 gap-2" m="t-2">

HonoJS APIs are simple functions that take a WebRequest and return a Response. When you pass these functions to modern runtimes like Workers or Deno, they work as web servers.

<img src="/hono-overview.svg" alt="Hono" class="w-full mx-auto border rounded-md shadow-sm" />



</div>

---

# Simple Example
Execute Hono with D1 in the browser#2

If you write code like this, you can create a runtime that works in the browser!

```ts
import { Hono } from "hono";

const app = new Hono();

app.get("/teapot", (c) => c.text("I'm a teapot"), 418)

... // Add more routes

const request = new Request(...);
const response = await app.fetch(request);//Ultra simple
```

---

# SQLite wasm and D1 Wrapper
Execute Hono with D1 in the browser#3

The Database is a SQLiteWasm(@sqlite.org/sqlite-wasm) covered with a Wrapper that makes it look like a D1.

```ts
export class D1Wrapper {
  private sqlite: Database;
  private stmt: { sql: string; binds?: SqlValue[] } | null = null;

  constructor(sqlite: Database) {
    this.sqlite = sqlite;
  }

  ....

  all() {
    const results = this.sqlite.exec({
      sql: this.stmt?.sql || "",
      bind: this.stmt?.binds || [],
      returnValue: "resultRows",
      rowMode: "object",
    });

    return { results };
  }
}
```

---

# Binding the database to Hono in the browser
Execute Hono with D1 in the browser#4

Binding by passing the DB as the second argument to the fetch method of Hono

```ts
import SQLite from "@sqlite.org/sqlite-wasm";

const db = new SQLite.oo1.DB();

app.get("/db", async (c) => {
  c.env.DB.prepare("SELECT * FROM table").all(); // Use the database
});

const request = new Request(...);
const response = await app.fetch(request, {
  DB: new D1Wrapper(db), // Pass the database
});
```

---

# Building with esbuild
Build with external packages#1


Hanabi uses esbuild to build its code. In doing so, external packages are also bound to a single file by retrieving them from esm.sh and bundling them
<div grid="~ cols-2 gap-2" m="t-2">

```ts
import type { Plugin } from "esbuild-wasm";

export const httpPlugin: Plugin = {
  name: "http",
  setup(build) {
    build.onResolve({ filter: /.*/ }, (args) => ({
      path: new URL(args.path, "https://esm.sh").toString(),
      namespace: "http-url",
    }));

    build.onLoad({ filter: /.*/, namespace: "http-url" }, async (args) => {
      const contents = await fetch(args.path).then((res) => res.text());
      return { contents };
    });
  },
};
```

```ts
import * as esbuild from "esbuild-wasm";
import { httpPlugin } from "./esbuild-http-plugin";

export const compile = async (code: string): Promise<string> => {
  await initEsbuild();

  const importSource = `import { jsx, Fragment } from 'https://esm.sh/hono/jsx'\n`;
  const transformed = await esbuild.build({
    ...,
    plugins: [httpPlugin], // Add the plugin
  });
  const output = transformed?.outputFiles?.[0].text;
  if (!output) throw new Error("Failed to compile", { cause: transformed?.errors });
  return output;
};
```

</div>

---

# About Typescript Editor
Typescript Editor#1

The editor of Hanabi's Playground is monaco-editor.

However, like DenoLSP, it has the ability to automatically insert type information from external packages

![Monaco Editor](/sc.png)

---

# x-typescript-types
Typescript Editor#2

When you get a package with `esm.sh`, it will be given the header `x-typescript-types`.
=> However, if the file is not bundled with a type file like Hono, only the surface layer can be read.

![x-typescript-types](/x-typescript-types.png)

```ts {1}
...
import { Hono } from './hono.d.ts';
...
export type { Env, ErrorHandler, Handler, MiddlewareHandler, Next, NotFoundHandler, ValidationTargets, Input, Schema, ToSchema, TypedResponse, } from './types.d.ts';
...
export type { Context, ContextVariableMap, ContextRenderer, ExecutionContext } from './context.d.ts';
...
export { Hono };
```

---

# Bundle .d.ts with cloudflare workers
Typescript Editor#3

Using Typescript's declare module syntax, dts files are bundled using a simple algorithm

```ts
export const getBundledDts = async (module: string) => {
  const indexDotDtsUrl = await getIndexeDtsUrl(module);
  if (!indexDotDtsUrl) return "";

  let queue = [indexDotDtsUrl];
  const dtsFiles: Record<string, string> = {};
  while (true) {
    ...
  }

  // バンドルされた型定義を生成
  let formatted = Object.entries(dtsFiles)
    .map(([url, dts]) => `declare module "${replacePath(url)}"{${dts}}`)
    .join("\n");
  formatted += `declare module "${module}" {export * from "${replacePath(indexDotDtsUrl)}"}`;

  return formatted;
};
```

---
layout: fact
---




# Thank you for listening!