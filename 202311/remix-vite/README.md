- [remix docs 💿 vite ⚡️ plugin](https://remix.run/docs/en/main/future/vite)
- [remix 💿 vite ⚡️](https://www.youtube.com/watch?v=B_vIp4xETl4)
### Vite Templates

- 2 templates if you don't want to migrate from a current remix app

1. standard vite plugin

```sh
npx create-remix@latest --template remix-run/remix/templates/unstable-vite
```

2. custom server

```
npx create-remix@latest --template remix-run/remix/templates/unstable-vite-express
```
### Migration Steps from Remix Compiler to Vite Compiler
 
- rename remix.config.js to vite.config.ts

```sh 
mv remix.config.js vite.config.ts
```
```ts
import { unstable_vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [
    remix({
      ignoredRouteFiles: ["**/.*"],
    }),
  ],
});
```

- swap the @remix-run/dev/dist folder out for the custom built one in the remix fork monorepo with the update to the vite plugin compiler setting
```sh
rm -rf node_modules/@remix-run/dev/dist
cp -r /home/bronifty/codes/remixDir/remix/build/node_modules/@remix-run/dev/dist node_modules/@remix-run/dev
```

![](./media/vite-plugin.png)




```sh
yarn add -D vite-plugin-svgr
```

