# NuxtJs Notes

## モダンフロントエンドの今
### モダンフロントエンドについて
流れが早いと言われたのは今は昔。
非常に安定し、停滞した状態。
SPAは2010年代のトレンドとなった。
現在、SPAとAPIによるWebアプリケーションは、もはやデファクトスタンダードになったと言っても過言ではない。
React, Vue, Angularの寡占状態
Web Woker領域へのロジックの移譲や、Web Assemblyを利用したハイパフォーマンスな先進的な機能も多く追加さてれいるが、まだ実用的な段階までは民主化されたいない。

### 安定したフロントエンド、JavaScriptに求められているもの

### Nuxt.jsとこれからのアプリケーション開発

## Nuxt.jsとは

### Next.jsをリスペクトして生まれたNuxt.js

### Nuxt.jsの特徴と機能

- ビルドプロセスの隠蔽

- Vueのエコシステムとのインテグレーション

- 独自レイヤの実装

### 多くの企業に愛されるNuxt.js

## Nuxt.jsがもたらすもの

### Nuxt.jsがフロントエンドにもたらす「規約」

## QiitaAPIを使った記事取得を通したNuxt.jsチュートリアル

### 動的ルーティングのコンテンツの出し分け

```html
<!-- // pages/users/_id.vue -->
<template>
  <div>
    <p>
      User ID: {{ UserId }}
    </p>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        userId: this.$route.params.id
      }
    }
  }
</script>
```

動的なルーティングを行なった場合、Nuxt.jsは```_```以降の名前をプロパティ名として、paramsへと代入する。

その為、実践的な開発では、```/usrs/:id/posts/:id```などとネストしたくなる場合を考え、実践的な開発では、```/users/_userid.vue```など、被りにくいものにしておくと便利である。この場合、$route.params.useridへと代入される。

## axios-moduleによる外部リソースの取得

### axios-moduleの導入

```
$ yarn add @nuxtjs/axios
```

```js
// nuxt.config.js
{
  modules: [
    '@nuxtjs/axios'
  ],
  axios: {

  }
}
```
modulesに配列としてモジュールのパッケージ名を渡す事で、対象となるモジュールが自動で読み込まれる。
axiosプロパティはaxiosの設定をするときに利用する。

```html
<template>
  <div>
    <!-- -->
  </div>
</template>

<script>
  export default {
    async mounted() { //asyncでこれから非同期処理を始めるよという意味
      console.log(
        JSON.stringfy( // JSOM.stringfy
          await this.$axios.$get('https://qiita.com/api/v2/items?query=tag:nuxt.js'),//awaitの部分の処理が終わるまで待つ
          true,
          ' '
        )
      )
    }
  }
</script>
```

このようにaxios-moduleを導入すると、全てのVueコンポーネントに$axiosという名称でaxiosが格納され、さらに、$get/$postなどaxiosの関数の前に＄をつけたものを利用する事で、Nuxt.jsの設定に合わせたリクエストが送信可能となります。

---

JSON.stringfyについて
>JSON.stringify() メソッドはある JavaScript の値を JSON 文字列に変換します。置き換え関数を指定して値を置き換えたり、置き換え配列を指定して指定されたプロパティのみを含むようにしたりすることができます。


```js
console.log(JSON.stringify({ x: 5, y: 6 }));
// expected output: "{"x":5,"y":6}"

console.log(JSON.stringify([new Number(3), new String('false'), new Boolean(false)]));
// expected output: "[3,"false",false]"

console.log(JSON.stringify({ x: [10, undefined, function(){}, Symbol('')] }));
// expected output: "{"x":[10,null,null,null]}"

console.log(JSON.stringify(new Date(2006, 0, 2, 15, 4, 5)));
// expected output: ""2006-01-02T15:04:05.000Z""
```

### 認証情報を付与したAPIリクエスト

```js
// plugins/axios.js
export default function({ $axios }) {
  $axios.onRequest((config) => {
    if( process.env.QIITA_TOKEN) {
      config.headers.common['Authorization'] = process.env.QIITA_TOKEN
    }
    return config
  })
}
```

ここで理解しておくことは、プラグインが一つの関数を持っているということと、その関数の引数として、axios-moduleのインスタンスである$axiosが格納されている事に注目する。

プラグインは、NuxtJsアプリケーションへと機能を追加する為のシンプルな仕組みであり、単純んあコード実行のほか、上記のように関数をエクスポートする事によって、Nuxt.js全体へのコンテキストへのインジェクションを行うことが可能である。
ここでインジェクションとは「注入」の意である。

ここでは、アプリケーションのライフサイクルの初期段階にて、$axiosに対して、onRequestを注入している。その際、QIITA_TOKEN環境変数がある場合は、設定し、これによって正常にリクエストヘッダーに認証情報が追加される事になる。

プラグインの設定の記述が完了したら、nuxt.config.js側で読み出す設定を行う。
ここでは、axios-moduleの拡張の導入と環境変数の追加を行なっている。

```js
// nuxt.config.js
{
  plugins:[
    '~/plugins/axios.js'
  ],
  env:[
    QIITA_TOKEN: process.env.QIITA_TOKEN
    ]
}
```

### 非同期通信を含むコンテンツのサーバーサイドレンダリング

