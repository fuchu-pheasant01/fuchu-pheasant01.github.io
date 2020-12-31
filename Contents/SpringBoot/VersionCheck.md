---
layout: default
title: ":leaves: Spring Boot"
description: ":leaves: Spring Boot 内蔵バージョン確認方法"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

Pom.xmlファイルには細かい個々の通常バージョンは記載せずSpringBoot側に自動で任すというのが
通常の様で<properties>タグ内にバージョンを個別指定できるがSpringBoot側での都合が悪くなり
構成がおかしくなる可能性もあるとの事。
それでもどのバージョンが使われているかは確認しなければならない。
以下で確認できる。

<br />

## 1. Spring Boot GitHubのdependencies階層のpom.xmlを確認する。

[spring-boot/pom.xml at v2.1.6.RELEASE · spring-projects/spring-boot · GitHub](https://github.com/spring-projects/spring-boot/blob/v2.1.6.RELEASE/spring-boot-project/spring-boot-dependencies/pom.xml)
Thymeleafなどのテンプレートエンジン、Spring Security、Hibernate、Jdbcなどのデータベース関連など
※URLは2.1.6RELEASEのものなのでコンボボックスのTagタブで目的のバージョンを選択できる。

## 2. EclipseのプロジェクトのMaven依存関係の.jarファイル名から

上記1.に加えTomcat(tomcat-embed-core-xxx.jar)やSpring Famework(spring-webmvc-xxxx.RELEASE.jar)やJPA(javax.persistence-api-xx.jar)など。
※xxxはバージョン。
本来は、pom.xmlファイルがどうであれMaven依存関係に追加されているライブラリで動く訳なのでここがズバリの解答と思う。

## 3. Eclipseでプロジェクトのpom.xmlのコードを編集で確認

コード編集の画面のタブの他に「実効POM(Effective POM)」タブがあるのでそこで詳しい事も分かる。
※しかし、アルファベット順ではないのでキーワードが分からなければ探すのに苦労する。(検索もかけれない)

* * *
