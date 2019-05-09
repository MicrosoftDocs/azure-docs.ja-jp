---
title: 'Azure Analysis Services チュートリアルのレッスン 2: データを取得する | Microsoft Docs'
description: Azure Analysis Services チュートリアル プロジェクトでデータを取得､インポートする方法を説明します｡
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6643d0851be1ad9d2827f51af3662a469020654c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189322"
---
# <a name="get-data"></a>データを取得する

このレッスンでは､SSDT の Get Data を使用して､Adventure Works サンプル データベースへの接続､データの選択とプレビュー､フィルター､モデル ワークスペースへのデータのインポートを行います｡  
  
Get Data を使うことで､幅広いさまざまなソースからデータをインポートできます (Azure SQL Database や Oracle､Sybase､OData Feed､Teradata､ファイルなど)｡ Power Query M 数式を使ってデータの照会を行うこともできます｡

> [!NOTE]
> このチュートリアルのタスクと画像は、オンプレミス サーバー上の AdventureWorksDW2014 データベースへの接続を示しています。 Azure 上の Adventure Works データベースとは異なる場合があります。
  
このレッスンの推定所要時間: **10 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 1:新しい表形式モデル プロジェクトを作成する](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)」を完了する必要があります。  
  
## <a name="create-a-connection"></a>接続を作成する  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>AdventureWorksDW2014 データベースとの接続を作成する  
  
1.  Tabular Model Explorer で **[Data Sources** > **Import from Data Source**] を右クリックします｡  
  
    これにより Get Data が起動し､手順に従ってデータソースに接続できます｡ Tabular Model Explorer が表示されない場合は､**Solution Explorer** で **Model.bim** ダブルクリックすることでデザイナにモデルを表示します｡ 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  Get Data データで **[Database** > **SQL Server Database** > **Connect]** をクリックします｡  
  
3.  **[SQL Server Database]** ダイアログ ボックスの **[Server]** に AdventureWorksDW2014 データベースをインストールしたサーバーの名前を入力し､**[Connect]** をクリックします｡  

4.  資格情報の入力を求められたら、データのインポートと処理を行う際､データ ソースへの接続にAnalysis Services が使用する資格情報を指定する必要があります。 **[Impersonation Mode]** で **[Impersonate Account]** モードを選択し､**[Connect]** をクリックします｡ パスワードの有効期限が無期限のアカウントを使用することをお勧めします｡

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Windows のユーザー アカウントとパスワードを使用することが､データソースへの接続で最も安全な方法です｡
  
5.  ナビゲーターで **AdventureWorksDW2014** データベースを選択し､**[OK]** をクリックします｡これで､データベースへの接続が作成されます｡ 
  
6.  ナビゲーターで､次の表の **DimCustomer**､**DimDate**､ **DimGeography**､**DimProduct**､ **DimProductCategory**､ **DimProductSubcategory**､および**FactInternetSales** チェック ボックスを選択し、**[編集]** をクリックします。

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
    クエリ エディターが開きます。 次のセクションでは、インポートするデータのみを選択します。

  
## <a name="filter-the-table-data"></a>テーブルのデータをフィルターする  
AdventureWorksDW2014 サンプル データベースのテー部には､モデルに含める必要のないデータがあります｡ 可能であれば、不要なデータをフィルターで除外して､モデルが使用するインメモリー空間を確保します｡ フィルターで列の一部をテーブルから除外し､ワークスペース データベースやデプロイ後のモデル データベースにインポートされないようにします｡ 
  
#### <a name="to-filter-the-table-data-before-importing"></a>インポートの前にテーブル データをフィルターする  
  
1.  Query Editor で **[DimCustomer]** テーブルを選択します｡ データソース (AdventureWorksDW2014 サンプル データベース) にある DimCustomer テーブルのビューが表示されます。 
  
2.  Ctrl を押しながら､**SpanishEducation**､ **FrenchEducation**､**SpanishOccupation**､および **FrenchOccupation** をクリックし (複数項目の選択)､右クリックして､**[Remove Columns]** をクリックします｡ 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    これらの列の値はインターネット販売分析に関係ありませんから､インポートする必要はありません｡ 不要な列を排除することで､モデルは小さくなり､効率的になります｡  

    > [!TIP]
    > 間違えた場合は、**[適用したステップ]** でステップを削除することで取り消すことができます。   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  各テーブルで次の列を削除することによって残りのテーブルをフィルターします｡  
    
    **DimDate**
    
      |列|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |列|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |列|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |列|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |列|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      列は削除されません。
  
## <a name="Import"></a>選択したテーブルと列のデータをインポートする  
プレビューして､不要なデータを排除すると､必要となる残りのデータをインポートできます｡ ウィザードによって､テーブルのデータがインポートされます｡テーブル間にリレーションシップがある場合は､それらリレーションシップもインポートされます｡ 新しいテーブルと列がモデルに作成されます｡フィルターで除外したデータはインポートされません｡  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>選択したテーブルと列のデータをインポートする  
  
1.  選択内容を確認します｡ 問題がなければ､**[インポート]** をクリックします｡ [Data Processing] ダイアログ ボックスにデータソースからワークスペース データベースへのデータのインポート状況が表示されます｡
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  **[閉じる]** をクリックします。  

  
## <a name="save-your-model-project"></a>モデル プロジェクトを保存します｡  
モデル プロジェクトは頻繁に保存してください｡  
  
#### <a name="to-save-the-model-project"></a>モデル プロジェクトを保存する  
  
-   **[ファイル]** > **[保存]** の順にクリックします。  
  
## <a name="whats-next"></a>次の手順
[レッスン 3: 日付テーブルとしてマークする](../tutorials/aas-lesson-3-mark-as-date-table.md)。

  
  
