---
title: "vuepressで簡易ログイン機能付きのドキュメントを作ってみる" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vue", "vuepress", "git"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

# 今回の内容
vuepressで簡易ログイン付きのドキュメントを作っていきます。

## vuepressの環境構築
まずは、コマンドラインでの作業です。

```sh
# 作業ディレクトリを作成する
$ mkdir my-doc && cd my-doc
# とりあえず全部Enterでok
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help init` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (my-doc) 
version: (1.0.0) 
description: 
entry point: (index.js) 
test command: 
git repository: 
keywords: 
author: 
license: (ISC) 
About to write to /Users/moc/workspace/lang-study/vue/my-doc/package.json:

{
  "name": "my-doc",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}


Is this OK? (yes)
# vuepressをローカルインストールする
$ npm install -D vuepress
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated mkdirp@0.3.0: Legacy versions of mkdirp are no longer supported. Please update to mkdirp 1.x. (Note that the API surface has changed to use Promises in 1.x.)
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142

added 1244 packages, and audited 1245 packages in 37s

67 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
# 最初のドキュメントを追加する
$ mkdir docs && echo '# Hello VuePress' > docs/README.md
# ディレクトリ構造を確認
$ tree -L 1
.
├── node_modules
├── package-lock.json
└── package.json
```

次に、package.jsonにscriptを追加します。

```diff json:package.json
 {
   "name": "my-doc",
   "version": "1.0.0",
   "description": "",
   "main": "index.js",
   "scripts": {
+    "docs:dev": "vuepress dev docs",
+    "docs:build": "vuepress build docs",
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "author": "",
   "license": "ISC",
   "devDependencies": {
     "vuepress": "^1.8.2"
   }
 }
```

ここまでできたら、ローカル環境を立ち上げてみます。
```sh
$ npm run docs:dev
success [02:03:50] Build 91a868 finished in 86 ms! ( http://localhost:8080/ )
```

![](https://storage.googleapis.com/zenn-user-upload/6dxbe6gm2rqxzknqf9e0ylpxqph7)

できました！

ここで一旦gitに登録していきます。

```sh
# gitのローカルリポジトリの初期化
$ git init
# .gitignoreの追加
# node_modulesとpackage-lock.jsonを管理対象から外す
$ echo "node_modules\npackage-lock.json" > .gitignore 
# stagingに追加
$ git add .
# コミット
$ git commit -m "my first commit"
[master (root-commit) 0d8eb94] my first commit
 3 files changed, 19 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 docs/README.md
 create mode 100644 package.json
```

次に、ディレクトリ構成を整えていきます。

vuepressの公式ページを参考にディレクトリ構成を作っていきましょう。

https://vuepress.vuejs.org/guide/directory-structure.html

```sh
# 必要なディレクトリを作成
$ mkdir docs/.vuepress
$ mkdir -p docs/.vuepress/{login,public,styles}
# 必要なファイルを作成
$ touch docs/.vuepress/{config,enhanceApp}.js
$ touch docs/.vuepress/login/{Login.vue,helper.js}
$ touch docs/.vuepress/styles/index.styl
# ディレクトリ構成を確認する
$ tree -aA -L 4 -I "node_modules|.git" 
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
├── package-lock.json
└── package.json
```

それでは、簡易ログイン機能を追加していきます。

## 簡易ログイン機能の実装
簡易ログイン機能には、**vuepress-login**を参考にしてみます。

https://github.com/TerryZ/vuepress-login

それでは、手順通りに進めていきましょう。

まずは、必要なプラグインである、**v-dialogs**をインストールします。

```sh
$ npm install v-dialogs -D

added 1 package, and audited 1246 packages in 3s

67 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

次に、作成したファイルを編集していきます。

```js: config.js
module.exports = {
  base: '/my-doc/',
  title: 'Vuepress with login',
  description: 'Vuepress with login',
  markdown: {
    lineNumbers: true
  }
}
```

```js: enhanceApp.js
import { checkAuth } from './login/helper'
import Login from './login/Login'

export default ({
  Vue,
  options,
  router,
  siteData,
}) => {
  Vue.mixin({
    mounted() {
      const doCheck = () => {
        if (!checkAuth()) {
          this.$dlg.modal(Login, {
            width: 300,
            height: 350,
            title: 'Employee login',
            singletonKey: 'employee-login',
            maxButton: false,
            closeButton: false,
            callback: data => {
              if (data === true) {

              }
            }
          })
        }
      }

      if (this.$dlg) {
        doCheck()
      } else {
        import('v-dialogs').then(resp => {
          Vue.use(resp.default)
          this.$nextTick(() => {
            doCheck()
          })
        })
      }
    }
  })
}
```

```jsx: Login.vue
<template>
  <div class="login-form">
    <div class="form-header">User Name</div>
    <div>
      <input type="text" class="form-control" v-model="username" />
    </div>
    <div class="form-header">Password</div>
    <div>
      <input type="password" class="form-control" v-model="password" />
    </div>

    <div class="btn-row">
      <button class="btn" @click="login">OK</button>
    </div>
  </div>
</template>

<script>
import { STORAGE_KEY } from "./helper";
export default {
  data() {
    return {
      username: "",
      password: "",
      validUserList: [
        {
          id: 1,
          name: "user1",
          password: "password",
        },
      ],
    };
  },
  methods: {
    login() {
      const user = this.validUserList.find(
        validUser => validUser.name === this.username
      );
      if (user) {
        const data = JSON.stringify({
          name: user.name,
          time: new Date().getTime(),
        });
        window.localStorage.setItem(STORAGE_KEY, data);
        this.$emit("close", true);
      } else {
        this.$dlg.alert("Please complete the content", {
          messageType: "warning",
        });
      }
    },
  },
};
</script>

<style lang="stylus">
.login-form {
  padding: 1rem;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;

  .btn-row {
    margin-top: 1rem;
  }

  .btn {
    padding: 0.6rem 2rem;
    outline: none;
    background-color: #60C084;
    color: white;
    border: 0;
  }

  .form-header {
    color: #666;
    margin-bottom: 0.5rem;
  }

  .form-control {
    padding: 0.6rem;
    border: 2px solid #ddd;
    width: 100%;
    margin-bottom: 0.5rem;
    box-sizing: border-box;
    outline: none;
    transition: border 0.2s ease;

    &:focus {
      border: 2px solid #aaa;
    }
  }
}
</style>
```

```js: helper.js
export const STORAGE_KEY = 'employee-auth'

export function checkAuth() {
  const auth = JSON.parse(localStorage.getItem(STORAGE_KEY))
  return auth && Object.keys(auth).length
}
```

```stylus: index.styl
shadow()
  box-shadow 0 .125rem 1rem rgba(0,0,0,.1) !important

border()
  border 1px solid $borderColor
  padding 5px
  margin-top 1rem
  border-radius 5px

h1
  font-size 2.5rem

h2
  font-size 1.9rem

h3
  font-size 1.5rem

h4
  font-size 1.2rem

.text-center
  text-align center
.nowrap
  white-space nowrap

.img
  height 150px
  width 150px

.img-small
  height $qcode-width
  width $qcode-width
  // object-fit cover

.border
  border()

.shadow
  shadow()

.border-shadow
  border()
  transition box-shadow .3s ease
  &:hover
    shadow()

.danger
  color red

.contains-task-list LI
  list-style-type none

```

```md: README.md
# Hello VuePress
```

**ポイント**

基本的には、**vuepress-login**のgithubのソースの通りにしていますが、**Login.vue**の中のloginメソッドを少しだけいじっています。

公式のページとの差分を示します。

```diff js
 login () {
-    if (this.username && this.password) {
+    const user = this.validUserList.find(
+    validUser => validUser.name === this.username
+    );
+    if (user) {
    const data = JSON.stringify({
-        name: this.username,
+        name: user.name,
        time: new Date().getTime()
    })
    window.localStorage.setItem(STORAGE_KEY, data)
    this.$emit('close', true)
    } else {
    this.$dlg.alert('Please complete the content', {
        messageType: 'warning'
    })
    }
}
```

公式では、サンプルページということもあり、**username**と**password**に何かしらの値が入っていたらログインできます。

そこで今回は、特定のユーザのみがログインできる（**user1**というユーザ名で、**password**というパスワードを入力すればログインできる）ようにしました。

確認してみましょう！

![](https://storage.googleapis.com/zenn-user-upload/dr7m7hlaomqs3bar8hbc8qzl745n)

まずは、ログインできないことを確認します。

User Nameに、**user**をPasswordに、**password**を入力して**OK**をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/kcs4jgecivq6pu16a0v8mx622m05)

ログインできませんでした。

次に、User Nameに、**user1**をPasswordに、**password**を入力して**OK**をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/69y8ynyeit5dqhlpx3c854u715qg)

ログインできました！

# まとめ
今回は、vuepressに簡易ログイン機能を実装してみました。

今後の展望としては、**Login.vue**にベタがきしている`validUserList`をAPI経由で取得できるようにすることです。

方法は模索中なので、いい感じにできたらまた記事にしようかと思います！
