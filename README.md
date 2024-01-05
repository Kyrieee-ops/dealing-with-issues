# エージェントグロー2024/01/04 課題対応
## GitPush時のデプロイについての深堀およびハンズオン
AWS CodeCommitに関して、CodeCommit上にリポジトリを作成してソースコードを管理する点については理解しているが、
GitHub上に管理を行い、GitHubのソースコードの変更を検知してEC2インスタンスへデプロイを実行するCI/CDの理解が浅いため
上記内容を課題対応として取り組む

## 課題対応の目的
自身のスキルアップのため、および未知の技術の理解のために本課題を対応する

## アウトプット媒体
完成に持っていけるかどうかは微妙だが、会社ブログへのアウトプットを目標にする

## 課題対応における環境
- OS: Windows (Linux -> EC2インスタンス)
- AWS

## スケジュール
- 9:00-12:00 参考サイトの記事を読み込み
- 12:00-13:00 お昼休憩
- 13:00-18:00 会社ブログへメモを残すつつアウトプット


## 発生した課題
[【AWS】git pushでEC2にも自動デプロイ(CodeDeploy / CodePipeline)](https://qiita.com/nasuB7373/items/081f5974e31419a1a844)
上記記事を参考にIAMロールを作成しようと思ったが、本記事ではEC2インスタンスに対して`AWS CodeDeployRole`をアタッチしているが、そもそもこのポリシーは`CodeDeploy`に対してアタッチすべきではないか？と思い、AssumeRoleは`codedeploy.amazonaws.com`に対して付与することとした。
実際にDeploy工程で動かしたときにエラーが出るかどうかなどは検証する。

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "Service": "codedeploy.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

## 参考サイト
- [【AWS】git pushでEC2にも自動デプロイ(CodeDeploy / CodePipeline)](https://qiita.com/nasuB7373/items/081f5974e31419a1a844)



# エージェントグロー2024/01/05 課題対応
## GitPush時のデプロイについての深堀およびハンズオン
AWS CodeCommitに関して、CodeCommit上にリポジトリを作成してソースコードを管理する点については理解しているが、
GitHub上に管理を行い、GitHubのソースコードの変更を検知してEC2インスタンスへデプロイを実行するCI/CDの理解が浅いため
上記内容を課題対応として取り組む

## 課題対応の目的
1/4昨日に引き続き、自身のスキルアップのため、および未知の技術の理解のために本課題を対応する

## アウトプット媒体
完成に持っていけるかどうかは微妙だが、会社ブログへのアウトプットを目標にする

## 課題対応における環境
- OS: Windows (Linux -> EC2インスタンス)
- AWS

## スケジュール
- 9:00-12:00 会社ブログへメモを残しつつアウトプット
- 12:00-13:00 お昼休憩
- 13:00-18:00 会社ブログへメモを残すつつアウトプット


## 発生した課題
appspec.ymlファイルのhooksで定義しているBeforeInstallとApplicationStopに関する処理順序についてよく理解していなかったが、ロードバランサーなしとロードバランサーありでデプロイ順序などが変わるなどがあることを知った。

```
version: 0.0
os: linux
files:
  - source: /index.html
    destination: /var/www/html/
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies
      timeout: 300
      runas: root
    - location: scripts/start_server
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server
      timeout: 300
      runas: root
```




## 参考サイト
- [【AWS】git pushでEC2にも自動デプロイ(CodeDeploy / CodePipeline)](https://qiita.com/nasuB7373/items/081f5974e31419a1a844)
- [AppSpec 「フック」セクション](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html)