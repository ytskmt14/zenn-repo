---
title: "【完？】vuepressで簡易ログイン機能付きのドキュメントを作ってみる" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue","vuepress","gas"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

前回は、vuepress x gas x spreadsheetで、簡易ログイン付きのドキュメントを作成してみました。

https://zenn.dev/moc/articles/vuepress-init

しかし、よくよく考えてみると、課題・改善点が出てきたので、今回はそれを修正していきます。

# 今回の内容
前回までの内容で、よくなかったところは次の点です。

* ログイン画面表示時に、ユーザ情報を取得してしまっていたので、情報流出の可能性がある
* パスワードを平文でやりとりしているのはやばい
* しかもGETでAPI叩いてるので、悪意のある人からするといいカモ

ということで、これらを解消すべく次のような方針で修正していきます。

* ログイン可能かどうかは、gas側で判定を行う
* API叩くときは、GETではなくPOSTで
* パスワードはハッシュ化する

それでは、直していきます。

## vuepress側の修正
ディレクトリ構成は以前のままです。

```sh
$ tree
.
├── .gitignore
├── docs
│   ├── .vuepress
│   │   ├── config.js
│   │   ├── enhanceApp.js
│   │   ├── login
│   │   │   ├── Login.vue
│   │   │   └── helper.js
│   │   ├── public
│   │   └── styles
│   │       └── index.styl
│   └── README.md
└── package.json
```

今回修正していくのは、**Login.vue**のscriptタグの中のコードのみです。

現在のコードはこのようになっています。
```js
<script>
import { STORAGE_KEY } from "./helper";
import axios from 'axios';
const url = "https://script.google.com/macros/s/[your-deploy-id]/exec"

export default {
  data() {
    return {
      username: "",
      password: "",
      validUserList: [],
    }
  },
  async mounted() {
    this.validUserList = await this.getValidUserList();
  },
   methods: {
    async getValidUserList() {
      const result = await axios.get(url);
      return result.data;
    },
    login() {
      const user = this.validUserList.find(
        validUser => validUser.username === this.username && validUser.password === this.password
      );
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

先に完成版を示します。

```js
<script>
import { STORAGE_KEY } from "./helper";
import axios from 'axios';
const url = "https://script.google.com/macros/s/[your_deploy_id]/exec"

export default {
  data() {
    return {
      username: "",
      password: "",
    }
  },
  methods: {
    login() {
      // ユーザ名、パスワードの入力チェック
      if (this.username && this.password) {
        // 入力済みの場合は、apiを叩く
        var hashedPassword = '';
        this.sha256(this.password).then(hp => {
          console.log(hp);
          hashedPassword = hp;
        }).catch(error => {
          console.log(error);
        });
        var param = {
          username: this.username,
          password: hashedPassword
        };
        const options = {
          method: 'POST',
          headers: { 'content-type': 'application/x-www-form-urlencoded' },
          data: param,
          url,
        };
        axios(options).then(response => {
          if (response.data.isValidUser) {
            // 送信したユーザ名とパスワードの組み合わせが
            // データ一覧に存在した場合
            const data = JSON.stringify({
              name: this.username,
              time: new Date().getTime(),
            });
            window.localStorage.setItem(STORAGE_KEY, data);
            this.$emit("close", true);
          } else {
            // ユーザ名とパスワードの組み合わせが正しくない場合
            this.$dlg.alert("Please enter valid user info", {
              messageType: "warning",
            });
          }
        });
      } else {
        // どちらか一方でも未入力であればエラーとする
        this.$dlg.alert("Please enter both username and password", {
          messageType: "warning",
        });
      }
    },
    async sha256(text){
      const uint8  = new TextEncoder().encode(text)
      const digest = await crypto.subtle.digest('SHA-256', uint8)
      return Array.from(new Uint8Array(digest)).map(v => v.toString(16).padStart(2,'0')).join('')
    }
  },
};
</script>
```

変更点はざっくり３つ。

1. vueインスタンスの**validUserList**の削除
vuepress側で使わなくなったので、削除します。
```diff js
 var param = {
     username: this.username,
-     password: hashedPassword,
+     password: hashedPassword
-    validUserList: [],
 };
