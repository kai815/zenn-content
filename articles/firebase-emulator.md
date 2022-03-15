---
title: "Firebase emulator"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

ClI のインストール

Mac

https://firebase.google.com/docs/cli?hl=ja#mac-linux-npm

firebase login

firebase emulators:start

No project active, but project aliases are available.

エラー

--project

で指定。

firebase emulators:start --project プロジェクト名
でこける。
The operation couldn't be completed. Unable to locate a Java Runtime.
Java が必要っぽいので

Java をダウンロード
https://java.com/ja/download/

Firestore への接続

https://firebase.google.com/docs/emulator-suite/connect_firestore?hl=ja

export とかもできるよ。

import とかもできる
