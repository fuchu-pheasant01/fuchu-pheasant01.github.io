---
layout: default
title: ":eyeglasses: .NET(C#、VB.NET、C++/CLI、F#)"
description: ":black_nib: .NETにおけるタイマーとスリープの使い方"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

.NETのタイマーは、Windowsフォームアプリ(WindowsForms)とWPFアプリとユニバーサルWindowsアプリ(UWP)  
それぞれでしか使えない物もあれば(.dllを参照すれば使えなくはないかも)、共通で使えるタイマーもある。  

<br />

## 1. スリープ

※タイマーと違い現在のスレッドを止めるのでCPUに負担をかけない(休める事ができる)。  

### **1-1. `System.Threading.Thread.Sleep()メソッドを使う`**

UIスレッドで使用した場合はUIスレッドを止めるためその間はUIのアクセスは出来ない。  
長時間止める場合は別スレッドを設ける必要があると思う。  

例：2000ミリ秒スリープする  

```csharp
System.Threading.Thread.Sleep(2000);
```

```vb
System.Threading.Thread.Sleep(2000)
```

### **1-2. `System.Threading.Tasks.Task.Delay()メソッドを使う`**

使用するメソッドにはasync(Async)修飾子が必要。使用する箇所にはawait(Await)が必要。  
(無くても動くがこれ以降のプログラムも実行されスレッドのスリープという目的ではなくなるため。)  
非同期で別スレッドで実行されるためUIスレッドから実行した場合UIのアクセスはできる。  
UIのアクセスはできるため、await以降の処理がある場合影響がないようにする必要がある。  

例：2000ミリ秒スリープする  

```csharp
private async void method()
{
  await System.Threading.Tasks.Task.Delay(2000);
}
```

```vb
Private Async Sub method()

  Await System.Threading.Tasks.Task.Delay(2000)
  
End Sub
```

<br />

## 2. タイマー

### **2-1. `System.Threading.Timerクラスを使う`**

-   「共通タイマー」   

Threading.Timerのイベントハンドラーは別スレッドで実行されるので直接UIスレッドのコントロールにアクセスできない。  
また、スレッドプールで実行される。  

```vb
Private ThreadingTmr As System.Threading.Timer

Private Sub Startmethod()

  '別スレッドで動くタイマーイベントを作成。インターバルは1000ミリ秒で発生。
  Dim ThreadingCallback As System.Threading.TimerCallback = Sub(state)
                                                              Dim o As Object
                                                              ThreadingTimer(o)
                                                            End Sub
  ThreadingTmr = New Threading.Timer(ThreadingCallback, Nothing, 0, 1000)
  
End Sub

Private Sub ThreadingTimer(sender As Object)

  '1000ミリ毎にこのメソッドが実行される。別スレッドなのでUIアクセスにはInvokeなどでアクセス。
  'WPF用のコードなのでWindowsFormsやUWPではそれなりのコードで書く。
  Application.Current.Dispatcher.Invoke(
    Sub()
      Label1.Content = "現在時刻 ：" & Format(DateTime.Now, "HH:mm:ss")
    End Sub)

End Sub

Private Sub Stopmethod()

  'タイマーを止める
  ThreadingTmr.Change(Threading.Timeout.Infinite, Threading.Timeout.Infinite)

End Sub
```

### **2-2. `System.Timers.Timerクラスを使う`**

-   「共通タイマー」  

Timers.Timerのイベントハンドラーは別スレッドで実行されるので直接UIスレッドのコントロールにアクセスできない。  
また、スレッドプールで実行される。  

```vb
Private WithEvents TimersTmr As New System.Timers.Timer()

Private Sub Startmethod()

  '別スレッドで動くタイマーイベントを作成。インターバルは1000ミリ秒で発生。
  AddHandler TimersTmr.Elapsed, New Timers.ElapsedEventHandler(AddressOf TimersTimer_Tick)
  TimersTmr.Interval = 1000
  TimersTmr.Start()

End Sub

Private Sub TimersTimer_Tick(sender As Object, e As Timers.ElapsedEventArgs) Handles TimersTmr.Elapsed

  '1000ミリ毎にこのメソッドが実行される。別スレッドなのでUIアクセスにはInvokeなどでアクセス。
  'WPF用のコードなのでWindowsFormsやUWPではそれなりのコードで書く。
  Application.Current.Dispatcher.Invoke(
    Sub()
      Label1.Content = "現在時刻 ：" & Format(DateTime.Now, "HH:mm:ss")
    End Sub)

End Sub

Private Sub Stopmethod()

  'タイマーを止める
  TimersTmr.Stop()

End Sub
```

### **2-3. `System.Windows.Forms.Timerクラスを使う`**

-   「WindowsForms限定」  

