---
title: "複数の Azure SQL データベースにわたるアドホック分析クエリの実行 | Microsoft Docs"
description: "Wingtip SaaS マルチテナント アプリで複数の SQL データベースに対してアドホック分析クエリを実行します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c287fe5d6b333c749b0580b5253e7e46ac27232b
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>すべての Wingtip SaaS テナントにわたるアドホック分析クエリの実行

このチュートリアルでは、テナント データベース セット全体を対象に分散クエリを実行し、アドホック分析を有効にします。 分散クエリを有効にするためにエラスティック クエリが利用されます。追加の分析データベースを (カタログ サーバーに) デプロイする必要があります。 これらのクエリでは、Wingtip SaaS アプリの日常業務データに埋もれている洞察を抽出できます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * テナントの境界を越えた効率的なクエリを可能にする、各データベースのグローバル ビューについて
> * アドホック分析データベースをデプロイする方法
> * すべてのテナント データベースにわたって分散クエリを実行する方法



このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。
* SQL Server Management Studio (SSMS) をインストールしている。 SSMS のダウンロードとインストールについては、[SQL Server Management Studio (SSMS) のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)に関するページを参照してください。


## <a name="ad-hoc-analytics-pattern"></a>アドホック分析パターン

SaaS アプリケーションで得られる優れた機会の 1 つは、クラウドで一元的に保存されている膨大なテナント データを使用することです。 このデータを使用することで、アプリケーションの操作と使用状況、テナント、ユーザー、設定、動作などに関する洞察が得られます。このような洞察は、機能の開発、操作性の改良、アプリやサービスへのその他の投資に役立ちます。

