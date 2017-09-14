---
title: "Azure Analysis Services チュートリアルのレッスン 9: 階層を作成する | Microsoft Docs"
description: 
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
ms.openlocfilehash: d628dc621335acf231342a6d9186079de16e85f4
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-9-create-hierarchies"></a>レッスン 9: 階層を作成する

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

このレッスンでは、階層を作成します。 階層は、レベルに分類された列のグループです。たとえば、Geography (地理的な場所) 階層には、Country (国)、State (州)、County (郡)、City (市) などの下位階層があります。 階層は、レポートするクライアント アプリケーションのフィールド リストにある他の列とは別に表示できます。階層を使用すれば、クライアント ユーザーが列をより簡単に探してレポートに含めることができます。 詳細については、「[階層](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)」を参照してください。
  
階層を作成するには、*ダイアグラム ビュー*でモデル デザイナーを使用します。 データ ビューでは、階層の作成および管理はサポートされていません。  
  
このレッスンの推定所要時間: **20 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、順番に従って実行する必要があります。 このレッスンの作業を実行する前に、前のレッスン「[レッスン 8: パースペクティブを作成する](../tutorials/aas-lesson-8-create-perspectives.md)」を完了している必要があります。  
  
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
[レッスン 10: パーティションを作成する](../tutorials/aas-lesson-10-create-partitions.md) 
  
  

