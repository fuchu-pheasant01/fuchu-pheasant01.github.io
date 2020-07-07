---
layout: default
title: ":octocat: Git、GitHub"
description: ":octocat: Git、GitHub環境構築メモ"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

事前にローカルにGit for WindowsまたはMacなどをインストールしている事。  
事前にGitHubに登録している事。  
WindowsではGit Bashやコマンドプロンプト、Macではターミナルで操作する事とする。  

<br />

## 1. Gitローカル内の作業

### 1-1. ローカルに個人識別情報の登録 config設定

ここでのユーザー名はGitHubのユーザー名とは別の名前でも良い  
以下の`--global`は、どのGitリポジトリにも適用されるものでGit for Windowsなどをインストールした  
最初の1回設定すれば良い。  
また、これの設定はユーザーフォルダ内に`.config`ファイルとして作成され保存されている。  

    $ git config –-global user.name "ユーザ名"
    $ git config --global user.email "メールアドレス"

個別のリポジトリや１つのPCで複数のGitHubのアカウントを操作する(サブアカウント)場合はリポジトリごとに  
設定しなければならず引数を`--local`とする。リポジトリごとなので1-2.の`git init`でリポジトリを作成した後に行う。  
config設定を行う前に`git add`や`git commit`などを行ってしまうと`--global`ユーザーでの行うことになりGitHubに  
`--global`ユーザーでpushしてしまうので注意。  

    $ git config –-local user.name "ユーザ名>"
    $ git config --local user.email "メールアドレス"

※サブアカウントにメインアカウントのユーザーでPushした形となるため(メールアドレスで紐づけられるのかも)。  

### 1-2. config情報の表示

1-1.で設定した内容を表示します。  

    $ git config --global --list
    $ git config --local --list

### 1-3. ローカルリポジトリの作成

`cd`コマンドでローカルリポジトリを作成したいフォルダに移動しておいた上で以下を実行します。  

    $ git init

### 1-4. ローカルリポジトリのステータス表示

ローカルリポジトリフォルダ内に新しいファイルが作成されたか、変更があったかなどを確認できる。

    $ git status

追加、変更、削除などがあればそのファイルは赤色で表示されます。  
以下の1-5.で`add`を実行したファイルは黄緑色で表示されます。  
ローカルリポジトリ内のファイルの追加や削除どのファイルも変更された箇所がなければ以下のメッセージが出ます。  

    $ nothing to commit, working tree clean

### 1-5. ローカルリポジトリのコミット対象のファイル追加

１ファイル単位(拡張子単位では\*.xxx)

    $ git add ファイル名

変更があったすべてのファイル  

```prompt
$ git add --all
```

### 1-6. コミット対象のファイル追加した内容の取り消し

1-5.で誤って追加した内容の取り消しができます。  

    $ git rm ファイル名

### 1-7. ローカルリポジトリのコミット

1-5.で`add`を実行したものをローカルリポジトリに確定します。  

    $ git commit -m "コミット名"

### 1-6. ローカルリポジトリにGitHubのリモートリポジトリ情報を追加

これは、GitHubにリモートのリポジトリを作成するのではなくローカルリポジトリにリモート情報を登録し、  
`push`や`pull`などを行うためにローカルとリモートの紐づけ設定を行うためのものです。  
**https接続の場合**  

    $ git remote add origin https://github.com/githubユーザー名/リモートリポジトリ名.git

上記では、`origin`という名前を付けましたがリモート情報に対するリモート名と言うことなので何でも良い。  
※名前を変えた場合は`git push origin master`などの`origin`をその名前に変えて実行します。  

git remote add〜の値を間違えた場合以下のようなメッセージが出る

    $ fatal:remote origin already exists.

**SSH接続の場合**  
https接続ではなく鍵を作成＆GitHubに登録し通信する場合は以下のようにする。  
※この後の秘密鍵と公開鍵を登録し、GitHubに公開鍵を追加登録の設定が必要です。  

```prompt
$ git remote add origin git@github.com:githubユーザー名/リモートリポジトリ名.git
```

### 1-7. リモートリポジトリ情報の確認

1-6.で行ったリモートリポジトリ情報の確認を以下でできます。  

    $ git remote -v
    または
    $ cat .git/config
    または
    $ git confgig --local --list

### 1-8.リモートリポジトリ情報の削除

1-6.で誤って登録したリモートリポジトリ情報を削除したい場合は以下でできます。  

    $ git remote rm origin

<br />

## 2. ローカルとリモートリポジトリの操作にかかわる設定

