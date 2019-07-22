# Vue.jsテストハンドブック
コンポーネントをテストする公式ライブラリーのvue-test-utilsとモダンテストフレームワークJestを使った、サンプルの紹介である。
vue-test-utilsのAPIとコンポーネントのテストの最適な実践を実践の紹介である。

- 最初はvue-cliをインストールしてテスト環境を準備してから最初のテストを書く。コンポーネントをレンダーするmountとshallowMountの2つの方法とそれぞれの違いを説明する。

## 続いてコンポーネントをテストする時によくある場面を紹介する

例えば、
-  propsを受け取る
-  算出プロパティ
-  別のコンポーネントをレンダーする
-  イベントをemitする

そのあと、次でもっと興味深いケースを見てみます。

例えば、
- Vuexをテストするベストプラクティス（コンポーネントと、コンポーネント以外）
- vueルーターのテスト
- 第三者のコンポーネントのテスト

JestのAPIでテストをもっと安定させる方法も紹介します。

例えば、
- APIレスポンスをモックする
- モジュールのモックとスパイ
- スナップショット

## vue-cliをインストールする

`vue-test-utils`は、VueJS公式のテスト用ライブラリで、本ガイド全体で使用していくツールです。
このツールは、ブラウザ環境でもNode.js環境でも動作しますし、どんなテストランナーとも組み合わせることが出来る。本ガイドでは、Node.js環境でテストをはしらせていきます。

`vue-cli`は`vue-test-utils`を用いたテストを始めるにあたって一番簡単な方法である。
これを用いる事で、プロジェクトを作成する際に、Jestの設定も行ってくれます。
Jestはよく使われているテストフレームワークである。

```
$ yarn global add @vue/cli
```
or
```
$ npm install -g @vue/cli
```

`vue create [project-name]` を実行して新しいプロジェクトを作成する。

その際に、"Manually select features"を選択し、さらに"Unit Testing"を選んで、さらにテストランナーにはJestを選択します。

インストールが終わったら、`cd`でプロジェクトのディレクトリに移動し、`yarn test:unit`を実行する。今までの作業がうまくいっていれば、画面に次のようにメッセージが表示される。

```
PASS  tests/unit/HelloWorld.spec.js
  HelloWorld.vue
    ✓ renders props.msg when passed (26ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.074s
```

## 最初のテストを書く
---
さて、先ほどはプロジェクト作成時に用意されていたテストを実行する。
次に自分の手を動かしてコンポーネント書き、そのためのテストを書いていく。
伝統的にTDDにおいては、まず、失敗するテストを書いて、それからテストをパスするコードを書く。
ですがまずはコンポーネントを最初に書いていくとにする。

プロジェクトを作成した時に作られた`src/components/HelloWorld.vue`や`tests/unit/HelloWorld.spec.js`は必要ないので、まずはこれを消す。

### Greeting componentを作成する。
---
`Greeting.vue`コンポーネントを作成していく。このコンポーネント用のファイルは`src/components`に配置する。`Greeting.vue`には次のコードを書く。

```html
<template>
  <div>
    {{ greeting }}
  </div>
</template>

<script>
export default {
  name: "Greeting",

  data() {
    return {
      greeting: "Vue and TDD"
    }
  }
}
</script>
```

### テストを書く
---
`Greeting`コンポーネントの役割はたった一つ。`greeting`の値をrenderする事だけである。
ですので、このためのテストを書く為には、次の方針でいく。

1. コンポーネントを`mount`を用いて`render`する。
2. コンポーネントのテキスト部分に"Vue and TDD"が含まれている事を`assert`する。
`assert`とは、テストにおいて、ある値がどのような値になればテストを通った事になるか、その定義を宣言する事。

`Greeting.spec.js`を`test/unit`ディレクトリの下に作成する。そしてこのファイルの中で、`Greeting.vue`をimportして、それから`mount`する。そうするとコードはだいたい次のようになる。

```js
import { mount } from '@vue/test-utils'
import Greeting from '@/components/Greeting.vue'

describe('Greeting.vue', () => {
  it('renders a greeting', () => {

  })
})
```

TDDに用いられるシンタックスは様々であるが、Jestにおいては`describe`と`it`が比較的多く使われている。
`describe`はテストがどのようなものなのか、概要を示す為に記述する。今回のケースでは、`Greeting.vue`という部分である。`it`は、それによって題名を与えられたテストが、どのような目的を果たす為にあるのか、という事を示す。コンポーネントの機能が増えた際には、itブロックを増やして行けば良い。

さて、コンポーネントを`mount`を用いてrenderせねばならない。この、一般的な慣習とし、これによって生じたコンポーネントを`wrapper`という名前の変数に紐付ける。さらにその内容を画面に表示させて、正常に動作しているか確認する。