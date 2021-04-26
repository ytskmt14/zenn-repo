---
title: "Zennの記事の執筆をローカル環境へ移行しました"
emoji: "👶"
type: "tech"
topics: ["zenn", "github"]
published: true
---

# はじめに
ご覧いただきありがとうございます。mocです。

今回は、zennの記事をローカル環境で執筆できるようにすべく、githubとの連携をやってみたので、やったことをまとめていきます。

# 今回の内容
公式から設定手順とかはわかりやすく公開されてるので、基本的にはこれの通りにやれば、問題ありません。
（つまづくところなかったので、公式神だなとしみじみ感じました。）

具体的に参考にしたのはこの３記事です。

https://zenn.dev/zenn/articles/connect-to-github

https://zenn.dev/zenn/articles/install-zenn-cli

https://zenn.dev/zenn/articles/zenn-cli-guide

これだけでも十分わかりやすいから、この記事必要ないのでは？とか思ったりもしましたが、アウトプットの特訓と思って、できるだけわかりやすく書いていければと思います。

少しだけ使いやすいようにアレンジも加えてみたので、参考になれば嬉しいです。

それでは、はじめていきます。

## Githubリポジトリを作成
まずは、zennと連携するためのリポジトリを作成します。

1. githubのリポジトリのページを開いて、**New**をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/gqvq7qrjwr5le6xbfr5hk5zcrv8q)

