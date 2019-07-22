# Node.js

```js
const http = require('http');
const server = http.createServer(
  (request, response) => {
    rewponse.end('Hello Node.js')
  }
);

sever.listen(3000)
```

`require`というメソッドを実行しているが、このメソッドは、Node.js独特のモジュールローディングシステムを利用するものである。
`http`というモジュールをロードしているが、これはHTTPアクセスのための機能を提供するものである。
このhttpはNode.jsのプログラムのもっとも中心的な機能となる。Node.jsのプログラムは、まずhttpをロードするところから、始まる。

## サーバーオブジェクトを作成する。
```js
const server = http.createServer(関数)
```

このcreateServerは、関数を一つ引数に用意しておく、この関数は以下のような形で定義する。

```js
(request, resoponse) => {
  ...実行する処理...
}
```
requestとresponseという二つの引数は、クライアントからサーバーへの要求と、サーバーからクライアントへの返信をそれぞれ管理するためのものである。
***createServerでは、必ずこの２つの引数を持った関数を用意する。***
この関数は、crateServerで作成されたhttp.Serverオブジェクトがサーバーとして実行されたときに必要なものである。そのサーバーに誰かがアクセスしてくると、この関数が呼び出されるのである。つまり、ここに処理を用意しておくと、「誰かがサーバーにアクセスしてきたら、必ずこの処理を実行する」ことが出来る。

request: http.ClientRequestオブジェクトが収められている
response: hhtp.ServerResponseオブジェクトが収められている。

- ヘッダー情報を設定する

```js
response.setHeader(名前,値)
```
```js
response.setHeader('Content-Type', 'text/html')
```

- ヘッダー情報を得る

```js
変数 = response.getHeader(名前)
```

- ヘッダー情報を出力する

```
response.writeHead(コード番号, メッセージ)
```

- コンテンツを出力する

```
response.write('<DOCUTYPE html> <html lang="ja">)
...
resoponse.end();
```

### 基本は......
***require, createServer, listen***

## HTMLファイルを使う

Node.jsでファイルを扱うオブジェクトは「File System」オブジェクト呼ばれるものである。これは「fs」というIDで用意されている。
これを利用するには、

```
変数 = require('fs')
```

このようにrequireを実行して、オブジェクト変数に取りこむ。そしてオブジェクト内にある、「readFile」というメソッドで、ファイルを読み込む。

```
fs.readFile(ファイル名、エンコーディング、関数)
```

readFileにはこんな具合に3つの引数を用意する。

- 1つ目は、読み込むファイルの名前。
- 2つ目は、ファイルの内容のエンコーディング方式を指定する。
- 3つ目は、readFileが完了した後に実行する処理を関数として用意しておく。

### HTMLファイルを用意

```html
<!DOCTYPE html>
<html lang="js">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Index</title>
</head>

<body>
  <h1>Index</h1>
  <h2>これはIndexページです</h2>
</body>

</html>
```

### ファイルを読み込んで表示する

```js
const http = require('http')
const fs = require('fs')

let server = http.createServer(
  (request, response) => {
    fs.readFile('./index.html', 'UTF-8',
    (error, data) => {
      response.writeHead(200, {'Content-Type' : 'text/html'})
      response.write(data);
      response.end();
    }
    )
  }
)
server.listen(3000)
console.log('Server start')
```

readFileのコールバック関数

```js
(error, data) => { ...実行する処理... }
```

第１引数には、読み込み時にエラーなどが起こった場合、そのエラーに関する情報をまとめたオブジェクトが渡される。
読み込んだデータは第２引数に渡されるので、そのデータをwriteで書き出す。

## テキストを操作するには
JavaScriptには、テキストの中から検索文字を探して、別のテキストに置き換える機能がある。これを使って読み込んだテキスト操作してみる。

### index.htmlの修正
```html
<body>
  <h1>dummy_title</h1>
  <p>dummy_content</p>
</body>
```
### app.jsを修正する

```js
const http = require('http')
const fs = require('fs')

let server = http.createServer(getFromClient)

server.listen(3000)
console,log('Server start!')
// ここまでメインプログラム＝＝＝＝＝＝＝＝＝＝＝＝＝

// createServerの処理

function getFromClient( request, response ) => {
  fs.readFile('./index.html', 'UTF-8',
    (error, data) => {
      let content = data.
          replace(/dummy_title/g,'タイトルです').
          replace(/dummy_title/g, 'これがコンテンツです。')

      response.writeHead(200, {'Content-Type' : 'text/html'})
      response.write(content)
      response.end()
    }
  )
}
```

