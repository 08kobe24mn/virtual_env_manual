# 環境構築手順書

## はじめに

今回ホストOSとして使用するのは、Windows10 Home  
使用するターミナルは、Command Prompt  
VirtualBoxとVagrantはインストールされている状態を想定しています。  
インストールがまだの方は先にインストールをお願いします。  
[VirtualBoxのインストール](https://www.virtualbox.org/wiki/Downloads)  
[Vagrantのインストール](https://www.vagrantup.com/downloads)

## 作成する環境のver

| 言語 | サーバ | DB | フレームワーク | OS |
| :---: | :---: | :---: | :---: | :---: |
| PHP7.3 | Nginx | MySQL5.7 | Laravel6.0| CentOS7|

## 目次

1. [作業ディレクトリの作成](#1-作業ディレクトリの作成)
2. [vagrantfileの編集](#2-vagrantfileの編集)
3. [vagrantプラグインのインストール](#3-vagrantプラグインのインストール)
4. [vagrantを使用してゲストOSの起動](#4-vagrantを使用してゲストosの起動)
5. [パッケージのインストール](#5-パッケージのインストール)
6. [PHPのインストール](#6-phpのインストール)
7. [composerのインストール](#7-composerのインストール)
8. [Laravelのインストール](#8-laravelのインストール)
9. [Nginxのインストール](#9-nginxのインストール)
10. [Laravelを表示する](#10-laravelを表示する)
11. [MySQLのインストール](#11-mysqlのインストール)
12. [Laravel認証機能作成](#12-laravel認証機能作成)  
[参考情報](#参考情報)

<br>

## 1. vagrantの作業ディレクトリの作成

以下のいずれかのディレクトリ下にvagrantの作業用ディレクトリを作成。  
* 自分の作業用ディレクトリ
* デスクトップ

今回は**virtual_env_manual**という名前のディレクトリを作成し、作業を進めていきます。

```
mkdir virtual_env_manual

cd virtual_env_manual
```
CentOS7のboxを追加します。  
今回使用する仮想マシンはVirtual Boxなので、3を選択してEnterを押す。

```
vagrant box add centos/7

1) hyperv
2) libvirt
3) virtualbox
4) vmware_desktop

Enter your choice: 3
```

以下のように表示されたらboxの追加は完了です。

```
Successfully added box 'centos/7' (v2004.01) for 'virtualbox'!
```

続いて仮想マシンの初期化を行い、初期設定ファイル(Vagrantfile)を作成する。

```
vagrant init centos/7
```

以下の文言が表示され、ディレクトリ内にVagrantfileが生成されていることが確認できればOKです。

```
A `Vagrantfile` has been placed in this directory. You are now  
ready to `vagrant up` your first virtual environment! Please read  
the comments in the Vagrantfile as well as documentation on  
`vagrantup.com` for more information on using Vagrant.  
```

<br>

## 2. vagrantfileの編集

作成したVagrantfileの内容を編集します。
3ヶ所編集します。

<br>

変更点1
```
# config.vm.network "forwarded_port", guest: 80, host: 8080
↓
config.vm.network "forwarded_port", guest: 80, host: 8080
```

変更点2
```
# config.vm.network "private_network", ip: "192.168.33.10"
↓
config.vm.network "private_network", ip: "192.168.33.19"
```

変更点3
```
# config.vm.synced_folder "../data", "/vagrant_data"
↓
config.vm.synced_folder "./", "/vagrant", type:"virtualbox"
```

上記3ヶ所を変更したらVagrantfileの編集は完了です。

<br>

## 3. vagrantプラグインのインストール

Vagrantには様々なプラグインが用意されています。  
今回は**vagarnt-vbguest**をインストールします。  
vagrant-vbguestとは、vagrantの起動時およびリロード時にGuestAdditonsのバージョンを  
VirtualBoxのバージョンに合わせて最新化してくれるプラグインです。

```
vagrant plugin install vagrant-vbguest

vagrant plugin list
```

上記でlistに追加されていればプラグインのインストールは完了です。

<br>

## 4. vagrantを使用してゲストOSの起動

Vagrantfileがあるディレクトリで以下のコマンドを実行してvagrantを起動する。

```
vagrant up
```

下記のコマンドで仮想マシンの状態を確認することができる。

```
vagrant status
```

実行した結果次のように表示されたら正常に起動できています。

```
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

続いて、ゲストOSにログインをします。

```
vagrant ssh
```

上記コマンドを実行し、

```
[vagrant@localhost ~]$
```

ログイン後、カーネルをアップデートします。

```
sudo yum -y update kernel

sudo yum -y install kernel-devel kernel-headers kernel-tools kernel-tools-libs
```

インストールが完了したら一度Vagrantを再起動します。

```
exit

vagrant reload
```

再度ログインできたら完了です。

<br>

## 5. パッケージのインストール

ここまでは最小限必要のコアパッケージしかインストールされていません。  
開発するにあたって必要となるパッケージをインストールしていきます。  
今回は**development tools**というグループパッケージをインストールします。

```
sudo yum -y groupinstall "development tools"
```

上記コマンドで、開発に必要なパッケージを一括でインストールできます。

<br>

## 6. PHPのインストール

PHPのインストールをしていきます。  
今回はPHP7.3をインストールしていきます。

```
sudo yum -y install epel-release wget

sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

sudo rpm -Uvh remi-release-7.rpm

sudo yum -y install --enablerepo=remi-php73 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip

php -v
```

上記コマンドを実行後、PHPのバージョンが確認出来たらPHPのインストールは完了です。

<br>

## 7. composerのインストール

PHPのパッケージ管理ツール**composer**をインストールします。

<br>

セットアップ用のPHPファイルをダウンロードします。  
実行したディレクトリにcomposer-setup.phpがダウンロードされる。

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

ダウンロードしたファイルを実行します。

```
php composer-setup.php
```

実行した後は不要なので削除します。

```
php -r "unlink('composer-setup.php');"
```

どのディレクトリにいてもcomposerコマンドを使用できるようfileの移動を行います。

```
sudo mv composer.phar /usr/local/bin/composer
```

最後にバージョンを確認出来たらcomposerのインストールは完了です。

```
composer -v
```

<br>

## 8. Laravelのインストール

Laravelのバージョン6.0を導入していきます。  
準備としてLaravelを導入するディレクトリに移動しましょう。

```
cd /vagrant
```

Laravel6.0をインストール

```
composer create-project laravel/laravel=6.0 --prefer-dist laravel_sample
```

インストールしたLaravelのディレクトリに移動してバージョンを確認

```
cd laravel_sample

php artisan --version
```

6.xになっていれば成功です。

<br>

## 9. Nginxのインストール

Nginxの最新版をインストールしていきます。  
viエディタを使用して以下のファイルを作成・編集していきます。

```
sudo vi /etc/yum.repos.d/nginx.repo
```

作成したファイルには次のように記述します。

```
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=0
enabled=1
```

入力方法は  
**i**でインサートモードにし、編集後**esc**でインサートを終了し、**:wq**で保存して終了します。  
続いてNginxをインストールしていきます。  
laravel_sampleでコマンドを実行します。

```
sudo yum install -y nginx

nginx -v
```

Nginxのバージョンは確認できましたか？  
確認出来たらNginxを起動しましょう。

```
sudo systemctl start nginx
```

起動したら、Vagrantfileで設定したipアドレスにアクセスしてみましょう。  
NginxのWelcomeページが表示されたら完了です。

<br>

## 10. Laravelを表示する

まず、Nginxのファイルを編集します。

```
sudo vi /etc/nginx/conf.d/default.conf
```

```
server {
  listen       80;
  server_name  192.168.33.19; # Vagranfileでコメントを外した箇所のipアドレスを記述してください
  root /vagrant/laravel_sample/public; # 追記
  index  index.html index.htm index.php; # 追記

  #charset koi8-r;
  #access_log  /var/log/nginx/host.access.log  main;

  location / {
      #root   /usr/share/nginx/html; # コメントアウト
      #index  index.html index.htm;  # コメントアウト
      try_files $uri $uri/ /index.php$is_args$args;  # 追記
  }

  # 省略

  # 以下の該当箇所のコメントアウトを指定の箇所外し、変更する場所もあるので変更を加える
  location ~ \.php$ {
  #    root           html;
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;  # $fastcgi_script_name以前を /$document_root/に変更
      include        fastcgi_params;
  }
  ```

続いてPHPインストール時にインストールしたphp-fpmの設定ファイルを編集していきます。

```
sudo vi /etc/php-fpm.d/www.conf
```

```
;24行目近辺
user = apache
↓ 変更
user = vagrant

group = apache
↓ 変更
group = vagrant
```

編集が完了したらNginxを再起動してphp-fpmを起動しましょう。

```
sudo systemctl restart nginx

sudo systemctl start php-fpm
```

403 Forbidden というエラーが出た場合。

```
sudo vi /etc/selinux/config
```

一ヶ所記述を変更してください。

```
SELINUX=enforcing
↓
SELINUX=disabled
```

保存を反映させるためにVagrantを再起動し、再接続しましょう。

```
exit

vagrant reload

vagrant ssh
```

Nginxを再起動、php-fpmを起動しましょう。

```
sudo systemctl restart nginx

sudo systemctl start php-fpm
```

Laravelのhomeが表示されたらエラー解決です。

<br>

## 11. MySQLのインストール

今回インストールするデータベースは、MySQL5.7です。

```
sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm

sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm

sudo yum install -y mysql-community-server

mysql --version
```

上記コマンドを実行し、最後にバージョンが確認出来たらインストール完了です。  
続いてMySQLを起動し接続します。  

編集が完了したら、MySQLを起動します。

```
sudo systemctl start mysqld
```

続いて初期パスワードを確認します。

```
sudo cat /var/log/mysqld.log | grep 'temporary password'

2021-01-01T00:00:00.000000Z 1 [Note] A temporary password is generated for root@localhost: <初期パスワード>
```

初期パスワードをコピーしてください。  
Enter password:の部分にコピーした初期パスワードを貼り付け、Enterを押すと接続できます。

```
mysql -u root -p

Enter password:<初期パスワード>

mysql>
```

続いて、パスワードの設定を行うのですが、  
初期設定の段階では、大文字＋小文字の英数字＋記号を含んだ8文字以上に設定しなければいけません。  
非常に面倒なので、シンプルなパスワードを設定できるよう設定ファイルを編集します。

```
sudo vi /etc/my.cnf
```

変更するのは一ヶ所だけです。  
validate-password=OFFを指定の場所に追加してください。

```
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
validate-password=OFF <-追加

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

編集後、MySQLの再起動が必要となります。

```
sudo systemctl restart mysqld
```

再度初期パスワードを使って接続し、passwordの変更を行います。

```
mysql > set password = "新しいpassword";
```

最後に使用するDBを作成します。

```
mysql > create database DBの名前;
```

これでMySQLのインストールと設定完了です。

<br>

## 12. Laravel認証機能作成

最後にLaravelの認証機能を作成していきます。

まず、laravel_sampleディレクトリ下の```.env```ファイルの内容を編集します。

```
DB_PASSWORD=
↓
DB_PASSWORD=登録したパスワード
```

laravel_sampleディレクトリに移動後、以下のコマンドを実行します。

```
composer require laravel/ui "^1.0" --dev

php artisan ui vue --auth

php artisan migrate
```
マイグレーションが問題なく実行できた後、ブラウザ上でユーザー登録ができれば完了です。



## 参考情報

[Qiita CentOS7 + Vagrant環境でLaravel構築 - 入門](https://qiita.com/Larkpop36/items/1991d5fd33759f3fc643)

[[PHP] Composerをインストールする方法](https://webbibouroku.com/Blog/Article/php-composer-setup)

[Nginx を CentOS 7 にインストールする手順](https://weblabo.oscasierra.net/nginx-centos7-install/)

[Laravel 6.x 認証](https://readouble.com/laravel/6.x/ja/authentication.html)