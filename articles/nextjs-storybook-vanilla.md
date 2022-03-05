---
title: "NextjsとStorybookとvanilla-extract"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nextjs", "Storybook", "vanilla-extract"]
published: false
---

この記事を参考にやってみましたが、上手く行かなかったところがあるので、備忘録がてらに描きます。
https://storybook.js.org/blog/get-started-with-storybook-and-next-js/

`npx sb init --builder webpack5`

`yarn storybook`

`Error: Cannot find module 'webpack/lib/util/makeSerializable.js'`
のエラー。

https://github.com/storybookjs/storybook/issues/15336

GitHub に issue があります。

```
module.exports = {
  typescript: { reactDocgen: false },//これを追加
  "stories": [
    "../stories/**/*.stories.mdx",
    "../stories/**/*.stories.@(js|jsx|ts|tsx)"
  ],
  "addons": [
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@storybook/addon-interactions"
  ],
  "framework": "@storybook/react",
  "core": {
    "builder": "webpack5"
  }
}
```

https://vanilla-extract.style/

Next に入れるとき
https://vanilla-extract.style/documentation/setup/#nextjs

`yarn add @vanilla-extract/css @vanilla-extract/babel-plugin @vanilla-extract/next-plugin`

```next.config.js
const {
  createVanillaExtractPlugin
} = require('@vanilla-extract/next-plugin');
const withVanillaExtract = createVanillaExtractPlugin();

/** @type {import('next').NextConfig} */
const nextConfig = {};

module.exports = withVanillaExtract(nextConfig);

```

https://github.com/seek-oss/vanilla-extract/issues/4#issuecomment-810842869
