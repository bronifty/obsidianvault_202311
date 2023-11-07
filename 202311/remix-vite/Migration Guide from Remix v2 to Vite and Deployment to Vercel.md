- [working repo](https://github.com/bronifty/vercel-remix-vite-deploy-v2-mdx.git)
- [remix.run/docs future vite](https://remix.run/docs/en/main/future/vite)

### Required Vercel Deployment Artifacts
1. vercel.json
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
	- deps depend on what you're using, but you'll need vite at the very least if it's not installed as well as the plugin from @remix-run/dev, but there is a GOTCHA! if you're using MDX because the current @remix-run/dev package will build successfully, but the render will fail on any mdx consumer pages; so you'll either have to wait until the fix comes in or use my repo with the custom module included which has incorporated the fix. that is reflected here in the scripts for initialize
```json
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
```