前節では、動作確認のしやすさの、mountedフックからaxios-moduleを利用してデベロッパーコンソールへとデータを表示したが、次は実際に投稿一覧をレンダリングしてみる。
その際、これまでのように、mountedフックを利用していると、サーバーサイドレンダリングが適切におこわなれない。
なぜなら、mountedフックは、Vueコンポーネントの生成後、そのコンポーネントがブラウザ上で、動作し出す時に利用されるフックであるためである。

ここでは、mountedフックの代わりにasyncData関数を使って実装を進める。asyncData関数はNuxtJsが独自実装したVueコンポーネントのdata関数の拡張であり、非同期処理を実行し、結果のサーバーサイドレンダリングをする為の関数である。
data関数でも同様の処理が出来ない訳ではないが、nuxt.jsがasyncData向けに最適化されている為、サーバーサイドレンダリングを行うnuxtJsの開発では、ページコンポーネントではasyncDataを利用するようにしておくと良い。

実際にasyncDataを利用して表示ロジックを作る場合のコード

```html
<template>
  <section class="container">
    <div>
      <h3>Nuxt.jsのタグが付けられた投稿の一覧</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
          <h4>
            <span>{{ item.title }}</span>
            <small> by {{item.user.id}}</small>
          </h4>
          <div>{{item.body.slice(0, 130)}}</div>
          <p><a :href="item.url">{{item.url}}</a></p>
        </li>
      </ul>
    </div>
  </section>
</template>

<script>
  export default{
    async asyncData({ app }) {
      const items = await app.$axios.$get('https://qiita.com/api/v2/items?query=tag:nuxt.js')

      return {
        items
      }
    }
  }
</script>

<style>
  .container {
    min-height: 100vh;
    padding: 16px;
  }

  h3{
    margin: 16px;
    padding: 8px 0;
    border-bottm: solid 1px #e5e5e5;
  }

  li + li {
    margin: 16px 0;
  }

  p {
    margin: 8px 0;
  }
</style>
```

コードにおけるmountedフックでの実行との最大の違いとして、asyncData関数はコンポーネントの初期化前に行われるという点がある。つまり、この時点では、thisへのアクセスが出来ない。
VuexストアのデータをmapGetters/mapActionsなどで引き回している場合はもちろん、this.$axiosなどの利用も出来ない環境となる。

しかし、それでは実用に程遠い為、asyncDataには様々な情報が格納されたオブジェクトが渡される。子ここでは、その中から```app```を読み出し、this.$axiosの代わりにapp.$axiosを叩く為に利用している。

### ルーティングと組み合わせたサーバーサイドレンダリング

```html
<template>
  <section class="container">
    <div>
      <h3>Nuxt.jsのタグが付けられた投稿の一覧</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
          <h4>
            <span>{{ item.title }}</span>
            <!-- <small> by {{item.user.id}}</small> -->
            <small>
              <span>by </span>
              <nuxt-link :to="`/user/${item.user.id}`">
                {{item.user.id}}
              </nuxt-link>
            </small>
          </h4>
          <div>{{item.body.slice(0, 130)}}</div>
          <p><a :href="item.url">{{item.url}}</a></p>
        </li>
      </ul>
    </div>
  </section>
</template>
```

```html
<template>
  <section class="container">
    <div>
      <h3>{{user.id}}</h3>
      <img :src="user.profile_img_url" width="120" alt="">
      <p> {{user.description || 'No description'}} </p>
      <p>
        <nuxt-link to="/">
          <small><b>トップへ戻る</b></small>
        </nuxt-link>
      </p>
      <h3>{{user.id}}さんの投稿一覧</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
          <h4>
            <span>{{item.title}}</span>
          </h4>
          <div>{{item.body.slice(0, 130)}}......</div>
          <p><a taget="_blank" :href="item.url">{{item.url}}</a></p>
        </li>
      </ul>
    </div>
  </section>
</template>

<script>
  export default {
    async asyncData({ route, app }) {
      const user = await app.$axios.$get(`https://qiita.com/api/v2/users/${route.params.id}`)
      const items = await app.$axios.$get(`https://qiita.com/api/v2/items?query=user:${route.params.id}`)
      return { user, items}
    }
  }

</script>
```

## layout機能

### レイアウトファイル設計のベストプラクティス

トップページにはログインページであったり、ダッシュボードのサマリーページであったり、他のページとは大きく異なるデザインを適用することがほとんどである。
そういったときにdefault.vueをトップページ用に、それぞれのページをhoge.vueなどとした場合、全てのページコンポーネントに対して```layout: 'single'```を指定する必要があり、適用忘れなどのミスの原因になる。

## Nuxt.jsのライフサイクル

```
start
|
nuxtServerInit
|
middleware
|-1. nuxt.config.js
|-2. matching layout
|-3. matiching page & children
validate()
|
asyncData() & fetch()
|
Render
|
|-nuxt-link-> middlewareに戻る
```

## Vuexのモジュールモードを活用したオートローディング

クラシックモードでは、自身でVuexを読み込み、ストアインスタンスを生成する形でVuexストアを構築していた。
対して、モジュールモードでは、開発者は規約に沿って記述し、適切にexportを行うだけで、***Nuxt.jsが自動的に名前空間付きのモジュールとして解釈して***、Vuexストアインスタンスの生成を行う。

