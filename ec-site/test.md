# Nuxt.js Vue.jsのテストについて

## フロントエンドにおけるテストの必要性

### フロントエンドのテストの難しさと民主化

- フロントエンドは従来からUI層と密接に関係している事、外部のSDKを読み込む事や、APIとの接続がある事から非常にテストが難しい分野であった。
>SDK（Software Development Kit）とは、あるシステムに対応したソフトウェアを開発するために必要なプログラムや技術文書などをひとまとめにしたものです。システムの開発元や販売元>>が、開発者に配布することもあります。
>SDKには、プログラムに連結して使用するためのライブラリファイル(他のプログラムに何らかの機能を提供するコードの集まり)、APIの仕様を記述した文書ファイルなどが含まれます。
これまでもSeleniumなどのe2eテストツールを利用することによってテストを行うことは可能であった。

- ただし、DOMやブラウザに強く依存する事から、テストの寿命は短いにもかかわらず作成コストは高い状況で、テストがしにくい環境にあった。

- しかし、そういった時代は過去のものとなった。現代的なフロントエンド開発に置いて、全体のコードベースにおける一定以上の割合はDOMの依存からすでに離れており、Node.js上で十分なテストが可能となった。

- ビジネスロジックのみのテストはもちろん、サーバーサイドレンダリングのシステムを根底に据える形で、DOMと関わるUIコンポーネントのテストも可能となった。

### 依然として残るテストの課題

- しかしながら、依然としてフロントエンドの課題は残る。

- DOMが関係する為にテストがしにくいという問題は解消された。しかし、 HTML構造が影響する所のテストは誰が担保するのか。HTMLやCSSを書くのは必ずフロントエンド開発に長けたエンジニアであるのか、そうでないのか。

- 最終的に１つのAjaxリクエストが飛ぶような機能単位の開発ではなく、スタンドアローンで１つアプリケーションとして動くようになった現代のフロントエンドでは、統合的にどのようにテストをしていくかという点については、まだ正解は見えない状況である。

### 何処までテストを書くか？

- その上で、何処までテストを書くかが問題となる。Nuxt.jsビギナーズガイド の著者である花谷拓磨氏は「ユニットテストについて、Vuexデータストアと挙動が複雑なVueコンポーネント、そしてフレームワークに依存しないレイヤのコードに絞ってテストを書くべき」と提唱している。


- また、システムテストやe2eテストについては、どうしてもUIの都合が付いてくる事によって非常に壊れやすい為、必要に応じてテストをする。HTMLの改変の可能性がある為、HTML単位のテストはしない形がベストである。

- Vueコンポーネントのテストを書くと、どうしてもUIが関わるため、行ったとしてもスナップショットテスト程度にしておくと良い。

## Jestの導入

```
$ yarn add -D jest
```

```js
// package.json
{
  "script": {
    "test": "jest"
  }
}
```

Vue.jsやNuxt.jsの開発においては、公式のテスト支援ツールである、VueTestUtilsがオフィシャルドキュメントで、Jestもしくは、mochaの利用を推奨している為、Jestを利用しておくと良い。


## Jestを用いたJavaScriptのテスティング

基本的にVue.jsのテスティングにおいては、単一ファイルコンポーネントの解釈などの都合上、Jestプラグインの導入が必須となっている。

はじめからそのあたりまでを含めて全て覚えるのは難しい為、まずはピュアなJavaScriptで書かれているVuexのストアを通して、JavaScriptのテストを体験していくのがおすすめである。

### テスト対象となる、Vuexストアの実装

単純なベンチマークアプリであるカウンターのstoreディレクトリを実装してみる。


```js
// app/store/index.js

const store = () => ({
  count: 0
})

const getters = {
  count: (state) => state.count
}

const mutations = {
  increment(state) {
    state.count++
  }
}

const actions = {
  increment({ commit }) {
    commit('increment')
  }
}

module.exports = {
  state,
  getters,
  mutations,
  actions
}
```

実際にブラウザ上で動作確認したい場合は、app/pages/index.vueに次のようなコードを書いて確認すると良い。

```html
//app/pages/index.vue
<template>
  <div>
    <h2>カウンター</h2>
    <h3>Count: {{count}}</h3>
    <button type="button" @click="increment"> Increment </button>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  computed: {
    ...mapGetters(['count'])
  },
  methods: {
    ...mapActoins(['increment'])
  }
}
</script>
```

### Vue/Vuexテストの為のVue Test Utilsの導入

Vue.jsおよびその周辺エコシステム(VuexやVue Router)をうまくテストする為には、VueTestUtilsが必要となる。
これは、vue-server-rendererを内部に持つテスト用のユーティリティ集であり、Node.js上で、あたかもブラウザ上でのイベントが動いているかのようなテストを行う事が可能になるものである。

```
$ yarn add -D @vue/test-utils lodash.clonedeep
```

### Vuexストアのテストコードの記述

VuexTestUtilsを使ったテストは基本的なテストの記述方法と大きな差ない。

Jestを利用するのであれば、describeによってテストの範囲をうまく切りつつ、testやit関数を利用してアサーションしていく。

VueTestUtilsを利用したテストでは、それぞれのテストの実行前にbeforeEachなどのフック内を用いて、モックのVueインスタンスを生成する必要がある。

Vueコンポーネントであれば、Vueコンポーネント自体のレンダリングの為の準備を行う。

Vuexストアであれば、ローカルのVueインスタンスを生成し、その中でVuexインスタンスを利用する準備を行う。



```js
// spec/sotre/index.spec.js

// vuexモジュールのインポート
const Vuex = require('vuex')
// storeモジュールをインポート
const index = require('.../.../app/store')
// createLocalVueモジュールをvue-test-utilsからインポート
const { createLocalVue } = require('@vue/test-utils')
// lodashからcloneDeepをインポート
const cloneDeep = require('lodash.clonedeep')
```
