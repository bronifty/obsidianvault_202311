- [working repo](https://github.com/bronifty/vercel-remix-vite-deploy-v2-mdx.git)
- [remix.run/docs future vite](https://remix.run/docs/en/main/future/vite)

> Remove remix.config.js if you are migrating otherwise it will confuse the vercel build API

### Required Vercel Deployment Artifacts
1. vercel.json
	- source is inside the build output directory, which is defined in the vercel settings to match the vite.config.ts file "assetsBuildDirectory" property's value (default is public/build)
	- destination is a custom express server defined in vercel's api folder, which imports the result of the server build, which is also defined in vite.config.ts as "serverBuildPath", which defaults to: build/index.js. 
		- destination points to: api/server.mjs, which points to: serverBuildPath in vite.config.ts

```json
{
  "rewrites": [
    {
      "source": "/((?!assets/).*)",
      "destination": "/api/server.mjs"
    }
  ]
}
```
2. api/server.mjs
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

// const port = 3000;
// app.listen(port, () => console.log("http://localhost:" + port));
export default app;
```
3. vite.config.ts
	- some or all of this (work in progress as you add feats)
```ts
// vite.config.ts
import { unstable_vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";
// import svgr from "vite-plugin-svgr";
import mdx from "@mdx-js/rollup";
import remarkFrontmatter from "remark-frontmatter";
import remarkMdxFrontmatter from "remark-mdx-frontmatter";
// import highlight from "rehype-highlight";

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
    }),
    tsconfigPaths(),
  ],
});

```

4. package.json
	- replace the package.json wholesale with this one
```json
{
  "name": "my-remix-app",
  "private": true,
  "sideEffects": false,
  "type": "module",
  "scripts": {
    "clean:dist": "rm -rf node_modules/@remix-run/dev/dist",
    "init:dist": "cp -r ./@remix-run/dev/dist/ node_modules/@remix-run/dev/",
    "initialize": "npm run clean:dist && npm run init:dist",
    "build": "echo \"make sure you set the vercel build output dir to public/build; the 'vercel-build' command will run in prod and deploy to vercel \"",
    "vercel-build": "npm run initialize && vite build && vite build --ssr",
    "dev": "node ./api/server.mjs",
    "start": "cross-env NODE_ENV=production node ./api/server.mjs",
    "local": "cross-env NODE_ENV=production node ./server.mjs",
    "local-dev": "node ./server.mjs",
    "typecheck": "tsc"
  },
  "dependencies": {
    "@remix-run/css-bundle": "^2.2.0",
    "@remix-run/express": "^2.2.0",
    "@remix-run/node": "^2.2.0",
    "@remix-run/react": "^2.2.0",
    "express": "^4.18.2",
    "isbot": "^3.6.8",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@mdx-js/rollup": "^3.0.0",
    "@remix-run/dev": "^2.2.0",
    "@remix-run/eslint-config": "^2.2.0",
    "@types/express": "^4.17.20",
    "@types/react": "^18.2.20",
    "@types/react-dom": "^18.2.7",
    "cross-env": "^7.0.3",
    "eslint": "^8.38.0",
    "remark-frontmatter": "^5.0.0",
    "remark-mdx-frontmatter": "^4.0.0",
    "typescript": "^5.1.6",
    "vite": "^4.5.0",
    "vite-tsconfig-paths": "^4.2.1"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}

```

