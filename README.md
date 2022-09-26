# allabout-ogawa-circleci
## 概要
circleciを利用してGKEへデプロイする方法を学習するためのアプリ
circleciとkubernetes(GKE)は実際に業務でつ分かれているため、理解を深めることを目的とする
そして、Github Actionsへの移行のための知識へつなげる

目的はcircleciの自動デプロイの理解のため、アプリはHello world出力するだけにとどめるが、
以前と違って、なるべく実務に近い形のLaravelでHello worldを出力させる


## github

https://github.com/oootaiji/allabout-ogawa-circleci

## 条件

dockerでローカル開発環境をつくり、それをcircleciでデプロイする

### インフラ
- GKE
- Github
- CircleCI
    - orb使わない
- セキュリティはある程度
    - SSLあり
    - VPCなし
- 可用性はある程度
    - ロードバランサあり
    - 自動スケールなし

### アプリ
- コンテナ
    - php:8.0-apache-buster
        - + nodejs
        - + composer
    - redis
- フレームワーク
    - 最新Laravel (php8)
- 機能
    - hello worldだけ出力

## 準備
### ローカル開発環境
- OS
    - macOS
- ツール
    - docker
    - docker-compose
    - kubectl
    - gcloud

### GCP環境
- GCPの Kubernetes Engine APIを有効にしておく

    ```
    1. 課金を有効
    2. Artifact Registry and Google Kubernetes Engine API を有効
    ```

- ローカルでgcloudにログインしておく

    ```
    gcloud auth login
    ```

- 固定IP作成

    ```
    gcloud compute addresses create <固定IP名> --global
    ```

- 固定IP確認

    ```
    gcloud compute addresses describe <固定IP名> --global
    ```

- DNSの設定

    ```
    独自ドメインは持っているものを使う
    上記のIPをAレコードで設定しておく
    ```


## 手順
### 権限の設定

- IAMでサービスアカウントを作成

### クラスターの設定
- kubernetesクラスタ作成

    ```
    cluster_name=allabout-ogawa-circleci
    gcloud container clusters create $cluster_name --machine-type=e2-micro --num-nodes=1 --region=us-west1
    ```

- クラスタの認証情報を取得 (このコマンドで、以下で指定したクラスターにデプロイされるようになる)

    ```
    cluster_name=allabout-ogawa-circleci
    gcloud container clusters get-credentials $cluster_name
    ```

- クラスタ作成確認

    ```
    https://console.cloud.google.com/kubernetes/list/overview?project=o-taiji
    ```

### GCRの設定

- gcrの認証 (us-east1)
    ```
    gcloud auth configure-docker us-east1-docker.pkg.dev
    ```

- gcr作成

    ```
    repo_name=allabout-ogawa-circleci
    gcloud artifacts repositories create $repo_name --repository-format=docker --location=us-east1
    ```

- gcr確認 レジストリ確認

    ```
    https://console.cloud.google.com/artifacts/browse/o-taiji?project=o-taiji
    ```

### DNSの設定

- 固定IP作成

    ```
    solid_ip_name=allabout-ogawa-circleci-ip
    gcloud compute addresses create $solid_ip_name --global
    ```

- 固定IP確認

    ```
    solid_ip_name=allabout-ogawa-circleci-ip
    gcloud compute addresses describe $solid_ip_name --global
    ```

- DNSの設定

    ```
    circleci.ogawa.allabout.oootaiji.comへ上記のIPをAレコードで追加
    ```

### デプロイ

- circelci確認

    ```
    https://app.circleci.com/pipelines/github/oootaiji
    ```

### 稼働確認


## 参考文献
- [CircleCI を使ってGKEへ自動デプロイ](https://qiita.com/wqwq/items/46a13019209aeafd2cec)
- [docker pushでunauthorized](https://genzouw.com/entry/2022/02/05/080015/2918/)
- [kubectl set imageについて(ローリングアップデート)](https://cloud.google.com/kubernetes-engine/docs/how-to/updating-apps?hl=ja)
- [envをbase64にしてcircleciのyamlで使う](https://support.circleci.com/hc/ja/articles/360003540393-ファイルをBase64使い-環境変数として挿入する方法)
- [docker install](https://docs.docker.jp/linux/step_one.html)
