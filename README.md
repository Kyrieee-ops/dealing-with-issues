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
- 13:00-18:00 会社ブログへメモを残すつつアウトプット, しばらくエラーにハマり原因を確認


## 発生した課題
CodeDeploy時以下のエラーが発生
```
The overall deployment failed because too many individual instances failed deployment, too few healthy instances are available for deployment, or some instances in your deployment group are experiencing problems.
```
さらにイベントログを確認してみると以下エラー
```
CodeDeploy agent was not able to receive the lifecycle event. Check the CodeDeploy agent logs on your host and make sure the agent is running and can connect to the CodeDeploy server.
```

原因の確認
CodeDeploy Agentのエラーログを確認
grepでエラー内容を確認
grep 'ERROR' /var/log/aws/codedeploy-agent/codedeploy-agent.log
```
2024-01-05T02:56:48 ERROR [codedeploy-agent(2678)]: InstanceAgent::Plugins::CodeDeployPlugin::CommandPoller: Error polling for host commands: Seahorse::Client::NetworkingError - unable to connect to `codedeploy-commands.ap-northeast-1.amazonaws.com`; SocketError: getaddrinfo: Name or service not known - /usr/share/ruby/net/http.rb:878:in `initialize' 

2024-01-05T02:56:48 ERROR [codedeploy-agent(2678)]: InstanceAgent::Plugins::CodeDeployPlugin::CommandPoller: Network error: #<Seahorse::Client::NetworkingError: unable to connect to `codedeploy-commands.ap-northeast-1.amazonaws.com`; SocketError: getaddrinfo: Name or service not known>

2024-01-05T05:06:00 ERROR [codedeploy-agent(2678)]: booting child: error during start or run: Errno::ENETUNREACH - Network is unreachable - connect(2) - /usr/share/ruby/net/http.rb:878:in `initialize'

2024-01-05T05:06:00 ERROR [codedeploy-agent(2678)]: booting child: error during start or run: SystemExit - exit - /opt/codedeploy-agent/lib/instance_agent/runner/child.rb:98:in 
`exit'
```


## 参考サイト
- [【AWS】git pushでEC2にも自動デプロイ(CodeDeploy / CodePipeline)](https://qiita.com/nasuB7373/items/081f5974e31419a1a844)
- [AppSpec 「フック」セクション](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html)