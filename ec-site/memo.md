# EC-SHOP-MOCK-UP MEMO

## create Nuxt project

```
yujiMBP:works yujiteshima$ npx create-nuxt-app ec-shop-mock-up
> Generating Nuxt.js project in /Users/yujiteshima/works/ec-shop-mock-up
? Project name ec-shop-mock-up
? Project description My praiseworthy Nuxt.js project
? Use a custom server framework none
? Choose features to install Progressive Web App (PWA) Support, Linter / Formatter, Prettier, Axios
? Use a custom UI framework bootstrap
? Use a custom test framework jest
? Choose rendering mode Universal
? Author name Yuji Teshima
? Choose a package manager yarn
```

## 初期設定

assets, commponents, layouts, middleware, pages, plugins, static ディレクトリをappディレクトリを作成して、移動させる。

### appディレクトリに移動した事によるpathの修正

```js
// nuxt.config.js
export default {
  mode: 'universal',
  srcDir: 'app', // <- 追記
}
```

テストの設定ファイルのpathも修正しておく。

```js
// jest.config.js
module.exports = {
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/$1',
    '^~/(.*)$': '<rootDir>/$1',
    '^vue$': 'vue/dist/vue.common.js'
  },
  moduleFileExtensions: ['js', 'vue', 'json'],
  transform: {
    '^.+\\.js$': 'babel-jest',
    '.*\\.(vue)$': 'vue-jest'
  },
  collectCoverage: true,
  collectCoverageFrom: [
    // - '<rootDir>/components/**/*.vue',
    // - '<rootDir>/pages/**/*.vue'
    '<rootDir>/app/components/**/*.vue', // <- path変更
    '<rootDir>/app/pages/**/*.vue'       // <- path変更
  ]
}
```

```js
// Logo.spec.js

import { mount } from '@vue/test-utils'
// - import Logo from '@/components/Logo.vue'
import Logo from '@/app/components/Logo.vue' // <- path変更
```


### 一度、動きを確認
```
$ cd ec-shop-mock-up
$ yarn dev -p 3030
```
テストもチェック
```
$ yarn test
```
### linterの設定

lintfixのコマンドを追記しておく

```js
// package.json
"lintfix": "eslint --fix --ext .js,.vue --ignore-path .gitignore .", // <- 追記
```
### FontAwesome導入

```
$ yarn add nuxt-fontawesome
$ yarn add @fortawesome/fontawesome-svg-core @fortawesome/vue-fontawesome
$ yarn add @fortawesome/free-solid-svg-icons
```

nuxt.config.jsでmodulsにnuxt-fontawesomeを追加

```js
// nuxt.config.js
module.exports = {
 modules: [
  ...
    'nuxt-fontawesome' //ここに追記
  ],

  ...

//この部分を追記
 fontawesome: {
    imports: [
      {
        set: '@fortawesome/free-solid-svg-icons',
        icons: ['fas']
      }
    ]
  }
}
```
Logo.vueにでも記述して、当たっているか確認しておく。
```html
  <font-awesome-icon icon="shopping-cart"></font-awesome-icon>
```

### sass導入

```
$ yarn add node-sass sass-loader style-loader
```

### 初期設定コメント

ディレクトリの構成は、好みの問題や他にも最適解あるのかもしれないが、Nuxt ビギナーズブックで提示された、appフォルダに集約する方法を採用する。テストファイルを同じディレクトリ構成で作り、可読性を高めたり、単純に自分がこのディレクトリ構成に慣れてきたという為にこのディレクトリ構成でいく。

### Git に FirstCommit、　FirstPushしておく。

```
$ git add .
$ git commit -m "first commit"
$ git remote add origin https://github.com/yujiteshima/ec-shop-mock-up.git
$ git push -u origin master
```

## Headerの作成

### Headerの仕様

- ブランドロゴある
- 検索フォームがある
- 検索フォームはセレクトとインプットがくっついているもの
- 新規登録リンクがある
- ログインリンクがある
- カートページリンクがある

### Gitのブランチ作成

```
$ git checkout -b create-TheHeader
```

### Header.vue作成
1. app/componentsディレクトリにTheHeader.vueを作成する。
2. testディレクトリにもcomponentsディレクトリを作成して、TheHeader.spec.jsを作成。
3. ついでにLogo.spec.vueもtest/componentsディレクトリに移動しておく。

### TheHeader.spec.js作成
テストファーストで書く。
リンクが存在するかどうかのチェック
```js
import { mount, createLocalVue, RouterLinkStub } from '@vue/test-utils'
import TheHeader from '~/app/components/TheHeader.vue'

const localVue = createLocalVue()

describe('components/TheHeader.vue', () => {
  test('TopPageへのリンクが存在する', () => {
    const wrapper = mount(TheHeader, {
      localVue,
      stubs: {
        NuxtLink: RouterLinkStub
      }
    })
    expect(wrapper.find(RouterLinkStub).props().to).toBe('/')
  })
})

```

