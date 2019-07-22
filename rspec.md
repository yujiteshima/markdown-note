# RSpec

## RSpceの書き方

```rb
describe [仕様を記述する対象(テスト対象)], type: [Specの種類] do

  context [ある状況・状態] do
    before do
      [事前準備]
    end

    it [仕様の内容(期待の概要)] do
      [期待する動作]
    end
  end

end
```

itに到達するまでに、describeやcontextを何個もネストする(重ねて使う)事もできる(type指定は外側でのdescribeだけに行う)。
内部のcontext,describe,itを並べて複数記述する事も出来る。

ネストしたり、複数記述したSystem Specのコードは、次のような雰囲気になる。

```rb
describe '~機能', type: :system do

  describe '登録' do

    context '○○の場合' do
      before do
        # (context 内を確認するのに必要な)事前準備
      end

      it '△△する' do
        # 期待する動作
      end
    end

    context '××の場合' do
      before do
        # (context 内を確認するのに必要な)事前準備
      end

      it '○○する' do
        # 期待する動作
      end
    end
  end

  describe '削除' do
    # 略
  end
end
```

RSpecの用語は、仕様書を記述しているという感覚を持ちやすいようにデザインされている。

ここで登場する用語は、２つのタイプに分けることが出来る。

1. テストケースを整理・分類する... describe, context
2. テストコードを実行する........ before, it

describeには「何について仕様を記述しようとしているのか」(テストの対象)を記述する。

System Specでは、一連の操作によって達成したい機能や処理の名前を記述する事になるでしょう。
Model Specでは、モデルクラス名やメソッド名を記述していく場所になる。describeのブロック(do~end)の中に、仕様の内容として、テストの本体を記述していく。
