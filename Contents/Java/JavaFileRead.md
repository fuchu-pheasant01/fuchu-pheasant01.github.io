---
layout: default
title: ":eyeglasses: Oracle Java、Apache Tomcat、Apache Maven、Spring Boot"
description: ":black_nib: Javaのクラスによるファイル読み書き計測"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

Javaにはファイルの読み書き(特にテキスト)には様々なクラスが用意されておりバッファなどを中継してとなると  
多いのでどれをどう使ってよいのか迷うのでここにまとめる事とする。  
ファイルは例としてコマンドプロンプトで400MByteのファイルを作成してテストする。  

    $ fsutil file createnew C:\Folder\Test.txt 400000000

タイムは、`System.currentTimeMillis()`を使う事とする。  
読み込みは、ディスクトップアプリのラベルなどへ内容を代入する際に時間がかかるので含めず`System.out.println()`  
でのコンソールで表示をしなるべくファイルの読み込みのみに時間を絞って行う事とする。  
計測はアプリケーションは終了させずそのまま5回実行する。その後、アプリケーションを1度終了し実行し直す。  
これを3回実行する。  
パソコンのハードの部分に影響するかもしれないので参考まで  
OS:Windows10 64bit、CPU 3.40GHz 4コア8スレッド、メモリー8G  

<br />

## 1. FileInputStream→InputStreamReaderでテキスト全件読み取り

### 1-1. 一度に読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var isr = new InputStreamReader(fis, Charset.forName("utf-8"))) {
    var sb = new StringBuilder();
    int len;
    var size = fis.available();
    var cr = new char[size];
    while ((len = isr.read(cr)) != -1) { //1度で読めると思うが念のため最終かチェック
      sb.append(cr, 0, len);
    } 
} catch (IOException e) {
    e.printStackTrace();
}

alltext = sb.toString();
long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |  1回目 |  2回目 |  3回目 |  4回目 |  5回目 |
| --: | ---: | ---: | ---: | ---: | ---: |
|   1 | 1592 | 1102 | 1036 | 1034 | 1036 |
|   2 | 1429 | 1136 | 1031 | 1056 | 1029 |
|   3 | 1433 | 1085 | 1046 | 1034 | 1036 |

### 1-2. 1文字づつ読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var isr = new InputStreamReader(fis, Charset.forName("utf-8"))) {
    var sb = new StringBuilder();
    int len;
    while ((len = isr.read()) != -1) {
      sb.append((char)len);
    } 
} catch (IOException e) {
    e.printStackTrace();
}

alltext = sb.toString();
long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |  1回目 |   2回目 |  3回目 |  4回目 |  5回目 |
| --: | ---: | ----: | ---: | ---: | ---: |
|   1 | 8657 | 14811 | 7773 | 7937 | 7761 |
|   2 | 8500 |  8296 | 8906 | 8369 | 8341 |
|   3 | 7452 |  8296 | 8430 | 8172 | 8174 |

<br />

## 2. FileInputStream→BufferedInputStream→InputStreamReaderでテキスト全件読み取り

### 2-1. 一度に読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var isr = new InputStreamReader(new BufferedInputStream(fis), Charset.forName("UTF8"))) {
    var sb = new StringBuilder();
    int len;
    var size = fis.available();
    var cr = new char[size];
    while ((len = isr.read(cr)) != -1) { //1度で読めると思うが念のため最終かチェック
      sb.append(cr, 0, len);
    } 
} catch (IOException e) {
    e.printStackTrace();
}

alltext = sb.toString();
long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |  1回目 |  2回目 |  3回目 |  4回目 |  5回目 |
| --: | ---: | ---: | ---: | ---: | ---: |
|   1 | 1406 | 1034 | 1012 | 1034 |  997 |
|   2 | 1437 | 1057 | 1020 | 1030 | 1011 |
|   3 | 1438 | 1074 | 1044 | 1042 | 1044 |

### 2-2. 1文字づつ読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var isr = new InputStreamReader(new BufferedInputStream(fis), Charset.forName("UTF8"))) {
    var sb = new StringBuilder();
    int len;
    while ((len = isr.read()) != -1) {
      sb.append((char)len);
    } 
} catch (IOException e) {
    e.printStackTrace();
}

alltext = sb.toString();
long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |   1回目 |   2回目 |  3回目 |  4回目 |  5回目 |
| --: | ----: | ----: | ---: | ---: | ---: |
|   1 | 14217 | 14474 | 7643 | 7483 | 7437 |
|   2 |  8383 | 14042 | 7859 | 7833 | 7831 |
|   3 |  8501 | 13912 | 8322 | 8105 | 8090 |

<br />

