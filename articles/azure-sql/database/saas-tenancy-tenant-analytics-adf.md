---
title: テナント データベースに対する分析クエリの実行
description: Azure SQL Database、Azure Synapse Analytics、Azure Data Factory、または Power BI から抽出されたデータを使用した、クロステナント分析クエリについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: dc47c996748b126841cbeff1ea3f6f18f423951f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96457650"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Azure SQL Database、Azure Synapse Analytics、Data Factory、Power BI による SaaS 分析を調べる
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、分析シナリオについて最初から最後まで説明します。 このシナリオでは、テナント データを分析することでソフトウェア ベンダーのスマートな意思決定能力がどのように向上するのかを説明します。 各テナント データベースから抽出されたデータを使用した分析によって、テナントの動作 (Wingtip Tickets SaaS サンプル アプリケーションの使用など) に関する洞察を獲得します。 このシナリオには、次の 3 つの手順が含まれます。

1. 各テナント データベースから分析ストア (この場合は専用 SQL プール) に **データを抽出** します。
2. 分析処理のために、**抽出されたデータを最適化** します。
3. **ビジネス インテリジェンス** ツールを使用して、意思決定を支援する有益な洞察を引き出します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> - 読み込み用のテナント分析ストアを作成する。
> - Azure Data Factory (ADF) を使用して、各テナント データベースから分析データ ウェアハウスにデータを抽出する。
> - 抽出されたデータを最適化する (スター スキーマに再編成する)。
> - 分析データ ウェアハウスのクエリを実行する。
> - データ視覚化に Power BI を使用して、テナント データの傾向を強調表示し、改善のための推奨事項を提案する。

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>抽出されたテナント データに対する分析

SaaS アプリケーションは、クラウドに膨大な量のテナント データを保持する可能性があります。 このデータから、アプリケーションの操作と使用状況や、テナントの動作に関するさまざまな分析情報が得られます。 これらの分析情報は、機能の開発、操作性の改善、アプリとプラットフォームへのその他の投資の指針となります。

すべてのデータが 1 つのマルチテナント データベースに存在する場合は、すべてのテナントのデータに簡単にアクセスできます。 しかし、何千ものデータベースに分散している場合、アクセスは複雑になります。 この複雑さを軽減する 1 つの方法として、クエリ用の分析データベースまたはデータ ウェアハウスにデータを抽出します。

このチュートリアルでは、Wingtip Tickets アプリケーションのエンド ツー エンドの分析シナリオを紹介します。 最初に、[Azure Data Factory (ADF)](../../data-factory/introduction.md) をオーケストレーション ツールとして使って、各テナント データベースからチケットの販売および関連するデータを抽出します。 このデータは、分析ストアのステージング テーブルに読み込まれます。 分析ストアには、SQL Database または専用 SQL プールを使用できます。 このチュートリアルでは、分析ストアとして [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) を使用します。

