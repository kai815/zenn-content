---
title: "Storybookを書いてる時にやってしまったアホなエラー"
emoji: "💦"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Storybook", "React"]
published: false
---

Type '(args: PropsType) => boolean' is not assignable to type 'ComponentStory<(props: PropsType) => Element>'.
Type '(args: PropsType) => boolean' is not assignable to type 'ArgsStoryFn<ReactFramework, PropsType>'.
Type 'boolean' is not assignable to type 'StoryFnReactReturnType'.

file 名を tsx じゃなくて ts にしてた
アホなミス

やってしまった

こんなミスはしないように

jsx 返してるのにエラーになる

なかなか気づかなかったので、誰かの助けになれば幸いです。

ただ、ミスの共有でした。
