---
title: "Azure SDK for Java のダウンロード | Microsoft Docs"
description: "Azure SDK for Java を、Maven プロジェクト用のサンプル コードと一緒にダウンロードする方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>Azure SDK for Java のダウンロード
この記事には、Azure Management Libraries for Java をダウンロードしてインストールするための手順が記載されています。

> [!NOTE]
> Azure Management Libraries for Java は、[Apache License Version 2.0][license] に準拠して配布されます。
>

## <a name="azure-libraries-for-java---manual-download"></a>Azure Libraries for Java - 手動ダウンロード
Azure Management Libraries for Java を手動でインストールするには、<http://go.microsoft.com/fwlink/?LinkId=690320> をクリックして、すべてのライブラリと依存関係を含む ZIP ファイルをダウンロードします。

zip ファイルをコンピューターにダウンロードしたら、コンテンツを抽出し、次のいずれかのオプションを使用して、JAR ファイルをプロジェクトに追加します。

* JAR ファイルを Eclipse または IntelliJ の Java プロジェクトにインポートします。
* Eclipse または IntelliJ の Java プロジェクトのビルド パスを JAR ファイルへのパスを含むように構成します。

> [!NOTE]
> ライセンスおよびその他の情報については、ZIP 内の license.txt と ThirdPartyNotices.txt ファイルを参照してください。
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Azure Management Libraries for Java - Maven によるビルド
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>手順 1 - ビルドに Maven を使用するようにプロジェクトを設定する
Azure Management Libraries for Java を使用する Maven プロジェクトを Eclipse で作成するには、「[Getting Started with Azure Management Libraries for Java (Java 用 Azure 管理ライブラリの概要)][maven-getting-started]」に記載された手順に従ってください。

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>手順 2 - 必要な依存関係を持つ Maven 設定を構成する
ビルドに Maven を使用するようにプロジェクトを構成したら、必要な依存関係を、次の例のような構文を使用する pom.xml ファイルに追加できます。 次の例に表示されたすべての依存関係を追加する必要はありません。追加する必要があるのは、プロジェクトに必要な特定の依存関係のみです。

> [!NOTE]
> 次のサンプルの各 `<version>` 要素内で、この例の "n.n.n" プレースホルダーを有効なバージョン番号に置き換えてください。有効なバージョン番号は、[Maven の Azure ライブラリ リポジトリ]から入手できます。
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>関連項目
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse のインストール]
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for Eclipse の新機能]
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ のインストール]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for IntelliJ の新機能]

Java での Azure の使用方法の詳細については、「 [Azure Java Developer Center]」を参照してください。

> [!NOTE]
> Eclipse でのビルド パスの設定方法については、Eclipse Web サイトの [Java ビルド パス] に関する記事を参照してください。
>

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ./azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Maven の Azure ライブラリ リポジトリ]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java ビルド パス]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

