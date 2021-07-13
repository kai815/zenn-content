---
title: "ViteをNuxtで試した"
emoji: "💨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Nuxt", "Vue", "Vite"]
published: true
---

最近話題となっている [Vite](https://vitejs.dev/) を Nuxt で試してみました。
Vite とは簡単に言ってしまうと、速い、フロントエンドのビルドツールです。
調べてみると、[Nuxt Vite](https://github.com/nuxt/vite) というのがあるんですね。
そちらで試したことを書いていきます。

## Nuxt に Vite を導入する

#### Nuxt のプロジェクトを作成

```
$ yarn create nuxt-app vite-nuxt
```

`vite-nuxt`のところは任意の名前で大丈夫です。
Vite を試したいだけなので、とりあえず最小限の構成にしました。
`target: 'static'`です。

#### Nuxt Vite の導入

まずはインストールします。

```
$ yarn add --dev nuxt-vite
```

インストールしたら、

```diff js:nuxt.config.js
  buildModules: [
+    'nuxt-vite'
 ],
```

buildModules に`nuxt-vite`を追記します。
最小限のプロジェクトの場合導入はこれだけです。

## 比べてみる

それでは通常の Nuxt と Vite を使った Nuxt を比較してみます。
「Welcome to your Nuxt Application」の当たりに注目してください。

### 通常の Nuxt

![通常の Nuxt](https://storage.googleapis.com/zenn-user-upload/f6348a2548041afe1b7d9095.gif)
変更を保存した後に少し時間が経ってから画面に変更が反映されます。

### vite の Nuxt

![vite の Nuxt](https://storage.googleapis.com/zenn-user-upload/4a7b27ccf5964213e130ea7c.gif)
変更を保存した後にすぐ、秒でとかではなく一瞬というかほぼ同時に画面に反映されます。

通常の Nuxt でもそんなに不満はなかったのですが、このくらい速いと開発体験はかなり向上されそうです。

## 本番プロジェクトでの運用

今仕事で使っているプロジェクトでも使ってみようと試したものの、sass 系のエラーがあり導入できませんでした。泣

[Preprocessor dependency "sass" not found. Did you install it?](https://github.com/nuxt/vite/issues/23)とかはバージョン変えればいいだけだったのですが、

他にも色々エラーが出たので見送りました。(2021 年 7 月 11 日時点くらい)

## 最後に

Vite は Nuxt 以外にも使えて、開発体験の向上もかなり見込めるツールなので、これから楽しみだなぁとい印象です。
Storybook とかにも使えたらかなりいいなと。
(あとは、Next もサポートするのかな。)
この記事が誰かの役に立ったら嬉しいです。
