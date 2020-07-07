---
layout: default
title: ":octocat: Apple MacOS"
description: ":computer: MacOSでPathを通す・環境変数設定"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

カレントディレクトリに、該当のコマンドを実行する実行ファイルが無い場合にOSが該当コマンドに対する  
実行ファイルを探し実行してもらうために設定するものである。  
ようするに、コマンドプロンプトでどのディレクトリーでも該当のコマンドを実行できる様にする事が目的。  
ここでは、JavaとApache Mavenを例に挙げる。設定をしていないと以下の様なメッセージが出る。  

    $ mvn --version
    -bash: mvn: command not found

<br />

## 1. 事前準備

Adopt Open JDK 11.0.3の.pkgを展開またはインストールし`javac`の絶対Pathが以下にあるとします。  
`Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home/bin/javac`  

Apache Maven 3.6.1の.tarを展開またはインストールし、`mvn`の絶対Pathが以下にあるとします。  
`~/apache-maven-3.6.1/bin/mvn`

※この様なPathやVersionですが、それぞれご自身の環境に合わせて読み取って下さい。  

<br />

## 2. .bash_profileの作成

MacOSでは`.bash_profile`ファイルにPathや環境変数を記憶します。  
`.bash_profile`ファイルは~/に作成しますがMacインストール時には作成されていないので作成します。  
また、ターミナルで行ってみます。  
`.bash_profile`は隠しファイルですのでディレクトリー内のファイル一覧の確認は`ls -a`オプションを付ける必要があります。  

2-1. cd ~/でディレクトリーを移動  
2-2. 以下のコマンドを打ち.bash_profileファイルを作成します。  

    $ touch .bash_profile

<br />

## 3. .bash_profileに情報を記憶する

3-1. openで.bash_profileをテキストエディタで開きます。  

    $ open ~/.bash_profile

3-2. 設定情報を書き込み上書き保存します。  

    export M2_HOME=~/apache-maven-3.6.1/
    M2=$M2_HOME/bin
    export PATH=$M2:$PATH
    export PATH=$PATH:/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home/bin
    export JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home

3-3. sourceで設定を適用します  

    $ source ~/.bash_profile

<br />

## 5. 確認する

以下を実行しバージョンが出力される事を確認してください。  

    $ java -–version
    または、
    $ mvn --version

* * *