### テキストの置換処理
テキストの置換は、テキストの値（string値）に用意されている「replace」というメソッドを使う。

```
変数 = テキスト .replace( 検索テキスト, 置換テキスト)
```

## テンプレートエンジンを使う

node.jsのテンプレートエンジンはpugとかEJSとかある。
ejsはNode.jsには標準では用意されていないので、パッケージマネージャーnpmでインストールする。

```
$ npm i -g ejs
```

ソースコードを書く
```html

<!DOCTYPE html>
<html lang="ja">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Index</title>
  <style>
  h1 {
    font-size: 60pt;
    color: #eee;
    text-align:right;
    margin: 0px;
  }
  body {
    font-size: 14pt;
    color: #999;
    margin: 5px;
  }
  </style>
</head>

<body>
  <h1>Index</h1>
  <h2>これはIndexページです</h2>
</body>

</html>
```

テンプレートファイルを表示させる。

1. テンプレートファイルを読み込む
fs.readFileを使っても良い、ここでは別のメソッドを使ってみる。基本的に普通のHTMLを読み込むのと同じ。

2. レンダリングする

3. 生成した表示内容を出力する。



### app.jsを修正する。

```js
const http = require('http')
const fs = require('fs')
const ejs = require('ejs')

const index_page = fs.readFileSync('./index.ejs', 'utf8')

let server = http.createServer(getFromClient)

server.listen(3000)
console,log('Server start!')

// ここまでメインプログラム＝＝＝＝＝＝＝＝＝＝＝＝＝

// createServerの処理

function getFromClient( request, response ) => {
  let content = ejs.render(index_page)
  response.writeHead(200, {'Content-Type' : 'text/html'})
  response.write(content)
  response.end()
}
```

### readFileSync

```
変数 = fs.readFileSync(ファイル名,エンコーディング)
```

readFileメソッドでは、コールバック関数を定義して、非同期処理を行なっていたが、このreadFileSyncは同期処理である。

### レンダリングの実行

```js
let content = ejs.render(index_page)
```

### プログラム側の値を表示させる

```
<%= 変数 %>
```

```
let content = ejs.render(indes_page, {
  tilte: "IndeX",
  content: "This is samplePage used by template"
})
```

## ルーティングをマスターする

### スタイルシートを使う
style.cssという名前でファイルを作り、スタイルを記述する。
テンプレート内で、読み込み

```
<link type="text/css" href="./style.css" rel="stylesheet">
```

このままでは、スタイルシートは適用されない。
なぜ適用されないのか、
試しに,
```
http://localhost:3000/style.css
```
にアクセスしてみると、スタイルシートの内容は表示されず、indes.ejsテンプレートの画面がそのまま表示される。

実は今のままでは、style.cssに限らず、http://localhost:3000の後に何をつけても
必ずindex.ejsの内容が表示されるようになる。要するに、http://localhost:3000/のサーバーにアクセスした場合は、全てindex.ejsが表示されるようになっている。

### ルーティングという考え方

これは不思議ではない、当たり前の事である。なぜなら、今まで作ったプログラムはただ「index.ejs」をロードしてレンダリングして表示するということしかやってなかったからである。
今まで作ったプログラムには、どのアドレスにアクセスしたらどういう内容を出力するかといった処理は全く書いていない。
そこで、どのアドレスにアクセスしたら、どういうコンテンツを出力するかということを定義する仕組みが必要になってくる。
これが ***ルーティング*** である。

### URLオブジェクトでアドレスを調べる。
クライアントがどんなアドレスでアクセスしてきたかを調べるオブジェクトにURLというオブジェクトがある。
これはURL(インターネットで使われるアドレス)を扱うための様々な機能をまとめたものである。
```
const url = require('url');
```

#### requestのURLで処理を分岐する

```js
let url_parts = url.parse(request.url)
switch (url_parts.pathname) {
  case "/":
  ..."/"にアクセスした時の処理...
  break

  ...必要なだけcaseを用意...
  }
```

- url.parse
URLデータをパース処理（データを解析して本来の状態に組み立て直す処理の事）する機能がある。それを利用して、ドメインより下のパスの部分の値をチェックして、それに応じて処理を分岐する。