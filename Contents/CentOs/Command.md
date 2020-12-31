---
layout: default
title:  ":computer: Linux、CentOS"
description: ":wrench: CentOSコマンド"
date: "2020/05/17"
lastmod: "2020/05/17"
---

### 0. ユーザーの切り替え

#### 0-1. 一般ユーザーからroot管理者に切り替え

    [username@localhost ~]$ su -
    パスワード:<※表示しないので構わず入力>
    [root@localhost ~]# 

#### 0-2. root管理者から一般ユーザーに戻す

    [root@localhost ~]# exit
    ログアウト
    [username@localhost ~]$

<br />

### 1. ディレクトリ、ファイル操作

#### 1-1. ディレクトリ(フォルダ)移動

`[username@localhost ~]$`の`~`が現在のディレクトリです。  
例：`/opt`ディレクトリへ移動する  

    [username@localhost ~]$ cd /opt
    [username@localhost opt]$

`[username@localhost opt]$`になりoptが現在のディレクトリです。  

#### 1-2. カレント(現在)ディレクトリ内のファイル、ディレクトリ一覧を表示

例：簡潔な表示。ルートディレクトリを表示  

    [username@localhost /]$ ls
    bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
    boot  etc  lib   media  opt  root  sbin  sys  usr

例：詳細な表示  
1文字目：dはフォルダ、-は一般ファイル  
2文字目～10文字目：アクセス権。  
2～4文字目は管理者(所有者)のアクセス権。5～7文字目はグループユーザーのアクセス権。  
8～10文字目は他の一般ユーザー(所有者以外)のアクセス権。  
アクセス権の`r`は読み取りの権利、`w`は書き込みの権利、`x`は実行の権利、`-`は権利なしを表す。  
また、rは数字の`4`を表し、wは`2`を表し、xは`1`を表し、-は`0`それらを足した数字で指定し使う場合もある。  
よってrwxは`7`、r-xは`5`と解釈する。以下のoptディレクトリではrwxr-xr-xとなっているので`755`となる。  

    [username@localhost /]$ ls -l
    合計 27
    lrwxrwxrwx.   1 root root    7  5月 11  2019 bin -> usr/bin
    dr-xr-xr-x.   6 root root 4096  1月 13 19:30 boot
    drwxr-xr-x.  20 root root 3140  1月 26 09:48 dev
    drwxr-xr-x. 133 root root 8192  1月 24 20:01 etc
    drwxr-xr-x.   3 root root   22  1月 13 19:26 home
    lrwxrwxrwx.   1 root root    7  5月 11  2019 lib -> usr/lib
    lrwxrwxrwx.   1 root root    9  5月 11  2019 lib64 -> usr/lib64
    drwxr-xr-x.   2 root root    6  5月 11  2019 media
    drwxr-xr-x.   2 root root    6  5月 11  2019 mnt
    drwxr-xr-x.   2 root root    6  5月 11  2019 opt
    dr-xr-xr-x. 222 root root    0  1月 26 09:48 proc
    dr-xr-x---.  15 root root 4096  1月 21 21:03 root
    drwxr-xr-x.  39 root root 1180  1月 26 09:50 run
    lrwxrwxrwx.   1 root root    8  5月 11  2019 sbin -> usr/sbin
    drwxr-xr-x.   2 root root    6  5月 11  2019 srv
    dr-xr-xr-x.  13 root root    0  1月 26 09:48 sys
    drwxrwxrwt.  18 root root 4096  1月 26 09:50 tmp
    drwxr-xr-x.  12 root root  144  1月 13 19:12 usr
    drwxr-xr-x.  21 root root 4096  1月 13 19:28 var

#### 1-3. ディレクトリ(フォルダ)作成

例：ホームディレクトリ`~`にtestdirという名前のディレクトリを作成する  

    [username@localhost ~]$ mkdir testdir

例：管理者(root)以外ではデフォルトでは許可がないルートディレクトリ`/`にtestdirという名前のディレクトリを作成する  

    [username@localhost ~]$ mkdir /testdir
    mkdir: ディレクトリ `/testdir` を作成できません: 許可がありません

#### 1-4. ディレクトリ(フォルダ)削除

例：ホームディレクトリ下のtestdirディレクトリを削除する  

    [username@localhost ~]$ rmdir testdir

#### 1-5. ディレクトリ(フォルダ)名の変更

例：ホームディレクトリ下のtestdirディレクトリ名をhenkodirという名前に変更する  

    [username@localhost ~]$ mv testdir/ henkodir/

