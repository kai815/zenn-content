---
title: "Cyberのスピードハッカソンやってみて"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

https://developers.cyberagent.co.jp/blog/archives/32747/
これをやりました。

対策

初期表示

blob の画像やめる

念のためのスクロールやめる

雑にメモ

webpack 系

main.js が重い

https://webpack.js.org/plugins/split-chunks-plugin/
https://webpack.js.org/guides/code-splitting/

https://www.hypertextcandy.com/react-code-splitting

dynamic import
https://stackoverflow.com/questions/53698194/module-default-is-undefined-dynamic-import

https://ja.reactjs.org/docs/code-splitting.html

split 動かない原因は modules だった。
false にすれば esmodule

https://babeljs.io/docs/en/babel-preset-env#modules

書き方で悩んだ
https://reactrouter.com/docs/en/v6/examples/lazy-loading
https://stackblitz.com/github/remix-run/react-router/tree/main/examples/lazy-loading?file=src/App.tsx

API 系
posts のデータ多すぎ

→server/src/routes/api/post.js を見ると、
limit とか指定できる

画像多い

mp3 と gif 重い

画像のサイズ

woff フォント

画像のレイジーロード
使った
https://github.com/Aljullu/react-lazy-load-image-component

corejs の budle サイズ
https://developer.a-blogcms.jp/blog/kaizen/bundlesize.html

ビルド
https://qiita.com/wwalpha/items/729d7fa0a08ba89395ab

バンドルサイズを減らす
https://blog.recruit.co.jp/rtc/2018/12/15/try_optimization_webpack_bundle_size/

https://tech.commmune.jp/entry/2019/12/19/083209
