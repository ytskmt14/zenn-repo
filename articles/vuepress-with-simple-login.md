---
title: "【続】vuepressで簡易ログイン機能付きのドキュメントを作ってみる" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue","vuepress","gas"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

先日は、vuepressで簡易ログイン付きのドキュメントを作成しました。

https://zenn.dev/moc/articles/vuepress-init

しかし、Login.vueにログイン可能なユーザ情報をベタがきしてるのがイケてない。。。

なので、今回はそれを解消していきます。

# 今回の内容
今回は、ログイン可能なユーザ情報を別のところに保持させて、ログインするときにその情報を参照して、ログイン判定を行えるようにします。

やることとしては、こんな感じです。

1. ログイン可能なユーザ情報を保持する場所を作る
2. 1.に保持している情報を外部から参照できるようにする
3. vuepress側で1の情報を見に行けるようにする

## 1.ログイン可能なユーザ情報を保持する場所を作る
今回は、google spreadsheetを使っていきます。

こんな感じでユーザ一覧を作ってみましょう。

![](https://storage.googleapis.com/zenn-user-upload/3xi9huf7ydo8z1vibmgs96h5ij6w)

ヘッダー行を、**username**と**password**としておきます。

そしてデータ行に、サンプルデータとして10個のデータを登録しておきます。
（ここは何を入れても大丈夫です。好きなデータをいれてみましょう。）

これでユーザ情報の準備は完了です、簡単！

## 2. 1に保持している情報を外部から参照できるようにする
spreadsheetに保持しているデータを外部から参照できるようにするためには、google apps scriptを利用します。

spreadsheetのメニューから、**ツール > スクリプトエディタ**を選択します。

![](https://storage.googleapis.com/zenn-user-upload/1kjxx9ftbbtbrg1gcpkthddd7sb2)

すると、google apps scriptの画面が開くので、デフォルトで作成されているファイル（コード.gs）を編集していきます。

![](https://storage.googleapis.com/zenn-user-upload/mnoshb2fkly4evdnp7o9s0y1djcb)

まずは、ファイル名をわかりやすくするために、**getValidUserList.gs**に変更します。

次にファイルの内容を下記のように編集します。

```js: getValidUserList.gs
function doGet() {
  var data = getAllUserList();

  var payload = JSON.stringify(data);
  var output = ContentService.createTextOutput();
  output.setMimeType(ContentService.MimeType.JSON);
  output.setContent(payload);  

  return output;
}

function getAllUserList() {
  // ユーザ一覧のシートを取得する
  var sheet = SpreadsheetApp.getActive().getSheetByName('list')
  // 取得したシートから、値が入っている範囲を取得する
  var range = sheet.getDataRange().getValues();

  // ヘッダー行の値を定義する
  var header = ['username', 'password'];
  // ユーザリスト
  var allUserList = [];

  // 取得したデータ全件ループ処理
  range.forEach(user => {
    // header行を処理対象外とする
    if (user[0] !== header[0]) {
      // ユーザ情報を作成
      var user_info = {
        username: user[0],
        password: user[1]
      }
      // ユーザリストにユーザ情報を追加
      allUserList.push(user_info)
    }
  })

  return allUserList;
}
```

簡単に解説です。

まず、このファイルには２つの関数が定義されています。

**doGet**関数と**getAllUserList**関数です。

doGet関数は、外部からGETリクエストがきたときに実行される関数です。
（3で、vuepressからユーザの情報を参照しようとしたときに、実行されます。）

ざっくりとした流れとしては、たったこれだけ。
* ユーザ一覧のデータを取得
* JSON形式のデータを返却

getAllUserList関数は、ユーザ一覧のスプレッドシートから情報を取得するための関数です。
doGet関数から呼ばれます。

処理の流れは、先ほど示したソースコードにコメントを書いているので、なんとなくお分かりいただけたかなと思います。

これで、ユーザ情報を取得する準備は完了です！

## 3. vuepress側で1の情報を見に行けるようにする
それでは最後のステップです。

前回作成したvuepressのコードをちょこっとだけ修正します。

と、その前に、外部の情報を取得するための**axios**というライブラリをインストールしていきましょう。

```sh
$ npm install axios -D
```

これができれば、後は、**Login.vue**のスクリプトタグのコードを修正していきます。

```diff js: Login.vue
 <script>
 import { STORAGE_KEY } from "./helper";
+import axios from 'axios';
+const url = "https://script.google.com/macros/s/AKfycbzk8_6iucpP6BjUKoUWuiMUEbJbbWvTU-809k1cQDx7tsHaRL1qC69G2AMfkpr4XA7v/exec"

 export default {
   data() {
     return {
       username: "",
       password: "",
       validUserList: [],
     }
   },
+  async mounted() {
+    this.validUserList = await this.getValidUserList();
+  },
   methods: {
+    async getValidUserList() {
+      const result = await axios.get(url);
+      return result.data;
+    },
     login() {
      const user = this.validUserList.find(
-        validUser => validUser.name === this.username
+        validUser => validUser.username === this.username && validUser.password === this.password
+      );
      if (user) {
         const data = JSON.stringify({
           name: user.name,
           time: new Date().getTime(),
         });
         window.localStorage.setItem(STORAGE_KEY, data);
         this.$emit("close", true);
       } else {
         this.$dlg.alert("Please enter valid user info", {
           messageType: "warning",
         });
       }
     },
 };
 </script>
```

これで全ての準備が整いました！

ローカル実行して試してみましょう。

画面を開いて、ユーザ一覧に記載していないユーザ名とパスワードを入力して、**OK**ボタンをクリックします。
（ユーザ名に**test99**を、パスワードに**password99**と入力しました。）

![](https://storage.googleapis.com/zenn-user-upload/j3v1gko5d2o0e72fejaiclvvht3h)

ユーザ一覧にない情報なので、ログインできませんでした。

![](https://storage.googleapis.com/zenn-user-upload/l33r45t221475qzsyx2mmvk74dm5)

次に、ユーザ一覧に記載しているユーザ名とパスワードの組み合わせを入力して、**OK**ボタンをクリックします。
（ユーザ名に**test5**を、パスワードに**password5**と入力しました。）

![](https://storage.googleapis.com/zenn-user-upload/xwlujw2sb6a7gbfn2ect0g3a8o7g)

ユーザ一覧にある情報なので、無事ログインできました。

![](https://storage.googleapis.com/zenn-user-upload/xm6iajif98nuik0ceq5xt3nv1225)

# まとめ
今回は、ベタがきしていたvalidUserListを外部から取得するようにしてみました。

google spreadsheetとgoogle apps script(gas)を使うと割と簡単に作ることができました！

いやー、いい感じです。

これからは、vuepressについてもうちょっと深めていきたいところです！！
