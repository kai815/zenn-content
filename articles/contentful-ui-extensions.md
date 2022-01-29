---
title: "ContentfulのUI Extensionsの紹介"
emoji: "💭"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["Contentful"]
published: false
---

仕事で Contentful を使っているのですが、Contentful の UI Extensions という機能を初めて使って、便利だったのでその紹介です。

## UI Extensions とは

Contentful の Web アプリの基本機能を拡張させてくれるものです。
入力する際のインターフェイスをより使いやすくすることも可能です。

参考リンク
https://www.contentful.com/developers/docs/extensibility/ui-extensions/

例：
・JSON Form Editor
JSON をそのまま編集させるのでなく、GUI 的に編集できるものです。

・Youtube ID
Youtube のリンクを入力すると、id だけを抽出してくれるものです。

Contentful で記事の投稿なのを行うのはビジネスサイドの方が多いかと思います。
UI Extensions を使い、そういった非エンジニアのにもよりわかりやすい入力フィールドを作ることも可能です。

## 設定の仕方

ここからは、UI Extensions の設定の仕方を説明します。

Settings をクリックして、Extensions（スクショの赤枠）を押します。
![](https://storage.googleapis.com/zenn-user-upload/5f4c9daa4000-20220106.png)

すると、以下のような画面になります。
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

### 設定画面の説明

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

### JSON Form Editor の設定

先ほどは YoutubeId を例に紹介にしました。
JSON Form Editor の場合を紹介します。

手順はほとんど同じですが、
入力する GitHub の URL が以下になります。
`https://github.com/contentful/extensions/blob/master/samples/json-form-editor/extension.json`

FieldTypes は Object に適用したいので、Object にチェックします。
![](https://storage.googleapis.com/zenn-user-upload/8a7d0e6d07bb-20220129.png)

#### Code(required)を編集

`window.CONTENTFUL_FORM_EDITOR_SCHEMA`のところを以下のように修正します。

```js
window.CONTENTFUL_FORM_EDITOR_SCHEMA = {
  title: "Person",
  oneOf: [
    {
      $ref: "#/definitions/basicperson",
      title: "Basic Person",
    },
  ],
  definitions: {
    basicperson: {
      title: "Person",
      type: "object",
      id: "person",
      properties: {
        name: {
          type: "string",
          description: "First and Last name",
          minLength: 4,
          propertyOrder: 10,
        },
        age: {
          type: "integer",
          default: 21,
          minimum: 18,
          maximum: 99,
          propertyOrder: 20,
        },
      },
    },
  },
};
```

schema の定義の仕方は下記のリンクが参考になります。
https://github.com/jdorn/json-editor#json-schema-support

元々の JSON のフィールドは以下のスクショのようなものです。
![](https://storage.googleapis.com/zenn-user-upload/2bee29b373d8-20220127.png)

こんな感じで編集すると以下のようになります。

JSON の直接編集するようなフォームだったのが、
通常のフィールドのようになりました。

これなら非エンジニアの方でも入力しやすくなるかと思います。

### まとめ
