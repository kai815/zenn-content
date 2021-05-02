---
title: " React Native,Expo,TypeScriptの開発環境構築"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ReactNative", "TypeScript", "Expo"]
published: false
---

タイトル通りです。React Native,Expo,TypeScript を用いた開発環境の構築をしたいと思います。

expo のインストール

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

template の違いは

[Xcode のインストールの参考](https://docs.expo.io/workflow/ios-simulator/?redirected)

```
$ expo start
```

ios のシュミレータを使おうとすると下記のようなエラーが出る場合

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

eslint の導入
