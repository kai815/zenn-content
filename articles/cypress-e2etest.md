---
title: " CypressでReactアプリにE2Eテストを導入する"
emoji: "🌀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["React", "TypeScript", "Cypress"]
published: true
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

また、今回テストに使うアプリは [React のチュートリアル](https://ja.reactjs.org/tutorial/tutorial.html)です。

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

![](https://storage.googleapis.com/zenn-user-upload/ltibnf6ibk7xxsxjo5y1fv2gpmev)

そして、cypress ディレクトリが作られて、サンプルのファイルなどができています。

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

`integration`ディレクトリに`sample.spec.js`を作成し、
以下を記述します。

```js:sample.spec.js
//とりあえず動いてるか
describe('Cypress', () => {
  it('が動いてるか', () => {
    expect(true).to.equal(true)
  })
})

```

アプリを起動し増す。

```
$ yarn start
```

アプリを起動した上で、テストを実行してみると

```
$ yarn run cy:run --spec=./cypress/integration/sample.spec.js
```

実行して見ると以下の画像のように実行できてることがわかります。

![](https://storage.googleapis.com/zenn-user-upload/dkcvvnit29kon9tb707fbe4fcn2f)

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
[参考](https://docs.cypress.io/guides/tooling/typescript-support#Set-up-your-dev-environment)

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

### data 属性を付与する

自分なりに React チュートリアルの書き方を変更しているので、
必ずしも全く同じではないかと思います。

#### ボタンに属性を付与

```tsx:src/components/square/index.tsx
//必要そうなところのみ抜粋
export interface SquarePropsInterface {
  value: string;
  onClick: () => void;
  indexNumber: number;
}

export const Square = (props:SquarePropsInterface) => {
  return (
    <button
      css={SquareStyle}
      onClick={() => {
        props.onClick();
      }}
      //ここでデータ属性を付与
      data-e2e={`button-${props.indexNumber}`}
    >
      <span css={SquareText}>{props.value}</span>
    </button>
  );
}

```

```tsx:my-app/src/components/board/index.tsx

//必要そうなところのみ抜粋
export interface BoardPropsInterface {
  squares: string[];
  onClick: (i: number) => void;
}

export const Board = (props:BoardPropsInterface) => {
  const renderSquare = (i: number)=> {
    return (
      <Square
        value={props.squares[i]}
        indexNumber={i}
        onClick={() => props.onClick(i)}
      />
    );
  }
  return (
    <div>
      <div css={BordRowStyle}>
        {renderSquare(0)}
        {renderSquare(1)}
        {renderSquare(2)}
      </div>
      <div css={BordRowStyle}>
        {renderSquare(3)}
        {renderSquare(4)}
        {renderSquare(5)}
      </div>
      <div css={BordRowStyle}>
        {renderSquare(6)}
        {renderSquare(7)}
        {renderSquare(8)}
      </div>
    </div>
  );
}

```

#### ゲームのステータスを表示するところにも付与

```tsx:my-app/src/components/status/index.tsx

//必要そうなところのみ抜粋

export interface StatusPropsInterFace{
  winner:string | null | undefined,
  xIsNext:boolean
}

export const Status = (props:StatusPropsInterFace) => {
  const { winner, xIsNext } = props;
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else{
    status = `次のプレイヤー ${xIsNext ? "X" : "O"}'s turn`;
  }
  return (
    <>
      //ここでデータ属性を付与
      <span data-e2e="status">
        {status}
      </span>
    </>
  )
}
```

### テストを書く

```ts:my-app/cypress/integration/game.spec.ts

describe('OXゲームで勝敗が決定した時のテスト', function () {
  //ホームURLに訪れる
  beforeEach(() => {
    cy.visit('/');
  });

  it('Xが勝利', () => {
    //最初のターンで左上のボタンをクリック
    cy.get('[data-e2e="button-0"]').click().get('[data-e2e="button-0"]').should('have.text', 'X')
    //その次のターンで一番上の真ん中をクリック
    cy.get('[data-e2e="button-1"]').click().get('[data-e2e="button-1"]').should('have.text', 'O')
    //その次のターン一番左の列の真ん中の段をクリック
    cy.get('[data-e2e="button-3"]').click().get('[data-e2e="button-3"]').should('have.text', 'X')
    //その次のターン一番真ん中の真ん中の段をクリック
    cy.get('[data-e2e="button-4"]').click().get('[data-e2e="button-4"]').should('have.text', 'O')
    //その次のターンで左下をクリック
    cy.get('[data-e2e="button-6"]').click().get('[data-e2e="button-6"]').should('have.text', 'X')
    //Xが勝利
    cy.get('[data-e2e="status"]').should('have.text', 'Winner: X')
  });

});

```

ご覧の通りかなり直感的に書くことができるかと思います。
`cy.get`で要素を取得して、クリックや、持つべきものを持っているかを判定しています。
今回は data 属性で取得しましたが、class 名で取得したい時は、
`get('.class-name')`とすると取得できるようです。

[参考](https://docs.cypress.io/api/commands/get)

テストを実行してみます。

```
$ yarn run cy:run --spec=./cypress/integration/game.spec.ts
```

![](https://storage.googleapis.com/zenn-user-upload/jnyeoaaqd6lmvrfhmasp6m7k8fhj)

テストが通りました。

### テストを音落す

```diff ts:my-app/cypress/integration/game.spec.ts
// OをXに変えてあえてテストを落す
-  cy.get('[data-e2e="button-4"]').click().get('[data-e2e="button-4"]').should('have.text', 'O')
+  cy.get('[data-e2e="button-4"]').click().get('[data-e2e="button-4"]').should('have.text', 'X')
```

変更してからテストを実行します。

```
$ yarn run cy:run --spec=./cypress/integration/game.spec.ts
```

![](https://storage.googleapis.com/zenn-user-upload/hwu72i7nb4z4z6snzga1c10bte6t)
意図通り、テストは落ちました。

ちなみにテストを落とすとスクリーンショットをとってくれます。
![](https://storage.googleapis.com/zenn-user-upload/kajqehr992ty3f2p1xzmg79wljpb)

どこで落ちたのかも確認しやすいですね。

### まとめ

導入で設定することもそこまで多くなく、テストコードも直感的に書ける印象です。
これから E2E テストを導入してみようかと考えている方は、検討してみてもいいかもしれませんね。
この記事が、皆様のお役に立てれば嬉しいです。
