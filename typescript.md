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

