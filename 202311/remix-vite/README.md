- [remix docs 💿 vite ⚡️ plugin](https://remix.run/docs/en/main/future/vite)
- [remix 💿 vite ⚡️](https://www.youtube.com/watch?v=B_vIp4xETl4)

## This is a Vite Plugin Setup and Migration Guide
### For Vercel Deployment Check Below
- [vercel deployment](./vercel)

### Remix Vite Plugin Templates

```sh
npx create-remix@latest
npx create-remix@latest --template remix-run/remix/templates/unstable-vite-express
```

### Vite Config

```ts
// vite.config.ts
import { unstable_vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";
import svgr from "vite-plugin-svgr";
import mdx from "@mdx-js/rollup";
import remarkFrontmatter from "remark-frontmatter";
import remarkMdxFrontmatter from "remark-mdx-frontmatter";
import highlight from "rehype-highlight";

export default defineConfig({
  plugins: [
    remix({
      ignoredRouteFiles: ["**/.*"],
      // serverModuleFormat: "cjs",
      appDirectory: "app",
      assetsBuildDirectory: "public/build",
      publicPath: "/build/",
      serverBuildPath: "build/index.js",
    }),
    mdx({
      remarkPlugins: [remarkFrontmatter, remarkMdxFrontmatter],
      rehypePlugins: [highlight],
    }),
    svgr(),
    tsconfigPaths(),
  ],
});
```

### Package.json

```json
{
  "name": "npx-create-remix-at-latest",
  "private": true,
  "sideEffects": false,
  "type": "module",
  "scripts": {
    "clean:dist": "rm -rf node_modules/@remix-run/dev/dist",
    "init:dist": "cp -r ../@remix-run/dev/dist/ node_modules/@remix-run/dev/",
    "build": "vite build && vite build --ssr",
    "dev": "node ./api/server.mjs",
    "start": "cross-env NODE_ENV=production node ./api/server.mjs",
    "typecheck": "tsc"
  },
  "dependencies": {
    "@mdx-js/mdx": "^3.0.0",
    "@mdx-js/react": "^3.0.0",
    "@remix-run/express": "^2.2.0",
    "@remix-run/node": "^2.2.0",
    "@remix-run/react": "^2.2.0",
    "cross-env": "^7.0.3",
    "highlight.js": "^11.9.0",
    "isbot": "^3.6.8",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "rehype-highlight": "^7.0.0"
  },
  "devDependencies": {
    "@mdx-js/rollup": "^3.0.0",
    "@remix-run/dev": "^2.2.0",
    "@remix-run/eslint-config": "^2.2.0",
    "@types/react": "^18.2.20",
    "@types/react-dom": "^18.2.7",
    "eslint": "^8.38.0",
    "remark-frontmatter": "^5.0.0",
    "remark-mdx-frontmatter": "^4.0.0",
    "typescript": "^5.1.6",
    "vite": "^4.5.0",
    "vite-plugin-svgr": "^4.1.0",
    "vite-tsconfig-paths": "^4.2.1"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

### Types
- env.d.ts
```ts
/// <reference types="@remix-run/node" />
/// <reference types="vite/client" />
/// <reference types="vite-plugin-svgr/client" />

declare module "*.mdx" {
  let MDXComponent: (props: any) => JSX.Element;
  export const frontmatter: any;
  export default MDXComponent;
}
```

### Server

```js
import {
  unstable_createViteServer,
  unstable_loadViteServerBuild,
} from "@remix-run/dev";
import { createRequestHandler } from "@remix-run/express";
import { installGlobals } from "@remix-run/node";
import express from "express";

installGlobals();

let vite =
  process.env.NODE_ENV === "production"
    ? undefined
    : await unstable_createViteServer();

const app = express();

// handle asset requests
if (vite) {
  app.use(vite.middlewares);
} else {
  app.use(
    "/build",
    express.static("public/build", { immutable: true, maxAge: "1y" })
  );
}
app.use(express.static("public", { maxAge: "1h" }));

// handle SSR requests
app.all(
  "*",
  createRequestHandler({
    build: vite
      ? () => unstable_loadViteServerBuild(vite)
      : await import("../build/index.js"),
  })
);

const port = 3000;
app.listen(port, () => console.log("http://localhost:" + port));
export default app;
```


### MDX Support (Import MDX content from a tsx route module consumer)
- app/content/blah.mdx
```mdx
---
title: Hello from MDX
---

export const meta = () => {
  return [{ title: frontmatter.title }];
};

# hello mdx

Another line

```tsx
export default function Test() {
  return (
    <>
      <div>Test</div>
    </>
  );
}
```
```

```

- app/routes/blah.tsx
```tsx
import Component, { frontmatter } from "../content/blah.mdx";

export default function About() {
  return (
    <>
      <h1>frontmatter.title: {frontmatter.title}</h1>
      <Component />
    </>
  );
}
```

### SVG Support with svgr

```tsx
import RemixLogo from "~/assets/remix-logo-glowing-R.svg?react";

export default function Index() {
  return (
    <>
      <RemixLogo width={40} height={40} stroke="red" strokeWidth={15} />
      <div>test</div>
    </>
  );
}
```

-----
## Styles

#### MDX Syntax Highlighting with Rehype

- add highlight.js to style the mdx code
```tsx
import {
  Links,
  LiveReload,
  Meta,
  Outlet,
  Scripts,
  ScrollRestoration,
} from "@remix-run/react";
import "highlight.js/styles/github.css";
import NavigationBar from "./components/NavigationBar";

export default function App() {
  return (
    <html lang="en">
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <Meta />
        <Links />
      </head>
      <body>
        <NavigationBar />
        <Outlet />
        <ScrollRestoration />
        <LiveReload />
        <Scripts />
      </body>
    </html>
  );
}
```


#### Tailwind
- shell command
```sh
yarn -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

- tailwind.config.js
```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./app/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

- tailwind.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

- root.tsx
```tsx
import {
  Links,
  LiveReload,
  Meta,
  Outlet,
  Scripts,
  ScrollRestoration,
} from "@remix-run/react";
import "./tailwind.css";
import "highlight.js/styles/github-dark.css";
import NavigationBar from "./components/NavigationBar";

export default function App() {
  return (
    <html lang="en">
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <Meta />
        <Links />
      </head>
      <body>
        <NavigationBar />
        <Outlet />
        <ScrollRestoration />
        <LiveReload />
        <Scripts />
      </body>
    </html>
  );
}
```

