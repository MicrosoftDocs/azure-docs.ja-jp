---
title: 'Azure Analysis Services チュートリアルのレッスン 3: 日付テーブルとしてマークする | Microsoft Docs'
description: Azure Analysis Services チュートリアル プロジェクトで日付テーブルをマークする方法を説明します｡
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4c383fe30b8a6be3a5915f3cc1c0f5e5712ab328
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188999"
---
# <a name="mark-as-date-table"></a>日付テーブルとしてマークする

｢レッスン 2: データを取得する｣では､DimDate というディメンション テーブルをインポートしました｡ モデルでは､このテーブルは DimDate と命名されていますが､このテーブルには日付と時刻データが含まれているため *日付テーブル*とも呼ばれます｡  
  
後でメジャーを作成するときに行うように､DAX タイムインテリジェンス関数を使用するたびに､プロパティ ("*日付テーブル*" とそのテーブルの一意識別子の "*日付列*") を指定する必要あります｡
  
このレッスンでは､ DimDateテーブルを*日付テーブル*､その日付テーブル内の日付列 を*日付列* (一意の識別子) としてマークします｡  

日付テーブルと日付列をマークする前に､モデルをより容易に理解できるよう､少しハウスキーピングを行っておきましょう｡ DimDate テーブルで、**FullDateAlternateKey** という名前の列を探してください。 この列の各行にはそれぞれ、テーブルに含まれる各カレンダー年の日付が格納されています。 この列は､メジャー数式やレポートで頻繁に使用することになります｡ しかしながら､実際には FullDateAlternateKey はこの列の優れた識別子ではありません｡ この列名を **Date** に変更することで､簡単に識別し､数式に含められるようにします｡ SSDT､さらには Power BI や Excel などのクライアント側のレポートアプリケーションで簡単に識別できるように必要に応じてテーブルや列などのオブジェクトの名前を変更することは良いアイデアです｡ 
  
このレッスンの推定所要時間: **3 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 2:データを取得する](../tutorials/aas-lesson-2-get-data.md)」を完了する必要があります。 

### <a name="to-rename-the-fulldatealternatekey-column"></a>FullDateAlternateKey 列の名前を変更する

1.  モデル デザイナーで **DimDate** テーブルをクリックします｡

2.  **FullDateAlternateKey** 列のヘッダーをダブルクリックして､名前を **Date** に変更します｡

  
### <a name="to-set-mark-as-date-table"></a>Mark as Date Table を設定する  
  
1.  **Date** 列を選択し､ **[プロパティ]** ウィンドウの **Data Type** で **Date** が選択されていることを確認します｡  
  
2.  **[Table]** メニューをクリックし､**[Date]** をクリックして､**[Mark as Date Table]** をクリックします｡  
  
3.  **[Mark as Date Table]** ダイアログ ボックスの **[Date]** リストボックスで､一意の識別子として **[Date]** 列を選択します｡ この列は既定で選択されています｡ Click **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>次の手順
[レッスン 4: リレーションシップを作成する](../tutorials/aas-lesson-4-create-relationships.md)。
  
