---
title: "Azure SQL Database の Query Performance Insight | Microsoft Docs"
description: "クエリのパフォーマンスを監視して、Azure SQL Database の CPU 消費量の多いクエリを明らかにします。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: c2f580b2-3835-453f-89f5-140e02dd2ea7
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ddf93a0db278fffa2aa45fdc73a1c7f57a84fb39
ms.openlocfilehash: 14140a6aa8c8cccf658f8e98305659e8ae76dcad
ms.lasthandoff: 01/31/2017


---
# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL Database Query Performance Insight
リレーショナル データベースのパフォーマンスの管理とチューニングは、かなりの専門知識と時間の投入を必要とする困難な作業です。 Query Performance Insight を使用すると、短時間でデータベース パフォーマンスのトラブルシューティングを行うことができます。以下の情報が提供されます。

* データベース リソース (DTU) の消費量の詳細な情報。 
* CPU/期間/実行回数の上位クエリ。パフォーマンス向上のためのチューニングの対象となる可能性があります。
* クエリの詳細にドリルダウンして、テキストやリソース使用率履歴を表示する機能。 
* [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>前提条件
* Query Performance Insight では、 [クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx) がデータベース上で実行されている必要があります。 クエリ ストアが実行されていない場合、ポータルで、クエリ ストアを有効にするよう求められます。

## <a name="permissions"></a>アクセス許可
Query Performance Insight を使用するには、次の [ロール ベースのアクセス制御](../active-directory/role-based-access-control-configure.md) 権限が必要です。 

* トップ リソース コンシューマー クエリとグラフを表示するには、**閲覧者**、**所有者**、**共同作成者**、**SQL DB 共同作業者**、または **SQL Server 共同作業者**の権限が必要です。 
* クエリ テキストを表示するには、**所有者**、**共同作成者**、**SQL DB 共同作業者**、または **SQL Server 共同作業者**の権限が必要です。

## <a name="using-query-performance-insight"></a>Query Performance Insight の使用
Query Performance Insight は簡単に使用できます。

* [Azure ポータル](https://portal.azure.com/) を開き、調査するデータベースを見つけます。 
  * 左側にあるメニューのサポートおよびトラブルシューティングの下で、[Query Performance Insight] を選択します。
* 最初のタブで、リソース消費量が上位のクエリの一覧を確認します。
* 個別のクエリを選択して詳細を表示します。
* [SQL Azure Database Advisor](sql-database-advisor.md) を開き、利用できる推奨事項があるかどうかを確認します。
* スライダーまたはズーム アイコンを使用して監視間隔を変更します。
  
    ![パフォーマンス ダッシュボード](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> SQL Database がクエリ パフォーマンスの洞察を提供するには、2 時間分のデータをクエリ ストアでキャプチャする必要があります。 一定の期間、データベースでアクティビティが発生しない場合、またはクエリ ストアがアクティブではない場合は、その時間帯を表示してもグラフは空になります。 クエリ ストアが実行されていない場合はいつでも有効にできます。   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>CPU 消費量上位クエリを確認する
[ポータル](http://portal.azure.com) で次のようにします。

1. SQL Database を参照し、**[すべての設定]** > **[サポート + トラブルシューティング]** > **[Query Performance Insight]** の順にクリックします。 
   
    ![[Query Performance Insight]][1]
   
    上位クエリ ビューが開き、CPU 消費量上位クエリの一覧が表示されます。
2. グラフのあたりをクリックして詳細を確認します。<br>上の折れ線はデータベース全体の DTU% を示します。棒グラフは、選択した期間に、選択したクエリによって消費された CPU% を示します (**[過去&1; 週間]** を選択した場合は各棒グラフが&1; 日を表します)。
   
    ![上位クエリ][2]
   
    下部のグリッドには、表示可能なクエリの集計情報が表されます。
   
   * クエリ ID - データベース内に含まれるクエリの一意の識別子。
   * 監視可能な期間のクエリあたりの CPU 使用率 (集計関数に依存)。
   * 1 つのクエリの実行時間 (集計関数に依存)。
   * 特定のクエリの実行回数の合計。
     
     グラフに表示する個別のクエリを選別するには、チェックボックスをオンまたはオフにします。
3. データが古くなった場合は、 **[更新]** ボタンをクリックします。
4. 監視間隔を変更してスパイクを調査するには、スライダーやズーム ボタンを使用します。 ![設定](./media/sql-database-query-performance/zoom.png)
5. 必要に応じて、別のビューを表示する場合は、 **[カスタム]** タブを選択して、以下の項目を設定できます。
   
   * メトリック (CPU、実行時間、実行回数)
   * 時間間隔 (過去 24 時間、過去 1 週間、過去 1 か月) 
   * クエリの数
   * 集計関数
     
     ![[設定]](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>個々のクエリの詳細表示
クエリの詳細を表示するには:

1. 上位のクエリの一覧でクエリをクリックします。
   
    ![詳細](./media/sql-database-query-performance/details.png)
2. 詳細ビューが開き、クエリの CPU 消費量、実行時間、実行回数が時間ごとに分割されます。
3. グラフのあたりをクリックして詳細を確認します。
   
   * 一番上のグラフの折れ線は、データベース全体の DTU% を示します。棒グラフは、選択したクエリによって消費された CPU% を示します。
   * 2 つ目のグラフは、選択したクエリの総実行時間を示します。
   * 一番下のグラフは、選択したクエリの総実行回数を示します。
     
     ![クエリの詳細][3]
4. 必要に応じて、スライダーやズーム ボタンを使うか、 **[設定]** をクリックして、クエリ データの表示方法をカスタマイズしたり、別の期間を選択したりできます。

## <a name="review-top-queries-per-duration"></a>実行時間あたりの上位クエリを確認する
最近の Query Performance Insight の更新により、潜在的なボトルネックの特定に役立つ&2; つの新しいメトリックとして、実行時間と実行回数が導入されました。<br>

実行時間の長いクエリは、長期にわたるリソースのロック、他のユーザーのブロック、スケーラビリティの制限を引き起こす最大の原因と考えられます。 こうしたクエリは、最適化に最も適した要素でもあります。<br>

実行時間の長いクエリを特定するには:

1. Query Performance Insight で、選択したデータベースの **[カスタム]** タブを開きます。
2. メトリックを **[実行時間]**
3. クエリの数と監視間隔を選択します。
4. 集計関数を選択します。
   
   * **Sum** は、監視間隔全体のすべてのクエリ実行時間を合計します。
   * **Max** は、監視間隔全体で実行時間が最長のクエリを検索します。
   * **Avg** は、全クエリ実行の平均実行時間を算出し、これらの平均値で上位のクエリを表示します。 
     
     ![クエリ実行時間][4]

## <a name="review-top-queries-per-execution-count"></a>実行回数あたりの上位クエリを確認する
実行回数が多くても、データベース自体には必ずしも影響せず、リソース使用率が低い場合もありますが、アプリケーション全体の処理速度は低下します。

場合によっては、実行回数が非常に多いと、ネットワークのラウンド トリップが増えることもあります。 ラウンド トリップはパフォーマンスに大きく影響します。 これは、ネットワーク待ち時間やダウンストリーム サーバーの待ち時間の影響を受けます。 

たとえば、多くのデータ主導 Web サイトは、ユーザー要求ごとに高い頻度でデータベースにアクセスします。 接続プールが有効に動作しても、ネットワーク トラフィックやデータベース サーバーの処理負荷の増加により、パフォーマンスに悪影響を及ぼす可能性があります。  一般的には、ラウンド トリップを全面的に最小限に抑えることが推奨されます。

頻繁に実行される ("通信頻度の高い") クエリを特定するには:

1. Query Performance Insight で、選択したデータベースの **[カスタム]** タブを開きます。
2. メトリックを **[実行回数]**
3. クエリの数と監視間隔を選択します。
   
    ![クエリ実行回数][5]

## <a name="understanding-performance-tuning-annotations"></a>パフォーマンス チューニングの注釈を理解する
Query Performance Insight でワークロードを探索する場合、垂直線付きのアイコンがグラフ上部に表示されることがあります。<br>

これらは注釈のアイコンであり、 [SQL Azure Database Advisor](sql-database-advisor.md)によって実行される、パフォーマンスに影響するアクションを表します。 注釈にカーソルを置くと、アクションの基本情報が表示されます。

![クエリの注釈][6]

詳細を確認したり、アドバイザーの推奨事項を適用するには、アイコンをクリックします。 アクションの詳細が開きます。 アクティブな推奨事項の場合は、直ちにコマンドを使用して適用できます。

![クエリの注釈の詳細][7]

### <a name="multiple-annotations"></a>複数の注釈
ズーム レベルによっては、互いに近接する注釈が&1; つに折りたたまれている場合があります。 この場合は、特殊なアイコンが表示されます。このアイコンをクリックすると新しいブレードが開き、グループ化された注釈の一覧が表示されます。
相互に関連するクエリやパフォーマンス チューニング アクションは、ワークロードへの理解を深めるのに役立ちます。 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Query Performance Insight 用のクエリ ストア構成の最適化
Query Performance Insight の使用中に、次のようなクエリ ストア メッセージが表示されることがあります。

* 「クエリ ストアはこのデータベースで適切に構成されていません。 詳細を確認するには、ここをクリックしてください。」
* 「クエリ ストアはこのデータベースで適切に構成されていません。 設定を変更するには、ここをクリックしてください。」 

これらのメッセージは、通常、クエリ ストアが新しいデータを収集できないときに表示されます。 

1 つ目は、クエリ ストアが読み取り専用状態にあり、パラメーターの設定が最適化されている場合に発生します。 クエリ ストアのサイズを増やすか、クエリ ストアを消去して、問題を修正できます。

![qds ボタン][8]

2 つ目は、クエリ ストアがオフの場合またはパラメーターの設定が最適化されていない場合に発生します。 <br>下のコマンドを実行することによって、または直接ポータルから、保存とキャプチャのポリシーを変更してクエリ ストアを有効化できます。

![qds ボタン][9]

### <a name="recommended-retention-and-capture-policy"></a>推奨される保存とキャプチャのポリシー
保持ポリシーには&2; つの種類があります。

* サイズ ベース – AUTO に設定した場合、最大サイズの近づくとデータが自動的にクリーンアップされます。
* 時間ベース – 既定では 30 日に設定されます。つまり、クエリ ストアの領域が不足すると、30 日より古いクエリ情報が削除されます。

キャプチャ ポリシーは次のように設定できます。

* **すべて** - すべてのクエリをキャプチャします。
* **AUTO** - 低頻度のクエリおよびコンパイル期間と実行期間が重要ではないクエリは無視されます。 実行カウント、コンパイル期間、実行期間のしきい値は内部的に決定されます。 これが既定のオプションです。
* **None** – クエリ ストアは新しいクエリのキャプチャを停止します。ただし、既にキャプチャされているクエリの実行時統計は引き続き収集されます。

すべてのポリシーを AUTO に設定し、クリーンアップ ポリシーを 30 日に設定することをお勧めします。

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

クエリ ストアのサイズを増やします。 これは、データベースに接続して、次のクエリを発行することにより実行できます。

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

これらの設定を適用すると、クエリ ストアでは最終的に新しいクエリが収集されるようになりますが、待機したくない場合はクエリ ストアを消去できます。 

> [!NOTE]
> 次のクエリを実行すると、現在クエリ ストア内にあるすべての情報が削除されます。 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>まとめ
Query Performance Insight を使うと、クエリ ワークロードの影響や、データベース リソース消費量との関係を理解できます。 この機能では、消費量上位クエリがわかり、問題になる前に簡単に識別して修正できます。

## <a name="next-steps"></a>次のステップ
[[Query Performance Insight]](sql-database-advisor.md) ブレードで **[推奨事項]** をクリックして、SQL Database のパフォーマンスの向上に関するその他の推奨事項を確認します。

![Performance Advisor](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png


