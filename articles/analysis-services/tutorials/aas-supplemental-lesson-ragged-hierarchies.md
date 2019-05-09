---
title: 'Azure Analysis Services チュートリアルの補足のレッスン: 不規則階層 | Microsoft Docs'
description: この Azure Analysis Services チュートリアルでは、不規則階層を修正する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 50926b7c8b06668770d9a00b8342d209fdb94649
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191107"
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>補足のレッスン - 不規則階層

この補足のレッスンでは、さまざまなレベルに空白の値 (メンバー) が含まれている階層でピボットするときの一般的な問題を解決します。 たとえば、上級管理者が、部門管理者と管理者以外の両方を直属の部下として持つ組織などがあります。 また、国 - リージョン - 市からなる地理的階層では、ワシントン D.C. やバチカン市など、都市によっては親となる州や県がありません。 階層に空のメンバーがあると、多くの場合、レベル数が異なったり不規則になったりします。

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

1400 互換性レベルの表形式モデルには、階層用の **[メンバーを隠す]** プロパティがあります。 **[既定]** 設定では、どのレベルにも空のメンバーがないと想定されています。 **[空白のメンバーを隠す]** 設定では、ピボットテーブルやレポートに追加した空のメンバーが階層から除外されます。  
  
このレッスンの推定所要時間: **20 分**  
  
## <a name="prerequisites"></a>前提条件  
この補足のレッスンのトピックは、表形式モデルのチュートリアルの一部です。 この補足のレッスンの作業を実行する前に、前のレッスンをすべて完了しているか、または Adventure Works Internet Sales のサンプル モデル プロジェクトを完了している必要があります。 

チュートリアルの一環として AW Internet Sales プロジェクトを作成した場合、モデルには不規則なデータや階層がまだ含まれていません。 この補足のレッスンを実行するには、まず最初に、いくつかのテーブルを追加することによって問題を生じさせ、リレーションシップ、計算列、メジャー、および新しい組織階層を作成する必要があります。 その作業に 15 分程かかります。 その後、ほんの数分でそれを解決します。  

## <a name="add-tables-and-objects"></a>テーブルとオブジェクトの追加
  
### <a name="to-add-new-tables-to-your-model"></a>新しいテーブルをモデルに追加するには
  
1.  表形式モデル エクスプローラーで、**[データ ソース]** を展開し、接続を右クリックして **[新しいテーブルのインポート]** を選択します。
  
2.  ナビゲーターで、**DimEmployee** と **FactResellerSales** を選択し、**[OK]** をクリックします。

3.  クエリ エディターで、**[インポート]** をクリックします。

4.  次の[リレーションシップ](../tutorials/aas-lesson-4-create-relationships.md)を作成します。

    | 表 1           | 列       | フィルターの方向   | テーブル 2     | 列      | アクティブ |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | 既定値            | DimDate     | 日付        | はい    |
    | FactResellerSales | DueDate      | 既定値            | DimDate     | 日付        | いいえ      |
    | FactResellerSales | ShipDateKey  | 既定値            | DimDate     | 日付        | いいえ      |
    | FactResellerSales | ProductKey   | 既定値            | DimProduct  | ProductKey  | はい    |
    | FactResellerSales | EmployeeKey  | 両方のテーブル | DimEmployee | EmployeeKey | はい    |

5. **DimEmployee** テーブルで、次の[集計列](../tutorials/aas-lesson-5-create-calculated-columns.md)を作成します。 

    **パス** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  **DimEmployee** テーブルで、**Organization (組織)** という名前の[階層](../tutorials/aas-lesson-9-create-hierarchies.md)を作成します。 次の列を順番通りに追加します。**Level1**、**Level2**、**Level3**、**Level4**、**Level5**。

7.  **FactResellerSales** テーブルで、次の[メジャー](../tutorials/aas-lesson-6-create-measures.md)を作成します。

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  [[Excel で分析]](../tutorials/aas-lesson-12-analyze-in-excel.md) を使用して Excel を開き、ピボットテーブルを自動的に作成します。

9.  **[ピボットテーブルのフィールド]** で、**DimEmployee** テーブルの **Organization** 階層を**行**に追加し、**FactResellerSales** テーブルの **ResellerTotalSales** メジャーを**値**に追加します。

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    ピボットテーブルから分かるとおり、階層には不規則な行が表示されます。 空のメンバーが表示される行が多数あります。

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>[メンバーを隠す] プロパティを設定して不規則階層を修正するには

1.  **表形式モデル エクスプ ローラー**で、**[テーブル]** > **[DimEmployee]** > **[階層]** > **[Organization]** を展開します。

2.  **[プロパティ]** > **[メンバーを隠す]** で、**[空白のメンバーを隠す]** を選択します。 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Excel に戻り、ピボットテーブルを更新します。 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    これで、大幅に改善されました。

## <a name="see-also"></a>関連項目   
[レッスン 9: 階層を作成する](../tutorials/aas-lesson-9-create-hierarchies.md)  
[補助レッスン - 動的なセキュリティ](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[補足のレッスン - 詳細行](../tutorials/aas-supplemental-lesson-detail-rows.md)  
