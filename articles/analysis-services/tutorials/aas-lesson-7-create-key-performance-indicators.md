---
title: "Azure Analysis Services チュートリアル - レッスン 7: 主要業績評価指標を作成する |Microsoft Docs"
description: "Azure Analysis Services チュートリアル プロジェクトで主要業績評価指標を作成する方法を説明します｡"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: d78808421dd5acd907aa9e9000bb3b770a42c061
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-7-create-key-performance-indicators"></a>レッスン 7: 主要業績評価指標を作成する

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

このレッスンでは､主要業績評価指標 (KPI) を作成します｡ KPI は､業績面からある値 (*Base* メジャーで定義) と *Target* 値 (メジャーまたは絶対値で定義) との比較評価に使用されます｡ レポート用のクライアント アプリケーションでは､KPI はビジネスのプロが事業の成功の摘要を理解したり､傾向を把握したりする容易で迅速な手段になります｡ 詳細は、[KPI](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular) を参照してください。
  
このレッスンの推定所要時間: **15 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンの作業を実行する前に、前のレッスン「[レッスン 6: メジャーを作成する](../tutorials/aas-lesson-6-create-measures.md)」を終えている必要があります。   
  
## <a name="create-key-performance-indicators"></a>主要業績評価指標を作成する  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>InternetCurrentQuarterSalesPerformance KPI を作成する  
  
1.  モデル デザイナーで **FactInternetSales** テーブルを選択します｡  
  
2.  メジャー グリッドで空のセルをクリックします｡  
  
3.  テーブルの上の数式バーに次の式を入力します｡ 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    このメジャーは､KPI の Base メジャーの働きをします｡  
  
4.  **InternetCurrentQuarterSalesPerformance** > **Create KPI** を右クリックします｡   
  
5.  [Key Performance Indicator (KPI)] ダイアログ ボックスの**[Target]** で､**Absolute Value** を選択し､**1.1** と入力します｡  
  
7.  左側 (低) のスライダー フィールドに **1** を入力し､右側 (高) のスライダー フィールドに **1.07** を入力します｡  
  
8.  **[Select Icon Style]** で､アイコン タイプとして菱形 (赤)､三角形 (黄色)､円 (緑) を選択します｡
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > 使用可能なアイコンのスタイルの下に展開可能な **Descriptions** ラベルがあります｡ このフィールドを使用して､クライアント アプリケーションでよりわかりやすいよう各種 KPI 要素の説明を入力できます｡  
  
9. **[OK]** をクリックして KPI を終了します。  
  
    メジャー グリッドの **InternetCurrentQuarterSalesPerformance** メジャーの横にアイコンがあります｡ このアイコンは､そのメジャーが KPI の Base 値の働きをしていることを示します｡  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>InternetCurrentQuarterMarginPerformance KPI を作成する  
  
1.  **FactInternetSales** テーブル用のメジャー グリッドで空のセルをクリックします｡  
  
2.  テーブルの上の数式バーに次の式を入力します｡  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Right-click **InternetCurrentQuarterMarginPerformance** > **Create KPI** を右クリックします｡  
  
4.  [Key Performance Indicator (KPI)] ダイアログ ボックスの**[Target]** で､**Absolute Value** を選択し､**1.25** と入力します｡   
  
5.  左側 (低) のスライダー フィールドでそのフィールドの値が **0.8** になるまでスライドし､右側 (高) のスライダー フィールドでフィールドの値が **1.03** になるまでスライドしします｡  
  
6.  **[Select Icon Style]** で､アイコン タイプとして菱形 (赤)､三角形 (黄色)､円 (緑) を選択し､**[OK]** をクリックします｡  
  
## <a name="whats-next"></a>次の手順
[レッスン 8: パースペクティブを作成する](../tutorials/aas-lesson-8-create-perspectives.md)
  
  

