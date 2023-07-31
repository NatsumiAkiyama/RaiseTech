#### 1. ssh -i ~/.ssh/RaiseTech04.pem ec2-user@パブリックIPv4アドレス　でEC2にSSH接続
  - `ssh: connect to host xxx.xxx.xxx.xxx port 22: Operation timed out`と表示される  
→元いた場所にて最初の設定で、該当EC2インスタンスのセキュリティグループのインバウンドルールのSSH接続のソース（接続元）を「マイIP」に限定していた。これにより、その作業場所のIPアドレスからでないとSSH接続ができない設定がされた。場所を移動して、移動先のwifiで作業を開始しSSH接続をしようとしたところ、ソース（接続元のIP）が変わってしまったため、接続元の許可がされておらずエラーが起きた。  
→その接続元の許可設定を移動先のIPアドレスに合わせて設定し直した。  
①AWS該当EC2インスタンスのセキュリティグループから、「インバウンドルールを編集」をクリック  
②タイプ:SSH の ソースで再度「マイIP」を選び直す  
③ルールを保存をクリック  
- ターミナルでの表示も自分のPCではなくEC2インスタンスに変わった。`[ec2-user@ip-10-0-7-129 ~]$ `  
- ログアウトするときは`exit`と入力する。

#### 2.パッケージをアップデート
- `sudo yum -y update` （-yは問い合わせにすべて「y」と返答するオプション。実行結果が分かっている時のみ使用）  
- `sudo yum -y install gcc-c++ make patch git curl zlib-devel openssl-devel ImageMagick-devel readline-devel libcurl-devel libffi-devel libicu-devel libxml2-devel libxslt-devel`


#### 3.Node.jsをインストール
- AWSのリポジトリに追加  
  `curl -sL https://rpm.nodesource.com/setup_14.x | sudo bash -`
- インストール  
  `sudo yum install -y nodejs`
- 下記コマンドでインストールされたか確認  
  `which node`  
  `/usr/bin/node`と表示されたのでOK  
- バージョンを確認  
  `node -v`  
  `v14.21.3`と表示され、バージョンを満たしていなかった  
- Node.jsのバージョン管理ツールnvmをインストールする  
  `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash`  
- 以下コマンド実行後、プロンプトに「nvm」と表示されることを確認する  
  `command -v nvm`  
- 上記で「nvm」が表示されなかったので以下を実行する  
  `source ~/.bash_profile`  
- Node.jsをバージョン指定でインストールする  
  `nvm install 17.9.1`  
- バージョンを確認  
  `node -v`  
  `v 17.9.1`と表示されたのでOK  

#### 4.yarnをインストール  
- AWSのリポジトリに追加  
  `curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo`  
- インストール  
  `sudo yum -y install yarn`  
- バージョンを確認  
  `yarn -v`  
  `1.22.19`と表示されたのでOK  

#### 5.rubyのバージョン管理ツールであるrbenvと、rubyをインストールするためのruby-buildと、rubyをインストール  
- レポジトリをクローンしてインストール  
  `git clone https://github.com/sstephenson/rbenv.git ~/.rbenv`  
- .bash_profileの設定  
  `echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile`  
  `echo 'eval "$(rbenv init -)"' >> ~/.bash_profile`  
  `source ~/.bash_profile`  
- ruby-buildインストールしてrubyをインストール  
  `git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build`  
  `rbenv install -v 3.1.2`  
- このインスタンスで使用するバージョンの設定  
  `rbenv global 3.1.2`  
- インストールしたrubyを使用可能にする  
  `rbenv rehash`  
- バージョンの確認  
  `ruby -v`  
  `ruby 3.1.2p20 (2022-04-12 revision 4491bb740a) [x86_64-linux]`と表示されたのでOK  

#### 6.GitHub利用をしてコードをクローンできるようにするため、EC2上で公開鍵と秘密鍵のペアを作成する
- `ssh-keygen -t rsa`  
- 何も入力せずそのままEnterを3回  
  `Enter file in which to save the key (/home/ec2-user/.ssh/id_rsa): `  
  `Enter passphrase (empty for no passphrase):`  
  `Enter same passphrase again:`   
- GitHubに公開鍵を登録  
  `cd .ssh`  
  `cat id_rsa.pub`  
- ここに表示された文字列を全てコピーする(ssh-rsaから)  
  `ssh -T git@github.com`  


#### 7.GitHubからクローンしてくるアプリの保存先を作成する
- `sudo mkdir /var/www/`  
- 作成したディレクトリの権限をec2-userに変更する  
  `sudo chown ec2-user /var/www/`  
  `cd /var/www`  
  `git clone git@github.com:アカウント名/アプリ名.git`  

#### 8.Amazon Linux2023にアプリが対応していないとのことで、EC2インスタンスを作り直し
- 「インスタンスを起動」を押下  
- 名前とタグは「raisetech-04」  
- アプリケーションおよび OS イメージ (Amazon マシンイメージ)は「Amazon Linux 2 AMI (HVM)」（無料利用枠、デフォルト）  
- インスタンスタイプは「t2.micro」（無料利用枠、デフォルト）  
- キーペアのタイプは「RSA」、プライベートキーファイル形式は「.pem」（デフォルト）  
- ネットワーク設定　VPCは1で作成したもの、サブネットはpublic-1a、パブリックIPの自動割り当ては「有効化」
- ファイアウォールは、既存のセキュリティグループ（RaiseTech-04 security group）
- ストレージ設定は「1x8GiB gp3」（デフォルト）
- 項番1~7を設定し直した

#### 8.MySQLの設定  
- `curl -fsSL https://raw.githubusercontent.com/MasatoshiMizumoto/raisetech_documents/main/aws/scripts/mysql_amazon_linux_2.sh | sh`  
- 初期パスワードの確認  
  `sudo cat /var/log/mysqld.log | grep "temporary password" | awk '{print $13}'`  
- ログイン確認  
  `mysql -u root -p`  
- パスワードの変更  
  `ALTER USER 'root'@'localhost' IDENTIFIED BY '設定するパスワード';
FLUSH PRIVILEGES;`  
- 「今回は EC2 上に DB を構築するのは NG、事前に作成した RDS へ繋ぐようにしてください」とのこと。設定し直し。  
- `sudo yum install mysql`　でMySQLをインストール
- `mysql -u admin -p -h RDSのエンドポイント`　でRDSに接続

#### 9.gemをインストール
- アプリのディレクトリに移動する  
  `cd アプリ名`  
- bundlerのインストール  
  `gem install bundler -v 2.3.14`  
- Gemのインストール  
  `bundle install`  

#### 10.database.ymlの設定
- サンプルファイルをコピー  
  `cp config/database.yml.sample config/database.yml`  
- database.ymlの編集  
  `vi config/database.yml`  
- password: にMySQLのパスワードを入力
- escを押して入力モードを終了する
- developmentとtestのsocketを書き換える  
  `:wq` で保存&編集完了  
  `socket: /var/lib/mysql/mysql.sock`  

#### 11.アプリケーションサーバーの起動
- `bin/setup`  
- `Restarting application server`と表示される  
  `bin/rails db:migrate`  
  `bin/dev`  
