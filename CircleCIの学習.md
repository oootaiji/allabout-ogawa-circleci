# CircleCIの学習
## 概要
知識は人に説明できる状態で初めて価値が出る
説明できる状態にするためアウトプットする

## CircleCIとは
### CircleCIとは
一言でいうと、Saas型のCI/CDサービス
CI/CDサービスのやることは主に以下の３つである

- ビルド
- テスト
- デプロイ

### CIとは
Continuous Integrationの略
問題ないかを確認するテストの部分で、テストを自動化する機能

### CDとは
Continuous Deliveryの略
ビルドとデプロイの部分で、ビルドしたものを本番環境へ配信するまでを自動化する機能

### CI/CDとは
CIとCDをまとめてやってくれる。アプリをビルドして問題ないかを検証して、本番環境へデプロイするまでのやってくれる

### CircleCIの特徴 (他のCICDとの違いも含めて)
- Saasであること (マネージド・サービス)
- CI/CDサービスである (CDのみのサービスも有る)
- gitのみ対応 (サブバージョンなどは非対応)
- yamlで設定を書く (シンプル)
- bashが利用されている
    - **は、*に展開される。展開されないようにするには、shopt -s globstarを設定
    - set -x は、デバッグ出力開始。詳細なログが流れる

## CircleCIの主な仕様
### 設定ファイルの用語

- 基本
    - executors
        - ジョブの実行環境を定義
        - 任意項目
    - commands
        - ジョブで使うコマンドセットを定義
        - 任意項目
    - jobs
        - 実行するジョブを定義
        - 必須項目
        - 必要とあれば、excecutorを指定
        - 必要とあれば、commandsを指定
    - workflows
        - ジョブの実行フローを定義
        - 必須項目
        - jobsを指定
- オプション項目
    - parallelism
        - 複数のコンテナでジョブの並列実行する機能
- プリ変数
    - checkout
        - workspaceにリポジトリをチェックアウトすること。始まりのジョブ
    - persist_to_workspace
        - ワークスペースを保存し、次のジョブへ共有する機能
    - attach_workspace
        - 保存されたワークスペースをアタッチ
    - save_cache
        - keyでキャッシュを保存
    - restore_cache
        - keyをもとに保存したキャッシュを復元
    - setup_remote_docker
        - セキュリティを高めるために、リモート環境でdockerコマンドを実行するようにする機能
- 実行タイミング
    - CIはリポジトリでプルリク出された時に実行される
    - CDはリポジトリでマージされた時に実行される
- ストレージの違い
    - アーティファクト: ワークフロー終了後の成果物データ (全体)
    - ワークスペース: 同じワークフローで共有されるデータ (ジョブをまたいだ共有)
    - キャッシュ: 同じジョブで共有されるデータ (ワークフローをまたいだ共有)
    - 図：https://circleci.com/docs/assets/img/docs/jobs-overview.png
- 並列実行と当時実行の違い
    - 並列実行とは、ジョブを並列実行させる数のこと
    - 同時実行とは、circleciのビルドの同時実行数制限のこと。プランによって同時実行数が制限されている。メンバー間も実行数は共有される。並列実行とは違う


## その他の学習
### apacheで不足していた知識

- conf-availableとconf-enabledの違い
    - https://qiita.com/naotwu/items/db8d07c5d747c530eb66
    - 〇〇-enabled から〇〇-available へのシンボリックリンクがある
        - a2ensite でsites-enabledからのシンボリックリンクを作成させる
        - a2enmod でmods-enabledからのシンボリックリンクが作成される
        - a3enconf でconf-enabledからのシンボリックリンクが作成される

### dockerで不足していた知識
- docker-composeからdockerfileへ変数を渡す
    - https://qiita.com/Targityen/items/2717511ca9f12c1c667f
- EXPOSE 80
    - コンテナのポートを公開
- pecl install と docker-php-ext-install 違い
    - docker-php-ext-install はphp標準の拡張モジュールをインストール
    - pecl install は標準にない拡張モジュールインストール
    - docker-php-ext-enable によってpeclでインストールしたモジュールを有効化する
- apt install と apt-get install の違い
    - apt の方が基本だが、CLIとしては、apt-get が推奨されている


### kubernetesで不足していた知識

- RollingUpdate
    - Blue/Green Deploymentとは少し違う
    - ダウンタイム無しでリリース
    - k8sにBlue/Greenは用意されていないので、基本的にこれを使う
    - maxSurge が宣言したポッド数を超えて作れるpod数
    - maxUnavailable が停止状態になれる最大のpod数
- デプロイ方式の種類
    - Recreate
        - ダウンタイムあり
        - 高速でシンプルな切り替えデプロイ
    - RollingUpdate
        - ダウンタイムなし
        - すぐに切り替わる
    - Blue/Green Deployment
        - ダウンタイムなし
        - 動作確認後に切り替える時間がある
    - Canary Release
        - 先行公開してリリース
- revisionHistoryLimit
    - ロールバック可能なReplicaSetの最大保有数。超えるとガベージ対象
- Kubernetesの設定の特徴
    - リソース要求とリソース制限の２軸で設定できる 
- Pod
    - 最小のデプロイ単位
    - 最終的なエンドポイント
- ReplicaSet
    - クラスタ全体のPodマネージャー
    - ReplicaSetで管理されたPodは、ノード障害時に自動で別のノードに割り当てられる
    - ReplicaSetのPod管理の仕組みは調整ループ
        - 望ましい状態: 
        - 現在の状態: 
    - RepolicaSetとPodは疎結合になっている
    - スケールすることもできる
- Deployment
    - ReplicaSetを管理するのがDeployment
- ロールアウトとは
    - デプロイと同義
- NodePortとClusterIP
    - Cluster IPはクラスター内部用のIP
    - NodePortは外部公開するためのポート
- NEGとは
    - Network Endpoint Groupのこと
    - Network EndpointとはPodのことを指す
- PodDisruptionBudget
    - 計画的にPodを停止させても問題ないようにする機能
    - kubectl drainさせてPodを待機させる

## 参考文献

記事

- [いまさらだけどCircleCIに入門したので分かりやすくまとめてみた](https://qiita.com/gold-kou/items/4c7e62434af455e977c2)
- [PodとReplicaSetとDeploymentの関連]https://blog.a-know.me/entry/2018/08/14/185324
- [NEGについて](https://christina04.hatenablog.com/entry/network-endpoint-group)
- [PodDisruptionBudgetについて](https://qiita.com/tkusumi/items/946b0f31931d21a78058)
- [Serviceタイプについて](https://zenn.dev/suiudou/articles/7dad08c5b64283)

ドキュメント

- [Dockerドキュメント](https://docs.docker.jp/index.html)
- [CircleCIドキュメント](https://circleci.com/docs/ja)
- [PodDisruptionBudgetのドキュメント](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)
- [Ingressのドキュメント](https://kubernetes.io/ja/docs/concepts/services-networking/ingress/)
- [Serviceのドキュメント](https://kubernetes.io/ja/docs/concepts/services-networking/service/)
