---
title: "Vue MasteryのIntro to Vue 2をやってみる~Lesson 3~" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

前回は、Vue MasteryのIntro to Vue 2のlesson2をやってみました。

https://zenn.dev/moc/articles/0639685e1de33f

今回は、引き続き、lesson3をやっていきます。

# 今回の内容
lesson3では、conditional rendering（条件付きレンダリング）について学べます。

ある条件で、特定の要素を表示したり、非表示にしたりできる仕組みがVueにはあるようです。

今回も、例のごとく、事前準備が必要だったので、それをやっていきましょう。

## 事前準備
今回必要なファイルは下記のものたちです。

今回は特に、事前準備として編集しておくファイルはありません。

lesson3から始める方は、前回記事をご参照いただければと思います。

1. main.js
2. index.html
3. style.css
4. vmSocks-green.jpg

それでは、内容に入っていきます。

## 今回の目的
前回、製品の画像と製品名を表示してきました。

index.htmlをブラウザで表示すると、以下のようになっているはずです。
![](https://storage.googleapis.com/zenn-user-upload/1x8utmtvg46qy6ruhjxtgz667xob)

今回は、この製品の在庫があるかどうかをVueインスタンスに定義したデータを元に判定し、表示していきます。

## コーディングスタート
main.jsに在庫の有無を表すプロパティ（inStock）を追加します。

```diff js: main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
+        inStock: true
     }
 })
```

## 課題
webアプリケーションでは、条件に合致するかどうかでページ上に要素を表示するか決めたいときがあります。

例えば、製品の在庫がなかったら、在庫切れであることをページ上に表示すべきです。

まずは、index.htmlを編集して、**在庫あり（In Stock）**と**在庫切れ（Out of Stock）**を表示するための要素を追加します。
（該当箇所の前後のみを示します。）

```diff html:index.html
 <div class="product">

     <div class="product-image">
         <img :src="image">
     </div>

     <div class="product-info">
         <h1>{{ product }}</h1>
+        <p>In Stock</p>
+        <p>Out of Stock</p>
     </div>
 </div>
```

## 解決方法
Vueでは、これを簡単に解決できます。

今回、Vueインスタンスのデータに**inStock**というプロパティを追加したので、それを利用します。

また、要素を表示するかどうかを決定するのに、`v-if`や`v-else`ディレクティブを利用します。

index.htmlを次のように編集してみましょう。

```diff html:index.html
 <div class="product">

     <div class="product-image">
         <img :src="image">
     </div>

     <div class="product-info">
         <h1>{{ product }}</h1>
-        <p>In Stock</p>
-        <p>Out of Stock</p>
+        <p v-if="inStock">In Stock</p>
+        <p v-else>Out of Stock</p>
     </div>
 </div>
```

`inStock`が真(true)であれば、**In Stock**と表示され、偽(false)であれば、**Out of Stock**と表示されます。

現時点のコードであれば、inStockの値は真(true)なので、**In Stock**と表示されるはずです。

ブラウザで確認してみましょう。

![](https://storage.googleapis.com/zenn-user-upload/jr56di2gwlz2ds3i76c6ncapdqrs)

ちゃんと表示されましたね！

試しに、main.jsのVueインスタンスのデータのinStockプロパティの値をfalseに変えて、再表示してみてください。

きっと、**Out of Stock**と表示されるはずです。

## v-else-ifディレクティブについて
`v-if`や`v-else`以外にも`v-else-if`というディレクティブも利用することができます。

例を示すために少しだけ複雑なことをやってみます。

まずは、main.jsを次のように編集します。
inventory(在庫)というプロパティを追加します。

```diff js:main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
-        inStock: true
+        inventory: 100
     }
 })
```

次に、index.htmlを編集します。

```diff html:index.html
 <div class="product">

     <div class="product-image">
         <img :src="image">
     </div>

     <div class="product-info">
         <h1>{{ product }}</h1>
-        <p v-if="inStock">In Stock</p>
-        <p v-else>Out of Stock</p>
+        <p v-if="inventory > 10">In Stock</p>
+        <p v-else-if="inventory <= 10 && inventory > 0">Almost sold out!</p>
+        <p v-else>Out of Stock</p>
     </div>
 </div>
```

これにより、inventory（在庫）の値によって、３つの文言の出し分けができるようになりました。

まずはこのまま再表示してみます。

![](https://storage.googleapis.com/zenn-user-upload/jr56di2gwlz2ds3i76c6ncapdqrs)

inventoryの値が100（10より大きい）なので、**In Stock**と表示されました。

次に、main.jsのVueインスタンスのinventoryプロパティの値を8に変更して、再表示してみます。

![](https://storage.googleapis.com/zenn-user-upload/g3r43xw7rjt5936ikypwlzvzr70d)

inventoryの8（10より小さく、0より大きい）なので、**Almost sold out!**と表示されました。

最後にinventoryプロパティの値を0にして、再表示してみましょう。

![](https://storage.googleapis.com/zenn-user-upload/d7l5uifs7wnx9s6h2gbxjn804g7t)

**Out of Stock**と表示されましたね。

このように、`v-if`や`v-else-if`、`v-else`を使って、要素の出し分けができるようになりました。

## v-showディレクティブについて
実はもう一つ、`v-show`ディレクティブというものがあります。

このディレクティブは、ページ上の要素を頻繁に表示と非表示を切り替える場合に使うべきです。

なぜなら、`v-show`を利用している要素は、DOM上に常に存在しており、表示や非表示は、CSSのプロパティである`display: none`を付与するかどうかで制御しているからです。

::: message
Chromeの開発者ツールを利用して、この制御の違いを確認してみましょう。

index.htmlとmain.jsをそれぞれ次のように編集します。
```diff html:index.html
 <div class="product">

     <div class="product-image">
         <img :src="image">
     </div>

     <div class="product-info">
         <h1>{{ product }}</h1>
-        <p v-if="inventory > 10">In Stock</p>
-        <p v-else-if="inventory <= 10 && inventory > 0">Almost sold out!</p>
-        <p v-else>Out of Stock</p>
+        <p v-if="inStock">In Stock</p>
+        <p v-else>Out of Stock</p>
+        <hr>
+        <p v-show="inStock">In Stock</p>
+        <p v-show="!inStock">Out of Stock</p>
     </div>
 </div>
```

```diff js:main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
-        inventory: 0
+        inStock: true
     }
 })
```

この状態で、ブラウザを再表示して、開発者ツールで確認してみます。

![](https://storage.googleapis.com/zenn-user-upload/irq4t6jooez04covmvyfjsd1vda0)

少し見にくいかもしれませんが、赤枠で囲んだところが、`v-if`/`v-else`を利用した箇所で、青枠で囲んだところが、`v-show`を利用した箇所になります。

`v-if`/`v-else`を利用した場合、条件が真となる場合は、DOM上に要素が存在しますが、偽となる場合は、DOM上に要素が存在しません。

一方で、`v-show`を利用した場合は、条件の真偽問わず、DOM上に要素が存在します。
ただし、CSSの`display: none`の指定によって、ページ上の表示/非表示を切り替えています。

:::

# まとめ
lesson3では、条件付きレンダリングを勉強しました。

Vueインスタンスにもつデータによって、表示を切り替えれるのはよいですね。

今回の在庫の有無によって表示を切り替える以外にも、ログイン機能を有するwebアプリケーションであれば、ログイン状態によって、表示を切り替えるときとかにも使えそうです。

今回もできることが増えました。楽しいですね！

次はlesson4です。

お楽しみに！！