次に、抽出されたデータを変換して一連の[スター スキーマ](https://www.wikipedia.org/wiki/Star_schema) テーブルに読み込みます。 これらのテーブルは、中央のファクト テーブルと関連するディメンション テーブルで構成されます。

- スター スキーマの中央のファクト テーブルには、チケット データが含まれます。
- ディメンション テーブルには、会場、イベント、顧客、購入日に関するデータが含まれます。

中央のテーブルとディメンション テーブルを組み合わせることで、効率的な分析処理が可能になります。 このチュートリアルで使用するスター スキーマを次の図に示します。

![このチュートリアルで使用されるスター スキーマを示す図。](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

最後に、スター スキーマ テーブルを照会します。 Power BI を使ってクエリ結果が視覚的に表示され、テナントの動作とアプリケーションの使用に関する分析情報が強調表示されます。 このスター スキーマで、次のことがわかるクエリを実行します。

- 誰がどの会場でチケットを購入しているか
- チケットの販売のパターンや傾向
- 各会場の相対的な人気

このチュートリアルでは、Wingtip Tickets のデータから得られる分析情報の基本的な例を示します。 たとえば、各会場のサービスの使用方法を理解すると、Wingtip Tickets ベンダーは、だいたい稼働している会場を対象とする異なるサービス プランを検討する可能性があります。

## <a name="setup"></a>セットアップ

### <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- Wingtip Tickets SaaS Database Per Tenant アプリケーションをデプロイします。 5 分未満でデプロイする方法については、[Wingtip SaaS アプリケーションのデプロイと確認](./saas-dbpertenant-get-started-deploy.md)に関するページを参照してください。
- Wingtip Tickets SaaS Database Per Tenant のスクリプトとアプリケーション [ソース コード](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)を GitHub からダウンロードします。 ダウンロードの手順を参照してください。 内容を抽出する前に、必ず *ZIP ファイルのブロックを解除* してください。
- Power BI Desktop をインストールします。 [Power BI Desktop をダウンロードします](https://powerbi.microsoft.com/downloads/)。
- 追加のテナントのバッチをプロビジョニングします。[**テナントのプロビジョニングに関するチュートリアル**](./saas-dbpertenant-provision-and-catalog.md)をご覧ください。

### <a name="create-data-for-the-demo"></a>デモ用のデータを作成する

このチュートリアルでは、チケット販売データの分析を調べます。 この手順では、すべてのテナントのチケット データを生成します。 後の手順で、分析用にこのデータを抽出します。 ある程度の範囲の異なるチケット購入パターンが示されるのに十分なデータがあるように、(前述のように) "_テナントのバッチをプロビジョニングしてください_"。

1. PowerShell ISE で *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* を開き、次の値を設定します。
    - **$DemoScenario** = **1**: すべての会場でイベントのチケットを購入
2. **F5** キーを押してスクリプトを実行し、すべての会場のチケット購入履歴を作成します。 20 のテナントについて、スクリプトは数万件のチケットを生成し、10 分以上かかる場合があります。

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Azure Synapse Analytics、Data Factory、Blob Storage をデプロイする

Wingtip Tickets アプリでは、テナントのトランザクション データは多くのデータベースに分散されます。 Azure Data Factory (ADF) を使って、データ ウェアハウスへのこのデータの抽出、読み込み、変換 (ELT) を調整します。 最も効率的に Azure Synapse Analytics にデータを読み込むため、ADF は中間 BLOB ファイルにデータを抽出した後、[PolyBase](../../synapse-analytics/sql-data-warehouse/design-elt-data-loading.md) を使ってデータ ウェアハウスにデータを読み込みます。

この手順において、チュートリアルで使われるその他のリソースをデプロイします。つまり、_tenantanalytics_ という名前の専用 SQL プール、_dbtodwload-\<user\>_ という名前の Azure Data Factory、_wingtipstaging\<user\>_ という名前の Azure ストレージ アカウントです。 ストレージ アカウントは、抽出されたデータ ファイルを、データ ウェアハウスに読み込む前に、BLOB として一時的に保持するために使われます。 この手順では、データ ウェアハウス スキーマもデプロイし、ELT プロセスを調整する ADF パイプラインを定義します。

1. PowerShell ISE で *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* を開き、次のように設定します。
    - **$DemoScenario** = **2**: テナント分析データ ウェアハウス、BLOB ストレージ、データ ファクトリをデプロイする
1. **F5** キーを押してデモ スクリプトを実行し、Azure リソースをデプロイします。

次に、デプロイした Azure リソースを確認します。

#### <a name="tenant-databases-and-analytics-store"></a>テナント データベースと分析ストア

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使って、**tenants1-dpt-&lt;ユーザー&gt;** および **catalog-dpt-&lt;ユーザー&gt;** サーバーに接続します。 &lt;ユーザー&gt; は、アプリをデプロイしたときに使った値に置き換えます。 ログインには *developer* を、パスワードには *P\@ssword1* を使います。 詳しいガイダンスについては、[入門チュートリアル](./saas-dbpertenant-wingtip-app-overview.md)をご覧ください。

![SSMS から SQL Database に接続する](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

オブジェクト エクスプローラーで次のようにします。

1. *tenants1-dpt-&lt;ユーザー&gt;* サーバーを展開します。
1. [データベース] ノードを展開し、テナント データベースの一覧を表示します。
1. *catalog-dpt-&lt;ユーザー&gt;* サーバーを展開します。
1. 次のオブジェクトを含む分析ストアが表示されることを確認します。
    1. テーブル **raw_Tickets**、**raw_Customers**、**raw_Events**、**raw_Venues** は、テナント データベースから抽出された生のデータを保持します。
    1. スター スキーマ テーブルは、**fact_Tickets**、**dim_Customers**、**dim_Venues**、**dim_Events**、**dim_Dates** です。
    1. ストアド プロシージャ **sp_transformExtractedData** は、データを変換してスター スキーマ テーブルに読み込むために使われます。

![オブジェクト エクスプローラーを示すスクリーンショット。テーブルが展開され、さまざまなデータベース オブジェクトが表示されています。](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>BLOB ストレージ

1. [Azure portal](https://ms.portal.azure.com) で、アプリケーションのデプロイに使ったリソース グループに移動します。 **wingtipstaging\<user\>** というストレージ アカウントが追加されていることを確認します。

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. **wingtipstaging\<user\>** ストレージ アカウントをクリックして、存在するオブジェクトを調べます。
1. **[BLOB]** タイルをクリックします
1. **configfile** コンテナーをクリックします
1. **configfile** コンテナーに JSON ファイル **TableConfig.json** が含まれることを確認します。 このファイルには、コピー元とコピー先のテーブル名、列名、およびトラッカー列名が含まれています。

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

[Azure portal](https://ms.portal.azure.com) のリソース グループで、_dbtodwload-\<user\>_ という名前の Azure Data Factory が追加されていることを確認します。

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

このセクションで、作成されたデータ ファクトリを調べます。
以下の手順でデータ ファクトリを起動します。

1. ポータルで、**dbtodwload-\<user\>** という名前のデータ ファクトリをクリックします。
2. **[作成と監視]** タイルをクリックして、別のタブで Data Factory デザイナーを起動します。

## <a name="extract-load-and-transform-data"></a>データの抽出、読み込み、変換

Azure Data Factory は、データの抽出、読み込み、変換の調整に使われます。 このチュートリアルでは、**rawTickets**、**rawCustomers**、**rawEvents**、**rawVenues** の各テナント データベースからの 4 つの異なる SQL ビューからデータを抽出します。 これらのビューには会場 ID が含まれるので、データ ウェアハウス内の各会場からのデータを識別できます。 データは、データ ウェアハウス内の対応するステージング テーブル **raw_Tickets**、**raw_customers**、**raw_Events**、**raw_Venue** に読み込まれます。 その後、ストアド プロシージャが生データを変換し、スター スキーマ テーブル **fact_Tickets**、**dim_Customers**、**dim_Venues**、**dim_Events**、および **dim_Dates** を設定します。

前のセクションでは、データ ファクトリを含む、必要な Azure リソースをデプロイして初期化しました。 デプロイされたデータ ファクトリには、テナント データの抽出、読み込み、変換に必要なパイプライン、データセット、リンクされたサービスなどが含まれます。 これらのオブジェクトをさらに詳しく調べた後、パイプラインをトリガーして、テナント データベースからデータ ウェアハウスにデータを移動します。

### <a name="data-factory-pipeline-overview"></a>Data Factory パイプラインの概要

このセクションでは、データ ファクトリに作成されたオブジェクトを調べます。 次の図では、このチュートリアルで使われる ADF パイプラインの全体的なワークフローについて説明します。 パイプラインを調べるのは後にして、先に結果を見たい場合は、次の「**パイプラインの実行をトリガーする**」に進んでください。

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

概要ページで左側の **[作成者]** タブに切り替え、3 つの [パイプライン](../../data-factory/concepts-pipelines-activities.md)と 3 つの [データセット](../../data-factory/concepts-datasets-linked-services.md)が作成されていることを確認します。
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

入れ子になった 3 つのパイプラインは、SQLDBToDW、DBCopy、および TableCopy です。

**パイプライン 1 - SQLDBToDW** は、カタログ データベース (テーブル名: [__ShardManagement].[ShardsGlobal]) に格納されているテナント データベースの名前を調べて、テナント データベースごとに、**DBCopy** パイプラインを実行します。 完了すると、提供されている **sp_TransformExtractedData** ストアド プロシージャのスキーマが実行されます。 このストアド プロシージャは、ステージング テーブルに読み込まれたデータを変換し、スター スキーマのテーブルに設定します。

**パイプライン 2 - DBCopy** は、BLOB ストレージに格納されている構成ファイルから、ソース テーブルと列の名前を調べます。  その後、**TableCopy** パイプラインが TicketFacts、CustomerFacts、EventFacts、および VenueFacts の 4 つの各テーブルに対して実行されます。 **[Foreach](../../data-factory/control-flow-for-each-activity.md)** アクティビティが、20 個のデータベースすべてに対して並列に実行します。 ADF では、最大 20 のループ イテレーションが並列に実行できます。 データベースを増やす場合は、複数のパイプラインを作成することを検討します。

**パイプライン 3 - TableCopy** は、SQL Database の行バージョン番号 (_rowversion_) を使って、変更または更新された行を識別します。 このアクティビティは、ソース テーブルから行を抽出するために開始と終了の行バージョンを検索します。 各テナント データベースに格納されている **CopyTracker** テーブルは、各実行において各ソース テーブルから抽出された最後の行を追跡します。 新しい行または変更された行は、データ ウェアハウス内の対応するステージング テーブル **raw_Tickets**、**raw_Customers**、**raw_Venues**、**raw_Events** にコピーされます。 最後に、次の抽出の最初の行バージョンとして使われるために、最後の行バージョンが **CopyTracker** テーブルに保存されます。

また、3 つのリンクされたサービスがパラメーター化されており、データ ファクトリをソース SQL Database、ターゲット専用 SQL プール、および中間 Blob Storage にそれぞれリンクしています。 **[作成者]** タブで **[接続]** をクリックして、次の図のように、リンクされたサービスを調べます。

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

3 つのリンクされたサービスに対応して、パイプライン アクティビティで入力または出力として使用するデータを参照する 3 つのデータセットがあります。 各データセットを調べて、使用されている接続とパラメーターを確認します。 _AzureBlob_ は、ソースとターゲットのテーブルと列、および各ソース内のトラッカー列を含む構成ファイルをポイントしています。
  
### <a name="data-warehouse-pattern-overview"></a>データ ウェアハウスのパターンの概要

Azure Synapse は、テナント データに対して集計を実行するための分析ストアとして使われます。 このサンプルでは、データ ウェアハウスにデータを読み込むには PolyBase が使用されます。 生データが読み込まれるステージング テーブルには、スター スキーマ テーブルに変換された行を追跡するための ID 列があります。 次の図は、読み込みパターンを示しています。![データベース テーブルの読み込みパターンを示す図。](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

緩やかに変化するディメンション (SCD) タイプ 1 のディメンション テーブルが、この例では使用されます。 各ディメンションでは、ID 列を使って代理キーが定義されています。 ベスト プラクティスとして、時間を節約するため日付ディメンション テーブルはあらかじめ設定されています。 他のディメンション テーブルについては、CREATE TABLE AS SELECT...(CTAS) ステートメントを使って、既存の変更された行と変更されていない行および代理キーを格納する一時テーブルが作成されます。 これは、IDENTITY_INSERT=ON で行われます。 その後、新しい行が IDENTITY_INSERT=OFF でテーブルに挿入されます。 ロールバックが簡単なように、既存のディメンション テーブルの名前が変更された後、一時テーブルの名前が変更されて新しいディメンション テーブルになります。 各実行の前に、古いディメンション テーブルが削除されます。

ファクト テーブルの前にディメンション テーブルが読み込まれます。 このシーケンスにより、到着する各ファクトについて、参照されているすべてのディメンションが既に存在することが保証されます。 ファクトが読み込まれると、対応する各ディメンションのビジネス キーが照合されて、対応する代理キーが各ファクトに追加されます。

変換の最後の手順では、パイプラインの次の実行のためにステージング データが削除されます。

### <a name="trigger-the-pipeline-run"></a>パイプラインの実行をトリガーする

次の手順に従って、抽出、読み込み、変換の完全なパイプラインをすべてのテナント データベースに対して実行します。

1. ADF ユーザー インターフェイスの **[作成者]** タブで、左側のウィンドウから **SQLDBToDW** パイプラインを選択します。
1. **[トリガー]** をクリックし、プルダウン メニューから **[Trigger Now]\(今すぐトリガー\)** をクリックします。 このアクションはパイプラインをすぐに実行します。 運用シナリオでは、スケジュールに基づいてデータを更新するパイプライン実行タイムテーブルを定義します。
  ![SQLDBToDW という名前のパイプラインのファクトリ リソースを示すスクリーンショット。[トリガー] オプションが展開され、[Trigger Now]\(今すぐトリガー\) が選択されています。](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. **[Pipeline Run]\(パイプライン実行\)** ページで、 **[完了]** をクリックします。

### <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. ADF ユーザー インターフェイスで左側のメニューから **[Monitor]\(監視\)** タブに切り替えます。
1. SQLDBToDW パイプラインの状態が **[成功]** になるまで **[更新]** をクリックします。
  ![SQLDBToDW パイプラインを示すスクリーンショット。[成功] の状態になっています。](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. SSMS でデータ ウェアハウスに接続し、スター スキーマ テーブルのクエリを行って、これらのテーブルにデータが読み込まれたことを確認します。

パイプラインが完了すると、ファクト テーブルにはすべての会場のチケット販売データが保持されており、ディメンション テーブルには対応する会場、イベント、顧客が設定されています。

## <a name="data-exploration"></a>データ探索

### <a name="visualize-tenant-data"></a>テナント データを視覚化する

スター スキーマ内のデータは、分析に必要なすべてのチケット販売データを提供します。 データをグラフィックに視覚化すると、大きなデータ セットでの傾向を把握しやすくなります。 このセクションでは、**Power BI** を使って、データ ウェアハウス内のテナント データの操作と視覚化を行います。

次の手順に従って、Power BI に接続し、以前に作成したビューをインポートします。

1. Power BI Desktop を起動します。
2. [ホーム] リボンの **[データを取得]** をクリックし、メニューの **[その他…]** を選択します。
3. **[データの取得]** ウィンドウで、 **[Azure SQL Database]** を選びます。
4. データベース ログイン ウィンドウで、サーバー名 (**catalog-dpt-&lt;ユーザー&gt;.database.windows.net**) を入力します。 **[データ接続モード]** で **[インポート]** を選び、 **[OK]** をクリックします。

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. 左側のウィンドウで **[データベース]** を選択し、ユーザー名として「*developer*」、パスワードとして「*P\@ssword1*」を入力します。 **[Connect]** をクリックします。  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. **[ナビゲーター]** ウィンドウで、分析データベースのスター スキーマ テーブル **fact_Tickets**、**dim_Events**、**dim_Venues**、**dim_Customers**、**dim_Dates** を選びます。 **[読み込み]** を選択します。

お疲れさまでした。 Power BI にデータを正常に読み込みました。 次に、興味深い視覚化を調べてテナントの分析情報を取得します。 分析が Wingtip Tickets ビジネス チームにデータ駆動型のレコメンデーションを提供する方法を見ていきます。 これらの推奨事項は、ビジネス モデルとカスタマー エクスペリエンスの最適化に役立ちます。

チケット販売データの分析を開始して、会場ごとの使用状況のバリエーションを確認します。 Power BI に表示されるオプションを選択して、各会場で販売されたチケットの合計数の横棒グラフをプロットします (チケット ジェネレーターのランダムなバリエーションにより、結果は異なる場合があります)。

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

上記のプロットでは、各会場で販売されたチケットの数にばらつきがあることを確認できます。 チケットの販売数が多い会場は、販売数が少ない会場よりもサービスを頻繁に利用しています。 これは、テナントのさまざまなニーズに応じてリソースの割り当てを調整するよい機会かもしれません。

データをさらに分析して、チケットの売上の経時的な変化を確認できます。 Power BI の次の画像に示すオプションを選択し、60 日間の日ごとに販売されたチケットの合計数をプロットします。

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

前のグラフでは、一部の会場でチケットの販売が急増していることがわかります。 これらのスパイクは、一部の会場がシステム リソースを過度に使用している可能性があるという考えを補強しています。 今のところ、スパイクが発生したときの明白なパターンはありません。

次に、これらの販売急増日の重要性を調べます。 チケットの発売後、これらのピークが発生したのはいつでしょうか。 日別のチケット販売数をプロットするには、Power BI の次の画像に示すオプションを選択します。

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

このプロットでは、一部の会場では販売初日に多数のチケットが売れていることがわかります。 これらの会場でチケットが発売されるとすぐに、購入が殺到したようです。 少数の会場でのこのアクティビティのバーストが、他のテナントのサービスに影響を及ぼしている可能性があります。

データを再度掘り下げて、この購入が殺到する状態が、これらの会場主催のすべてのイベントに当てはまるかどうかを確認します。 以前のプロットでは、Contoso Concert Hall で多くのチケットが販売されていること、および Contoso にも特定の日にチケット販売の急増があることを確認しました。 Power BI のオプションを操作して、Contoso Concert Hall のチケットの累計販売数をプロットし、各イベントの販売動向に注目します。 すべてのイベントが同じ販売パターンに従っているのでしょうか。 次のようなプロットを生成してみてください。

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Contoso Concert Hall の各イベントに対する時間経過に伴う累積チケット販売数のこのプロットは、急増がすべてのイベントで発生するわけではないことを示しています。 フィルター オプションを使って、他の会場の販売傾向を調べてください。

チケット販売パターンに関する洞察により、Wingtip Tickets でビジネス モデルを最適化できます。 Wingtip では、すべてのテナントに均等に課金するのではなく、コンピューティング サイズが異なるサービス レベルを導入する必要があると考えられます。 1 日により多くのチケットを販売する必要がある大規模な会場には、サービス レベル アグリーメント (SLA) が高い上位層を提供できます。 これらの会場では、データベースごとのリソースの上限が高いプールにデータベースを配置できます。 各サービス層に時間単位の販売割り当てを設定し、割り当てを超えた場合は追加料金が課金されるようにすることもできます。 売上が定期的に激増する大規模な会場は上位層からメリットが得られ、Wingtip Tickets はサービスをより効率的に収益化できます。

その一方で、Wingtip Tickets の一部の顧客は、サービス コストに見合うだけのチケットを販売するのに苦戦していると不満を漏らしています。 これらの洞察に、業績が低迷している会場のチケットの売上を伸ばす機会がおそらくあります。 売上が増加すれば、サービスの知覚価値が高まります。 fact_Tickets を右クリックし、 **[新しいメジャー]** を選択します。 **AverageTicketsSold** という新しいメジャーの次の式を入力します。

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

相対的な成果を確認するために、次の視覚化オプションを選択して、各会場で販売されたチケットの割合をプロットします。

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

上記のプロッでは、ほとんどの会場ではチケットの 80% 以上が販売されますが、半分以上のチケットを販売するのさえ苦労している会場もあることがわかります。 Values Well を操作して、各会場で販売されたチケットの最大または最小の割合を選択します。

## <a name="embedding-analytics-in-your-apps"></a>アプリへの分析の埋め込み

このチュートリアルでは、ソフトウェア ベンダーのテナントの理解を向上させるために使用されるテナント間の分析に注目しました。 分析では、業務効率的化の管理に役立つ "_テナント_" に関する分析情報も提供できます。

Wingtip Tickets の例では、チケット販売の傾向が予測可能なパターンに従うことを発見しました。 この分析情報は、成績の悪い会場のチケット販売を増やすのに役立つ可能性があります。 おそらく、機械学習技法を導入してイベントのチケット販売を予測する機会があります。 価格変更の影響をモデル化して、割引を行った場合の影響を予測できます。 Power BI Embedded をイベント管理アプリケーションに統合して、チケットの総収益や販売数の少ないイベントの収益への割引の影響など、予測される販売を視覚化できる可能性があります。 Power BI Embedded を使うと、チケット価格に対する割引の実際の適用を、視覚化エクスペリエンスに統合することさえできます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> - 読み込み用のテナント分析ストアを作成する。
> - Azure Data Factory (ADF) を使用して、各テナント データベースから分析データ ウェアハウスにデータを抽出する。
> - 抽出されたデータを最適化する (スター スキーマに再編成する)。
> - 分析データ ウェアハウスのクエリを実行する。
> - データ視覚化に Power BI を使用して、テナント データの傾向を強調表示し、改善のための推奨事項を提案する。

お疲れさまでした。

## <a name="additional-resources"></a>その他のリソース

- [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)