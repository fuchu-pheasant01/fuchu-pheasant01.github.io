---
layout: default
title: ":eyeglasses: Oracle Java、Apache Tomcat、Apache Maven、Spring Boot"
description: ":exclamation: Javaによる集約例外ハンドラー"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

Javaにも`try-catch`で例外をキャッチしていない例外を自動でキャッチしてくれるハンドラーがある様です。  
これを集約例外ハンドラーと呼ばれている様です。  
ただし、try-catchを強制されない非検査例外の`RuntimeException`および`その派生クラス`の例外に限る様です。  
この機能を使うことによりtry-catch文をむやみに使うことを減らせますが想定できるエラーの場合はハンドリングを  
行う事もあるので想定できないバグなどのキャッチに役立ちそうです。  

<br />

## 1. 該当のスレッドによる使用方法

該当のスレッドだけに適用したい場合は`setUncaughtExceptionHandler`メソッドで以下の様に使います。  

```java
public class Project1 implements Runnable {
  public static void main(String[] args) {
    Thread thread = new Thread(new Project1());
    thread.setUncaughtExceptionHandler(new OriginalUncaughtException());
    thread.start();
  }

  @Override
  public void run() {
    //ここを開始したスレッドのエントリーポイントとしてプログラムの内容を書いていきます。
    //これ以降のプログラムの非検査例外を自動でキャッチできます。
  }
}
```

例外をキャッチするプロシージャーは`UncaughtExceptionHandler`インターフェースの実装を行います。  

```java
import java.lang.Thread.UncaughtExceptionHandler;

public class OriginalUncaughtException implements UncaughtExceptionHandler {
  //集約例外プロシージャー
  @Override
  public void uncaughtException(Thread thread, Throwable throwable) {
    //例外の内容やトレース内容をLogに出力したい場合やユーザーに画面出力したい場合にここへ書きます。
    
    System.out.println(throwable.getClass().getName());
    throwable.printStackTrace();
    
    //スレッドを再起動する場合は以下をコメントアウトします。
    //thread.start();
  }
}
```

<br />

## 2. すべてのスレッドによる適用

Threadクラスの`setDefaultUncaughtExceptionHandler`というstaticなメソッドを使う様ですが現在作成中。  

* * *