```
$ yarn test
>> Failed to mount component: template or render function not defined.
>> [vue-test-utils]: find did not return Component, cannot call props() on empty Wrapper
```
抜粋だがTheHeader.vueが空っぽであるのでエラーが出た。


### TheHeader.vueを記述していく。

```html
<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="secondary">
      <b-navbar-brand>
        <nuxt-link to="/">EC-SHOP-MOCK-UP</nuxt-link>
      </b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <!-- <b-nav-item href="#">Link</b-nav-item>
          <b-nav-item href="#" disabled>Disabled</b-nav-item> -->
          <b-nav-item>
            <b-nav-form>
              <b-form-select
                v-model="selected"
                :options="options"
                size="sm"
                class="select-form"
              >
              </b-form-select>
              <b-form-input
                v-model="keyword"
                size="sm"
                placeholder="Search"
              ></b-form-input>
              <b-button
                size="sm"
                placeholder="Search"
                variant="success"
                @click="search"
              >
                Search
              </b-button>
            </b-nav-form>
          </b-nav-item>
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-navbar-nav class="ml-auto mr-5">
            <b-nav-item v-if="!isLogin">
              <nuxt-link to="/auth">Login/SignUp</nuxt-link>
            </b-nav-item>
            <b-nav-item v-if="isLogin">
              <nuxt-link to="/auth">
                Logout
              </nuxt-link>
            </b-nav-item>
            <b-nav-item>
              <nuxt-link to="/cart">
                <font-awesome-icon icon="shopping-cart"></font-awesome-icon>
              </nuxt-link>
            </b-nav-item>
          </b-navbar-nav>
          <b-nav-item-dropdown text="Lang" right>
            <b-dropdown-item href="#">EN</b-dropdown-item>
            <b-dropdown-item href="#">ES</b-dropdown-item>
            <b-dropdown-item href="#">RU</b-dropdown-item>
            <b-dropdown-item href="#">FA</b-dropdown-item>
          </b-nav-item-dropdown>

          <b-nav-item-dropdown right>
            <!-- Using 'button-content' slot -->
            <template slot="button-content"
              ><em>User</em></template
            >
            <b-dropdown-item href="#">Profile</b-dropdown-item>
            <b-dropdown-item href="#">Sign Out</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selected: null,
      keyword: '',
      options: [
        { value: null, text: 'All Item' },
        { value: 'typeA', text: 'A' },
        { value: 'typeB', text: 'B' },
        { value: 'typeC', text: 'C' },
        { value: 'typeD', text: 'D' }
      ],
      isLogin: true
    }
  },
  methods: {
    search() {
      console.log('this is search')
    }
  }
}
</script>

<style lang="scss" scoped>
a {
  color: bisque;
  text-decoration: none;
}
</style>
```

### The Header.vue作成の際の気付きポイント

- nuxt-linkの中のtextにcssを効かす為には、ローダーを通した後の```a```タグを指定しなくてはならない。
- ここでのメソッドとstateはモックなので、Storeを作成したら連携させる。
- 単体テストで何をテストしなくてはならないのかを考えなくてはならない。

### TheHeader.vueを作成しなので、Gitのブランチを結合させる。

```
$ git checkout master

$ git merge create-TheHeader
```

## default.vueの作成

### buranchを作成
```
$ git checkout -b create-top-page
```

上下左右中央揃えにするクラスセット

```html
class=" d-flex align-items-center justify-content-center"
```
colmの中のtextを縦並びにしたい時に、flexボックスを使ってしまうと、```br```を使っても横に並んでしまう。この際は、```flex-column```を使う。

```html
class = "d-flex flex-column align-items-center justify-content-center"
```

### Top Page 作成

