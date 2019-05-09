---
title: Elastic Database ツールの概要 - Azure | Microsoft Docs
description: 実行が容易なサンプル アプリケーションを含む、Azure SQL Database の Elastic Database ツール機能の基本説明です。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7c2e3b2bea7ddef3dfbf69f85b499d9a60158689
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999129"
---
# <a name="get-started-with-elastic-database-tools"></a>Elastic Database ツールの概要

このドキュメントでは、サンプル アプリを実行することで、[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)の開発を体験できます。 サンプル アプリでは単純なシャーディング アプリケーションを作成し、Azure SQL Database の Elastic Database ツールの主な機能について詳しく見て行きます。 また、[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)、[マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)のユース ケースに重点を置いています。 このクライアント ライブラリは .NET と Java で使用できます。 

## <a name="elastic-database-tools-for-java"></a>Java 用 Elastic Database ツール

### <a name="prerequisites"></a>前提条件

* Java Developer Kit (JDK) バージョン 1.8 以降
* [Maven](https://maven.apache.org/download.cgi)
* Azure の SQL Database サーバーまたは SQL Server のローカル インスタンス

### <a name="download-and-run-the-sample-app"></a>サンプル アプリケーションのダウンロードと実行

JAR ファイルをビルドし、サンプル プロジェクトを始めるには、以下のようにします。 
1. クライアント ライブラリとサンプル アプリを含む [GitHub リポジトリ](https://github.com/Microsoft/elastic-db-tools-for-java)を複製します。 

2. _./sample/src/main/resources/resource.properties_ ファイルを編集して、次の項目を設定します。
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. サンプル プロジェクトをビルドするには、_./sample_ ディレクトリで次のコマンドを実行します。

    ```
    mvn install
    ```
    
4. サンプル プロジェクトを開始するには、_./sample_ ディレクトリで次のコマンドを実行します。 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. クライアント ライブラリの機能をより深く知るには、さまざまなオプションを試してみてください。 サンプル アプリの実装の詳細を知るために、コードの詳細を確認することができます。

    ![Java の進行状況][5]
    
お疲れさまでした。 これで、Azure SQL Database で Elastic Database ツールを使って、最初のシャーディング アプリケーションを適切にビルドし、実行できました。 Visual Studio または SQL Server Management Studio を使用して SQL Database に接続し、サンプルで作成したシャードの内容を簡単に確認してください。 新しいサンプル シャード データベースと、シャード マップ マネージャー データベースがサンプルで作成されていることがわかります。 

お使いの Maven プロジェクトにクライアント ライブラリを追加するには、POM ファイルに次の依存関係を追加します。

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>.NET 用 Elastic Database ツール

### <a name="prerequisites"></a>前提条件

* Visual Studio 2012 以降 (C#)。 「 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)」で無償評価版をダウンロードしてください。
* NuGet 2.7 以降。 最新版を入手するには、「[Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)(NuGet のインストール)」をご覧ください。

### <a name="download-and-run-the-sample-app"></a>サンプル アプリケーションのダウンロードと実行

ライブラリをインストールするには、 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)に移動します。 ライブラリは、次のセクションで説明するサンプル アプリと共にインストールされます。

サンプルをダウンロードして実行するには、次の手順を実行します。 

1. [Elastic DB Tools for Azure SQL - Getting Started のサンプル](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6)を MSDN からダウンロードします。 任意の場所にサンプルを解凍します。

2. *ElasticScaleStarterKit.sln* ソリューションを *C#* ディレクトリから開いてプロジェクトを作成します。

3. サンプル プロジェクトのソリューションで、*app.config* ファイルを開きます。 ファイルの指示に従って、使う Azure SQL Database サーバーの名前とサインイン情報 (ユーザー名とパスワード) を追加します。

4. アプリケーションをビルドし、実行します。 メッセージが表示されたら、Visual Studio によるソリューションの NuGet パッケージの復元を有効にします。 これにより、Elastic Database クライアント ライブラリの最新版が NuGet からダウンロードされます。

5. クライアント ライブラリの機能をより深く知るには、さまざまなオプションを試してみてください。 アプリケーションで実行されたステップはコンソールに出力されるので、動作していたコードをじっくりと検討することができます。
   
    ![進捗状況][4]

お疲れさまでした。 これで、SQL Database で Elastic Database ツールを使って、最初のシャーディング アプリケーションを適切にビルドし、実行できました。 Visual Studio または SQL Server Management Studio を使用して SQL Database に接続し、サンプルで作成したシャードの内容を簡単に確認してください。 新しいサンプル シャード データベースと、シャード マップ マネージャー データベースがサンプルで作成されていることがわかります。

> [!IMPORTANT]
> 最新バージョンの Management Studio を常に使用して、Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="key-pieces-of-the-code-sample"></a>コード サンプルの主要部

* **シャードとシャード マップの管理**:このコードは、シャード、範囲、マッピングが *ShardManagementUtils.cs* ファイルでどのように機能するかを示します。 詳細については、「[シャード マップ マネージャーでデータベースをスケールアウトする](https://go.microsoft.com/?linkid=9862595)」をご覧ください。  

* **データ依存ルーティング**:トランザクションの適切なシャードへのルーティングは、*DataDependentRoutingSample.cs* ファイルに示されます。 詳細については、「[データ依存ルーティング](https://go.microsoft.com/?linkid=9862596)」をご覧ください。 

* **複数のシャードにまたがるクエリ実行**:複数のシャードに対するクエリの実行は、*MultiShardQuerySample.cs* ファイルに示されます。 詳細については、「[マルチシャード クエリ実行](https://go.microsoft.com/?linkid=9862597)」をご覧ください。

* **空のシャードの追加**:新しい空のシャードの反復追加は、*CreateShardSample.cs* ファイルのコードによって実行されます。 詳細については、「[シャード マップ マネージャーでデータベースをスケールアウトする](https://go.microsoft.com/?linkid=9862595)」をご覧ください。

## <a name="other-elastic-scale-operations"></a>他の Elastic Scale の操作

* **既存のシャードの分割**:シャードを分割する機能は、分割/マージ ツールで提供されます。 詳細については、「[スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください。

* **既存のシャードのマージ**:シャードのマージも分割/マージ ツールを使って行われます。 詳細については、「[スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください。   

## <a name="cost"></a>コスト

Elastic Database ツール ライブラリは無料です。 Elastic Database ツールを使っても、Azure の利用料以外の追加料金は発生しません。 

たとえば、サンプル アプリケーションは新しいデータベースを作成します。 この機能のコストは、選んだ SQL Database のエディションと、アプリケーションによる Azure の使用状況に応じて異なります。

料金情報については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

## <a name="next-steps"></a>次の手順

Elastic Database ツールについて詳しくは、以下の記事をご覧ください。

* コード サンプル 
  * Elastic Database ツール ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)、[Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Azure SQL 用 Elastic Database ツール - Entity Framework の統合](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [スクリプト センターのシャードの弾力性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* ブログ:[Elastic Scale の発表](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* チャンネル 9:[Elastic Scale の概要に関するビデオ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* ディスカッション フォーラム:[Azure SQL Database フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* パフォーマンスを測定するには:[シャード マップ マネージャーのパフォーマンス カウンター](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

