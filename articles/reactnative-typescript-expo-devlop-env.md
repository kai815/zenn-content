---
title: " React Native,Expo,TypeScriptの開発環境構築をする"
emoji: "🎥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ReactNative", "TypeScript", "Expo", "ESLint"]
published: true
---

タイトル通りですが、React Native,Expo,TypeScript を用いた開発環境の構築をしていきます。
スマホアプリの開発もやってみたいということで、ReactNative を学び始めたので、
そのメモ的に書きます。

## Expo のインストールからアプリの起動まで

[Expo のドキュメント](https://expo.io/)

Expo をインストールします。

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

※`project-name`のところは任意の名前です。

下記のような選択画面になるので、

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

`blank (TypeScript) same as blank but with TypeScript configuration`
を選択します。

`Managed workflow`と`Bare workflow`の違いは簡単にいうと、
`Managed workflow`→Expo のサポート下で開発できる
`Bare workflow`→Expo なしでの開発に近く、カスタマイズ性は上がるが難易度が高い
という違いのようです。

`blank`と`tabs`の違いは初めにいくつか画面が作られているかどうかです。

作られたプロジェクトのディレクトリに移動

```
$ cd project-name
```

```
$ expo start
```

スタートすると以下のような画面になれば OK です。
![](https://storage.googleapis.com/zenn-user-upload/sw46nhvcikwgmvjzshbtytu9ozxw)

左の`Run on iOS simulator`、`Run on Android device/emulator`を押すと、
それぞれ、iOS のシュミレータ、Android のエミュレータが起動します。
（Android の場合は Android Studio を先に起動しておく必要もあります。）

iOS のシュミレータを使うには Xcode が必要なので、まだの人は下記を参考にインストールしてください。
[Xcode のインストールの参考](https://docs.expo.io/workflow/ios-simulator/)

Android のエミュレータを使うには Android Studio Emulator が必要なので、まだの人は下記を参考にをインストールしてください。
[Android Studio Emulator のインストールの参考](https://docs.expo.io/workflow/android-studio-emulator/)

無事起動できると以下の画像のようになります。（左が iOS で右が Android です）
![](https://storage.googleapis.com/zenn-user-upload/8pjh9bu8vrp42d4k6dyxthxouqzn)

### iOS のシュミレータを使おうとすると下記のようなエラーが出る場合

`Error: EMFILE: too many open files, watch at FSEvent.FSWatcher.\_handle.onchange (internal/fs/watchers.js:178:28)`

[エラー対処の参考](https://stackoverflow.com/questions/58675179/error-emfile-too-many-open-files-react-native-cli)

上記を参考に、

```
$ brew update
```

```
$ brew install watchman
```

をします。

watchman をインストールしようとすると下記のようなエラーに当たる場合は、

`Error: python@3.9: the bottle needs the Apple Command Line Tools to be installed. You can install them, if desired, with: xcode-select --install`

エラー文の通り、

```
$ xcode-select --install
```

をしてから

```
$ brew install watchman
```

をするとインストールできます。

これでシュミレータが動くようになりました。

## Eslint の導入

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
`To check syntax, find problems, and enforce code style`を選択します。

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
  Browser
✔ Node
```

どこでコードを走らせるかなので、
`Node`を選択します。

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

### まとめ

以上です。
スマホアプリ初心者の自分は、「Xcode も Android Studio も必要なんだ!」と
少し戸惑いました（当たり前っちゃ当たり前なのか）が、
思ったよりも簡単に環境構築ができた印象が自分の中ではあります。

この記事が誰かのお役に立てれば嬉しいです。