```html
<template>
  <b-container>
    <b-row class="top-row mx-1 row-bg">
      <b-col cols="12">
        <div
          class="bg-primary content-col m-1 d-flex align-items-center justify-content-center"
        >
          ここにスライダーを実装する
        </div>
      </b-col>
    </b-row>
    <b-row class="about-row m-1 row-bg2">
      <b-col sm="6">
        <div
          class="bg-success content-col m-1 d-flex align-items-center justify-content-center"
        >
          おすすめとかのテキスト
        </div>
      </b-col>
      <b-col sm="6">
        <div
          class="bg-info content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">
            おすすめのイメージ
          </nuxt-link>
        </div>
      </b-col>
    </b-row>
    <b-row class="topics-row m-1 row-bg">
      <b-col sm="12">
        <div
          class="bg-secondary topic-header m-1 d-flex align-items-center justify-content-center"
        >
          TopicsのHeader
        </div>
      </b-col>
      <b-col sm="6">
        <div
          class="bg-success content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">
            Topicsのイメージ
          </nuxt-link>
        </div>

        Topicsのテキスト
      </b-col>
      <b-col sm="6">
        <div
          class="bg-info content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">
            Topicsのイメージ
          </nuxt-link>
        </div>
        <div>Topicsのテキスト</div>
      </b-col>
    </b-row>

    <b-row class="new-row m-1 row-bg2">
      <b-col cols="6" sm="3">
        <div
          class="bg-success content-col m-1 d-flex flex-column align-items-center justify-content-center"
        >
          <div>新商品のテキスト</div>
          <div>
            <nuxt-link to="#">MORE</nuxt-link>
          </div>
        </div>
      </b-col>
      <b-col cols="6" sm="3">
        <div
          class="bg-info content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">新商品のイメージ</nuxt-link>
        </div>
        <div>新商品名 ¥価格</div>
      </b-col>
      <b-col cols="6" sm="3">
        <div
          class="bg-primary content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">新商品のイメージ</nuxt-link>
        </div>
        <div>新商品名 ¥価格</div>
      </b-col>
      <b-col cols="6" sm="3">
        <div
          class="bg-warning content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">新商品のイメージ</nuxt-link>
        </div>
        <div>新商品名 ¥価格</div>
      </b-col>
    </b-row>

    <b-row class="category-row m-1 row-bg">
      <b-col sm="12">
        <div
          class="bg-success m-1 d-flex align-items-center justify-content-center"
        >
          新商品のテキスト
        </div>
      </b-col>
      <b-col sm="4">
        <div
          class="bg-info content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">カテゴリのイメージ</nuxt-link>
        </div>
      </b-col>
      <b-col sm="4">
        <div
          class="bg-primary content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">カテゴリのイメージ</nuxt-link>
        </div>
      </b-col>
      <b-col sm="4">
        <div
          class="bg-warning content-col m-1 d-flex align-items-center justify-content-center"
        >
          <nuxt-link to="#">カテゴリのイメージ</nuxt-link>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<style lang="scss" scoped>
div {
  padding: 0 15px 0 0;
}
a {
  color: bisque;
  text-decoration: none;
  &:hover {
    color: firebrick;
  }
}
.row-bg {
  background-color: cadetblue;
}
.row-bg2 {
  background-color: burlywood;
}

.top-row {
  .content-col {
    height: 50vh;
  }
}
.about-row {
  .content-col {
    height: 30vh;
  }
}
.topics-row {
  .content-col {
    height: 30vh;
  }
}
.new-row {
  .content-col {
    height: 30vh;
  }
}
.category-row {
  .content-col {
    height: 30vh;
  }
}
</style>
```

### Git Merge

```
$ git checkou master
$ git merge create-top-page
```

### Jumbotron 作成

### buranchを作成

```
$ git checkout -b create-Jumbotron
```


## Firebase
### FirebaseSDK
```
<!-- The core Firebase JS SDK is always required and must be listed first -->
<script src="https://www.gstatic.com/firebasejs/6.2.4/firebase-app.js"></script>

<!-- TODO: Add SDKs for Firebase products that you want to use
     https://firebase.google.com/docs/web/setup#config-web-app -->

<script>
  // Your web app's Firebase configuration
  var firebaseConfig = {
    apiKey: "AIzaSyBBr9TUwA6Pq_uzNKQYcNaBxc7Ge8ImepY",
    authDomain: "ec-shop-mock-up.firebaseapp.com",
    databaseURL: "https://ec-shop-mock-up.firebaseio.com",
    projectId: "ec-shop-mock-up",
    storageBucket: "ec-shop-mock-up.appspot.com",
    messagingSenderId: "1049935503390",
    appId: "1:1049935503390:web:b23c9bd07a6e6799"
  };
  // Initialize Firebase
  firebase.initializeApp(firebaseConfig);
</script>
```

```
$ yarn add firebase firebaseui
```

```js
import firebase from 'firebase'

if (!firebase.apps.length) {
  firebase.initializeApp({
    apiKey: "AIzaSyBBr9TUwA6Pq_uzNKQYcNaBxc7Ge8ImepY",
    authDomain: "ec-shop-mock-up.firebaseapp.com",
    databaseURL: "https://ec-shop-mock-up.firebaseio.com",
    projectId: "ec-shop-mock-up",
    storageBucket: "ec-shop-mock-up.appspot.com",
    messagingSenderId: "1049935503390",
    appId: "1:1049935503390:web:b23c9bd07a6e6799"
  })
}

export default firebase
```

```
$ yarn add firebaseui-ja --save
```

```
build: {
    /*
     ** You can extend webpack config here
     */
    extend(config, ctx) {
      // Run ESLint on save
      if (ctx.isDev && ctx.isClient) {
        config.devtool = 'inline-cheap-module-source-map'
        config.module.rules.push({
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /(node_modules)/
        })
      }
    }
  }
```

```
$ yarn add vuex-persistedstate
```