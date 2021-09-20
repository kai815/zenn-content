---
title: "NuxtのSSGのgenerateの改善~payloadちゃんと使えてますか？~"
emoji: "🪤"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nuxt", "Vue", "JavaScript"]
published: true
---

Nuxt の generate の改善を職場ですることがあったので、そこで得た知見を備忘録的に書いてきます。

## 環境

nuxt v2.15.7

## Qiita の API について

Qiita api を使って実際にやってみます。
(Zenn に記事書いてるのに Qiita で申し訳ないですが、サンプルにちょうどよかったので。)

Qiita の API のドキュメントは[こちら](https://qiita.com/api/v2/docs)

一覧の取得のドキュメントは[こちら](https://qiita.com/api/v2/docs#get-apiv2items)

特定の記事の取得のドキュメントは[こちら](https://qiita.com/api/v2/docs#get-apiv2itemsitem_id)

100 件の記事を取得するには以下のような感じす。
`https://qiita.com/api/v2/items?page=1&per_page=100`

### 題材

QiitaAPI で取得した記事のタイトルの一覧画面
![](https://storage.googleapis.com/zenn-user-upload/e40b7c3a4222f65592740956.png)

QiitaAPI で取得した各詳細記事のタイトルのみの画面
![](https://storage.googleapis.com/zenn-user-upload/f118ea865b98a0416282051e.png)

というすごくシンプルなものです。

## axios を使う

axios の install

```
$ yarn add axios
```

API のを叩くために axios を利用します。

## Nuxt の通常バージョンで generate する

nuxt-link にあるものは自動でビルドしてくれるようです。
[参考ドキュメント](https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#crawler)

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
  async asyncData({app}) {
    return await axios.get('https://qiita.com/api/v2/items?page=1&per_page=100',
      {
        headers: {
          Authorization: `Bearer ${app.$config.token}`,
        }
      }
    )
    .then(response => {
      return {
        posts: response.data
      }
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
  async asyncData({app,params}){
    return await axios.get(`https://qiita.com/api/v2/items/${params.id}`,
      {
        headers: {
          Authorization: `Bearer ${app.$config.token}`,
        }
      }
    ).then(response=>{
      return {
        post: response.data
      }
    })
  }
}
</script>
```

### 503 エラーになる

この状態で`yarn generate`をすると、下記のように 503 エラーが出ます。
![](https://storage.googleapis.com/zenn-user-upload/c792934490aeb9227c6f0db1.png)
各記事ページの生成時にも API のリクエストがいきますし、リクエストの間を空けないためそうなっているのでしょう。

interval を使います。
[interval プロパティのドキュメント](https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#interval)

`nuxt.config` に `interval:100` を指定します。

```diff js:nuxt.config.js
export default {
  //他のところは割愛
+ generate:{
+   interval: 100,
+ }
}
```

generate の結果です。
![](https://storage.googleapis.com/zenn-user-upload/b66f09c5135726275872f3ac.png)

一応ソースコードは[こちら](https://github.com/kai815/try-vite/tree/payload/normal-nuxt)にあげてます。

## payload を使う

nuxt-link が貼られている path は`nuxt.config`の`generate`プロパティに記載してくても、ページ生成はされます。
[参考](https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#routes)

ただ、`payload`を使うためには、`generate`プロパティに記載する必要があります。

[payload とは](https://nuxtjs.org/ja/docs/configuration-glossary/configuration-generate#payload-%E3%81%AB%E3%82%88%E3%82%8B%E5%8B%95%E7%9A%84%E3%83%AB%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E7%94%9F%E6%88%90%E3%81%AE%E9%AB%98%E9%80%9F%E5%8C%96)

詳細記事のルーティングを作るために API にアクセスしたときに、
そこで 100 件分の記事のデータを取るなら、
そのデータを payload に入れて、
各記事のページでも `payload` を使うことで API のアクセスを減らせます。

`nuxt.config` に以下のようなコードを追記します。

```diff js:nuxt.config.js
+ import axios from 'axios';

export default {
  generate:{
+    async routes(){
+     const posts = (await axios.get('https://qiita.com/api/v2/items?page=1&per_page=100',
+        {
+          headers: {
+            Authorization: `Bearer ${process.env.TOKEN}`,
+          }
+        })
+      ).data

+      const postRoutes = posts.map(post=>{
+        return{
+          route:`/post/${post.id}`,
+          payload:post
+        }
+      })
+      return [
+        {
+          route:"/post",
+          payload: posts
+        },
+        ...postRoutes
+      ]
+    }
  }
}
```

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
  async asyncData({ app, payload }) {
    //payloadを使う
    const posts = payload !== undefined ? payload
      : (await axios.get('https://qiita.com/api/v2/items?page=1&per_page=100',
        {
          headers: {
            Authorization: `Bearer ${app.$config.token}`,
          }
        }
        )).data
    return { posts }
  }
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
  async asyncData({app, params, payload}){
    //payloadを使う
    const post = payload !== undefined ? payload
      : (await axios.get(`https://qiita.com/api/v2/items/${params.id}`,
        {
          headers: {
            Authorization: `Bearer ${app.$config.token}`,
          }
        }
        )).data
    return {post}
  }
}
</script>
```

今回の場合であれば、`payload` によって API のアクセスは 1 度で済むようになったので、
`interval` もセットする必要がなくなりました。

`generate` にかかる時間も速くなりました。(22.02→12.27)
![](https://storage.googleapis.com/zenn-user-upload/d2abe7e56e87db03c17334eb.png)

ここまでのソースは[こちら](https://github.com/kai815/try-vite/tree/use-payload/normal-nuxt)です

## payload は dev では undefined になる

`yarn dev` では `payload`は`undefined` になるので、注意する必要があります。

payload 自体の動作確認は`yarn generate` してから `yarn start` する必要があります。

## まとめ

Nuxt の SSG の generate 時には payload を上手く使うことで、不要な API のアクセスを減らすことができます。
もし、generate に時間かかっているなら、不要にデータを取得してないか見直してみるといいかもしれません。