```vb
Private WithEvents FormsTmr As New System.Windows.Forms.Timer()

Private Sub Startmethod()

  '呼び出しスレッドで動くタイマーイベントを作成。インターバルは1000ミリ秒で発生。
  AddHandler FormsTmr.Tick, New EventHandler(AddressOf FormsTimer_Tick)
  FormsTmr.Interval = 1000
  FormsTmr.Start()

End Sub

Private Sub FormsTimer_Tick(sender As Object, e As EventArgs) Handles FormsTmr.Tick

  'UIスレッドで動作させる場合、このイベントを実行している間は他のイベントハンドラーは受け付けない。
  Label1.Text = "現在時刻 ：" & Format(DateTime.Now, "HH:mm:ss")

End Sub

Private Sub Stopmethod()

  'タイマーを止める
  FormsTmr.Stop()

End Sub
```

### **2-4. `System.Windows.Threading.DispatcherTimerクラスを使う`**

-   「WPF限定」  

```vb
Private WithEvents DispaTmr As New System.Windows.Threading.DispatcherTimer()

Private Sub Startmethod()

  '呼び出しスレッドで動くタイマーイベントを作成。インターバルは1000ミリ秒で発生。
  AddHandler DispaTmr.Tick, New EventHandler(AddressOf DispatcherTimer_Tick)
  DispaTmr.Interval = New TimeSpan(0, 0, 0, 1, 0)
  DispaTmr.Start()
  
End Sub

Private Sub DispatcherTimer_Tick(sender As Object, e As EventArgs) Handles DispaTmr.Tick

  'UIスレッドで動作させる場合、このイベントを実行している間は他のイベントハンドラーは受け付けない。
  Label1.Content = "現在時刻 ：" & Format(DateTime.Now, "HH:mm:ss")
  'CommandManagerにRequerySuggestedイベントを発生させる
  CommandManager.InvalidateRequerySuggested()

End Sub

Private Sub Stopmethod()

  'タイマーを止める
  DispaTmr.Stop()
  
End Sub
```

<br />

## 3. 現在の日付や時刻を取得

### **3-1. `System.DateTimeクラスのNowプロパティを使う`**

日時とミリ秒までの取得が可能。その他、午前午後や曜日の取得が可能。  

-   `Microsoft.VisualBasic.Strings.Format()`メソッドを使っての表示例  
    ※時間は24時間表記例  

```vb
System.Console.WriteLine(System.DateTime.Now, "yyyy/MM/dd HH:mm:ss.fff")
```

-   DateTimeクラスのメソッドとプロパティで文字列連結を行って表示する例  

```vb
Dim GetNow As DateTime = System.DateTime.Now

System.Console.WriteLine(GetNow.ToShortDateString() & " " & GetNow.ToLongTimeString() & "." & GetNow.Millisecond.ToString("D3"))
```

-   DateTimeクラスのプロパティで文字列連結を行って表示する例  

```vb
Dim GetNow As DateTime = System.DateTime.Now
Dim NowStr As String = GetNow.Year & "/" & GetNow.Month.ToString("D2") & "/" & GetNow.Day.ToString("D2") & " "
NowStr &= GetNow.Hour.ToString("D2") & ":" & GetNow.Minute.ToString("D2") & ":" & GetNow.Second.ToString("D2")
NowStr &= "." & GetNow.Millisecond.ToString("D3")

System.Console.WriteLine(NowStr)
```

<br />

## 4. 経過時間を図る

いずれも、WinAPIでクエリーパフォーマンスカウンタが使用できるかによるが百ナノ秒レベルまで対応している。  
しかし、WindowsOS上などでは十ミリ以下の精度は期待できないかも。  

### **4-1. `System.Diagnostics.StopWatchクラスを使う`**

WinAPIでクエリーパフォーマンスカウンタが利用できればWinAPIのQueryPerformanceCounter()関数などが使われ、  
利用できなければシステムタイマーが呼ばれる。  
IsHighResolutionプロパティでどちらの方法を呼び出して使われるかも確認できる。  
Frequencyプロパティで周波数(カウント/秒)が確認できる(WinAPIのQueryPerformanceFrequency()関数を呼ぶ)。  

```vb
Private Swatch As New System.Diagnostics.StopWatch()

Private Sub method()

  Swatch.Reset()
  Swatch.Start()

End Sub

Private Sub Stopmethod()

  Dim Tspan As New TimeSpan()
  
  Swatch.Stop()
  Tspan = Swatch.Elapsed
  System.Console.WriteLine(Tspan.ToString("hh':'mm':'ss'.'fffffff"))

End Sub
```

### **4-2. `System.DateTimeクラスのNowプロパティの差分で行う`**

※TimeSpan.ToString()メソッドのフォーマットを使う場合の文字列はシングルクォートで囲むか、  
エスケープ文字(バックスラッシュ、エンマーク)が文字の前に必要。  
また、時表示文字は小文字のhhでなければならない。  
日付については差が1日置かないと付かなかったはず？(`1.hh:mm:ss`など)。

