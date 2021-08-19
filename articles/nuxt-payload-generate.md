---
title: "Nuxtjsのgenerateの速度改善に役立つpayloadについて"
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

自分が行ったプロジェクトでは 14 分くらいかかっていたのが、7 分程度になりました。

では実際に見てみましょう。

quita api を使って実際にやってみます。

https://qiita.com/api/v2/docs

axios の install

```
$ yarn add axios
```
