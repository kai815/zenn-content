---
title: "NextjsとStorybookとstitches"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nextjs", "Storybook", "stitches"]
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

https://github.com/modulz/stitches

https://stitches.dev/blog/using-nextjs-with-stitches

Next のサンプル
https://github.com/vercel/next.js/tree/canary/examples/with-stitches

インストール
`yarn add @stitches/react`

```
import { createStitches } from '@stitches/react';

export const { styled, getCssText } = createStitches({
  theme: {
    fonts: {
      system: 'system-ui',
    },
    colors: {
      hiContrast: 'hsl(206,10%,5%)',
      loContrast: 'white',
    },
    fontSizes: {
      1: '13px',
      2: '15px',
      3: '17px',
    },
  },
});
```

stitches は意外と入れやすかったかも？
