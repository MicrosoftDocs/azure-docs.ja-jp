---
title: 複数のデータベースにわたるレポート クエリ
description: 分散クエリを使用したテナント間レポート。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 18a02b81e459217ccca53d48a08e35a706b071b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793264"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>分散クエリを使用したテナント間レポート
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、テナント データベース セット全体を対象に、レポート用の分散クエリを実行します。 これらのクエリによって、Wingtip Tickets SaaS アプリの日常業務データに埋もれている洞察を抽出できます。 これを行うには、追加のレポート データベースをカタログ サーバーにデプロイし、エラスティック クエリを使用して分散クエリを有効にします。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> 
> * レポート データベースをデプロイする方法
> * すべてのテナント データベースにわたって分散クエリを実行する方法
> * 各データベースのグローバル ビューがテナント間の効率的なクエリを可能にする方法


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。


* Wingtip Tickets SaaS Database Per Tenant アプリをデプロイします。 5 分未満でデプロイするには、「[Deploy and explore the Wingtip Tickets SaaS Multi-tenant Database application (Wingtip Tickets SaaS Database Per Tenant アプリケーションのデプロイと探索)](./saas-dbpertenant-get-started-deploy.md)」を参照してください
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](/powershell/azure/get-started-azureps)」をご覧ください。
* SQL Server Management Studio (SSMS) をインストールしている。 SSMS のダウンロードとインストールについては、[SQL Server Management Studio (SSMS) のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)に関するページを参照してください。


## <a name="cross-tenant-reporting-pattern"></a>テナント間のレポート パターン

