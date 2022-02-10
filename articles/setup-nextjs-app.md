---
title: "公式ドキュメントから学ぶNext.js〜環境構築〜" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["next.js"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。
普段はバックエンドエンジニアですが、フロント周りもできるようになりたい！ということでNext.jsを触ってみます。

特別な理由があるわけではありません。
最近お話ししたエンジニアの人がReact楽しい！って言ってて、「React webアプリケーション」とかでググったら、目についたのがNext.jsだった、ただそれだけです。

なので、本記事は初学者である私が手を動かしながら、「へぇー」と思ったことを残したり、理解を深めるために言語化してみたりすることを目的としています。


# 今回の内容
[公式ドキュメント](https://nextjs.org/docs/getting-started)をみながら環境構築をしていきます。
見た感じでは、秒で終わりそう、すごい。と思いながらやっていきます。

## プロジェクトの作成
まずは、プロジェクトを作成します。
```sh
$ yarn create next-app --typescript
```

プロジェクトの名前を聞かれるので、任意の名前をつけてあげます。
ここでは、**my-app**とします。
ディレクトリ構成は下記のようになります。
（treeコマンドは別途インストールしています。）
```sh
$ cd my-app
$ tree -a -I "node_modules|.git|.next"
.
├── .eslintrc.json
├── .gitignore
├── README.md
├── next-env.d.ts
├── next.config.js
├── package.json
├── pages
│   ├── _app.tsx
│   ├── about.tsx
│   ├── api
│   │   └── hello.ts
│   └── index.tsx
├── public
│   ├── favicon.ico
│   └── vercel.svg
├── styles
│   ├── Home.module.css
│   └── globals.css
├── tsconfig.json
└── yarn.lock
```

## 画面起動
開発用サーバを起動してみます。

```sh
$ yarn dev
yarn run v1.22.10
$ next dev
ready - started server on 0.0.0.0:3000, url: http://localhost:3000
event - compiled client and server successfully in 146 ms (125 modules)
```

起動できたら、お好みのブラウザで下記URLにアクセスしてみましょう。
http://localhost:3000

↓のような画面が表示されたらOKです。

![](https://storage.googleapis.com/zenn-user-upload/f398241acdc2-20220210.png)

え、もう終わった、すごい。

# まとめ
想像以上に簡単でした。

ディレクトリ構成眺めていろいろ深掘りしようと思いましたが、ちょっと時間かかりそうなので、必要なタイミングで少しずつ理解を深めていければなーと思います。

次は、[こちら](https://nextjs.org/docs/basic-features/pages)を見ていこうかと思います！
