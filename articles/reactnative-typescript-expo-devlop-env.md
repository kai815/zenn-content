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

参考
https://stackoverflow.com/questions/58675179/error-emfile-too-many-open-files-react-native-cli

`brew update`

You have 4 outdated formulae installed.
You can upgrade them with brew upgrade
or list them with brew outdated.

`brew upgrade`
の結果

==> nodebrew
You need to manually run setup_dirs to create directories required by nodebrew:
/usr/local/opt/nodebrew/bin/nodebrew setup_dirs

Add path:
export PATH=$HOME/.nodebrew/current/bin:$PATH

To use Homebrew's directories rather than ~/.nodebrew add to your profile:
export NODEBREW_ROOT=/usr/local/var/nodebrew

zsh completions have been installed to:
/usr/local/share/zsh/site-functions
==> git
The Tcl/Tk GUIs (e.g. gitk, git-gui) are now in the `git-gui` formula.

zsh completions and functions have been installed to:
/usr/local/share/zsh/site-functions

とエラーが出る
