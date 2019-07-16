# JavaScript

## 非同期処理

### 非同期処理とは何か

プログラムを実行すると、コードを上から順に１行ずつ実行していく。
その処理の１つに時間のかかる処理があると、その実行が完了するまで、次の行には進まない。
例えば次のようなコードのことである。

```js
// sync_sleep.js
console.log("start")

function sleep(milliSeconds) {
  let startTime = new Date().getTime()
  while(new Date().getTime() < startTime + milliSeconds)
  console.log("sleepが完了しました")
}

sleep(5000)

console.log("end")
```

```
$ node sync_sleep.js
start
sleepが完了しました
end
```

#### 非同期処理

サーバーと通信を行なった際に、リクエストが返ってくるまでに数秒以上もかかると困る。

そこで、
「ある関数が呼び出された時、戻り値として本来渡したい結果を返すのではなく、一度関数としては終了し（＝呼び出し元に戻る）、後で本来渡したかった値を返せる状態になった時に、呼び出し元にその値を通知する」
という仕組みが考え出された。

このような仕組みを非同期処理といい、何かしらの処理が完了したら渡したfunctionを実行させるという関数をコールバック関数という。

例えば、ファイルを読み取って、その内容を出力する処理を行う。
まずテキストファイルを用意する。

```
1
2
```

次に、そのファイル内容を読み取って出力してみます。

```js
// async_readfile.js
console.log("start")

let fs = require('fs')
fs.readFile("number.txt", "utf-8", function(err, data)){
  if (err) throw err;
  console.log("ファイルの読み取り準備が出来ました")
  console.log(data);
}

console.log("end")

```

```
$ node async_readfile.js
start
end
ファイルの読み取り準備が出来ました
1
2
```

まず、async_readfile.jsに記載されたコードが実行される。
その際、fs.readFileは非同期関数として実装されており、呼びだれている関数は、一度終了し、中の処理が完了した段階で呼び出し元に通知します。
結果、endが先に出力された。
これが非同期処理である。

## Promise

Promiseの基本的な考え方は「非同期的に動作する関数は、本来返したい戻り値の代わりに「プロミスオブジェクト」という特別なオブジェクトを返しておき、（本来返したい）値を渡せる状態になったら、そのプロミスオブジェクトを通して呼び出し元に値を渡す」というものである。

### 基本の使い方

1. Promiseオブジェクトを作成し、返す。Promiseオブジェクトを作成するには、New Promise(fn)
2. fnには何らかの処理を書く。

- 処理結果が正常なら、resolve(結果の値)を呼ぶ。
- 処理結果がエラーなら、reject(Errorオブジェクト)を呼ぶ。

3. 1のpromiseオブジェクトに対して```.then```で値が返ってきた時のコールバックを設定する。

1秒たったら文字列```async```を返す非同期処理をPromiseで実装する。

```js
// promise.js

console.log('start');

function puts(str) {
  // 1. Promiseコンストラクタをnewして、promiseオブジェクトを返す
  return new Promise(function(resolve) {
    setTimeout(function() {
      resolve(str);
    }, 1000)
  });
}

// 2. 1のpromiseオブジェクトに対して、.thenで値が返ってきた時のコールバックを設定する

puts('async').then(function(result) {
  console.log(result)
})

console.log('end')
```

実行結果

```
$ time node promise.js
start
end
async
```

最後に文字列```async```が返ってきて、だいたい１秒で処理全体が完了している。

### Promiseの仕組み

Promiseオブジェクトには状態がある。それは以下の３種類である。

- Fulfilled: resolve(成功)した時。この時onFulfilledが呼ばれる。
- Rejected: reject(失敗)した時。この時onRejectedが呼ばれる。
- Pending: FulfilledまたはRejectedではない時。つまりpromiseオブジェクトが作成された初期状態等が該当する

最初はPendingになっているが、完了するとFullfilled、失敗するとRejectedになる。

コンストラクタであるPromiseには、newするときにfunctionを渡している。

このfunctionには、２つの引数を指定することができる。

1. 第一引数として指定しているresolve(必須)。このresolveはfunctionで、処理結果が正常だった場合に実行させる。resolveを実行するとPromiseオブジェクトの状態がfullfilledになる。
2. 第二引数であるreject(省略可)。同じくfunctionだが、resolveとは逆で、処理結果がエラーであった場合に実行させる。rejectを実行すると、Promiseオブジェクトの状態がrejectedになる。

このようにして、Promiseコンストラクタに渡したfunction内で、Promiseオブジェクトの状態をpendingからfullfilled及びrejectedに変化させる。



### 次はgetURLという、GETをするための関数を作る。
この```getURL```では、所得結果のステータスコードが200の場合の取得に成功、それ以外はエラーにしている。

```js
// https-promise.js
function getURL(url) {
  // 1.Promiseコンストラクタをnewして、promiseオブジェクトを返す
  return new Promise(function (resolve, reject) {
    const https = require(`https`);

    https.get(url, function(res) {
      if(res.statusCode === 200) {
        res.on('data', function(chunk) {
          resolve(chunk);
        })
      } else {
        // 2. うまくいかなかった場合の処理を記述する
        reject(new Error(res.statusCode));
      }
    }).on('error', function(e) {
      reject(new Error(e.message));
    })
  })
}

const URL = "https://www.google.co.jp";
// 3. promiseオブジェクトに対して、 .thenで値が返ってきた時のコールバックを設定する

getURL(URL).then(function onFulfilled(value) {
  console.log
})
```
