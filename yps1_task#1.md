# ypsに並走する
[yps1 task#1](https://github.com/yotaro-ok/yps/blob/master/task_1.md)

## GitHubアカウントの作成
***
## AWS無料アカウントの作成
***
## EC2上にCentOS7でインスタンス作成

#### 1.EC2を選択する
#### 2.インスタンスを選択する
#### 3.インスタンスの作成を選択する
#### 4.無料利用枠のみを選択する
#### 5.AWS Marketplaceを選択して検索欄にCentOS7と入力する
#### 6.Continueを選択
#### 7.タイプ：t2.micro vCPU :1 メモリ:1　を選択し手次のステップ：インスタンスの詳細設定を選択する
#### 8.追加を選択する
#### 9.サイズ（GiB）に30を入力して次のステップ：タグの追加を選択する
#### 10.セキュリティグループの設定を選択する
#### 11.確認と作成を選択する
#### 12.起動を選択する
#### 13.キーペアの作成を選択し、キーペア名前を入力後にキーペアのダウンロードを選択する
#### 14.インスタンスの作成を選択する
#### 15.インスタンスの状態がrunnnig になっていることを確認する

インスタンスの作成が成功！

***

## SSH接続、環境設定、アップデート

#### awsに接続する
#### 参考
- [win](https://www.dot-plus.com/cloud-service/aws/2935/#TeraTermSSH)
- [mac](https://qiita.com/ai-2723/items/eb156cd4dd3ccfac8791)

#### SElinuxの設定を無視する
```
sudo setenforce 0
vi /etc/selinux/config
```
> SELINUX=disabled<br>
getenfoceとコマンド打ってpermissiveが出れば成功

#### 編集の前に元ファイルを保存する
```
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.org
```
.orgはオリジナルファイルたよーということを表現するときによく使うんだそうです



#### AWSコンソールのセキュリティグループからルールを追加

```
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.org
sudo vi /etc/ssh/sshd_config
```

> #Port 22　→Port ルールの追加で入力した番号<br>
> #PermitRootLogin yes　→PermitRootLogin no<br>

### エラーが表示されないことを確認 
```sudo sshd -t```

#### sshd再起動
```sudo systemctl restart sshd```

#### もういっこターミナルを立ち上げてssh接続できるかどうか確認してみよう

#### ログインに成功したらアップデート
```sudo yum update -y```

[元ツイ1](https://twitter.com/yotaro__ok/status/1284454044077965313)<br>
[元ツイ2](https://twitter.com/yotaro__ok/status/1284115619034484737)<br>

#### 余裕があれば
```
sudo -s /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
sudo -s /sbin/mkswap /var/swap.1
sudo -s /sbin/swapon /var/swap.1
```
