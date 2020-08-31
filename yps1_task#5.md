### webアプリの作成
####　DBの作成
- テーブル構成を変更する
- DDLをmysqldumpで取得する

/var/www/html/yps  
vscodeからssh接続をしてターミナルを開く  
ディレクトリを作る  
```
mkdir resources/sql/
```
ダンプをとる
```
mysqldump -u root -p -d worldcup2014db > resources/sql/worldcup2014db.sql
```
テーブルがあるかをチェック
```
grep -i 'create table' resources/sql/worldcup2014db.sql
```
結果
> CREATE TABLE \`countries\` (  
> CREATE TABLE \`goals\` (  
> CREATE TABLE \`goals_tmp\` (  
> CREATE TABLE \`pairings\` (  
> CREATE TABLE \`pairings_tmp\` (  
> CREATE TABLE \`players\` (  
> CREATE TABLE \`players_tmp\` (  

countries, goals, pairings, playersのテーブルにフィールドを追加  
[参考](https://github.com/yotaro-ok/yps/issues/14#issuecomment-678294088)  

sqlにログイン
```
mysql -u root -p
```
データベースを削除
```
drop database worldcup2014db;
```
DB再作成してuse
```
create database worldcup2014db;
use worldcup2014db;
```
構成変更したDDLを流し込む
```
source resources/sql/worldcup2014db.sql;
```
テーブルが出来ているかチェック
```
show tables;
```
データの取得
```
cd /tmp
sudo yum install wget unzip -y
wget http://tech.pjin.jp/wp-content/uploads/2016/04/worldcup2014.zip
unzip worldcup2014.zip
ls -la worldcup2014.sql
```
worldcup2014.sqlのテーブル構成部分を削除
```
grep -i 'create table' /tmp/worldcup2014.sql
```
この部分を削除する
> CREATE TABLE \`countries\` (  
> CREATE TABLE \`goals\` (  
> CREATE TABLE \`goals_tmp\` (  
> CREATE TABLE \`pairings\` (  
> CREATE TABLE \`pairings_tmp\` (  
> CREATE TABLE \`players\` (  
> CREATE TABLE \`players_tmp\` (  
[参考](https://twitter.com/yotaro__ok/status/1296806680001273856)  

/var/www/html/ypsに戻る
```
cd -
```
mysql cliにログイン
```
mysql -u root -p -D worldcup2014db
```
/tmp/worldcup2014.sqlのデータだけ流し込む
```
source /tmp/worldcup2014.sql;
```
確認
```
select count(*) from countries;
select count(*) from goals;
select count(*) from pairings;
select count(*) from players;
```
[結果](https://twitter.com/yotaro__ok/status/1296810491721916419)

#### modelクラスの作成
以前作製したファイルの削除
```
rm app/Models/Player.php 
```
モデルクラスの作成
```
php artisan make:model Models/Country -m
php artisan make:model Models/Goal -m
php artisan make:model Models/Pairing -m
php artisan make:model Models/player -m
```
念の為、明示的にテーブル名を指定  
ex.app\Models\player.php
> <\?php
> 
> namespace App\\Models;
> 
> use Illuminate\\Database\\Eloquent\Model;
> 
> class player extends Model  
> \{  
>    //  
>    protected $table = "players";  
>    protected $dates = ["expired_at", "deleted_at", "updated_at", "created_at"];  
> \}  

#### controllerクラスの作成
```
php artisan make:controller CountryController --resource --model=Models/Country
php artisan make:controller GoalController --resource --model=Models/Goal
php artisan make:controller PairingController --resource --model=Models/Pairing
php artisan make:controller PlayerController --resource --model= Models/Player
php artisan make:controller PlayerController --resource --model=Models/Player
php artisan make:controller WelcomeController --resource
```
#### viewクラス
resources/views/welcome.blade.phpを書き換え  
[ソース](https://github.com/yotaro-ok/myapp/blob/develop/resources/views/welcome.blade.php)
#### ルータ
[ソース](https://github.com/yotaro-ok/myapp/blob/develop/routes/web.php)

####
初期化
```
php artisan cache:clear && php artisan config:clear && php artisan route:clear && php artisan view:clear
composer clear-cache && composer dump-autoload --optimize
sudo chown -R centos:nginx /var/www/html/yps
```
課題
　①テーブルビューを１つ追加
　②functionを１つ追加
　③controllerからviewに変数渡し
[参考](https://www.ritolab.com/entry/49)

#### ※npm nodejsのアップデート
[参考](https://cpoint-lab.co.jp/article/201811/6351/)

```
composer require laravel/ui
php artisan ui bootstrap
sudo yum remove node npm -y
curl -sL https://rpm.nodesource.com/setup_12.x | sudo bash -
sudo yum install nodejs -y
cd /var/www/html/yps
rm -rf ./node_modules
npm install && npm run dev
```
#### 追加課題
1.得点王として自分を追加する。  
mysqlからplayersとgoalsにデータを追加する。  
```
insert into players values (737,12,26,'MF','CN','YPS1','1990-08-26',158,55,CURRENT_TIMESTAMP,NULL,NULL,NULL);
```
2.二人の酒井選手にそれぞれ名前を追加する  
```
select * from players where name LIKE '酒井%';
update players set name = '酒井宏樹' where id=723;
update players set name = '酒井高徳' where id=724;
select name,club,birth from players where name like '酒井%';
```
3.データベースを論理削除してみる  
[課題](https://twitter.com/yotaro__ok/status/1298973532647378944)  

[解答](https://twitter.com/yotaro__ok/status/1299657766265479168)  
[参考](https://twitter.com/miyupacaaa/status/1299748646620078081)  
