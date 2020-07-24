### 追加課題
1. 時刻をJSTにする
1. .bashrcに追記してプロンプトの色を変える

#### 時刻をJSTにする

```$sudo timedatectl se-timezone Asia/Tokyo ```

dateコマンドで時刻が確認できる<br>
引数なしのtimedatecltdでも設定が確認できる<br>
[参考](https://weblabo.oscasierra.net/centos7-timezone-1/)<br>

mfeed時刻提供サービスを利用する場合は /etc/chrony.confを編集する
```
$sudo vi /etc/chrony.conf
```
> #server 0.centos.pool.nyp.org ibrust<br>
> #server 1.centos.pool.nyp.org ibrust<br>
> #server 2.centos.pool.nyp.org ibrust<br>
> #server 3.centos.pool.nyp.org ibrust<br>
> #server ntp2.jst.mfeed.ad.jp ibrust<br>

#### .bashrcに追記してコマンドプロンプトの色を変える
サーバ種別や環境をより直感的に識別するためにプロンプトの色を変えておくと便利。<br>
BashのPromptの書式に関する情報は、PS1というShell変数に格納されているので、exprotsコマンドで変数を設定する。<br>


```
export PS1='\[\033[01;36m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ 
```
ファイルの作成
```
vi .bash_profile//何もせず終了
vi .bashrc
```
以下を追記
> PS1="\[\033[36m\]\h:\W \u\$\[\033[0m\]"

ターミナルを再起動すると文字色が変更する

[参考](https://qiita.com/wildeagle/items/5da17e007e2c284dc5dd)
[ターミナルの文字色を変える](https://qiita.com/hirose/items/bbfeeb3fe50725f2a7a7)

#### bash-complication
入力補間機能。TABキーを押すとコマンドを補完してくれる。

```
$sudo yum insatall bash-completion
```
bash-completionの定義ファイルは、次のディレクトリ配下に存在する。
- /etc/bash_completion.d/
- /usr/share/bash-completion/completions/

[参考](https://qiita.com/yamada-hakase/items/bf163f0924e4d925fefb)

[追加課題の元ツイ](https://twitter.com/webappli_cot/status/1285946242355220480)
