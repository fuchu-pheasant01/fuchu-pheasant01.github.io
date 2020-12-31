---
layout: default
title: ":eyeglasses: .NET(C#、VB.NET、C++/CLI、F#)"
description: ":black_nib: .NET その他のメモ"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. システムアイコンを表示させる

1-1. ToolStripStatusLabelの先頭にシステムアイコンを表示させる  

```vb
ToolStripStatusLabel1.Image = Bitmap.FromHicon(System.Drawing.SystemIcons.Warning.Handle)
ToolStripStatusLabel1.Text = "本文"
```

1-2. PictureBoxにシステムアイコンを追加しPictureBoxのサイズにアイコンのサイズも合わせる  

```vb
PictureBox1.Image = Bitmap.FromHicon(System.Drawing.SystemIcons.Warning.Handle)
PictureBox1.SizeMode = PictureBoxSizeMode.StretchImage
```

<br />

## 2. DataGridViewの縦スクロールバーを使わずVScrollBarを使いDataGridViewをスクロールさせる

※DataGridViewでスクロールするだけの行数が足らない場合にスクロールボタンを押すとエラーとなるので考え中  
※DataGridView自体の高さによっての行数(表示できる行数)をあらかじめ確認しその行数に達していなければスクロールボタンを  
無効にすれば良いが余り良い方法ではないので考え中  

```vb
Private Sub method()

  'DataGridViewの垂直スクロールバーを非表示
  DataGridView1.ScrollBars = ScrollBars.Horizonta
  'DataGridViewのスクロールバーの代わりにVScrollBarを使用しDataGridViewの行数とスクロールバーのサイズを同期
  VScrollBar1.Maximum = DataGridView1.RowCount

End Sub

Private Sub DataGridView1_Scroll(sender As Object, e As ScrollEventArgs) Handles DataGridView1.Scroll

  VScrollBar1.Value = e.NewValue

End Sub

Private Sub VScrollBar1_Scroll(sender As Object, e As ScrollEventArgs) Handles VScrollBar1.Scroll

  DataGridView1.FirstDisplayedScrollingRowIndex = e.NewValue

End Sub
```

<br />

## 3. PrintPreviewDialogのToolStripの高さを変える

※PrintDocumentによるプレビューは自作してもできそうだが、PrintPreviewDialogのToolStripはカスタマイズできる様だ    

```vb
Private Sub method()

  Dim Pdoc As New System.Drawing.Printing.PrintDocument()
  Dim Pprev As New PrintPreviewDialog()
  'AddHandler Pdoc.PrintPage, AddressOf Pdoc_PrintPage
  
  Pprev.Document = pd
  Pprev.WindowState = FormWindowState.Maximized 'Dialogの最大化
  
  Dim Tstrip As ToolStrip = Pprev.Controls(1)
  Tstrip.Height = 60
  
  Pprev.ShowDialog()
  
End Sub
```

<br />

## 4. WPFアプリのRichTextBoxの行間を狭くする

デフォルトでは行間が広い。デフォルトで1行空ける設定になっているのかと勘違いしたが行間の方だった。  
.xamlのRitchTextBoxタグに`Block.LineHeight="1"`を追加すればよい。  

```xml
<RichTextBox Block.LineHeight="1" >
<FlowDocument>
  <Paragraph>
    <Run Text="テキストメッセージ"/>
  </Paragraph>
  </FlowDocument>
</RichTextBox>
```

* * *
