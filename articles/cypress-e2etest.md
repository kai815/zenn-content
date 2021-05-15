---
title: " cypressでE2Eテストを実装"
emoji: "🎥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["React", "TypeScript", "Cypress"]
published: false
---

create-react-app で作られたアプリが前提

[Cypress](https://www.cypress.io/)

E2E テストとはブラウザでユーザが実際に操作するのを再現して行うテストです。

インストール
`yarn add -D cypress`

package.json に以下を追記して

` "cy:open": "cypress open"`

`yarn run cy:open`
するといっぱいファイルが作られる

cypress.json に今回テストするアプリの URL を記述します。

```
{
  "baseUrl": "http://localhost:3000"
}
```

テストを起動するためのコマンド

```
"cy:run": "cypress run",
"cy:run:chrome": "cypress run --browser chrome",
"cy:run:firefox": "cypress run --browser firefox"
```

アプリケーションのパス/cypress/integration/sample.spec.js
とかに以下を記述して、

```
//とりあえず動いてるか
describe('Cypress', () => {
  it('が動いてるか', () => {
    expect(true).to.equal(true)
  })
})
```

アプリを起動して
`yarn start`

テストしてみると
`yarn run cy:run --spec=./cypress/integration/sample.spec.js`

実行して見ると以下のように実行できてることがわかります。

※ちなみにアプリを起動しないと以下のようなエラーになります。

```
Cypress could not verify that this server is running:

  > http://localhost:3000

We are verifying this server because it has been configured as your `baseUrl`.

Cypress automatically waits until your server is accessible before running tests.

We will try connecting to it 3 more times...
We will try connecting to it 2 more times...
We will try connecting to it 1 more time...

Cypress failed to verify that your server is running.

Please start this server and then run Cypress again.
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

ts 化
しようとするとエラーになる

`cypress sample.spec.ts' cannot be compiled under '--isolatedModules' because it is considered a global script file. Add an import, export, or an empty 'export {}' statement to make it a module.`

cypress 配下に書けば行けそう
https://docs.cypress.io/guides/tooling/typescript-support#Set-up-your-dev-environment

tsconfig.json

```
{
"extends": "../tsconfig.json",
"compilerOptions": {
"isolatedModules": false,
"types": ["cypress"]
},
"include": ["./**/*.ts"]
}

```

data 属性を付与する

```

```
