---
layout: default
title: ":eyeglasses: Oracle Java、Apache Tomcat、Apache Maven、Spring Boot"
description: ":black_nib: Javaによるスリープとタイマーの使い方"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. スリープ

※タイマーと違い現在のスレッドを止めるのでCPUに負担をかけない(休める事ができる)。  

### 1-1. **`java.lang.Thread.sleep()`メソッドを使う**

引数はミリ秒。  

例：2000ミリ秒スリープする  

```java
Thread.sleep(2000);
```

### 1-2. **`java.util.concurrent.TimeUnit`を使う**

TimeUnitは日単位、時単位、秒単位、ミリ単位、マイクロ単位、ナノ単位でのスリープができる。  
Thread.sleep()より起動か停止に+10ミリ程度の差が生じるみたいなのでThred.sleep()の方が  
良いかもしれない。  
また、同じ2秒を作るのに200ミリ秒を10回ループを回して作った方が精度が高いのかと思いきや、  
起動か停止にかかる時間を要するので素直に`MILLISECONDS.sleep(2000);`などとした方が良い。  

例：DAYS(日)で２日スリープする。  

```java
TimeUnit.DAYS.sleep(2);
```

例：HOURS(時)で２時間スリープする。  

```java
TimeUnit.HOURS.sleep(2);
```

例：SECONDS(秒)で2秒スリープする。  

```java
TimeUnit.SECONDS.sleep(2);
```

例：MILLISECONDS(ミリ秒)で2秒スリープする  

```java
TimeUnit.MILLISECONDS.sleep(2000);
```

例：MICROSECONDS(マイクロ秒)で2秒スリープする。  

```java
TimeUnit.MICROSECONDS.sleep(2000000);
```

例：NANOSECONDS(ナノ秒)で2秒スリープする。  

```java
TimeUnit.NANOSECONDS.sleep(2000000000);
```

<br />

## 2. タイマー

### 2-1. **`java.util.TimerクラスとTimerTask`クラスを使う**

TimerTaskクラスのrun()メソッドをオーバーライドし、そのrun()メソッドにタイマー発生時の内容を  
書く。Timerクラスのschedule()メソッドでタイマー発生時間を指定し開始するする。    

例：タイマー発生イベントを500ミリ秒後に発生させ、その後2000ミリ秒間隔で3回発生させる。  

```java
Timer timer = new Timer();

TimerTask task = new TimerTask() {
  int count = 0;
  
  @Override
  public void run() {
    System.out.println("呼び出し");
    count += 1;
    if (count >= 3)
      timer.cancel();
  }
};
timer.schedule(task, 500, 2000);
```

### 2-1. **`java.swing.Timer`クラスを使う**

<br />

## 3. 現在の日付や時刻の取得

### 3-1. **`java.util.Date`クラスを使う**

ミリ秒までしか取得できない。  

-   `java.text.SimpleDateFormat.SimpleDateFormat`クラスを使う  

```java
SimpleDateFormat simpledf = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss.SSS");
System.out.println("現在の日時は" + simpledf.format(new java.util.Date()));
```

### 3-2. **`java.util.CalendarのgetTime()`を使う**

ミリ秒までしか取得できない。  

-   `java.text.SimpleDateFormat.SimpleDateFormat`クラスを使う  

```java
SimpleDateFormat simpledf = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss.SSS");
java.util.Calendar cal = java.util.Calendar.getInstance();
System.out.println("現在の日時は" + simpledf.format(cal.getTime()));
```

### 3-3. **`java.time.LocalDateTimeクラスのnow()`メソッドを使う**

ナノ秒までの取得ができる(Macではマイクロ秒までしかできなかった)。現在はこちらが主流の様だ。  

-   `java.time.format.DateTimeFormatterクラスのofPattern()`メソッドを使う  

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss.SSSSSSSSS");
System.out.println(formatter.format(java.time.LocalDateTime.now()));
```

<br />

## 4. 経過時間を測る

### 4-1. **`System.currentTimeMillis()`メソッドを使う**

```java
long StartTime;

private void method() {
  StartTime = System.currentTimeMillis();
}

private void Stopmethod() {
  long StopTime = System.currentTimeMillis();
  
  System.out.println("スタート" + StartTime);
  System.out.println("ストップ" + StopTime);
  System.out.println("経過" + (StopTime - StartTime) + "ミリ秒");
}
```

### 4-2. **`System.nanoTime()`メソッドを使う**

```java
long StartTime;

private void method() {
  StartTime = System.nanoTime();
}

private void Stopmethod() {
  long StopTime = System.nanoTime();
  
  System.out.println("スタート" + StartTime);
  System.out.println("ストップ" + StopTime);
  System.out.println("経過" + (StopTime - StartTime) + "ナノ秒");
}
```

* * *
