---
title: "Vue MasteryのIntro to Vue 2をやってみる~Lesson 4~" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

前回は、Vue MasteryのIntro to Vue 2のlesson3をやってみました。

https://zenn.dev/moc/articles/vue-mastery-lesson3

今回は、引き続き、lesson4をやっていきます。

# 今回の内容
lesson4では、List Rendering（リストレンダリング）について学べます。

htmlでは、ulタグやolタグを使って、その中にliタグを並べていくことで表現できますね。

さて、Vueを使うとどんな風に表現できるのか、楽しみです。

## 事前準備
今回は、（lesson3をやっていれば、）事前準備は不要です。

必要なファイルは下記の通りです。

1. main.js
2. index.html
3. style.css
4. vmSocks-green.jpg

それでは、やっていきましょう！

## 今回の目的
製品の詳細のリストを表示したい！

## コーディングスタート
index.htmlは現時点でこのようになっています。
```html: index.html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Product App</title>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <div class="nav-bar"></div>
    <div id="app">
        <div class="product">

            <div class="product-image">
                <img :src="image">
            </div>

            <div class="product-info">
                <h1>{{ product }}</h1>
                <p v-if="inStock">In Stock</p>
                <p v-else>Out of Stock</p>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="main.js"></script>
</body>
</html>
```

まず、main.jsに製品の詳細に関するデータ（**details**）を配列で定義します。

```diff js:main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
-        inStock: true
+        inStock: true,
+        details: ["80% cotton", "20% polyester", "Gender-neutral"],
    }
})
```

## 課題
製品の詳細をページに表示したいが、この配列を使ってどのようにして表示できるのか？

## 解決方法
`v-for`ディレクティブを利用します。

この`v-for`ディレクティブは、配列データをループ処理することができるので、配列に含まれるデータを順番に表示することが可能です。

index.htmlを編集してみましょう。
（該当箇所の前後のみ示します。）

```diff html: index.html
 <div class="product-info">
     <h1>{{ product }}</h1>
     <p v-if="inStock">In Stock</p>
     <p v-else>Out of Stock</p>

+    <ul>
+        <li v-for="detail in details">{{ detail }}</li>
+    </ul>

</div>
```

たったこれだけです。ブラウザでindex.htmlを表示してみましょう。

![](https://storage.googleapis.com/zenn-user-upload/jc6r039z14q1sp3mbkj2at2p9y2u)

うまく表示されましたね！

仕組みについてみていきます。

`v-for`ディレクティブの構文は、javascriptの`for of`や`for in`のそれとよく似ています。

単数形の`detail`はエイリアスと呼ばれ、配列の中の値が順番に入る入れ物となります。

`in`の後の`details`は、コレクションと呼ばれ、ループ処理を行う対象を指定します。

今回、`v-for`ディレクティブはliタグの中で利用されているので、`details`の配列の中の値の個数分liタグが出力されます。

それでは、もう少し複雑な例をみてみましょう。

## オブジェクトの繰り返し

製品ページでは、異なる型番の同一製品を表示できるようにする必要があります。

そこで、main.jsに`variants`というデータを用意します。
今回の例では、同じ詳細情報(**detail**)をもつ色違いの製品があるとしましょう。

```diff js:main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
         inStock: true,
         details: ["80% cotton", "20% polyester", "Gender-neutral"],
+        variants: [
+            {
+                variantId: 2234,
+                variantColor: "green"
+            },
+            {
+                variantId: 2235,
+                variantColor: "blue"
+            }
+        ]
    }
})
```

次に、index.htmlを編集して、`variants`の中の`variantColor`を全て表示できるようにします。

```diff html:index.html
 <div class="product-info">
     <h1>{{ product }}</h1>
     <p v-if="inStock">In Stock</p>
     <p v-else>Out of Stock</p>

     <ul>
         <li v-for="detail in details">{{ detail }}</li>
     </ul>

+    <div v-for="variant in variants">
+        <p>{{ variant.variantColor }}</p>
+    </div>

 </div>
```

この状態でindex.htmlをブラウザで再表示してみましょう。

![](https://storage.googleapis.com/zenn-user-upload/5qgtmhuarzstsklutq2mkpznm1sk)

greenとblueが表示できましたね！

今回の場合、variantオブジェクトから色の情報（**variantColor**）を表示させています。

もし、`{{ variant }}`のみを記載すると、オブジェクト全体が表示されます。

興味のある方は試してみましょう。

なお、このように複数要素を表示する場合、特定のキー属性を使うことが推奨されます。

今回の場合、`variant`のユニークなキーである、`variantId`プロパティを使いましょう。

index.htmlを次のように編集します。
（表示は変わりません。）

```diff html:index.html
　<div class="product-info">
 　   <h1>{{ product }}</h1>
  　  <p v-if="inStock">In Stock</p>
   　 <p v-else>Out of Stock</p>

　    <ul>
  　      <li v-for="detail in details">{{ detail }}</li>
    　</ul>

-    <div v-for="variant in variants">
+    <div v-for="variant in variants" :key="variant.variantId">
         <p>{{ variant.variantColor }}</p>
     </div>

 </div>
```

# まとめ
lesson4では、Rendering（リストレンダリング）について学びました。

`v-for`ディレクティブを用いることで、Vueインスタンスに定義した配列データをその値の個数分、簡単に表示できるようになりました。

