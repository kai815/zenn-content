---
title: "SvelteKit,Newtでブログを作りVercelにデプロイする"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Svelte", "SvelteKit", "TypeScript", "Vercel", "Newt"]
published: true
---

SvelteKit の 1.0 が先日リリースされましたね。
https://svelte.jp/blog/announcing-sveltekit-1.0

そこで試しに SvelteKit と[Newt](https://www.newt.so/)というヘッドレス CMS で、
簡単なブログを作り、Vercel にデプロイするまでやってみましたのでそちらについて書いていきたいと思います。

この記事は[Svelte のカレンダー | Advent Calendar 2022](https://qiita.com/advent-calendar/2022/svelte) - Qiita の 25 日目となる投稿です。(ぎりぎり滑り込みました。)

### 前提

Node.js のバージョンは v18.12.1 です。

## 環境構築

まずはドキュメントに沿ってインストールしていきます。

```
npm create svelte@latest try-svelte-kit
```

try-svelte-kit のところは任意で良いです。

すると色々と質問されるのでそちらに回答していきます。

```
// どのtemplateを使うか聞かれてます(Skeleton projectを選びました)
Which Svelte app template? › - Use arrow-keys. Return to submit.
    SvelteKit demo app
❯   Skeleton project - Barebones scaffolding for your new SvelteKit app
    Library skeleton project
```

```
// TypeScriptの型チェックを追加するか聞かれてます(Yesと答えました)
Add type checking with TypeScript? › - Use arrow-keys. Return to submit.
    Yes, using JavaScript with JSDoc comments
❯   Yes, using TypeScript syntax
    No
```

```
// ESLintを入れるか聞かれてます(Yesにしました)
Add ESLint for code linting? › No / Yes
```

```
// Prettierを入れるか聞かれてます(Yesにしました)
Add Prettier for code formatting?
```

```
// Playwrightを入れるか聞かれてます(Noにしました)
 Add Playwright for browser testing? › No / Yes
```

```
// Vitestを入れるか聞かれてます(Noにしました)
Add Vitest for unit testing? › No / Yes
```

質問に答え終わると以下のように準備ができたと言われるので、
Next steps のところを行なっていきます。

```
Your project is ready!
✔ Typescript
  Inside Svelte components, use <script lang="ts">
✔ ESLint
  https://github.com/sveltejs/eslint-plugin-svelte3
✔ Prettier
  https://prettier.io/docs/en/options.html
  https://github.com/sveltejs/prettier-plugin-svelte#options

Install community-maintained integrations:
  https://github.com/svelte-add/svelte-adders

Next steps:
  1: cd try-svelte-kit
  2: npm install (or pnpm install, etc)
  3: git init && git add -A && git commit -m "Initial commit" (optional)
  4: npm run dev -- --open
```

最後の

```
npm run dev -- --open
```

をやって、以下のようなの画面が出れば OK です。
![初期画面](https://storage.googleapis.com/zenn-user-upload/754a242aaec9-20221225.png)

環境構築はあっという間に終わりました。

## Newt のデータを取得する

続いて Newt のデータの取得のための処理を書いていきます。

### SDK のインストールから取得する関数まで

用意してくれている [SDK](https://github.com/Newt-Inc/newt-client-js) をインストールします。

```
 npm install newt-client-js
```

インストールしたら`.env`,`src/lib/newt.ts`を作成して、編集していきます。

```text:.env
SPACE_ID = <NewtのスペースID>
TOKEN = <Newtのトークン>
APP_UID = <NewtのアプリID>
```

TOKEN はスペース設定の API キーのところから見ることができます。
Newt のスペースなどについては、いかにドキュメントがあります。
https://www.newt.so/docs/space

```ts:src/lib/newt.ts
import { createClient } from "newt-client-js";
import { SPACE_ID, TOKEN, APP_UID } from '$env/static/private';

const client = createClient({
  spaceUid: SPACE_ID,
  token: TOKEN,
  apiType: 'cdn' // You can specify "cdn" or "api".
})
```

.env に追加した環境変数は`$env/static/private`で参照できます。
https://kit.svelte.dev/docs/modules#$env-static-private

### データ取得の処理

```ts:src/lib/newt.ts
const ArticleId = 'article'

//必要なプロパティのみ定義
export type Article = {
  _id: string,
  title: string,
  slug: string,
  body?: string,
}

//記事の一覧を取得
export const getArticleList = async () => {
  const response = await client.getContents<Article>({
    appUid:APP_UID,
    modelUid:ArticleId,
    query:{
      select: ['_id', 'title', 'body', 'slug']
    }
    //limitなどの指定をできるが今回は割愛
  })
  return response
}

//指定された記事を取得
export const getArticle = async ({contentId}:{contentId:string}) =>{
  const article = await client.getContent<Article>({
    appUid:APP_UID,
    modelUid:ArticleId,
    contentId:contentId,
    query:{
      select: ['title', 'body'],
      // body: { fmt: 'text' },
    }
  })
  return article
}
```

`query` の `select` はどの項目を含めるかどうかです。
`query`の`body`の`fmt`を`text`を指定すると、テキストが返ってきて、何も指定しないで、HTML のデータが入ってきます。
今回は HTML のデータをそのまま使います。

参考
https://developers.newt.so/apis/cdn#tag/contents_general/Queries/Select
https://developers.newt.so/apis/cdn#tag/contents_general/Queries/Format

## ページの作成

### 一覧

`src/routes/+page.server.ts`を作成して、編集します。
ここがデータを取得するところになります。

```ts:src/routes/+page.server.ts
import { getArticleList } from "../lib/newt";
import type { PageServerLoad } from "./$types";

export const load: PageServerLoad = async () => {
  return await getArticleList();
};

export const prerender = true;
```

`prerender`を`true`にすることで事前に HTML を生成することができるようです。

`src/routes/+page.svelte`を編集します。

```vue:src/routes/+page.svelte
<script lang="ts">
  import type { PageData } from "./$types";
  export let data: PageData;
</script>

<svelte:head>
  <title>Home</title>
  <meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
  <h1>SevelteKitブログ</h1>
  <ul>
  {#each data.items as item}
    <li>
      <a href="{item._id}">{item.title}</a>
    </li>
  {/each}
  </ul>
</section>

<style>
section {
  display: flex;
  flex-direction: column;
}
</style>
```

`lang=ts`とすることで ts が使えます。`servet.ts`で書いた`load`関数の返り値の値は、
`.svelte`ファイルの`data`に入るようです。

以下のような画面が表示されていれば、成功です。
![一覧画面](https://storage.googleapis.com/zenn-user-upload/65624acfd081-20221225.png)

### 詳細

詳細画面も同じように作っていきます。

`src/routes/[contentId]/+page.server.ts`を作成して、編集してきます。

```ts:src/routes/[contentId]/+page.server.ts
import { getArticle } from "../../lib/newt";
import type { PageServerLoad } from "./$types"

export const load:PageServerLoad = async ({params}) => {
  return await getArticle({contentId:params.contentId});
};

export const prerender = true;
```

`params`の中に`contentId`が入ってくるので、それを使い記事を取得してます。

`src/routes/[contentId]/+page.svelte`でページの見た目を作ってきます。

```vue:src/routes/[contentId]/+page.svelte
<script lang="ts">
  import type { PageData } from "./$types";
  export let data:PageData;
</script>

<svelte:head>
  <title>data.title</title>
  <meta name="description" content="Svelte demo app" />
</svelte:head>
<section>
  <h1>data.title</h1>
  {@html data.body}
</section>
<style>
h1 {
  font-size: 30px;
  font-weight: bold;
}
section :global(h2){
  font-size:20px;
  font-weight:bold;
  text-decoration: underline solid red 2px;
}
</style>
```

`@html`を使って、Newt から HTML が返ってくるをそのまま入れています。
https://svelte.jp/docs#template-syntax-html

最初@html の中の`h2`タグにスタイルが当たらなかったのですが、`:global(h2)`をすることで当たりました。
https://stackoverflow.com/questions/56607606/styling-a-html-tag-of-a-svelte-component-by-ising-the-in-component-style
※global となっているので、他のページにも影響あるのか試してみたのですが、他のページには影響なさそうでした。

以下のような画面が出れば成功です。
![記事詳細](https://storage.googleapis.com/zenn-user-upload/61f511531cf2-20221225.png)

## prerender の違いを試した

先ほど出てきた、`prerender`を試した時の補足です。
true にして、`npm run build`すると `.svelte-kit/output`の下に HTML が生成されているのがわかります。

![prerender true](https://storage.googleapis.com/zenn-user-upload/cb6baf1791c0-20221225.png)

false にすると、`.svelte-kit/output`の下に HTML がないです。
![prerender false](https://storage.googleapis.com/zenn-user-upload/3c01fb1049b6-20221225.png)

## Vercel にデプロイする

Default の adaptor で Vercel はサポートされているらしいので、デプロイのためにやったことはほとんどありませんでした。

プロジェクトを新規作成したら、GitHub から import するだけです。
![import](https://storage.googleapis.com/zenn-user-upload/2b4f37257cf8-20221226.png)

強いて必要な設定というなら、SPACE_ID などの環境変数のセットくらいです。
プロジェクトを選択して、Settings→Environment Variables のところでセットできます。
![vercel環境変数](https://storage.googleapis.com/zenn-user-upload/c03c21d72aa5-20221225.png)

https://kit.svelte.dev/docs/adapters#supported-environments
https://github.com/sveltejs/kit/tree/master/packages/adapter-vercel

余談ですが、Vercel がこんなコースを出してるのですね。（デプロイの仕方調べてる時に知りました。）
https://vercel.com/docs/beginner-sveltekit

## 最後に

いかがだったでしょうか。
思ったよりも詰まることなく、デプロイまでいけましたね。
まだ触ったばっかりですが、route のところや,SG の切り替えとかもわかりやすく、
使いやすいんじゃないかなぁと思いました。
あとドキュメントも Svelte も SvelteKit もわかりやすい印象でした。
今後の進化にも期待したいですね。
