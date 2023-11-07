- let's start with the templates - create-remix@latest standard and unstable-vite

```sh
npx create-remix@latest
npx create-remix@latest --template remix-run/remix/templates/unstable-vite-express
```

- the first thing i want to do is cp remix.config.js vite.config.ts and add some vite plugin specific configs as well as yarn add the plugin as a dev dependency. also we need to make sure remix-run node react and express are installed as runtime deps

```sh
yarn add -D @remix-run/dev
yarn add @remix-run/express
```

```ts
// vite.config.ts
import { unstable_vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";
// import { mdx } from "@mdx-js/rollup";

export default defineConfig({
  plugins: [
    remix({
      ignoredRouteFiles: ["**/.*"],
      appDirectory: "app",
      assetsBuildDirectory: "public/build",
      publicPath: "/build/",
      serverBuildPath: "build/index.js",
    }),
  ],
});

```

