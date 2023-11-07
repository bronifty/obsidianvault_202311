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
