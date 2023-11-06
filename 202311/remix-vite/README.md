[remix docs 💿 vite plugin](https://remix.run/docs/en/main/future/vite)
[remix 💿 vite ⚡️](https://www.youtube.com/watch?v=B_vIp4xETl4)


- 2 templates
- standard vite plugin
```sh
npx create-remix@latest --template remix-run/remix/templates/unstable-vite
```
- custom server
```
npx create-remix@latest --template remix-run/remix/templates/unstable-vite-express
```

- vite.config.ts with the plugin
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
rm -rf /home/bronifty/codes/remixDir/1-mig-remix-vite/node_modules/@remix-run/dev/dist/
cp -r /home/bronifty/codes/remixDir/remix/build/node_modules/@remix-run/dev/dist /ho
me/bronifty/codes/remixDir/1-mig-remix-vite/node_modules/@remix-run/dev
```

![](./media/vite-plugin.png)


