# AWS-Linux

AWS上にLinux構築

＃Day1
【東京リージョンでEC2立ち上げ】
AMI:Ubuntu Server　22.04 LTS
インスタンスタイプ：t2.micro
ネットワーク：デフォルトVPC
サブネット：自動割り当て
パブリックIP：自動割り当て＞有効化」
セキュリティグループ：新規作成＞自分のIPのみ許可
ストレージ：８GB、GP３、インスタンス終了時に削除ON

トラブルなくインスタンス起動成功！

＃Day2
【SSH接続テスト】
1. ダウンロードしたキーペアをsshフォルダに移動。
   Linux使用コマンド:mv ~/downloads/<キーペア名> ~/.ssh/
   ※フォルダに移動できてるか確認　lsコマンド使用。
2. アクセス権変更(セキュリティ対策)
   Linux使用コマンド:chmod 400 ~/.ssh/<キーペア名>
3. SSH接続※AWSマネジメントコンソールで確認したIPv4を使う。
   Linux使用コマンド:ssh -i ~/.ssh/<キーペア名> ubuntu@IPv4
   キーペアの権限が広すぎる(他のユーザーから見える状態)だと、SSHが弾いて接続できないから注意。
4. 初回接続
   初回だけ接続確認あり。＞yesと入力して続行。
   接続成功すると ubuntu@IP-が表示される。
5. Ubuntuのバージョン確認
   Linux使用コマンド:lsb_release -a
   バージョンが表示されたら成功。
〜トラブル対処メモ〜
Permission denied:.pemのパス or 権限ミス
タイムアウト:セキュリティグループでポート(SSH:20)が閉じている可能性あり。
ホストが応答しない:インスタンス停止中 or IPが変わっている
IPが変わっている場合、Elastic IP(固定IP)を割り当てる。割り当てないとインスタンス停止時に変わるので注意。

#Day3
【Nginx立ち上げ】
1. システムの最新化
   Linux使用コマンド：sudo apt update && sudo apt upgrade -y
   ※1 コマンドの意味はupdate成功後にupgradeを行う。末尾の-yはYes確認の簡略化。
   ※2 apt・・・パッケージ管理ツールでソフトのインストール・更新・検索・削除などをやってくれる。
   ※3 パッケージ・・・Linuxではソフトウェアのことをパッケージという。
   ※4 updateはパッケージの「最新情報リスト」を取得(カタログ更新)、情報更新。upgradeはそのリストをもとに新しいバージョンにアップデートする、実際に更新。
   ※5 sudoコマンドの役割は一時的にroot権限を付与してプログラムを実行させるコマンド。
2. Nginxのインストール
   Linux使用コマンド：sudo apt install nginx -y
   ※Nginx・・・Webサーバーのこと。Linuxでは他にはApache、Litespeedなどがある。
3. Nginxを起動して、状態を確認
　　Linux使用コマンド：sudo systemctl start nginx
                    sudo systemctl status nginx
   statusの画面でactive表示を確認。
   ※ systemctlコマンド・・・サービス(＝常駐プログラム)を管理するコマンド、Nginxなどはバックグラウンドで動いているので、それを制御する。サービスは「daemon：デーモン」ともいわれる。
                           startはサービスを起動、statusは状態を確認するコマンド。止めたい場合はstopを使用。
4. ポート80を開放(HTTP用)
   AWSコンソール＞セキュリティグループ＞インバウンドルールを編集
   タイプ：HTTP　プロトコル：TCP　ポート範囲：80 ソース：0.0.0.0/0
   ※ ソースはアクセスを許可するIPアドレス(範囲)のこと。0.0.0.0/0は「全世界からアクセスOK」ということ。
5. ブラウザでアクセスして確認
   "Welcome to nginx!"ページ表示確認。接続成功！
   
   
