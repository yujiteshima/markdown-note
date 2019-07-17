# TypeScript

## 開発環境と設定

## TypeScriptの基礎

### 意図しないNANを防ぐ
#### JavaScriptの課題その１
#### JavaScriptの課題その２

### 基本の型
#### boolean型
#### number型
#### string型
#### array型
#### tuple型
#### any型
#### unknown型
#### void型
#### null型/undefine型
#### never型
#### object型

### 高度な型
#### Intersection Types
#### Union Types
#### Literal Types

### typeofキーワードとkeyofキーワード
#### typeofキーワード
#### keyofキーワード

### アサーション

### クラス
#### 宣言と継承
#### クラスメンバー修飾子

### 列挙型
#### 数値列挙型
#### 文字列列挙
#### open ended

## TypeScriptの型推論

### const/letの型推論
#### letの型推論
#### constの型推論
#### Widening Literal Types

### Array/Tupleの型推論
#### Arrayの型推論
####
####

###

###
####
####

###
####
####
####
####

###
####
####

###

## 意図しないNANを防ぐ
NaNとは、「Not-a-Number」の略で、JavaScriptにおける「非数」(数でないもの)を表す特別な値である。一般には、異常な値や定義されていない演算の結果などとして使われる。
### JavaScriptの課題その１

```js
// example.js
function expo2(amount) {
  return amount ** 2
}
const value = expo2('1,000')
const withTax = Value * 1.1 // Nan
```
上記のコードは、引数に数値として扱うことのできない値を渡してしまった事により、「NaN」が表示されてしまうという失敗例である。引数は数値である必要があり、それ以外を許容しないようにしなければならない。

- TypeScriptの解決：引数を型注釈で制約する


このような場合、引数に型注釈(アノテーション)を付与し、明示的に型を制約する。関数利用時に型不整合がある場合、コンパイルエラーを得ることが出来る。コンパイルエラーを得やすいコードほど厳格性が高まるため、バグを含みにくくなる。

```js
// example.ts
function expo2(amount: number) {
  return ** 2
}
console.log(expo2(1000))
console.log(wxpo2('1.000'))
```

### JavaScriptの課題２

```js
// example.js
function taxed(amount) {
  return amount * 1.1
}
function fee(amount) {
  return `{amount * 1.4}`
}
function price(amount) {
  return `${fee(amount)}`
}

const demand = '¥' + taxed(price(1000)) // ¥NaN
```
上記のコードは、原価に手数料を加え、請求額を求めようとしたコードの失敗例である。請求額計算の途中、数値が文字列に変換されてしまったことがバグの原因である。

- TypeScriptの解決：戻り値を型注釈で制約する


関数戻り値に型注釈（アノテーション）を付与し、明示的に戻り型を制約する。型注釈と定義内容に型不整合がある場合、コンパイルエラーを得ることが出来る。

```js
// example.ts

function taxed(amount): number{
  return amount * 1.1
}

function fee(amount): number{
  return amount * 1.4
}

function price(amount): number{
  return `${fee(amount)}` // Error!
}
```
上記のコードはtsconfig.jsonで「"noImplicitAny":false」としていることが前提。

### 基本の型
TypeScriptではJavaScriptで期待されるのとほぼ同じ型をサポートしている。
#### boolean型
boolean型はもっとも基本的なデータ型であり、取りうる値は```ture/false```である。

```js
let flg: boolean = false;
```

#### number型
JavaScriptと同様に、TypeScriptの全ての数値は浮動小数点数値である。TypeScriptは、16進数と　10進数のリテラルに加えて、ECMAScript2015で導入された２進数と8進数のリテラルもサポートしている。
```js
let decimal: number = 256
let hex: number = 0xfff
let binary: number = 0b0000
let octal: number = 0o123
```

### string型
JavaScriptプログラミングでもっとも基本的な処理は、テキストデータを扱う事である。他の言語と同様に、文字列を扱うためにString型を使用する。JavaScriptと同様に、文字列データを二重引用符、一重引用符、またはバッククォートで囲う。

