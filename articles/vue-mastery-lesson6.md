---
title: "Vue MasteryのIntro to Vue 2をやってみる~Lesson 6~" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

前回は、Vue MasteryのIntro to Vue 2のlesson5をやってみました。

https://zenn.dev/moc/articles/vue-mastery-lesson5

今回は、引き続き、lesson6をやっていきます。

# 今回の内容
lesson6では、Class & Style Binding（クラスとスタイルのバインディング）について学びます。

それでは、やっていきましょう。

## 事前準備
code Penのリンクを完全に見落としていました。。
そこにstyle.cssのコードがあったので、それを記載します。
その他は、特に編集不要です。

* style.css

```diff css: style.css
body {
  font-family: tahoma;
  color:#282828;
  margin: 0px;
}

.nav-bar {
  background: linear-gradient(-90deg, #84CF6A, #16C0B0);
  height: 60px;
  margin-bottom: 15px;
}

#app {
  display: flex;
}

img {
  border: 1px solid #d8d8d8;
  width: 70%;
  margin: 40px;
  box-shadow: 0px .5px 1px #d8d8d8;
}

.product-image {
  flex-basis: 700px;
}

.product-info {
  margin-top: 10px;
  flex-basis: 500px;
}

.color-box {
  width: 40px;
  height: 40px;
  margin-top: 5px;
}

.cart {
  margin-right: 25px;
  float: right;
  border: 1px solid #d8d8d8;
  padding: 5px 20px;
}

button {
  margin-top: 30px;
  border: none;
  background-color: #1E95EA;
  color: white;
  height: 40px;
  width: 100px;
  font-size: 14px;
} 

.disabledButton {
  background-color: #d8d8d8;
}

.review-form {
  width: 30%;
  padding: 20px;
  border: 1px solid #d8d8d8;  
}

input {
  width: 100%;  
  height: 25px;
  margin-bottom: 20px;
}

textarea {
  width: 100%;
  height: 60px;
}

.outOfStock {
  text-decoration: line-through;
```

## 今回の目的
製品のカラーバリエーションをテキストではなく、カラーサンプルを実際に表示したい。

## 課題
lesson5では、**blue**や**green**といったテキストの表示エリア（`p`タグ）にマウスカーソルを当てると、製品画像を更新するというイベントハンドラーを作成しました。

今回は、`p`タグの代わりに、`div`タグの`background-color`というスタイルを使って、カラーサンプルを表示したい。

## 解決方法
index.htmlを下記のように編集します。

```diff html: index.html
- <div v-for="variant in variants" :key="variant.variantId">
+ <div class="color-box" 
+      v-for="variant in variants"
+      :key="variant.variantId"
+      :style="{ backgroundColor: variant.variantColor}"
+      @mouseover="updateProduct(variant.variantImage)"
+ >
     <p >{{ variant.variantColor }}</p>
 </div>
```

この状態で、index.htmlをブラウザで表示してみます。
（cssファイルを入れ替えたので、全体的なレイアウトが前回までと異なります。）

![](https://storage.googleapis.com/zenn-user-upload/aehvjlzlc39jltbiqpmtphbymlj5)

**blue**や**green**とテキストで表示されていた箇所が、カラーサンプルの枠として表示されるようになりました。

マウスカーソルを当てても、ちゃんと製品画像が更新されます。

次に、**Add to cart**ボタンにも修正を加えていきます。

目的としては、在庫切れ（`inStock: false`）のときに、ボタンを非活性にすることです。

index.htmlを編集しましょう。

```diff html: index.html
- <button v-on:click="addToCart">Add to Cart</button>
+ <button v-on:click="addToCart" 
+         :disabled="!inStock"
+         :class="{ diabledButton: !inStock}"
+         >Add to Cart
+ </button>
```

main.jsの`inStock`の値をfalseの状態にして、ブラウザで再表示してみます。

![](https://storage.googleapis.com/zenn-user-upload/zuih28lucf31e81nbvsyuutimhzt)

**Add to Cart**ボタンが薄いグレーになり、ボタンをクリックしても、カウントアップされないようになりました。

# まとめ
lesson6では、Class & Style Bindingについて学びました。

見た目の部分もいろいろ制御できるようになったので、できることの幅が広がってきた感じがしますね。

次回は、lesson7をやっていきます！