#### 1-10. ディレクトリ(フォルダ)の権限変更

例：ホームディレクトリ下のtestdirディレクトリはすべてのユーザーにすべての権利(777)を与える  

    [username@localhost ~]$ chmod 777 testdir

`ls -l`で確認してみます。`rwxrwxrwx`となりtestdirは黄緑の背景色が付いています。    

    [username@localhost ~]$ ls -l
    drwxrwxrwx. 2 username username 6  1月 26 10:31 testdir

<br />

### 3. パッケージ、書庫管理

#### 3-1. パッケージ名の検索

※インターネットに接続できる状態にして試してください。  
例：簡潔な検索。検索キー：httpd  

    [username@localhost ~]$ dnf list httpd
    利用可能なパッケージ
    httpd.x86_64          2.4.37-16.module_el8.1.0+256+ae790463           AppStream

例：詳細な検索。検索キー：httpd  

    [username@localhost ~]$ dnf list | grep httpd
    centos-logos-httpd.noarch                           80.5-2.el8
                                AppStream
    httpd.x86_64                                        2.4.37-16.module_el8.1.0+25
    6+ae790463                  AppStream
    httpd-devel.x86_64                                  2.4.37-16.module_el8.1.0+25
    6+ae790463                  AppStream
    httpd-filesystem.noarch                             2.4.37-16.module_el8.1.0+25
    6+ae790463                  AppStream
    httpd-manual.noarch                                 2.4.37-16.module_el8.1.0+25
    6+ae790463                  AppStream
    httpd-tools.x86_64                                  2.4.37-16.module_el8.1.0+25
    6+ae790463                  AppStream
    keycloak-httpd-client-install.noarch                1.0-2.el8
                                AppStream
    libmicrohttpd.i686                                  1:0.9.59-2.el8
                                BaseOS
    libmicrohttpd.x86_64                                1:0.9.59-2.el8
                                BaseOS
    python3-keycloak-httpd-client-install.noarch        1.0-2.el8
                                AppStream

#### 3-2. インストール済のパッケージ検索方法

例：簡潔な検索。検索キー：httpd  

    [username@localhost ~]$ dnf list --installed httpd
    インストール済みのパッケージ
    httpd.x86_64          2.4.37-16.module_el8.1.0+256+ae790463           @AppStream

インストールされたパッケージが無い場合

    [username@localhost ~]$ dnf list --installed httpd
    エラー： 表示するための一致したパッケージはありません

例：詳細な検索。検索キー：httpd  

    [username@localhost ~]$ dnf list --installed | grep httpd

#### 3-3. パッケージのインストール先Path確認

    [username@localhost ~]$ which httpd
    /usr/sbin/httpd

該当するものが無い(インストールされていない)場合は以下の様なメッセージが出ます。  

    /usr/bin/which: no httpd in(/home/username/.local/bin:/home/username/bin:/home/
    username/.local/bin:/home/username/bin:/usr/local/bin:/usr/local/sbin:/user/bin:/
    usr/sbin)

#### 3-4. 書庫などのファイルダウンロード

例：ダウンロードディレクトリにtar.gz書庫ファイルをダウンロードする

    [username@localhost ダウンロード]$ curl -OL htts://xxxx.tar.gz

#### 3-5. tar.gz書庫の展開(解凍)

例：tar.gz書庫ファイルを~testdirディレクトリに展開する。  
※-C以降を指定しなければ現在のディレクトリ(例ではダウンロードディレクトリ)に展開します。  
※展開先のフォルダは書き込み可能である事。  
※所有者をそのままにする事もできオプションに`p`を追加しxzpvfなどとする事もできる(fより前に書く事xzvfpではいけない)。

    [username@localhost ダウンロード]$ tar -xzvf xxxx.tar.gz -C ~testdir

#### 3-6. .zip書庫の展開(解凍)

例：zip書庫ファイルを~testdirディレクトリに展開する。  
※-d以降を指定しなければ現在のディレクトリ(例ではダウンロードディレクトリ)に展開します。  
※展開先のフォルダは書き込み可能である事。

    [username@localhost ダウンロード]$ unzip xxxx.zip -d ~testdir

<br />

### 4. テキストファイルをテキストアプリで開く

例：CentOSのGUI(Gnome)標準のテキストエディタgeditでtestdirのtest.iniファイルを開く。  

    [username@localhost ~]$ gedit testdir/test.ini

* * *
