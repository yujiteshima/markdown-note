# Express Note

## 基本コード

1. expressオブジェクトの用意
2. アプリ(appオブジェクト)の作成
3. ルーティングの設定
4. 待ち受け開始

### expressオブジェクトの用意

まず最初に行うのはexpressオブジェクトの用意である。
これはrequireでモジュールをロードするだけである。

```js
const express = require('express')
```
ここでロードしているexpressというのがExpress本体のモジュールである。
ここでロードしたexpressオブジェクトから、アプリケーションのオブジェクトを作成する。

### アプリケーションオブジェクトの作成
expressを用意して最初に行うのが、アプリケーションオブジェクトの作成である。
このアプリケーションというのがExpressのアプリケーション本体となるオブジェクトなのである。

```js
const app = express()
```

### ルーティングの設定

アプリケーションにはまだ何もルーティングの設定がされていない。

Webアプリケーションのトップページにアクセスできるようにルーティングの設定を作成する。

```
app.get('/', (req, res) => {
  res.send('Welcome to Express!!')
})
```

ここではappオブジェクトの「get」というメソッドを呼び出している。

これはGETアクセスの設定を行う為のものである。

これは以下のようになっている。

```js
app.get( パス , 実行する関数)
```

第一引数には、割り当てるパスを指定する。ここでは```'/'```としている。

第二引数には、呼び出される関数を用意する。

これで第一引数のパスにクライアントがアクセスしたら、第二引数の関数が実行されるようになる。

第二引数に用意する関数は、以下のような形で定義する。

```js
(req, res) => {... 実行する処理 ...}
```

Expressのresとreqは、Node.jsとは違う（Express独自のオブジェクト）であるが、基本的にリクエストとレスポンスの機能や情報をまとめたものである。

ここでは```send```メソッドをを使っている。

これは、クライアントに送信するボディ部分の値を設定するもので、

```js
res.send( 表示するテキスト )
```

このように実行する。

### 待ち受けの開始

```js
app.listen(3000, () => {
  console.log('Start server port:3000')
})
```

### EJSを導入する

```
$ npm install --save ejs
```


### viewsディレクトリ作成する

### index.ejsを作成する

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta http-equiv="content-type" content="text/html; charset=UTF-8">
  <title><%=title%></title>
  <link type="text/css" href="./style.css" rel="stylesheet">
</head>
<body>
  <head>
    <h1><%=title%></h1>
  </head>
  <div role="main">
    <p><%- content %></p>
  </div>
</body>
</html>
```
ここで、<%=hoge%>は変数の出力。
<%- fuga %>はhtmlをエスケープせずに出力する為のもの。

### index.jsの修正

```js
const express = require('express')
const ejs = require('ejs') // 追加

const app = express()

app.engine('ejs', ejs.renderFile) // 追加

app.get('/',(req, res) => {
  // index.ejsをレンダリングする
  app.render('index.js',{
    title: 'Index', content: 'This is Express-app Top page!'
  })
  // コメントアウト res.send('Welcome to Express!!')
})
const server = app.listen(3333, () => {
  console.log('Server is running! at localhost://3333')
})
// app.listen(3333, () => {
//   console.log('Start server localhost://3333')
// })
```

### テンプレートのレンダリング

```
$ const ejs = require('ejs')
```
Node.jsでは、ejsオブジェクトのrenderメソッドを呼び出してテンプレートをレンダリングしたが、Expressは少しやり方が違う。

- エンジンの設定を行う

```js
const app = express()
app.engine('ejs', ejs.renderFile)
```

ここではappオブジェクトを作成した後で、アプリケーションのテンプレートエンジンを設定している。
appオブジェクトのengineメソッドは、アプリケーションで使用するレンダリングエンジンの設定を行うものである。
第一引数には、レンダリングするファイルの拡張子を指定して、第二引数には、レンダリングに用いられる関数/メソッドを指定する。

