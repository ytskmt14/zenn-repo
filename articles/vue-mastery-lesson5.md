---
title: "Vue MasteryのIntro to Vue 2をやってみる~Lesson 5~" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

前回は、Vue MasteryのIntro to Vue 2のlesson4をやってみました。

https://zenn.dev/moc/articles/vue-mastery-lesson4

今回は、引き続き、lesson5をやっていきます。

# 今回の内容
lesson5では、Event Handling（イベントハンドリング）について学びます。

それでは、やっていきましょう。

## 事前準備
今回はちょっと事前準備多めです。

使うコードは下記の通り。

:::message
style.cssについては、私が似たような見た目になるように自作したものなので、動画と完全に一致はしません。ご了承ください。
:::

1. main.js
2. index.html
3. style.css
4. vmSocks-green.jpg
5. vmSocks-blue.jpg

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

                <ul>
                    <li v-for="detail in details">{{ detail }}</li>
                </ul>

                <div v-for="variant in variants" :key="variant.variantId">
                    <p>{{ variant.variantColor }}</p>
                </div>
            </div>

            <button>Add to Cart</button>

            <div class="cart">
                <p>Cart({{ cart }})</p>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="main.js"></script>
</body>
</html>
```

```js: main.js
var app = new Vue({
    el: '#app',
    data: {
        product: 'Socks',
        image: './assets/vmSocks-green.jpg',
        inStock: true,
        details: ["80% cotton", "20% polyester", "Gender-neutral"],
        variants: [
            {
                variantId: 2234,
                variantColor: "green",
            },
            {
                variantId: 2235,
                variantColor: "blue",
            }
        ],
        cart: 0
    }
})
```

```css: style.css
.nav-bar {
    background: #58db74;
    height: 50px;
}

.product {
    margin-top: 40Px;
}

.product-image {
    float: left;
    width: 50%;
}

.product-image img {
    width: 80%;
}

.product-info {
    float: right;
    width: 50%;
}

button {
    background: #228bc8;
    border: 1px solid #228bc8;
    color: #fff;
    float: left;
    height: 40px;
}

.cart {
    border: 1px solid lightgrey;
    float:left;
    margin-left: 10px;
    padding: 1px 4px;
}
```

この時点で、index.htmlをブラウザで表示してみるとこんな感じになります。

![](https://storage.googleapis.com/zenn-user-upload/armxh7bgg7x7jriocyaktbwssjhb)

lesson4から変わったのは、**Add to Cart**ボタンと、**Cart(0)** というラベルです。

それでは、やっていきましょう。

## 今回の目的
カート内のアイテム数を増やすボタンを作ろう！

## 課題
事前準備で追加された、**Add to Cart**ボタンをクリックしたら、**Cart(n)** のnを1ずつ増やしていきたい。

**Add to Cart**ボタンにクリックイベントを追加したらよさそうですが、Vueでは次のように書きます。

index.htmlを編集してみましょう。
（該当箇所の前後のみを表示します。）

```diff html: index.html
- <button>Add to Cart</button>
+ <button v-on:click="cart += 1">Add to Cart</button>

 <div class="cart">
     <p>Cart({{ cart }})</p>
 </div>
```

`v-on`ディレクティブを使います。

この状態で、ブラウザを再表示させて、**Add to Cart**ボタンを1回クリックしてみましょう。

![](https://storage.googleapis.com/zenn-user-upload/h0byv8w8ldjc9d5ggtl1etva5j3x)

**Cart(0)** が**Cart(1)** になったことがわかるかと思います。

このように、短い処理を書くだけであれば、これでも良いのですが、処理が長くなってしまうと見づらいコードになってしまいます。

そこで、クリックイベントが発火したら、**cart**を1増やすメソッドを呼び出すようにしてみましょう。

index.htmlとmain.jsを次のように編集します。

```diff html: index.html
- <button v-on:click="cart += 1">Add to Cart</button>
+ <button v-on:click="addToCart">Add to Cart</button>

 <div class="cart">
     <p>Cart({{ cart }})</p>
 </div>
```

```diff js:main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
         inStock: true,
         details: ["80% cotton", "20% polyester", "Gender-neutral"],
         variants: [
             {
                 variantId: 2234,
                 variantColor: "green",
             },
             {
                 variantId: 2235,
                 variantColor: "blue",
             }
         ],
         cart: 0
-    }
+    },
+    methods: {
+        addToCart() {
+            this.cart += 1
+        }
+    }
 })
