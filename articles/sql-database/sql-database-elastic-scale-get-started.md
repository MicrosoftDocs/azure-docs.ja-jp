---
title: "Elastic Database ツールの概要 | Microsoft Docs"
description: "実行が容易なサンプル アプリケーションを含む、Azure SQL Database の Elastic Database ツール機能の基本説明です。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 637463399593f4bc9ff5bfcbf67bf93b816efc7f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="get-started-with-elastic-database-tools"></a>Elastic Database ツールの概要
このドキュメントでは、サンプル アプリを実行して開発作業について説明します。 サンプルでは単純なシャーディング アプリケーションを作成し、Elastic Database ツールの主な機能について詳しく見て行きます。 サンプルでは、[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)の機能を示します。

ライブラリをインストールするには、 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)に移動します。 ライブラリは、次のセクションで説明するサンプル アプリと共にインストールされます。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2012 以降 (C#)。 「 [Visual Studio のダウンロード](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)」で無償評価版をダウンロードしてください。
* NuGet 2.7 以降。 最新版を入手するには、「[Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)(NuGet のインストール)」をご覧ください。

## <a name="download-and-run-the-sample-app"></a>サンプル アプリケーションのダウンロードと実行
**Azure SQL 用 Elastic DB ツール - 概要**のページで示したサンプル アプリケーションでは、Elastic Database ツールを使用したシャーディング アプリケーションの開発エクスペリエンスにおける最も重要な側面を紹介しています。 そこでは、主な使用事例として[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)、[マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)に焦点を当てています。 サンプルをダウンロードして実行するには、次の手順を実行します。 

1. [Elastic DB Tools for Azure SQL - Getting Started のサンプル](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6)を MSDN からダウンロードします。 任意の場所にサンプルを解凍します。

2. **ElasticScaleStarterKit.sln** ソリューションを **C#** ディレクトリから開いてプロジェクトを作成します。

3. サンプル プロジェクトのソリューションで、**app.config** ファイルを開きます。 ファイルの指示に従って、使用する Azure SQL Database サーバー名とサインイン情報 (ユーザー名とパスワード) を追加します。

4. アプリケーションをビルドし、実行します。 メッセージが表示されたら、Visual Studio によるソリューションの NuGet パッケージの復元を有効にします。 これで Elastic Database クライアント ライブラリの最新版が NuGet からダウンロードされます。

5. クライアント ライブラリの機能をより深く知るために、さまざまなオプションを試してみてください。 アプリケーションで実行されたステップはコンソールに出力されますので、動作していたコードをじっくりと検討することができます。
   
    ![進捗状況][4]

これで、最初のシャーディング アプリケーションを Elastic Database ツールを使用して正しく SQL Database にビルドし、実行できました。 Visual Studio または SQL Server Management Studio を使用して SQL Database に接続し、サンプルで作成したシャードの内容を簡単に確認してください。 新しいサンプル シャード データベースと、シャード マップ マネージャー データベースがサンプルで作成されていることがわかります。

> [!IMPORTANT]
> 最新バージョンの Management Studio を常に使用して、Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>コード サンプルの主要部
* **シャードとシャード マップの管理**: このコードは、シャード、範囲、マッピングが **ShardManagementUtils.cs** ファイルでどのように機能するかを示します。 詳細については、「[シャード マップ マネージャーでデータベースをスケールアウトする](http://go.microsoft.com/?linkid=9862595)」をご覧ください。  

* **データ依存ルーティング**: トランザクションの適切なシャードへのルーティングは、**DataDependentRoutingSample.cs** に示されます。 詳細については、「[データ依存ルーティング](http://go.microsoft.com/?linkid=9862596)」をご覧ください。 

* **複数のシャードにまたがるクエリ実行**: 複数のシャードに対するクエリの実行は、**MultiShardQuerySample.cs** ファイルに示されます。 詳細については、「[マルチシャード クエリ実行](http://go.microsoft.com/?linkid=9862597)」をご覧ください。

* **空のシャードの追加**: 新しい空のシャードの反復追加は、**CreateShardSample.cs** ファイルのコードによって実行されます。 詳細については、「[シャード マップ マネージャーでデータベースをスケールアウトする](http://go.microsoft.com/?linkid=9862595)」をご覧ください。

### <a name="other-elastic-scale-operations"></a>他の Elastic Scale の操作
* **既存のシャードの分割**: シャードを分割する機能は、**分割/マージ ツール**で提供されます。 詳細については、「[スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください。

* **既存のシャードのマージ**: シャードのマージも**分割/マージ ツール**を使用して行われます。 詳細については、「[スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください。   

## <a name="cost"></a>コスト
Elastic Database ツールは無料です。 Elastic Database ツールを使用しても、Azure の利用料以外の追加料金は発生しません。 

たとえば、サンプル アプリケーションは新しいデータベースを作成します。 この場合のコストは、選択した SQL Database のエディションと、アプリケーションによる Azure の使用状況に応じて異なります。

料金情報については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

## <a name="next-steps"></a>次のステップ
Elastic Database ツールの詳細については、以下のページをご覧ください。

* コード サンプル 
  * [Azure SQL 用 Elastic DB ツール - 概要](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Azure SQL 用 Elastic DB ツール - Entity Framework の統合](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [スクリプト センターのシャードの弾力性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* ブログ: [Elastic Scale の発表](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [Implementing Scale-Out Using Sharding with the Elastic Database Client Library Video (Elastic Database クライアント ライブラリのシャードを使用したスケールアウトの実装)](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* チャンネル 9: [Elastic Scale の概要に関するビデオ](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* ディスカッション フォーラム: [Azure SQL Database フォーラム](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* パフォーマンスを測定するには: [シャード マップ マネージャーのパフォーマンス カウンター](sql-database-elastic-database-client-library.md)

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


