---
title: "ViteをNuxtで試した"
emoji: "💨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nuxt", "Vue", "Vite"]
published: false
---

最近話題となっている [Vite](https://vitejs.dev/) を Nuxt で試してみました。
調べてみると、[nuxt/vite](https://github.com/nuxt/vite) というのがあるんですね。
そちらを試したことを書いていきます。

## Nuxt に Vite を導入する

#### Nuxt のプロジェクトを作成

```
$ yarn create nuxt-app normal-nuxt
```

Vite を試したいだけなので、とりあえず最小限の構成にしました。

```diff js:nuxt.config.js
  buildModules: [
+    'nuxt-vite'
 ],
```

buildModules に`nuxt-vite`を追記します。
最小限のプロジェクトの場合導入はこれだけです。

## 比べてみる

### 通常の Nuxt

![通常の Nuxt](https://storage.googleapis.com/zenn-user-upload/f6348a2548041afe1b7d9095.gif)

### vite の Nuxt

![vite の Nuxt](https://storage.googleapis.com/zenn-user-upload/4a7b27ccf5964213e130ea7c.gif)
