---
title: "MacでWPFの開発環境を構築する"
emoji: "🔔"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["csharp", "WPF"]
published: false
---

Mac で WPF の環境構築をします。

以下にあるように、visualstudio for Mac だと WPF の開発はできないようです。
https://visualstudio.microsoft.com/ja/vs/mac/

mac 内に windows 環境を作ります。

「Boot Camp」「Parallels」「VirtualBox」があります。

今回は BootCamp を選択する事にしました。

その理由はいくつかあるのですが、主に以下の点です。

・使ってない Mac Air があったので、それを Windows 用にしてしまう。

・お金をかけたくない

・VirtualBox は重いらしい

・Apple が提供してるらしいので安心

と考えていたが、

BootCamp だと空きディスクを 64GB も用意する必要がある
https://support.apple.com/ja-jp/HT201468
ので、自分の Mac の空き容量を考えると BootCamp だと難しい。

paralells の無料版でまずはやってみる

以下のリンクからダウンロード
https://www.parallels.com/jp/

アクセスの許可
