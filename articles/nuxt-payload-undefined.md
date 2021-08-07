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

yarn dev でも出てきた欲しい気もしますがね。

ビルド速度の改善に役立ちます。

約半分になりました。
payload を使うことで、各ページの生成時に API にアクセスする必要がなくなるので、generate がすごく速くなります。
