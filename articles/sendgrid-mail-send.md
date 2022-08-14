---
title: "SendGridでGlobal Unsubscribeでも強制的にメールを送る方法"
emoji: "📩"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["SendGrid"]
published: false
---
SendGridの持つ機能を使って比較的簡単にメールの配信停止機能を作ることが出来ます。
メールのグループごとに配信停止をコントロールすることも可能です。
やり方などは以下のリンクが参考になります。

* [受信者目線で配信停止を管理しよう！Unsubscribe GroupsとPreference Centerの概要](https://sendgrid.kke.co.jp/blog/?p=10316)
* [Unsubscribe Groups](https://sendgrid.kke.co.jp/docs/User_Manual_JP/Suppressions/advanced_suppression_manager.html)
* [Setup Unsubscribe with SendGrid API & Dynamic Templates](https://dgorski.medium.com/setup-unsubscribe-with-sendgrid-api-dynamic-templates-3b942d325d3d)


## 配信停止画面
SendGridの用意してくれた特定のグループのみを配信停止する配信停止用の画面がありますが、そこから
以下のような配信停止の設定の画面にも飛ぶことができます。
![optout](https://storage.googleapis.com/zenn-user-upload/b2027487ce4a-20220814.png)
また`Opt Out of All Emails`をクリックすると、`Global Unsubscribe`という全てのメールを配信停止にする設定になります。

こうなると困るのが、パスワードリセット通知などのユーザーに届ける必要のあるメールまで届かなくなってしまうことです。

## 強制的に送る方法
強制的に送る方法としては、リクエスト時のパラメータの`mail_settings`の`bypass_list_management`の`enable`を`true`にすることです。
サンプルコードは以下です。
```js:sample.js
const sgMail = require('@sendgrid/mail')
sgMail.send({
  //他の設定は割愛してます。
  mail_settings: {//ここの設定が重要
    bypass_list_management: {
      enable: true,
    },
  },
})
```
### 参考
https://sendgrid.kke.co.jp/docs/API_Reference/Web_API_v3/Mail/index.html#-Request-Body-Parameters