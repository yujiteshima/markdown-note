# EC-SITE-APP-Plan

## 目的
ポートフォリオアプリの一つとしての制作
ec-site-appのモックアップを制作する事で、同じ仕様で、様々な技術を試して見ることが出来、技術間の比較が出来るようになる。

## 仕様
- webApp
- login/logout/signUp機能がある
- 商品の検索が出来る


ログイン後可能な機能
- 商品をカートに入れれる
- 商品のお気に入りが出来る
- 商品を購入出来る

## ショップ管理機能
まずは完全なモックアップで作る。ECサイトとの連携は無しでも良いのでフロントエンドとしては見栄えの良いものを目指す。

- 商品管理が出来る
- 売り上げが分かる
- 会員管理が出来る

## 技術仕様
EC-ShopのフロントエンドはNuxt.jsを使う。
デプロイ先はfirebase function でExpress.jsのミドルウエアとしてNuxt.jsを仕様してSSRを実現する。
CSSフレームワークはBootstrap-vueを使用する。
静的なホスティングで良い部分はfirebase hostingを使用する。
testingframeworkはjestを使う。vue-test-utilsも使用する。
strageはclout strageを使用する。
言語はjavascriptを使用する。
CIはcircleCIを使って自動テストを走らせる。
テストファーストは出来ないので、全て製作してから、勉強しながら知見を集めながらテストを書いていく。
最終的な製作過程をまとめる際にはテストファーストのように見せる。
認証はFirebaseAuthenticationを使用する。
BackendはRubyOnRailとFirebaseStoreを併用する。
RubyOnRailsはAPIモードで製作する。デプロイ先はHerokuでCIはCircleCIを使う。
今回はRailsの記述量は少なくなるかもしれないが、テスティングフレームワークはRspecを使う。
CI時に自動テストを走らせる。
FireBaseStoreとRubyOnRailの役割分担は大義名文が欲しいので、考える。
分ける目的は一つのアプリでどちらも出来るとアピールしたいから。
またfirebasestoreで管理する部分を増やすことで、製作にかかる工数や負担を減らすことが出来る。
大義名分もfirebaseStore等のnoSqlの短所と長所を理解していることをアピール出来る。
どこかでgraphQLを使用できるとこがないか探す。

管理画面はReac.jsで製作する。Next.jsを使う。
言語はTypeScriptを使う。
デプロイ先はAWSを使う。画像の保存先もAWS。AuthenticationもAWSを使う。
バックエンドはExpress.jsを使う。TypeScriptで書く。






