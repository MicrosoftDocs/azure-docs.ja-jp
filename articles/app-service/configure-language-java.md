---
title: Windows Java アプリを構成する - Azure App Service | Microsoft Docs
description: Azure App Service の既定の Windows インスタンスで Java アプリが実行されるように構成する方法について説明します。
keywords: azure app service, web アプリ, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 25434360bcc0155411451dbac065e0b7fad9c3bf
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617480"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Azure App Service 向けの Windows Java アプリを構成する

Azure App Service を使用すると、Java 開発者は、完全に管理された Windows ベースのサービス上で Tomcat または Java Standard Edition (SE) パッケージ化 Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service を使用する Java 開発者向けに主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず [Java クイック スタート](app-service-web-get-started-java.md)をお読みください。 Java 開発に限られない、App Service の使用に関する一般的な質問については、[App Service Windows の FAQ](faq-configuration-and-management.md) で回答されています。

> [!NOTE]
> お探しの情報がない場合、 [Windows OSS の FAQ](faq-configuration-and-management.md) または [Java Linux の構成ガイド](containers/configure-language-java.md) で Java アプリのデプロイとセキュリティについて参照してください。

## <a name="configuring-tomcat"></a>Tomcat の構成

Tomcat の `server.xml` または他の構成ファイルを編集するには、最初にポータルで Tomcat のメジャー バージョンを確認してメモします。

1. `env` コマンドを実行して、ご使用のバージョンの Tomcat ホーム ディレクトリを見つけます。 `AZURE_TOMCAT`で始まり、ご使用のメジャー バージョンに一致する環境変数を検索します。 たとえば、`AZURE_TOMCAT85_HOME` は Tomcat 8.5 を指しています。
1. ご使用のバージョンの Tomcat ホーム ディレクトリを特定したら、構成ディレクトリを `D:\home` にコピーします。 たとえば、`AZURE_TOMCAT85_HOME` の値が `D:\Program Files (x86)\apache-tomcat-8.5.37` の場合、コピーした構成ディレクトリの完全なパスは `D:\home\tomcat\conf` になります。

最後に、お客様のアプリ サービスを再起動します。 デプロイメントの場所は、以前と同様に `D:\home\site\wwwroot\webapps` になります。

## <a name="java-runtime-statement-of-support"></a>Java ランタイムのサポート ステートメント

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/downloads/azure-only/zulu/) です。

メジャー バージョンの更新プログラムは、Azure App Service for Windows の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/cvss.cfm) で 9.0 以上の基本スコアにより定義されます。

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

### <a name="local-development"></a>ローカル開発

開発者は、[Azul のダウンロード サイト](https://www.azul.com/downloads/azure-only/zulu/)から運用エディションの Azul Zulu Enterprise JDK をローカルでのデプロイ用にダウンロードできます。

### <a name="development-support"></a>開発サポート

[正規の Azure サポート プラン](https://azure.microsoft.com/support/plans/)を利用して Azure または [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 用の開発を行うとき、Microsoft では [Azure がサポートする Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) の製品サポートを利用できます。

### <a name="runtime-support"></a>ランタイム サポート

[正規のサポート プラン](https://azure.microsoft.com/support/plans/)の対象である開発者は、Azure サポートを利用して Azul Zulu JDK に関する[問題を投稿](/azure/azure-supportability/how-to-create-azure-support-request)できます。

## <a name="next-steps"></a>次の手順

このトピックでは、Azure App Service for Windows の Java ランタイムのサポート ステートメントを提供します。

- Azure App Service での Web アプリケーションのホストの詳細については、「[App Service の概要](overview.md)」を参照してください。
- Azure 開発に関する Java については、「[Java 開発者向けの Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)」を参照してください。
