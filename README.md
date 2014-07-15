circleci-testkitchen
====================

[![circleci status](https://circleci.com/gh/critical-alert/circleci-testkitchen/tree/master.png?circle-token=ba67ac96c81c821616deb057b9acdd5abf2aa95d "circleci status")](https://circleci.com/gh/critical-alert/circleci-testkitchen/tree/master.png?circle-token=ba67ac96c81c821616deb057b9acdd5abf2aa95d)

## circle ciでec2インスタンスにserverspecするやつ

何が起こる?

* githubにpushする
* circleciがそれを検知してビルドを開始
* circleci上のコンテナ内で`bundle install`し`test kitchen`がインストールされる
* `bundle exec kitchen test`が以下をやってくれる
	* ec2内にインスタンスを立ち上げる
	* chefをインストール
	* cook
	* serverspecを実行
	* 成功したらインスタンスをterminate
* circleci上でビルドが`passing`になる
* githubでそれがわかる
* chatツールで通知される(オプション)
* circle.yml参照

## .kitchen.yml (重要)

```
  aws_access_key_id: <%= ENV['AWS_ACCESS_KEY'] %>
  aws_secret_access_key: <%= ENV['AWS_SECRET_ACCESS_KEY'] %>
  aws_ssh_key_id: <%= ENV['AWS_SSH_KEY_ID'] %>
  ssh_key: <%= File.expand_path('~/.ssh/id_circleci') %>
```

* AWSでインスタンスを立ち上げる設定が書かれている
* apiキーなど設定ファイルに書いてコミットできない値はすべて環境変数にしている
* circleci側のEnvironment variablesで下記の設定をしておく
	* `AWS_ACCESS_KEY`
	* `AWS_SECRET_ACCESS_KEY`
	* `AWS_SSH_KEY_ID` :AWS上でEC2を起動する際にsshの鍵を指定する時の鍵の名前
* ssh keyを設定できるのでプライベートキーを登録しておく
	* 登録時、hostnameのところに名前を入れると `~/.ssh/id_hostname` として鍵が配置される
* その他(インスタンスタイプなど)の設定は見ればだいたい分かるとはず
