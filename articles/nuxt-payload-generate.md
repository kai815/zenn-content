---
title: "Nuxtjsのgenerateの改善~payloadちゃんと使えてますか？"
emoji: "🪤"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nuxt", "Vue"]
published: false
---

Nuxt の generate の改善を職場ですることがあったので、そこで得た知見を備忘録的に書いてきます。

## Qiita の API について

Qiita api を使って実際にやってみます。
(Zenn に記事書いてるのに Qiita で申し訳ないですが、サンプルにちょうどよかったので。)

https://qiita.com/api/v2/docs

一覧の取得のドキュメントはこちら
https://qiita.com/api/v2/docs#get-apiv2items

特定の記事の取得
https://qiita.com/api/v2/docs#get-apiv2itemsitem_id

100 件の記事を取得するには以下のような感じす。
`https://qiita.com/api/v2/items?page=1&per_page=100`

## axios を使う

axios の install

```
$ yarn add axios
```

API のを叩くために axios を利用します。

## Nuxt の通常バージョンで generate する

nuxt-link にあるものは自動でビルドしてくれるようです。
https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#crawler

まずは以下のようなソースでビルドしてみます。

```vue:pages/post/index.vue
<template>
<div>
  一覧
  <ul>
    <li v-for="post in posts" :key="post.id">
      <nuxt-link :to="`/post/${post.id}`">
      {{post.title}}
      </nuxt-link>
    </li>
  </ul>
</div>
</template>

<script>
import axios from 'axios';
export default {
  async asyncData() {
    return await axios.get('https://qiita.com/api/v2/items?page=1&per_page=100',
      {
         headers: {
           Authorization: `Bearer 認証トークン`,
         }
      }
    )
    .then(response => {
      return {
        posts: response.data
      }
    })
    .catch(error => {
      console.log(error)
    })
  },
}
</script>

```

```vue:pages/post/_id.vue
<template>
<div>
  {{post.title}}
</div>
</template>

<script>
import axios from 'axios';
export default {
  async asyncData({ payload, params }){
    const post = payload !== undefined ? payload :(await axios.get(`https://qiita.com/api/v2/items/${params.id}`,
      {
         headers: {
           Authorization: `Bearer 認証トークン`,
         }
       }
    )).data
    return { post }
  }
}
</script>
```

## payload を使う

https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#routes

https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#payload-%E3%81%AB%E3%82%88%E3%82%8B%E5%8B%95%E7%9A%84%E3%83%AB%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E7%94%9F%E6%88%90%E3%81%AE%E9%AB%98%E9%80%9F%E5%8C%96

## payload は undefined になる

残念ながらこれは解消できないみたいです

https://github.com/nuxt/nuxt.js/issues/8676

yarn generate してから start する必要があります。

これがめんどくさい

これは Nuxt にも対応して欲しいものですね。
