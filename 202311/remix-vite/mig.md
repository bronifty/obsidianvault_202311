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
import mdx from "@mdx-js/rollup";
import remarkFrontmatter from "remark-frontmatter";
import remarkMdxFrontmatter from "remark-mdx-frontmatter";
import tsconfigPaths from "vite-tsconfig-paths";
import svgr from "vite-plugin-svgr";

export default defineConfig({
  plugins: [
    remix({
      ignoredRouteFiles: ["**/.*"],
      appDirectory: "app",
      assetsBuildDirectory: "public/build",
      publicPath: "/build/",
      serverBuildPath: "build/index.js",
    }),
    mdx({
      remarkPlugins: [remarkFrontmatter, remarkMdxFrontmatter],
    }),
    svgr(),
    tsconfigPaths(),
  ],
});
```

### Package Deps
```json
"dependencies": {
    "@remix-run/express": "^2.2.0",
    "@remix-run/node": "^2.2.0",
    "@remix-run/react": "^2.2.0",
    "cross-env": "^7.0.3",
    "isbot": "^3.6.8",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
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
  ```

### Package Scripts
```json
"scripts": {
    "clean:dist": "rm -rf node_modules/@remix-run/dev/dist",
    "init:dist": "cp -r ../@remix-run/dev/dist/ node_modules/@remix-run/dev/",
    "build": "vite build && vite build --ssr",
    "dev": "node ./api/server.mjs",
    "start": "cross-env NODE_ENV=production node ./api/server.mjs",
    "typecheck": "tsc"
},
```

### MDX Consumer
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

```ts
export default function Test() {
  return (
    <>
      <div>Test</div>
    </>
  );
}
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

### env.d.ts

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

### svgr

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