### 2-1. 秘密鍵と公開鍵を作成し、GitHubに公開鍵を追加登録

**鍵をローカル(自分のPC)に追加**

    $ ssh-keygen -t rsa

Windowsはユーザーフォルダ下`.ssh\id_rsaとid_rsa_pub`にMacはユーザーフォルダ下`~/.ssh/id_rsaとid_rsa.pub`に  
の鍵が作成される。  
※読み取り専用パーミッションの変更は行わなくてもできるが以下で行える。  
`cd ~/.ssh`などで.sshフォルダへ移動しておく事。  

    $ chmod 600 id_rsa

**GitHubに公開鍵を追加**  
Windowsの場合は、`id_rsa_pub`の中身をテキストエディタで開きテキストすべてコピーしGitHubの`Personal settings`の  
`SSH and GPG keys`タブの`New SSH key`をクリックし貼り付け。鍵名は鍵に対する名前なので何でも良い。  

Macの場合では、これらは隠しファイルとなって作成されている。  
また、テキストエディタを開いてコピペは便利が悪いので以下のコマンドを打つとクリップボードにコピーされる。  
その後、GitHubの`New SSH key`にペーストする。

    $ pbcopy < ~/.ssh/id_rsa.pub
    または
    $ cat ~/.ssh/id_rsa.pub | pbcopy

### 2-2. １つのPCで複数のGitHubアカウントを作成しそれぞれの鍵を使いpushし使い分ける

**2つ目の鍵の作成**   

    $ cd ./ssh

でユーザーディレクトリの`./ssh`フォルダへ移動した上で以下を実行する事。  

    $ ssh-keygen -t rsa -C "メールアドレス" -f futatsume_rsa

パスフレーズを聞かれるが設定していなければそのままEnter。  
※実行したディレクトリ内に作成されるので注意が必要(そのために上記の`./ssh`へ移動)。  

`~/.ssh/`下にfutatsume_rsaとfutatsume_rsa.pubファイルが出来る。  
futatsume_rsa.pubファイル内のテキストをコピーし、2つ目のGitHubアカウントのNew SSH Keyに貼り付け登録する。  
また、使い分けるために`~/.ssh/configファイル`に以下を追加。  
※無い場合は、拡張子無しのconfigファイルを追加(.txtファイルとなるなら拡張子名を表示をし拡張子を消す)。  
**configファイル**

```config
Host id
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/id_rsa
  TCPKeepAlive yes
  IdentitiesOnly yes

Host futatsume
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/futatsume_rsa
  TCPKeepAlive yes
  IdentitiesOnly yes
```

GitHubと紐づけられているか確認します。successfullyが出ればOK。  

    $ ssh -T futatsume
    Hi GitHubアカウント名 You've successfully authenticated, but GitHub does not provide shell access.

しかし、何故か上記`SSH接続`のためのリモート情報登録`git@git.com:～`でpushすると以下の様なパーミッションエラーが出る。  

    ERROR: Permission to GitHubアカウント名/リポジトリ名.git denied to ローカルアカウント名.
    fatal: Could not read from remote repository.

    Please make sure you have the correct access rights
    and the repository exists.

よって、リモートリポジトリ情報の登録を以下の様に変える。

    $ git remote add origin2 futatsume:githubユーザー名/リモートリポジトリ名.git

これで`push`できると思う。`git@github.com`ではなく`config`ファイルに登録した`futatsume`という名前を使う様です。  
※ただし、これは2つめのGitHubアカウント(サブ)に対する方法であり、メインGitHubアカウントでは`git@github.com`で良い様です。  

### 2-3. ローカルリポジトリにコミットされたファイルや内容をリモートリポジトリ(GitHub)へ送る

    $ git push origin master

※リモートリポジトリの追加で名前を`origin`にしなかった場合はその名前に変えて実行します。 

**強制的にpush**  
リモートリポジトリの内容は消えても良くローカルリポジトリの内容をリモートリポジトリに反映したい場合などは以下を実行。  

    $ git push origin -f master

### 2-4. リモートリポジトリ(GitHub)の内容をローカルリポジトリへ取り込む

    $ git pull origin master

### 2-5. リモートとローカルのファイルの不一致

リモートとローカルリポジトリの不一致が発生する場合がある。  
※例えば、GitHubでリポジトリを作成する際に`Readme.txt`を作成するにチェックを入れ作成した場合ローカルには、Readme.txt
は無く不一致が発生しているためである。以下を実行すると解消されるがローカルリポジトリの内容は書き換わるので注意。  

    $ git fetch
    $ git rebase origin/master

* * *
