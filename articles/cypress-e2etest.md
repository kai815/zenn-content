---
title: " CypressでReactアプリにE2Eテストを導入する"
emoji: "🌀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["React", "TypeScript", "Cypress"]
published: false
---

create-react-app で作られたアプリに Cypress を用いて、
E2E テスト実装したいと思います。

[Cypress](https://www.cypress.io/)

E2E テストとはブラウザでユーザが実際に操作するのを再現して行うテストです。

## 環境

```
$ npm list --depth=0
├── react@16.14.0
├── react-dom@16.14.0
└── typescript@3.7.7
//必要そうなのだけ
```

また、今回使うアプリは [React のチュートリアル](https://ja.reactjs.org/tutorial/tutorial.html)です。

## 導入

インストールします。

```
$ yarn add -D cypress
```

`package.json` に以下を追記して

```json:package.json
"scripts": {
  //これを追記
  "cy:open": "cypress open"
}
```

```
$ yarn run cy:open
```

を実行すると、以下の画像のような画面が立ち上がります。

そして、cypress ディレクトリが作られて、サンプルのファイルなどができている。

`cypress.json` に今回テストするアプリの URL を記述します。

```json:cypress.json
{
  "baseUrl": "http://localhost:3000"
}
```

テストを起動するためのコマンドを`package.json`に記述します。

```json:package.json
"scripts":{
  //以下を追記
  "cy:run": "cypress run",
  "cy:run:chrome": "cypress run --browser chrome",
  "cy:run:firefox": "cypress run --browser firefox"
}

```

`--browser`でブラウザの指定ができるようです。

`アプリケーションのパス/cypress/integration/sample.spec.js`を作成して、
以下を記述して、

```js:sample.spec.js
//とりあえず動いてるか
describe('Cypress', () => {
  it('が動いてるか', () => {
    expect(true).to.equal(true)
  })
})

```

アプリを起動して

```
$ yarn start
```

テストしてみると
`yarn run cy:run --spec=./cypress/integration/sample.spec.js`

実行して見ると以下の画像のように実行できてることがわかります。

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

## TypeScript で書けるように

TS 化しようとすると下記のようなエラーになります。

`cypress sample.spec.ts' cannot be compiled under '--isolatedModules' because it is considered a global script file. Add an import, export, or an empty 'export {}' statement to make it a module.`

cypress 配下に tsconfig を書けばいけそう
https://docs.cypress.io/guides/tooling/typescript-support#Set-up-your-dev-environment

```json:tsconfig.json
{
  "extends": "../tsconfig.json",
  "compilerOptions": {
    "isolatedModules": false,
    "types": ["cypress"]
  },
  "include": ["./**/*.ts"]
}
```

## React チュートリアルにテスト書く

data 属性を付与する

```

```

```

```

```

```