![テナント間の分散クエリ パターン](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

SaaS アプリケーションの使用による優れた機会の 1 つは、クラウドに保存されている膨大なテナント データを使用して、アプリケーションの操作と使用状況に関する洞察を得られることです。 このような洞察は、機能の開発、操作性の改良、アプリやサービスへのその他の投資に役立ちます。

単一のマルチテナント データベース内にあるこのデータにアクセスすることは簡単ですが、数千ものデータベースにわたる規模で分散されている場合は簡単ではありません。 1 つのアプローチは[エラスティック クエリ](elastic-query-overview.md)を使用するものであり、この方法では、分散した一連のデータベースにわたって共通スキーマでクエリを行えます。 これらのデータベースは、別のリソース グループとサブスクリプションに分散できますが、共通のログインを共有する必要があります。 エラスティック クエリは、分散 (テナント) データベース内のテーブルまたはビューをミラー化する外部テーブルが定義されている単一の "*ヘッド*" データベースを使用します。 このヘッド データベースに送信されたクエリはコンパイルされ、分散クエリ プランが生成されます。このクエリの一部は、必要に応じてテナント データベースにプッシュダウンされます。 エラスティック クエリでは、カタログ データベースのシャード マップを使用して、すべてのテナント データベースの場所を特定します。 ヘッド データベースのセットアップとクエリは、標準的な [Transact-SQL](/sql/t-sql/language-reference) を使用して簡単に行うことができ、Power BI や Excel などのツールからのクエリがサポートされます。

テナント データベース全体でクエリを分散することで、エラスティック クエリはライブ プロダクション データを短時間で洞察できます。 エラスティック クエリは潜在的に多数のデータベースからデータを引き出すため、1 つのマルチテナント データベースに同じようなクエリを送信する場合と比較して待機時間が長くなる可能性があります。 クエリは、ヘッド データベースに返されるデータを最小限に抑えるように設計します。 頻繁に使用される、あるいは複雑な分析クエリ/レポートとは対照的に、エラスティック クエリは多くの場合、少量のリアルタイム データの問い合わせに最適です。 クエリの動作に問題がある場合、[実行プラン](/sql/relational-databases/performance/display-an-actual-execution-plan)を見て、クエリのどの部分がリモート データベースにプッシュダウンされ、どのくらいのデータが返されているかを確認してください。 複雑な集計や分析処理を必要とするクエリは、分析クエリのために最適化されたデータベースまたはデータ ウェアハウスにテナント データを抽出することで、効率的に処理できる可能性があります。 このパターンは[テナント分析のチュートリアル](saas-tenancy-tenant-analytics.md)で説明されています。 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant アプリケーション スクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリで入手できます。 Wingtip Tickets SaaS スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。

## <a name="create-ticket-sales-data"></a>チケット売り上げデータを作成する

より興味深いデータ セットにクエリを実行する目的で、チケットジェネレーターを実行し、チケット売り上げデータを作成します。

1. *PowerShell ISE* で、\\...Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* スクリプトを開き、次の値を設定します。
   * **$DemoScenario** = 1, **すべての会場のイベントのチケットを購入**.
2. **F5** キーを押してスクリプトを実行し、チケットの売り上げを生成します。 スクリプトが実行されている間、このチュートリアルの手順を続行します。 チケット データは、*アドホック分散クエリの実行* セクションで照会され、チケットジェネレーターが完了するまで待機します。

## <a name="explore-the-global-views"></a>グローバル ビューを詳しく知る

Wingtip Tickets SaaS Database Per Tenant アプリケーションでは、各テナントに 1 つのデータベースが指定されています。 そのため、データベース テーブルに含まれるデータは、単一テナントのパースペクティブにスコープを設定しています。 ただし、すべてのデータベースにクエリを実行するときは、エラスティック クエリがデータを、テナントによりシャードされた 1 つの論理データベースにデータが含まれるかのように処理できることが重要です。 

このパターンをシミュレートするために、'グローバル' ビュー セットがテナント データベースに追加されます。このビューは、グローバルにクエリ実行される各テーブルにテナント ID を与えます。 たとえば、*VenueEvents* ビューは、計算した *VenueId* を *Events* テーブルから与えられた列に追加します。 同様に、*VenueTicketPurchases* と *VenueTickets* ビューは、それぞれのテーブルから与えられた計算済みの *VenueId* 列を追加します。 これらのビューは、*VenueId* 列が存在する場合、クエリを並列化して適切なリモート テナント データベースにそれらをプッシュダウンするために、エラスティック クエリによって使用されます。 これにより返されるデータの量が劇的に減り、クエリがたくさんあってもパフォーマンスが相当上がります。 これらのグローバル ビューはすべてのテナント データベースで事前作成されています。

1. SSMS を開き、[tenants1-&lt;USER&gt; サーバーに接続します](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
1. **[データベース]** を展開し、 _[contosoconcerthall]_ を右クリックして、 **[新しいクエリ]** を選択します。
1. 次のクエリを実行して、シングル テナント テーブルとグローバル ビューの違いを調べます。

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

これらのビューでは、*VenueId* は Venue 名のハッシュとして計算されましたが、一意の値はどのような方法で求めてもかまいません。 この方法は、カタログで使用するためにテナント キーを計算する方法に似ています。

*Venues* ビューの定義を調べるには:

1. **オブジェクト エクスプローラー** で、 **[contosoconcerthall]**  >  **[ビュー]** の順に展開します。

   ![4 種類の Venue Screenshot dbo を含む、[ビュー] ノードのコンテンツを示すスクリーンショット。](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. **[dbo.Venues]** を右クリックします。
3. **[ビューをスクリプト化]**  >  **[新規作成]**  >  **[新しいクエリ エディター ウィンドウ]** の順に選択します。

他の *Venue* ビューをスクリプト化し、*VenueId* の追加方法を確認します。

## <a name="deploy-the-database-used-for-distributed-queries"></a>分散クエリに使用されるデータベースをデプロイする

この演習では、_adhocreporting_ データベースをデプロイします。 これは、すべてのテナント データベースに対してクエリを実行する場合に使用されるスキーマが含まれるヘッド データベースです。 このデータベースは既存のカタログ サーバーにデプロイされます。サンプル アプリのすべての管理関連データベースに使用されるサーバーです。

1. *PowerShell ISE* で \\...Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* を開きます。 

1. **$DemoScenario = 2**, "_アドホック レポート データベースをデプロイする_" を設定します。

1. **F5** キーを押してスクリプトを実行し、*adhocreporting* データベースを作成します。

次のセクションでは、分散クエリの実行に利用できるように、データベースにスキーマを追加します。

## <a name="configure-the-head-database-for-running-distributed-queries"></a>分散クエリを実行するための "ヘッド" データベースを構成する

この演習では、_adhocreporting_ データベースにスキーマ (外部データ ソースと外部テーブル定義) を追加して、すべてのテナント データベースでクエリを実行できるようにします。

1. SQL Server Management Studio を開き、前の手順で作成したアドホック レポート データベースに接続します。 データベースの名前は *adhocreporting* になります。
2. SSMS で、...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ を開きます。
3. SQL スクリプトを確認し、次の点に注意します。

   エラスティック クエリは、データベース スコープの資格情報を使用して、各テナント データベースにアクセスします。 この資格情報は、すべてのデータベースで使用できる必要があり、通常、これらのクエリを有効にするために必要な最小限の権限が付与されている必要があります。

    ![資格情報を作成する](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   カタログ データベースを外部データ ソースとして使用することで、クエリの実行時にカタログに登録されているすべてのデータベースにクエリが分散されます。 デプロイごとにサーバー名が異なるため、このスクリプトでは、スクリプトが実行される現在のサーバー (@@servername) からカタログ サーバーの場所を取得します。

    ![外部データ ソースを作成する](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   外部テーブルは、前のセクションで説明したグローバル ビューを参照します。**DISTRIBUTION = SHARDED(VenueId)** で定義されています。 各 *VenueId* は個々のデータベースにマッピングされるため、次のセクションで示すように、多くのシナリオでパフォーマンスが改善されます。

    ![外部テーブルを作成する](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   作成され、データが設定されているローカル テーブル _VenueTypes_。 この参照データ テーブルは、すべてのテナント データベースで共通です。ここではローカル テーブルとして表すことができます。また、共通データを入力できます。 クエリによっては、このテーブルをヘッド データベースに定義すると、ヘッド データベースに移動する必要があるデータの量が減少する可能性があります。

    ![テーブルを作成する](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   この方法で参照テーブルを追加する場合、テナント データベースを更新するときは必ず、テーブルのスキーマとデータを更新します。

4. **F5** キーを押してスクリプトを実行し、*adhocreporting* データベースを初期化します。 

これで分散クエリを実行し、すべてのテナントを対象に洞察を収集できます。

## <a name="run-distributed-queries"></a>分散クエリを実行する

これで *adhocreporting* データベースが設定されたので、分散クエリをいくつか実行します。 クエリ処理の場所をより良く理解できるように実行プランを含めます。 

実行プランを調べるとき、プラン アイコンにカーソルを合わせると詳細が表示されます。 

外部データ ソースの定義時に **DISTRIBUTION = SHARDED(VenueId)** を設定したことで、多くのシナリオでパフォーマンスが改善します。これは重要なことなのでご留意ください。 各 *VenueId* は個々のデータベースにマッピングされるため、フィルター処理はリモートで簡単に実行されます。必要なデータのみが返されます。

1. SSMS で ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* を開きます。
2. **adhocreporting** データベースに接続していることを確認します。
3. **[クエリ]** メニューを選択し、 **[実際の実行プランを含める]** をクリックします。
4. *[Which venues are currently registered?]* クエリを強調表示し、**F5** を押します。

   クエリは会場一覧全体を返します。すべてのテナントにクエリを実行し、各テナントからデータを返すことがいかに簡単かわかります。

   プランを調べ、コスト全体がリモート クエリであることを確認します。各テナント データベースがクエリをリモートで実行し、その会場情報をヘッド データベースに返しています。

   ![SELECT * FROM dbo.Venues](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. 次のクエリを選択し、**F5** を押します。

   このクエリは、テナント データベースとローカル *VenueTypes* テーブル (*adhocreporting* データベースのテーブルであり、ローカル) のデータを結合します。

   プランを調べ、コストの大半がリモート クエリであることを確認します。 各テナント データベースが会場情報を返し、ローカル *VenueTypes* テーブルでローカル結合を行い、フレンドリ名を表示します。

   ![リモートとローカルのデータで結合](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. 次に *On which day were the most tickets sold?* クエリを選択し、**F5** を押します。

   このクエリは、少しばかり複雑な結合と集計を行います。 ほとんどの処理は、リモートで発生します。  各会場の 1 日のチケット販売数を含む行のみがヘッド データベースに返されます。

   ![query](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> 
> * すべてのテナント データベースにわたって分散クエリを実行する
> * レポート データベースをデプロイし、分散クエリを実行するために必要なスキーマを定義する


それでは、[テナント分析チュートリアル](saas-tenancy-tenant-analytics.md)をお試しください。個別の分析データベースにデータを抽出することで、より複雑な分析を処理できます。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS Database Per Tenant アプリケーションに基づく作業のための追加のチュートリアル](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック クエリ](elastic-query-overview.md)