```vb
Private StartTime As New System.DateTime()

Private Sub method()

  StartTime = System.DateTime.Now
  
End Sub

Private Sub Stopmethod()

  Dim Tspan As New TimeSpan()
  
  Tspan = System.DateTime.Now.Subtract(StartTime)
  'Tspan = System.DateTime.Now - StartTime
  System.Console.WriteLine(Tspan.ToString("hh':'mm':'ss'.'fffffff"))

End Sub
```

### **4-3. `WinAPIのQueryPerformanceCounterを使う`**

QueryPerformanceCounter()の値は、`時間間隔の測定に使用できる高解像度（<1us）のタイムスタンプ`という事の様だ。  
高分解能パフォーマンスカウンタをハードウェアでサポートしていなければ、QueryPerformanceCounter()関数や  
QueryPerformanceFrequency()関数はFalse(0)を返す。  
しかし、MicrosoftによるとWindowsXP以降を実行するシステムでは発生しない様だ。  

```cpp
BOOL QueryPerformanceCounter(
  LARGE_INTEGER *lpPerformanceCount
);

BOOL QueryPerformanceFrequency(
  LARGE_INTEGER *lpFrequency
);
```

```vb
<System.Runtime.InteropServices.DllImport("kernel32.dll")>
Private Shared Function QueryPerformanceCounter(ByRef lpPerformanceCount As Long) As Boolean
End Function

<System.Runtime.InteropServices.DllImport("kernel32.dll")>
Private Shared Function QueryPerformanceFrequency(ByRef lpFrequency As Long) As Boolean
End Function

'API定義は以下でも良い
'Private Declare Function QueryPerformanceCounter Lib "kernel32" (ByRef lpPerformanceCount As Long) As Boolean
'Private Declare Function QueryPerformanceFrequency Lib "kernel32" (ByRef lpFrequency As Long) As Boolean

Private StartCount As Long

Private Sub method()

  'パフォーマンスカウンタの現在値を取得しこれを開始値とする
  If QueryPerformanceCounter(StartCount) Then
    System.Console.WriteLine("開始値：" & StartCount)
  Else
    '高分解能パフォーマンスカウンタをサポートしていない場合
    System.Console.WriteLine("QueryPerformanceCounter()APIはサポートされていません。")
    Exit Sub
  End If

End Sub

Private Sub Stopmethod()

  Dim StopCount As Long

  'パフォーマンスカウンタの現在値を取得しこれを停止値とする
  QueryPerformanceCounter(StopCount)
  System.Console.WriteLine("停止値：" & StopCount)
  
  '周波数(カウント/秒)を取得する 
  Dim Frequency As Long
  QueryPerformanceFrequency(Frequency)

  '差分を周波数で割り秒計算する 
  Dim Byo As Double = CDbl((StopCount - StartCount) / Frequency)
  System.Console.WriteLine(Byo & "秒")

  'オリジナルフォーマットで表示を行おうとしたが以下ではマイクロ秒以下が0となるので考え中
  Dim Tspan As TimeSpan = TimeSpan.FromSeconds(Byo)
  System.Console.WriteLine(Tspan.ToString("hh':'mm':'ss'.'fffffff"))
  
End Sub
```

```csharp
[System.Runtime.InteropServices.DllImport("kernel32.dll")]
static extern bool QueryPerformanceCounter(out long lpPerformanceCount);

[System.Runtime.InteropServices.DllImport("kernel32.dll")]
static extern bool QueryPerformanceFrequency(out long lpFrequency);

long StartCount;

private void method()
{
  //パフォーマンスカウンタの現在値を取得しこれを開始値とする
  if (QueryPerformanceCounter(out StartCount))
  {
    System.Console.WriteLine("開始値：" & StartCount);
  }
  else
  {
    //高分解能パフォーマンスカウンタをサポートしていない場合
    System.Console.WriteLine("QueryPerformanceCounter()APIはサポートされていません。");
    return;
  }
}

private void Stopmethod()
{
  long StopCount;
  
  //パフォーマンスカウンタの現在値を取得しこれを停止値とする
  QueryPerformanceCounter(out StopCount);
  System.Console.WriteLine("停止値：" & StopCount);
  
  //周波数(カウント/秒)を取得する 
  long Frequency;
  QueryPerformanceFrequency(out Frequency);

  //差分を周波数で割り秒計算する 
  double Byo = (double)((StopCount - StartCount) / Frequency);
  System.Console.WriteLine(Byo & "秒");
  
  //オリジナルフォーマットで表示を行おうとしたが以下ではマイクロ秒以下が0となるので考え中
  Dim Tspan As TimeSpan = TimeSpan.FromSeconds(Byo);
  System.Console.WriteLine(Tspan.ToString("hh':'mm':'ss'.'fffffff"));
}
```

* * *