## 3. FileInputStream→BufferedInputStreamでテキスト全件読み取り

### 3-1. 一度に読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var br = new BufferedInputStream(fis)) {
    var size = fis.available();
    var by = new byte[size];
    br.read(by);
    alltext = new String(by, Charset.forName("UTF-8"));
} catch (IOException e) {
    e.printStackTrace();
}

long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 | 1回目 | 2回目 | 3回目 | 4回目 | 5回目 |
| --: | --: | --: | --: | --: | --: |
|   1 | 781 | 749 | 617 | 578 | 602 |
|   2 | 766 | 735 | 603 | 596 | 608 |
|   3 | 766 | 749 | 689 | 599 | 594 |

### 3-2. 1文字づつ読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var br = new BufferedInputStream(fis)) {
    var sb = new StringBuilder();
    int ic;
    while ((ic = br.read()) != -1) {
      sb.append((char)ic);
    }
    alltext = sb.toString();
} catch (IOException e) {
    e.printStackTrace();
}

long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |  1回目 |  2回目 |  3回目 |  4回目 |  5回目 |
| --: | ---: | ---: | ---: | ---: | ---: |
|   1 | 2625 | 3046 | 3482 | 3585 | 3472 |
|   2 | 2632 | 2875 | 2437 | 2430 | 2377 |
|   3 | 2594 | 2291 | 2354 | 2891 | 2387 |

<br />

## 4. FileInputStream→InputStreamReader→BufferedReaderでテキスト全件読み取り

### 4-1. 一度に読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var br = new BufferedReader(new InputStreamReader(fis, Charset.forName("UTF8")))) {
    var size = fis.available();
    var cr = new char[size];
    int len;
    var sb = new StringBuilder();
    while ((len = br.read(cr)) != -1) {
      sb.append(cr, 0, len);
    }
} catch (IOException e) {
    e.printStackTrace();
}

alltext = sb.toString();
long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |  1回目 |  2回目 |  3回目 |  4回目 |  5回目 |
| --: | ---: | ---: | ---: | ---: | ---: |
|   1 | 1445 | 1047 | 1007 | 1042 | 1000 |
|   2 | 1367 | 1025 |  984 |  999 | 1016 |
|   3 | 1468 | 1047 | 1009 | 1025 | 1000 |

### 4-2. 1文字づつ読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try (var fis = new FileInputStream("C:\\Folder\\Test.txt");
    var br = new BufferedReader(new InputStreamReader(fis, Charset.forName("UTF8")))) {
    var size = fis.available();
    var cr = new char[size];
    int len;
    var sb = new StringBuilder();
    while ((len = br.read()) != -1) {
      sb.append((char)len);
    }
} catch (IOException e) {
    e.printStackTrace();
}

alltext = sb.toString();
long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 |  1回目 |  2回目 |  3回目 |  4回目 |  5回目 |
| --: | ---: | ---: | ---: | ---: | ---: |
|   1 | 8992 | 2924 | 2791 | 2809 | 2872 |
|   2 | 3280 | 2987 | 2786 | 2776 | 2777 |
|   3 | 3280 | 2971 | 2827 | 2776 | 2788 |

<br />

## 5. FilesクラスのreadStringメソッドでテキスト全件読み取り(Java11以降)

### 5-1. 一度に読み取りString型に代入する

```java
long start = System.currentTimeMillis();
String alltext = null;
		
try {
    Path path = Paths.get("C:\\Folder\\Test.txt");
    alltext = Files.readString(path, Charset.forName("UTF8"));
} catch (IOException e) {
    e.printStackTrace();
}

long stop = System.currentTimeMillis();
System.out.println(String.valueOf(stop - start));
```

**計測(ミリ秒)**  

|  起動 | 1回目 | 2回目 | 3回目 | 4回目 | 5回目 |
| --: | --: | --: | --: | --: | --: |
|   1 | 609 | 415 | 431 | 377 | 375 |
|   2 | 641 | 435 | 385 | 370 | 375 |
|   3 | 609 | 466 | 421 | 375 | 391 |

<br />

### まとめ

Java11以降であれば、`java.nio.file.Files`クラスの`readString()`メソッドで読み取るのが最適の様だ。  
続いて、FileInputStream→BufferedInputStreamで一度にまとめて読み取るのも中々の様だ。  
どれを使っても傾向としては、アプリを起動し2度目、3度目以降の読み取りが内部でどうなているのか、キャッシュみたいな  
何かがあるのか分からないが早くなる様だ。  
また、1文字や今回試していないが行単位で読み込むよりは1度にまとめて読み込む方が断然早い様だ。  
まとめて読み込んだ上で行単位なり分割する方が良いと思われる。  

* * *