2. リポジトリの名前を入力し、**Create repository**をクリックします。（今回は、**zenn-repo**としました。）
![](https://storage.googleapis.com/zenn-user-upload/ildaizacoktvdh7t39r9ps1gozyo)

3. リポジトリの作成完了！
リポジトリのURLは後で使うので、メモしておきましょう。
ここでは、**git@github.com:ytskmt14/zenn-repo.git**をメモしておきます。
sshの設定をしていない方は、HTTPSを選択して、表示されるURLをメモしましょう。

![](https://storage.googleapis.com/zenn-user-upload/rtf5mkii2k9tmr747w7pdcbm40jq)

## Zennとgithubを連携する
次に、Zennとgithubの連携の設定をしていきます。

1. Zennを開いて、ヘッダーの自分のアイコンをクリックして、**GitHubからのデプロイ**を選択します。
![](https://storage.googleapis.com/zenn-user-upload/xenq2ye27lokqjauodnskpccxchj)

2. **リポジトリを連携する**ボタンをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/10q20til7ljgt4gqgmomvvkyni81)

3. 連携設定の画面が開くので、**Only select repositories**を選択し、プルダウンから先ほど作成した、githubのリポジトリを選択して、**Install & Authorize**をクリックします。（今回の場合は、**zenn-repo**を選択します。）
![](https://storage.googleapis.com/zenn-user-upload/adx2to5fxpj2vffkbpy2f7ipjbe2)

4. これで連携が完了しました！
![](https://storage.googleapis.com/zenn-user-upload/afkjne8xt8uyqvp8l9rrleej962q)

## ローカル環境構築
ローカル環境で、記事を執筆するための準備をしていきます。
私は、エディタにVScodeを利用しています。


1. Zennのリポジトリをclone
リポジトリを作成したときにメモしておいたURLをここで使います。
```sh
$ git clone git@github.com:ytskmt14/zenn-repo.git
Cloning into 'zenn-repo'...
warning: You appear to have cloned an empty repository.
```

2. npmの初期化

```sh
$ npm init --yes
Wrote to /Users/moc/workspace/zenn-repo/package.json:

{
  "name": "zenn-repo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/ytskmt14/zenn-repo.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/ytskmt14/zenn-repo/issues"
  },
  "homepage": "https://github.com/ytskmt14/zenn-repo#readme"
}
``` 

3. Zenn-cliの導入

```sh
$ npm install zenn-cli
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated fsevents@2.1.3: "Please update to latest v2.3 or v2.2"

added 860 packages, and audited 861 packages in 24s

44 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

4. zenn用のセットアップ

```sh
$ npx zenn init
Generating .gitignore skipped.

  🎉Done!
  早速コンテンツを作成しましょう

  👇新しい記事を作成する
  $ zenn new:article

  👇新しい本を作成する
  $ zenn new:book

  👇表示をプレビューする
  $ zenn preview
```
これでローカル環境構築は完了です！

5. ディレクトリ構成
現時点でのディレクトリ構成はこんな感じになっています。

![](https://storage.googleapis.com/zenn-user-upload/mv7zrb0fz6ai02w2e8x6sxqt5xkn)

## 既存の記事を移行する
私は、この作業をする時点で、2つ記事を書いていたので、それを移行していきます。

1. 既存記事のファイル名と内容を取得する
マイページのArticlesを開くと、記事の下にグレーの文字で、**~.md**とファイル名が表示されているのでそれをメモします。
私の場合、**88c7535ecbee13.md**と**0639685e1de33f.md**の２つです。

また、ファイルの内容は、記事のリストの右側の編集アイコンの右隣にあるアイコンをクリックして、**内容をmarkdownで出力**をクリックすることで取得できます。
別タブで開かれるので、開いたままにしておきましょう。
![](https://storage.googleapis.com/zenn-user-upload/quri87t2e619wpaaj9h5btpfqost)

2. 取得したファイルをローカル環境に移行する
今回は、記事（article）の移行なので、articles配下にファイルを作成していきます。

```sh
$ pwd
/Users/moc/workspace/zenn-repo
# 先ほど確認した記事のファイル名と同じファイル名のファイルを作成する
$ touch articles/88c7535ecbee13.md articles/0639685e1de33f.md
```

あとは、それぞれのファイルに別タブで開いておいたファイルの内容をコピペするだけです。

## プレビューで確認する
zenn-cliを使うと、実際の表示をプレビューで確認しながら執筆できます。
ホットリロードも対応しているので、保存したらすぐに反映されるのでとっても便利です。

プレビューで確認するためには下記のコマンドを利用します。
```sh
$ npx zenn preview
👀 Preview on http://localhost:8000
```

そしてブラウザで http://localhost:8000 を開いてみると、、、

![](https://storage.googleapis.com/zenn-user-upload/gm17i8rgkuv62mvsntqrnx58lkw4)

うまく表示されました！

## 記事ファイルの作成
zenn-cliでは、プレビュー以外にも、記事ファイルの作成用のコマンドも用意されています。

```sh
$ npx zenn new:article
```

便利ですね！

## ここで少しアレンジ
今のままでも十分に便利なのですが、めんどくさがりな私はこう思いました。

1. プレビューするのにコマンド叩くのめんどくさい
2. 記事を書き始めるのもコマンド叩くのめんどくさい

これを解消していきます。

### プレビューするのにコマンド叩くのめんどくさい
プレビューするのに必要なコマンドは、
```sh
$ npx zenn preview
```
でしたね。

これすらめんどくさいので、package.jsonにスクリプトを追加します。

```diff js:package.json
 {
   "name": "zenn-repo",
   "version": "1.0.0",
   "description": "",
   "main": "index.js",
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1",
+    "preview": "npx zenn preview",
   },
   "repository": {
     "type": "git",
     "url": "git+https://github.com/ytskmt14/zenn-repo.git"
   },
   "keywords": [],
   "author": "",
   "license": "ISC",
   "bugs": {
     "url": "https://github.com/ytskmt14/zenn-repo/issues"
   },
   "homepage": "https://github.com/ytskmt14/zenn-repo#readme",
   "dependencies": {
     "zenn-cli": "^0.1.79"
   }
 }
```

こうすることで、VScodeのnpmスクリプトから実行できるようになります。
previewの一番右の実行アイコンを押すだけ！
めっちゃ楽、最高です。

![](https://storage.googleapis.com/zenn-user-upload/pndhahz6e9bdvw77sl4rvczskpwq)

package.jsonのscriptsに記載したので、このコマンドでももちろん起動可能です。

```sh
$ npm run preview
```

### 記事を書き始めるのもコマンド叩くのめんどくさい
これもプレビューのときと同じで、package.jsonにスクリプトを追加します。
それに加えて、自分なりのテンプレートを準備しました。

```sh
$ pwd
/Users/moc/workspace/zenn-repo
# テンプレート用のファイルを作成
$ mkdir -p articles/template && touch articles/template/template.md
```

執筆時点では、テンプレートファイルはこんな感じにしています。
今後、必要に応じて更新していく予定です。
```markdown: template.md
---
title: "My Article Title" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["my-topics"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

# 今回の内容

# まとめ
```

あとは、このテンプレートのファイルをgitの管理対象外とするために、**.gitignore**を下記のように編集します。

```markup: .gitignore
node_modules
articles/template
```

最後に、package.jsonを下記のように編集します。

```diff js:package.json
 {
   "name": "zenn-repo",
   "version": "1.0.0",
   "description": "",
   "main": "index.js",
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1",
     "preview": "npx zenn preview",
+    "create": "cp ./articles/template/template.md ./articles/xxx.md"
   },
   "repository": {
     "type": "git",
     "url": "git+https://github.com/ytskmt14/zenn-repo.git"
   },
   "keywords": [],
   "author": "",
   "license": "ISC",
   "bugs": {
     "url": "https://github.com/ytskmt14/zenn-repo/issues"
   },
   "homepage": "https://github.com/ytskmt14/zenn-repo#readme",
   "dependencies": {
     "zenn-cli": "^0.1.79"
   }
 }
```

これも同様に、VScodeのnpmスクリプトから実行可能になりました。
![](https://storage.googleapis.com/zenn-user-upload/2l1a830ggpqj75h6ewdchhvhjl56)

# まとめ
