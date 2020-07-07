---
layout: default
title: ":eyeglasses: Oracle Java、Apache Tomcat、Apache Maven、Spring Boot"
description: ":question: Swing、Awt関連での実行時クラッシュについて"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

私の環境のEclipseやAdoptOpenJDKを使用している関連もあるかもしれませんが、SwingやAwtでのGUIアプリケーション実行時に  
特定のバージョンではクラッシュが発生します。  
Java11では、11.0.6、11.0.7がそれに該当します。13.0.1などでも同じ現象が起きる様です。  

    #
    # A fatal error has been detected by the Java Runtime Environment:
    #
    #  EXCEPTION_ACCESS_VIOLATION (0xc0000005) at pc=0x00007fffdd24f9d6, pid=8308, tid=3068
    #
    # JRE version: OpenJDK Runtime Environment (11.0.7+10) (build 11.0.7+10)
    # Java VM: OpenJDK 64-Bit Server VM (11.0.7+10, mixed mode, tiered, compressed oops, g1 gc, windows-amd64)
    # Problematic frame:
    # C  [awt.dll+0x8f9d6]
    #
    # No core dump will be written. Minidumps are not enabled by default on client versions of Windows
    #
    # If you would like to submit a bug report, please visit:
    #   https://github.com/AdoptOpenJDK/openjdk-support/issues
    # The crash happened outside the Java Virtual Machine in native code.
    # See problematic frame for where to report the bug.
    #

    ---------------  S U M M A R Y ------------

    Command Line: -Dfile.encoding=UTF-8 <エントリーポイントクラス>

    Host: xxxxx
    Time: Fri Apr 17 16:59:12 2020 ???? (?W???) elapsed time: 0 seconds (0d 0h 0m 0s)

    ---------------  T H R E A D  ---------------

    Current thread (0x000001f321717000):  JavaThread "Thread-0" [_thread_in_native, id=3068, stack(0x0000002b45100000,0x0000002b45200000)]

    Stack: [0x0000002b45100000,0x0000002b45200000],  sp=0x0000002b451fe960,  free space=1018k
    Native frames: (J=compiled Java code, j=interpreted, Vv=VM code, C=native code)
    C  [awt.dll+0x8f9d6]

    Java frames: (J=compiled Java code, j=interpreted, Vv=VM code)
    j  sun.awt.windows.WComponentPeer._setFont(Ljava/awt/Font;)V+0 java.desktop@11.0.7
    j  sun.awt.windows.WComponentPeer.setFont(Ljava/awt/Font;)V+7 java.desktop@11.0.7
    j  sun.awt.windows.WWindowPeer.initialize()V+42 java.desktop@11.0.7
    j  sun.awt.windows.WFramePeer.initialize()V+1 java.desktop@11.0.7
    j  sun.awt.windows.WComponentPeer.<init>(Ljava/awt/Component;)V+83 java.desktop@11.0.7
    j  sun.awt.windows.WCanvasPeer.<init>(Ljava/awt/Component;)V+2 java.desktop@11.0.7
    j  sun.awt.windows.WPanelPeer.<init>(Ljava/awt/Component;)V+2 java.desktop@11.0.7
    j  sun.awt.windows.WWindowPeer.<init>(Ljava/awt/Window;)V+2 java.desktop@11.0.7
    j  sun.awt.windows.WFramePeer.<init>(Ljava/awt/Frame;)V+2 java.desktop@11.0.7
    j  sun.awt.windows.WToolkit.createFrame(Ljava/awt/Frame;)Ljava/awt/peer/FramePeer;+5 java.desktop@11.0.7
    j  java.awt.Frame.addNotify()V+20 java.desktop@11.0.7
    j  java.awt.Window.show()V+8 java.desktop@11.0.7
    j  java.awt.Component.show(Z)V+5 java.desktop@11.0.7
    j  java.awt.Component.setVisible(Z)V+2 java.desktop@11.0.7
    j  java.awt.Window.setVisible(Z)V+2 java.desktop@11.0.7
    j  desk.sample.HttpRestClient11.run()V+46
    j  java.lang.Thread.run()V+11 java.base@11.0.7
    v  ~StubRoutines::call_stub

    siginfo: EXCEPTION_ACCESS_VIOLATION (0xc0000005), reading address 0x0000000000000058

<br />

## 1. 原因や傾向

エラーログの`awt.dll`やネットでの検索などで調べた結果では、SwingやAwtで日本語文字を扱う際に発生する問題の様です。  
また、Swingでは`JFrame.setVisible(true);`でGUIのウィンドウを表示する直前まではクラッシュせずこれを実行するとクラッシュ  
が発生する様です。  
原因はバグと言われている様ですが、Javaの実行時の引数に`-Dfile.encoding=UTF-8 クラス名`をデフォルトで指定しているのですが  
このエンコードの`UTF-8`や`EUC-JP`でクラッシュさせてしまう様です。  
※参考：`UTF-8`(awt.dll+0x8fa16)、`EUC-JP`(awt.dll+0x8f9d6)  
また、SwingやAwtのクラスを使わないプロジェクト(Webアプリ系など)では当然ながらUTF-8でもクラッシュしません。 
ネットの記事ではバグと言われていたり11.0.8でそれが解決される様な事も書かれていたりします。  

<br />

## 2. 解決方法

1.  LTS版などの関係もあり難しいのですが、Webアプリ系などでは11.0.7などのLTS版、SwingやAwtのGUIアプリの場合は11.0.5および  
    11.0.8(もう少し先のリリース)で直っていればこれらを使う。  
2.  SwingやAwtアプリの場合のみJavaの実行時の引数で`-Dfile.encoding=UTF-8 クラス名`、`-Dfile.encoding=EUC-JP クラス名`  
    以外の文字コードを指定し使う。  

| JDKバージョン | OK文字コード                                                                   | NG文字コード      |
| :------- | :------------------------------------------------------------------------ | :----------- |
| 11.0.5   | UTF-8、EUC-JP、ISO-8859-1、US-ASCII、UTF-16、UTF-16BE、UTF-16LE、Shift_JIS、MS932 | --           |
| 11.0.6   | ISO-8859-1、US-ASCII、UTF-16、UTF-16BE、UTF-16LE、Shift_JIS、MS932              | UTF-8、EUC-JP |
| 11.0.7   | ISO-8859-1、US-ASCII、UTF-16、UTF-16BE、UTF-16LE、Shift_JIS、MS932              | UTF-8、EUC-JP |
| 11.0.8   | 未定                                                                        | 未定           |
| 13.0.1   | ISO-8859-1、US-ASCII、UTF-16、UTF-16BE、UTF-16LE、Shift_JIS、MS932              | UTF-8、EUC-JP |
| 14.0.1   | ISO-8859-1、US-ASCII、UTF-16、UTF-16BE、UTF-16LE、Shift_JIS、MS932              | UTF-8、EUC-JP |

※ちなみに、awt.dllはbinフォルダ内にありますがクラッシュが発生しない11.0.5のawt.dllのみをコピーして入れ替えを行いましたが  
ダメでした。  
よって、awt.dllのみ関連しているわけではなさそうです。  

**根本的な原因やこれがバグなのかどうかは疑問が残ります。次回のバージョンで解消されるでしょうか？**  
※2020/04/21：14.0.1では解決されていませんでした。

* * *
