---
layout: default
title: ":eyeglasses: .NET(C#、VB.NET、C++/CLI、F#)"
description: ":new: 新・デスクトップアプリ"
date: "2020/05/17"
lastmod: "2020/05/17"
---

2019/12/17日現在  

## 0. はじめに

C#、VB.NETなどでのデスクトップアプリはWinForms、WPF、UWP、Win32である。  
どれも良し悪しがありどれが良いか分からない。Win32とWinFormsは古くこれからの主流にするかは疑問。  
しかし、WinFormsしか扱えなかったりこれらですでに動いている物を置き換えるには難がある。  
WPFはそれほど普及していないのとこれからの主流になるかこれまた分からない。ただし、凝ったUIデザインを扱える。  
UWPは、現在Microsoftが力を注いでおりこれからの主流にしたい様だがまだまだWinFormsなどで使えた機能や  
ライブラリーが使えないまたは開発されていない(サードパーティ製も含めて)状態のものもある。  
さらに、セキュリティを強化している様でWinFormsなどの勢いで作成する事ができず適切な処置を施さないと使う事すら  
できなかったりする。  
また、2019/09月にMicrosoftが`.NET Core(他のOSで動作)3.0`、12月には`.NET Core3.1(LTS版)`が発表されWinForms  
とWPFを扱える様になった。  
2020年01月にはWindows7のサポートが切れるので(一部企業版では最大3年のESUサポート)Windows10などへ移行措置が取られる。  
これでUWPが主流となっていくのか？また、今更だがUWP?で開発された物を以前(Win7、8.1など)のOSで動作できるように　　
`xaml island`なんてのもある様だ。  
一方、JavaではJava11でJavaFxが同梱されなくなり別途追加する必要がある。古いSwingなどを使うかどうかという事もあり  
Javaでデスクトップアプリを作りにくくなった。さらに、Delphiなどもあるがどう扱って良いか分からない事が多い。  
条件などもあるが基本的にはデスクトップアプリでの開発をする場合はどれが主流になっていくのか？
`.NET Core`のデザイナーがGUIで作成できないなど不完全な事もありこれからの注目となりそうだ。  

<br />

## 1. UWP(Universal Windows Platform)

**1-0. はじめに**  
WinFormsからするとかなり変っている。まずデフォルトではデザイナーが出てこない。  
LabelやDataGridViewがなくそれ相当のコントロールはどれか？  
System.Windows.FormsではないのでMessageBoxクラスがないのでダイアログすら出し方が分からない。  
画面遷移のやり方特に呼び出し先画面から呼び出し側画面への戻り方が分からない。  
そもそも画面が重なっているだけなのか呼び出し側画面は呼び出し先画面が表示されている時は動いているのか？  
モーダルとモードレスの使い方は？それぞれの画面のサイズは変更できるのか？  
タイトルの文字を変更するのも分からない。取り合えず思いついただけでもこれだけあるのでこれはメモをする必要がある。  

**1-1. 環境構築**  

**1-2. コントロール・クラス比較**  
|No|WinForms|UWP|  
\|--\|:-------\|:----\|  
|1|Label|TextBlock|  
|2|DataGridView|GridView|  
|3|MessageBox|MessageDialog<br />ContentDialog|  

**1-3. 画面遷移**  

```csharp
Form2.Show(); //WinForms
Frame.Navigate(typeof(BlankPage1)); //UWP
```

```vb
Form2.Show() 'WinForms
Frame.Navigate(GetType(BlankPage1)) 'UWP
```

**1-4. 変った事**  
コントロールを配置すると自動でコントロール名が付けられていたが自分でコントロール名を  
付けなければいけない。.xamlウィンドウorプロパティウィンドウで値を入力する。    

**1-5. 分からない事**  
・GridViewの表示  
・SQLServerのWindows認証接続  

<br />

## 2. WPF(Windows Presentation Foundation)

**2-0. はじめに**  
.xamlファイルでのデザインとなっている事や凝ったデザインができるという事だがいまいちその  
便利さが分からない。少し遅いとの情報もある。  
FormベースからxamlベースでのデザインとなったがUWPよりは手軽に使える。  

**2-1. 環境構築**  
`.NET Framework3.0`以降を扱えるVisual Studioであれば標準で入っている。  

**2-2. コントロール・クラス比較**  
|Num|WinForms|WPF|none|  
\|-----:\|:--------:\|:------------\|---:\|  
|1|System.Windows.Forms.Label|System.Windows.Controls.Label<br />System.Windows.Controls.TextBlock||  
|2|System.Windows.Forms.DataGridView|System.Windows.Controls.DataGrid||  
|3|System.Windows.Forms.MessageBox|System.Windows.MessageBox||  
|4|System.Windows.Forms.MenuStrip|System.Windows.Controls.Menu||  
|5|System.Windows.Forms.RichTextBox|System.Windows.Controls.RichTextBox||  
|6|System.Windows.Forms.Timer|System.Windows.Threading.DispatcherTimer||

**2-3. イベントプロシージャー比較**  
|No|WinForms|WPF|  
\|--:\|:-------\|:----\|  
|1|Form1_Load|MainWindow1_Loaded|  

**2-4. コントロールメソッド比較**  
|No|用途|WinForms|WPF|  
\|--:\|:--\|:-------\|:----\|  
|1|ウィンドウタイトルバーテキスト|Me.Text|Me.Title|  
|2|ラベルテキスト|Label1.Text|Label1.Context|  

**2-5. 変った事**  
コントロールを配置すると自動でコントロール名が付けられていたが自分でコントロール名を  
付けなければいけない。.xamlウィンドウorプロパティウィンドウで値を入力する。  
Gridという画面を分割してデザインする様だがいまいち使い方が分からない。

・ウィンドウの最大化、最小化ボタンの表示／非表示の仕方が少し変わり、最小化ボタンのみを非表示にする事が  
WPFの機能だけではできなくなった様だ。  
WPFはResizeMode = ResizeMode.CanMinimizeで最大化ボタンのみ無効(VisibleではなくEnabled相当)、または  
ResizeMode = ResizeMode.NoResizeで両方非表示はできる。  
WinFormsではMinimizeBoxにfalseを指定すればよかったのだが不便になった。  
どうしても最小化のみ非表示をしたければWin32レベルでの変更が必要になる。  

**2-6. 分からない事**  
・WinFormsのPanelコントロールの代替が何か分からないBorderコントロールは複数の子コントロールを持てない。  
・UIメッセージキューの処理はWinFormsでApplication.DoEvents()を使用出来たが(推奨しないが)、WPFでは  
DispatcherFrameやBack Worker Threadを使用する必要があるとの事だがまだ良くわからない。  

**2-7. 変らず使える事**
タイマー、スリープ関連  
System.Timers.Timer、System.Threading.Timer、System.Threading.Thread.Sleep、System.Diagnostics.Stopwatch  

* * *
