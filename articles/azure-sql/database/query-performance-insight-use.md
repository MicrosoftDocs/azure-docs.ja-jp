---
title: Query Performance Insight
description: クエリのパフォーマンスを監視して、Azure SQL Database の単一およびプールされたデータベースに対して、CPU の消費量と実行時間が最大のクエリを特定します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: db24f280f66e567572821297cfc9bb9b1e19743b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222345"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Azure SQL Database の Query Performance Insight
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Query Performance Insight は、単一およびプールされたデータベースに対するインテリジェントなクエリ分析を提供します。 これは、ワークロードで最もリソースを消費し、長時間実行されるクエリを特定するのに役立ちます。 これにより、ワークロードの全体的なパフォーマンスを向上させるため、およびコストをかけているリソースを効率的に使用するために、最適化するクエリを見つけることができます。 Query Performance Insight を使用すると、以下が提供され、データベースのパフォーマンスのトラブルシューティングに費やす時間を短縮できます。

* データベース リソース (DTU) の消費量の詳細な情報
* CPU、期間、および実行回数別の上位データベース クエリの詳細 (パフォーマンス向上のためのチューニングの対象となる可能性があります)
* クエリの詳細にドリルダウンして、クエリ テキストやリソース使用率の履歴を表示する機能
* [データベース アドバイザー](database-advisor-implement-performance-recommendations.md)からのパフォーマンスに関する推奨事項を示す注釈

