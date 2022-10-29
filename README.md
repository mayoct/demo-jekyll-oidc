# demo-jekyll-oidc
demo-jekyll-oidcは、[CircleCI](https://circleci.com/ja/) を使って [Amazon S3](https://aws.amazon.com/jp/s3/) に静的ウェブコンテンツをデプロイするサンプルプロジェクトです。

静的ウェブコンテンツのビルドには、[Jekyll](http://jekyllrb-ja.github.io)を使用しています。

## CircleCI test-deploy ワークフローの流れ
以下の順でジョブを実行して行きます。

### build ジョブ
Jekyllを使って静的ウェブコンテンツをビルド(Jekyll build)した後で、
生成した静的ウェブコンテンツをテスト(HTMLProofer tests)します。

テストで問題がなければ、生成したコンテンツを(後続のジョブで参照できるよう)
ワークスペースに保存します。

### preview ジョブ
先行するbuildジョブが成功し、かつ、ブランチ名が /.*-preview/ の場合に preview ジョブを実行します。
具体的には、s3://mshk-jekyll-deploy-s3/${CIRCLE_BRANCH} に生成コンテンツ(_site配下)を同期しています。

例えば、
- hello ブランチのビルド結果は、https://mshk-jekyll-preview-s3.s3.ap-northeast-1.amazonaws.com/hello-preview/ で公開
- bye ブランチのビルド結果は、https://mshk-jekyll-preview-s3.s3.ap-northeast-1.amazonaws.com/bye-preview/ で公開

する設定を Amazon S3 側で行っています。

実際の運用では、(main ブランチへのマージ後など)適切なタイミングで preview ジョブで同期した Amazon S3 上のディレクトリは削除する方が良いかと思います。

### deploy ジョブ
先行するbuildジョブが成功し、かつ、ブランチ名が main の場合に deploy ジョブを実行します。
具体的には、s3://mshk-jekyll-deploy-s3/ に生成コンテンツ(_site配下)を同期しています。

https://mshk-jekyll-deploy-s3.s3.ap-northeast-1.amazonaws.com/index.html 

## 設定
本リポジトリを fork した後、.circleci/config.yml 中の記載を自分の環境に合わせて書き換えます。

## 使用Orb
本プロジェクトでは、以下の CircleCI Orb を使用することで、自動化定義を簡潔に記述しています。
- [circleci/aws-cli](https://circleci.com/developer/ja/orbs/orb/circleci/aws-cli)
- [circleci/aws-s3](https://circleci.com/developer/orbs/orb/circleci/aws-s3)
- [circleci/ruby](https://circleci.com/developer/orbs/orb/circleci/ruby)
- [circleci/slack](https://circleci.com/developer/orbs/orb/circleci/slack)

## リンク

## License
[MIT](https://choosealicense.com/licenses/mit/)