# Nuxt-Blog

## Nuxt-Projectを作る

```
$ npx create-nuxt-app nuxt-blog-test && cd _$
```

```
$ yarn add contentful

$ yarn add markdown-it

$ yarn add vue2-filters

$ yarn add 
```

contentful: ContentfulとNuxt.jsを連携するのに使う
markdown-it: MarkdownmをHTMLに変換するのに使う
vue2-filters: 記事データの抽出、並び替えに使う

### sass導入

```
$ yarn add node-sass sass-loader style-loader
```

その後、pluginsフォルダの中にcontentful.jsおよび、vue2-filters.jsというファイルを作成し、それぞれ次のように編集する。

```
$ cd plugins
$ touch contentful.js
$ touch vue2-filters.js
```

```js
// pluging/contentful.js
const contentful = require('contentful')
const config = {
  space: process.env.CTF_SPACE_ID,
  accessToken: process.env.CTF_CDA_ACCESS_TOKEN
}

module.exports = {
  createClient () {
    return contentful.createClient(config)
  }
}
```

```js
// plugins/vue2-filters.js
import Vue from 'vue'
import Vue2Filters from 'vue2-filters'

Vue.use(Vue2Filters)
```

## Contentfulと連携させる

プロジェクトフォルダ内のnuxt.config.jsを次の通りに編集する。
```js
// nuxt.config.js

const config = require('./.contentful.json')
// contentfulのconfigのロード
const contentful = require('contentful')
// contentful本体のロード

const client = contentful.createClient({
  space: config.CTF_SPACE_ID,
  accessToken: config.CTF_CDA_ACCESS_TOKEN
})

module.export = {
  // 環境変数
  env: {
    CTF_SPACE_ID: config.CTF_SPACE_ID,
    CTF_CDA_ACCESS_TOKEN: config.CTF_CDA_ACCESS_TOKEN
  }
  modules: [
    '@nuxtjs/markdownit'
  ],
  plugins: [
    '~/plugins/vue2-filters'
  ],
  markdownit: {
    html: true,
    injected: true,
    linkify: true,
    breaks: false
  },
  generate: {
    routes () {
      return Promise.all([
        client.getEntries({
          'content_type': 'post'
        }),
        client.getEntries({
          'content_type': 'category'
        }),
        client.getEntries({
          'content_type': 'tag'
        })
      ]).then(([posts,categories,tags]) => {
        return [
          ...posts.items.map(post => `posts/${post.fields.slug}`),
          ...categories.items.map(category => `posts/category/${category.fields.slug}`),
          ...tags.items.map(tag => `posts/tag/${tag.fields.slug}`)
        ]
      })
    }
}
```

続いて、プロジェクトフォルダ直下に`.contentful.json`というファイルを作成して、次のように編集する。

```
$ touch .contentful.json
```

```json
{
  "CTF_SPACE_ID": "ahwbresh7yhd",
  "CTF_CDA_ACCESS_TOKEN": "Xls4xdD1qm44Fu7RHfZR6zVs1UyPysMJ8gZa3GxfS5E"
}
```

記事を表示させるためのテンプレートを作る

以上で既に、Nuxt.js側で記事を取得してページに表示させることができる状態になっている。これからページのデザインを行い、実際に記事を表示させていく。

Nuxt.jsでは、ページ内の各セクション（ヘッダー、フッター、サイドバーなど）を、コンポーネントという小さい部品に区切って管理することが出来る。
それぞれのファイルが長ったらしくならないだけでなく、１つのコンポーネントを複数の場所で使いまわすことが出来るので、非常に便利。

