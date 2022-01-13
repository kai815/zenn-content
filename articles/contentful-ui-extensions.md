---
title: "ContentfulのUI Extensionsの紹介"
emoji: "💭"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["Contentful"]
published: false
---

## UI Extensions とは

contenful の入力を手助けしてくれる

JSON Editor

Youtube ID

非エンジニアが使ってもわかりやすくしたいという要望に応えられる。

JSON の入力がしんどい

## 設定の仕方

Settings をクリックして、Extensions（スクショの赤枠）を押す
![](https://storage.googleapis.com/zenn-user-upload/5f4c9daa4000-20220106.png)

すると、以下の画面になります。
ここで、Add extension を押すと Github からインストールするか、新しく作成するかを選べるので、必要な方を選びます。（今回は、Github からの方で説明します。）
![](https://storage.googleapis.com/zenn-user-upload/b5e5b225928b-20220106.png)

Github からの場合、以下のような画面になります。
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
