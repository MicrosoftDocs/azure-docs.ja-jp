---
title: 抽出されたデータを使用したクロステナント分析
description: シングル テナント アプリでの複数の Azure SQL データベースから抽出されたデータを使用した、クロステナント分析クエリ。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 98896b5b728a729a29f989b3b9a76f29131af8d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93305972"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>抽出されたデータを使用したクロステナント分析 - シングルテナント アプリ
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
このチュートリアルでは、シングル テナントの実装に関する完全な分析シナリオについて説明します。 シナリオでは、分析によって企業の賢明な意思決定を可能にする方法を示します。 各テナント データベースから抽出されたデータを使用した分析によって、テナントの動作 (Wingtip Tickets SaaS サンプル アプリケーションの使用など) に関する洞察を獲得します。 このシナリオには、次の 3 つの手順が含まれます。 

1.  各テナント データベースから分析ストアにデータを **抽出** して分析ストアに **読み込みます**。
2.  分析処理のために、**抽出されたデータを変換** します。
3.  **ビジネス インテリジェンス** ツールを使用して、意思決定を支援する有益な洞察を引き出します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - データの抽出先となるテナント分析ストアを作成する。
> - エラスティック ジョブを使用して、各テナント データベースから分析ストアにデータを抽出する。
> - 抽出されたデータを最適化する (スター スキーマに再編成する)。
> - 分析データベースを照会する。
> - データ視覚化に Power BI を使用して、テナント データの傾向を強調表示し、改善のための推奨事項を提案する。

