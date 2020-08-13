### task4
WordPressセットアップ

wpをダウンロードして解答
```
cd /tmp
wget https://ja.wordpress.org/latest-ja.zip
unzip ./latest-ja.zip
```
専用のディレクトリを用意
```
mv wordpress wwpp
mv wwpp /var/www/html/
```
SQLにログイン
```
mysql -u root -p
```

```
create database wwppdb;
grant all on wwppdb.* to wwppuser@localhost identified by '任意のパスワード';
```

> ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
[Mysql 5.7* パスワードをPolicyに合わせるとめんどくさい件について](https://qiita.com/keisukeYamagishi/items/d897e5c52fe9fd8d9273)
```
SHOW GLOBAL VARIABLES LIKE 'validate%';
```
validate_password_policy=MEDIUMに設定されてる。文字列の長さを指定した長さにして、数字を使って、大文字、小文字を使って、さらに特殊文字を用いてパスワードにしてくださいということらしい。  
  
wwpp内にあるwp-config.phpのバックアップを取って、開く。
```
cd /var/www/html/wwpp/
cp -p wp-config-sample.php wp-config.php
vi wp-config.php
```
[ここ](https://github.com/yotaro-ok/yps/issues/12#issuecomment-671045833)を参考にしながらwp-configを編集する  
次にwwppディレクトリとその中のファイル群の権限を設定
```
cd .. // cd /var/www/html
sudo find /var/www/html/wwpp/ -type f -exec chmod 664 {} \;
sudo find /var/www/html/wwpp/ -type d -exec chmod 775 {} \;
sudo chown -R centos:nginx /var/www/html/wwpp/
```
.htaccessファイルを作成。権限変更  
[.htaccessファイルとは](https://qiita.com/sunnyG/items/7e5bd6e8dc9b04c9978e)  
[ngixでhtaccessを使う？](https://twitter.com/yotaro__ok/status/1292580923192430593)

```
cd wwpp
vi .htaccess  
```
次の内容を記述して保存
> <files wp-config.php>  
>   order allow,deny  
>   deny from all  
> </files>  

Laravel学習時に行った設定をWP用に設定を変える。
```
sudo vi /etc/nginx/conf.d/default.conf
```
> root /var/www/html/yps/public;→root /var/www/html/wwpp;  
> location /wp-config.php {  
>  deny all;  
> }
```
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```
インスタンスのIPv4アドレスにアクセスしてwordporessのインストール画面が表示されるのを確認する  
WordPressをインスール  
ログインしてダッシュボード画面が表示されていることを確認  

sshポートから出来るようにするプラグインを入れる  

```
cd wp-content/plugins/
wget https://downloads.wordpress.org/plugin/ssh-sftp-updater-support.0.8.2.zip
unzip ./ssh-sftp-updater-support.0.8.2.zip 
rm ssh-sftp-updater-support.0.8.2.zip 
sudo chown -R centos:nginx ./ssh-sftp-updater-support/
```
WP管理画面のプラグインページを見て、「SSH SFTP Updater Support」を有効化  
その後「SSH SFTP Updater Support」を選択して、更新  
WordPressの画面に戻り、プラグインのところにあるSSH SFTP Updater Supportを有効化  

> ホスト名：ecXXXX.ap-northeast-1.compute.amazonaws.com:ポート番号  
> ユーザ名：centos  
> 接続形式：SSH2  
> Upload Private Key:XXX.pemファイルを選択  

更新できたらターミナルに戻ってコンフィグの権限を設定  

```
cd /var/www/html/wwpp/
sudo chmod 664 ./wp-config.php
```
テーマを選んで、セキュリティ設定
```

```
[元ツイ](https://twitter.com/yotaro__ok/status/1292432592973647872)  
[参考1](https://paca-gatsby.netlify.app/2020-08-09/)  
[参考2](https://yousuke.hatenadiary.com/entry/2020/08/09/230208)

[追加課題](https://twitter.com/yotaro__ok/status/1292586026733428736)
