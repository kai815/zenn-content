---
title: "Nuxtjsのpayloadはdevだとundefinedになる"
emoji: "🪤"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nuxt", "Vue"]
published: false
---

nuxtjs の payload は dev だと undefined になる

https://github.com/nuxt/nuxt.js/issues/8676

yarn dev で試した時には以下のようになる。

payload のデータは出てこない。

generate してから、start すると 出てくる。