単一のマルチテナント データベース内にあるこのデータにアクセスすることは簡単ですが、数千ものデータベースにわたる規模で分散されている場合は簡単ではありません。 1 つのアプローチは[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用するものであり、この方法では、分散した一連のデータベースにわたって共通スキーマでクエリを行えます。 エラスティック クエリは、分散 (テナント) データベース内のテーブルまたはビューをミラー化する外部テーブルが定義されている単一の "*ヘッド*" データベースを使用します。 このヘッド データベースに送信されたクエリはコンパイルされ、分散クエリ プランが生成されます。このクエリの一部は、必要に応じてテナント データベースにプッシュダウンされます。 エラスティック クエリでは、カタログ データベースのシャード マップを使用して、テナント データベースの場所を指定します。 セットアップとクエリは、標準的な [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) を使用して簡単に行うことができ、Power BI や Excel などのツールからのアドホック クエリをサポートしています。

テナント データベース全体でクエリを分散することで、エラスティック クエリはライブ プロダクション データを短時間で洞察できます。 ただし、エラスティック クエリはたくさんのデータベースからデータを引き出すため、1 つのマルチテナント データベースに同じようなクエリを送信する場合と比較し、待機時間が長くなることがあります。 返されるデータを最小限に抑えるために、クエリの設計には注意が必要です。 頻繁に使用される、あるいは複雑な分析クエリ/レポートとは対照的に、エラスティック クエリは多くの場合、少量のリアルタイム データの問い合わせに最適です。 クエリの動作に問題がある場合、[実行プラン](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)を見て、クエリのどの部分がリモート データベースにプッシュダウンされているか、どのくらいのデータが返されているかを確認してください。 複雑な分析処理を必要とするクエリは、分析クエリのために最適化された専用のデータベースやデータ ウェアハウスにテナント データを抽出することで、より効果的に機能することがあります。 このパターンは[テナント分析のチュートリアル](sql-database-saas-tutorial-tenant-analytics.md)で説明されています。 

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip SaaS のスクリプトとアプリケーション ソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 [Wingtip SaaS のスクリプトをダウンロードする手順](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="create-ticket-sales-data"></a>チケット売り上げデータを作成する

より興味深いデータ セットにクエリを実行する目的で、チケットジェネレーターを実行し、チケット売り上げデータを作成します。

1. *PowerShell ISE* で、...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* スクリプトを開き、次の値を設定します。
   * **$DemoScenario** = 1, **すべての会場のイベントのチケットを購入**.
2. **F5** キーを押してスクリプトを実行し、チケットの売り上げを生成します。 スクリプトが実行されている間、このチュートリアルの手順を続行します。 チケット データのクエリは*アドホック分散クエリの実行*セクションで実行されます。そのため、その演習に進むとき、まだ実行中であればチケットジェネレーターの完了を待ちます。

## <a name="explore-the-global-views"></a>グローバル ビューを詳しく知る

Wingtip SaaS アプリケーションは、データベースあたりのテナント モデルを使用して構築されるため、テナント データベース スキーマはシングル テナントの観点から定義されます。 テナント固有の情報は、*Venue* という 1 つのテーブルに存在します。このテーブルは常に 1 行で構成されており、主キーなしでヒープとして実装されています。 スキーマ内の他のテーブルは、*Venue* テーブルに関連付ける必要はありません。通常の用途では、データがどのテナントに属しているかは明白です。

ただし、すべてのデータベースにクエリを実行するときは、エラスティック クエリがデータを、テナントによりシャードされた 1 つの論理データベースにデータが含まれるかのように処理できることが重要です。 これを実現するために、'グローバル' ビュー セットがテナント データベースに追加されます。このビューは、グローバルにクエリ実行される各テーブルにテナント ID を与えます。 たとえば、*VenueEvents* ビューは、計算した *VenueId* を *Events* テーブルから与えられた列に追加します。 (基礎の *Events* テーブルではなく) *VenueEvents* のヘッド データベースで外部テーブルを定義することで、エラスティック クエリは *VenueId* に基づいて結合をプッシュダウンできます。(ヘッド データベースではなく) 各リモート データベースで並列実行できます。 これにより返されるデータの量が劇的に減り、クエリがたくさんあってもパフォーマンスが相当上がります。 これらのグローバル ビューはすべてのテナント データベース (と *basetenantdb* で) で事前作成されています。

1. SSMS を開き、[tenants1-&lt;USER&gt; サーバーに接続します](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
2. **[データベース]** を展開し、**[contosoconcerthall]** を右クリックして、**[新しいクエリ]** を選択します。
3. 次のクエリを実行して、シングル テナント テーブルとグローバル ビューの違いを調べます。

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

1. **オブジェクト エクスプローラー**で、**[contosoconcethall]** > **[ビュー]** の順に展開します。

   ![ビュー](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. **[dbo.Venues]** を右クリックします。
3. **[ビューをスクリプト化]** > **[新規作成]** > **[新しいクエリ エディター ウィンドウ]** の順に選択します。

他の *Venue* ビューをスクリプト化し、*VenueId* の追加方法を確認します。

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>アドホック分散クエリに使用されるデータベースをデプロイする

この演習では、*adhocanalytics* データベースをデプロイします。 これは、すべてのテナント データベースに対してクエリを実行する場合に使用されるスキーマが含まれるヘッド データベースです。 このデータベースは既存のカタログ サーバーにデプロイされます。サンプル アプリのすべての管理関連データベースに使用されるサーバーです。

1. *PowerShell ISE* で ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* を開き、次の値を設定します。
   * **$DemoScenario** = 2、**アドホック分析データベースをデプロイする**。

2. **F5** キーを押してスクリプトを実行し、*adhocanalytics* データベースを作成します。

次のセクションでは、分散クエリの実行に利用できるように、データベースにスキーマを追加します。

## <a name="configure-the-database-for-running-distributed-queries"></a>分散クエリを実行するためのデータベースを構成する

この演習では、テナント データベース全体でクエリ実行を可能にするアドホック分析データベースにスキーマ (外部データ ソースと外部テーブル定義) を追加します。

1. SQL Server Management Studio を開き、前の手順で作成したアドホック分析データベースに接続します。 データベースの名前は adhocanalytics になります。
2. SSMS で、...\Learning Modules\Operational Analytics\Adhoc Analytics\ *Initialize-AdhocAnalyticsDB.sql* を開きます。
3. SQL スクリプトを読み、次の点に注意します。

   エラスティック クエリは、データベース スコープの資格情報を使用して、各テナント データベースにアクセスします。 この資格情報は、すべてのデータベースで使用できる必要があり、通常、これらのアドホック クエリを有効にするために必要な最小限の権限が付与されている必要があります。

    ![資格情報を作成する](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   カタログ データベースでテナント シャード マップを使用するように定義されている外部データ ソース。 これを外部データ ソースとして使用すると、クエリが実行されたとき、カタログに登録されているすべてのデータベースにクエリが分散されます。 デプロイごとにサーバー名が異なるため、この初期化スクリプトでは、スクリプトが実行された現在のサーバー (@@servername) を検索することでカタログ サーバーの場所を取得します。

    ![外部データ ソースを作成する](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   外部テーブルは、前のセクションで説明したグローバル ビューを参照します。**DISTRIBUTION = SHARDED(VenueId)** で定義されています。 各 *VenueId* は 1 つのデータベースにマッピングされるため、次のセクションで示すように、多くのシナリオでパフォーマンスが改善されます。

    ![外部テーブルを作成する](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   作成され、データが設定されているローカル テーブル *VenueTypes*。 この参照データ テーブルは、すべてのテナント データベースで共通です。ここではローカル テーブルとして表すことができます。また、共通データを入力できます。 クエリによっては、テナント データベースと *adhocanalytics* データベースの間で移動するデータ量が減ることがあります。

    ![テーブルを作成する](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   この方法で参照テーブルを追加する場合、テナント データベースを更新するときは必ず、テーブルのスキーマとデータを更新します。

4. **F5** キーを押してスクリプトを実行し、*adhocanalytics* データベースを初期化します。 

これで分散クエリを実行し、すべてのテナントを対象に洞察を収集できます。

## <a name="run-ad-hoc-distributed-queries"></a>アドホック分散クエリを実行する

これで *adhocanalytics* データベースが設定されたので、分散クエリをいくつか実行します。 クエリ処理の場所をより良く理解できるように実行プランを含めます。 

実行プランを調べるとき、プラン アイコンにカーソルを合わせると詳細が表示されます。 

外部データ ソースの定義時に **DISTRIBUTION = SHARDED(VenueId)** を設定したことで、多くのシナリオでパフォーマンスが改善します。これは重要なことなのでご留意ください。 各 *VenueId* は 1 つのデータベースにマッピングされるため、フィルター処理はリモートで簡単に実行されます。必要なデータのみが返されます。

1. SSMS で ....\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* を開きます。
2. **adhocanalytics** データベースに接続していることを確認します。
3. **[クエリ]** メニューを選択し、**[実際の実行プランを含める]** をクリックします。
4. *[Which venues are currently registered?]* クエリを強調表示し、**F5** を押します。

   クエリは会場一覧全体を返します。すべてのテナントにクエリを実行し、各テナントからデータを返すことがいかに簡単かわかります。

   プランを調べ、コスト全体がリモート クエリであることを確認します。各テナント データベースに進み、会場情報を選択しているためです。

   ![SELECT * FROM dbo.Venues](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. 次のクエリを選択し、**F5** を押します。

   このクエリは、テナント データベースとローカル *VenueTypes* テーブル (*adhocanalytics* データベースのテーブルであり、ローカル) のデータを結合します。

   プランを調べ、コストの大半がリモート クエリであることを確認します。各テナントの会場情報 (dbo.Venues) を問い合わせ、ローカル *VenueTypes* テーブルでクイック ローカル結合を行い、フレンドリ名を表示しているためです。

   ![リモートとローカルのデータで結合](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. 次に *On which day were the most tickets sold?* クエリを選択し、**F5** を押します。

   このクエリは、少しばかり複雑な結合と集計を行います。 重要な点は、処理の大半がリモートで行われるということです。再度、必要な行だけ返します。各会場で 1 日に売り上げたチケットの数を対象に 1 行だけ返します。

   ![クエリ](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * すべてのテナント データベースにわたって分散クエリを実行する
> * アドホック分析データベースをデプロイし、それにスキーマを追加し、分散クエリを実行します。


それでは[テナント分析チュートリアル](sql-database-saas-tutorial-tenant-analytics.md)を試してください。個別の分析データベースにデータを抽出することでより複雑な分析を処理できます。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック クエリ](sql-database-elastic-query-overview.md)

