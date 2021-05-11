---
title: " React Native,Expo,TypeScriptの開発環境構築"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ReactNative", "TypeScript", "Expo", "ESLint"]
published: false
---

タイトル通りです。React Native,Expo,TypeScript を用いた開発環境の構築をしたいと思います。

## expo のインストール

npm の場合

```
$ npm install -g expo-cli
```

yarn の場合

```
$ yarn global add expo-cli
```

インストールされたかの確認のためにバージョンチェク

```
$ expo --version
```

プロジェクトの作成

```
$ expo init project-name
```

下記のような選択画面

```
? Choose a template: › - Use arrow-keys. Return to submit.
    ----- Managed workflow -----
    blank                 a minimal app as clean as an empty canvas
❯   blank (TypeScript)    same as blank but with TypeScript configuration
    tabs (TypeScript)     several example screens and tabs using react-navigation and TypeScript
    ----- Bare workflow -----
    minimal               bare and minimal, just the essentials to get you started
    minimal (TypeScript)  same as minimal but with TypeScript configuration
```

Choose a template: › blank (TypeScript) same as blank but with TypeScript configuration
を選択

`Managed workflow`と`Bare workflow`の違いは簡単にいうと、
`Managed workflow`→Expo のサポート下で開発できる
`Bare workflow`→Expo なしでの開発に近く、カスタマイズ性は上がるが難易度が高い
という違いのようです。

`blank`と`tabs`の違いは初めにいくつか画面が作られているかどうかです。

```
$ cd project-name
```

```
$ expo start
```

ios のシュミレータを使うには Xcode が必要なので、下記を参考にインストールする。
[Xcode のインストールの参考](https://docs.expo.io/workflow/ios-simulator/?redirected)

## ios のシュミレータを使おうとすると下記のようなエラーが出る場合

`Error: EMFILE: too many open files, watch at FSEvent.FSWatcher.\_handle.onchange (internal/fs/watchers.js:178:28)`

[エラー対処の参考](https://stackoverflow.com/questions/58675179/error-emfile-too-many-open-files-react-native-cli)

```
$ brew update
```

```
$ brew install watchman
```

インストールしようとすると下記のようなエラーに当たる場合は、

`Error: python@3.9: the bottle needs the Apple Command Line Tools to be installed. You can install them, if desired, with: xcode-select --install`

```
$ xcode-select --install
```

をしてから

```
$ brew install watchman
```

をする

これでエミュレータが動くようになる。

## eslint の導入

```
$ npx eslint --init
```

色々質問されるので、自分の目的に合うように選択してきます。

```
? How would you like to use ESLint? …
  To check syntax only
  To check syntax and find problems
❯ To check syntax, find problems, and enforce code style
```

文法のチェックと、ファイルの修正もして欲しいので,
`To check syntax, find problems, and enforce code style`を選択。

```
? What type of modules does your project use? …
❯ JavaScript modules (import/export)
  CommonJS (require/exports)
  None of these
```

modules のタイプとして何を使うか聞かれているので、
`JavaScript modules (import/export)`を選択します。

```
? Which framework does your project use? …
❯ React
  Vue.js
  None of these
```

どのフレームワークを使うかを聞かれているので`React`を選択します。

```
? Does your project use TypeScript? › No / Yes
```

TypeScript を使うか聞かれているので、`Yes`にします。

```
? Where does your code run? …  (Press <space> to select, <a> to toggle all, <i> to invert selection)
✔ Browser
✔ Node
```

どこでコードを走らせるかなので、
`Node`を選択します

```
? How would you like to define a style for your project? …
❯ Use a popular style guide
  Answer questions about your style
  Inspect your JavaScript file(s)
```

スタイルガイドの選択です。
有名なスタイルガイドを使いたい場合は、`Use a popular style guide`を選択します。
`Answer questions about your style`を選択すると、
色々な質問に答えて独自に設定してく形式です。

```
? Which style guide do you want to follow? …
❯ Airbnb: https://github.com/airbnb/javascript
  Standard: https://github.com/standard/standard
  Google: https://github.com/google/eslint-config-google
```

`Use a popular style guide`を選択すると、上記の中から選ぶことができます。
私は今回は、Airbnb を使用します。

```
? What format do you want your config file to be in? …
❯ JavaScript
  YAML
  JSON
```

Config ファイルの形式を聞かれています。
今回は`JavaScript` を選択します。

以上です。
この記事が皆様のお役に立てれば嬉しいです。
