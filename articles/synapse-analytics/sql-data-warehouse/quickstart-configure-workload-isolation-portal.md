---
title: クイック スタート:ワークロードの分離を構成する - ポータル
description: Azure portal を使用して、ワークロードの分離を構成します。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 30862a0c16995e143df72f2a243419819941f54e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213042"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>クイック スタート:Azure portal でワークロード グループを使用して Synapse SQL プールのワークロードの分離を構成する

このクイックスタートでは、リソースを予約するためのワークロード グループを作成することによって、[ワークロードの分離](sql-data-warehouse-workload-isolation.md)を構成します。  このチュートリアルでは、`DataLoads` と呼ばれるデータ読み込み用のワークロード グループを作成します。 このワークロード グループによって、システム リソースの 20% が予約されます。  データの読み込み用に 20% が分離されるため、SLA の達成を可能にするリソースが保証されます。  ワークロード グループを作成したら、[ワークロード分類子](quickstart-create-a-workload-classifier-portal.md)を作成して、このワークロード グループにクエリを割り当てます。


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。


## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

> [!NOTE]
> Azure Synapse Analytics に SQL プール インスタンスを作成すると、新しい課金対象サービスが発生する可能性があります。  詳細については、「[Azure Synapse Analytics の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Synapse SQL の SQL プール インスタンスが既に用意されていて、CONTROL DATABASE アクセス許可を持っていることを前提としています。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成してください。

>[!IMPORTANT] 
>ワークロード管理を構成するには、SQL プールがオンラインになっている必要があります。 

## <a name="configure-workload-isolation"></a>ワークロードの分離を構成する
ワークロード グループを作成することによって、SQL プールのリソースを特定のワークロード用に分離して予約できます。  ワークロード グループがワークロードの管理にどのように役立つかの詳細については、[ワークロードの分離](sql-data-warehouse-workload-isolation.md)の概念に関するドキュメントを参照してください。  [ポータルを使用した作成と接続](create-data-warehouse-portal.md)に関するクイックスタートでは、**mySampleDataWarehouse** を作成して 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** にワークロード グループを作成します。

20% の分離でワークロード グループ作成するには、次のようにします。
1.  Azure portal の左側のページで **[Azure Synapse Analytics (以前の SQL DW)]** をクリックします。
2.  **[Azure Synapse Analytics (以前の SQL DW)]** ページから **[mySampleDataWarehouse]** を選択します。 SQL プールが開きます。
3.  **[ワークロード管理]** をクリックします。
4.  **[新しいワークロード グループ]** をクリックします。
5.  **[カスタム]** をクリックします。

    ![[カスタム] をクリックする](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  **[ワークロード グループ]** に「`DataLoads`」と入力します。
7.  **[最小リソース (%)]** に「`20`」と入力します。
8.  **[要求ごとの最小リソース (%)]** に「`5`」と入力します。
9.  **[上限リソース (%)]** に「`100`」と入力します。
10.   **[保存]** をクリックします。

   ![[保存] をクリックします。](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

ワークロード グループが作成されると、ポータルの通知が表示されます。  ワークロード グループのリソースが、構成済みの値の下のグラフに表示されます。

   ![[最終] をクリックする](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した `DataLoads` ワークロード グループを削除するには、次のようにします。
1. `DataLoads` ワークロード グループの右側にある **`...`** をクリックします。
2. **[ワークロード グループの削除]** をクリックします。
3. ワークロード グループの削除の確認を求めるメッセージが表示されたら、 **[はい]** をクリックします。
4. **[Save]** をクリックします。

   ![[削除] をクリックする](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



データ ウェアハウス ユニットと、データ ウェアハウスに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。

- データをストレージに保持しておく場合は、データ ウェアハウスを使わない間、コンピューティング リソースを一時停止できます。 コンピューティングを一時停止すると、データ ストレージに対してのみ課金されます。 データを使用する準備ができたら、コンピューティングを再開します。
- それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。

以下の手順に従ってリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にサインインし、データ ウェアハウスを選択します。

    ![リソースをクリーンアップする](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンを選択します。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、 **[開始]** を選択します。

3. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、 **[削除]** を選択します。

4. 作成した SQL Server を削除するには、前の画像の **sqlpoolservername.database.windows.net** を選択して、 **[削除]** を選択します。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

5. リソース グループを削除するには、**myResourceGroup** を選択して、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

`DataLoads` ワークロード グループを使用するには、[ワークロード分類子](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)を作成して、このワークロード グループに要求をルーティングする必要があります。  [ワークロード分類子の作成](quickstart-create-a-workload-classifier-portal.md)に関するクイックスタートに進んで、`DataLoads` 用のワークロード分類子を作成してください。

## <a name="see-also"></a>関連項目
ワークロード管理のためにワークロードを監視する方法の詳細については、[ワークロードの重要度の管理と監視](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)に関するハウツー記事を参照してください。
