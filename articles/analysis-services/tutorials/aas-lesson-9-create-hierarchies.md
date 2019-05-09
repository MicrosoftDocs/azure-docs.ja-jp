---
title: 'Azure Analysis Services チュートリアルのレッスン 9: 階層を作成する | Microsoft Docs'
description: 表形式モデルで階層を作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5eb80051052138924cdb30655609215974435839
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729918"
---
# <a name="create-hierarchies"></a>階層を作成する

このレッスンでは、階層を作成します。 階層は、レベルに分類された列のグループです。 たとえば、Geography (地理的な場所) 階層には、Country (国)、State (州)、County (郡)、City (市) などの下位階層があります。 階層は、レポートするクライアント アプリケーションのフィールド リストにある他の列とは別に表示できます。階層を使用すれば、クライアント ユーザーが列をより簡単に探してレポートに含めることができます。 詳細については、「[階層](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)」を参照してください。
  
階層を作成するには、*ダイアグラム ビュー*でモデル デザイナーを使用します。 データ ビューでは、階層の作成および管理はサポートされていません。  
  
このレッスンの推定所要時間: **20 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 8: パースペクティブを作成する](../tutorials/aas-lesson-8-create-perspectives.md)」を完了する必要があります。  
  
## <a name="create-hierarchies"></a>階層を作成する  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>DimProduct テーブルで Category 階層を作成するには  
  
1.  モデル デザイナー (ダイアグラム ビュー) で、**DimProduct** テーブルを右クリックし、**[階層の作成]** を選択します。 テーブル ウィンドウの一番下に、新しい階層が表示されます。 この階層の名前を **Category** に変更します。  
  
2.  **ProductCategoryName** 列をクリックし、新しい **Category** 階層までドラッグします。  
  
3.  **Category** 階層で、**ProductCategoryName** > **[名前の変更]** を右クリックし、「**Category**」と入力します。  
  
    > [!NOTE]  
    > 階層内で列の名前を変更しても、テーブル内でその列の名前は変更されません。 階層内の列は、テーブル内の列のみを表しています。  
  
4.  **ProductSubcategoryName** 列をクリックし、**Category** 階層までドラッグします。 この階層の名前を **Subcategory** に変更します。 
  
5.  **ModelName** 列を右クリックして **[階層に追加]**、**Category** の順に選択します。 この階層の名前を **Model** に変更します。

6.  最後に、**EnglishProductName** を Category 階層に追加します。 この階層の名前を **Product** に変更します。  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>DimDate テーブルで階層を作成するには  
  
1.  **DimDate** テーブルで、**Calendar** という名前の階層を作成します。  
  
3.  次の列を順番通りに追加します。

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  **DimDate** テーブルで、**Fiscal** 階層を作成します。 次の列を順番どおりに追加します。  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  最後に、**DimDate** テーブルで、**ProductionCalendar** 階層を作成します。 次の列を順番どおりに追加します。  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
## <a name="whats-next"></a>次の手順
[レッスン 10: パーティションを作成する](../tutorials/aas-lesson-10-create-partitions.md)。 
  
  
