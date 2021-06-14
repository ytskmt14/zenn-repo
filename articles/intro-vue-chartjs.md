---
title: "ポートフォリオのスキルセットをグラフで可視化したお話" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Vue.js", "vue-chartjs"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

今回は、最近作った[ポートフォリオ](https://ytskmt14.github.io/portfolio/)を改修したお話です。

ゴールとしては、スキルセットの表示をダニング・クルーガー効果のグラフ上にマッピングすること。

[この記事](https://hrzine.jp/article/detail/2077)の図がズバリです。


# 今回の内容
今回改修するのはこの部分。

画像

ここをグラフ形式での表示ができるようにしていきます。

## 必要なライブラリのインストール
まずはグラフを扱う上で必要なライブラリをインストールしていきます。

今回使用するのは、 **vue-chartjs** と **chart.js**です。

```sh
$ npm install vue-chartjs
$ npm install chart.js@2.9.4
```

ここで、`$ npm install chart.js`としてしまうと、chart.jsの3系がインストールされてしまい、うまく動きませんでした。

vue-chartjsはchart.jsの3系に対応していないようです。
（めっちゃハマったポイントです。）

これで、グラフを作成する準備ができました！

## ダニング・クルーガー効果のグラフを作る
まずは、ベースとなるグラフを描画するためのコンポーネントを作っていきます。

ファイル名はなんでも良いですが、今回は **MySkillOnCorrelationDiagram.vue**としています。

```js: MySkillOnCorrelationDiagram.vue
<script>
import { Line } from "vue-chartjs";

export default {
  extends: Line,
  name: "LineChart",
  data: () => ({
    chartdata: {
      labels: ["☆☆☆☆☆", "★☆☆☆☆", "★★☆☆☆", "★★★☆☆", "★★★★☆", "★★★★★", "", ""],
      datasets: [
        {
          label: "自己評価と能力（ダニング・クルーガー効果）",
          radius: 0.1,
          data: [0, 100, 10, 25, 40, 70, 85],
          borderColor: "#CFD8DC",
          fill: false,
          type: "line",
          lineTension: 0.4,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        xAxes: [
          {
            scaleLabel: {
              display: true,
              labelString: "能力",
            },
          },
        ],
        yAxes: [
          {
            ticks: {
              beginAtZero: true,
              stepSize: 10,
              suggestedMax: 100,
            },
            scaleLabel: {
              display: true,
              labelString: "自己評価",
            },
          },
        ],
      },
    },
  }),
  mounted() {
    this.renderChart(this.chartdata, this.options);
  },
};
</script>
```

縦軸に自己評価、横軸に能力としています。

## 自分のスキルをマッピング
さて、ベースとなるグラフができたので、次はスキルをマッピングしていきます。

かなり強引な感じがしますが、このようにしました。

（きっともっといい感じの実装方法があるので、ここはリファクタリングしたい。）

```diff js:MySkillOnCorrelationDiagram.vue
 <script>
 import { Line } from "vue-chartjs";

 export default {
   extends: Line,
   name: "LineChart",
   data: () => ({
     chartdata: {
       labels: ["☆☆☆☆☆", "★☆☆☆☆", "★★☆☆☆", "★★★☆☆", "★★★★☆", "★★★★★", "", ""],
       datasets: [
         {
           label: "自己評価と能力（ダニング・クルーガー効果）",
           radius: 0.1,
           data: [0, 100, 10, 25, 40, 70, 85],
           borderColor: "#CFD8DC",
           fill: false,
           type: "line",
           lineTension: 0.4,
-        }
+        },
+        {
+          label: "Language",
+          color: "red",
+          pointStyle: "rectRounded",
+          pointRadius: 4,
+          pointBorderColor: "red",
+          pointBackgroundColor: "red",
+          borderColor: "red",
+          data: [null, null, 10, 25, 40, null, null],
+          fill: false,
+          type: "line",
+          lineTension: 0.4,
+          showLine: false,
+        },
+        {
+          label: "Framework",
+          color: "blue",
+          pointStyle: "rectRounded",
+          pointRadius: 6,
+          pointBorderColor: "blue",
+          pointBackgroundColor: "blue",
+          borderColor: "blue",
+          data: [null, null, null, 25, 40, null, null],
+          fill: false,
+          type: "line",
+          lineTension: 0.4,
+          showLine: false,
+        },
+        {
+          label: "Others",
+          color: "green",
+          pointStyle: "rectRounded",
+          pointRadius: 8,
+          pointBorderColor: "green",
+          pointBackgroundColor: "green",
+          borderColor: "green",
+          data: [null, null, 10, null, 40, null, null],
+          fill: false,
+          type: "line",
+          lineTension: 0.4,
+          showLine: false,
+        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
+      tooltips: {
+        callbacks: {
+          title: function (tooltipItem) {
+            switch(tooltipItem[0].label) {
+              case "★☆☆☆☆":
+                return "完全に理解した";
+              case "★★☆☆☆":
+                return "なんもわからん";
+              case "★★★☆☆":
+                return "できるかも";
+              case "★★★★☆":
+                return "できそう";
+              case "★★★★★":
+                return "チョットデキル";
+            }
+          },
+          label: function (tooltipItem, data) {
+            var label = data.datasets[tooltipItem.datasetIndex].label;
+            if (label === "Language" ) {
+              switch (tooltipItem.xLabel) {
+                case "★★☆☆☆":
+                  return "Ruby";
+                case "★★★☆☆":
+                    return "Python, Javascript, HTML, CSS";
+                case "★★★★☆":
+                  return "Java";
+              }
+            } else if (label === "Framework") {
+              switch(tooltipItem.xLabel) {
+                case "★★★☆☆":
+                    return "Django, Ruby on Rails";
+                case "★★★★☆":
+                  return "Vue.js, Vuetify";
+              }
+            } else if (label === "Others") {
+              switch(tooltipItem.xLabel) {
+                case "★★☆☆☆":
+                    return "Docker";
+                case "★★★★☆":
+                  return "Git, Github";
+              }
+            }
+          },
+        },
+      },
       scales: {
         xAxes: [
           {
             scaleLabel: {
               display: true,
               labelString: "能力",
             },
           },
         ],
         yAxes: [
           {
             ticks: {
               beginAtZero: true,
               stepSize: 10,
               suggestedMax: 100,
             },
             scaleLabel: {
               display: true,
               labelString: "自己評価",
             },
           },
         ],
       },
     },
   }),
   mounted() {
     this.renderChart(this.chartdata, this.options);
   },
 };
 </script>
```

これにより、こんな感じの表示が可能になりました！

画像

# まとめ
今回は、自分のスキルセットをダニング・クルーガー効果のグラフにマッピングしてみました！

正直機能としては無駄機能な感じが否めません。。。

でも、グラフの書き方を完全に理解した（笑）ので、満足です。

私のポートフォリオは[こちら](https://ytskmt14.github.io/portfolio/)。

ソースもGithubにあげているので、参考までに。

https://github.com/ytskmt14