![Query Performance Insight](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>前提条件

Query Performance Insight では、 [クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) がデータベース上で実行されている必要があります。 既定では、Azure SQL Database のすべてのデータベースに対して自動的に有効になります。 クエリ ストアが実行されていない場合、Azure portal で有効にするように求められます。

> [!NOTE]
> ポータルで "クエリ ストアはこのデータベースで適切に構成されていません" というメッセージが表示された場合は、[クエリ ストア構成の最適化](#optimize-the-query-store-configuration)に関する記述を参照してください。

## <a name="permissions"></a>アクセス許可

Query Performance Insight を使用するには、次の [Azure ロール ベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) 権限が必要です。

* 上位のリソース消費量クエリとグラフを表示するには、**閲覧者**、**所有者**、**共同作成者**、**SQL DB 共同作業者**、または **SQL Server 共同作業者** の権限が必要です。
* クエリ テキストを表示するには、**所有者**、**共同作成者**、**SQL DB 共同作業者**、または **SQL Server 共同作業者** の権限が必要です。

## <a name="use-query-performance-insight"></a>Query Performance Insight の使用

Query Performance Insight は簡単に使用できます。

1. [Azure portal](https://portal.azure.com/) を開き、調査するデータベースを見つけます。
2. 左側のメニューから、 **[インテリジェント パフォーマンス]**  >  **[Query Performance Insight]** の順に開きます。
  
   ![メニューの [Query Performance Insight]](./media/query-performance-insight-use/tile.png)

3. 最初のタブで、リソース消費量が上位のクエリの一覧を確認します。
4. 個別のクエリを選択して詳細を表示します。
5. **[インテリジェント パフォーマンス]**  >  **[パフォーマンスの推奨事項]** の順に開き、パフォーマンスの推奨事項が利用可能かどうかを確認します。 組み込みのパフォーマンスの推奨事項について詳しくは、[Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md) に関するページを参照してください。
6. スライダーまたはズーム アイコンを使用して、監視間隔を変更します。

   ![パフォーマンス ダッシュボード](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Query Performance Insight で情報を表示する Azure SQL Database の場合、クエリ ストアで数時間分のデータをキャプチャする必要があります。 一定の期間に、データベースでアクティビティが発生していない場合、またはクエリ ストアがアクティブではなかった場合、Query Performance Insight でその時間の範囲が表示されたときにグラフは空になります。 クエリ ストアが実行されていない場合はいつでも有効にできます。 詳細については、「[クエリ ストアを使用するときの推奨事項](/sql/relational-databases/performance/best-practice-with-the-query-store)」を参照してください。
>

データベースのパフォーマンスに関する推奨事項を確認する場合は、Query Performance Insight のナビゲーション ブレードの [[推奨事項]](database-advisor-implement-performance-recommendations.md) を選択します。

![[推奨事項] タブ](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>上位の CPU 消費量クエリを確認する

既定では、Query Performance Insight を最初に開いたときに、上位 5 つの CPU 消費量クエリが表示されます。

1. グラフに表示する個別のクエリを選別するには、チェック ボックスをオンまたはオフにします。

   上の折れ線は、データベース全体の DTU の割合を示しています。 棒グラフは、選択された期間に選択されたクエリで消費された CPU の割合を示しています。 たとえば、 **[過去 1 週間]** が選択されている場合、各棒は 1 日を表します。

   ![上位のクエリ](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > 折れ線で示されている DTU は、1 時間の最大消費値に集計されます。 これは、クエリ実行の統計情報でのみ大まかに比較するためです。 DTU 使用率が実行されたクエリに比べて高すぎるように見える場合がありますが、そうでない可能性もあります。
   >
   > たとえば、わずか数分でクエリでの DTU の消費量が上限に達して 100% になった場合、Query Performance Insight の DTU の折れ線は、まる 1 時間の消費率を 100% (最大集計値の結果) として示しています。
   >
   > より細かく比較する場合 (最大で 1 分間) は、次のようにして、カスタム DTU 使用率のグラフを作成することを検討してください。
   >
   > 1. Azure portal で、 **[Azure SQL Database]**  >  **[監視]** の順に選択します。
   > 2. **[メトリック]** を選びます。
   > 3. **[+ グラフの追加]** を選択します。
   > 4. グラフの DTU の割合を選択します。
   > 5. さらに、左上のメニューで **[過去 24 時間]** を選択し、それを 1 分に変更します。
   >
   > より詳細なカスタム DTU グラフを使用して、クエリ実行グラフと比較します。

   下部のグリッドには、次の表示可能なクエリの集計情報が示されます。

   * クエリ ID。データベースでのクエリの一意識別子です。
   * 監視可能な期間のクエリあたりの CPU 使用率。集計関数に依存します。
   * 1 つのクエリの実行時間。これも集計関数に依存します。
   * 特定のクエリの実行回数の合計。

2. データが古くなった場合は、 **[更新]** ボタンを選択します。

3. 監視間隔を変更して消費量の急増を調べるには、スライダーとズーム ボタンを使用します。

   ![間隔を変更するためのスライダーとズーム ボタン](./media/query-performance-insight-use/zoom.png)

4. 必要に応じて、 **[カスタム]** タブを選択し、以下を表示するようにビューをカスタマイズすることができます。

   * メトリック (CPU、実行時間、実行回数)。
   * 時間間隔 (過去 24 時間、過去 1 週間、過去 1 か月)。
   * クエリの数
   * 集計関数
  
   ![[カスタム] タブ](./media/query-performance-insight-use/custom-tab.png)
  
5. **[進む >]** ボタンを選択して、カスタマイズされたビューを表示します。

   > [!IMPORTANT]
   > Query Performance Insight は、選択内容に応じて、上位の 5 個から 20 個の消費量クエリを表示するように制限されています。 データベースでは表示される上位のクエリ以外にもさらに多くのクエリを実行できます。これらのクエリはグラフには含まれません。
   >
   > 表示される上位のクエリ以外にも、多くの小さなクエリが頻繁に実行され、DTU の大部分が使用されるようなデータベース ワークロードが存在する可能性があります。 これらのクエリはパフォーマンス グラフに表示されません。
   >
   > たとえば、あるクエリでしばらくの間、かなりの量の DTU が消費された可能性があるとします。しかし、監視期間におけるその総消費量は、他の上位の消費量クエリよりも少なくなっています。 このような場合、このクエリのリソース使用率はグラフには表示されません。
   >
   > Query Performance Insight の制限を超える上位のクエリ実行について確認する必要がある場合は、高度なデータベース パフォーマンスの監視とトラブルシューティングのための [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) の使用を検討してください。
   >

## <a name="view-individual-query-details"></a>個々のクエリの詳細を表示する

クエリの詳細を表示するには:

1. 上位クエリのリストでクエリを選択します。

    ![上位クエリのリスト](./media/query-performance-insight-use/details.png)

   詳細なビューが開きます。 CPU 消費量、期間、および実行回数が時系列で示されます。

2. グラフの機能を選択して詳細を確認します。

   * 一番上のグラフの折れ線は、データベース全体の DTU の割合を示しています。 棒グラフは、選択されたクエリで消費された CPU の割合を示しています。
   * 2 つ目のグラフは、選択されたクエリの総実行時間を示しています。
   * 一番下のグラフは、選択されたクエリによる総実行回数を示しています。

   ![クエリの詳細](./media/query-performance-insight-use/query-details.png)

3. 必要に応じて、スライダーやズーム ボタンを使用するか、 **[設定]** を選択してクエリ データの表示方法をカスタマイズするか、別の時間範囲を選びます。

   > [!IMPORTANT]
   > Query Performance Insight ではどの DDL クエリもキャプチャされません。 場合によっては、アドホック クエリがすべてキャプチャされない可能性があります。
   >

## <a name="review-top-queries-per-duration"></a>実行時間あたりの上位クエリを確認する

Query Performance Insight の 2 つのメトリック (実行時間と実行回数) は、潜在的なボトルネックの特定に役立つ場合があります。

実行時間の長いクエリは、長期にわたるリソースのロック、他のユーザーのブロック、スケーラビリティの制限を引き起こす最大の原因と考えられます。 これらは、最適化に最も適した要素でもあります。 詳細については、「[Azure SQL のブロックの問題を理解して解決する](understand-resolve-blocking.md)」を参照してください。

実行時間の長いクエリを特定するには、次のようにします。

1. Query Performance Insight で、選択されたデータベースの **[カスタム]** タブを開きます。
2. メトリックを **[実行時間]** に変更します。
3. クエリの数と監視間隔を選択します。
4. 集計関数を選択します。

   * **Sum** では、監視間隔全体のすべてのクエリ実行時間を合計します。
   * **Max** では、監視間隔全体で実行時間が最長のクエリを検索します。
   * **Avg** では、全クエリ実行の平均実行時間を算出し、これらの平均値の上位のクエリを表示します。

   ![クエリ実行時間](./media/query-performance-insight-use/top-duration.png)

5. **[進む >]** ボタンを選択して、カスタマイズされたビューを表示します。

   > [!IMPORTANT]
   > クエリ ビューを調整しても、DTU の折れ線は更新されません。 DTU の折れ線は常に、その間隔での最大消費値を示します。
   >
   > データベースの DTU 消費量をより詳しく確認する場合 (最大で 1 分間) は、次のように Azure portal でカスタム グラフを作成することを検討してください。
   >
   > 1. **[Azure SQL Database]**  >  **[監視]** の順に選択します。
   > 2. **[メトリック]** を選びます。
   > 3. **[+ グラフの追加]** を選択します。
   > 4. グラフの DTU の割合を選択します。
   > 5. さらに、左上のメニューで **[過去 24 時間]** を選択し、それを 1 分に変更します。
   >
   > カスタム DTU グラフを使用して、クエリのパフォーマンス グラフと比較することをお勧めします。
   >

## <a name="review-top-queries-per-execution-count"></a>実行回数あたりの上位クエリを確認する

実行回数が多くてもデータベース自体には影響しない場合や、リソース使用率が低い場合でも、データベースを使用するユーザー アプリケーションの処理速度は低下する可能性があります。

場合によっては、実行回数が多いと、ネットワークのラウンド トリップが増えることがあります。 ラウンド トリップはパフォーマンスに影響します。 これは、ネットワーク待ち時間やダウンストリーム サーバーの待ち時間の影響を受けます。

たとえば、多くのデータ ドリブン Web サイトでは、ユーザー要求ごとに高い頻度でデータベースにアクセスします。 接続プールは役立ちますが、ネットワーク トラフィックやサーバーの処理負荷の増加により、パフォーマンスが低下する可能性があります。 一般的には、ラウンド トリップを最小限に抑えます。

頻繁に実行される ("頻度の高い") クエリを特定するには、次のようにします。

1. Query Performance Insight で、選択されたデータベースの **[カスタム]** タブを開きます。
2. メトリックを **[実行回数]** に変更します。
3. クエリの数と監視間隔を選択します。
4. **[進む >]** ボタンを選択して、カスタマイズされたビューを表示します。

   ![クエリ実行回数](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>パフォーマンス チューニングの注釈を理解する

Query Performance Insight でワークロードを調べる際に、垂直線付きのアイコンがグラフ上部に表示されることがあります。

これらのアイコンは注釈です。 [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md) からのパフォーマンスに関する推奨事項が示されます。 注釈にカーソルを置くことで、パフォーマンスの推奨事項に関する概要を表示することができます。

   ![クエリの注釈](./media/query-performance-insight-use/annotation.png)

詳細を確認したり、アドバイザーの推奨事項を適用したりする場合は、アイコンを選択し、推奨されるアクションの詳細を開きます。 これがアクティブな推奨事項の場合は、ポータルからすぐに適用できます。

   ![クエリの注釈の詳細](./media/query-performance-insight-use/annotation-details.png)

ズーム レベルによっては、互いに近接する注釈が 1 つに折りたたまれている場合があります。 Query Performance Insight では、これがグループ注釈アイコンとして表されます。 グループ注釈アイコンを選択すると、注釈を一覧表示する新しいブレードが開きます。

相互に関連するクエリやパフォーマンス チューニング アクションは、ワークロードの理解を深めるのに役立つ場合があります。

## <a name="optimize-the-query-store-configuration"></a>クエリ ストアの構成の最適化

Query Performance Insight を使用しているときに、次のようなクエリ ストアのエラー メッセージが表示される場合があります。

* 「クエリ ストアはこのデータベースで適切に構成されていません。 詳細を確認するには、ここをクリックしてください。」
* 「クエリ ストアはこのデータベースで適切に構成されていません。 設定を変更するには、ここをクリックしてください。」

これらのメッセージは、通常、クエリ ストアで新しいデータを収集できないときに表示されます。

1 つ目は、クエリ ストアが読み取り専用状態にあり、パラメーターが最適に設定されている場合に発生します。 これは、データ ストアのサイズを増やすか、クエリ ストアを消去することで修正できます (クエリ ストアを消去すると、以前に収集されたテレメトリがすべて失われます)。

   ![クエリ ストアの詳細](./media/query-performance-insight-use/qds-off.png)

2 つ目は、クエリ ストアが有効になっていないか、パラメーターが最適に設定されていない場合に発生します。 保持およびキャプチャ ポリシーを変更できます。また、クエリ ストアを有効にすることもできます。その場合は、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) または Azure portal から提供される以下のコマンドを実行します。

### <a name="recommended-retention-and-capture-policy"></a>推奨される保存とキャプチャのポリシー

保持ポリシーには 2 つの種類があります。

* **サイズ ベース**:このポリシーが **AUTO** に設定されている場合、最大サイズに近づくとデータが自動的にクリーンアップされます。
* **時間ベース**:既定では、このポリシーは 30 日に設定されます。 クエリ ストアの領域が不足すると、30 日を経過したクエリ情報が削除されます。

キャプチャ ポリシーは、次のように設定できます。

* **[すべて]** : クエリ ストアですべてのクエリがキャプチャされます。
* **Auto**:クエリ ストアでは、低頻度のクエリおよびコンパイル期間と実行期間が重要ではないクエリは無視されます。 実行回数、コンパイル期間、実行期間のしきい値は内部的に決定されます。 既定のオプションです。
* **None**:クエリ ストアでは新しいクエリのキャプチャが停止されます。しかし、既にキャプチャされているクエリの実行時統計は引き続き収集されます。

[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) または Azure portal から以下のコマンドを実行して、すべてのポリシーを **AUTO** に設定し、クリーンアップ ポリシーを 30 日に設定することをお勧めします (`YourDB` はデータベース名に置き換えてください)。

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) または Azure portal 経由でデータベースに接続し、以下のクエリを実行して、クエリ ストアのサイズを増やします (`YourDB` はデータベース名に置き換えてください)。

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

これらの設定を適用すると、最終的にクエリ ストアで新しいクエリのテレメトリが収集されるようになります。 クエリ ストアをすぐに動作させる必要がある場合は、SSMS または Azure portal 経由で次のクエリを実行し、クエリ ストアを消去するように選択することもできます。 (`YourDB` はデータベース名に置き換えてください)。

> [!NOTE]
> 次のクエリを実行すると、クエリ ストア内の、以前に収集された監視対象テレメトリがすべて削除されます。

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>次のステップ

多数の単一およびプールされたデータベース、エラスティック プール、マネージド インスタンス、およびインスタンス データベースについて高度なパフォーマンス監視を行う場合は、[Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) の使用を検討してください。