---
title: 'Azure Analysis Services チュートリアルのレッスン 10: パーティションを作成する | Microsoft Docs'
description: この Azure Analysis Services チュートリアル プロジェクトでパーティションを作成する方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91d34065753ae29b14aad8580bf922b8213a7988
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738625"
---
# <a name="create-partitions"></a>パーティションを作成する

このレッスンでは､FactInternetSales テーブルをより小さな論理部分に分割し､それぞれを独立して処理 (再表示) することを可能にするパーティションを作成します｡ 既定では､モデルに含まれる各表のパーティションはすべて 1 つであり､そのパーティションに､そのテーブルのすべての列と行が含まれます｡ FactInternetSales テーブルの場合は､データを年度別､sなわち､表の 5 年間の年度ごにパーティションを 1 つ作成します｡ 各パーティションはそれぞれ独立して処理できます｡ 詳細は､[Partitions](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular) を参照してください｡ 
  
このレッスンの推定所要時間: **15 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 9: 階層を作成する](../tutorials/aas-lesson-9-create-hierarchies.md)」を終えている必要があります。  
  
## <a name="create-partitions"></a>パーティションを作成する  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>FactInternetSales テーブルでパーティションを作成する  
  
1.  Tabular Model Explorer で **Tables** を展開し､**FactInternetSales** > **Partitions** を右クリックします｡  
  
2.  Partition Manager で **[Copy]** をクリックし､名前を**FactInternetSales2010** に変更します｡
  
    このパーティションには 2010 年度という特定の年度の行のみ含まれるようにするため､クエリ式を変更する必要があります｡
  
4.  **[Design]** をクリックしてQuery Editor を開き､**FactInternetSales2010** クエリをクリックします｡

5.  プレビューの **OrderDate** 列見出しの下向き矢印をクリックし､**Date/Time Filters** > **Between** をクリックします｡

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  [Filter Rows] ダイアログ ボックスの **[Show rows where: OrderDate]** の **is after or equal to** をそのままにして､日付フィールドに「**1/1/2010**」と入力します。 **And** 演算子は選択状態のままにして､**is before** を選択し､日付フィールドに **1/1/2011** を入力して､**[OK]** をクリックします｡

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    クエリ エディターの [適用したステップ] に、Filtered Rows という名前の新しいステップがあります。 このフィルターを使って、2010 年以降の注文日のみを選択します。

8.  **[インポート]** をクリックします。

    Partition Manager のクエリ式に Filtered Rows 句が追加されています｡

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    このステートメントは､Filtered Rows 句の指定に従って､このパーティションには､OrderDate の日付が 2010 年度の行のデータのみ含めることの指定です｡  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>2011 年度のパーティションを作成する  
  
1.  パーティション一覧で､作成した**FactInternetSales2010** パーティションをクリックし､**[Copy]** をクリックします｡  パーティション名を **FactInternetSales2011** に変更します｡ 

    Query Editor を使って新しい Filtered Rows 句を作成する必要はありません｡ 2010 年度のクエリのコピーを作成したため、2011 年度のクエリを少し変更すればよいだけです｡
  
2.  このパーティションが 2011 年度の行のみ含むようにするには､**Query Expression** の Filtered Rows 句の年度をそれぞれに次のように **2011** と **2012** に置き換えます｡  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>2012 と 2013､2014 年度用のパーティションを作成します｡  
  
- 上記の手順に従って 2012 と 2013､2014 年度用のパーティションを作成し､Filtered Rows 句の年度を変更して､その年度の行のみ含まれるようにしてください｡ 
  

## <a name="delete-the-factinternetsales-partition"></a>FactInternetSales パーティションを削除する
各年度のパーティションを作成したら､FactInternetSales パーティションを削除することで､パーティションの処理で Process All を選択した際の重複を防ぐことができます｡

#### <a name="to-delete-the-factinternetsales-partition"></a>FactInternetSales パーティションを削除する
-  FactInternetSales パーティションをクリックし、**[削除]** をクリックします。



## <a name="process-partitions"></a>パーティションを処理する  
Partition Manager で､新しく作成した各パーティションの **Last Processed** 列を見ると､それらパーティションが処理された履歴がないことが分かります｡ パーティションを作成したら､[Process Partitions] または [Process Table] 操作を実行してパーティションのデータを再表示してください｡  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>FactInternetSales パーティションを処理する  
  
1.  **[OK]** をクリックして Partition Manager を閉じます｡  
  
2.  **FactInternetSales** テーブルをクリックし､**Model** menu > **Process** > **Process Partitions** をクリックします｡  
  
3.  [Process Partitions] ダイアログ ボックスで **Mode** が **Process Default** に設定されていることを確認します｡  
  
4.  作成した 5 つのパーティションのそれぞれについて､ **[Process]** 列のチェックボックスを選択し､**[OK]** をクリックします｡  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Impersonation 資格証明を求められた場合は､レッスン 2 で使用した Windows ユーザー名とパスワードを入力します｡  
  
    **[Data Processing]** ダイアログ ボックスに､各パーティションの処理詳細が表示されます｡ 転送される行数は､パーティションごとに異なります｡ 各パーティションには､SQL ステートメントの WHERE 句に指定された年度の行数のみが含まれます｡ 処理を終えたら､[Data Processing] ダイアログ ボックスを閉じます｡  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
## <a name="whats-next"></a>次の手順
次のレッスン｢[レッスン 11: ロールを作成する](../tutorials/aas-lesson-11-create-roles.md)｣に進みます。 
