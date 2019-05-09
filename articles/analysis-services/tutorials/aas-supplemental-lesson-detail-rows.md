---
title: 'Azure Analysis Services チュートリアルの補足のレッスン: 詳細行 | Microsoft Docs'
description: この Azure Analysis Services チュートリアルでは、詳細行の式を作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f52d6ebf4a65a6a182b24017d652693c6f5e4072
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190240"
---
# <a name="supplemental-lesson---detail-rows"></a>補足のレッスン - 詳細行

この補足のレッスンでは、DAX エディターを使用して、カスタムの詳細行の式を定義します。 詳細行の式はメジャーに関するプロパティです。エンドユーザーに、メジャーの集計結果についてより詳細な情報を提供します。 
  
このレッスンの推定所要時間: **10 分**  
  
## <a name="prerequisites"></a>前提条件  
この補足のレッスンは、表形式モデルのチュートリアルの一部です。 この補足のレッスンの作業を実行する前に、前のレッスンをすべて完了しているか、または Adventure Works Internet Sales のサンプル モデル プロジェクトを完了している必要があります。  
  
## <a name="whats-the-issue"></a>問題点
詳細行の式を追加する前に、InternetTotalSales メジャーの詳細を見てみましょう。

1.  SSDT で、**[モデル]** メニュー > **[Excel で分析]** をクリックして Excel を開き、空のピボットテーブルを作成します。
  
2.  **[ピボットテーブルのフィールド]** で、FactInternetSales テーブルの **InternetTotalSales** メジャーを **[値]** に、DimDate テーブルの **CalendarYear** を **[列]** に、**EnglishCountryRegionName** を **[行]** に追加します。 これで、ピボットテーブルに InternetTotalSales メジャーからの集計結果が、リージョンと年ごとに表示されます。 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. ピボットテーブルで、年およびリージョン名の集計値をダブルクリックします。 ここでは、Australia と 2014 年の値をダブルクリックしています。 データが含まれた新しいシートが開きますが、このデータは実際には役に立ちません。

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
ここでの目的は、InternetTotalSales メジャーの集計結果に寄与するデータの列と行が含まれたテーブルです。 そのために、メジャーのプロパティとして詳細行の式を追加します。

## <a name="add-a-detail-rows-expression"></a>詳細行の式を追加する

#### <a name="to-create-a-detail-rows-expression"></a>詳細行の式を作成するには 
  
1. FactInternetSales テーブルのメジャー グリッドの **InternetTotalSales** メジャーをクリックします。 

2. **[プロパティ]** > **[詳細行の式]** で、エディター ボタンをクリックして DAX エディターを開きます。

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. DAX エディターで、次の式を入力します。

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    この式は、FactInternetSales テーブルの名前、列、およびメジャー結果を指定し、ユーザーがピボットテーブルまたはレポート内の集計結果をダブルクリックしたときに、関連テーブルが返されます。

4. Excel に戻って、手順 3 で作成したシートを削除し、集計値をダブルクリックします。 今回は、メジャーに対して詳細行の式プロパティが定義されているので、前回よりも役に立つデータが格納された新しいシートが開きます。

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. モデルを再デプロイします。

  
## <a name="see-also"></a>関連項目  

[SELECTCOLUMNS 関数 (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[補足のレッスン - 動的なセキュリティ](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[補足のレッスン - 不規則階層](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 