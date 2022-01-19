---
title: "ContentfulのUI Extensionsの紹介"
emoji: "💭"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["Contentful"]
published: false
---

仕事で Contentful を使っているのですが、Contentful の UI Extensions という機能を初めて使って、
便利だったのでその紹介です。

## UI Extensions とは

Contentful の Web アプリの基本機能を拡張させてくれるものです。
入力する際のインターフェイスをより使いやすくすることも可能です。

参考リンク
https://www.contentful.com/developers/docs/extensibility/ui-extensions/

例：
・JSON Editor
JSON をそのまま編集させるのでなく、GUI 的に編集できるものです。

・Youtube ID
Youtube のリンクを入力すると、id だけを抽出してくれるものです。

Contentful を使うビジネスサイドの非エンジニアの方
にもよりわかりやすい入力フィールドを作ることも可能です。

## 設定の仕方

ここからは、UI Extensions の設定の仕方を説明します。

1. Settings をクリックして、Extensions（スクショの赤枠）を押します。
   ![](https://storage.googleapis.com/zenn-user-upload/5f4c9daa4000-20220106.png)

すると、以下のような画面になります。
ここで、Add extension を押すと Github からインストールするか、新しく作成するかを選べるので、必要な方を選びます。（今回は、Github からの方で説明します。）
![](https://storage.googleapis.com/zenn-user-upload/b5e5b225928b-20220106.png)

2. Github からの場合、以下のような画面になります。
   ![](https://storage.googleapis.com/zenn-user-upload/6431119ac555-20220107.png)

そこに使いたい extensions の Github の extensions.json の URL を入力する必要があります。

例えば、
youtube-id という extensions を入れたいなら、
`https://github.com/contentful/extensions/blob/master/samples/youtube-id/extension.json`
という URL を入れます。

[https://github.com/contentful/extensions/tree/master/samples](https://github.com/contentful/extensions/tree/master/samples)

にサンプルがあります。

入力すると以下のような画面になります。
![](https://storage.googleapis.com/zenn-user-upload/28795cc9d6fa-20220107.png)

設定画面の説明

FieldTypes はその名の通り、どんなフィールドのタイプにこの Extensions を適用するかです。

Legacy sidebar extension の Yes, this is a legacy sidebar extension のチェックは不要です。
（付けるとサイドバーに表示されます。）

Hosting も元々チェックされてる Hosted by Contentful (srcdoc)でいいです。
もしホスティングを変更したければ、指定してください。

Code(required)に実際にどんな挙動をするかを記述します。
Github からインストールした場合は、元々記述がありますので、必要に応じて、値を変更します。

元々の入力画面
![](https://storage.googleapis.com/zenn-user-upload/56e5349bbbe0-20220114.png)

フィールド側での設定
![](https://storage.googleapis.com/zenn-user-upload/d83c9eca3e42-20220115.png)

実際に設定した画面は以下です。Youtube の動画 URL を貼り付けると、動画の id のみが抽出されてます。
![](https://storage.googleapis.com/zenn-user-upload/70a24efd489e-20220116.gif)

Youtube の動画の URL を上げるだけで、id を抽出してくれるので、「id ってどれ？」みたいなやりとりも減りそうですね。

### JSON Editor の設定

先ほどは YoutubeId を例に紹介にしました。
JSON Editor の場合を紹介します。