```

この状態で、ブラウザを再表示させて、さっきと同じように**Add to Cart**ボタンを1回クリックしてみましょう。

同じ結果になったはずです。

さて、`addToCart()`メソッドで`cart`の値をインクリメント（+1）しているわけですが、なぜか`this.cart`となっています。

この`this.`とは何者なのか、試しにこれを外して、`cart += 1`としてもう一度ブラウザを再表示させてみましょう。

そして、さっきと同じように、**Add to Cart** ボタンをクリックしてみてください。

どうでしょう？Cartの中の数字が変わりません。

javascript関連のエラーは、開発者ツールのコンソールに表示されるので、そちらを確認してみます。

![](https://storage.googleapis.com/zenn-user-upload/6dccwsaee19dtcdljwouzihovd29)

**ReferenceError: cart is not defined**と出ていますね。

これは、`cart`という変数が定義されていませんよ、という意味です。

お作法として、Vueインスタンスのメソッドから、Vueインスタンスのデータプロパティを参照したい場合は、**このVueインスタンス**のデータプロパティのcartを使いますよという意味で、`this.`をつけてあげる必要があります。

ここまでで、Vueにおけるイベントハンドリングの基礎を学びました。

もう少し複雑な例をみてみましょう。

まず、Vueインスタンスのデータの**variants**に**variantImage**を追加します。

main.jsを次のように編集してみましょう。

```diff js: main.js
 var app = new Vue({
     el: '#app',
     data: {
         product: 'Socks',
         image: './assets/vmSocks-green.jpg',
         inStock: true,
         details: ["80% cotton", "20% polyester", "Gender-neutral"],
         variants: [
             {
                 variantId: 2234,
                 variantColor: "green",
+                variantImage: './assets/vmSocks-green.jpg'
             },
             {
                 variantId: 2235,
                 variantColor: "blue",
+                variantImage: './assets/vmSocks-blue.jpg'
             }
         ],
         cart: 0
     },
     methods: {
         addToCart() {
             this.cart += 1
         }
     }
 })
```

これで、variantはそれぞれ緑と青の靴下の画像の情報をもったことになります。

## 課題
variantの色の表示部分にマウスカーソルを当てると、製品画像が切り替わるようにしたい。

## 解決方法
ここでも、`v-on`ディレクティブを使います。

今度は、マウスカーソルを当てたときのイベントなので、**click**イベントではなく**mouseover**イベントを使いましょう。

また、`v-bind`ディレクティブのときと同様に、`v-on`ディレクティブも省略することができます。

実際に、index.htmlを編集して確認していきましょう。

```diff html:index.html
 <div class="product-info">
     <h1>{{ product }}</h1>
     <p v-if="inStock">In Stock</p>
     <p v-else>Out of Stock</p>

     <ul>
         <li v-for="detail in details">{{ detail }}</li>
     </ul>

     <div v-for="variant in variants" :key="variant.variantId">
-       <p @mouseover="updateProduct(variant.variantImage)">{{ variant.variantColor }}</p>
+        <p @mouseover="updateProduct(variant.variantImage)">
+            {{ variant.variantColor }}
+        </p>
     </div>
 </div>

 <button v-on:click="addToCart">Add to Cart</button>

 <div class="cart">
     <p>Cart({{ cart }})</p>
 </div>
```

`v-on`ディレクティブの省略形は、`@` です。

ここで、**updateProduct**メソッドをmain.jsに追加していきます。

```diff js:main.js
 methods: {
     addToCart() {
         this.cart += 1
-    }
+    },
+    updateProduct(variantImage) {
+        this.image = variantImage
+    }
 }
```

index.htmlをブラウザで再表示させて、mouseoverイベントを追加した要素（greenやblueと表示されている箇所）にマウスカーソルを当ててみましょう。
（下記画像の赤枠で囲んだ箇所です。）

![](https://storage.googleapis.com/zenn-user-upload/yjm6sawznz9yqjljq8y92z6k1j19)

すると、blueにマウスカーソルを当てると、青色の靴下の画像が、greenに当てると緑色の靴下の画像が表示されると思います。

updateProductメソッド内で、`image`の値を更新するときに、`this.image`とする理由はさっきの`cart`のときと同じです。

これで、lesson5は完了！

# まとめ
lesson5では、イベントハンドリングについて学びました。

新しく`v-on`ディレクティブというものが登場しましたね。

実際のコードをみたときに、`v-bind`は`:`、`v-on`は`@`と省略されていることがほとんどだと思うので、忘れずにしっかりと覚えておきたいところです。

それでは、お付き合いいただきありがとうございました。

次回は、lesson6をやっていきます！
