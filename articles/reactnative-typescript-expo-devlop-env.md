---
title: " React Native,Expo,TypeScriptの開発環境構築"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["React Native", "TypeScript", "Expo"]
published: false
---

タイトル通りです。React Native,Expo,TypeScript を用いた開発環境の構築をしたいと思います。

expo のインストール

npm
`npm install -g expo-cli`

yarn
`yarn global add expo-cli`

インストールされたかの確認のためにバージョンチェク
`expo --version`

プロジェクトの作成
`expo init project-name`

Choose a template: › blank (TypeScript) same as blank but with TypeScript configuration
を選択

Xcode のインストール
https://docs.expo.io/workflow/ios-simulator/?redirected

`Error: EMFILE: too many open files, watch at FSEvent.FSWatcher.\_handle.onchange (internal/fs/watchers.js:178:28)`