```
2. **login**メソッドの修正
前回までは、取得したユーザ一覧の情報とログインフォームに入力した情報を比較していました。

それを今回は、入力した情報をgas側に送信、そのレスポンスの情報からログインできるかどうかを判断するようにしてみます。
（前回からの差分が結構あるので、修正後のもののみ示します。）

流れとしては、
* ログインフォームの入力チェック
* パスワードのハッシュ化
* リクエストパラメータの作成
* gasで作成したAPIを叩く
* レスポンスの値を判定する
といったような感じです。

```js
login() {
  // ユーザ名、パスワードの入力チェック
  if (this.username && this.password) {
    // 入力済みの場合は、apiを叩く
    var hashedPassword = '';
    this.sha256(this.password).then(hp => {
      console.log(hp);
      hashedPassword = hp;
    }).catch(error => {
      console.log(error);
    });
    var param = {
      username: this.username,
      password: hashedPassword
    };
    const options = {
      method: 'POST',
      headers: { 'content-type': 'application/x-www-form-urlencoded' },
      data: param,
      url,
    };
    axios(options).then(response => {
      if (response.data.isValidUser) {
        // 送信したユーザ名とパスワードの組み合わせが
        // データ一覧に存在した場合
        const data = JSON.stringify({
          name: this.username,
          time: new Date().getTime(),
        });
        window.localStorage.setItem(STORAGE_KEY, data);
        this.$emit("close", true);
      } else {
        // ユーザ名とパスワードの組み合わせが正しくない場合
        this.$dlg.alert("Please enter valid user info", {
            messageType: "warning",
        });
      }
    });
  } else {
    // どちらか一方でも未入力であればエラーとする
    this.$dlg.alert("Please enter both username and password", {
      messageType: "warning",
    });
  }
}
```

3. **sha256**メソッドの追加
先ほどの**login**メソッドで利用している、パスワードをハッシュ化するためのメソッドを追加します。

```js
async sha256(text){
    const uint8  = new TextEncoder().encode(text)
    const digest = await crypto.subtle.digest('SHA-256', uint8)
    return Array.from(new Uint8Array(digest)).map(v => v.toString(16).padStart(2,'0')).join('')
}
```

これで、vuepress側の修正は完了です。

## gas側の修正
次に、gas側のコードを修正していきます。

完成形はこんな感じになります。
```js
// POST リクエスト処理
function doPost(e) {
  var params = JSON.parse(e.postData.getDataAsString());  
  // リクエストパラメータからusernameとpasswordを取得する
  var username = params.username;
  var password = params.password;

  // ログイン可能ユーザかどうか判定する
  var judgeResult = false;
  // ユーザ一覧からデータを取得
  var validUserList = getAllUserList();

  // リクエストパラメータで受け取ったusernameとpasswordの組み合わせが
  // ユーザ一覧に含まれるか判定する
  const validUser = validUserList.find((validUser) => {
      var hashedPassword = '';
      sha256(validUser.password)
      .then(hp => {
        hashedPassword = hp;
      })
      .catch(error => {
        console.log(error)
      });
      return validUser.username === username && hashedPassword === password
    });

  if (validUser) {
    judgeResult =true;
  }

  var output = ContentService.createTextOutput();
  output.setMimeType(ContentService.MimeType.JSON);
  output.setContent(JSON.stringify({ isValidUser: judgeResult }));

  return output;
}

// ユーザ一覧の取得
function getAllUserList() {
  var sheet = SpreadsheetApp.getActive().getSheetByName('list')
  var range = sheet.getDataRange().getValues();

  var header = ['username', 'password'];
  var allUserList = [];

  range.forEach(user => {
    // header行を処理対象外とする
    if (user[0] !== header[0]) {
      var user_info = {
        username: user[0],
        password: user[1]
      }
      allUserList.push(user_info)
    }
  })

  Logger.log(allUserList);
  return allUserList;
}

async function sha256(text){
    const uint8  = new TextEncoder().encode(text)
    const digest = await crypto.subtle.digest('SHA-256', uint8)
    return Array.from(new Uint8Array(digest)).map(v => v.toString(16).padStart(2,'0')).join('')
}
```

メソッドは３つ。
1. **doPost**メソッド
以前までは、doGetメソッドを使っていましたが、vuepress側からpostで送信されてくるので、doPostメソッドを利用します。
（doGetメソッドは削除します。）

このメソッドの流れは、
* リクエストの取得
* ユーザ一覧の取得
* リクエストの情報とユーザ一覧の情報を比較し、ログイン可能か判定
  - リクエストで送られてくるパスワードは、ハッシュ化されているので、ユーザ一覧のパスワードもハッシュ化して比較します
* 判定結果をJSON形式で返却
という感じです。

2. **getAllUserList**メソッド
これは、前回までと同じなので、省略します。

3. **sha256**メソッド
これも、vuepress側のメソッドと同じなので省略します。

これで、gas側の修正も完了です。

それでは、試してみます。

まずは、usernameとpasswordを未入力の状態で、**OK**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/k8z7m859fuj56rsnq1d18m2pep9c)

usernameとpasswordの両方を入力する旨のメッセージが表示されました！
![](https://storage.googleapis.com/zenn-user-upload/zg30ana453dlyesv612xv2jh2462)

次に、ユーザ一覧にない情報を入力して、**OK**ボタンをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/u941jsqc5t33xjdtdcrcpvpqmwnf)

有効な情報を入力する旨のメッセージが表示されました！
![](https://storage.googleapis.com/zenn-user-upload/4ac9cixyyphdstk3tx7b3sk3z5uy)

最後に、ユーザ一覧にある情報を入力して、**OK**ボタンをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/jpemrr8njkojytg8qmq8fl711o3w)

無事、ログインができました！！
![](https://storage.googleapis.com/zenn-user-upload/g42y9gzlsi9pax5peam4v2ctq0c8)

# まとめ
今回は、思いつきで実装していた簡易ログイン機能をよりちゃんとしたものにしてみました。

これで一旦ログイン周りは完結です。

また修正が必要そうであれば、直していきます。
