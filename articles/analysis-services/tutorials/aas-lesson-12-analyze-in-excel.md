---
title: 'Azure Analysis Services チュートリアルのレッスン 12: Excel で分析 | Microsoft Docs'
description: Azure Analysis Services チュートリアル プロジェクトで Excel の Analyze を使用する方法を説明します｡
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e5ac04c7bd8174f823cacc67401af8a98b3d8170
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186483"
---
# <a name="analyze-in-excel"></a>Excel で分析

このレッスンでは､[Excel で分析] 機能を使用して Microsoft Excel を開き､モデル ワークスペースへのデータ ソース接続を自動的に作成して､ワークシートに自動的にピボットテーブルを追加します｡ Analyze in Excel 機能は､モデルをデプロイする前にモデル デザインの有効性を迅速かつ簡単にテストする手段を提供することを意図しています｡ このレッスンでは､データ分析は行いません｡ このレッスンの目的はモデル作成者が､モデル デザインのテストに利用できるツールを慣れてもらうことにあります｡   
  
このレッスンを行うには､Visual Studio と同じコンピューターに Excel がインストールされている必要があります｡
  
このレッスンの推定所要時間: **5 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 11: ロールを作成する](../tutorials/aas-lesson-11-create-roles.md)」を終えている必要があります。  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Default および Internet Sales パースペクティブを使用して参照します｡  
これらのタスクを初めて行う場合は､Default パースペクティブ(すべてのモデル オブジェクトが含まれる) と Internet Sales パースペクティブの両方を使ってモデルを参照します｡ Internet Sales パースペクティブでは､Customer テーブル オブジェクトが除外されています｡  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Default パースペクティブを使って参照する  
  
1.  **[Model]** メニュー > **[Analyze in Excel]** をクリックします｡  
  
2.  **[Analyze in Excel]** ダイアログ ボックスで **[OK]** をクリックします｡  
  
    Excel で新しいブックが開きます。 現在のユーザー アカウントを使ってデータ ソース接続が作成され､Default パースペクティブを使って表示可能なフィールドが定義されます｡ ワークシートに自動的にピボットテーブルが追加されます｡  
  
3.  Excel の **[ピボットテーブルのフィールド リスト]** に、メジャー グループ **DimDate** と **FactInternetSales** が表示されます。 また、**DimCustomer**､**DimDate**､ **DimGeography**､**DimProduct**､ **DimProductCategory**､ **DimProductSubcategory**､および**FactInternetSales** の各テーブルも、それぞれの列に表示されます。  
  
4.  ワークブックを保存しないで Excel を終了します｡  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Internet Sales パースペクティブを使って参照する  
  
1.  **[モデル]** メニューをクリックし、**[Analyze in Excel]** をクリックします。  
  
2.  **[Analyze in Excel]** ダイアログ ボックスで､**Current Windows User** を選択状態のままにし､**[Perspective]** ドロップダウン リストボックスで**Internet Sales** を選択して､**[OK]** をクリックします｡ 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  Excel の **PivotTable Fields** のフィールド一覧からDimCustomer テーブルが除外されています｡  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  ワークブックを保存しないで Excel を終了します｡  
  
## <a name="browse-by-using-roles"></a>ロールを使って参照する  
ロールは､あらゆる表形式モデルの重要な要素です｡ メンバーとしてユーザーが登録されているロールがない場合､ユーザーはモデルを使ってデータにアクセスすることも分析することもできません｡ Analyze in Excel 機能は､定義されているロールをテストする手段を提供します｡  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Sales Manager ユーザー ロールを使って参照する  
  
1.  SSDT で、**[モデル]** メニューをクリックし、**[Excel で分析]** をクリックします。  
  
2.  **[モデルへの接続に使用するユーザー名またはロールを指定します]** で **[ロール]** を選択し、ドロップダウン リストボックスで **[Sales Manager]** を選択して､**[OK]** をクリックします｡  
  
    Excel で新しいブックが開きます。 ピボットテーブルが自動的に作成されます。 [ピボットテーブルのフィールド リスト] に、新しいモデルで使用できるすべてのデータ フィールドが表示されます。  
      
3.  ワークブックを保存しないで Excel を終了します｡  
  
## <a name="whats-next"></a>次の手順
次のレッスン｢[レッスン 13: デプロイ](../tutorials/aas-lesson-13-deploy.md)」に進みます。

  
  
  