```ts
let color: string = "white";
color = 'black';
let myColor: string = `my color is ${color}`
```

## ReactでTypeScriptを使う利点

reactでは、コーディングにJavaScriptの拡張構文であるJSXを使用する。TypeScriptでは、JSXを記述する場合、拡張子を「.tsx」としてファイルを作成する。
純関数とHTMLテンプレートが入り混じったJSXでは、型はどのように開発をサポートするのであろうか？本節では、ReactがTypeScriptの恩恵を享受するシーンを確認する。
JSX(TSX)が、型システムと親和性が高いことが納得できる。


### 最小限のReact開発環境(Partcel編)
```
$npm i react react-do

$npm i -D @types/react @types/react-dom parcel-bundler
```

- tscコマンドなどで作成した`tsconfig.json`の`compilerOptions`に`"jsx": "react"`を追加する。
- ReactやTypeScriptのビルド設定は、これだけである。

```js
// tsconfig.json
"compilerOption": {
  ...
  "jsx": "react"
}
```

エントリーポイントになるHTMLファイルに「`<script src="./app.tsc"></script>`」と記述する。

```html
// index.html

<!DOCTYPE html>
<html>
  <head>
    <script src="./app.tsc"></script>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

```js
// app.tsx
import * as React from 'react'
import { render } from 'react-dom'
render(<div>Hello world!</div>, document.getElementById('app'))
```

```
$ npx parcel index.html
```

### 最小限のReact開発環境（create-react-app編）

```
$ npm i -g create-react-app

$ create-react-app hello-world --typescript
```

```
|-node_modules
|-public
|   |-favicon.ico
|   |-index.html
|   |-manifest.json
|
|-src
|   |-App.css
|   |-App.test.tsx
|   |-App.tsx
|   |-index.css
|   |-index.tsx
|   |-logo.svg
|   |-react-app-env.d.ts
|   |-serviceWorker.ts
|
|-.gitignore
|-package.json
|-README.md
|-tsconfig.json
|-yarn.lock
```

```
$ yarn start
```

### 目指すマークアップ出力

```html
<div>
  <div>
    <h1>健康意識に関する調査結果</h1>
  </div>
  <table>
    <thead>
      <tr>
        <th>対象世代</th>
        <th>十分に取り組んでいる</th>
        <th>近いうちに取り組みたい</th>
        <th>取り組んでいない</th>
        <th>必要ない</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th>20~30歳</th>
        <td>18%</td>
        <td>22%</td>
        <td>37%</td>
        <td>23%</td>
      </tr>
      <tr>
        <th>30~40歳</th>
        <td>12%</td>
        <td>28%</td>
        <td>42%</td>
        <td>18%</td>
      </tr>
    </tbody>
  </table>
</div>
```

### データ型を定義する

コンポーネントの開発は、表示するデータ型の考察から始まる。実際にWebアプリケーションのフロントエンドの実装では、APIでデータを取得し、それを表示するという作業が大半を占める。
ここでは解説の為、モックデータを`data.ts`で定義し、表示する。配列がテーブルの行、オブジェクトが列として利用するデータと捉えるとわかりやすい。

```ts
// data.ts
export const rows = [
  {
    id: 'up20-un30',
    generation: '20~30歳',
    answers: [0.18, 0.22, 0.37, 0.23]
  },
  {
    id: 'up30-un40',
    generation: '30~40歳',
    answers: [0.12, 0.28, 0.42, 0.18]
  }
]
```

このデータに対して型を付与すると、以下のようになる。
```ts
export type Row = {
  id: string
  genaration: string
  answers: number[]
}
export type Rows = Row[]
```

このRows型をモックデータにアノテーションで付与する。Row型と異なるオブジェクトを含んだ場合、コンパイルエラーが得られる事を確認できる。

```ts
export const row: Rows = [ // アノテーションで付与

]
```