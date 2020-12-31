---
layout: default
title: ":newspaper: Microsoft .NET、Visual Studio"
description: ":paperclip: Microsoft .NET、Visual Studioライセンス"
date: "2020/10/10"
lastmod: "2020/10/11"
---

## 1. .NET Framework、.NET Coreのライセンスについて

Microsoft Visual Studioにより作成したプログラム(ソース)は、作成者に著作権がありマイクロソフトに著作権は無い。  
よって、DLLも作成者に著作権がありその作成者のライセンスに従わなければならない。  
さらに、.NETの標準クラスライブラリーはマイクロソフトに著作権がある事になりそのライセンスに従うことになる。  
そのライセンスによると.NET標準クラスライブラリーの一部や単一のファイルだけをアプリケーションに付属し配布する事はできない様になっている。  
.NET Framework再配布可能パッケージなどのまとまった単位で配布(インストール)しなければならない。  
問題は、.NET標準ライブラリー以外の外部ライブラリー(DLLなども)を扱う場合そのメーカーなどのライセンスに従う必要がありますが使用するための条件  
として派生物のソースコードを開示しなければならない場合です。オープンソースソフトウェアのGPLやLGPLなどがこれに当たり.NET Frameworkのソース  
コードも開示する事となり.NET Framework(.NET Coreでは無い)においてはソースコードは公開されておらずその外部ライブラリーを使う事はできません。  
外部ライブラリー使用するとライセンス違反になりますし、逆に.NET Frameworkのソースコードが分かった所で公開を許可されていなければMicrosoftの  
ライセンス違反となります。  

.NET Coreは、オープンソースソフトウェアでありMITライセンス、Apacheライセンス2.0に従うよう指定されている。  

また、Microsoft Visual Studioなどでコンパイルしたデスクトップアプリケーションは、ただのバイナリファイルであるためマイクロソフトに著作権は無い。  

<br />

## 2. Microsoft Visual Studioのライセンスについて

### 2-1. Microsoft Visual Studio Express for Windows Desktopのライセンス

Microsoft Visual Studio Express Editonは、機能が制限されている事条件に無料で商用利用も可能な製品です。  
配布は可能ですが、第三者が使用する目的での配布は禁止されています。  

[Microsoft Visual Studio 2017 Express for Windows Desktopライセンス条項](https://visualstudio.microsoft.com/ja/license-terms/mlt080317/)  

### 2-2. Microsoft Visual Studio Community Editionのライセンス

Microsoft Visual Studio Community Editionは、私的目的、個人開発者やエンタープライズに該当しない企業が無料で商用利用も可能な製品です。  
エンタープライズとは、PCの台数が250台を超えている、または250人を超えるユーザーがいる、または年間収益が米100万ドル(約1億円)を超えるいずれかの条件  
に該当する企業です。エンタープライズに該当しない企業は、PCの台数が250台以下かつユーザー250人以下かつ年間収益が米100万ドル(約1億円)以下となります。  
また、エンタープライズに該当しない企業でもCommunity Editionを使えるユーザーは5名までとなっている。  
ただし、エンタープライズに該当しない企業でユーザー5名までであってもエンタープライズ企業のオープンソースプロジェクトではない受託開発では使う事が  
できない。  
ただし、エンタープライズ企業である場合や受託開発でもオープンソースプロジェクトであれば使う事ができるという複雑なエディションとなっています。  

Microsoft Visual Studio Community Editionは、再インストールする事を目的としてバックアップ用の複製を1部だけ許されています。  

[Microsoft Visual Studio 2015 Communityライセンス条項](https://visualstudio.microsoft.com/ja/license-terms/microsoft-visual-studio-community-2015/)  
[Microsoft Visual Studio 2019 Communityライセンス条項](https://visualstudio.microsoft.com/ja/license-terms/mlt031819/)  

### 2-3. プロダクトキーについて

Microsoft Visual Studio Expressのライセンス認証を行う際、同じアカウントでのプロダクトキーは同じものが発行された気がするが複数PCにインストールする  
場合は複数のアカウントを取る必要があるのか？以降試してないので機会があれば試す事にする。  

* * *
