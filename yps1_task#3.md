### yps1_task3
やること
- SQL：テーブル作成・バッチ作成
- php.ini設定
- GitHubにファイルをアップロード

#### SQLテーブル作成・バッチ作成
バックアップファイルとの比較でcolordiffをインストールする
```
sudo yum install colordiff -y
```
##### SQLのテーブル作成＆バッチの作成
SQLテーブル作成
```
cd /tmp
sudo yum install wget
wget http://tech.pjin.jp/wp-content/uploads/2016/04/worldcup2014.zip
unzip worldcup2014.zip
ls -la worldcup2014.sql
mysql -u root -p//sqlにログイン

create database worldcup2014db;//データベースworldcup2014dbの作成
use worldcup2014db;
source ./worldcup2014.sql;
show tables;//データベース名が表示されればOK
exit;//抜ける
```
バッチ処理  
データベースからあらかじめ選手の名前を取り出す
```
cd /var/www/html/yps/
vi .env
```
> DB_DATABASE=worldcup2014db

```
php artisan make:model Models/Player
ls -la app/Models/Player.php // ファイルがあるか確認
php artisan make:command TestCommand
ls -la app/Console/Commands/TestCommand.php // ファイルがあるか確認
```
ここからVSCodeで作業する  
/var/www/html/yps/App/Console/Commands/TestCommand.php を[こちら](https://github.com/yotaro-ok/yps/issues/3#issuecomment-663672640)に変更

```
php artisan config:clear
php artisan test_command
```
ターミナルからデータベースの選手名が表示されればOK

##### 日本語入力できない問題の解決
[MySQLクライアントに日本語が入力できない理由](https://developer.suzna.com/entry/2018/04/23/103928)  
SQLのファイルを作ってそれを読み込ませることで解決する。

```
cd /var/tmp
vi get_players.sql//新規ファイル作成
```
> use worldcup2014db;  
> select * from players where name = ‘酒井’;  
get_playsers.sqlを読みこんで使ってみる  
```
mysql -u root -p  < ./get_players.sql
mysql -u root -p  < ./get_players.sql > ./out.txt // ファイル出力もできる
```
酒井さんを呼び出せたらOK  
(実際は、LravelからSQLを実行するので、あまりCLIは使わない)

#### php.iniの設定
元ファイルを保存しておく
```
sudo cp /etc/php.ini /etc/php.ini.org
sudo vi /etc/php.ini
```
[CentOSにPHP7をインストールしたらやっておくべき初期設定 ](https://affiwork.net/php-settings/)  
[解答](https://github.com/yotaro-ok/yps/issues/5#issuecomment-667150729)  
設定が終わったらphp-fpmとNginxを再起動して設定を有効化。  
```
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```
エラーログファイルの作成   
php.iniの末尾にしたをくわえる  
> errorlog= “/var/log/phperrors.log”  
```
sudo touch /var/log/php_errors.log
sudo chown nginx:nginx /var/log/php_errors.log
```
php-fpmとNginxを再起動して設定を有効化。
```
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```
#### GitHubにファイルをアップロードする
gitをインストールしてプロジェクトファイルypsでgit init
```
sudo yum install git -y
cd /var/www/html/yps
git init
```
GitHubでリポジトリを作成  
.gitignoreにlaravelを指定  
READMEの作成  
```
cd /var/www/html/yps/
vi .git/config
```

> [user]<br>
>   name = 名前<br>
>   email = メアド<br>
> [core]<br>
>        repositoryformatversion = 0<br>
>        filemode = true<br>
>        bare = false<br>
>        logallrefupdates = true<br>
> [remote "origin"]<br>
>   url = git@github.com:GitHubのアカウント名/リポジトリ名.git<br>
>    fetch = +refs/heads/*:refs/remotes/origin/*<br>
> [branch "master"]<br>
>    remote = origin<br>
>    merge = refs/heads/master<br>

秘密鍵を作る

```
ssh-keygen -t rsa -b 4096 -C "メアド"
```
エンターキー押下  
パスワード入力×２
```
cat ~/.ssh/id_rsa.pub
```
表示された文字列をGitHubアカウントのSettingからSSHKeyに登録する
```
cd /var/www/html/yps/
git add .
git commit -am "initial"
git pull
git push origin master
```

[develop、featureブランチを切る](https://qiita.com/Naoki206/items/e5520453f92dcd4274f1)  

```
git branch //masterにいることを確認
git branch develop // develop作成
git checkout develop // developに移動
git branch // developに移動してればok
git push origin develop // リポートリポジトリに反映
```
Githubにdevelopブランチができていれば成功

パスフレーズの入力をするとプッシュできる  
[元ツイ](https://twitter.com/yotaro__ok/status/1289185995875745794)  
[参考](https://paca-gatsby.netlify.app/2020-07-30/)  
