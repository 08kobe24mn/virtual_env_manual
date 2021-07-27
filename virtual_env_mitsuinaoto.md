# 環境構築手順書

## 作成する環境のver

| 言語 | サーバ | DB | フレームワーク | OS |
| :---: | :---: | :---: | :---: | :---: |
| PHP7.3 | Nginx | MySQL5.7 | Laravel6.0| CentOS7|

## 目次

1. [vagrantの作業ディレクトリの作成](#1-vagrantの作業ディレクトリの作成)
2. [vagrantfileの編集](#2-vagrantfileの編集)
3. [vagrantプラグインのインストール](#3-vagrantプラグインのインストール)
4. [vagrantを使用してゲストOSの起動](#4-vagrantを使用してゲストosの起動)
5. [パッケージのインストール](#5-パッケージのインストール)
6. [PHPのインストール](#6-phpのインストール)
7. [composerのインストール](#7-composerのインストール)
8. [MySQLのインストール](#8-mysqlのインストール)
9. [Nginxのインストール](#9-nginxのインストール)
10. [Laravelのインストール](#10-laravelのインストール)
11. [Laravelを動かす](#11-laravelを動かす)
12. [Laravelプロジェクト作成](#12-laravelプロジェクト作成)
13. [Laravel認証機能作成](#13-laravel認証機能作成)

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

## 4. vagrantを使用してゲストOSの起動

## 5. パッケージのインストール

## 6. PHPのインストール

## 7. composerのインストール

## 8. MySQLのインストール

## 9. Nginxのインストール

## 10. Laravelのインストール

## 11. Laravelを動かす

## 12. Laravelプロジェクト作成

## 13. Laravel認証機能作成