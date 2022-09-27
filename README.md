# allabout-ogawa-circleci
## 概要
circleciを利用してGKEへデプロイする方法を学習するためのアプリ
circleciとkubernetes(GKE)は実際に業務でつ分かれているため、理解を深めることを目的とする
そして、Github Actionsへの移行のための知識へつなげる

目的はcircleciの自動デプロイの理解のため、アプリはHello world出力するだけにとどめるが、
以前と違って、なるべく実務に近い形のLaravelでHello worldを出力させる


## 成果物

```
Github (README.md, CircleCiの学習.md)
https://github.com/oootaiji/allabout-ogawa-circleci

アプリ (お金かかるため、現在停止中)
https://circleci.ogawa.allabout.oootaiji.com
```

## 目的・要件

- 実践的学習
    - 商用・業務で使うことを意識する
- devopsの学習
    - 本番環境はdockerコンテナが動いているが、ローカル開発環境もコンテナで動くようにする
    - devopsを意識して、開発環境と本番環境の連携を容易にする
- CircleCIの学習が主軸

### インフラ
- クラウド
    - GKE (Google Kubernetes Engine)
    - ロードバランサ (GKEのingress)
    - DNS (公開ドメイン)
    - SSL (証明書発行)
    - やらない
        - VPC (ネットワーク設計)
        - HPA (オートスケール)
- リポジトリ管理
    - Github
- CI/CD
    - CircleCI (orb使わない)

### アプリ

- 最新Laravel (php8)
- hello worldだけ出力


## 準備
### 必須環境
- OS
    - macOS
- ツール
    - docker
    - docker-compose
    - kubectl
    - gcloud

### 事前設定

以下の項目を事前に設定しておく必要がある
(デプロイ自動化はやることが多い)

1. GKEの準備
2. クラスターの作成
3. レジストリの作成
4. 公開用の固定IPの作成
5. サービスアカウント(権限)JSONの作成
6. CircleCIに環境変数を設定

### 1. GKEの準備
- GCPの Kubernetes Engine APIを有効にしておく

    ```
    1. 課金を有効
    2. Artifact Registry and Google Kubernetes Engine API を有効
    ```

- ローカルでgcloudにログインしておく

    ```
    gcloud auth login
    ```

### 2. クラスターの作成
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

### 3. レジストリの作成

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

### 4. 公開用の固定IPの作成

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

### 5. サービスアカウントJSONの作成

- IAMでサービスアカウントを作成
    - 以下の3つのロールを入れておく
        - Kubernetes Engine (とりあえず管理者でOK)
        - Artifact Registry (とりあえず管理者でOK)
        - ストレージ (とりあえず管理者でOK)

### 6. CircleCIに環境変数を設定

以下の２つにおいて、レポジトリに載せられない変数を登録しておき、
ciecleciのジョブ実行時に渡せるようにしておく

- Laravel本番環境用の.env
- サービスアカウントJSON


## 手順

- ローカル開発環境構築
- circleciのconfig.yaml作成
    - gkeデプロイのmanifestファイル作成
- 準備手順が完了していれば、pushして自動デプロイされることを確認

### 稼働確認

- circelci確認

    ```
    https://app.circleci.com/pipelines/github/oootaiji
    ```

- Web公開確認

    ```
    https://circleci.ogawa.allabout.oootaiji.com/
    ```


## さいごに

- 本当はやりたかったこと
    - コンテナイメージのビルドには時間がかかるので、ビルド済みのイメージをレジストリに用意しておくべき
    - VPCは商用では必須なので、本当はVPCは組むべき
    - CIの中心である自動テストもやりたかった
- 次回やりたいこと
    - Github Actionsで同じものを実装
    - CIとCDを分けることを意識

## 参考文献
- [CircleCI を使ってGKEへ自動デプロイ](https://qiita.com/wqwq/items/46a13019209aeafd2cec)
- [docker pushでunauthorized](https://genzouw.com/entry/2022/02/05/080015/2918/)
- [kubectl set imageについて(ローリングアップデート)](https://cloud.google.com/kubernetes-engine/docs/how-to/updating-apps?hl=ja)
- [envをbase64にしてcircleciのyamlで使う](https://support.circleci.com/hc/ja/articles/360003540393-ファイルをBase64使い-環境変数として挿入する方法)
- [docker install](https://docs.docker.jp/linux/step_one.html)
