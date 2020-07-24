### yps1 #task2
- mysql 5.7のインストール
- nginxのインストール
- php7.3のインストール
- vscodeでのssh接続設定

#### mysql 5.7 のインストール

```
sudo yum localinstall http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
sudo yum install -y mysql-community-server
mysqld --version //バージョン確認ができればok
sudo systemctl enable mysqld //自動起動設定
```
エラーが起きていないか確認する
```
sudo systemctl start mysqld
sudo systemctl status mysqld
sudo systemctl stop mysqld
```
初期パスワードを取得する
```
sudo cat /var/log/mysqld.log | grep -i root
```
root@localhost:* に表示されたものが初期パスワード

```
sudo systemctl start mysqld
mysql -u root -p //初期パスワードを入力する
```
mysqlのプロンプトになったら初期パスワードを変更する
```
SET PASSWORD = PASSWORD('XXXXXXXX');
```
exitでmysqlプロンプトから出る<br>
mysqlの文字コードの変更をする
```
sudo vi /etc/my.cnf
```
以下を追記
> character-set-server=utf8mb4<br>
> [client]<br>
> default-character-set=utf8mb4<br>

mysqlを再起動してログイン
```
sudo systemctl restart mysqld
mysql -u root -p
```
設定を確認
```
show variables like "chara%";
```
[MySQLの文字コードをutf8mb4に変更](https://qiita.com/deco/items/bfa125ae45c16811536a)

#### nginxのインストール

ファイル作成
```
sudo vi /etc/yum.repos.d/nginx.repo 
```
以下を追記して保存
> [nginxv]<br>
> name=nginx repo<br>
> baseurl=https://nginx.org/packages/centos/$releasever/$basearch/<br>
> gpgcheck=0<br>
> enabled=1<br>

nginxのインストール
```
sudo yum install nginx -y
nginx -v　//バージョン表示されていれば成功
sudo systemctl enable nginx // 自動起動設定
```
AWSマネジメントコンソールからHTTPとHTTPSをセキュリティグループのインバウンドルールに追加する<br>
```
sudo systemctl start nginx //nginxの起動
```
AWSインスタンスのIPv4アドレスにブラウザからアクセスできるようになるぞ！<br><br>
設定ファイルを編集する
```
sudo mkdir -p /var/www/html //ファイルの作成
sudo chown -R centos:nginx /var/www
sudo cp /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.org　//nginxの元ファイルを保存
sudo vi /etc/nginx/conf.d/default.conf　//設定ファイルを編集
```
> server_name //IPv4 パブリック DNS(IPv4);<br>
> #root /usr/share/nginx/html;<br>
> root /var/www/html;<br>

ブラウザでアクセスすると403が表示される。

#### PHP7.3のインストール
EPELとREMIをインストールする
```
sudo yum install epel-release
sudo yum update
sudo yum install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum update
```
PHPをインストールする
```
sudo yum -y install --enablerepo=epel,remi,remi-php73 php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt php-fpm php-mysql php-mysqlnd
php -v //バージョンが表示されたら成功
```
設定の変更
```
sudo cp /etc/php-fpm.d/www.conf /etc/php-fpm.d/www.conf.org
sudo vi /etc/php-fpm.d/www.conf
```
> user = nginx<br>
> group = nginx<br>
> listen = /var/run/php-fpm/php-fpm.sock<br>
> listen.owner = nginx<br>
> listen.group = nginx<br>
> listen.mode = 0660<br>
```
sudo vi /etc/nginx/conf.d/default.conf
```
> index index.php index.html index.htm; //index.phpを追加<br>
> try_files $uri $uri/ /index.php?$query_string;//indexの下の行に追加<br>
> location ~ \.php$ {//server{}の下の行に追加<br>
>                 root /var/www/html;<br>
>                fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;<br>
>                 fastcgi_index  index.php;<br>
>                 fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;<br>
>                 include        fastcgi_params;<br>
>         }<br>

編集したら次のコマンドを入力
```
sudo systemctl restart nginx
sudo systemctl enable php-fpm
sudo systemctl start php-fpm
sudo chown -R centos:nginx /var/www/
```
phpファイルを作成する
```
vi /var/www/html/index.php
```
index.phpの中身
```
<?php
echo phpinfo();
?>
```
IPv4のアドレスにアクセスするとindex.phpが表示される

#### laravelのインストール

[laravel入門](https://qiita.com/yukibe/items/5ee27163b603d7f68250)
```
cd /tmp
php -r "copy('http://getcomposer.org/installer', 'composer-setup.php’);”
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer  
sudo chmod +x /usr/local/bin/composer
```
プロジェクトファイルの作成
```
cd /var/www/html
sudo yum install zip unzip -y
composer create-project --prefer-dist laravel/laravel yps
```
設定
```
cd yps
cp -p .env.example .env
php artisan key:generate
vi .env
```
> APP_URL=ブラウザのURL
> DB_PASSWORD="yps#OK123"

```
sudo yum install npm node -y
composer install
npm install
sudo chown -R centos:nginx /var/www/
sudo chmod -R 777 storage/ bootstrap/cache/
```
rootの変更
```
sudo vi /etc/nginx/conf.d/default.conf
```
> /var/www/html;→/var/www/html/yps/public;

nginxとphp-fpmを再起動する
```
sudo systemctl restart nginx
sudo systemctl restart php-fpm
```

ブラウザでアクセスするとLaravelが表示される

#### VSCodeからEC2に接続する
[VSCode の Remote – SSH 機能を使って EC2 上で開発する](http://blog.serverworks.co.jp/tech/2020/02/20/vscode-remote-ssh/)

[参考](https://paca-gatsby.netlify.app/2020-07-23/)<br>
[元ツイ](https://twitter.com/yotaro__ok/status/1286271610815516672)
