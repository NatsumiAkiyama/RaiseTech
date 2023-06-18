# 第3回課題

## AP サーバーについて調べてみましょう。AP サーバーの名前とバージョンを確認してみましょう。AP サーバーを終了させた場合、引き続きアクセスできますか？結果を確認して、また AP サーバーを起動してください。
　![Puma version: 5.6.5](./images/Puma-ver.png)
　![AP サーバーを終了させた場合アクセスできなくなりました。](./images/Puma-stop.png)
　rails sで起動。Ctrl+Cで終了。

## DB サーバーについて調べてみましょう。サンプルアプリケーションで使った DB サーバー（DB エンジン）の名前と、今 Cloud9 で動作しているバージョンはいくつか確認してみましょう。DB サーバーを終了させた場合、引き続きアクセスできますか？
  ![MySQL Ver 8.0.33](./images/MySQL-ver.png)
　![DBサーバーを終了させた場合アクセスできなくなりました。](./images/MySQL-stop.png)
　mysql —versionでバージョン確認。sudo service mysqld start で起動。sudo service mysqld stop で停止。
　
## Rails の構成管理ツールの名前は何でしたか？確認してみてください。
　Bundler

## 今回の課題から学んだことを報告してください。
