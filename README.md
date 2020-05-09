# はじめに
　Docker 上に開発ツールのコンテナを立ち上げて運用する際、ツールが増える度に新たなポートを発行しなければならず管理が大変になると感じたため、nginx-proxy を利用して各種ツールにドメイン名でアクセスできるようにしました。

## 前提
* Docker Desktop for Windows（または Mac） がインストールされていること。

## 準備
#### 開発ツール用のネットワークを作成します。
「proxy/create-network.sh」を実行します。

#### nginx-proxy のコンテナを立ち上げます。
```bash
$ cd proxy
$ docker-compose up -d
```
\* ポート番号は 80 を利用しています。

# ツール利用手順
## PlantUML（[GitHub](https://github.com/plantuml/plantuml-server)）
　テキストファイルで UML が書けるツールです。私の場合 Docker で PlantUML のサーバーをローカル環境に立ち上げて Visual Studio Code で UML を書いています。

#### コンテナを立ち上げます。
```bash
$ cd plantuml
$ docker-compose up -d
```
#### ドメイン名を hosts ファイルに記載します。
```
127.0.0.1 plantuml.devtool.com
```

#### Visual Studio Code で PlanuUML のプラグインのインストールとサーバーの設定を行います。
以下のサイトを参考に設定してください。
[UMLの爆速プレビュー環境をVisual Studio Code + PlantUML Server on Dockerで簡単に構築する](https://dev.classmethod.jp/tool/plantuml-server-on-docker/)

\* PlantUML を Docker で起動する手順は不要です。

\* Plantuml: Server の値には http://plantuml.devtool.com を設定してください。

## Wekan（[GitHub](https://github.com/wekan/wekan)）
　カンバンツール。個人のタスク管理として使用しています。

#### コンテナを立ち上げます。
```bash
$ cd wekan
$ docker-compose up -d
```

#### ドメイン名を hosts ファイルに記載します。
```
127.0.0.1 wekan.devtool.com
```

## Crowi（[GitHub](https://github.com/crowi/crowi))
　Markdown で記述できる Wiki です。個人的なメモをまとめるために使います。

　**本ツールは nginx で起動していません。**

#### Crowi のリポジトリをクローンします。
```bash
$ cd crowi
$ git clone https://github.com/crowi/crowi -b v1.7.9 --depth 1
```

#### docker-compose, Docker ファイルを差し替えます。
以下の 2 つのファイルでクローンしたフォルダのファイルを上書きします。
* devtool-docker/crowi/docker-compose.development.yml
* devtool-docker/crowi/Dockerfile

＜上書き先（クローンしたフォルダ）＞
* devtool-docker/crowi/crowi/docker-compose.development.yml
* devtool-docker/crowi/crowi/Dockerfile

  **本手順でやっていること**
  * docker-compose を立ち上げる際に npm のインストールに失敗してしまう問題を回避するために、DockerFile に記述されている npm のバージョンを 5.6.0 に変更している。
  * crowi から参照されるコンテナ（MongoDB, PlantUML など）のポート指定を port から expose に変更することで、docker-compose ないでのみアクセスできるようにしている。

#### コンテナを立ち上げます。
```bash
$ cd crowi
$ docker-compose -f docker-compose.development.yml up -d
```
**ブラウザから「localhost:3000/crowi」の URL でアクセスできます。**

# 参考
* [VPSにdockerで複数サイトをホスティングするには？](https://suin.io/561)
* [【実験】1つのWindowsに5人のJenkinsを立ち上げてみた](https://www.taka-output-blog.com/multi-jenkins/)
* [【Docker】複数のコンテナ（サービス）を1サーバーで起動させてサブドメインで80ポートにアクセスする](https://qiita.com/at-946/items/dc8562346904cca2bb3b)
