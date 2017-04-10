---
title: "Azure Toolkit for IntelliJ の新機能 | Microsoft Docs"
description: "Azure Toolkit for IntelliJ の最新の機能について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 46ed791f-df59-416a-809e-f52345ad973c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda;martinsawicki
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: a2006dbcf0d63ef38651a0859dc654d531fd875a


---
# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ の新機能
## <a name="azure-toolkit-for-intellij-releases"></a>Azure Toolkit for IntelliJ リリース
この記事では、Azure Toolkit for IntelliJ の各種リリースと最新情報について記載しています。

> [!NOTE]
> Eclipse IDE 用の Azure Toolkit もあります。 詳細については、「 [Azure Toolkit for Eclipse]」をご覧ください。
> 
> 

### <a name="august-26-2016"></a>2016 年 8 月 26 日
Azure Toolkit for IntelliJ - August 2016 リリースでは、次の点が強化されています。

* **カスタム JDK ディストリビューション**。 Azure Toolkit for IntelliJ は、Azure WebApp コンテナーに対する任意の JDK バージョンの指定およびデプロイをサポートするようになりました。
  * Azure によって提供されている JDK に加えて、Azul Systems から提供されている Azure で利用できる Zulu OpenJDK の多様なバージョンから選択できます。
  * ZIP ファイルとしてストレージ アカウントにアップロードする場合は、独自の JDK ディストリビューションも指定できます。
* **Azure 用エクスプローラー ビューの機能強化**:
  * Azure の新しい Resource Manager モデルを使用した仮想マシンの管理をサポートします。IDE を離れずにリソース マネージャー ベースの仮想マシンを一覧表示、作成、削除できます。
  * Azure Resource Manager を使用したストレージ アカウントの BLOB 管理をサポートします。これは、「クラシック」ストレージ アカウントを管理するための既存の機能を補完するものです。
* **Microsoft JDBC Driver 6.0 for SQL Server**。 この更新は、Microsoft SQL Server (v6.0) の最新の JDBC ドライバーを組み込みます。このドライバーは、Java プロジェクトに簡単に追加できるライブラリとして含まれて、以前のバージョンから置き換わるようになりました。

### <a name="june-29-2016"></a>2016 年 6 月 29 日
Azure Toolkit for IntelliJ - June 2016 リリースでは、次の点が強化されています。

* **Java 8 の要件**。 Azure Toolkit for IntelliJ には、現在、Java 8 が必要です。ただし、この要件はツールキットにのみ適用されます。 - アプリケーションは、Azure でサポートされている Java のすべてのバージョンを引き続き使用できます。
* **最新の Java JDK のサポート**。 Java JDK の最新バージョンは、現在、Azure Toolkit for IntelliJ でサポートされています。
* **Azure SDK v2.9.1 のサポート**。 最新バージョンの Azure SDK が、Azure Toolkit for IntelliJ を使用するための最低限の前提条件になりました。
* **統合サンプル**。 Azure Toolkit for IntelliJ には、現在、開発者の作業開始に役立ついくつかのサンプル アプリケーションが特徴付けられています。
* **HDInsight ツールの統合**。 Azure の HDInsight ツールは、現在、Azure Toolkit for IntelliJ にバンドルされています。 詳細については、 [IntelliJ 用の HDInsight Tools プラグイン]に関するページを参照してください。
* **Java Web アプリのリモート デバッグ**。 Azure Toolkit for IntelliJ には、現在、Azure App Service での Java Web アプリのリモート デバッグがサポートされています。

### <a name="april-12-2016"></a>2016 年 4 月 12 日
Azure Toolkit for IntelliJ - April 2016 リリースでは、次の点が強化されています。

* **Azure SDK v2.9.0 のサポート**。 最新バージョンの Azure SDK が、Azure Toolkit for IntelliJ を使用するための最低限の前提条件になりました。
* **Azure Web アプリのサポートに関連する、その他の操作性、応答性、およびパフォーマンスの強化**。 Toolkit が Azure と通信する際のパフォーマンスがさまざまな点で最適化されたため、より応答性の高い UI となっています。
* **IntelliJ 内から Azure の既存の Web アプリケーション コンテナーを削除する機能**。 Azure Toolkit for IntelliJ を使用して、IntelliJ を離れることなく既存の Azure Web コンテナーを削除できるようになりました。

## <a name="see-also"></a>関連項目
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse のインストール]
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for Eclipse の新機能]
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ のインストール]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]
  * *Azure Toolkit for IntelliJ の新機能 (この記事)*

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ./azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547

[IntelliJ 用の HDInsight Tools プラグイン]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md



<!--HONumber=Jan17_HO1-->


