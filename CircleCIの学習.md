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

## Github Actionsについて

- GithubのCDツール
- Microsoftのご加護


## その他

### apache

apacheで不足していた知識

- conf-availableとconf-enabledの違い
    - https://qiita.com/naotwu/items/db8d07c5d747c530eb66
    - 〇〇-enabled から〇〇-available へのシンボリックリンクがある
        - a2ensite でsites-enabledからのシンボリックリンクを作成させる
        - a2enmod でmods-enabledからのシンボリックリンクが作成される
        - a3enconf でconf-enabledからのシンボリックリンクが作成される

### docker

dockerで不足していた知識

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


## 参考文献
- [いまさらだけどCircleCIに入門したので分かりやすくまとめてみた](https://qiita.com/gold-kou/items/4c7e62434af455e977c2)