![この記事で使用されているアーキテクチャの概要を示す図。](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>オフライン テナント分析パターン

マルチテナント SaaS アプリケーションには、通常は、クラウドに保存された膨大な量のテナント データがあります。 このデータから、アプリケーションの操作と使用状況や、テナントの動作に関するさまざまな洞察が得られます。 これらの洞察は、機能の開発、操作性の改善、アプリとプラットフォームへのその他の投資の指針となります。

すべてのデータが 1 つのマルチテナント データベースに存在する場合は、すべてのテナントのデータに簡単にアクセスできます。 しかし、何千もの可能性があるデータベースに分散している場合、アクセスは複雑になります。 複雑さを軽減し、トランザクション データに対する分析クエリの影響を最小限に押さえる 1 つの方法は、目的に合うように設計された分析データベースまたはデータ ウェアハウスにデータを抽出することです。

このチュートリアルでは、Wingtip Tickets SaaS アプリケーション用の完全な分析シナリオを紹介します。 最初に、*エラスティック ジョブ* を使用して、各テナント データベースからデータを抽出し、それを分析ストア内のステージング テーブルに読み込みます。 分析ストアには、SQL Database または専用 SQL プールを使用できます。 大規模なデータ抽出には、[Azure Data Factory](../../data-factory/introduction.md) が推奨されます。

次に、集計データを一連の[スター スキーマ](https://www.wikipedia.org/wiki/Star_schema) テーブルに変換します。 これらのテーブルは、中央のファクト テーブルと関連するディメンション テーブルで構成されます。  Wingtip Tickets では:

- スター スキーマの中央のファクト テーブルには、チケット データが含まれます。
- ディメンション テーブルは、会場、イベント、顧客、および購入日に関するデータを記述します。

中央のファクト テーブルとディメンション テーブルを組み合わせることで、効率的な分析処理が可能になります。 このチュートリアルで使用するスター スキーマを次の図に示します。
 
![architectureOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

最後に、**Power BI** を使用して分析ストアに対してクエリが実行され、テナントの動作と Wingtip Tickets アプリケーションの使用に関する分析情報が強調表示されます。 以下を行うクエリを実行します。
 
- 各会場の相対的な人気を示す
- さまざまなイベントのチケットの売上パターンを強調表示する
- 複数の会場でのイベントの販売の相対的な成功を表示する

各テナントによるサービスの使用方法に関する理解を用いて、サービスを収益化し、テナントの成功を支援するようにサービスを改善するためのオプションを調べます。 このチュートリアルでは、テナント データから得られる洞察の種類の基本的な例を示します。

## <a name="setup"></a>セットアップ

### <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- Wingtip Tickets SaaS Database Per Tenant アプリケーションをデプロイします。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](./saas-dbpertenant-get-started-deploy.md)に関するページを参照してください。
- Wingtip Tickets SaaS Database Per Tenant のスクリプトとアプリケーション [ソース コード](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)を GitHub からダウンロードします。 ダウンロードの手順を参照してください。 内容を抽出する前に、必ず *ZIP ファイルのブロックを解除* してください。 Wingtip Tickets SaaS スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。
- Power BI Desktop をインストールします。 [Power BI Desktop のダウンロード](https://powerbi.microsoft.com/downloads/)
- 追加のテナントのバッチをプロビジョニングします。[**テナントのプロビジョニングに関するチュートリアル**](./saas-dbpertenant-provision-and-catalog.md)をご覧ください。
- ジョブ アカウントとジョブ アカウント データベースを作成します。 適切な手順については、[**スキーマ管理に関するチュートリアル**](./saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)をご覧ください。

### <a name="create-data-for-the-demo"></a>デモ用のデータを作成する

このチュートリアルでは、チケット売上データで分析を実行します。 現在の手順では、すべてのテナントのチケット データを生成します。  後で、分析用にこのデータを抽出します。 *前述のようにテナントのバッチをプロビジョニングし、相当量のデータを確保してください*。 十分な量のデータがあれば、さまざまなチケット購入パターンを明らかにすることができます。

1. PowerShell ISE で *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* を開き、次の値を設定します。
    - **$DemoScenario** = **1**: すべての会場でイベントのチケットを購入
2. **F5** キーを押してスクリプトを実行し、各会場のすべてのイベントのチケット購入履歴を作成します。  スクリプトが数分間実行され、数万枚のチケットが生成されます。

### <a name="deploy-the-analytics-store"></a>分析ストアをデプロイする
多くの場合、すべてのテナント データを保持する多数のトランザクション データベースが存在します。 多数のトランザクション データベースのテナント データを、1 つの分析ストアに集約する必要があります。 集約により、データの効率的なクエリが可能になります。 このチュートリアルでは、Azure SQL Database を使用して集約されたデータを格納します。

次の手順では、**tenantanalytics** という分析ストアをデプロイします。 このチュートリアルで後ほど設定する、定義済みのテーブルもデプロイします。
1. PowerShell ISE で *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* を開きます。 
2. 選択した分析ストアに合わせて、スクリプト内の $DemoScenario 変数を設定します。
    - 列ストアを使用せずに SQL Database を使用するには、 **$DemoScenario** = **2** を設定します
    - 列ストアを使用して SQL Database を使用するには、 **$DemoScenario** = **3** を設定します  
3. **F5** キーを押して、テナント分析ストアを作成するデモ スクリプト (*Deploy-TenantAnalytics\<XX>.ps1* スクリプトを呼び出すスクリプト) を実行します。 

アプリケーションをデプロイし、対象のテナント データを入力しました。次に、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、**tenants1-dpt-&lt;User&gt;** サーバーと **catalog-dpt-&lt;User&gt;** サーバーに接続します。ログイン名として「*developer*」、パスワードとして「*P\@ssword1*」を使用します。 詳しいガイダンスについては、[入門チュートリアル](./saas-dbpertenant-wingtip-app-overview.md)をご覧ください。

![SQL Server への接続に必要な情報を示すスクリーンショット。](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

オブジェクト エクスプローラーで次の手順を実行します。

1. *tenants1-dpt-&lt;User&gt;* サーバーを展開します。
2. [データベース] ノードを展開し、テナント データベースの一覧を表示します。
3. *catalog-dpt-&lt;User&gt;* サーバーを展開します。
4. 分析ストアと jobaccount データベースが表示されていることを確認します。

SSMS オブジェクト エクスプローラーで分析ストア ノードを展開して、次のデータベース項目を表示します。

- **TicketsRawData** テーブルと **EventsRawData** テーブルには、テナント データベースから抽出した生データが保持されます。
- スター スキーマ テーブルは、**fact_Tickets**、**dim_Customers**、**dim_Venues**、**dim_Events**、**dim_Dates** です。
- ストアド プロシージャは、生データ テーブルからスター スキーマ テーブルを設定するために使用します。

![SSMS オブジェクト エクスプローラーに表示されているデータベース項目のスクリーンショット。](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>データの抽出 

### <a name="create-target-groups"></a>ターゲット グループを作成する 

先に進む前に、ジョブ アカウントと jobaccount データベースがデプロイされていることを確認します。 次の一連の手順では、エラスティック ジョブを使用して各テナント データベースからデータを抽出し、データを分析ストアに保存します。 次に、2 番目のジョブでデータを細分化し、スター スキーマの各テーブルに格納します。 この 2 つのジョブは、それぞれ **TenantGroup**、**AnalyticsGroup** という名前の 2 つの異なるターゲット グループに対して実行されます。 抽出ジョブは、すべてのテナント データベースが含まれた TenantGroup に対して実行されます。 細分化ジョブは、分析ストアだけが含まれた AnalyticsGroup に対して実行されます。 次の手順に従って、ターゲット グループを作成します。

1. SSMS で、catalog-dpt-&lt;User&gt; の **jobaccount** データベースに接続します。
2. SSMS で *…\Learning Modules\Operational Analytics\Tenant Analytics\TargetGroups.sql* を開きます。 
3. スクリプトの先頭の @User 変数を変更し、`<User>` を Wingtip SaaS アプリのデプロイ時に使用したユーザー値に置き換えます。
4. **F5** キーを押してスクリプトを実行し、2 つのターゲット グループを作成します。

### <a name="extract-raw-data-from-all-tenants"></a>すべてのテナントから生データを抽出する

"*チケット/顧客*" データでは、"*イベント/会場*" データよりも頻繁に大幅なデータ変更が発生すると考えられます。 そのため、チケット/顧客データは、イベント/会場データよりも頻繁に別途抽出することを検討します。 このセクションでは、次の 2 つのジョブを定義し、スケジュールします。

- チケット/顧客データを抽出する。
- イベント/会場データを抽出する。

各ジョブでは、データを抽出して分析ストアに送信します。 別のジョブで、抽出されたデータを分析スター スキーマに細分化します。

1. SSMS で、catalog-dpt-&lt;User&gt; サーバーの **jobaccount** データベースに接続します。
2. SSMS で *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql* を開きます。
3. スクリプトの先頭の @User を変更し、`<User>` を Wingtip SaaS アプリのデプロイ時に使用したユーザー名に置き換えます。 
4. F5 キーを押してスクリプトを実行します。このスクリプトにより、各テナント データベースからチケット データと顧客データを抽出するジョブが作成され、実行されます。 このジョブはデータを分析ストアに保存します。
5. tenantanalytics データベースの TicketsRawData テーブルを照会して、テーブルにすべてのテナントのチケット情報が設定されていることを確認します。

![ExtractTickets データベースを示すスクリーンショット。オブジェクト エクスプローラーで TicketsRawData dbo が選択されています。](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

手順 2. の **\ExtractTickets.sql** を **\ExtractVenuesEvents.sql** に置き換えて、上記の手順を繰り返します。

ジョブが正常に実行されると、分析ストアの EventsRawData テーブルに、すべてのテナントの新しいイベントと会場の情報が設定されます。 

## <a name="data-reorganization"></a>データの再編成

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>抽出されたデータを細分化してスター スキーマ テーブルを設定する

次に、抽出された生データを、分析クエリに最適化された一連のテーブルに細分化します。 スター スキーマを使用します。 中央のファクト テーブルには、個々のチケット売上レコードが保持されます。 他のテーブルには、会場、イベント、顧客の関連データが設定されます。 また、時間ディメンション テーブルがあります。 

このセクションでは、抽出された生データをスター スキーマ テーブルのデータとマージするジョブを定義して実行します。 マージ ジョブが完了すると、生データが削除され、テーブルは次回のテナント データ抽出ジョブで設定できる状態になります。

1. SSMS で、catalog-dpt-&lt;User&gt; の **jobaccount** データベースに接続します。
2. SSMS で *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql* を開きます。
3. **F5** キーを押してスクリプトを実行します。このスクリプトにより、分析ストアで sp_ShredRawExtractedData ストアド プロシージャを呼び出すジョブが定義されます。
4. ジョブを正常に実行するための十分な時間を確保します。
    - jobs.jobs_execution テーブルの **Lifecycle** 列でジョブの状態を確認します。 先に進む前に、ジョブが **成功** したことを確認します。 ジョブが正常に完了すると、次の図のようにデータが表示されます。

![細分化](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>データの探索

### <a name="visualize-tenant-data"></a>テナント データを視覚化する

スター スキーマ テーブルのデータは、分析に必要なすべてのチケット売上データを提供します。 大規模なデータセットで傾向をわかりやすくするには、グラフを使用してデータを視覚化する必要があります。  このセクションでは、**Power BI** を使用して、抽出して整理したテナント データを操作し、視覚化する方法について説明します。

次の手順に従って、Power BI に接続し、以前に作成したビューをインポートします。

1. Power BI Desktop を起動します。
2. [ホーム] リボンの **[データを取得]** をクリックし、メニューの **[その他…]** を選択します。
3. **[データを取得]** ウィンドウで、[Azure SQL Database] を選択します。
4. データベース ログイン ウィンドウで、サーバー名 (catalog-dpt-&lt;User&gt;.database.windows.net) を入力します。 **[データ接続モード]** の **[インポート]** を選択し、[OK] をクリックします。 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. 左側のウィンドウで **[データベース]** を選択し、ユーザー名として「*developer*」、パスワードとして「*P\@ssword1*」を入力します。 **[Connect]** をクリックします。  

    ![[SQL Server データベース] ダイアログを示すスクリーンショット。ここでは、ユーザー名とパスワードを入力できます。](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. **ナビゲーター** ウィンドウで、分析データベースのスター スキーマ テーブル (fact_Tickets、dim_Events、dim_Venues、dim_Customers、dim_Dates) を選択します。 **[読み込み]** を選択します。 

お疲れさまでした。 Power BI にデータが正常に読み込まれました。 これで、テナントに関する洞察を得るために、興味のある視覚化機能の探索を開始できます。 次に、分析を使用して、データ ドリブンの推奨事項を Wingtip Tickets ビジネス チームに提供する方法について説明します。 これらの推奨事項は、ビジネス モデルとカスタマー エクスペリエンスの最適化に役立ちます。

まず、チケット売上データを分析して、各会場の利用状況の差異を確認します。 Power BI で次のオプションを選択して、各会場で販売されたチケットの総数を示す棒グラフをプロットします。 チケット ジェネレーターのランダムな変動により、結果が異なる場合があります。
 
![右側のデータ視覚化での Power BI の視覚化と制御を示すスクリーンショット。](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

上記のプロットでは、各会場で販売されたチケットの数にばらつきがあることを確認できます。 チケットの販売数が多い会場は、販売数が少ない会場よりもサービスを頻繁に利用しています。 これは、テナントのさまざまなニーズに応じてリソースの割り当てを調整するよい機会かもしれません。

データをさらに分析して、チケットの売上の経時的な変化を確認できます。 Power BI で次のオプションを選択して、60 日間にわたり、1 日に販売されたチケットの総数をプロットします。
 
![「Ticket Sale Distribution (チケット販売分布) と Sale Day (販売日)」というタイトルの Power BI の視覚化を示すスクリーンショット。](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

上記のグラフは、一部の会場のチケットの販売数がスパイク (突出) していることを示しています。 これらのスパイクは、一部の会場がシステム リソースを過度に使用している可能性があるという考えを補強しています。 今のところ、スパイクが発生したときの明白なパターンはありません。

次に、これらのピークの販売日の有意性をさらに調査します。 チケットの発売後、これらのピークが発生したのはいつでしょうか。 1 日に販売されたチケット数をプロットするには、Power BI で次のオプションを選択します。

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

上記のプロットは、一部の会場が販売初日に多数のチケットを販売したことを示しています。 これらの会場でチケットが発売されるとすぐに、購入が殺到したようです。 少数の会場でのこのアクティビティのバーストが、他のテナントのサービスに影響を及ぼしている可能性があります。

データを再度掘り下げて、この購入が殺到する状態が、これらの会場主催のすべてのイベントに当てはまるかどうかを確認します。 これまでのプロットで、Contoso Concert Hall が多数のチケットを販売しており、特定の日にチケットの販売数が急増していることがわかりました。 Power BI のオプションを操作して、Contoso Concert Hall のチケットの累計販売数をプロットし、各イベントの販売動向に注目します。 すべてのイベントが同じ販売パターンに従っているのでしょうか。

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Contoso Concert Hall の上記のプロットは、購入が殺到する状態がすべてのイベントで発生しているわけではないことを示しています。 フィルター オプションを操作して、他の会場の販売動向を確認します。

チケット販売パターンに関する洞察により、Wingtip Tickets でビジネス モデルを最適化できます。 Wingtip では、すべてのテナントに均等に課金するのではなく、コンピューティング サイズが異なるサービス レベルを導入する必要があると考えられます。 1 日により多くのチケットを販売する必要がある大規模な会場には、サービス レベル アグリーメント (SLA) が高い上位層を提供できます。 これらの会場では、データベースごとのリソースの上限が高いプールにデータベースを配置できます。 各サービス層に時間単位の販売割り当てを設定し、割り当てを超えた場合は追加料金が課金されるようにすることもできます。 売上が定期的に激増する大規模な会場は上位層からメリットが得られ、Wingtip Tickets はサービスをより効率的に収益化できます。

その一方で、Wingtip Tickets の一部の顧客は、サービス コストに見合うだけのチケットを販売するのに苦戦していると不満を漏らしています。 これらの洞察に、業績が低迷している会場のチケットの売上を伸ばす機会がおそらくあります。 売上が増加すれば、サービスの知覚価値が高まります。 fact_Tickets を右クリックし、 **[新しいメジャー]** を選択します。 **AverageTicketsSold** という新しいメジャーの次の式を入力します。

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

相対的な成果を確認するために、次の視覚化オプションを選択して、各会場で販売されたチケットの割合をプロットします。

![「Average Tickets Sold By Each Venue (会場別チケット平均販売数)」というタイトルの Power BI の視覚化を示すスクリーンショット。](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

上記のプロットは、ほとんどの会場がチケットの 80% 以上を販売しているにもかかわらず、座席の半分以上を埋めるのに苦労している会場もあることを示しています。 Values Well を操作して、各会場で販売されたチケットの最大または最小の割合を選択します。

以前に、分析を深めることによって、チケットの販売数は予測可能なパターンに従う傾向があることを発見しました。 この発見により、Wingtip Tickets は動的価格設定を推奨することで、業績が低迷している会場がチケットの売上を伸ばせるよう支援できる可能性があります。 この発見により、機械学習の手法を使用して各イベントのチケットの売上を予測する機会を明らかにすることもできます。 チケットの販売時に割引を適用することによる収益への影響を予測することも可能です。 Power BI Embedded をイベント管理アプリケーションに統合することができます。 統合により、予測される売上とさまざまな割引の影響を視覚化できます。 アプリケーションを活用して、分析画面から直接適用される最適な割引を考案できます。

WingTip アプリケーションのテナント データの傾向を確認しました。 アプリケーションが SaaS アプリケーション ベンダーのビジネス上の意思決定に役立つ情報を提供する他の方法を検討することができます。 ベンダーは、テナントのニーズに適切に応えることができます。 このチュートリアルでは、テナント データで分析を実行するために必要なツールを活用して、企業がデータ ドリブンの意思決定を行うことができるようにしました。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> - 定義済みのスター スキーマ テーブルを含むテナント分析データベースをデプロイする
> - エラスティック ジョブを使用して、すべてのテナント データベースからデータを抽出する
> - 抽出されたデータを分析用に設計されたスター スキーマの各テーブルにマージする
> - 分析データベースを照会する 
> - データ視覚化に Power BI を使用して、テナント データの傾向を確認する 

お疲れさまでした。

## <a name="additional-resources"></a>その他のリソース

- [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- [エラスティック ジョブ](./elastic-jobs-overview.md)
- [抽出されたデータを使用したクロステナント分析 - マルチテナント アプリ](./saas-multitenantdb-tenant-analytics.md)
