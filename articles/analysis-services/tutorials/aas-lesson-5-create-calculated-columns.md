---
title: 'Azure Analysis Services チュートリアル - レッスン 5: 計算列を作成する | Microsoft Docs'
description: この Azure Analysis Services チュートリアル プロジェクトで計算列を作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb0a17d4b7e11dd83439d943abf0bdd34ef915ae
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187452"
---
# <a name="create-calculated-columns"></a>計算列を作成する

このレッスンでは､計算列を追加することによってモデルにデータを作成します｡ 計算列 (カスタム列) は､Get Data を使用するとき、またはクエリ エディターを使用して､あるいは､このレッスンで行うように後でモデル デザイナーで作成することができます｡ 詳細は､[計算列](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns)を参照してください｡
  
3 つのテーブルに 5 つの計算列を新しく作成します｡ 手順はタスクごとに若干異なります。これは、新しい列の作成や列名の変更､テーブル内のさまざまな場所に列を配置する方法はいくつかあることを示しています。  

また､このレッスンでは Data Analysis Expressions (DAX) を初めて使用します｡ DAX は､表形式モデルに対して高度にカスタマイズ可能な数式を作成するための専用言語です｡ このチュートリアルでは､DAX を使用して､計算列､メジャー､ロール フィルターを作成します｡ 詳細は、[表形式モデルにおけるDAX](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular) を参照してください｡ 
  
このレッスンの推定所要時間: **15 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 4: リレーションシップを作成する](../tutorials/aas-lesson-4-create-relationships.md)」を終えている必要があります。 
  
## <a name="create-calculated-columns"></a>計算列を作成する  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>DimDate テーブルに MonthCalendar 計算列を作成する  
  
1.  **[モデル]** メニュー > **[モデル ビュー]** > **[データ ビュー]** をクリックします。  
  
    計算列は､Date View でモデル デザイナーを使用することによってのみ作成できます｡  
  
2.  モデル デザイナーで **DimDate** テーブル (タブ) をクリックします｡  
  
3.  **CalendarQuarter** 列ヘッダーを右クリックして､**[Insert Column]** をクリックします｡  
  
    **Calculated Column 1** という名前の新しい列が**Calendar Quarter** 列の左側に追加されます｡  
  
4.  テーブルの上の数式バーに次の DAX 式を入力します。AutoComplete は列やテーブルの完全修飾名を入力するのに役立つとともに､使用可能な関数をすべて一覧表示します｡  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    計算列のすべての行に値が入力されます｡ テーブルを下方向にスクロールすると､この列の各行のデータに基づいて､行にさまざまな値が設定さていることがわかります｡    
  
5.  列名を **MonthCalendar** に変更します｡ 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
MonthCalendar 計算列は､月の名前で並べ替えが可能な列です｡  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>DimDate テーブルに DayOfWeek 計算列を作成する  
  
1.  **DimDate** テーブルがアクティブなままの状態で､**[列]** メニューをクリックし､**[列の追加]** をクリックします｡  
  
2.  数式バーに次の式を入力します｡  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    式の入力を終えたら､Enter キーを押します｡ テーブルの右端に新しい列が追加されます｡  
  
3.  列名を **DayOfWeek** に変更します｡  
  
4.  列の見出しをクリックし､**EnglishDayNameOfWeek** 列と **DayNumberOfMonth** 列の間に列をドラッグします｡  
  
    > [!TIP]  
    > テーブル内の列を移動すると､ナビゲートしやすくなります｡  
  
DayOfWeek 計算列は､曜日の名前で並べ替えが可能な列です｡  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>DimProduct テーブルに ProductSubcategoryName 列を作成する  
  
  
1.  **DimProduct** テーブルの右端までスクロールします｡ 右端の列名が **Add Column** (斜体) になっています｡この列の見出しをクリックします｡  
  
2.  数式バーに次の式を入力します｡  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  列名を **ProductSubcategoryName** に変更します｡  
  
ProductSubcategoryName 計算列は､DimProduct テーブルに階層を作成するのに使用されます｡この列には､DimProductSubcategory テーブルのEnglishProductSubcategoryName 列からデータが取り込まれます｡ 階層が複数のテーブルにまたがることはできません｡ 階層は､後ほどレッスン 9 で作成します｡  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>DimProduct テーブルに ProductCategoryName 計算列を作成する  
  
1.  **DimProduct** テーブルがアクティブなままの状態で､**[列]** メニューをクリックし､**[Add Column]** をクリックします｡  
  
2.  数式バーに次の式を入力します｡  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  列名を **ProductCategoryName** に変更します｡  
  
ProductCategoryName 計算列は､DimProduct テーブルに階層を作成するのに使用されます｡この列には､DimProductCategory テーブルのEnglishProductCategoryName 列からデータが取り込まれます｡ 階層が複数のテーブルにまたがることはできません｡  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>FactInternetSales テーブルに Margin 計算列を作成する  
  
1.  モデル デザイナーで **FactInternetSales** テーブルを選択します｡  
  
2.  **SalesAmount** 列と **TaxAmt** 列の間に新しい計算列を作成します｡  
  
3.  数式バーに次の式を入力します｡  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  列名を **Margin** に変更します｡  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    Margin 計算列は販売ごとの利幅の分析に使用されます｡  
  
## <a name="whats-next"></a>次の手順
[レッスン 6: メジャーを作成する](../tutorials/aas-lesson-6-create-measures.md)」を終えている必要があります。
  
  
  
