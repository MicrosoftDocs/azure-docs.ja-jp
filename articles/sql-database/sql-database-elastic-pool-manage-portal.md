---
title: "Azure Portal: SQL Database のエラスティック プールの作成と管理 | Microsoft Docs"
description: "Azure Portal と SQL Database の組み込みのインテリジェンスを使って、スケーラブルなエラスティック プールの管理、監視を行い、適切なサイズを設定して、データベース パフォーマンスの最適化とコストの管理を行う方法について説明します。"
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 878bb6e792cff555747912a57c1a15632f06b2f7
ms.lasthandoff: 03/10/2017


---
# <a name="create-and-manage-an-elastic-pool-with-the-azure-portal"></a>Azure Portal を使用したエラスティック プールの作成と管理
このトピックでは、Azure Portal を使ってスケーラブルな[エラスティック プール](sql-database-elastic-pool.md)を作成および管理する方法について説明します。 Azure エラスティック プールは、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、REST API、[C#](sql-database-elastic-pool-manage-csharp.md) を使用して作成および管理することもできます。 また、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md) を使用して、データベースを作成したり、エラスティック プールに出し入れしたりすることもできます。

## <a name="create-an-elastic-pool"></a>エラスティック プールの作成 

エラスティック プールを作成する方法は&2; つあります。 目的のプール設定がわかっている場合は、一から作成できます。また、サービスによる推奨設定から始めることもできます。 SQL Database には、データベースの過去の使用状況テレメトリに基づいてコスト効率が高いエラスティック プール設定を推奨するインテリジェンスが組み込まれています。

サーバーに複数のプールを作成することはできますが、同じプールに異なるサーバーからデータベースを追加することはできません。 

> [!NOTE]
> エラスティック プールは、現在プレビュー段階にあるインド西部を除くすべての Azure リージョンで一般公開 (GA) されています。  プレビュー段階のリージョンでも、できるだけ早く一般公開される予定です。
>
>

### <a name="step-1-create-an-elastic-pool"></a>手順 1. エラスティック プールを作成する

既存のデータベースをエラスティック プールに移動する最も簡単な方法は、既存の**[サーバー]** ブレードから新しいエラスティック プールを作成するというものです。

> [!NOTE]
> 新しいエラスティック プールは、**Marketplace** で "**SQL エラスティック プール**" を検索するか、**[SQL エラスティック プール]** 参照ブレードの **[+追加]** をクリックする方法でも作成できます。 このプールのプロビジョニング ワークフローでは、新しいサーバーまたは既存のサーバーを指定できます。
>
>

1. [Azure Portal](http://portal.azure.com/) で、**[その他のサービス]** **>** **[SQL Server]** の順にクリックし、エラスティック プールに追加するデータベースが含まれているサーバーをクリックします。
2. **[新しいプール]**をクリックします。

    ![サーバーへのプールの追加](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **- または -**

    そのサーバーに推奨されるエラスティック プールがあることを示すメッセージが表示される場合があります (V12 のみ)。 メッセージをクリックして、データベースの過去の使用状況テレメトリに基づいた推奨のプールを表示します。次に、レベルをクリックして詳細を確認し、プールをカスタマイズします。 推奨のしくみについては、このトピックで後述する「[プールの推奨事項について](#understand-pool-recommendations)」を参照してください。

    ![推奨されるプール](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. **[エラスティック プール]** ブレードが表示されます。これは、プールの設定を指定する場所です。 前の手順で **[新しいプール]** をクリックした場合には、価格レベルは既定の **Standard**、選択されているデータベースはない状態になります。 空のプールを作成することも、そのサーバーからプールに移動する一連の既存のデータベースを指定することもできます。 推奨されるプールを作成している場合は、推奨される価格レベル、パフォーマンス設定、データベース一覧があらかじめ入力されていますが、それらは引き続き変更できます。

    ![エラスティック プールの構成](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. エラスティック プールの名前を指定するか、既定値を使用します。

### <a name="step-2-choose-a-pricing-tier"></a>手順 2. 価格レベルを選択する

プールのエラスティック データベースで使用できる機能のほか、eDTU の最大数 (eDTU MAX) や各データベースで使用できるストレージ容量 (GB) は、プールの価格レベルによって決まります。 詳細については、「サービス プラン」を参照してください。

プールの価格レベルを変更するには、**[価格レベル]** をクリックし、目的の価格レベルをクリックして、**[選択]** をクリックします。

> [!IMPORTANT]
> 価格レベルを選択し、最後の手順で **[OK]** をクリックして変更を確定すると、プールの価格レベルを変更できなくなります。 既存のエラスティック プールの価格レベルを変更するには、任意の価格レベルでエラスティック プールを作成し、その新しいプールにデータベースを移行します。
>
>

![料金レベルを選択します](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

### <a name="step-3-configure-the-pool"></a>手順 3. プールを構成する

価格レベルを設定した後は、データベースを追加するプールで [プールの構成] をクリックし、プールの eDTU とストレージ (プールの GB 値) を設定して、さらにプール内のエラスティック データベースの最小および最大 eDTU を設定します。

1. **[プールの構成]**
2. プールに追加するデータベースを選択します。 プールを作成する場合、この手順は省略できます。 プールが作成されたら、データベースを追加できます。
    データベースを追加するには、**[データベースの追加]**、追加するデータベース、**[選択]** の順にクリックします。

    ![[データベースの追加]](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    使用するデータベースに過去の使用状況テレメトリが十分にある場合、構成の決定に役立つ **[Estimated eDTU and GB usage (推定される eDTU と GB の使用状況)]** グラフと **[Actual eDTU usage (実際の eDTU 使用量)]** 棒グラフが更新されます。 また、プールの適切なサイズ設定に役立つ推奨事項に関するメッセージが表示される場合もあります。 詳細については、「 [動的な推奨事項](#dynamic-recommendations)」を参照してください。

3. **[プールの構成]** ページにあるコントロールを使用して設定を確認し、プールを構成します。 各サービス レベルの上限の詳細については、[エラスティック プールの制限](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)に関するトピックを参照してください。エラスティック プールの適切なサイズ設定に関する詳細なガイダンスについては、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)に関する記事を参照してください。 プールの設定の詳細については、「[エラスティック プールのプロパティ](sql-database-elastic-pool.md#elastic-pool-properties)」を参照してください。

    ![エラスティック プールの構成](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. **[選択]** in the **Configure Pool** をクリックします。
5. **[OK]** をクリックすると、プールが作成されます。


## <a name="understand-elastic-pool-recommendations"></a>エラスティック プールの推奨事項について

SQL Database サービスでは使用状況の履歴を評価し、単一のデータベースを使用するよりコスト効率が高い場合は、1 つ以上のプールを推奨します。 それぞれの推奨事項は、サーバーのデータベースでプールに最適なデータベースを一意の小集合にしたもので構成されます。

![推奨されるプール](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

プールの推奨事項は、以下で構成されます。

- プールの価格レベル (Basic、Standard、Premium)
- 適切な **プールの eDTU** (プールあたりの最大 eDTU とも呼ばれます)
- データベースあたりの **eDTU の最大値**と **eDTU の最小値**
- プールの推奨データベースの一覧

> ![重要] プールを推奨するとき、このサービスでは過去 30 日間のテレメトリが考慮されます。 データベースがエラスティック プールの候補と見なされるには 7 日間以上存在している必要があります。 エラスティック プールに既に存在するデータベースはエラスティック プール推奨の候補と見なされません。
>

このサービスでは、各サービス レベルの単一のデータベースを同じレベルのプールに移動することの必要性と対費用効果が評価されます。 たとえば、サーバーのすべての Standard データベースの Standard エラスティック プールに対する適合性が評価されます。 つまり、このサービスでは、Standard データベースを Premium プールに移動することなど、レベル間の推奨は行われません。

データベースをプールに追加した後は、選択したデータベースの使用状況の履歴に基づき、推奨事項が動的に生成されます。 この推奨事項は、**[プールの構成]** ブレード上部の推奨事項バナーに加え、eDTU と GB の使用状況グラフにも表示されます。 このような推奨事項は、特定のデータベース用に最適化されたエラスティック プールの作成をサポートすることを目的としています。

![動的な推奨事項](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="manage-and-monitor-an-elastic-pool"></a>エラスティック プールの管理と監視

Azure Portal を使用すると、プール内のエラスティック プールとデータベースを監視および管理できます。 ポータルでは、エラスティック プールおよびプール内のデータベースの使用状況を監視できます。 また、エラスティック プールに対して複数の変更を行い、すべての変更を同時に送信できます。 このような変更としては、データベースの追加または削除、エラスティック プールの設定の変更、データベース設定の変更などがあります。

次の図に、エラスティック プールの例を示します。 ビューには次のものが含まれます。

*  エラスティック プールおよびプールに含まれるデータベースの両方についてリソースの使用状況を監視するためのグラフ。
*  エラスティック プールを変更するための **[プールの構成]** ボタン。
*  新しいデータベースを作成して現在のエラスティック プールに追加する **[データベースの作成]** ボタン。
*  リスト内のすべてのデータベースに対して Transact SQL スクリプトを実行することによって多数のデータベースを管理できるエラスティック ジョブ。

![プール ビュー][2]

特定のプールのリソース使用状況を見ることができます。 既定では、プールは過去&1; 時間のストレージと eDTU の使用状況を表示するように構成されます。 さまざまな時間枠で異なるメトリックを表示するようにグラフを構成できます。

1. 操作するエラスティック プールを選択します。
2. **[エラスティック プールの監視]** の下には、**[リソース使用率]** というグラフがあります。 グラフをクリックします。

    ![エラスティック プールの監視][3]

    **[メトリック]** ブレードが開き、指定したメトリックについて指定した時間範囲の詳細ビューが表示されます。   

    ![[メトリック] ブレード][9]

### <a name="to-customize-the-chart-display"></a>グラフの表示をカスタマイズするには

グラフおよびメトリックのブレードを編集して、使用された CPU の割合、データ IO の割合、ログ IO の割合など、他のメトリックを表示できます。

1. [メトリック] ブレードで、 **[編集]**をクリックします。

    ![[編集] をクリックする][6]

2. **[グラフの編集]** ブレードで、時間の範囲 (過去&1; 時間、今日、過去&1; 週間) を選択するか、**[カスタム]** をクリックして過去&2; 週間の任意の日付範囲を選択します。 グラフの種類 (棒または線) を選択し、監視するリソースを選択します。

   > [!Note]
   > グラフに同時に表示できるのは測定単位が同じメトリックのみです。 たとえば、"eDTU 割合" を選択した場合、割合に関する他のメトリックのみを測定単位として選択できます。
   >

    ![[編集] をクリックする](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

    

3. 次に、 **[OK]**をクリックします

## <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>エラスティック プール内のデータベースの管理と監視

個々のデータベースで潜在的な問題を監視することもできます。

1. **[弾力性データベースの監視]**には、5 つのデータベースのメトリックを表示するグラフがあります。 既定では、プール内のデータベースのうち過去 1 時間の平均 eDTU 使用率で上位の 5 つがグラフに表示されます。 グラフをクリックします。

    ![エラスティック プールの監視][4]

2. **[データベース リソース使用率]** ブレードが表示されます。 プール内のデータベースの使用状況の詳細が表示されます。 ブレード下部のグリッドを使用すると、プール内の任意のデータベース (最大 5 件) を選択して、その使用率をグラフに表示できます。 また、 **[グラフの編集]**をクリックすると、グラフに表示されるメトリックと時間範囲をカスタマイズできます。

    ![[データベース リソース使用率] ブレード][8]

### <a name="to-customize-the-view"></a>ビューをカスタマイズするには

1. **[データベース リソース使用率]** ブレードで、**[グラフの編集]** をクリックします。

    ![[グラフの編集] をクリックする](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. **[グラフの編集]** ブレードで、時間の範囲 (過去 1 時間または過去 24 時間) を選択するか、**[カスタム]** をクリックして過去 2 週間以内の異なる日を選択します。

    ![[カスタム] をクリックする](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. **[データベースの比較]** ドロップダウンをクリックして、データベースの比較に使用するメトリックを選択します。

    ![グラフを編集する](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>監視するデータベースを選択するには

**[データベース リソース使用率]** ブレードのデータベースの一覧では、リスト内のページを検索するかデータベースの名前を入力して、特定のデータベースを見つけることができます。 データベースを選択するにはチェック ボックスを使用します。

![監視するデータベースを探す][7]


## <a name="add-an-alert-to-an-elastic-pool-resource"></a>エラスティック プール リソースへのアラートの追加

エラスティック プールにルールを追加して、設定した使用率のしきい値にエラスティック プールが達したときに、ユーザーへの電子メールの送信や URL エンドポイントへのアラート文字列の送信を行うことができます。

> [!IMPORTANT]
> エラスティック プールのリソース使用率の監視には、20 分以上の遅れがあります。 エラスティック プールに対して 30 分未満のアラートを設定することは現在サポートされていません。 30 分未満の期間 (「PowerShell API の「-WindowSize」というパラメーター) を含むエラスティック プールのアラート設定は、実行されない場合があります。 エラスティック プールに対して定義するアラートでは、30 分以上の期間 (WindowSize) を使用してください。
>
>

**任意のリソースへのアラートの追加**

1. **[リソース使用率]** グラフをクリックして **[メトリック]** ブレードを開きます。次に、**[アラートの追加]** をクリックし、**[アラート ルールの追加]** ブレードで情報を入力します (**[リソース]** は使用するプールとして自動的に設定されます)。
2. **[名前]** と **[説明]** を入力して、自分と受信者がアラートを識別できるようにします。
3. 一覧でアラートの対象となる **[メトリック]** を選択します。

    グラフにはそのメトリックのリソース使用率が動的に表示されるため、しきい値の選択に役立ちます。

4. **[条件]** (より大きい、より小さいなど) と **[しきい値]** を選択します。
5. **[OK]**をクリックします。

## <a name="move-a-database-into-an-elastic-pool"></a>エラスティック プールへのデータベースの移動

既存のプールのデータベースを追加または削除することができます。 データベースは他のプールにあってもかまいません。 ただし、追加できるのは同じ論理サーバー上にあるデータベースだけです。

1. プールのブレードで、**[エラスティック データベース]** の **[プールの構成]** をクリックします。

    ![[プールの構成] をクリックする][1]

2. **[プールの構成]** ブレードで、**[プールに追加]** をクリックします。

    ![Click Add to pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. **[データベースの追加]** ブレードで、プールに追加するデータベースを選択します。 **[選択]**をクリックします。

    ![追加するデータベースを選択する](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    **[プールの構成]** ブレードに、追加するものとして選択したデータベースが一覧表示されます。状態は **[保留中]** に設定されています。

    ![保留中のプールの追加](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. **[プールの構成]** ブレードで、**[保存]** をクリックします。

    ![[保存] をクリックします。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>エラスティック プールからのデータベースの移動

1. **[プールの構成]** ブレードで、削除するデータベースを選択します。

    ![データベース一覧](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. **[プールから削除]**をクリックします。

    ![データベース一覧](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    **[プールの構成]** ブレードに、削除するものとして選択したデータベースが一覧表示されます。状態は **[保留中]** に設定されています。

    ![データベースの追加と削除をプレビューする](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. **[プールの構成]** ブレードで、**[保存]** をクリックします。

    ![[保存] をクリックします。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-an-elastic-pool"></a>エラスティック プールのパフォーマンス設定の変更

エラスティック プールのリソース使用率を監視すると、必要な調整が見つかることがあります。 プールのパフォーマンスやストレージ制限の変更が必要な場合、 プール内のデータベースの設定を変更することになります。 パフォーマンスとコストの最適なバランスを確保するために、プールの設定はいつでも変更できます。 詳しくは、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool-guidance.md)」をご覧ください。

プールあたりの eDTU 数またはストレージ制限、およびデータベースあたりの eDTU 数を変更するには:

1. **[プールの構成]** ブレードを開きます。

    **[エラスティック プールの設定]** で、スライダーを使ってプールの設定を変更します。

    ![エラスティック プール リソースの使用率](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 設定を変更すると、変更によって予想される毎月のコストが表示されます。

    ![エラスティック プールと新しい月額料金の更新](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="latency-of-elastic-pool-operations"></a>エラスティック プール操作の待機時間
* 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
* プールあたりの eDTU の変更は、プール内のすべてのデータベースで使用される領域の合計に依存します。 変更の平均時間は、100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースで使用される領域の合計が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

## <a name="next-steps"></a>次のステップ

- エラスティック プールとは何かを理解するには、[SQL Database のエラスティック プール](sql-database-elastic-pool.md)に関するトピックを参照してください。
- エラスティック プールの使用に関するガイダンスについては、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)に関するトピックを参照してください。
- エラスティック ジョブを使用してプール内の任意の数のデータベースに対して Transact-SQL スクリプトを実行するには、[エラスティック ジョブの概要](sql-database-elastic-jobs-overview.md)に関するトピックを参照してください。
- プール内の任意の数のデータベースに対してクエリを実行するには、[エラスティック クエリの概要](sql-database-elastic-query-overview.md)に関するトピックを参照してください。
- プール内の任意の数のデータベースにまたがるトランザクションについては、[エラスティック トランザクション](sql-database-elastic-transactions-overview.md)に関するトピックを参照してください。


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

